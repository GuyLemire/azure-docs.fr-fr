---
title: Créer un hub d’événement avec PowerShell - Azure Event Hubs | Microsoft Docs
description: Ce démarrage rapide décrit la création d’un Event Hub à l’aide de Azure PowerShell puis l’envoi et la réception des événements à l’aide du Kit de développement .NET Standard.
services: event-hubs
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: quickstart
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: d91f98f4f54c1b7c46b3390427c6c389ec01d3c9
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/08/2018
ms.locfileid: "53088168"
---
# <a name="quickstart-create-an-event-hub-using-azure-powershell"></a>Démarrage rapide : Créer un hub d’événements avec Azure PowerShell

Azure Event Hubs est une plateforme de diffusion de données volumineuses et un service d’ingestion d’événements, capable de recevoir et de traiter des millions d’événements par seconde. Les concentrateurs d’événements peuvent traiter et stocker des événements, des données ou la télémétrie produits par des logiciels et appareils distribués. Les données envoyées à un concentrateur d’événements peuvent être transformées et stockées à l’aide d’adaptateurs de traitement par lot/stockage ou d’un fournisseur d’analyse en temps réel. Pour une présentation détaillée d’Event Hubs, consultez [Vue d’ensemble d’Event Hubs](event-hubs-about.md) et [Fonctionnalités d’Event Hubs](event-hubs-features.md).

Dans ce démarrage rapide, vous créez un Event Hub à l’aide d’Azure PowerShell.

## <a name="prerequisites"></a>Prérequis

Pour suivre ce tutoriel, veillez à disposer des éléments suivants :

- Abonnement Azure. Si vous n’en avez pas, [créez un compte gratuit][] avant de commencer.
- [Visual Studio 2017 Update 3 (version 15.3, 26730.01)](https://www.visualstudio.com/vs) ou ultérieur.
- [Kit SDK .NET Standard](https://www.microsoft.com/net/download/windows), version 2.0 ou ultérieure.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Si vous utilisez PowerShell localement, vous devez exécuter la dernière version de PowerShell pour suivre ce guide de démarrage rapide. Si vous devez procéder à une installation ou une mise à niveau, consultez [Installation et configuration d’Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-5.7.0).

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Un groupe de ressources est une collection logique de ressources Azure, dont vous avez besoin pour créer un hub d’événements. 

L’exemple suivant crée un groupe de ressources dans la région USA Est. Remplacez `myResourceGroup` par le nom du groupe de ressources que vous souhaitez utiliser :

```azurepowershell-interactive
New-AzureRmResourceGroup –Name myResourceGroup –Location eastus
```

## <a name="create-an-event-hubs-namespace"></a>Créer un espace de noms Event Hubs

Une fois que vous avez créé votre groupe de ressources, créez un espace de noms Event Hubs au sein de ce groupe de ressources. Un espace de noms Event Hubs fournit un nom de domaine complet unique dans lequel vous pouvez créer votre hub d’événements. Remplacez `namespace_name` par un nom unique pour votre espace de noms :

```azurepowershell-interactive
New-AzureRmEventHubNamespace -ResourceGroupName myResourceGroup -NamespaceName namespace_name -Location eastus
```

## <a name="create-an-event-hub"></a>Créer un hub d’événements

Maintenant que vous avez un espace de noms Event Hubs, créez un hub d’événements au sein de cet espace de noms :  
La période autorisée pour `MessageRetentionInDays` est comprise entre un et sept jours.

```azurepowershell-interactive
New-AzureRmEventHub -ResourceGroupName myResourceGroup -NamespaceName namespace_name -EventHubName eventhub_name -MessageRetentionInDays 3
```

Félicitations ! Vous avez utilisé Azure PowerShell pour créer un espace de noms Event Hubs, ainsi qu’un Event Hub dans cet espace de noms. 

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez créé un espace de noms Event Hubs et utilisé des exemple d’application pour envoyer et recevoir des événements depuis votre Event Hub. Pour obtenir des instructions pas à pas sur l’envoi à ou la réception d’événements à partir d’un Event Hub, consultez les didacticiels suivants : 

- **Envoyer des événements à un hub d’événements** : [.NET Core](event-hubs-dotnet-standard-getstarted-send.md), [.NET Framework](event-hubs-dotnet-framework-getstarted-send.md), [Java](event-hubs-java-get-started-send.md), [Python](event-hubs-python-get-started-send.md), [Node.js](event-hubs-node-get-started-send.md), [Go](event-hubs-go-get-started-send.md), [C](event-hubs-c-getstarted-send.md)
- **Recevoir des événements d’un hub d’événements** : [.NET Core](event-hubs-dotnet-standard-getstarted-receive-eph.md), [.NET Framework](event-hubs-dotnet-framework-getstarted-receive-eph.md), [Java](event-hubs-java-get-started-receive-eph.md), [Python](event-hubs-python-get-started-receive.md), [Node.js](event-hubs-node-get-started-receive.md), [Go](event-hubs-go-get-started-receive-eph.md), [Apache Storm](event-hubs-storm-getstarted-receive.md)

[créez un compte gratuit]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
[Install and Configure Azure PowerShell]: https://docs.microsoft.com/powershell/azure/install-azurerm-ps
[New-AzureRmResourceGroup]: https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermresourcegroup
[fully qualified domain name]: https://wikipedia.org/wiki/Fully_qualified_domain_name
[3]: ./media/event-hubs-quickstart-powershell/sender1.png
[4]: ./media/event-hubs-quickstart-powershell/receiver1.png
[5]: ./media/event-hubs-quickstart-powershell/metrics.png
