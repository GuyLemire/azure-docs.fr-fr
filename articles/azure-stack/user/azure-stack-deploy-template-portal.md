---
title: Déployer des modèles à l’aide du portail dans Azure Stack | Microsoft Docs
description: Apprenez à utiliser le portail Azure Stack pour déployer des modèles.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: eafa60f2-16c9-4ef1-b724-47709e9ea29e
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/05/2019
ms.author: sethm
ms.reviewer: unknown
ms.openlocfilehash: 4a8d4ab4d93831abbd9489d9023dd9b6f5c66d6d
ms.sourcegitcommit: f4b78e2c9962d3139a910a4d222d02cda1474440
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/12/2019
ms.locfileid: "54246889"
---
# <a name="deploy-templates-using-the-azure-stack-portal"></a>Déployer des modèles à l’aide du portail Azure Stack

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

Vous pouvez utiliser le portail pour déployer des modèles Azure Resource Manager dans Azure Stack.

## <a name="to-deploy-a-template"></a>Pour déployer un modèle

1. Connectez-vous au portail, sélectionnez **+ Créer une ressource**, puis sélectionnez **Personnalisé**.
2. Sélectionnez **Déploiement de modèle**.
3. Sélectionnez **Modifier un modèle**, puis collez le code de votre modèle JSON dans la fenêtre de code. Sélectionnez **Enregistrer**.
4. Sélectionnez **Modifier les paramètres**, indiquez des valeurs pour les paramètres affichés, puis sélectionnez **OK**.
5. Sélectionnez **Abonnement**. Choisissez l’abonnement que vous voulez utiliser, puis sélectionnez **OK**.
6. Sélectionnez **Groupe de ressources**. Choisissez un groupe de ressources existant ou créez-en un, puis sélectionnez **OK**.
7. Sélectionnez **Créer**. Le tableau de bord contient maintenant une nouvelle vignette qui suit la progression du déploiement de votre modèle.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur le déploiement de modèles, consultez l’article suivant :

- [Déployer des modèles avec PowerShell](azure-stack-deploy-template-powershell.md)
