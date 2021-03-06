---
title: Verwalten von Computeressourcen für SQL-Pool
description: Informieren Sie sich über die Leistungsfunktionen für horizontales Skalieren in einem Azure Synapse Analytics-SQL-Pool. Skalieren Sie durch Anpassen der DWUs auf, oder senken Sie die Kosten durch Anhalten des Data Warehouse.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 11/12/2019
ms.author: rortloff
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: daf57c7e6ef40f75eac070c06547cf2a28338f21
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/03/2020
ms.locfileid: "80633243"
---
# <a name="manage-compute-in-azure-synapse-analytics-data-warehouse"></a>Verwalten von Computeressourcen im Azure Synapse Analytics-Data Warehouse

Informieren Sie sich über die Verwaltung von Computeressourcen im Azure Synapse Analytics-SQL-Pool. Senken Sie die Kosten, indem Sie den SQL-Pool anhalten, oder skalieren Sie das Data Warehouse, um Leistungsanforderungen zu erfüllen.

## <a name="what-is-compute-management"></a>Was ist Computeverwaltung?

In der Architektur von Data Warehouse werden Speicher- und Computeressourcen voneinander getrennt, sodass sie unabhängig voneinander skaliert werden können. Daher können Sie Computeressourcen skalieren, um Leistungsanforderungen unabhängig vom Datenspeicher zu erfüllen. Sie können Computeressourcen auch anhalten und fortsetzen. Eine logische Konsequenz dieser Architektur ist es, dass die [Abrechnung](https://azure.microsoft.com/pricing/details/sql-data-warehouse/) für Compute- und Speicherressourcen unabhängig voneinander erfolgt. Wenn Sie Ihr Data Warehouse für eine Weile nicht verwenden müssen, können Sie Computekosten sparen, indem Sie Computeressourcen anhalten.

## <a name="scaling-compute"></a>Skalieren von Computeressourcen

Sie können Computeressourcen horizontal hoch- und wieder herunterskalieren, indem Sie die Einstellung für [Data Warehouse-Einheiten](what-is-a-data-warehouse-unit-dwu-cdwu.md) für Ihren SQL-Pool anpassen. Die Lade- und die Abfrageleistung kann linear erhöht werden, wenn Sie weitere Data Warehouse-Einheiten hinzufügen.

Schritte zur horizontalen Skalierung finden Sie in den Schnellstarts zum [Azure-Portal](quickstart-scale-compute-portal.md), zu [PowerShell](quickstart-scale-compute-powershell.md) oder zu [T-SQL](quickstart-scale-compute-tsql.md). Sie können auch horizontale Skalierungsvorgänge mit einer [REST-API](sql-data-warehouse-manage-compute-rest-api.md#scale-compute) ausführen.

Um einen Skalierungsvorgang auszuführen, beendet SQL-Pool zunächst alle eingehenden Abfragen und führt dann einen Rollback der Transaktionen durch, um einen konsistenten Zustand zu gewährleisten. Die Skalierung tritt erst auf, wenn der Transaktionsrollback abgeschlossen ist. Für einen Skalierungsvorgang trennt das System die Speicherebene von den Computeknoten, fügt Computeknoten hinzu und verbindet dann die Speicherebene wieder mit der Computeebene. Jeder SQL-Pool wird als 60 Verteilungen gespeichert, die auf die Computeknoten gleichmäßig verteilt werden. Durch Hinzufügen von weiteren Computeknoten wird die Computeleistung erhöht. Mit zunehmender Anzahl von Computeknoten verringert sich die Anzahl der Verteilungen pro Computeknoten, sodass mehr Computeleistung für Ihre Abfragen bereitsteht. Entsprechend verringert sich mit abnehmender Anzahl von Data Warehouse-Einheiten die Anzahl der Computeknoten, wodurch die Computeressourcen für Abfragen verringert werden.

Die folgende Tabelle zeigt, wie sich die Anzahl von Verteilungen pro Computeknoten ändert, wenn sich die Data Warehouse-Einheiten ändern.  DW30000c bietet 60 Computeknoten und führt zu einer viel höheren Abfrageleistung als DW100c.

| Data Warehouse-Einheiten  | \# von Computeknoten | \# Verteilungen pro Knoten |
| -------- | ---------------- | -------------------------- |
| DW100c   | 1                | 60                         |
| DW200c   | 1                | 60                         |
| DW300c   | 1                | 60                         |
| DW400c   | 1                | 60                         |
| DW500c   | 1                | 60                         |
| DW1000c  | 2                | 30                         |
| DW1500c  | 3                | 20                         |
| DW2000c  | 4                | 15                         |
| DW2500c  | 5                | 12                         |
| DW3000c  | 6                | 10                         |
| DW5000c  | 10               | 6                          |
| DW6000c  | 12               | 5                          |
| DW7500c  | 15               | 4                          |
| DW10000c | 20               | 3                          |
| DW15000c | 30               | 2                          |
| DW30000c | 60               | 1                          |

## <a name="finding-the-right-size-of-data-warehouse-units"></a>Ermitteln der richtigen Größe der Data Warehouse-Einheiten

Um von den Leistungsvorteilen der Skalierung insbesondere für größere Data Warehouse-Einheiten zu profitieren, sollten Sie ein Dataset von mindestens 1 TB verwenden. Um die optimale Anzahl der Data Warehouse-Einheiten für Ihren SQL-Pool zu ermitteln, probieren Sie ein zentrales Hoch- und Herunterskalieren aus. Führen Sie nach dem Laden Ihrer Daten einige Abfragen mit verschiedenen Mengen an Datawarehouse-Einheiten aus. Da die Skalierung schnell erfolgt, können Sie innerhalb einer Stunde verschiedene Leistungsebenen ausprobieren.

Empfehlungen für die Ermittlung der besten Anzahl von Data Warehouse-Einheiten:

- Beginnen Sie bei einem in der Entwicklung befindlichen SQL-Pool mit einer geringeren Anzahl von Data Warehouse-Einheiten.  Ein guter Ausgangspunkt ist DW400c oder DW200c.
- Überwachen Sie die Anwendungsleistung, und beobachten Sie dabei die Anzahl der ausgewählten Data Warehouse-Einheiten im Vergleich zur beobachteten Leistung.
- Gehen Sie von einer linearen Skalierung aus, und bestimmen Sie, um wie viel Sie die Data Warehouse-Einheiten erhöhen oder verringern müssen.
- Nehmen Sie weitere Anpassungen vor, bis Sie die optimale Leistungsstufe für Ihre geschäftlichen Anforderungen erreichen.

## <a name="when-to-scale-out"></a>Fälle für das Aufskalieren

Das horizontale Hochskalieren von Data Warehouse-Einheiten wirkt sich auf die folgenden Aspekte der Leistung aus:

- Lineares Verbessern der Systemleistung bei Scans, Aggregationen und CTAS-Anweisungen.
- Erhöhen der Anzahl von Readern und Writern für des Laden von Daten.
- Maximieren der Anzahl von gleichzeitigen Abfragen und Parallelitätsslots.

Empfehlungen für Fälle, in denen Sie Data Warehouse-Einheiten aufskalieren sollten:

- Bevor Sie einen umfangreichen Vorgang zum Laden oder Transformieren von Daten durchführen, skalieren Sie auf, damit die Daten schneller verfügbar sind.
- Skalieren Sie während der Hauptgeschäftszeiten auf, um eine größere Anzahl gleichzeitiger Abfragen verarbeiten zu können.

## <a name="what-if-scaling-out-does-not-improve-performance"></a>Welche Optionen gibt es, wenn horizontales Hochskalieren die Leistung nicht verbessert?

Fügen Sie Data Warehouse-Einheiten hinzu, um die Parallelität zu erhöhen. Wenn die Arbeit gleichmäßig zwischen den Computeknoten verteilt wird, wird die Abfrageleistung durch die zusätzliche Parallelität verbessert. Wenn das horizontale Hochskalieren die Leistung nicht ändert, sind einige Ursachen denkbar. Die Daten können über die Verteilungen verstreut sein, oder Abfragen führen zu umfangreichen Datenverschiebung. Informationen zu Leistungsproblemen bei Abfragen finden Sie unter [Behandlung von Problemen mit der Abfrageleistung](sql-data-warehouse-troubleshoot.md#performance).

## <a name="pausing-and-resuming-compute"></a>Anhalten und Fortsetzen von Computeressourcen

Das Anhalten einer Computeressource bewirkt, dass die Speicherebene von den Computeknoten getrennt wird. Die Computeressourcen werden aus Ihrem Konto freigegeben. Computeressourcen werden Ihnen nicht berechnet, während Computeressourcen angehalten sind. Beim Fortsetzen von Computeressourcen wird der Speicher wieder mit den Computeknoten verbunden, die Computeressourcen werden wieder berechnet.
Wenn Sie einen SQL-Pool anhalten, geschieht Folgendes:

- Compute- und Speicherressourcen werden an den Pool der verfügbaren Ressourcen im Rechenzentrum zurückgegeben.
- Die Kosten für Data Warehouse-Einheiten sind für die Dauer der Pause gleich null.
- Die Speicherung von Daten ist nicht betroffen, und Ihre Daten bleiben intakt.
- Alle laufenden oder in die Warteschlange eingereihten Vorgänge werden abgebrochen.

Wenn Sie einen SQL-Pool fortsetzen, geschieht Folgendes:

- Der SQL-Pool lädt Compute- und Speicherressourcen entsprechend Ihrer Einstellung für Data Warehouse-Einheiten.
- Computeressourcen werden wieder für die Data Warehouse-Einheiten berechnet.
- Ihre Daten sind verfügbar.
- Wenn der SQL-Pool online ist, müssen Sie Ihre Workloadabfragen neu starten.

Falls der SQL-Pool immer verfügbar sein soll, könnten Sie ihn auf die kleinste Größe herunterskalieren, statt ihn anzuhalten.

Schritte zum Anhalten und Fortsetzen finden Sie in den Schnellstarts zum [Azure-Portal](pause-and-resume-compute-portal.md) oder zu [PowerShell](pause-and-resume-compute-powershell.md). Sie Können auch die [REST-API zum Anhalten](sql-data-warehouse-manage-compute-rest-api.md#pause-compute) oder die [REST-API zum Fortsetzen](sql-data-warehouse-manage-compute-rest-api.md#resume-compute) verwenden.

## <a name="drain-transactions-before-pausing-or-scaling"></a>Beseitigen von Transaktionen vor dem Pausieren oder Skalieren

Es wird empfohlen, dass vorhandene Transaktionen abgeschlossen werden, bevor Sie einen Anhalte- oder Skalierungsvorgang initiieren.

Beim Anhalten oder Skalieren Ihres SQL-Pools werden Ihre Abfragen im Hintergrund abgebrochen, wenn Sie die Anforderung zum Anhalten oder Skalieren initiieren. Das Abbrechen einer einfachen SELECT-Abfrage ist ein schneller Vorgang und hat fast keinerlei Auswirkung auf den Zeitraum, der für das Pausieren oder Skalieren Ihrer Instanz anfällt.  Dagegen können Transaktionsabfragen, bei denen die Daten oder die Struktur der Daten geändert wird, unter Umständen nicht so schnell beendet werden. **Transaktionsabfragen müssen laut Definition entweder vollständig abgeschlossen sein, oder es muss ein Rollback der Änderungen durchgeführt werden.** Ein Rollback der Schritte, die von einer Transaktionsabfrage ausgeführt wurden, kann genauso lange oder sogar länger als die ursprüngliche Änderung dauern, die mit der Abfrage durchgeführt werden sollte. Wenn Sie beispielsweise eine Abfrage abbrechen, mit der Zeilen gelöscht werden, und die Abfrage bereits eine Stunde lang ausgeführt wurde, kann es eine Stunde dauern, bis die gelöschten Zeilen wieder eingefügt wurden. Wenn Sie das Pausieren oder Skalieren bei aktiven Transaktionen ausführen, kann das Pausieren oder Skalieren lange dauern, weil erst gewartet werden muss, bis das Rollback abgeschlossen ist.

Siehe auch: [Grundlagen von Transaktionen](sql-data-warehouse-develop-transactions.md) und [Optimieren von Transaktionen](sql-data-warehouse-develop-best-practices-transactions.md).

## <a name="automating-compute-management"></a>Automatisieren der Computeverwaltung

Informationen zum Automatisieren der Computeverwaltungsvorgänge finden Sie unter [Verwenden von Azure Functions zum Automatisieren von SQL DW-Computeebenen](manage-compute-with-azure-functions.md).

Jeder Vorgang zum horizontalen Skalieren, Anhalten und Fortsetzen kann mehrere Minuten in Anspruch nehmen. Wenn Sie das Skalieren, Anhalten oder Fortsetzen automatisch durchführen, empfiehlt es sich, eine Logik zu implementieren, die sicherstellt, dass bestimmte Vorgänge abgeschlossen wurden, bevor mit einer anderen Aktion fortgefahren wird. Überprüfen Sie den Status des SQL-Pools über verschiedene Endpunkte, um sicherzugehen, dass die Automatisierung dieser Vorgänge ordnungsgemäß implementiert werden kann.

Informationen zum Überprüfen des SQL-Pool-Status finden Sie in den Schnellstarts zu [PowerShell](quickstart-scale-compute-powershell.md#check-data-warehouse-state) oder [T-SQL](quickstart-scale-compute-tsql.md#check-data-warehouse-state). Sie können den SQL-Pool-Status auch mit einer [REST-API](sql-data-warehouse-manage-compute-rest-api.md#check-database-state) überprüfen.

## <a name="permissions"></a>Berechtigungen

Zum Skalieren des SQL-Pools sind die in [ALTER DATABASE](/sql/t-sql/statements/alter-database-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) beschriebenen Berechtigungen erforderlich.  Zum Anhalten und Fortsetzen ist die Berechtigung [Mitwirkender von SQL DB](../../role-based-access-control/built-in-roles.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#sql-db-contributor) erforderlich, insbesondere „Microsoft.Sql/servers/databases/action“.

## <a name="next-steps"></a>Nächste Schritte

Sehen Sie sich die Anleitung zum [Verwalten von Computeressourcen](manage-compute-with-azure-functions.md) an. Ein weiterer Aspekt der Verwaltung von Computeressourcen ist das Zuordnen der verschiedenen Computeressourcen zu einzelnen Abfragen. Weitere Informationen finden Sie unter [Ressourcenklassen für die Workloadverwaltung](resource-classes-for-workload-management.md).
