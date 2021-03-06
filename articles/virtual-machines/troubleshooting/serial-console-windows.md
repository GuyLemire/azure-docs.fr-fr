---
title: Console série de machine virtuelle Azure pour Windows | Microsoft Docs
description: Console série bidirectionnelle pour machines virtuelles Windows Azure.
services: virtual-machines-windows
documentationcenter: ''
author: harijay
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 10/31/2018
ms.author: harijay
ms.openlocfilehash: 535c65f58ac9a3f39faa347ca853bfa410b7f182
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/10/2018
ms.locfileid: "53185327"
---
# <a name="virtual-machine-serial-console-for-windows"></a>Console série de machine virtuelle pour Windows

La console série de machine virtuelle du portail Azure permet aux machines virtuelles Windows d’accéder à une console texte. Cette connexion série s’effectue par le biais du port série COM1 de la machine virtuelle. Elle fournit l’accès à la machine virtuelle et n’est pas liée au réseau de la machine virtuelle ni à l’état du système d’exploitation. Seul le portail Azure permet d’accéder à la console série pour une machine virtuelle. Cette opération est autorisée uniquement pour les utilisateurs disposant d’un rôle d’accès Contributeur de machine virtuelle ou d’un rôle supérieur vis-à-vis de la machine virtuelle.

Pour en savoir plus sur la console série des machines virtuelles Linux, consultez [Console série de machine virtuelle pour Linux](serial-console-linux.md).

> [!NOTE]
> La console série de machines virtuelles est généralement disponible dans les régions Azure globales. Elle n’est pas encore disponible dans les clouds Azure Government, ni dans les clouds Azure - Chine.


## <a name="prerequisites"></a>Prérequis

* La machine virtuelle dans laquelle vous accédez à une console série doit utiliser le modèle de déploiement de gestion des ressources. Les déploiements classiques ne sont pas pris en charge.

* Les [diagnostics de démarrage](boot-diagnostics.md) doivent être activés sur la machine virtuelle dans laquelle vous accédez à une console série.

    ![Paramètres de l’option Diagnostics de démarrage](../media/virtual-machines-serial-console/virtual-machine-serial-console-diagnostics-settings.png)

* Un compte utilisant une console série doit disposer du [rôle Contributeur de machine virtuelle](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) pour la machine virtuelle et du compte de stockage de [diagnostics de démarrage](boot-diagnostics.md) :

* La machine virtuelle dans laquelle vous accédez à une console série doit disposer d’un compte avec mot de passe. Vous pouvez en créer un avec la fonction [Réinitialiser le mot de passe](https://docs.microsoft.com/azure/virtual-machines/extensions/vmaccess#reset-password) de l’extension d’accès aux machines virtuelles. Sélectionnez **Réinitialiser le mot de passe** dans la section **Support + dépannage**.


## <a name="get-started-with-the-serial-console"></a>Bien démarrer avec la console série
Pour les machines virtuelles, la console série est accessible uniquement sur le portail Azure :

  1. Ouvrez le [portail Azure](https://portal.azure.com).
  1. Dans le menu de gauche, sélectionnez **Machines virtuelles**.
  1. Sélectionnez une machine virtuelle dans la liste. La page de présentation de la machine virtuelle s’ouvre.
  1. Faites défiler l’écran jusqu’à la section **Support + dépannage**, puis sélectionnez **Console série**. Un nouveau volet s’ouvre avec la console série, puis démarre la connexion.

## <a name="enable-serial-console-functionality"></a>Activer la fonctionnalité de console série

### <a name="enable-the-serial-console-in-custom-or-older-images"></a>Activer la console série dans les images personnalisées ou anciennes
La [console SAC](https://technet.microsoft.com/library/cc787940(v=ws.10).aspx) est activée par défaut dans les nouvelles images Windows Server sur Azure. La console SAC est prise en charge sur les versions serveur de Windows, mais elle n’est pas disponible sur les versions client (par exemple Windows 10, Windows 8 ou Windows 7).

Pour les anciennes images Windows Server (créées avant février 2018), vous pouvez activer automatiquement la console série via la commande Run du portail Azure. Dans le portail Azure, sélectionnez **Exécuter la commande**, puis sélectionnez la commande nommée **EnableEM** dans la liste.

![Liste des commandes Run](./media/virtual-machines-serial-console/virtual-machine-windows-serial-console-runcommand.png)

Vous pouvez aussi activer manuellement la console série pour les machines virtuelles Windows créées avant février 2018 en effectuant les étapes suivantes :

1. Connectez-vous à la machine virtuelle Windows à l’aide du Bureau à distance.
1. À une invite de commandes d’administration, exécutez les commandes suivantes :
    - `bcdedit /ems {current} on`
    - `bcdedit /emssettings EMSPORT:1 EMSBAUDRATE:115200`
1. Redémarrez le système pour activer la console SAC.

    ![Console SAC](./media/virtual-machines-serial-console/virtual-machine-windows-serial-console-connect.gif)

Si nécessaire, la console SAC peut aussi être activée hors connexion :

1. Sélectionnez le disque Windows pour lequel vous souhaitez configurer la console SAC et attachez-le en tant que disque de données à la machine virtuelle existante.

1. À une invite de commandes d’administration, exécutez les commandes suivantes :
   - `bcdedit /store <mountedvolume>\boot\bcd /ems {default} on`
   - `bcdedit /store <mountedvolume>\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200`

#### <a name="how-do-i-know-if-sac-is-enabled"></a>Comment savoir si la console SAC est activée ?

Si la [console SAC](https://technet.microsoft.com/library/cc787940(v=ws.10).aspx) n’est pas activée, la console série n’affichera pas l’invite SAC. Parfois, les informations d’intégrité de la machine virtuelle s’affichent, et d’autres fois non. Si vous utilisez une image Windows Server créée avant février 2018, la console SAC ne sera probablement pas activée.

### <a name="enable-the-windows-boot-menu-in-the-serial-console"></a>Activer le menu de démarrage Windows dans la console série

Si vous souhaitez que les invites de commandes du chargeur de démarrage Windows soient affichées dans la console série, vous pouvez ajouter les options supplémentaires suivantes à vos données de configuration de démarrage. Pour plus d’informations, consultez [bcdedit](https://docs.microsoft.com/windows-hardware/drivers/devtest/bcdedit--set).

1. Connectez-vous à la machine virtuelle Windows à l’aide du Bureau à distance.

1. À une invite de commandes d’administration, exécutez les commandes suivantes :
   - `bcdedit /set {bootmgr} displaybootmenu yes`
   - `bcdedit /set {bootmgr} timeout 10`
   - `bcdedit /set {bootmgr} bootems yes`

1. Redémarrer le système pour activer le menu de démarrage

> [!NOTE]
> Le délai d’expiration que vous avez défini pour l’affichage du menu du gestionnaire de démarrage aura un impact sur le temps de démarrage de votre système d’exploitation. Si vous pensez que la valeur de délai d’attente de 10 secondes est trop courte ou trop longue, affectez une valeur différente.

## <a name="use-serial-console"></a>Utiliser la console série

### <a name="use-cmd-or-powershell-in-serial-console"></a>Utiliser CMD ou PowerShell dans la console série

1. Connectez-vous à la console série. Si la connexion réussit, l’invite est **SAC>**  :

    ![Se connecter à la console SAC](./media/virtual-machines-serial-console/virtual-machine-windows-serial-console-connect-sac.png)

1.  Entrez `cmd` pour créer un canal qui a une instance CMD.

1.  Entrez `ch -si 1` pour basculer vers le canal qui exécute l’instance CMD.

1.  Appuyez sur **Entrée**, puis entrez des informations de connexion disposant d’autorisations administratives.

1.  Une fois que vous avez entré des informations d’identification valides, l’instance CMD s’ouvre.

1.  Pour démarrer une instance de PowerShell, entrez `PowerShell` dans l’instance CMD, puis appuyez sur **Entrée**.

    ![Ouvrir l’instance PowerShell](./media/virtual-machines-serial-console/virtual-machine-windows-serial-console-powershell.png)

### <a name="use-the-serial-console-for-nmi-calls"></a>Utiliser la console série pour les appels NMI
Une interruption non masquable (NMI) est conçue pour créer un signal que les logiciels sur une machine virtuelle n’ignoreront pas. À l’origine, les NMI ont été utilisées pour surveiller les problèmes matériels sur les systèmes nécessitant des temps de réponse spécifiques. Aujourd’hui, les programmeurs et les administrateurs système utilisent souvent les NMI comme mécanisme de débogage ou de dépannage des systèmes qui ne répondent pas.

Vous pouvez utiliser la console série pour envoyer une NMI à une machine virtuelle Azure à l’aide de l’icône de clavier dans la barre de commandes. Une fois que la NMI est remise, la configuration de machine virtuelle contrôle le mode de réponse du système. Windows peut être configuré pour se bloquer et créer un fichier de vidage mémoire lors de la réception d’une NMI.

![Envoyer une NMI](../media/virtual-machines-serial-console/virtual-machine-windows-serial-console-nmi.png) <br>

Pour plus d’informations sur la configuration de Windows pour créer un fichier de vidage sur incident quand il reçoit une NMI, consultez [Guide pratique pour générer un fichier de vidage sur incident à l’aide d’une NMI](https://support.microsoft.com/help/927069/how-to-generate-a-complete-crash-dump-file-or-a-kernel-crash-dump-file).

### <a name="use-function-keys-in-serial-console"></a>Utiliser les touches de fonction dans la console série
L’utilisation des touches de fonction est activée dans la console série sur les machines virtuelles Windows. Dans la liste déroulante de la console série, la touche F8 permet d’accéder facilement au menu des paramètres de démarrage avancés, mais la console série est compatible avec toutes les autres touches de fonction. Selon l’ordinateur à partir duquel vous utilisez la console série et son clavier, il se peut que vous deviez appuyer sur **Fn** + **F1** (ou F2, F3, etc.).

### <a name="use-wsl-in-serial-console"></a>Utiliser WSL dans la console série
Le sous-système Windows pour Linux (WSL) est activé pour Windows Server 2019 ou versions ultérieures. Ainsi, vous pouvez activer WSL afin de l’utiliser dans la console série si vous exécutez Windows Server 2019 ou une version ultérieure. Cette possibilité est particulièrement intéressante pour les utilisateurs qui connaissent également les commandes Linux. Pour obtenir des instructions sur l’activation de WSL pour Windows Server, consultez le [guide d’installation](https://docs.microsoft.com/windows/wsl/install-on-server).

## <a name="disable-serial-console"></a>Désactiver la console série
Par défaut, tous les abonnements ont accès à la console série pour toutes les machines virtuelles. Vous pouvez désactiver la console série au niveau de l’abonnement ou de la machine virtuelle.

> [!NOTE]
> Afin d’activer ou de désactiver la console série pour un abonnement, vous devez disposer des autorisations en écriture sur l’abonnement. Ces autorisations incluent, mais de façon non limitative, les rôles d’administrateur ou de propriétaire. Des rôles personnalisés peuvent aussi disposer d’autorisations en écriture.

### <a name="subscription-level-disable"></a>Désactiver au niveau de l’abonnement
La console série peut être désactivée pour un abonnement complet par le biais de l’[appel d’API REST Disable Console](/rest/api/serialconsole/console/disableconsole). Vous pouvez utiliser la fonctionnalité **Essayez** disponible sur la page de documentation de l’API afin de désactiver et d’activer la console série pour un abonnement. Entrez votre ID d’abonnement pour **subscriptionId**, entrez « par défaut » pour **par défaut**, puis sélectionnez **Exécuter**. Les commandes Azure CLI ne sont pas encore disponibles.

![Essayez l’API REST](../media/virtual-machines-serial-console/virtual-machine-serial-console-rest-api-try-it.png)

Vous pouvez également utiliser le jeu de commandes Bash ci-dessous dans Cloud Shell afin de désactiver, d’activer et d’afficher l’état désactivé de la console série pour un abonnement :

* Afin d’obtenir l’état désactivé de la console série pour un abonnement :
    ```azurecli-interactive
    $ export ACCESSTOKEN=($(az account get-access-token --output=json | jq .accessToken | tr -d '"'))

    $ export SUBSCRIPTION_ID=$(az account show --output=json | jq .id -r)

    $ curl "https://management.azure.com/subscriptions/$SUBSCRIPTION_ID/providers/Microsoft.SerialConsole/consoleServices/default?api-version=2018-05-01" -H "Authorization: Bearer $ACCESSTOKEN" -H "Content-Type: application/json" -H "Accept: application/json" -s | jq .properties
    ```
* Afin de désactiver la console série pour un abonnement :
    ```azurecli-interactive
    $ export ACCESSTOKEN=($(az account get-access-token --output=json | jq .accessToken | tr -d '"'))

    $ export SUBSCRIPTION_ID=$(az account show --output=json | jq .id -r)

    $ curl -X POST "https://management.azure.com/subscriptions/$SUBSCRIPTION_ID/providers/Microsoft.SerialConsole/consoleServices/default/disableConsole?api-version=2018-05-01" -H "Authorization: Bearer $ACCESSTOKEN" -H "Content-Type: application/json" -H "Accept: application/json" -s -H "Content-Length: 0"
    ```
* Afin d’activer la console série pour un abonnement :
    ```azurecli-interactive
    $ export ACCESSTOKEN=($(az account get-access-token --output=json | jq .accessToken | tr -d '"'))

    $ export SUBSCRIPTION_ID=$(az account show --output=json | jq .id -r)

    $ curl -X POST "https://management.azure.com/subscriptions/$SUBSCRIPTION_ID/providers/Microsoft.SerialConsole/consoleServices/default/enableConsole?api-version=2018-05-01" -H "Authorization: Bearer $ACCESSTOKEN" -H "Content-Type: application/json" -H "Accept: application/json" -s -H "Content-Length: 0"
    ```

### <a name="vm-level-disable"></a>Désactiver au niveau de la machine virtuelle
La console série peut être désactivée pour une machine virtuelle spécifique au travers de la désactivation du paramètre de diagnostics de démarrage de cette machine virtuelle. Désactivez les diagnostics de démarrage à partir du portail Azure afin de désactiver la console série pour la machine virtuelle.

## <a name="serial-console-security"></a>Sécurité de la console série

### <a name="access-security"></a>Sécurité des accès
L’accès à la console série est limité aux utilisateurs disposant d’un rôle d’accès [Contributeur de machine virtuelle](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) ou d’un rôle supérieur vis-à-vis de la machine virtuelle. Si votre tenant Azure Active Directory nécessite une authentification multifacteur (MFA), l’accès à la console série nécessite également l’authentification MFA, car l’accès de la console série s’effectue par le biais du [portail Azure](https://portal.azure.com).

### <a name="channel-security"></a>Sécurité des canaux
Toutes les données envoyées sur les canaux sont chiffrées.

### <a name="audit-logs"></a>Journaux d’audit
Tous les accès à la console série sont journalisés dans les journaux [Diagnostics de démarrage](https://docs.microsoft.com/azure/virtual-machines/linux/boot-diagnostics) de la machine virtuelle. L’accès à ces journaux est détenu et contrôlé par l’administrateur de la machine virtuelle Azure.

>[!CAUTION]
Aucun mot de passe d’accès pour la console n’est journalisé. Toutefois, si des commandes exécutées dans la console contiennent ou affichent des mots de passe, des secrets, des noms d’utilisateur ou toute autre forme d’informations d’identification personnelle (PII), ces derniers sont écrits dans les journaux Diagnostics de démarrage de la machine virtuelle. Ils sont accompagnés de tout autre texte visible dans le cadre de l’implémentation de la fonctionnalité de scrollback de la console série. Ces journaux sont circulaires et seules les personnes disposant d’autorisations de lecture pour le compte de stockage de diagnostics peuvent y accéder. Toutefois, nous vous recommandons de suivre les bonnes pratiques concernant l’utilisation du Bureau à distance pour tout élément susceptible d’impliquer des secrets et/ou des informations d’identification personnelle.

### <a name="concurrent-usage"></a>Utilisation simultanée
Si un utilisateur est connecté à la console série alors qu’un autre utilisateur demande l’accès à la même machine virtuelle, le premier utilisateur est déconnecté pendant que le second utilisateur est connecté.

>[!CAUTION]
Cet utilisateur n’est pas réellement déconnecté. La possibilité d’appliquer une déconnexion réelle (par le biais de SIGHUP ou d’un mécanisme similaire) est en cours d’étude. Pour Windows, un délai d’expiration automatique est activé dans la console SAC. Pour Linux, vous pouvez configurer le délai d’expiration de terminal.

## <a name="accessibility"></a>Accessibilité
L’accessibilité est un point central de la console série Azure. Nous avons donc fait en sorte que la console série soit accessible aux personnes présentant des déficiences visuelles ou auditives ainsi qu’aux personnes qui ne peuvent pas utiliser une souris.

### <a name="keyboard-navigation"></a>Navigation au clavier
Utilisez la touche **Tabulation** de votre clavier pour naviguer dans l’interface de la console série à partir du portail Azure. Votre emplacement est mis en surbrillance à l’écran. Pour quitter la fenêtre de la console série, appuyez sur les touches **Ctrl**+**F6** de votre clavier.

### <a name="use-the-serial-console-with-a-screen-reader"></a>Utiliser la console série avec un lecteur d’écran
La console série comprend une prise en charge intégrée des lecteurs d’écran. Quand le lecteur d’écran est activé, le texte de remplacement du bouton sélectionné est lu à voix haute.

## <a name="common-scenarios-for-accessing-the-serial-console"></a>Scénarios courants d’accès à la console série
Scénario          | Actions à effectuer dans la console série
:------------------|:-----------------------------------------
Règles de pare-feu incorrectes | Accès à la console série et correction des règles de pare-feu Windows.
Contrôle de la corruption du système de fichiers | Accédez à la console série et récupérez le système de fichiers.
Problèmes de configuration RDP | Accédez à la console série et modifiez les paramètres. Pour plus d’informations, consultez la [documentation relative au protocole RDP](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-allow-access).
Système de verrouillage du réseau | Accédez à la console série à partir du portail Azure pour gérer le système. Certaines commandes réseau sont répertoriées sur la page [Commandes Windows : CMD et PowerShell](serial-console-cmd-ps-commands.md).
Interaction avec le chargeur de démarrage | Accédez à BCD par le biais de la console série. Pour plus d’informations, consultez [Activer le menu de démarrage Windows dans la console série](#enable-the-windows-boot-menu-in-the-serial-console).


## <a name="errors"></a>Errors
Étant donné que la plupart des erreurs sont temporaires, une nouvelle tentative de connexion peut souvent les corriger. Le tableau suivant présente une liste d’erreurs accompagnées de solutions d’atténuation.

Error                            |   Atténuation
:---------------------------------|:--------------------------------------------|
Unable to retrieve boot diagnostics settings for *&lt;VMNAME&gt;*. To use the serial console, ensure that boot diagnostics is enabled for this VM (Impossible de récupérer les paramètres de diagnostic de démarrage. Pour utiliser la console série, vérifiez que les diagnostics de démarrage sont activés dans la machine virtuelle) | Vérifiez que l’option [diagnostics de démarrage](boot-diagnostics.md) est activée dans la machine virtuelle.
The VM is in a stopped deallocated state. Start the VM and retry the serial console connection (La machine virtuelle est arrêtée et à l’état Désalloué. Démarrez la machine virtuelle, puis retentez une connexion à la console série) | La machine virtuelle doit être à l’état Démarré pour accéder à la console série.
You do not have the required permissions to use this VM serial console. Ensure you have at least Virtual Machine Contributor role permissions. (Vous ne disposez pas des autorisations nécessaires pour utiliser la console série sur cette machine virtuelle. Vous devez disposer des autorisations du rôle Contributeur de machine virtuelle au minimum.)| L’accès à la console série nécessite certaines autorisations. Pour plus d’informations, consultez [Prérequis](#prerequisites).
Unable to determine the resource group for the boot diagnostics storage account *&lt;STORAGEACCOUNTNAME&gt;*. Verify that boot diagnostics is enabled for this VM and you have access to this storage account (Impossible de déterminer le groupe de ressources pour le compte de stockage de diagnostic de démarrage. Vérifiez que les diagnostics de démarrage sont activés sur la machine virtuelle et que vous avez accès au compte de stockage) | L’accès à la console série nécessite certaines autorisations. Pour plus d’informations, consultez [Prérequis](#prerequisites).
Une réponse « Interdit » s’est produite lors de l’accès au compte de stockage des diagnostics de démarrage de cette machine virtuelle. | Assurez-vous que les diagnostics de démarrage n’ont pas un pare-feu de compte. Un compte de stockage des diagnostics de démarrage accessible est nécessaire au fonctionnement de la console série.
WebSocket est fermé ou n’a pas pu être ouvert. | Vous devrez peut-être autoriser `*.console.azure.com`. Une approche plus détaillée, mais plus longue, consiste à autoriser les [plages IP du centre de données Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653), qui changent régulièrement.
Seules les informations d’intégrité sont affichées lors de la connexion à une machine virtuelle Windows| Cette erreur se produit si la console d’administration spéciale n’a pas été activée pour votre image Windows. Pour obtenir des instructions sur la façon d’activer manuellement la console SAC sur votre machine virtuelle Windows, consultez [Activer la console série dans les images personnalisées ou anciennes](#enable-the-serial-console-in-custom-or-older-images). Pour plus d’informations, consultez [Signaux d’intégrité Windows](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Windows_Health_Info.md).

## <a name="known-issues"></a>Problèmes connus
Nous sommes conscients de certains problèmes avec la console série. Voici une liste de ces problèmes et la procédure d’atténuation associée.

Problème                             |   Atténuation
:---------------------------------|:--------------------------------------------|
L’utilisation de la touche **Entrée** après la bannière de connexion n’entraîne pas l’affichage d’une invite de connexion. | Pour plus d’informations, consultez [La touche Entrée n’a aucun effet](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Hitting_enter_does_nothing.md). Cette erreur peut se produire si vous exécutez une machine virtuelle personnalisée, une appliance à sécurité renforcée ou une configuration de démarrage qui empêche Windows de se connecter correctement au port série. Cette erreur se produit également si vous exécutez une machine virtuelle cliente Windows 10, car seules les machines virtuelles Windows Server sont configurées pour avoir EMS activé.
Impossible d’entrer des données à l’invite de commandes de la console SAC si le débogage du noyau est activé. | Établissez une connexion RDP vers la machine virtuelle et exécutez `bcdedit /debug {current} off` à partir d’une invite de commandes avec élévation de privilèges. Si vous ne pouvez pas établir de connexion RDP, vous pouvez joindre le disque du système d’exploitation à une autre machine virtuelle Azure et le modifier pendant qu’il est joint en tant que disque de données en exécutant `bcdedit /store <drive letter of data disk>:\boot\bcd /debug <identifier> off`, puis en rebasculant le disque.
Coller du contenu dans PowerShell sous la console SAC génère un troisième caractère si le contenu d’origine contenait un caractère répété. | Une solution de contournement consiste à exécuter `Remove-Module PSReadLine` pour décharger le module PSReadLine de la session active. Cette action ne supprime pas et ne désinstalle pas le module.
Certaines entrées de clavier produisent une sortie étrange de la console SAC (par exemple, **[A**, **[3~**). | Les séquences d’échappement [VT100](https://aka.ms/vtsequences) ne sont pas prises en charge par l’invite de la console SAC.
Le collage de chaînes longues ne fonctionne pas. | La console série limite la longueur des chaînes collées dans le terminal à 2 048 caractères afin d’empêcher toute surcharge de la bande passante du port série.


## <a name="frequently-asked-questions"></a>Questions fréquentes (FAQ)

**Q. Comment puis-je envoyer des commentaires ?**

R. Envoyez vos commentaires en créant un problème GitHub sur https://aka.ms/serialconsolefeedback. Vous pouvez également envoyer des commentaires par le biais de azserialhelp@microsoft.com ou dans la catégorie Machine virtuelle de la page http://feedback.azure.com.

**Q. Est-ce que la console série prend en charge l’opération Copier/Coller ?**

R. Oui. Utilisez les combinaisons de touches **Ctrl**+**Maj**+**C** et **Ctrl**+**Maj**+**V** pour copier et coller du texte dans le terminal.

**Q. Qui peut activer ou désactiver la console série pour mon abonnement ?**

R. Pour activer ou désactiver la console série au niveau d’un abonnement, vous devez disposer des autorisations en écriture sur l’abonnement. Les rôles ayant une autorisation en écriture incluent notamment les rôles administrateur et propriétaire. Des rôles personnalisés peuvent aussi disposer d’autorisations en écriture.

**Q. Qui peut accéder à la console série pour ma machine virtuelle ?**

R. Vous devez disposer du rôle Contributeur de machine virtuelle ou d’un rôle supérieur vis-à-vis d’une machine virtuelle pour accéder à la console série de la machine virtuelle.

**Q. Ma console série n’affiche rien. Que dois-je faire ?**

R. Votre image est probablement mal configurée pour l’accès à la console série. Pour plus d’informations sur la configuration de votre image afin d’activer la console série, consultez [Activer la console série dans les images personnalisées ou anciennes](#enable-the-serial-console-in-custom-or-older-images).

**Q. La console série est-elle disponible pour les groupes de machines virtuelles identiques ?**

R. À ce stade, l’accès à la console série n’est pas pris en charge pour les instances de groupe de machines virtuelles identiques.

## <a name="next-steps"></a>Étapes suivantes
* Pour obtenir des instructions détaillées sur les commandes CMD et PowerShell que vous pouvez utiliser dans la console SAC Windows, consultez la page [Commandes Windows : CMD et PowerShell](serial-console-cmd-ps-commands.md).
* La console série est également disponible pour les machines virtuelles [Linux](serial-console-linux.md).
* En savoir plus sur les [diagnostics de démarrage](boot-diagnostics.md).
