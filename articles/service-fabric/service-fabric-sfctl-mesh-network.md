---
title: 'CLI Azure Service Fabric : sfctl mesh network | Microsoft Docs'
description: Décrit les commandes sfctl mesh network de l'interface de ligne de commande CLI Service Fabric.
services: service-fabric
documentationcenter: na
author: Christina-Kang
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 12/06/2018
ms.author: bikang
ms.openlocfilehash: 8828605241d55ac6f9ce789ccc55c30f2cd36434
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/12/2018
ms.locfileid: "53283495"
---
# <a name="sfctl-mesh-network"></a>sfctl mesh network
Récupère et supprime des ressources du réseau maillé.

## <a name="commands"></a>Commandes

|Commande|Description|
| --- | --- |
| delete | Supprime la ressource réseau. |
| list | Répertorie toutes les ressources réseau. |
| show | Récupère la ressource réseau portant le nom spécifié. |

## <a name="sfctl-mesh-network-delete"></a>sfctl mesh network delete
Supprime la ressource réseau.

Supprime la ressource réseau identifiée par le nom.

### <a name="arguments"></a>Arguments

|Argument|Description|
| --- | --- |
| --name -n [obligatoire] | Nom du réseau. |

### <a name="global-arguments"></a>Arguments globaux

|Argument|Description|
| --- | --- |
| --debug | Augmente le détail de la journalisation pour afficher tous les journaux de débogage. |
| --help -h | Affiche ce message d’aide et quitte. |
| --output -o | Format de sortie.  Valeurs autorisées \: json, jsonc, table, tsv.  Valeur par défaut \: json. |
| --query | Chaîne de requête JMESPath. Pour obtenir plus d’informations et des exemples, consultez : http\://jmespath.org/. |
| --verbose | Augmente le détail de la journalisation. Utilisez --debug pour les journaux de débogage complets. |

## <a name="sfctl-mesh-network-list"></a>sfctl mesh network list
Répertorie toutes les ressources réseau.

Récupère les informations relatives à toutes les ressources réseau d'un groupe de ressources donné. Les informations incluent la description et d'autres propriétés du réseau.

### <a name="global-arguments"></a>Arguments globaux

|Argument|Description|
| --- | --- |
| --debug | Augmente le détail de la journalisation pour afficher tous les journaux de débogage. |
| --help -h | Affiche ce message d’aide et quitte. |
| --output -o | Format de sortie.  Valeurs autorisées \: json, jsonc, table, tsv.  Valeur par défaut \: json. |
| --query | Chaîne de requête JMESPath. Pour obtenir plus d’informations et des exemples, consultez : http\://jmespath.org/. |
| --verbose | Augmente le détail de la journalisation. Utilisez --debug pour les journaux de débogage complets. |

## <a name="sfctl-mesh-network-show"></a>sfctl mesh network show
Récupère la ressource réseau portant le nom spécifié.

Récupère les informations relatives à la ressource réseau portant le nom spécifié. Les informations incluent la description et d'autres propriétés du réseau.

### <a name="arguments"></a>Arguments

|Argument|Description|
| --- | --- |
| --name -n [obligatoire] | Nom du réseau. |

### <a name="global-arguments"></a>Arguments globaux

|Argument|Description|
| --- | --- |
| --debug | Augmente le détail de la journalisation pour afficher tous les journaux de débogage. |
| --help -h | Affiche ce message d’aide et quitte. |
| --output -o | Format de sortie.  Valeurs autorisées \: json, jsonc, table, tsv.  Valeur par défaut \: json. |
| --query | Chaîne de requête JMESPath. Pour obtenir plus d’informations et des exemples, consultez : http\://jmespath.org/. |
| --verbose | Augmente le détail de la journalisation. Utilisez --debug pour les journaux de débogage complets. |


## <a name="next-steps"></a>Étapes suivantes
- [Configurez](service-fabric-cli.md) l’interface de ligne de commande Service Fabric.
- Découvrez comment utiliser l’interface de ligne de commande (CLI) Service Fabric à l’aide d’[exemples de scripts](/azure/service-fabric/scripts/sfctl-upgrade-application).