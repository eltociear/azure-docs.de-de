---
title: Benutzerdefinierte Daten und virtuelle Azure-Computer
description: Details zur Verwendung von benutzerdefinierten Daten und cloud-init auf virtuellen Azure-Computern
services: virtual-machines
author: mimckitt
ms.service: virtual-machines
ms.topic: article
ms.date: 03/06/2020
ms.author: mimckitt
ms.openlocfilehash: aadac082e90a19d1a185dd7e6181a490adb70a10
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80089194"
---
# <a name="custom-data-and-cloud-init-on-azure-virtual-machines"></a>Benutzerdefinierte Daten und cloud-init auf virtuellen Azure-Computern

## <a name="what-is-custom-data"></a>Was sind benutzerdefinierte Daten?

Kunden fragen häufig, wie sie zum Zeitpunkt der Bereitstellung ein Skript oder andere Metadaten in einen virtuellen Microsoft Azure-Computer einfügen können.  In anderen Clouds wird dieses Konzept häufig als Benutzerdaten bezeichnet.  In Microsoft Azure verfügen wir über ein ähnliches Feature, das als benutzerdefinierte Daten bezeichnet wird. 

Benutzerdefinierte Daten werden nur beim ersten Start bzw. beim ersten Setup für die VM verfügbar gemacht. Dies wird als Bereitstellung bezeichnet. Die Bereitstellung ist der Vorgang, bei dem VM-Erstellungsparameter (z. B. Hostname, Benutzername, Kennwort, Zertifikate, benutzerdefinierte Daten, Schlüssel usw.) für den virtuellen Computer verfügbar gemacht werden. Dabei werden sie von einem Bereitstellungs-Agent verarbeitet, z. B. dem [Linux-Agent](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) und [cloud-init](https://docs.microsoft.com/azure/virtual-machines/linux/using-cloud-init#troubleshooting-cloud-init). 


## <a name="passing-custom-data-to-the-vm"></a>Übergeben von benutzerdefinierten Daten an die VM
Wenn Sie benutzerdefinierte Daten verwenden möchten, müssen Sie die Inhalte zunächst mit Base64 codieren, bevor Sie sie an die API übergeben. Dies gilt jedoch nicht, wenn Sie ein CLI-Tool verwenden, das diese Konvertierung durchführt, z. B. die Azure-Befehlszeilenschnittstelle (AZ CLI). Die Dateigröße darf 64 KB nicht überschreiten.

In der Befehlszeilenschnittstelle können Sie Ihre benutzerdefinierten Daten als Datei übergeben. Sie werden dann in Base64 konvertiert.
```bash
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS-CI:7-CI:latest \
  --custom-data cloud-init.txt \
  --generate-ssh-keys
```

In Azure Resource Manager (ARM) gibt es eine [Base64-Funktion](https://docs.microsoft.com/azure/azure-resource-manager/templates/template-functions-string#base64).

```json
"name": "[parameters('virtualMachineName')]",
"type": "Microsoft.Compute/virtualMachines",
"apiVersion": "2019-07-01",
"location": "[parameters('location')]",
"dependsOn": [
..],
"variables": {
        "customDataBase64": "[base64(parameters('stringData'))]"
    },
"properties": {
..
    "osProfile": {
        "computerName": "[parameters('virtualMachineName')]",
        "adminUsername": "[parameters('adminUsername')]",
        "adminPassword": "[parameters('adminPassword')]",
        "customDataBase64": "[variables('customData')]"
        },
```

## <a name="processing-custom-data"></a>Verarbeiten von benutzerdefinierten Daten
Die auf den virtuellen Computern installierten Bereitstellungs-Agents verarbeiten die Verbindung mit der Plattform und das Platzieren im Dateisystem. 

### <a name="windows"></a>Windows
Benutzerdefinierte Daten werden in *%SYSTEMDRIVE%\AzureData\CustomData.bin* als Binärdatei abgelegt, jedoch nicht verarbeitet. Wenn Sie diese Datei verarbeiten möchten, müssen Sie ein benutzerdefiniertes Image erstellen und Code schreiben, der die Datei „CustomData.bin“ verarbeitet.

### <a name="linux"></a>Linux  
Unter Linux-Betriebssystemen werden benutzerdefinierte Daten über die Datei „ovf-env.xml“ an den virtuellen Computer weitergegeben, die während der Bereitstellung in das Verzeichnis */var/lib/waagent* kopiert wird.  Neuere Versionen des Microsoft Azure-Linux-Agents kopieren die Base64-codierten Daten zur Vereinfachung ebenfalls in */var/lib/waagent/CustomData*.

Azure unterstützt derzeit zwei Bereitstellungs-Agents:
* Linux-Agent: Standardmäßig verarbeitet der Agent keine benutzerdefinierten Daten. Sie müssen ein benutzerdefiniertes Image erstellen, in dem diese Verarbeitung aktiviert ist. Die relevanten Einstellungen gemäß der [Dokumentation](https://github.com/Azure/WALinuxAgent#configuration) sind:
    * Provisioning.DecodeCustomData
    * Provisioning.ExecuteCustomData

Wenn Sie benutzerdefinierte Daten aktivieren und ein Skript ausführen, wird die Meldung der VM, dass sie bereit ist oder die Bereitstellung erfolgreich war, bis zum Abschluss des Skripts verzögert. Wenn das Skript die zulässige VM-Bereitstellungdauer von 40 Minuten überschreitet, tritt beim Erstellen des virtuellen Computers ein Fehler auf. Beachten Sie: Wenn beim Ausführen des Skripts ein Fehler auftritt, wird dies nicht als schwerwiegender Bereitstellungsfehler angesehen. Sie müssen einen Benachrichtigungspfad erstellen, um über den Abschlussstatus des Skripts informiert zu werden.

Weitere Informationen zum Troubleshooting für die Ausführung von benutzerdefinierten Daten finden Sie unter */var/log/waagent.log*.

* cloud-init: Standard für die Verarbeitung von benutzerdefinierten Daten. cloud-init kann benutzerdefinierte Daten in [mehreren Formaten](https://cloudinit.readthedocs.io/en/latest/topics/format.html) verarbeiten, z. B. Konfiguration für cloud-init, Skripts usw. Die Verarbeitung von benutzerdefinierten Daten mit cloud-init erfolgt ähnlich wie beim Linux-Agent. Wenn beim Ausführen der Konfigurationsverarbeitung oder von Skripts Fehler auftreten, wird dies nicht als schwerwiegender Bereitstellungsfehler angesehen. Sie müssen einen Benachrichtigungspfad erstellen, um über den Abschlussstatus des Skripts informiert zu werden. Anders als beim Linux-Agent wartet cloud-init jedoch nicht auf den Abschluss der Konfiguration benutzerdefinierter Daten, bevor an die Plattform gemeldet wird, dass die VM bereit ist. Weitere Informationen zu cloud-init unter Azure finden Sie in der [Dokumentation](https://docs.microsoft.com/azure/virtual-machines/linux/using-cloud-init).


Informationen zum Troubleshooting bei der Ausführung benutzerdefinierter Daten finden Sie in der [Dokumentation](https://docs.microsoft.com/azure/virtual-machines/linux/using-cloud-init#troubleshooting-cloud-init) im Thema zum Troubleshooting.


## <a name="faq"></a>Häufig gestellte Fragen
### <a name="can-i-update-custom-data-after-the-vm-has-been-created"></a>Kann ich benutzerdefinierte Daten nach dem Erstellen der VM aktualisieren?
Für einzelne VMs können benutzerdefinierte Daten im VM-Modell nicht aktualisiert werden. Für VMSS können Sie benutzerdefinierte VMSS-Daten jedoch über die REST-API aktualisieren. (Dies gilt nicht für PS- oder AZ CLI-Clients.) Beim Aktualisieren von benutzerdefinierten Daten im VMSS-Modell geschieht Folgendes:
* Vorhandene Instanzen in der VMSS erhalten die aktualisierten benutzerdefinierten Daten erst, wenn Sie das Reimaging durchführen.
* Vorhandene Instanzen in der VMSS, die aktualisiert werden, erhalten nicht die aktualisierten benutzerdefinierten Daten.
* Neue Instanzen erhalten die neuen benutzerdefinierten Daten.

### <a name="can-i-place-sensitive-values-in-custom-data"></a>Kann ich vertrauliche Werte in benutzerdefinierte Daten einschließen?
Es wird empfohlen, vertrauliche Daten **nicht** in benutzerdefinierten Daten zu speichern. Weitere Informationen finden Sie unter [Bewährte Methoden für die Sicherheit und Verschlüsselung in Azure](https://docs.microsoft.com/azure/security/fundamentals/data-encryption-best-practices).


### <a name="is-custom-data-made-available-in-imds"></a>Werden benutzerdefinierte Daten in IMDS zur Verfügung gestellt?
Nein, dieses Feature ist derzeit nicht verfügbar.
