---
title: Problembehandlung – Azure IoT Hub-Fehler „504101 GatewayTimeout“
description: Grundlegendes zum Beheben des Fehlers „504101 GatewayTimeout“
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: b74a93e15d533bf9b15797e6371a25230f7a08f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76960255"
---
# <a name="504101-gatewaytimeout"></a>504101 GatewayTimeout

In diesem Artikel werden die Ursachen des Fehlers **504101 GatewayTimeout** und die Lösungen dafür beschrieben.

## <a name="symptoms"></a>Symptome

Wenn Sie versuchen, eine direkte Methode von IoT Hub für ein Gerät aufzurufen, schlägt die Anforderung mit der Meldung **504101 GatewayTimeout** fehl.

## <a name="cause"></a>Ursache

### <a name="cause-1"></a>Ursache 1

IoT Hub hat einen Fehler festgestellt und konnte nicht bestätigen, ob die direkte Methode vor dem Timeout abgeschlossen wurde.

### <a name="cause-2"></a>Ursache 2

Wenn eine frühere Version des Azure IoT C# SDKs (< 1.19.0) verwendet wird, kann der AMQP-Link zwischen dem Gerät und IoT Hub aufgrund eines Fehlers automatisch gelöscht werden.

## <a name="solution"></a>Lösung

### <a name="solution-1"></a>Lösung 1

Geben Sie eine Wiederholung aus.

### <a name="solution-2"></a>Lösung 2

Führen Sie ein Upgrade auf die neueste Version des Azure IoT C# SDKs durch.