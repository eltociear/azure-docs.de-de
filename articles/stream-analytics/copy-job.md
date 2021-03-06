---
title: Kopieren oder Sichern von Azure Stream Analytics-Aufträgen
description: In diesem Artikel wird beschrieben, wie Sie einen Azure Stream Analytics-Auftrag kopieren oder sichern.
author: su-jie
ms.author: sujie
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 09/11/2019
ms.openlocfilehash: 5c8f770855dd8d19a9d313f1b79f9bf8da4b2393
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75771494"
---
# <a name="copy-or-back-up-azure-stream-analytics-jobs"></a>Kopieren oder Sichern von Azure Stream Analytics-Aufträgen

Sie können Ihre bereitgestellten Azure Stream Analytics-Aufträge mit Visual Studio Code oder Visual Studio kopieren oder sichern. 

## <a name="before-you-begin"></a>Voraussetzungen
* Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen.

* Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.

* Installieren Sie die [Azure Stream Analytics-Erweiterung für Visual Studio Code](quick-create-vs-code.md#install-the-azure-stream-analytics-tools-extension) oder die [Azure Stream Analytics-Tools für Visual Studio](quick-create-vs-code.md#install-the-azure-stream-analytics-tools-extension).  

## <a name="visual-studio-code"></a>Visual Studio Code

1. Klicken Sie auf der Aktivitätsleiste von Visual Studio Code auf das **Azure**-Symbol, und erweitern Sie dann den Knoten **Stream Analytics**. Ihre Aufträge sollten unter Ihren Abonnements angezeigt werden.

   ![Öffnen von Stream Analytics-Explorer](./media/vscode-explore-jobs/open-explorer.png)

2. Suchen Sie den zu exportierenden Auftrag im **Stream Analytics-Explorer** in Visual Studio Code, um einen Auftrag in ein lokales Projekt zu exportieren. Wählen Sie dann einen Ordner für Ihr Projekt aus.

    ![Exportieren von ASA-Aufträgen in Visual Studio Code](./media/vscode-explore-jobs/export-job.png)

    Das Projekt wird in den von Ihnen ausgewählten Ordner exportiert und zu Ihrem aktuellen Arbeitsbereich hinzugefügt.

    ![Exportieren von ASA-Aufträgen in Visual Studio Code](./media/stream-analytics-manage-job/copy-backup-stream-analytics-jobs.png)

3. Um den Auftrag in einer anderen Region zu veröffentlichen oder unter einem anderen Namen zu sichern, wählen Sie **Zum Veröffentlichen aus Ihren Abonnements auswählen** im Abfrage-Editor (\*.asaql) aus, und befolgen Sie die Anweisungen.

    ![Veröffentlichen in Azure in Visual Studio Code](./media/quick-create-vs-code/submit-job.png)

## <a name="visual-studio"></a>Visual Studio

1. Befolgen Sie die [Anweisungen zum Exportieren eines bereitgestellten Azure Stream Analytics-Auftrags zu einem Projekt](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-vs-tools#export-jobs-to-a-project).

2. Öffnen Sie im Abfrage-Editor die \*.asaql-Datei, wählen Sie **An Azure übermitteln** im Skript-Editor aus, und befolgen Sie die Anweisungen, um den Auftrag in einer anderen Region zu veröffentlichen oder unter einem neuen Namen zu sichern.

## <a name="next-steps"></a>Nächste Schritte

* [Schnellstart: Erstellen eines Stream Analytics-Auftrags mithilfe von Visual Studio Code](quick-create-vs-code.md)
* [Schnellstart: Erstellen eines Stream Analytics-Auftrags mithilfe von Visual Studio](stream-analytics-quick-create-vs.md)
* [Bereitstellen eines Azure Stream Analytics-Auftrags mit CI/CD mithilfe von Azure Pipelines](stream-analytics-tools-visual-studio-cicd-vsts.md)
