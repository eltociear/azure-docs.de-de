---
title: Senden von Ereignissen an eine Umgebung – Azure Time Series Insights | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie einen Event Hub konfigurieren, eine Beispielanwendung ausführen und Ereignisse an Ihre Azure Time Series Insights-Umgebung senden.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 02/11/2020
ms.custom: seodec18
ms.openlocfilehash: c3c7f59ecb3a06d80012917e2da4425a899859d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79228014"
---
# <a name="send-events-to-a-time-series-insights-environment-by-using-an-event-hub"></a>Senden von Ereignissen an die Azure Time Series Insights-Umgebung mithilfe eines Event Hub

In diesem Artikel erfahren Sie, wie Sie einen Event Hub in Azure Event Hubs erstellen und konfigurieren. Außerdem erfahren Sie, wie Sie eine Beispielanwendung ausführen, um Ereignisse mithilfe von Push von Event Hubs zu Azure Time Series Insights zu übertragen. Wenn Sie bereits über einen Event Hub mit Ereignissen im JSON-Format verfügen, überspringen Sie dieses Tutorial, und sehen Sie sich Ihre Umgebung in [Azure Time Series Insights](./time-series-insights-update-create-environment.md) an.

## <a name="configure-an-event-hub"></a>Konfigurieren eines Event Hubs

1. Weitere Informationen, wie Sie einen Event Hub erstellen, finden Sie in der [Event Hubs-Dokumentation](https://docs.microsoft.com/azure/event-hubs/).
1. Suchen Sie im Suchfeld nach **Event Hubs**. Wählen Sie in der Liste mit den zurückgegebenen Ergebnissen **Event Hubs** aus.
1. Wählen Sie Ihren Event Hub aus.
1. Wenn Sie einen Event Hub erstellen, erstellen Sie einen Event Hub-Namespace. Wenn Sie noch keinen Event Hub innerhalb des Namespace erstellt haben, erstellen Sie in dem Menü unter **Entitäten** einen Event Hub.  

    [![Liste der Event Hubs](media/send-events/tsi-connect-event-hub-namespace.png)](media/send-events/tsi-connect-event-hub-namespace.png#lightbox)

1. Nachdem Sie einen Event Hub erstellt haben, wählen Sie ihn in der Liste der Event Hubs aus.
1. Wählen Sie in dem Menü unter **Entitäten** die Option **Event Hubs** aus.
1. Wählen Sie den Namen des Event Hubs aus, um ihn zu konfigurieren.
1. Wählen Sie unter **Übersicht** den Eintrag **Consumergruppen** und dann **Consumergruppe** aus.

    [![Erstellen einer Consumergruppe](media/send-events/add-event-hub-consumer-group.png)](media/send-events/add-event-hub-consumer-group.png#lightbox)

1. Erstellen Sie eine Consumergruppe, die ausschließlich von Ihrer Time Series Insights-Ereignisquelle verwendet wird.

    > [!IMPORTANT]
    > Achten Sie darauf, dass diese Consumergruppe nicht von einem anderen Dienst, beispielsweise durch einen Azure Stream Analytics-Auftrag oder durch eine andere Time Series Insights-Umgebung, verwendet wird. Wenn die Consumergruppe von den anderen Diensten verwendet wird, wirkt sich das negativ auf Lesevorgänge für diese Umgebung und für andere Dienste aus. Wenn Sie **$Default** als Consumergruppe verwenden, können andere Leser Ihre Consumergruppe möglicherweise wiederverwenden.

1. Wählen Sie in dem Menü unter **Einstellungen** die Option **SAS-Richtlinien** und anschließend **Hinzufügen** aus.

    [![Wählen Sie „SAS-Richtlinien“ aus, und klicken Sie dann auf die Schaltfläche „Hinzufügen“](media/send-events/add-shared-access-policy.png)](media/send-events/add-shared-access-policy.png#lightbox).

1. Erstellen Sie im Bereich **Neue SAS-Richtlinie hinzufügen** eine gemeinsame Zugriffsrichtlinie mit dem Namen **MySendPolicy**. Sie verwenden diese gemeinsame Zugriffsrichtlinie zum Senden von Ereignissen in den später in diesem Artikel vorkommenden C#-Beispielen.

    [![Geben Sie in das Feld „Richtlinienname“ den Wert „MySendPolicy“ ein](media/send-events/configure-shared-access-policy-confirm.png)](media/send-events/configure-shared-access-policy-confirm.png#lightbox).

1. Aktivieren Sie unter **Anspruch**, das Kontrollkästchen **Senden**.

## <a name="add-a-time-series-insights-instance"></a>Hinzufügen einer Time Series Insights-Instanz

Das Time Series Insights-Update verwendet Instanzen, um eingehenden Telemetriedaten kontextbezogene Daten hinzuzufügen. Die Daten werden zum Zeitpunkt der Abfrage mithilfe einer **Time Series-ID** verknüpft. Die **Time Series-ID** für das Windenergieanlagen-Beispielprojekt, das wir später in diesem Artikel verwenden, ist `id`. Weitere Informationen zu Time Series Insights-Instanzen und der **Time Series-ID** finden Sie unter [Zeitreihenmodelle](./time-series-insights-update-tsm.md).

### <a name="create-a-time-series-insights-event-source"></a>Erstellen einer Time Series Insights-Ereignisquelle

1. Falls Sie noch keine Ereignisquelle erstellt haben, führen Sie die Schritte aus, um [eine Ereignisquelle zu erstellen](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-eventhub).

1. Legen Sie einen Wert für `timeSeriesId` fest. Weitere Informationen zur **Time Series-ID** finden Sie unter [Zeitreihenmodelle](./time-series-insights-update-tsm.md).

### <a name="push-events-to-windmills-sample"></a>Übertragen von Ereignissen mittels Push in das Windenergieanlagen-Beispiel

1. Suchen Sie über die Suchleiste nach **Event Hubs**. Wählen Sie in der Liste mit den zurückgegebenen Ergebnissen **Event Hubs** aus.

1. Wählen Sie Ihre Event Hub-Instanz aus.

1. Wechseln Sie zu **Freigegebene Zugriffsrichtlinien** > **MySendPolicy**. Kopieren Sie den Wert für **Verbindungszeichenfolge – Primärschlüssel**.

    [![Kopieren des Werts der Primärschlüssel-Verbindungszeichenfolge](media/send-events/configure-sample-code-connection-string.png)](media/send-events/configure-sample-code-connection-string.png#lightbox)

1. Gehe zu https://tsiclientsample.azurewebsites.net/windFarmGen.html. Über die URL werden simulierte Windenergieanlagen erstellt und ausgeführt.
1. Fügen Sie in das Feld **Event Hub-Verbindungszeichenfolge** auf der Webseite die Verbindungszeichenfolge ein, die Sie im [Eingabefeld für Windenergieanlagen](#push-events-to-windmills-sample) kopiert haben.
  
    [![Einfügen der Primärschlüssel-Verbindungszeichenfolge in das Feld „Event Hub-Verbindungszeichenfolge“](media/send-events/configure-wind-mill-sim.png)](media/send-events/configure-wind-mill-sim.png#lightbox)

1. Wählen Sie **Zum Starten klicken** aus. 

    > [!TIP]
    > Der Windenergieanlagen-Simulator erstellt auch JSON-Code, den Sie als Nutzlast für die [Time Series Insights-GA-Abfrage-APIs](https://docs.microsoft.com/rest/api/time-series-insights/ga-query) verwenden können.

    > [!NOTE]
    > Der Simulator sendet weiterhin Daten, bis der Browsertab geschlossen wird.

1. Wechseln Sie zu Ihrem Event Hub im Azure-Portal zurück. Auf der Seite **Übersicht** werden die neuen vom Event Hub empfangenen Ereignisse angezeigt.

    [![Eine Seite „Übersicht“ des Event Hubs, die Metriken für den Event Hub zeigt](media/send-events/review-windmill-telemetry.png)](media/send-events/review-windmill-telemetry.png#lightbox)

## <a name="supported-json-shapes"></a>Unterstützte JSON-Formen

### <a name="example-one"></a>Beispiel eins

* **Eingabe**: Ein einfaches JSON-Objekt.

    ```JSON
    {
        "id":"device1",
        "timestamp":"2016-01-08T01:08:00Z"
    }
    ```

* **Ausgabe**: Ein Ereignis.

    |id|timestamp|
    |--------|---------------|
    |device1|2016-01-08T01:08:00Z|

### <a name="example-two"></a>Beispiel zwei

* **Eingabe**: Ein JSON-Array mit zwei JSON-Objekten. Jedes JSON-Objekt wird in ein Ereignis konvertiert.

    ```JSON
    [
        {
            "id":"device1",
            "timestamp":"2016-01-08T01:08:00Z"
        },
        {
            "id":"device2",
            "timestamp":"2016-01-17T01:17:00Z"
        }
    ]
    ```

* **Ausgabe**: Zwei Ereignisse.

    |id|timestamp|
    |--------|---------------|
    |device1|2016-01-08T01:08:00Z|
    |device2|2016-01-08T01:17:00Z|

### <a name="example-three"></a>Beispiel drei

* **Eingabe**: Ein JSON-Objekt mit einem geschachtelten JSON-Array, das zwei JSON-Objekte enthält.

    ```JSON
    {
        "location":"WestUs",
        "events":[
            {
                "id":"device1",
                "timestamp":"2016-01-08T01:08:00Z"
            },
            {
                "id":"device2",
                "timestamp":"2016-01-17T01:17:00Z"
            }
        ]
    }
    ```

* **Ausgabe**: Zwei Ereignisse. Die **location**-Eigenschaft wird in jedes Ereignis kopiert.

    |location|events.id|events.timestamp|
    |--------|---------------|----------------------|
    |WestUs|device1|2016-01-08T01:08:00Z|
    |WestUs|device2|2016-01-08T01:17:00Z|

### <a name="example-four"></a>Beispiel vier

* **Eingabe**: Ein JSON-Objekt mit einem geschachtelten JSON-Array, das zwei JSON-Objekte enthält. Diese Eingabe zeigt, dass globale Eigenschaften vom komplexen JSON-Objekt dargestellt werden können.

    ```JSON
    {
        "location":"WestUs",
        "manufacturer":{
            "name":"manufacturer1",
            "location":"EastUs"
        },
        "events":[
            {
                "id":"device1",
                "timestamp":"2016-01-08T01:08:00Z",
                "data":{
                    "type":"pressure",
                    "units":"psi",
                    "value":108.09
                }
            },
            {
                "id":"device2",
                "timestamp":"2016-01-17T01:17:00Z",
                "data":{
                    "type":"vibration",
                    "units":"abs G",
                    "value":217.09
                }
            }
        ]
    }
    ```

* **Ausgabe**: Zwei Ereignisse.

    |location|manufacturer.name|manufacturer.location|events.id|events.timestamp|events.data.type|events.data.units|events.data.value|
    |---|---|---|---|---|---|---|---|
    |WestUs|manufacturer1|EastUs|device1|2016-01-08T01:08:00Z|pressure|psi|108.09|
    |WestUs|manufacturer1|EastUs|device2|2016-01-08T01:17:00Z|vibration|abs G|217.09|

## <a name="next-steps"></a>Nächste Schritte

- [Anzeigen Ihrer Umgebung](https://insights.timeseries.azure.com) im Times Series Insights-Explorer.

- Weitere Informationen zu [IoT Hub-Gerätemeldungen](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-construct)
