---
title: 'Tutorial: Erstellen eines Azure-VM-Clusters mit Terraform mithilfe der Modulregistrierung'
description: In diesem Tutorial erstellen Sie mit Terraform-Modulen einen Cluster mit virtuellen Windows-Computern in Azure.
keywords: Azure DevOps Terraform VM virtueller Computer Cluster Modul Registrierung
ms.topic: tutorial
ms.date: 03/09/2020
ms.openlocfilehash: 9faeee9bb2f0fb6dc148a3868f6fc0dae3833a2a
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/24/2020
ms.locfileid: "78945289"
---
# <a name="tutorial-create-an-azure-vm-cluster-with-terraform-using-the-module-registry"></a>Tutorial: Erstellen eines Azure-VM-Clusters mit Terraform mithilfe der Modulregistrierung

Dieser Artikel führt Sie durch die Erstellung eines kleinen VM-Clusters mit dem [Azure Compute-Modul](https://registry.terraform.io/modules/Azure/compute/azurerm/1.0.2) für Terraform. In diesem Tutorial lernen Sie Folgendes: 

> [!div class="checklist"]
> * Einrichten der Authentifizierung mit Azure
> * Erstellen der Terraform-Vorlage
> * Visualisieren der Änderungen mit plan
> * Anwenden der Konfiguration zum Erstellen des VM-Clusters

Weitere Informationen zu Terraform finden Sie in der [Terraform-Dokumentation](https://www.terraform.io/docs/index.html).

## <a name="set-up-authentication-with-azure"></a>Einrichten der Authentifizierung mit Azure

> [!TIP]
> Wenn Sie [Terraform-Umgebungsvariablen verwenden](terraform-install-configure.md) oder dieses Tutorial in der [Azure Cloud Shell](/azure/cloud-shell/overview) ausführen, überspringen Sie diesen Schritt.

 Unter [Installieren von Terraform und Konfigurieren des Zugriffs auf Azure](terraform-install-configure.md) finden Sie Informationen zum Erstellen eines Azure-Dienstprinzipals. Verwenden Sie diesen Dienstprinzipal, um folgenden Code in die neue Datei `azureProviderAndCreds.tf` in einem leeren Verzeichnis einzufügen:

```hcl
variable subscription_id {}
variable tenant_id {}
variable client_id {}
variable client_secret {}

provider "azurerm" {
    version = "~>1.40"

    subscription_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    tenant_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    client_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    client_secret = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
}
```

## <a name="create-the-template"></a>Erstellen der Vorlage

Erstellen Sie mit folgendem Code eine neue Terraform-Vorlage mit dem Namen `main.tf`:

```hcl
module mycompute {
    source = "Azure/compute/azurerm"
    resource_group_name = "myResourceGroup"
    location = "East US 2"
    admin_password = "ComplxP@assw0rd!"
    vm_os_simple = "WindowsServer"
    is_windows_image = "true"
    remote_port = "3389"
    nb_instances = 2
    public_ip_dns = ["unique_dns_name"]
    vnet_subnet_id = module.network.vnet_subnets[0]
}

module "network" {
    source = "Azure/network/azurerm"
    location = "East US 2"
    resource_group_name = "myResourceGroup"
}

output "vm_public_name" {
    value = module.mycompute.public_ip_dns_name
}

output "vm_public_ip" {
    value = module.mycompute.public_ip_address
}

output "vm_private_ips" {
    value = module.mycompute.network_interface_private_ip
}
```

Führen Sie `terraform init` in Ihrem Konfigurationsverzeichnis aus. Bei Terraform-Versionen ab 0.10.6 wird die folgende Ausgabe angezeigt:

![Terraform-Initialisierung](media/terraformInitWithModules.png)

## <a name="visualize-the-changes-with-plan"></a>Visualisieren der Änderungen mit plan

Führen Sie `terraform plan` aus, um eine Vorschau der Infrastruktur des virtuellen Computers anzuzeigen, die durch die Vorlage erstellt wird.

![terraform plan](media/terraform-create-vm-cluster-with-infrastructure/terraform-plan.png)


## <a name="create-the-virtual-machines-with-apply"></a>Erstellen der virtuellen Computer mit apply

Führen Sie `terraform apply` aus, um die VMs in Azure bereitzustellen.

![terraform apply](media/terraform-create-vm-cluster-with-infrastructure/terraform-apply.png)

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"] 
> Sehen Sie sich die Liste der [Azure Terraform-Module](https://registry.terraform.io/modules/Azure) an.