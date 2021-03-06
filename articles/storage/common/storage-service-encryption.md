---
title: Azure Storage-Verschlüsselung für ruhende Daten
description: Azure Storage schützt Ihre Daten, indem der Dienst diese automatisch verschlüsselt, bevor diese in der Cloud gespeichert werden. Sie können von Microsoft verwaltete Schlüssel für die Verschlüsselung der Daten in Ihrem Speicherkonto nutzen oder die Verschlüsselung mit Ihren eigenen Schlüsseln verwalten.
services: storage
author: tamram
ms.service: storage
ms.date: 03/12/2020
ms.topic: conceptual
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: f8f6f40f8ce8297b3cbfe6b3afcbf10df4db6572
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79409829"
---
# <a name="azure-storage-encryption-for-data-at-rest"></a>Azure Storage-Verschlüsselung für ruhende Daten

Mit Azure Storage werden Ihre Daten beim Speichern in der Cloud automatisch verschlüsselt. Durch die Azure Storage-Verschlüsselung werden Ihre Daten ausreichend geschützt, um den Sicherheits- und Complianceanforderungen Ihrer Organisation gerecht zu werden.

## <a name="about-azure-storage-encryption"></a>Informationen zur Azure Storage-Verschlüsselung

Daten in Azure Storage werden auf transparente Weise mit der [AES-256-Verschlüsselung](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) ver- und entschlüsselt, einer der stärksten verfügbaren Blockchiffren, und sind mit dem FIPS 140-2-Standard konform. Die Azure Storage-Verschlüsselung ähnelt der BitLocker-Verschlüsselung unter Windows.

Die Azure Storage-Verschlüsselung ist für alle Speicherkonten aktiviert, einschließlich Resource Manager-Speicherkonten und klassischer Speicherkonten. Die Azure Storage-Verschlüsselung kann nicht deaktiviert werden. Da Ihre Daten standardmäßig geschützt werden, müssen Sie weder Code noch Anwendungen ändern, um die Azure Storage-Verschlüsselung nutzen zu können.

Daten in einem Speicherkonto werden unabhängig von der Leistungsstufe (Standard oder Premium), der Zugriffsebene (heiß oder kalt) und dem Bereitstellungsmodell (Azure Resource Manager oder klassisch) verschlüsselt. Außerdem werden auch alle Blobs auf der Archivebene verschlüsselt. Die Verschlüsselung wird von allen Azure Storage-Redundanzoptionen unterstützt, und bei aktivierter Georeplikation werden alle Daten in der primären und sekundären Region verschlüsselt. Alle Azure Storage-Ressourcen werden verschlüsselt, z. B. Blobs, Datenträger, Dateien, Warteschlangen und Tabellen. Objektmetadaten werden ebenfalls verschlüsselt. Es fallen keine zusätzlichen Kosten für die Azure Storage-Verschlüsselung an.

Alle Blockblobs, Anfügeblobs und Seitenblobs, die nach dem 20. Oktober 2017 in Azure Storage geschrieben wurden, sind verschlüsselt. Blobs, die vor diesem Datum erstellt wurden, werden weiterhin durch einen Hintergrundprozess verschlüsselt. Um die Verschlüsselung eines Blobs zu erzwingen, das vor dem 20. Oktober 2017 erstellt wurde, können Sie das Blob neu schreiben. Informationen zum Überprüfen des Verschlüsselungsstatus eines Blobs finden Sie unter [Überprüfen des Verschlüsselungsstatus eines Blobs](../blobs/storage-blob-encryption-status.md).

Weitere Informationen zu den kryptografischen Modulen, die der Azure Storage-Verschlüsselung zugrunde liegen, finden Sie unter [Kryptografie-API: Die nächste Generation](https://docs.microsoft.com/windows/desktop/seccng/cng-portal).

## <a name="about-encryption-key-management"></a>Informationen zur Verwaltung von Verschlüsselungsschlüsseln

Standardmäßig werden Daten in einem Speicherkonto mit von Microsoft verwalteten Schlüsseln verschlüsselt. Sie können von Microsoft verwaltete Schlüssel für die Verschlüsselung Ihrer Daten nutzen oder die Verschlüsselung mit Ihren eigenen Schlüsseln verwalten. Wenn Sie die Verschlüsselungsverwaltung mit Ihren eigenen Schlüsseln wählen, haben Sie zwei Optionen:

- Sie können einen *kundenseitig verwalteten Schlüssel* mit Azure Key Vault für die Ver- und Entschlüsselung von Daten im Blobspeicher und in Azure Files angeben.<sup>1,2</sup> Weitere Informationen zu kundenseitig verwalteten Schlüsseln finden Sie unter [Verwenden kundenseitig verwalteter Schlüssel mit Azure Key Vault für die Verwaltung der Azure Storage-Verschlüsselung](encryption-customer-managed-keys.md).
- Sie können einen *vom Kunden bereitgestellten Schlüssel* für Blob-Speichervorgänge angeben. Ein Client, der eine Lese- oder Schreibanforderung für Blob Storage sendet, kann einen Verschlüsselungsschlüssel für die Anforderung enthalten, um genau steuern zu können, wie Blobdaten verschlüsselt und entschlüsselt werden. Weitere Informationen zu vom Kunden bereitgestellten Schlüsseln finden Sie unter [Angeben eines Verschlüsselungsschlüssels bei Stellen einer Anforderung für Blob Storage (Vorschau)](encryption-customer-provided-keys.md).

In der folgenden Tabelle werden die Schlüsselverwaltungsoptionen für Azure Storage-Verschlüsselung verglichen.

|                                        |    Von Microsoft verwaltete Schlüssel                             |    Vom Kunden verwaltete Schlüssel                                                                                                                        |    Vom Kunden bereitgestellte Schlüssel                                                          |
|----------------------------------------|-------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------|
|    Verschlüsselungs-/Entschlüsselungsvorgänge    |    Azure                                              |    Azure                                                                                                                                        |    Azure                                                                         |
|    Unterstützte Azure Storage-Dienste    |    All                                                |    Blob Storage, Azure Files<sup>1,2</sup>                                                                                                               |    Blob Storage                                                                  |
|    Schlüsselspeicher                         |    Microsoft-Schlüsselspeicher    |    Azure-Schlüsseltresor                                                                                                                              |    Azure Key Vault oder ein beliebiger anderer Schlüsselspeicher                                                                 |
|    Verantwortlich für die Schlüsselrotation         |    Microsoft                                          |    Kunde                                                                                                                                     |    Kunde                                                                      |
|    Schlüsselverwendung                           |    Microsoft                                          |    Azure-Portal, REST-API für Speicherressourcenanbieter, Azure Storage-Verwaltungsbibliotheken, PowerShell, CLI        |    REST-API für Azure Storage (Blob Storage), Azure Storage-Clientbibliotheken    |
|    Schlüsselzugriff                          |    Nur Microsoft                                     |    Microsoft, Kunde                                                                                                                    |    Nur Kunde                                                                 |

<sup>1</sup> Informationen zum Erstellen eines Kontos, das die Verwendung von kundenseitig verwalteten Schlüsseln mit Queue Storage unterstützt, finden Sie unter [Erstellen eines Kontos, das kundenseitig verwaltete Schlüssel für Warteschlangen](account-encryption-key-create.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json) unterstützt.<br />
<sup>2</sup> Informationen zum Erstellen eines Kontos, das die Verwendung von kundenseitig verwalteten Schlüsseln mit Table Storage unterstützt, finden Sie unter [Erstellen eines Kontos, das kundenseitig verwaltete Schlüssel für Tabellen](account-encryption-key-create.md?toc=%2fazure%2fstorage%2ftables%2ftoc.json) unterstützt.

## <a name="next-steps"></a>Nächste Schritte

- [Was ist der Azure-Schlüsseltresor?](../../key-vault/key-vault-overview.md)
- [Configure customer-managed keys for Azure Storage encryption from the Azure portal (Konfigurieren von Kunden verwalteter Schlüssel für die Azure Storage-Verschlüsselung mithilfe des Azure-Portals)](storage-encryption-keys-portal.md)
- [Configure customer-managed keys for Azure Storage encryption from PowerShell (Konfigurieren von Kunden verwalteter Schlüssel für die Azure Storage-Verschlüsselung mithilfe von Azure PowerShell)](storage-encryption-keys-powershell.md)
- [Konfigurieren von von Kunden verwalteten Schlüsseln für die Azure Storage-Verschlüsselung mithilfe der Azure CLI](storage-encryption-keys-cli.md)
