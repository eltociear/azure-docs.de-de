---
title: Delegieren eines Subnetzes an Azure NetApp Files | Microsoft-Dokumentation
description: Dieser Artikel beschreibt das Delegieren eines Subnetzes an Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/19/2020
ms.author: b-juche
ms.openlocfilehash: b83f530549ffa43789963fd0c95b4982f5289356
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80054466"
---
# <a name="delegate-a-subnet-to-azure-netapp-files"></a>Delegieren eines Subnetzes an Azure NetApp Files 

Sie müssen ein Subnetze an Azure NetApp Files delegieren.   Wenn Sie ein Volume erstellen, müssen Sie das delegierte Subnetz angeben.

## <a name="considerations"></a>Überlegungen
* Der Assistent zum Erstellen eines neuen Subnetzes verwendet standardmäßig eine /24-Netzwerkmaske, die 251 IP-Adressen bereitstellt. Die Verwendung einer /28-Netzwerkmaske, die 16 nutzbare IP-Adressen bereitstellt, ist für den Dienst ausreichend.
* In jedem Azure Virtual Network (VNET) kann nur ein Subnetz an Azure NetApp Files delegiert werden.   
   Azure ermöglicht es Ihnen, mehrere delegierte Subnetze in einem VNet zu erstellen.  Alle Versuche, ein neues Volume zu erstellen, schlagen jedoch fehl, wenn Sie mehr als ein delegiertes Subnetz verwenden.
* Sie können im delegierten Subnetz keine Netzwerksicherheitsgruppe und keinen Dienstendpunkt festlegen. Dadurch würde bei der Subnetzdelegierung ein Fehler auftreten.
* Der Zugriff auf einem Volume aus einem globalen virtuellen Peeringnetzwerk wird zurzeit nicht unterstützt.
* Das Erstellen von [benutzerdefinierten angepassten Routen](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#custom-routes) in VM-Subnetzen mit Adresspräfix (Ziel) für ein Subnetz, das an Azure NetApp Files delegiert wurde, wird nicht unterstützt. Dies wirkt sich auf die VM-Konnektivität aus.

## <a name="steps"></a>Schritte 
1.  Wechseln Sie im Azure-Portal zum Blatt **Virtuelles Netzwerk**, und wählen Sie das virtuelle Netzwerk aus, das Sie für Azure NetApp Files verwenden möchten.    

1. Wählen Sie auf dem Blatt „Virtuelles Netzwerk“ die Option **Subnetze** aus, und klicken Sie auf die Schaltfläche **+Subnetz**. 

1. Erstellen Sie ein neues Subnetz, das für Azure NetApp Files verwendet werden soll, indem Sie die folgenden erforderlichen Felder auf der Seite „Subnetz hinzufügen“ ausfüllen:
    * **Name**: Geben Sie den Namen des Subnetzes an.
    * **Adressbereich**: Geben Sie den IP-Adressbereich an.
    * **Subnetzdelegierung**: Wählen Sie **Microsoft.NetApp/volumes** aus. 

      ![Subnetzdelegierung](../media/azure-netapp-files/azure-netapp-files-subnet-delegation.png)
    
Sie können ein Subnetz auch beim [Erstellen eines Volumes für Azure NetApp Files](azure-netapp-files-create-volumes.md) erstellen und delegieren. 

## <a name="next-steps"></a>Nächste Schritte  
* [Erstellen eines Volumes für Azure NetApp Files](azure-netapp-files-create-volumes.md)
* [Erfahren Sie mehr über die Integration virtueller Netzwerke für Azure-Dienste](https://docs.microsoft.com/azure/virtual-network/virtual-network-for-azure-services)


