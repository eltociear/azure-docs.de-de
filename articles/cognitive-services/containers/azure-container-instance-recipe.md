---
title: 'Azure Container Instances: Anleitung'
titleSuffix: Azure Cognitive Services
description: Es wird beschrieben, wie Sie Cognitive Services-Container für Azure Container Instances bereitstellen.
services: cognitive-services
author: aahill
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: f247465c7e2c0a212df2821ebc7165d3ee5b15f3
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/08/2020
ms.locfileid: "80876656"
---
# <a name="deploy-and-run-container-on-azure-container-instance"></a>Bereitstellen und Ausführen eines Containers unter Azure Container Instances

Führen Sie die folgenden Schritte aus, um Azure Cognitive Services-Anwendungen problemlos in der Cloud zu skalieren, indem Sie [Azure Container Instances](https://docs.microsoft.com/azure/container-instances/) verwenden. Mithilfe von Containern können Sie sich auf die Erstellung von Anwendungen konzentrieren und müssen sich nicht um die Verwaltung der Infrastruktur kümmern. Weitere Informationen zur Verwendung von Containern finden Sie unter [Features und Vorteile](../cognitive-services-container-support.md#features-and-benefits).

## <a name="prerequisites"></a>Voraussetzungen

Diese Lösung funktioniert für alle Cognitive Services-Container. Die Cognitive Services-Ressource muss im Azure-Portal erstellt werden, bevor diese Lösung verwendet wird. Jede Cognitive Services-Instanz, die Container unterstützt, verfügt über ein Dokument mit einer Installationsanleitung, indem speziell die Installation und Konfiguration des Diensts für einen Container beschrieben wird. Da für einige Dienste eine Datei oder eine Gruppe von Dateien als Eingabe für den Container benötigt wird, sollten Sie unbedingt mit dem Container vertraut sein und diesen bereits erfolgreich verwendet haben, bevor Sie diese Lösung einsetzen.

* Eine Cognitive Services-Ressource, die im Azure-Portal erstellt wurde.
* Cognitive Services-**Endpunkt-URL**: Lesen Sie die entsprechende Installationsanleitung Ihres Diensts für den Container, um über das Azure-Portal zu ermitteln, wo sich die Endpunkt-URL befindet, und wie ein korrektes Beispiel für die URL aussieht. Das genaue Format kann sich von Dienst zu Dienst unterscheiden.
* Cognitive Services-**Schlüssel**: Die Schlüssel befinden sich auf der Seite **Schlüssel** für die Azure-Ressource. Sie benötigen nur einen der beiden Schlüssel. Der Schlüssel ist eine Zeichenfolge mit 32 alphanumerischen Zeichen.
* Ein einzelner Cognitive Services-Container auf Ihrem lokalen Host (Ihrem Computer). Stellen Sie sicher, dass Folgendes möglich ist:
  * Rufen Sie das Image mit einem `docker pull`-Befehl ab.
  * Führen Sie den lokalen Container erfolgreich mit allen erforderlichen Konfigurationseinstellungen aus, indem Sie einen `docker run`-Befehl verwenden.
  * Rufen Sie den Endpunkt des Containers auf, um eine HTTP 2xx-Antwort und eine JSON-Antwort zu erhalten.

Sie müssen alle Variablen in spitzen Klammern (`<>`) durch Ihre eigenen Werte ersetzen. Hierbei müssen auch die spitzen Klammern ersetzt werden.

[!INCLUDE [Create a Text Analytics Containers on Azure Container Instances](includes/create-container-instances-resource.md)]

## <a name="use-the-container-instance"></a>Verwenden der Containerinstanz

1. Wählen Sie **Übersicht** aus, und kopieren Sie die IP-Adresse. Dies ist eine numerische IP-Adresse, z. B. `55.55.55.55`.
1. Öffnen Sie eine neue Browserregisterkarte, und verwenden Sie die IP-Adresse, z. B. `http://<IP-address>:5000 (http://55.55.55.55:5000`). Sie sehen die Homepage des Containers und erkennen daran, dass der Container ausgeführt wird.

1. Wählen Sie **Dienst-API-Beschreibung**, um die Swagger-Seite für den Container anzuzeigen.

1. Wählen Sie eine der **POST**-APIs und dann **Ausprobieren** aus.  Die Parameter werden einschließlich der Eingabe angezeigt. Fügen Sie die Parameter ein.

1. Wählen Sie **Ausführen**, um die Anforderung an Ihre Containerinstanz zu senden.

    Sie haben Cognitive Services-Container in Azure Container Instances erfolgreich erstellt und verwendet.
