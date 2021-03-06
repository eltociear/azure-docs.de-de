---
title: Elastisches Skalieren mit der Cassandra-API in Azure Cosmos DB
description: Erfahren Sie mehr über die zum Skalieren eines Cassandra-API-Kontos für Azure Cosmos DB verfügbaren Optionen und ihre Vor- und Nachteile.
author: TheovanKraay
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 01/13/2020
ms.author: thvankra
ms.openlocfilehash: 10d81de48c0d8f56c7c3fd26e3fd82a8c3df84c6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79474678"
---
# <a name="elastically-scale-an-azure-cosmos-db-cassandra-api-account"></a>Elastisches Skalieren eines Cassandra-API-Kontos für Azure Cosmos DB

Es gibt verschiedene Möglichkeiten, die elastische Natur der Azure Cosmos DB-API für Cassandra zu erkunden. Um herauszufinden, wie eine effektive Skalierung in Azure Cosmos DB funktioniert, müssen Sie wissen, wie Sie die richtige Menge an Anforderungseinheiten (Request Units, RUs) bereitstellen, um die Leistungsanforderungen in Ihrem System zu erfüllen. Weitere Informationen zu Anforderungseinheiten finden Sie im Artikel [Anforderungseinheiten](request-units.md). 

Für die Cassandra-API können Sie die Belastung durch Anforderungseinheiten für einzelne Abfragen mit dem [.NET SDK und Java-SDK](https://docs.microsoft.com/azure/cosmos-db/find-request-unit-charge#cassandra-api) abrufen. Dies ist hilfreich, wenn Sie die Anzahl der Anforderungseinheiten ermitteln möchten, die Sie im Dienst bereitstellen müssen.

![Von Datenbankvorgängen genutzte Anforderungseinheiten](./media/request-units/request-units.png)

## <a name="handling-rate-limiting-429-errors"></a>Verarbeiten der Ratenbegrenzung (Fehler 429)

Azure Cosmos DB gibt Fehler zur Ratenbegrenzung (429) zurück, wenn Clients mehr Ressourcen (Anforderungseinheiten) verbrauchen als die von Ihnen bereitgestellte Menge. Diese Ausnahmen werden von der Cassandra-API in Azure Cosmos DB in Überladungsfehler im nativen Cassandra-Protokoll übersetzt. 

Wenn die Latenz in Ihrem System keine Rolle spielt, kann es ausreichen, die Begrenzung der Durchsatzrate mithilfe von Wiederholungsversuchen zu verarbeiten. Informationen zur transparenten Verarbeitung der Ratenbegrenzung mithilfe der [Azure Cosmos DB-Erweiterung](https://github.com/Azure/azure-cosmos-cassandra-extensions) für die [Cassandra-Wiederholungsrichtlinie](https://docs.datastax.com/en/developer/java-driver/4.4/manual/core/retries/) in Java finden Sie in diesem [Java-Codebeispiel](https://github.com/Azure-Samples/azure-cosmos-cassandra-java-retry-sample). Sie können auch die [Spark-Erweiterung](https://mvnrepository.com/artifact/com.microsoft.azure.cosmosdb/azure-cosmos-cassandra-spark-helper) verwenden, um die Ratenbegrenzung zu verarbeiten.

## <a name="manage-scaling"></a>Verwalten der Skalierung

Wenn Sie die Latenz minimieren müssen, gibt es in der Cassandra-API eine ganze Reihe von Optionen zum Verwalten der Skalierung und Bereitstellen des Durchsatzes (RUs):

* [Manuell über das Azure-Portal](#use-azure-portal)
* [Programmgesteuert über die Funktionen der Steuerungsebene](#use-control-plane)
* [Programmgesteuert über CQL-Befehle mit einem bestimmten SDK](#use-cql-queries)
* [Dynamisch über Autopilot](#use-autopilot)

In den folgenden Abschnitten werden die Vor- und Nachteile der einzelnen Ansätze beschrieben. So können Sie die beste Strategie auswählen, um die Skalierungsanforderungen Ihres Systems, die Gesamtkosten und die Effizienzanforderungen Ihrer Lösung gegeneinander abzuwägen.

## <a name="use-the-azure-portal"></a><a id="use-azure-portal"></a>Verwenden des Azure-Portals

Sie können die Ressourcen im Cassandra-API-Konto für Azure Cosmos DB im Azure-Portal skalieren. Weitere Informationen finden Sie unter [Bereitstellen des Durchsatzes für Container und Datenbanken](set-throughput.md). In diesem Artikel werden die relativen Vorteile erläutert, von denen Sie profitieren, wenn Sie den Durchsatz im Azure-Portal auf der Ebene [Datenbank](set-throughput.md#set-throughput-on-a-database) oder auf der Ebene [Container](set-throughput.md#set-throughput-on-a-container) festlegen. Die in diesen Artikeln verwendeten Begriffe „Datenbank“ und „Container“ entsprechen den Begriffen „Keyspace“ und „Tabelle“ für die Cassandra-API.

Der Vorteil dieser Methode besteht darin, dass sie eine unkomplizierte, „schlüsselfertige“ Möglichkeit zur Verwaltung der Durchsatzkapazität in der Datenbank darstellt. Sie birgt allerdings den Nachteil, dass in vielen Fällen bestimmte Automatisierungsebenen sowohl kostengünstig als auch hochleistungsfähig sein müssen, damit Ihr Skalierungsansatz funktioniert. In den nächsten Abschnitten werden die relevanten Szenarien und Methoden erläutert.

## <a name="use-the-control-plane"></a><a id="use-control-plane"></a>Verwenden der Steuerungsebene

Die Azure Cosmos DB-API für Cassandra bietet die Möglichkeit, den Durchsatz mithilfe unserer verschiedenen Features auf Steuerungsebene programmgesteuert anzupassen. Anleitungen und Beispiele finden Sie in den entsprechenden Artikeln zu [Azure Resource Manager](manage-cassandra-with-resource-manager.md), [PowerShell](powershell-samples-cassandra.md) und [Azure CLI](cli-samples-cassandra.md).

Diese Methode bietet den Vorteil, dass Sie das Hoch- oder Herunterskalieren von Ressourcen timerbasiert automatisieren können, um sowohl Lastspitzen als auch Zeiträume mit niedriger Aktivität zu berücksichtigen. Sehen Sie sich das Beispiel [hier](https://github.com/Azure-Samples/azure-cosmos-throughput-scheduler) an, um zu erfahren, wie Sie dies mit Azure Functions und PowerShell erreichen.

Ein Nachteil dieses Ansatzes ist, dass Sie nicht in Echtzeit auf unvorhersehbare Änderungen bei den Skalierungsanforderungen reagieren können. Stattdessen müssen Sie möglicherweise den Anwendungskontext in Ihrem System auf Client-/SDK-Ebene in Betracht ziehen oder [Autopilot](provision-throughput-autopilot.md) nutzen.

## <a name="use-cql-queries-with-a-specific-sdk"></a><a id="use-cql-queries"></a>Verwenden von CQL-Abfragen mit einem bestimmten SDK

Sie können das System dynamisch per Code skalieren, indem Sie [CQL ALTER-Befehle](cassandra-support.md#keyspace-and-table-options) für die betreffende Datenbank oder den betreffenden Container ausführen.

Dieser Ansatz bietet den Vorteil, dass Sie dynamisch und auf eine Weise, die sich für die jeweilige Anwendung optimal eignet, auf Skalierungsanforderungen reagieren können. Bei diesem Ansatz können Sie weiterhin von den Standardgebühren und -preisen für Anforderungseinheiten profitieren. Wenn die Skalierungsanforderungen Ihres Systems größtenteils vorhersehbar sind (etwa 70 % oder mehr), ist das SDK mit CQL möglicherweise eine kostengünstigere Methode der automatischen Skalierung als Autopilot. Der Nachteil hierbei ist, dass die Implementierung von Wiederholungsversuchen sehr komplex sein und die Ratenbegrenzung zu höheren Latenzen führen kann.

## <a name="use-autopilot"></a><a id="use-autopilot"></a>Verwenden von Autopilot

Zusätzlich zur manuellen oder programmgesteuerten Bereitstellung von Durchsatz können Sie auch Azure Cosmos-Container im Autopilot-Modus konfigurieren. Autopilot führt eine automatische und sofortige Skalierung auf die Nutzungsanforderungen innerhalb der angegebenen RU-Bereiche durch, ohne dass dabei SLAs gefährdet werden. Weitere Informationen finden Sie im Artikel [Erstellen von Azure Cosmos-Containern und -Datenbanken im Autopilot-Modus](provision-throughput-autopilot.md).

Der Vorteil dieses Ansatzes: Er ist die einfachste Methode zum Verwalten der Skalierungsanforderungen in Ihrem System. Dieser Ansatz garantiert, dass **innerhalb der konfigurierten RU-Bereiche** keine Ratenbegrenzung angewendet wird. Der Nachteil: Wenn die Skalierungsanforderungen in Ihrem System vorhersagbar sind, ist Autopilot möglicherweise weniger kosteneffektiv als die Verwendung der oben beschriebenen Methoden mit der Steuerungsebene oder der Verwendung des SDK.

## <a name="next-steps"></a>Nächste Schritte

- Erste Schritte mit dem [Erstellen eines Cassandra-API-Kontos, einer Datenbank und einer Tabelle](create-cassandra-api-account-java.md) mithilfe einer Java-Anwendung
