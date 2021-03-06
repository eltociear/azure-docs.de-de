---
title: Allgemeine SAS-URL-Probleme und Fehlerbehebungen für den Azure Marketplace
description: Liste allgemeiner Probleme in Verbindung mit der Verwendung von SAS-URIs und möglicher Lösungen.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/27/2018
ms.author: dsindona
ms.openlocfilehash: 47702959474a352a8e13710ec850f789dee4d517
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80278160"
---
# <a name="common-sas-url-issues-and-fixes"></a>Allgemeine SAS-URL-Probleme und Fehlerbehebungen

Die folgende Tabelle enthält einige allgemeine Probleme im Zusammenhang mit der Verwendung von Shared Access Signatures (dienen zur Identifizierung und gemeinsamen Verwendung der hochgeladenen VHDs für Ihre Lösung) sowie Lösungsvorschläge.

| **Problem** | **Fehlermeldung** | **Behebung** | 
| --------- | ------------------- | ------- | 
| &emsp; *Fehler beim Kopieren von Images* |  |  |
| „?“ wurde in der SAS-URL nicht gefunden. | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Aktualisieren Sie die SAS-URL mithilfe empfohlener Tools. |
| Die Parameter „st“ und „se“ sind in der SAS-URL nicht enthalten. | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Aktualisieren Sie die SAS-URL mit geeigneten Werten für **Startdatum** und **Enddatum**. | 
| „sp=rl“ ist in der SAS-URL nicht enthalten. | `Failure: Copying Images. Not able to download blob using provided SAS Uri` | Aktualisieren Sie die SAS-URL mit den Berechtigungen `Read` und `List`. | 
| Der VHD-Name in der SAS-URL enthält Leerzeichen. | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Entfernen Sie die Leerzeichen aus der SAS-URL. |
| SAS-URL-Autorisierungsfehler | `Failure: Copying Images. Not able to download blob due to authorization error` | Überprüfen und korrigieren Sie das SAS-URI-Format. Generieren Sie ihn bei Bedarf neu. |
| Die Parameter „st“ und „se“ der SAS-URL verfügen nicht über eine vollständige Angabe für Datum und Uhrzeit. | `Failure: Copying Images. Not able to download blob due to incorrect SAS URL` | Für ein vollständiges Datums- und Uhrzeitformat (Beispiel: `11-02-2017T00:00:00Z`) sind SAS-URL-Parameter für **Startdatum** und **Enddatum** (Teilzeichenfolgen `st` und `se`) erforderlich. Verkürzte Versionen sind nicht zulässig. (Von bestimmten Befehlen der Azure-Befehlszeilenschnittstelle werden unter Umständen standardmäßig verkürzte Werte generiert.) | 
|  |  |  |

Weitere Informationen finden Sie unter [Verwenden von Shared Access Signatures (SAS)](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/).
