---
title: Gérer Key Vault dans Azure Stack avec PowerShell | Microsoft Docs
description: Découvrir comment gérer Key Vault dans Azure Stack avec PowerShell
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 22B62A3B-B5A9-4B8C-81C9-DA461838FAE5
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/05/2019
ms.author: sethm
ms.openlocfilehash: db68d3ac626d80361e456a251b93d847a73afb8c
ms.sourcegitcommit: 63b996e9dc7cade181e83e13046a5006b275638d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/10/2019
ms.locfileid: "54192625"
---
# <a name="manage-key-vault-in-azure-stack-using-powershell"></a>Gérer Key Vault dans Azure Stack à l’aide de PowerShell

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

Vous pouvez gérer Key Vault dans Azure Stack en utilisant PowerShell. Cet article montre comment utiliser les cmdlets PowerShell Key Vault pour :

* Création d’un coffre de clés
* Stocker et gérer des clés de chiffrement et des secrets.
* Autoriser des utilisateurs ou des applications à appeler des opérations dans le coffre.

>[!NOTE]
>Les applets de commande PowerShell Key Vault décrites dans cet article sont fournies dans le SDK Azure PowerShell.

## <a name="prerequisites"></a>Prérequis

* Vous devez vous abonner à une offre qui inclut le service Azure Key Vault.
* [Installez PowerShell pour Azure Stack](azure-stack-powershell-install.md).
* [Configurez l’environnement PowerShell de l’utilisateur Azure Stack](azure-stack-powershell-configure-user.md).

## <a name="enable-your-tenant-subscription-for-key-vault-operations"></a>Activer votre abonnement de locataire pour les opérations Key Vault

Avant de pouvoir exécuter des opérations sur un coffre de clés, vous devez vérifier que votre abonnement de locataire est activé pour les opérations de coffre. Pour vérifier que les opérations de coffre sont activées, exécutez la commande suivante :

```PowerShell
Get-AzureRmResourceProvider -ProviderNamespace Microsoft.KeyVault | ft -Autosize
```

**Sortie**

Si votre abonnement est activé pour les opérations de coffre, la sortie indique que **RegistrationState** est **Registered** pour tous les types de ressources d’un coffre de clés.

![État d’inscription du coffre de clés](media/azure-stack-key-vault-manage-powershell/image1.png)

Si les opérations de coffre ne sont pas activées, exécutez la commande suivante pour inscrire le service Key Vault dans votre abonnement :

```PowerShell
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.KeyVault
```

**Sortie**

Si l’inscription réussit, la sortie suivante est retournée :

![Register ](media/azure-stack-key-vault-manage-powershell/image2.png)

Quand vous appelez les commandes Key Vault, vous pouvez obtenir une erreur comme « L’abonnement n’est pas inscrit pour utiliser l’espace de noms Microsoft.KeyVault ». Si vous recevez une erreur, assurez-vous d’avoir [activé le fournisseur de ressources Key Vault](#enable-your-tenant-subscription-for-vault-operations) en suivant les instructions fournies précédemment.

## <a name="create-a-key-vault"></a>Création d’un coffre de clés

Avant de créer un coffre de clés, créez un groupe de ressources pour que toutes les ressources associées au coffre de clés fassent partie d’un groupe de ressources. Utilisez la commande suivante pour créer un groupe de ressources :

```PowerShell
New-AzureRmResourceGroup -Name "VaultRG" -Location local -verbose -Force
```

**Sortie**

![Nouveau groupe de ressources](media/azure-stack-key-vault-manage-powershell/image3.png)

Maintenant, utilisez la cmdlet **New-AzureRMKeyVault** pour créer un coffre de clés dans le groupe de ressources que vous avez créé précédemment. Cette commande lit trois paramètres obligatoires : le nom du groupe de ressources, le nom du coffre de clés et l’emplacement géographique.

Exécutez la commande suivante pour créer un coffre de clés :

```PowerShell
New-AzureRmKeyVault -VaultName "Vault01" -ResourceGroupName "VaultRG" -Location local -verbose
```

**Sortie**

![Nouveau coffre de clés](media/azure-stack-key-vault-manage-powershell/image4.png)

La sortie de cette commande affiche les propriétés du coffre de clés que vous avez créé. Lorsqu’une application accède à ce coffre, elle doit utiliser la propriété `Vault URI`, qui est `https://vault01.vault.local.azurestack.external` dans cet exemple.

### <a name="active-directory-federation-services-ad-fs-deployment"></a>Déploiement Active Directory Federation Services (AD FS)

Dans un déploiement AD FS, vous pouvez obtenir cet avertissement : « La stratégie d’accès n’est pas définie. Aucun utilisateur ou application n’est autorisé à utiliser ce coffre ». Pour résoudre ce problème, définissez une stratégie d’accès pour le coffre à l’aide de la cmdlet [Set-AzureRmKeyVaultAccessPolicy](azure-stack-key-vault-manage-powershell.md#authorize-an-application-to-use-a-key-or-secret) :

```PowerShell
# Obtain the security identifier(SID) of the active directory user
$adUser = Get-ADUser -Filter "Name -eq '{Active directory user name}'"
$objectSID = $adUser.SID.Value

# Set the key vault access policy
Set-AzureRmKeyVaultAccessPolicy -VaultName "{key vault name}" -ResourceGroupName "{resource group name}" -ObjectId "{object SID}" -PermissionsToKeys {permissionsToKeys} -PermissionsToSecrets {permissionsToSecrets} -BypassObjectIdValidation
```

## <a name="manage-keys-and-secrets"></a>Gérer les clés et les secrets

Après avoir créé un coffre de coffre de clés, effectuez les étapes suivantes pour créer et gérer les clés et les secrets dans le coffre.

### <a name="create-a-key"></a>Créer une clé

Utilisez la cmdlet **Add-AzureKeyVaultKey** pour créer ou importer une clé protégée par logiciel dans un coffre de clés.

```PowerShell
Add-AzureKeyVaultKey -VaultName "Vault01" -Name "Key01" -verbose -Destination Software
```

Le paramètre `Destination` permet de spécifier que la clé est protégée par un logiciel. Une fois l’opération terminée, la commande affiche les détails de la clé nouvellement créée.

**Sortie**

![Nouvelle clé](media/azure-stack-key-vault-manage-powershell/image5.png)

Vous pouvez maintenant référencer la clé nouvellement créée à l’aide de son URI. Si vous créez ou importez une clé qui a le même nom qu’une clé existante, la clé d’origine est mise à jour avec les valeurs spécifiées dans la nouvelle clé. Vous pouvez accéder à la version précédente à l’aide de l’URI propre à la version de la clé. Par exemple : 

* Utilisez `https://vault10.vault.local.azurestack.external:443/keys/key01` pour obtenir toujours la version en cours.
* Utilisez `https://vault010.vault.local.azurestack.external:443/keys/key01/d0b36ee2e3d14e9f967b8b6b1d38938a` pour obtenir cette version spécifique.

### <a name="get-a-key"></a>Obtenir une clé

Utilisez la cmdlet **Get-AzureKeyVaultKey** pour lire une clé et ses détails.

```PowerShell
Get-AzureKeyVaultKey -VaultName "Vault01" -Name "Key01"
```

### <a name="create-a-secret"></a>Créer un secret

Utilisez la cmdlet **Set-AzureKeyVaultSecret** pour créer ou mettre à jour un secret dans un coffre. Un secret est créé s’il n’existe pas encore. Une nouvelle version du secret est créée s’il existe déjà.

```PowerShell
$secretvalue = ConvertTo-SecureString "User@123" -AsPlainText -Force
Set-AzureKeyVaultSecret -VaultName "Vault01" -Name "Secret01" -SecretValue $secretvalue
```

**Sortie**

![Créer un secret](media/azure-stack-key-vault-manage-powershell/image6.png)

### <a name="get-a-secret"></a>Obtenir un secret

Utilisez la cmdlet **Get-AzureKeyVaultSecret** pour lire un secret dans un coffre de clés. Cette commande peut retourner toutes les versions ou des versions spécifiques d’un secret.

```PowerShell
Get-AzureKeyVaultSecret -VaultName "Vault01" -Name "Secret01"
```

Après avoir créé les clés et les secrets, vous pouvez autoriser des applications externes à les utiliser.

## <a name="authorize-an-application-to-use-a-key-or-secret"></a>Autoriser une application à utiliser une clé ou un secret

Utilisez la cmdlet **Set-AzureRmKeyVaultAccessPolicy** pour autoriser une application à accéder à une clé ou à un secret dans le coffre de clés. Dans l’exemple suivant, le nom du coffre est `ContosoKeyVault`ContosoKeyVault et l’ID client de l’application que vous voulez autoriser est `8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed`. Pour autoriser l’application, exécutez la commande suivante. Si vous le souhaitez, vous pouvez spécifier le paramètre `PermissionsToKeys` pour définir des autorisations pour un utilisateur, une application ou un groupe de sécurité.

```PowerShell
Set-AzureRmKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -ServicePrincipalName 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed -PermissionsToKeys decrypt,sign
```

Si vous souhaitez autoriser cette même application à lire les secrets de votre coffre, exécutez l’applet de commande suivante :

```PowerShell
Set-AzureRmKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -ServicePrincipalName 8f8c4bbd-485b-45fd-98f7-ec6300 -PermissionsToKeys Get
```

## <a name="next-steps"></a>Étapes suivantes

* [Déployer une machine virtuelle avec un mot de passe stocké dans Key Vault](azure-stack-key-vault-deploy-vm-with-secret.md)
* [Déployer une machine virtuelle avec un certificat stocké dans Key Vault](azure-stack-key-vault-push-secret-into-vm.md)
