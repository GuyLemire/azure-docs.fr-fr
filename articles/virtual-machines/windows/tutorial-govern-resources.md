---
title: 'Didacticiel : régir des machines virtuelles Azure à l’aide d’Azure PowerShell | Microsoft Docs'
description: Avec ce didacticiel, vous allez apprendre à utiliser Azure PowerShell afin de gérer les machines virtuelles Azure en appliquant le RBAC, des stratégies, des verrous et des balises
services: virtual-machines-windows
documentationcenter: virtual-machines
author: tfitzmac
manager: jeconnoc
editor: tysonn
ms.service: virtual-machines-windows
ms.workload: infrastructure
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: tutorial
ms.date: 10/12/2018
ms.author: tomfitz
ms.custom: mvc
ms.openlocfilehash: 6377a54cc862bb5f62726c3ce91a41cc6eb0763d
ms.sourcegitcommit: 3a02e0e8759ab3835d7c58479a05d7907a719d9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/13/2018
ms.locfileid: "49311386"
---
# <a name="tutorial-learn-about-windows-virtual-machine-governance-with-azure-powershell"></a>Tutoriel : en savoir plus sur la gouvernance de machines virtuelles Windows avec Azure PowerShell

[!INCLUDE [Resource Manager governance introduction](../../../includes/resource-manager-governance-intro.md)]

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Les exemples de cet article nécessitent la version 6.0 ou ultérieure d’Azure PowerShell. Si vous exécutez PowerShell localement et si vous n’avez pas la version 6.0 ou version ultérieure, [mettez à jour votre version](/powershell/azure/install-azurerm-ps). Vous devez également exécuter `Connect-AzureRmAccount` pour créer une connexion avec Azure. Pour les installations locales, vous devez également [télécharger le module PowerShell Azure AD](https://www.powershellgallery.com/packages/AzureAD/) pour créer un nouveau groupe Azure Active Directory.

## <a name="understand-scope"></a>Comprendre la portée

[!INCLUDE [Resource Manager governance scope](../../../includes/resource-manager-governance-scope.md)]

Dans ce didacticiel, vous allez appliquer tous les paramètres de gestion à un groupe de ressources afin de pouvoir facilement supprimer ces paramètres lorsque vous avez terminé.

Créons ce groupe de ressources.

```azurepowershell-interactive
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS
```

Pour le moment, le groupe de ressources est vide.

## <a name="role-based-access-control"></a>Contrôle d’accès en fonction du rôle

Vous devez vous assurer que les utilisateurs de votre organisation disposent du niveau d’accès approprié à ces ressources. Il n’est pas question de leur accorder un accès illimité, mais de faire en sorte qu’ils puissent accomplir leur travail. Le [contrôle d’accès en fonction du rôle](../../role-based-access-control/overview.md) vous permet de définir les utilisateurs autorisés à effectuer des actions spécifiques dans une étendue.

Pour créer et supprimer des attributions de rôles, les utilisateurs doivent disposer d’un accès `Microsoft.Authorization/roleAssignments/*`. Cet accès est accordé par le biais du rôle Propriétaire ou Administrateur de l’accès utilisateur.

Pour gérer les solutions de machine virtuelle, il existe trois rôles de ressource qui fournissent un accès souvent nécessaire :

* [Collaborateur de machine virtuelle](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor)
* [Collaborateur de réseau](../../role-based-access-control/built-in-roles.md#network-contributor)
* [Contributeur de compte de stockage](../../role-based-access-control/built-in-roles.md#storage-account-contributor)

Au lieu d’assigner des rôles à des utilisateurs, il est souvent plus facile d’utiliser un groupe Azure Active Directory hébergeant les utilisateurs qui ont besoin d’effectuer des actions similaires. Ensuite, vous affectez ce groupe au rôle approprié. Pour cet article, utilisez un groupe existant pour la gestion de la machine virtuelle, ou utilisez le portail pour [créer un groupe Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

Après avoir créé un groupe ou trouvé un groupe existant, utilisez la commande [New-AzureRmRoleAssignment](/powershell/module/azurerm.resources/new-azurermroleassignment) pour assigner le groupe Azure Active Directory au rôle Contributeur de machine virtuelle pour le groupe de ressources.  

```azurepowershell-interactive
$adgroup = Get-AzureRmADGroup -DisplayName <your-group-name>

New-AzureRmRoleAssignment -ObjectId $adgroup.id `
  -ResourceGroupName myResourceGroup `
  -RoleDefinitionName "Virtual Machine Contributor"
```

Si vous recevez une erreur indiquant **Principal <guid> does not exist in the directory** (Le principal n’existe pas dans le répertoire), cela signifie que le nouveau groupe ne s’est pas propagé dans Azure Active Directory. Essayez d’exécuter à nouveau la commande.

En règle générale, vous répétez ce processus pour *Contributeur de réseaux* et *Contributeur de comptes de stockage*, pour être sûr que les utilisateurs sont affectés à la gestion des ressources déployées. Dans cet article, vous pouvez ignorer ces étapes.

## <a name="azure-policy"></a>Azure Policy

[Azure Policy](../../azure-policy/azure-policy-introduction.md) vous permet de vous assurer que toutes les ressources de l’abonnement répondent aux normes de l’entreprise. Votre abonnement comprend déjà plusieurs définitions de stratégie. Pour afficher les définitions de stratégie disponibles, utilisez la commande [Get-AzureRmPolicyDefinition](/powershell/module/AzureRM.Resources/Get-AzureRmPolicyDefinition) :

```azurepowershell-interactive
(Get-AzureRmPolicyDefinition).Properties | Format-Table displayName, policyType
```

Vous voyez les définitions de stratégie existantes. Le type de stratégie est **BuiltIn** ou **Custom**. Recherchez les définitions qui décrivent la condition que vous souhaitez affecter. Dans cet article, vous affectez des stratégies qui :

* Limitent les emplacements pour toutes les ressources
* Limitent les références SKU pour les machines virtuelles
* Auditent les machines virtuelles qui n’utilisent pas de disques managés

L’exemple suivant vous permet de récupérer trois définitions de stratégie basées sur le nom d’affichage. Vous utilisez la commande [New-AzureRMPolicyAssignment](/powershell/module/azurerm.resources/new-azurermpolicyassignment) pour assigner ces définitions au groupe de ressources. Pour certaines stratégies, vous devez fournir des valeurs de paramètre pour définir les valeurs autorisées.

```azurepowershell-interactive
# Values to use for parameters
$locations ="eastus", "eastus2"
$skus = "Standard_DS1_v2", "Standard_E2s_v2"

# Get the resource group
$rg = Get-AzureRmResourceGroup -Name myResourceGroup

# Get policy definitions for allowed locations, allowed SKUs, and auditing VMs that don't use managed disks
$locationDefinition = Get-AzureRmPolicyDefinition | where-object {$_.properties.displayname -eq "Allowed locations"}
$skuDefinition = Get-AzureRmPolicyDefinition | where-object {$_.properties.displayname -eq "Allowed virtual machine SKUs"}
$auditDefinition = Get-AzureRmPolicyDefinition | where-object {$_.properties.displayname -eq "Audit VMs that do not use managed disks"}

# Assign policy for allowed locations
New-AzureRMPolicyAssignment -Name "Set permitted locations" `
  -Scope $rg.ResourceId `
  -PolicyDefinition $locationDefinition `
  -listOfAllowedLocations $locations

# Assign policy for allowed SKUs
New-AzureRMPolicyAssignment -Name "Set permitted VM SKUs" `
  -Scope $rg.ResourceId `
  -PolicyDefinition $skuDefinition `
  -listOfAllowedSKUs $skus

# Assign policy for auditing unmanaged disks
New-AzureRMPolicyAssignment -Name "Audit unmanaged disks" `
  -Scope $rg.ResourceId `
  -PolicyDefinition $auditDefinition
```

## <a name="deploy-the-virtual-machine"></a>Déployer la machine virtuelle

Vous avez affecté des rôles et des stratégies, vous êtes donc prêt à déployer votre solution. La taille par défaut est Standard_DS1_v2, qui correspond à l’une des références SKU autorisées. Lors de l’exécution de cette étape, vous êtes invité à saisir vos informations d’identification. Les valeurs que vous saisissez sont configurées comme le nom d’utilisateur et le mot de passe pour la machine virtuelle.

```azurepowershell-interactive
New-AzureRmVm -ResourceGroupName "myResourceGroup" `
     -Name "myVM" `
     -Location "East US" `
     -VirtualNetworkName "myVnet" `
     -SubnetName "mySubnet" `
     -SecurityGroupName "myNetworkSecurityGroup" `
     -PublicIpAddressName "myPublicIpAddress" `
     -OpenPorts 80,3389
```

Une fois votre déploiement terminé, vous pouvez appliquer davantage de paramètres de gestion à la solution.

## <a name="lock-resources"></a>Verrouiller des ressources

Les [verrous de ressources](../../azure-resource-manager/resource-group-lock-resources.md) empêchent les utilisateurs de votre organisation de supprimer ou de modifier accidentellement des ressources critiques. Contrairement au contrôle d’accès basé sur les rôles, les verrous de ressources permettent d’appliquer une restriction à tous les utilisateurs et rôles. Vous pouvez définir le niveau de verrouillage sur *CanNotDelete* ou *ReadOnly*.

Pour verrouiller la machine virtuelle et le groupe de sécurité réseau, utilisez la commande [New-AzureRmResourceLock](/powershell/module/azurerm.resources/new-azurermresourcelock) :

```azurepowershell-interactive
# Add CanNotDelete lock to the VM
New-AzureRmResourceLock -LockLevel CanNotDelete `
  -LockName LockVM `
  -ResourceName myVM `
  -ResourceType Microsoft.Compute/virtualMachines `
  -ResourceGroupName myResourceGroup

# Add CanNotDelete lock to the network security group
New-AzureRmResourceLock -LockLevel CanNotDelete `
  -LockName LockNSG `
  -ResourceName myNetworkSecurityGroup `
  -ResourceType Microsoft.Network/networkSecurityGroups `
  -ResourceGroupName myResourceGroup
```

Pour tester les verrous, essayez d’exécuter la commande suivante :

```azurepowershell-interactive 
Remove-AzureRmResourceGroup -Name myResourceGroup
```

Un message d’erreur s’affiche, indiquant que l’opération de suppression ne peut pas être effectuée à cause d’un verrou. Pour pouvoir supprimer le groupe de ressources, vous devez impérativement en supprimer les verrous. Cette étape est expliquée dans [Nettoyer les ressources](#clean-up-resources).

## <a name="tag-resources"></a>Baliser des ressources

Vous allez appliquer des [balises](../../azure-resource-manager/resource-group-using-tags.md) à vos ressources Azure pour les organiser de façon logique par catégories. Chaque balise se compose d’un nom et d’une valeur. Par exemple, vous pouvez appliquer le nom « Environnement » et la valeur « Production » à toutes les ressources en production.

[!INCLUDE [Resource Manager governance tags Powershell](../../../includes/resource-manager-governance-tags-powershell.md)]

Pour appliquer des balises à une machine virtuelle, utilisez la commande [Set-AzureRmResource](/powershell/module/azurerm.resources/set-azurermresource) :

```azurepowershell-interactive
# Get the virtual machine
$r = Get-AzureRmResource -ResourceName myVM `
  -ResourceGroupName myResourceGroup `
  -ResourceType Microsoft.Compute/virtualMachines

# Apply tags to the virtual machine
Set-AzureRmResource -Tag @{ Dept="IT"; Environment="Test"; Project="Documentation" } -ResourceId $r.ResourceId -Force
```

### <a name="find-resources-by-tag"></a>Rechercher des ressources à l’aide de leurs balises

Pour rechercher des ressources avec le nom et la valeur d’une balise, utilisez la commande [Get-AzureRmResource](/powershell/module/azurerm.resources/get-azurermresource) :

```azurepowershell-interactive
(Get-AzureRmResource -Tag @{ Environment="Test"}).Name
```

Vous pouvez utiliser les valeurs retournées pour des tâches de gestion telles que l’arrêt de toutes les machines virtuelles avec une valeur de balise.

```azurepowershell-interactive
Get-AzureRmResource -Tag @{ Environment="Test"} | Where-Object {$_.ResourceType -eq "Microsoft.Compute/virtualMachines"} | Stop-AzureRmVM
```

### <a name="view-costs-by-tag-values"></a>Afficher les coûts selon les valeurs de balise

[!INCLUDE [Resource Manager governance tags billing](../../../includes/resource-manager-governance-tags-billing.md)]

## <a name="clean-up-resources"></a>Supprimer des ressources

Vous ne pouvez pas supprimer le groupe de sécurité réseau verrouillé tant que vous n’avez pas supprimé le verrou. Pour supprimer le verrou, utilisez la commande [Remove-AzureRmResourceLock](/powershell/module/azurerm.resources/remove-azurermresourcelock) :

```azurepowershell-interactive
Remove-AzureRmResourceLock -LockName LockVM `
  -ResourceName myVM `
  -ResourceType Microsoft.Compute/virtualMachines `
  -ResourceGroupName myResourceGroup
Remove-AzureRmResourceLock -LockName LockNSG `
  -ResourceName myNetworkSecurityGroup `
  -ResourceType Microsoft.Network/networkSecurityGroups `
  -ResourceGroupName myResourceGroup
```

Lorsque vous n’en avez plus besoin, vous pouvez utiliser la commande [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) pour supprimer le groupe de ressources, la machine virtuelle et toutes les ressources associées.

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>Étapes suivantes

Ce didacticiel vous montré comment créer une image de machine virtuelle. Vous avez appris à effectuer les actions suivantes :

> [!div class="checklist"]
> * Assigner des utilisateurs à un rôle
> * Appliquer des stratégies qui imposent des standards
> * Protéger les ressources critiques avec des verrous
> * Ajouter des balises aux ressources pour la facturation et la gestion

Passez au didacticiel suivant pour en savoir plus sur les machines virtuelles hautement disponibles.

> [!div class="nextstepaction"]
> [Surveiller les machines virtuelles](tutorial-monitoring.md)

