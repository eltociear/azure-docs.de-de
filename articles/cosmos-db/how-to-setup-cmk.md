---
title: Konfigurieren von kundenseitig verwalteten Schlüsseln für Ihr Azure Cosmos DB-Konto
description: Informationen zum Konfigurieren von kundenseitig verwalteten Schlüsseln für Ihr Azure Cosmos DB-Konto mit Azure Key Vault
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/19/2020
ms.author: thweiss
ROBOTS: noindex, nofollow
ms.openlocfilehash: 6e2a90b8f81b9b945905ee98beb1686c54a62e8a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80063756"
---
# <a name="configure-customer-managed-keys-for-your-azure-cosmos-account-with-azure-key-vault"></a>Konfigurieren von kundenseitig verwalteten Schlüsseln für Ihr Azure Cosmos-Konto mit Azure Key Vault

> [!NOTE]
> Derzeit müssen Sie den Zugriff anfordern, um diese Funktion verwenden zu können. Wenden Sie sich zu diesem Zweck an [azurecosmosdbcmk@service.microsoft.com](mailto:azurecosmosdbcmk@service.microsoft.com).

Die in Ihrem Azure Cosmos-Konto gespeicherten Daten werden automatisch und nahtlos mit von Microsoft verwalteten Schlüsseln (**vom Dienst verwaltete Schlüssel**) verschlüsselt. Sie können optional eine zweite Verschlüsselungsschicht mit von Ihnen verwalteten Schlüsseln (**vom Kunden verwaltete Schlüssel**) hinzufügen.

![Verschlüsselungsschicht um Kundendaten](./media/how-to-setup-cmk/cmk-intro.png)

Sie müssen vom Kunden verwaltete Schlüssel in [Azure Key Vault](../key-vault/key-vault-overview.md) speichern und einen Schlüssel für jedes Azure Cosmos-Konto bereitstellen, für das vom Kunden verwalteten Schlüssel aktiviert sind. Dieser Schlüssel wird zum Verschlüsseln aller in diesem Konto gespeicherten Daten verwendet.

> [!NOTE]
> Derzeit sind von Kunden verwaltete Schlüssel nur für neue Azure Cosmos-Konten verfügbar. Sie sollten während der Kontoerstellung konfiguriert werden.

## <a name="register-the-azure-cosmos-db-resource-provider-for-your-azure-subscription"></a><a id="register-resource-provider"></a> Registrieren des Azure Cosmos DB-Ressourcenanbieters für Ihr Azure-Abonnement

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an, navigieren Sie zu Ihrem Azure-Abonnement, und wählen Sie auf der Registerkarte **Einstellungen** die Option **Ressourcenanbieter** aus:

   ![Eintrag „Ressourcenanbieter“ im linken Menü](./media/how-to-setup-cmk/portal-rp.png)

1. Suchen Sie nach dem Ressourcenanbieter **Microsoft.DocumentDB**. Überprüfen Sie, ob der Ressourcenanbieter bereits als registriert gekennzeichnet ist. Wenn dies nicht der Fall ist, wählen Sie den Ressourcenanbieter aus, und wählen Sie dann **Registrieren** aus:

   ![Registrieren des Ressourcenanbieters „Microsoft.DocumentDB“](./media/how-to-setup-cmk/portal-rp-register.png)

## <a name="configure-your-azure-key-vault-instance"></a>Konfigurieren Ihrer Azure Key Vault-Instanz

Wenn Sie vom Kunden verwaltete Schlüssel mit Azure Cosmos DB verwenden, müssen Sie zwei Eigenschaften für die Azure Key Vault-Instanz festlegen, die Sie zum Hosten Ihrer Verschlüsselungsschlüssel verwenden möchten. Diese Eigenschaften lauten **Vorläufiges Löschen** und **Nicht bereinigen**. Diese Eigenschaften sind standardmäßig nicht aktiviert. Sie können sie entweder mit PowerShell oder über die Azure-Befehlszeilenschnittstelle aktivieren.

Informationen zum Aktivieren dieser Eigenschaften für eine vorhandene Azure Key Vault-Instanz finden Sie in den Abschnitten „Aktivieren des vorläufigen Löschens“ und „Aktivieren des Bereinigungsschutzes“ in einem der folgenden Artikel:

- [Verwenden des vorläufigen Löschens mit PowerShell](../key-vault/key-vault-soft-delete-powershell.md)
- [Verwenden des vorläufigen Löschens mit Azure CLI](../key-vault/key-vault-soft-delete-cli.md)

## <a name="add-an-access-policy-to-your-azure-key-vault-instance"></a>Hinzufügen einer Zugriffsrichtlinie zu Ihrer Azure Key Vault-Instanz

1. Navigieren Sie im Azure-Portal zu der Azure Key Vault-Instanz, die Sie zum Hosten Ihrer Verschlüsselungsschlüssel verwenden möchten. Wählen Sie im linken Menü die Option **Zugriffsrichtlinien** aus.

   ![„Zugriffsrichtlinien“ im linken Menü](./media/how-to-setup-cmk/portal-akv-ap.png)

1. Wählen Sie **+ Zugriffsrichtlinie hinzufügen** aus.

1. Wählen Sie im Dropdownmenü **Schlüsselberechtigungen** die Berechtigungen **Abrufen**, **Schlüssel entpacken** und **Schlüssel packen** aus:

   ![Auswählen der richtigen Berechtigungen](./media/how-to-setup-cmk/portal-akv-add-ap-perm2.png)

1. Wählen Sie unter **Prinzipal auswählen** die Option **Nichts ausgewählt** aus. Suchen Sie dann nach dem **Azure Cosmos DB**-Prinzipal, und wählen Sie ihn aus (um ihn leichter zu finden, können Sie auch nach der Prinzipal-ID suchen: `a232010e-820c-4083-83bb-3ace5fc29d0b` für alle Azure-Regionen, mit Ausnahme der Azure Government-Regionen, bei denen die Prinzipal-ID `57506a73-e302-42a9-b869-6f12d9ec29e9` lautet). Wählen Sie schließlich im unteren Bereich **Auswählen** aus. Wenn der Prinzipal **Azure Cosmos DB** nicht in der Liste aufgeführt ist, müssen Sie möglicherweise den Ressourcenanbieter **Microsoft.DocumentDB** erneut registrieren (wie im Abschnitt [Registrieren des Ressourcenanbieters](#register-resource-provider) in diesem Artikel beschrieben).

   ![Auswählen des Azure Cosmos DB-Prinzipals](./media/how-to-setup-cmk/portal-akv-add-ap.png)

1. Wählen Sie **Hinzufügen** aus, um die neue Zugriffsrichtlinie hinzuzufügen.

## <a name="generate-a-key-in-azure-key-vault"></a>Generieren eines Schlüssels in Azure Key Vault

1. Navigieren Sie im Azure-Portal zu der Azure Key Vault-Instanz, die Sie zum Hosten Ihrer Verschlüsselungsschlüssel verwenden möchten. Wählen Sie dann im linken Menü die Option **Schlüssel** aus:

   ![Eintrag „Schlüssel“ im linken Menü](./media/how-to-setup-cmk/portal-akv-keys.png)

1. Wählen Sie **Generieren/Importieren** aus, geben Sie einen Namen für den neuen Schlüssel an, und wählen Sie eine RSA-Schlüsselgröße aus. Es wird eine Größe von mindestens 3072 empfohlen, da diese Größe die höchste Sicherheit bietet. Wählen Sie anschließend **Erstellen** aus:

   ![Erstellen eines neuen Schlüssels](./media/how-to-setup-cmk/portal-akv-gen.png)

1. Wählen Sie nach der Schlüsselerstellung den neu erstellten Schlüssel und dann seine aktuelle Version aus.

1. Kopieren Sie den **Schlüsselbezeichner** des Schlüssels mit Ausnahme des Teils nach dem letzten Schrägstrich:

   ![Kopieren des Schlüsselbezeichners des Schlüssels](./media/how-to-setup-cmk/portal-akv-keyid.png)

## <a name="create-a-new-azure-cosmos-account"></a>Erstellen eines neuen Azure Cosmos-Kontos

### <a name="using-the-azure-portal"></a>Verwenden des Azure-Portals

Wählen Sie beim Erstellen eines neuen Azure Cosmos DB-Kontos im Azure-Portal im Schritt **Verschlüsselung** die Option **Vom Kunden verwalteter Schlüssel** aus. Fügen Sie in das Feld **Schlüssel-URI** den URI/Schlüsselbezeichner des Azure Key Vault-Schlüssels ein, den Sie im vorherigen Schritt kopiert haben:

![Festlegen der CMK-Parameter im Azure-Portal](./media/how-to-setup-cmk/portal-cosmos-enc.png)

### <a name="using-azure-powershell"></a>Verwenden von Azure PowerShell

Berücksichtigen Sie Folgendes, wenn Sie ein neues Azure Cosmos DB-Konto mit PowerShell erstellen:

- Übergeben Sie den zuvor kopierten URI des Azure Key Vault-Schlüssels unter der Eigenschaft **keyVaultKeyUri** im **PropertyObject**.

- Verwenden Sie die API-Version **2019-12-12**.

> [!IMPORTANT]
> Sie müssen den `Location`-Parameter explizit festlegen, damit das Konto erfolgreich mit vom Kunden verwalteten Schlüsseln erstellt werden kann.

```powershell
$resourceGroupName = "myResourceGroup"
$accountLocation = "West US 2"
$accountName = "mycosmosaccount"

$failoverLocations = @(
    @{ "locationName"="West US 2"; "failoverPriority"=0 }
)

$CosmosDBProperties = @{
    "databaseAccountOfferType"="Standard";
    "locations"=$failoverLocations;
    "keyVaultKeyUri" = "https://<my-vault>.vault.azure.net/keys/<my-key>";
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2019-12-12" -ResourceGroupName $resourceGroupName `
    -Location $accountLocation -Name $accountName -PropertyObject $CosmosDBProperties
```

### <a name="using-an-azure-resource-manager-template"></a>Verwenden einer Azure Resource Manager-Vorlage

Berücksichtigen Sie Folgendes, wenn Sie ein neues Azure Cosmos-Konto anhand einer Azure Resource Manager-Vorlage erstellen:

- Übergeben Sie den zuvor kopierten URI des Azure Key Vault-Schlüssels unter der Eigenschaft **keyVaultKeyUri** im Objekt **properties**.

- Verwenden Sie die API-Version **2019-12-12**.

> [!IMPORTANT]
> Sie müssen den `Location`-Parameter explizit festlegen, damit das Konto erfolgreich mit vom Kunden verwalteten Schlüsseln erstellt werden kann.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "accountName": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "keyVaultKeyUri": {
            "type": "string"
        }
    },
    "resources": 
    [
        {
            "type": "Microsoft.DocumentDB/databaseAccounts",
            "name": "[parameters('accountName')]",
            "apiVersion": "2019-12-12",
            "kind": "GlobalDocumentDB",
            "location": "[parameters('location')]",
            "properties": {
                "locations": [ 
                    {
                        "locationName": "[parameters('location')]",
                        "failoverPriority": 0,
                        "isZoneRedundant": false
                    }
                ],
                "databaseAccountOfferType": "Standard",
                "keyVaultKeyUri": "[parameters('keyVaultKeyUri')]"
            }
        }
    ]
}

```

Stellen Sie die Vorlage mithilfe des folgenden PowerShell-Skripts bereit:

```powershell
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$accountLocation = "West US 2"
$keyVaultKeyUri = "https://<my-vault>.vault.azure.net/keys/<my-key>"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile "deploy.json" `
    -accountName $accountName `
    -location $accountLocation `
    -keyVaultKeyUri $keyVaultKeyUri
```

### <a name="using-the-azure-cli"></a>Verwenden der Azure-Befehlszeilenschnittstelle

Wenn Sie ein neues Azure Cosmos-Konto über die Azure-Befehlszeilenschnittstelle erstellen, übergeben Sie den URI des Azure Key Vault-Schlüssels, den Sie zuvor unter dem Parameter **--key-uri** kopiert haben.

```azurecli-interactive
resourceGroupName='myResourceGroup'
accountName='mycosmosaccount'
keyVaultKeyUri = 'https://<my-vault>.vault.azure.net/keys/<my-key>'

az cosmosdb create \
    -n $accountName \
    -g $resourceGroupName \
    --locations regionName='West US 2' failoverPriority=0 isZoneRedundant=False \
    --key-uri $keyVaultKeyUri
```

## <a name="frequently-asked-questions"></a>Häufig gestellte Fragen

### <a name="is-there-any-additional-charge-for-using-customer-managed-keys"></a>Fallen bei Verwendung von kundenseitig verwalteten Schlüsseln zusätzliche Gebühren an?

Ja. Um der zusätzlichen Computeauslastung Rechnung zu tragen, die zum Verwalten der Datenverschlüsselung und -entschlüsselung mit vom Kunden verwalteten Schlüsseln erforderlich ist, steigt der Verbrauch von [Anforderungseinheiten](./request-units.md) von allen für das Azure Cosmos-Konto ausgeführten Vorgängen um 25 %.

### <a name="what-data-gets-encrypted-with-the-customer-managed-keys"></a>Welche Daten werden mit den vom Kunden verwalteten Schlüsseln verschlüsselt?

Mit Ausnahme der nachfolgend aufgeführten Metadaten werden alle in Ihrem Azure Cosmos-Konto gespeicherten Daten mit den vom Kunden verwalteten Schlüsseln verschlüsselt:

- Die Namen Ihrer Azure Cosmos DB-[Konten, -Datenbanken und -Container](./account-overview.md#elements-in-an-azure-cosmos-account)

- Die Namen Ihrer [gespeicherten Prozeduren](./stored-procedures-triggers-udfs.md)

- Die in Ihren [Indizierungsrichtlinien](./index-policy.md) deklarierten Eigenschaftenpfade

- Die Werte der [Partitionsschlüssel](./partitioning-overview.md) Ihrer Container

### <a name="are-customer-managed-keys-supported-for-existing-azure-cosmos-accounts"></a>Werden vom Kunden verwaltete Schlüssel für vorhandene Azure Cosmos-Konten unterstützt?

Dieses Feature ist derzeit nur für neue Konten verfügbar.

### <a name="is-there-a-plan-to-support-finer-granularity-than-account-level-keys"></a>Ist die Unterstützung einer feineren Granularität als bei Schlüsseln auf Kontoebene geplant?

Derzeit nicht. Es werden jedoch Schlüssel auf Containerebene in Erwägung gezogen.

### <a name="how-do-customer-managed-keys-affect-a-backup"></a>Wie wirken sich vom Kunden verwaltete Schlüssel auf eine Sicherung aus?

Azure Cosmos DB erstellt [regelmäßige und automatische Sicherungen](../synapse-analytics/sql-data-warehouse/backup-and-restore.md) der in Ihrem Konto gespeicherten Daten. Bei diesem Vorgang werden die verschlüsselten Daten gesichert. Um die wiederhergestellte Sicherung verwenden zu können, ist der zum Zeitpunkt der Sicherung verwendete Verschlüsselungsschlüssel erforderlich. Das bedeutet, dass keine Sperrung erfolgt ist und die zum Zeitpunkt der Sicherung verwendete Version des Schlüssels weiterhin aktiviert ist.

### <a name="how-do-i-revoke-an-encryption-key"></a>Wie sperre/widerrufe ich einen Verschlüsselungsschlüssel?

Die Schlüsselsperrung erfolgt durch Deaktivieren der aktuellen Version des Schlüssels:

![Deaktivieren der Version eines Schlüssels](./media/how-to-setup-cmk/portal-akv-rev2.png)

Alternativ können Sie zum Sperren aller Schlüssel in einer Azure Key Vault-Instanz die dem Azure Cosmos DB-Prinzipal erteilte Zugriffsrichtlinie löschen:

![Löschen der Zugriffsrichtlinie für den Azure Cosmos DB-Prinzipal](./media/how-to-setup-cmk/portal-akv-rev.png)

### <a name="what-operations-are-available-after-a-customer-managed-key-is-revoked"></a>Welche Vorgänge sind nach dem Sperren/Widerrufen eines vom Kunden verwalteten Schlüssels verfügbar?

Das Löschen von Konten ist der einzige mögliche Vorgang, wenn der Verschlüsselungsschlüssel widerrufen wurde.

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über die [Datenverschlüsselung in Azure Cosmos DB](./database-encryption-at-rest.md).
- Überblick über [sicheren Zugriff auf Daten in Cosmos DB](secure-access-to-data.md).
