---
title: Machine Learning Services mit R (Vorschau)
description: In diesem Artikel werden die Machine Learning Services (mit R) in Azure SQL-Datenbank und ihre Funktionsweise erläutert.
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: dphansen
ms.author: davidph
ms.reviewer: carlrab
manager: cgronlun
ms.date: 11/20/2019
ms.openlocfilehash: 2a2cd4bfc3d393543b41eea776f02723d94054b1
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80345828"
---
# <a name="azure-sql-database-machine-learning-services-with-r-preview"></a>Machine Learning Services (mit R) in Azure SQL-Datenbank (Vorschauversion)

Machine Learning Services ist eine Funktion von Azure SQL-Datenbank zum Ausführen von R-Skripts in der Datenbank. Die Funktion umfasst Microsoft-R-Pakete für leistungsstarke Predictive Analytics und maschinelles Lernen. Die relationalen Daten können über gespeicherte Prozeduren, R-Anweisungen in T-SQL-Skripts oder R-Code mit T-SQL in R-Skripts verwendet werden.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

> [!NOTE]
> Die Vorschau ist für Einzeldatenbanken und Pools für elastische Datenbanken mit dem vCore-basierten Kaufmodell in den Dienstebenen **Universell** und **Unternehmenskritisch** verfügbar. In dieser ersten Vorschauversion werden weder die Dienstebene **Hyperscale** noch die Bereitstellungsoption **Verwaltete Instanz** unterstützt. R ist derzeit die einzige unterstützte Sprache. Momentan ist keine Unterstützung für Python vorhanden.
>
> Die Vorschauversion ist derzeit in den folgenden Regionen verfügbar: „Europa, Westen“, „Europa, Norden“, „USA, Westen 2“, „USA, Osten“, „USA, Süden-Mitte“, „USA, Norden-Mitte“, „Kanada, Mitte“, „Asien, Südosten“, „Indien, Süden“ und „Australien, Südosten“.

## <a name="what-you-can-do-with-r"></a>Einsatzmöglichkeiten für R

Nutzen Sie die Leistung der Sprache R für erweiterte Analysen und maschinelles Lernen in der Datenbank. Diese Fähigkeit ermöglicht Berechnungen und die Verarbeitung an dem Ort, an dem sich die Daten befinden, sodass diese nicht mehr über das Netzwerk gepullt werden müssen. Außerdem können Sie die Leistungsfähigkeit der R-Pakete für Unternehmen nutzen, um erweiterte Analysen in einem großen Maßstab durchzuführen.

Machine Learning Services umfasst eine Basisdistribution von R zusammen mit R-Paketen für Unternehmen von Microsoft. Die R-Funktionen und -Algorithmen von Microsoft sind auf Skalierbarkeit und Anwendbarkeit ausgelegt. Sie ermöglichen Predictive Analytics, statistische Modellierung, Datenvisualisierungen und leistungsstarke Algorithmen für maschinelles Lernen.

### <a name="r-packages"></a>R-Pakete

Die am häufigsten verwendeten Open-Source-R-Pakete sind bei Machine Learning Services bereits vorinstalliert. Zudem sind die folgenden R-Pakete von Microsoft bereits enthalten:

| R-Paket | BESCHREIBUNG|
|-|-|
| [Microsoft R Open](https://mran.microsoft.com/rro) | Microsoft R Open ist die erweiterte R-Distribution von Microsoft. Diese umfassende Open-Source-Plattform wird für statistische Analysen und Data Science verwendet. Sie basiert auf R und ist vollständig kompatibel mit R. Zudem beinhaltet sie Funktionen für eine verbesserte Leistung und Reproduzierbarkeit. |
| [RevoScaleR](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-revoscaler) | RevoScaleR ist die primäre Bibliothek für skalierbares R. Die Funktionen in dieser Bibliothek gehören zu den am häufigsten verwendeten. Datentransformationen und -manipulationen, statistische Zusammenfassung, Visualisierung und viele Formen der Modellierung und Analyse sind in diesen Bibliotheken enthalten. Darüber hinaus verteilen die Funktionen in diesen Bibliotheken die Workloads automatisch auf die verfügbaren Kerne, um die Verarbeitung zu parallelisieren, und sie bieten die Möglichkeit, die Daten in Blöcken zu verarbeiten, die von der Berechnungs-Engine koordiniert und verwaltet werden. |
| [MicrosoftML (R)](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-microsoftml) | MicrosoftML fügt Algorithmen für maschinelles Lernen hinzu, mit denen Sie benutzerdefinierte Modelle für die Text-, Bild- und Standpunktanalyse erstellen können. |

Zusätzlich zu den vorinstallierten Paketen können Sie [weitere Pakete installieren](sql-database-machine-learning-services-add-r-packages.md).

<a name="signup"></a>

## <a name="sign-up-for-the-preview-closed"></a>Registrieren für die Vorschau (geschlossen)

> [!IMPORTANT]
> Die Registrierung bei Machine Learning Services von Azure SQL-Datenbank (Vorschauversion) ist derzeit **geschlossen**.

## <a name="next-steps"></a>Nächste Schritte

- Lesen Sie [Key differences from SQL Server Machine Learning Services](sql-database-machine-learning-services-differences.md) (Wichtige Unterschiede zwischen Machine Learning Services in Azure SQL Server).
- Wenn Sie erfahren möchten, wie Sie R zum Abfragen von Machine Learning Services in Azure SQL-Datenbank (Vorschauversion) verwenden, lesen Sie die [Schnellstartanleitung](sql-database-connect-query-r.md).
- Für die ersten Schritte mit einfachen R-Skripts lesen Sie [Erstellen und Ausführen einfacher R-Skripts in Machine Learning Services von Azure SQL-Datenbank (Vorschauversion)](sql-database-quickstart-r-create-script.md).
