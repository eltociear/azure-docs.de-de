---
title: Bereitstellen einer VM von Ihren VHDs für den Azure Marketplace
description: Dieser Artikel erläutert, wie Sie eine VM von einer in Azure bereitgestellten virtuellen Festplatte registrieren.
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: dsindona
ms.openlocfilehash: 5263d24c411ef8de4187c2fd750013374d779f04
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80277938"
---
# <a name="deploy-a-vm-from-your-vhds"></a>Bereitstellen eines virtuellen Computers auf Basis der VHDs

Dieser Abschnitt erläutert, wie Sie einen virtuellen Computer (VM) von einer in Azure bereitgestellten virtuellen Festplatte (VHD) bereitstellen.  Hier sind die erforderlichen Tools aufgeführt. Es wird beschrieben, wie Sie diese verwenden, um ein Benutzer-VM-Image zu erstellen und dieses dann mithilfe von PowerShell-Skripts in Azure bereitzustellen.

Nachdem Sie Ihre virtuellen Festplatten – die generalisierte Betriebssystem-VHD und ggf. Datenträger-VHDs – in Ihr Azure-Speicherkonto hochgeladen haben, können Sie die Festplatten als Benutzer-VM-Image registrieren. Anschließend können Sie das Image testen. Da Ihre Betriebssystem-VHD generalisiert wurde, können Sie den virtuellen Computer nicht direkt bereitstellen, indem Sie die VHD-URL angeben.

Weitere Informationen zu VM-Images finden Sie in den folgenden Blogbeiträgen:

- [VM-Image](https://azure.microsoft.com/blog/vm-image-blog-post/)
- [VM Image PowerShell How To](https://azure.microsoft.com/blog/vm-image-powershell-how-to-blog-post/) (VM-Images in PowerShell – Vorgehensweisen)

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

## <a name="prerequisite-install-the-necessary-tools"></a>Voraussetzung: Installieren der erforderlichen Tools

Falls Sie dies noch nicht getan haben, installieren Sie Azure PowerShell und die Azure CLI gemäß den folgenden Anweisungen:

- [Installieren von Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps)
- [Installieren der Azure-Befehlszeilenschnittstelle](https://docs.microsoft.com/cli/azure/install-azure-cli)


## <a name="deployment-steps"></a>Bereitstellungsschritte

Sie führen die folgenden Schritte aus, um ein Benutzer-VM-Image zu erstellen und bereitzustellen:

1. Erstellen des Benutzer-VM-Images. Dazu gehört das Erfassen und Generalisieren des Images. 
2. Erstellen von Zertifikaten und Speichern in einem neuen Azure Key Vault. Ein Zertifikat ist erforderlich, um eine sichere WinRM-Verbindung mit dem virtuellen Computer herzustellen.  Es werden eine Azure Resource Manager-Vorlage und ein Azure PowerShell-Skript bereitgestellt. 
3. Bereitstellen der virtuellen Maschine aus einem Benutzer-VM-Image mithilfe der angegebenen Vorlage und des Skripts.

Nachdem Ihr virtueller Computer bereitgestellt wurde, können Sie [Ihr VM-Image zertifizieren](./cpp-certify-vm.md).

1. Klicken Sie auf **Neu**, suchen Sie nach **Vorlagenbereitstellung**, und wählen Sie dann **Eigene Vorlage im Editor erstellen** aus.  <br/>
   ![Erstellen einer Vorlage für die VHD-Bereitstellung im Azure-Portal](./media/publishvm_021.png)

1. Kopieren Sie diese [JSON-Vorlage](./cpp-deploy-json-template.md), fügen Sie sie in den Editor ein, und klicken Sie auf **Speichern**. <br/>
   ![Speichern der Vorlage für die VHD-Bereitstellung im Azure-Portal](./media/publishvm_022.png)

1. Geben Sie die Parameterwerte für die angezeigten Eigenschaftenseiten für die **benutzerdefinierte Bereitstellung** ein.

   <table> <tr> <td valign="top"> <img src="./media/publishvm_023.png" alt="Custom deployment property page 1"> </td> <td valign="top"> <img src="./media/publishvm_024.png" alt="Custom deployment property page 2"> </td> </tr> </table> <br/> 

   |  **Parameter**              |   **Beschreibung**                                                            |
   |  -------------              |   ---------------                                                            |
   | Name des Speicherkontos des Benutzers   | Name des Speicherkontos, in dem sich die generalisierte VHD befindet                    |
   | Name des Speichercontainers des Benutzers | Name des Containers, in dem sich die generalisierte VHD befindet                          |
   | DNS-Name für öffentliche IP-Adresse      | DNS-Name der öffentlichen IP-Adresse. Der DNS-Name ist der des virtuellen Computers. Sie legen ihn nach der Bereitstellung des Angebots im Azure-Portal fest.  |
   | Administratorbenutzername             | Der Benutzername des Administratorkontos für die neue VM                                  |
   | Administratorkennwort              | Das Kennwort des Administratorkontos für die neue VM                                  |
   | Betriebssystemtyp                     | VM-Betriebssystem: `Windows` \| `Linux`                                    |
   | Abonnement-ID             | Bezeichner des ausgewählten Abonnements                                      |
   | Position                    | Geografischer Standort der Bereitstellung                                        |
   | Größe des virtuellen Computers                     | [Größe des virtuellen Azure-Computers](https://docs.microsoft.com/azure/virtual-machines/windows/sizes), z.B. `Standard_A2` |
   | Öffentliche IP-Adresse      | Name der öffentlichen IP-Adresse                                               |
   | VM-Name                     | Name des neuen virtuellen Computers                                                           |
   | Name des virtuellen Netzwerks        | Name des virtuellen Netzwerks, das von der VM verwendet wird                                   |
   | NIC-Name                    | Name der Netzwerkschnittstellenkarte, über die das virtuelle Netzwerk ausgeführt wird               |
   | VHD-URL                     | Vollständige URL der VHD mit dem Betriebssystemdatenträger                                                     |
   |  |  |
            
1. Nachdem Sie diese Werte angegeben haben, klicken Sie auf **Kauf**. 

Azure startet die Bereitstellung: Im angegebenen Speicherkontopfad wird eine neue VM mit der angegebenen nicht verwalteten VHD erstellt.  Sie können den Fortschritt im Azure-Portal nachverfolgen, indem Sie im Portal auf der linken Seite auf **Virtuelle Computer** klicken.  Nachdem der virtuelle Computer erstellt wurde, ändert sich der Status von `Starting` zu `Running`. 


### <a name="deploy-a-vm-from-powershell"></a>Bereitstellen eines virtuellen Computers mit PowerShell

Zum Bereitstellen eines großen virtuellen Computers aus dem soeben erstellten generalisierten VM-Image verwenden Sie die folgenden Cmdlets.

``` powershell
    $img = Get-AzureVMImage -ImageName "myVMImage"
    $user = "user123"
    $pass = "adminPassword123"
    $myVM = New-AzureVMConfig -Name "VMImageVM" -InstanceSize "Large" -ImageName $img.ImageName | Add-AzureProvisioningConfig -Windows -AdminUsername $user -Password $pass
    New-AzureVM -ServiceName "VMImageCloudService" -VMs $myVM -Location "West US" -WaitForBoot
```


## <a name="next-steps"></a>Nächste Schritte

Als nächstes [erstellen Sie ein Benutzer-VM-Image](cpp-create-user-image.md) für Ihre Lösung.

