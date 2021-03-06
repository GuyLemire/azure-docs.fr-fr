---
title: Configurer des alertes sur l’intégrité du service Azure avec OpsGenie | Microsoft Docs
description: Obtenir des notifications personnalisées sur les événements d’intégrité du service sur votre instance OpsGenie.
author: shawntabrizi
manager: scotthit
editor: ''
services: service-health
documentationcenter: service-health
ms.assetid: ''
ms.service: service-health
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/14/2017
ms.author: shtabriz
ms.openlocfilehash: e406c80e408ddcf4d1d53f4e49925bded43b650f
ms.sourcegitcommit: 7cd706612a2712e4dd11e8ca8d172e81d561e1db
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/18/2018
ms.locfileid: "53579479"
---
# <a name="configure-service-health-alerts-with-opsgenie"></a>Configurer des alertes sur l’intégrité de service avec OpsGenie

Cet article vous explique comment configurer les alertes sur l’intégrité du service Azure avec OpsGenie à l’aide d’un Webhook. L’intégration de l’intégrité du service Azure d’[OpsGenie](https://www.opsgenie.com/) permet de transférer les alertes d’intégrité du service Azure à OpsGenie. OpsGenie peut déterminer les personnes à notifier selon des planifications d’appel, via e-mail, SMS, appel téléphonique, notification Push iOS et Android et en remontant les alertes jusqu’à leur prise en compte ou leur clôture.

## <a name="creating-a-service-health-integration-url-in-opsgenie"></a>Création d’une URL d’intégration de l’intégrité du service dans OpsGenie
1.  Vérifiez que vous êtes inscrit et connecté à votre compte [OpsGenie](https://www.opsgenie.com/).

1.  Accédez à la section **Intégrations** d’OpsGenie.

    ![Section Intégration dans OpsGenie](./media/webhook-alerts/opsgenie-integrations-section.png)

1.  Cliquez sur le bouton d’intégration de **l’intégrité du Service Azure**.

    ![Bouton Intégrité du service Azure dans OpsGenie](./media/webhook-alerts/opsgenie-azureservicehealth-button.png)

1.  **Nommez** votre alerte et remplissez le champ **Assigné à l’équipe** .

1.  Remplissez les autres champs, tels que **Destinataires**, **Activé**, et **Supprimer les notifications**.

1.  Copiez et enregistrez l’**URL d’intégration**, qui doit déjà contenir votre `apiKey` ajouté à la fin.

    ![URL d’intégration dans OpsGenie](./media/webhook-alerts/opsgenie-integration-url.png)

1.  Sélectionnez **Enregistrer l’intégration**

## <a name="create-an-alert-using-opsgenie-in-the-azure-portal"></a>Créer une alerte à l’aide d’OpsGenie dans le portail Azure
### <a name="for-a-new-action-group"></a>Pour un nouveau groupe d’action :
1. Suivez les étapes 1 à 8 de [Créer une alerte sur une notification sur l’intégrité du service pour un nouveau groupe d’actions à l’aide du portail Azure](../azure-monitor/platform/alerts-activity-log-service-notifications.md).

1. À définir dans la liste des **Actions** :

    a. **Type d’action :** *Webhook*

    b. **Détails :** **URL d’intégration** OpsGenie précédemment enregistrée.

    c. **Nom :** nom, alias ou identificateur du webhook.

1. Quand vous avez terminé, sélectionnez **Enregistrer** pour créer l’alerte.

### <a name="for-an-existing-action-group"></a>Pour un groupe d’actions existant :
1. Dans le [portail Azure](https://portal.azure.com/), sélectionnez **Moniteur**.

1. Dans la section **Paramètres**, sélectionnez **Groupes d’actions**.

1. Recherchez et sélectionnez le groupe d’actions que vous souhaitez modifier.

1. À ajouter à la liste des **Actions** :

    a. **Type d’action :** *Webhook*

    b. **Détails :** **URL d’intégration** OpsGenie précédemment enregistrée.

    c. **Nom :** nom, alias ou identificateur du webhook.

1. Quand vous avez terminé, sélectionnez **Enregistrer** pour mettre à jour le groupe d’actions.

## <a name="testing-your-webhook-integration-via-an-http-post-request"></a>Tester l’intégration à Webhook via une demande HTTP POST
1. Créez la charge utile d’intégrité du service que vous souhaitez envoyer. Vous trouverez un exemple de charge utile du Webhook d’intégrité du service dans la page [Webhook pour des alertes du journal d’activité Azure](../azure-monitor/platform/activity-log-alerts-webhook.md).

1. Créez une requête HTTP POST comme suit :

    ```
    POST        https://api.opsgenie.com/v1/json/azureservicehealth?apiKey=<APIKEY>

    HEADERS     Content-Type: application/json

    BODY        <service health payload>
    ```
1. Vous devez recevoir une réponse `200 OK` avec le message indiquant que l’opération a réussi.

1. Accédez à [OpsGenie](https://www.opsgenie.com/) pour confirmer que votre intégration a été définie avec succès.

## <a name="next-steps"></a>Étapes suivantes
- Découvrez comment [configurer des notifications de Webhook pour les systèmes de gestion de problème existants](service-health-alert-webhook-guide.md).
- Consultez le [schéma webhook des alertes de journal d’activité](../azure-monitor/platform/activity-log-alerts-webhook.md). 
- En savoir plus sur les [notifications sur l’intégrité du service](../azure-monitor/platform/service-notifications.md).
- En savoir plus sur les [groupes d’actions](../azure-monitor/platform/action-groups.md).