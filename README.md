# creating-azure-storage-account
download for automation
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string"
        },
        "storageAccountName": {
            "type": "string"
        },
        "accountType": {
            "type": "string"
        },
        "kind": {
            "type": "string"
        },
        "accessTier": {
            "type": "string"
        },
        "minimumTlsVersion": {
            "type": "string"
        },
        "supportsHttpsTrafficOnly": {
            "type": "bool"
        },
        "allowBlobPublicAccess": {
            "type": "bool"
        },
        "allowSharedKeyAccess": {
            "type": "bool"
        },
        "allowCrossTenantReplication": {
            "type": "bool"
        },
        "defaultOAuth": {
            "type": "bool"
        },
        "networkAclsBypass": {
            "type": "string"
        },
        "networkAclsDefaultAction": {
            "type": "string"
        },
        "isContainerRestoreEnabled": {
            "type": "bool"
        },
        "isBlobSoftDeleteEnabled": {
            "type": "bool"
        },
        "isContainerSoftDeleteEnabled": {
            "type": "bool"
        },
        "changeFeed": {
            "type": "bool"
        },
        "isVersioningEnabled": {
            "type": "bool"
        },
        "isShareSoftDeleteEnabled": {
            "type": "bool"
        }
    },
    "variables": {},
    "resources": [
        {
            "name": "[parameters('storageAccountName')]",
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2021-06-01",
            "location": "[parameters('location')]",
            "properties": {
                "accessTier": "[parameters('accessTier')]",
                "minimumTlsVersion": "[parameters('minimumTlsVersion')]",
                "supportsHttpsTrafficOnly": "[parameters('supportsHttpsTrafficOnly')]",
                "allowBlobPublicAccess": "[parameters('allowBlobPublicAccess')]",
                "allowSharedKeyAccess": "[parameters('allowSharedKeyAccess')]",
                "allowCrossTenantReplication": "[parameters('allowCrossTenantReplication')]",
                "defaultToOAuthAuthentication": "[parameters('defaultOAuth')]",
                "networkAcls": {
                    "bypass": "[parameters('networkAclsBypass')]",
                    "defaultAction": "[parameters('networkAclsDefaultAction')]",
                    "ipRules": []
                }
            },
            "dependsOn": [],
            "sku": {
                "name": "[parameters('accountType')]"
            },
            "kind": "[parameters('kind')]",
            "tags": {}
        },
        {
            "name": "[concat(parameters('storageAccountName'), '/default')]",
            "type": "Microsoft.Storage/storageAccounts/blobServices",
            "apiVersion": "2021-06-01",
            "properties": {
                "restorePolicy": {
                    "enabled": "[parameters('isContainerRestoreEnabled')]"
                },
                "deleteRetentionPolicy": {
                    "enabled": "[parameters('isBlobSoftDeleteEnabled')]"
                },
                "containerDeleteRetentionPolicy": {
                    "enabled": "[parameters('isContainerSoftDeleteEnabled')]"
                },
                "changeFeed": {
                    "enabled": "[parameters('changeFeed')]"
                },
                "isVersioningEnabled": "[parameters('isVersioningEnabled')]"
            },
            "dependsOn": [
                "[concat('Microsoft.Storage/storageAccounts/', parameters('storageAccountName'))]"
            ]
        },
        {
            "name": "[concat(parameters('storageAccountName'), '/default')]",
            "type": "Microsoft.Storage/storageAccounts/fileservices",
            "apiVersion": "2021-06-01",
            "properties": {
                "shareDeleteRetentionPolicy": {
                    "enabled": "[parameters('isShareSoftDeleteEnabled')]"
                }
            },
            "dependsOn": [
                "[concat('Microsoft.Storage/storageAccounts/', parameters('storageAccountName'))]",
                "[concat(concat('Microsoft.Storage/storageAccounts/', parameters('storageAccountName')), '/blobServices/default')]"
            ]
        }
    ],
    "outputs": {}
}
