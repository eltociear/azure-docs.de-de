---
title: 'PowerShell: Erstellen einer benutzerdefinierte Rolle in einem Lab in Azure DevTest Labs'
description: Dieser Artikel stellt ein PowerShell-Beispielskript vor, das einem Lab in Azure DevTest Labs einen externen Benutzer hinzufügt.
services: lab-services
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2020
ms.author: spelluru
ms.openlocfilehash: 9605b3c63877335b8f180ae5fd2b06edc1601096
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76760451"
---
# <a name="use-powershell-to-create-a-custom-role-in-a-lab-in-azure-devtest-labs"></a>Erstellen einer benutzerdefinierte Rolle in einem Lab in Azure DevTest Labs mithilfe von PowerShell

Dieses PowerShell-Beispielskript erstellt eine benutzerdefinierte Rolle für ein Lab in Azure DevTest Labs. 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

## <a name="prerequisites"></a>Voraussetzungen
* **Ein Lab.** Das Skript erfordert ein vorhandenes Lab. 

## <a name="sample-script"></a>Beispielskript

[!code-powershell[main](../../../powershell_scripts/devtest-lab/create-custom-role-in-lab/create-custom-role-in-lab.ps1 "Add external user to a lab")]

## <a name="script-explanation"></a>Erläuterung des Skripts

Das Skript verwendet die folgenden Befehle: 

| Get-Help | Notizen |
|---|---|
| [Get-AzProviderOperation](/powershell/module/az.resources/get-azprovideroperation) | Ruft die Vorgänge für einen Azure-Ressourcenanbieter ab, die mit Azure RBAC abgesichert werden können. |
| [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) | Dient zum Auflisten aller Azure RBAC-Rollen, die für die Zuweisung verfügbar sind. |
| [New-AzRoleDefinition](/powershell/module/az.resources/new-azroledefinition) | Dient zum Erstellen einer benutzerdefinierten Rolle. |

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Azure PowerShell finden Sie in der [Azure PowerShell-Dokumentation](https://docs.microsoft.com/powershell/).

Weitere PowerShell-Skriptbeispiele für Azure Lab Services finden Sie unter [Azure Lab Services – Beispiele für PowerShell](../samples-powershell.md).
