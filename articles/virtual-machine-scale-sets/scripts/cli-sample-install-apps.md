---
title: 'Azure CLI-Beispiele: Installieren von Apps'
description: Dieses Skript erstellt eine VM-Skalierungsgruppe unter Ubuntu und installiert eine einfache Webanwendung unter Verwendung der benutzerdefinierten Skripterweiterung.
author: cynthn
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.devlang: azurecli
ms.topic: sample
ms.date: 03/27/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 12925c06c8b0fbe0e223623584f7496bf34d3618
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/24/2020
ms.locfileid: "76278752"
---
# <a name="install-applications-into-a-virtual-machine-scale-set-with-the-azure-cli"></a>Installieren von Anwendungen in einer VM-Skalierungsgruppe mit der Azure CLI
Dieses Skript erstellt eine VM-Skalierungsgruppe unter Ubuntu und installiert eine einfache Webanwendung unter Verwendung der benutzerdefinierten Skripterweiterung. Nach dem Ausführen des Skripts können Sie über einen Webbrowser auf die Web-App zugreifen.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Beispielskript
[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine-scale-sets/install-apps/install-apps.sh "Install apps into a scale set")]

## <a name="clean-up-deployment"></a>Bereinigen der Bereitstellung
Führen Sie den folgenden Befehl aus, um die Ressourcengruppe, die Skalierungsgruppe und alle dazugehörigen Ressourcen zu entfernen.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Erläuterung des Skripts
In diesem Skript werden die folgenden Befehle verwendet, um eine Ressourcengruppe, eine VM-Skalierungsgruppe und alle dazugehörigen Ressourcen zu erstellen. Jeder Befehl in der Tabelle ist mit der zugehörigen Dokumentation verknüpft.

| Get-Help | Notizen |
|---|---|
| [az group create](/cli/azure/ad/group) | Erstellt eine Ressourcengruppe, in der alle Ressourcen gespeichert sind. |
| [az vmss create](/cli/azure/vmss) | Erstellt die VM-Skalierungsgruppe und verbindet sie mit dem virtuellen Netzwerk, dem Subnetz und der Netzwerksicherheitsgruppe. Des Weiteren wird ein Lastenausgleich erstellt, um Datenverkehr auf mehrere VM-Instanzen zu verteilen. Dieser Befehl gibt auch das zu verwendende VM-Image und die Administratoranmeldeinformationen an.  |
| [az vmss extension set](/cli/azure/vmss/extension) | Installiert die benutzerdefinierte Skripterweiterung von Azure, um ein Skript auszuführen, das die Datenträger auf den einzelnen VM-Instanzen vorbereitet. |
| [az network lb rule create](/cli/azure/network/lb/rule) | Erstellt eine Lastenausgleichsregel, um den Datenverkehr am TCP-Port 80 auf die VM-Instanzen in der Skalierungsgruppe zu verteilen. |
| [az network public-ip show](/cli/azure/network/public-ip) | Ruft Informationen zur zugewiesenen öffentlichen IP-Adresse ab, die vom Load Balancer verwendet wird. |
| [az group delete](/cli/azure/ad/group) | Löscht eine Ressourcengruppe einschließlich aller geschachtelten Ressourcen. |

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zur Azure CLI finden Sie in der [Azure CLI-Dokumentation](https://docs.microsoft.com/cli/azure/overview).

Weitere Azure CLI-Skriptbeispiele für VM-Skalierungsgruppen finden Sie in der [Dokumentation zu Azure-VM-Skalierungsgruppen](../cli-samples.md).
