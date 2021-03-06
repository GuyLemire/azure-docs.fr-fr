---
title: Résoudre les problèmes du service Azure Stack Validation | Microsoft Docs
description: Résolvez les problèmes de validation en tant que service pour Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/26/2018
ms.author: mabrigg
ms.reviewer: johnhas
ROBOTS: NOINDEX
ms.openlocfilehash: 0fb1afb1a07b31f7e261c958b8a03bec3b299433
ms.sourcegitcommit: f4b78e2c9962d3139a910a4d222d02cda1474440
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/12/2019
ms.locfileid: "54245291"
---
# <a name="troubleshoot-validation-as-a-service"></a>Résoudre les problèmes de validation en tant que service

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Voici les problèmes courants, sans lien avec les versions logicielles, et leurs solutions.

## <a name="local-agent"></a>Agent local

### <a name="the-portal-shows-local-agent-in-debug-mode"></a>Le portail affiche l’agent local en mode débogage

Cela vient probablement du fait que l’agent ne peut pas envoyer de pulsations au service, car la connexion réseau est instable. Une pulsation est envoyée toutes les cinq minutes. Si le service ne reçoit pas de pulsation pendant 15 minutes, il considère que l’agent est inactif et plus aucun test n’est planifié pour ce service. Vérifiez le message d’erreur dans le fichier *Agenthost.log*, il se trouve dans le répertoire où l’agent a été démarré.

> [!Note]
> Tous les tests déjà en cours d’exécution sur l’agent continuent de s’exécuter, mais si la pulsation n’est pas restaurée avant la fin du test, l’agent ne peut pas mettre à jour l’état du test, ni charger les journaux. Le test apparaît toujours comme étant **en cours d’exécution** et il doit être annulé.

### <a name="agent-process-on-machine-was-shut-down-while-executing-test-what-to-expect"></a>Le processus de l’agent sur la machine a été arrêté pendant l’exécution du test. À quoi s’attendre ?

Si le processus de l’agent est arrêté de manière anormale, par exemple un redémarrage de la machine avec suppression du processus (un CTRL+C dans la fenêtre de l’agent est considéré comme un arrêt correct), le test en cours d’exécution sur cette machine continue de s’afficher **en cours d’exécution**. Si l’agent est redémarré, il met à jour l’état du test qui est **annulé**. Si l’agent n’est pas redémarré, le test s’affiche comme étant **en cours d’exécution**, et vous devez annuler manuellement le test.

> [!Note]
> Les tests au sein d’un workflow sont planifiés pour une exécution séquentielle. Les tests **en attente** ne sont pas exécutés tant que les tests affichant l’état **en cours d’exécution** dans ce même workflow ne sont pas terminés.

## <a name="vm-images"></a>Images de machine virtuelle

### <a name="handle-slow-network-connectivity"></a>Gérer les problèmes de lenteur de la connexion réseau

Vous pouvez télécharger l’image PIR sur un partage dans votre centre de données local. Vous pouvez ensuite vérifier l’image.

<!-- This is from the appendix to the Deploy local agent topic. -->

#### <a name="download-pir-image-to-local-share-in-case-of-slow-network-traffic"></a>Télécharger l’image PIR sur un partage local en cas de trafic réseau lent

1. Téléchargez AzCopy à partir de : [vaasexternaldependencies(AzCopy)](https://vaasexternaldependencies.blob.core.windows.net/prereqcomponents/AzCopy.zip)

2. Extrayez AzCopy.zip et accédez au répertoire contenant AzCopy.exe

3. Ouvrez Windows PowerShell à partir d’une invite de commandes avec élévation des privilèges. Exécutez les commandes suivantes :

```PowerShell  
    .\azcopy.exe /Source:'https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container' /Dest:'<LocalFileShare>' /Pattern:'Server2016DatacenterFullBYOL.vhd' /NC:12 /V:azcopylog.log /Y
    .\azcopy.exe /Source:'https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container' /Dest:'<LocalFileShare>' /Pattern:'Server2016DatacenterCoreBYOL.vhd' /NC:12 /V:azcopylog.log /Y
    .\azcopy.exe /Source:'https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container' /Dest:'<LocalFileShare>' /Pattern:'WindowsServer2012R2DatacenterBYOL.vhd' /NC:12 /V:azcopylog.log /Y
    .\azcopy.exe /Source:'https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container' /Dest:'<LocalFileShare>' /Pattern:'Ubuntu1404LTS.vhd' /NC:12 /V:azcopylog.log /Y
    .\azcopy.exe /Source:'https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container' /Dest:'<LocalFileShare>' /Pattern:'Ubuntu1604-20170619.1.vhd' /NC:12 /V:azcopylog.log /Y
```

> [!Note]  
> LocalFileShare représente le chemin de partage ou le chemin local.

#### <a name="verifying-pir-image-file-hash-value"></a>Vérification de la valeur de hachage du fichier Image PIR

Vous pouvez utiliser l’applet de commande **Get-HashFile** pour obtenir la valeur de hachage des fichiers image du dépôt d’images publiques téléchargées pour vérifier l’intégrité des images.

| Nom de fichier | SHA256 |
|---------------------------------------|------------------------------------------------------------------|
| Server2016DatacenterFullBYOL.vhd | 6ED58DCA666D530811A1EA563BA509BF9C29182B902D18FCA03C7E0868F733E9 |
| WindowsServer2012R2DatacenterBYOL.vhd | 9792CBF742870B1730B9B16EA814C683A8415EFD7601DDB6D5A76D0964767028 |
| Server2016DatacenterCoreBYOL.vhd | 5E80E1A6721A48A10655E6154C1B90E320DF5558487D6A0D7BFC7DCD32C4D9A5 |
| Ubuntu1404LTS.vhd | B24CDD12352AAEBC612A4558AB9E80F031A2190E46DCB459AF736072742E20E0 |
| Ubuntu1604-20170619.1.vhd | C481B88B60A01CBD5119A3F56632A2203EE5795678D3F3B9B764FFCA885E26CB |

### <a name="failure-occurs-when-uploading-vm-image-in-the-vaasprereq-script"></a>Une défaillance se produit lors du chargement d’une image de machine virtuelle dans le script `VaaSPreReq`

Vérifiez tout d’abord que l’environnement est sain :

1. À partir du DVM/de la zone de saut, vérifiez que vous pouvez vous connecter au portail d’administration à l’aide des informations d’identification de l’administrateur.
1. Vérifiez qu’aucune alerte ou qu’aucun avertissement ne s’affiche.

Si l’environnement est sain, chargez manuellement les 5 images de machine virtuelle requises pour les exécutions du test du service VaaS :

1. Connectez-vous au portail d’administration en tant qu’administrateur de service. L’URL du portail d’administration se trouve dans le fichier d’informations de tampon ou ECE. Pour obtenir des instructions, consultez la section [Paramètres d’environnement](azure-stack-vaas-parameters.md#environment-parameters).
1. Sélectionnez **Autres services** > **Fournisseurs de ressources** > **Compute** > **Images de VM**.
1. Sélectionnez le bouton **+Ajouter** situé en haut du panneau **Images de VM**.
1. Vérifiez ou modifiez les valeurs des champs suivants pour la première image de machine virtuelle :
    > [!IMPORTANT]
    > Toutes les valeurs par défaut ne sont pas correctes pour l’élément existant de la Place de marché.

    | Champ  | Valeur  |
    |---------|---------|
    | Publisher | MicrosoftWindowsServer |
    | Offre | WindowsServer |
    | Type de système d’exploitation |  Windows |
    | SKU | 2012-R2-Datacenter |
    | Version | 1.0.0 |
    | URI de l’objet blob du disque du système d’exploitation | https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container/WindowsServer2012R2DatacenterBYOL.vhd |

1. Cliquez sur le bouton **Créer**.
1. Répétez cette opération pour les images restantes de machine virtuelle.

Propriétés des 5 images de machine virtuelle :

| Publisher  | Offre  | Type de système d’exploitation | SKU | Version | URI de l’objet blob du disque du système d’exploitation |
|---------|---------|---------|---------|---------|---------|
| MicrosoftWindowsServer| WindowsServer |  Windows | 2012-R2-Datacenter | 1.0.0 | https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container/WindowsServer2012R2DatacenterBYOL.vhd |
| MicrosoftWindowsServer | WindowsServer |  Windows | 2016-centre-de-données | 1.0.0 | https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container/Server2016DatacenterFullBYOL.vhd |
| MicrosoftWindowsServer | WindowsServer |  Windows | 2016-Datacenter-Server-Core | 1.0.0 | https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container/Server2016DatacenterCoreBYOL.vhd |
| Canonical | UbuntuServer | Linux | 14.04.3-LTS | 1.0.0 | https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container/Ubuntu1404LTS.vhd |
| Canonical | UbuntuServer | Linux | 16.04-LTS | 16.04.20170811 | https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container/Ubuntu1604-20170619.1.vhd |

## <a name="next-steps"></a>Étapes suivantes

- Pour connaître les modifications des dernières versions, consultez l’article [Notes de publication de la validation en tant que service](azure-stack-vaas-release-notes.md).