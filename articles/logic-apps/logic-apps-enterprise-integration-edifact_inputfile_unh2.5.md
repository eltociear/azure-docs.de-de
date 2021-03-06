---
title: UNH2.5-Segmente in EDIFACT-Nachrichten
description: Auflösen von EDIFACT-Nachrichten mit UNH2.5-Segmenten in Azure Logic Apps mit Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 04/27/2017
ms.openlocfilehash: ad50cbb423f8c60f1caad159bc1a20cf96ed98aa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74792541"
---
# <a name="handle-edifact-documents-with-unh25-segments-in-azure-logic-apps"></a>Verarbeiten von EDIFACT-Dokumenten mit UNH2.5-Segmenten in Azure Logic Apps

Wenn ein EDIFACT-Dokument ein UNH2.5-Segment enthält, wird das Segment für die Schemasuche verwendet. In der folgenden exemplarischen EDIFACT-Nachricht ist das UNH-Feld beispielsweise `EAN008`:

`UNH+SSDD1+ORDERS:D:03B:UN:EAN008`

Gehen Sie zur Verarbeitung dieser Nachricht wie folgt vor:

1. Aktualisieren Sie das Schema.

1. Überprüfen Sie die Vereinbarungseinstellungen.

## <a name="update-the-schema"></a>Aktualisieren des Schemas

Um die Nachricht zu verarbeiten, müssen Sie ein Schema bereitstellen, das über den UNH2.5-Stammknotennamen verfügt. Der Schemastammname für das exemplarische UNH-Feld ist beispielsweise `EFACT_D03B_ORDERS_EAN008`. Für jedes Element vom Typ `D03B_ORDERS`, das über ein anderes UNH2.5-Segment verfügt, muss ein eigenes Schema bereitgestellt werden.

## <a name="add-schema-to-edifact-agreement"></a>Hinzufügen des Schemas zur EDIFACT-Vereinbarung

### <a name="edifact-decode"></a>Decodieren von EDIFACT-Nachrichten

Richten Sie das Schema in den Empfangseinstellungen der EDIFACT-Vereinbarung ein, um die eingehende Nachricht zu decodieren:

1. Öffnen Sie im [Azure-Portal](https://portal.azure.com) Ihr Integrationskonto.

1. Fügen Sie das Schema Ihrem Integrationskonto hinzu.

1. Konfigurieren Sie das Schema in den Empfangseinstellungen der EDIFACT-Vereinbarung.

1. Wählen Sie die EDIFACT-Vereinbarung und anschließend **Als JSON bearbeiten** aus. Fügen Sie den UNH2.5-Wert dem Abschnitt `schemaReferences` der Empfangsvereinbarung hinzu:

   ![Hinzufügen des UNH2.5-Werts zur Empfangsvereinbarung](./media/logic-apps-enterprise-integration-edifact_inputfile_unh2.5/image1.png)

### <a name="edifact-encode"></a>Codieren von EDIFACT-Nachrichten

Richten Sie das Schema in den Sendeeinstellungen der EDIFACT-Vereinbarung ein, um die eingehende Nachricht zu codieren:

1. Öffnen Sie im [Azure-Portal](https://portal.azure.com) Ihr Integrationskonto.

1. Fügen Sie das Schema Ihrem Integrationskonto hinzu.

1. Konfigurieren Sie das Schema in den Sendeeinstellungen der EDIFACT-Vereinbarung.

1. Wählen Sie die EDIFACT-Vereinbarung aus, und klicken Sie auf **Als JSON bearbeiten**.  Fügen Sie den UNH2.5-Wert in der Sendevereinbarung dem Abschnitt **schemaReferences** hinzu.

1. Wählen Sie die EDIFACT-Vereinbarung und anschließend **Als JSON bearbeiten** aus. Fügen Sie den UNH2.5-Wert dem Abschnitt `schemaReferences` der Sendevereinbarung hinzu:

   ![Hinzufügen des UNH2.5-Werts zur Sendevereinbarung](./media/logic-apps-enterprise-integration-edifact_inputfile_unh2.5/image2.png)

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zu Integrationskontovereinbarungen finden Sie [hier](../logic-apps/logic-apps-enterprise-integration-agreements.md).