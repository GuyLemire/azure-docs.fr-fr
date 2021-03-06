---
title: Expression workspace() dans une requête Azure Log Analytics | Microsoft Docs
description: L’expression workspace est utilisée dans une requête Log Analytics dans le but de récupérer des données à partir d’un espace de travail spécifique du même groupe de ressources, d’un autre groupe de ressources ou d’un autre abonnement.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 09/10/2018
ms.author: bwren
ms.openlocfilehash: 24a737a728b0a249fda76cbff481bea284ac24aa
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/10/2018
ms.locfileid: "53182942"
---
# <a name="workspace-expression-in-log-analytics-query"></a>Expression workspace() dans une requête Log Analytics

L’expression `workspace` est utilisée dans une requête Log Analytics dans le but de récupérer des données à partir d’un espace de travail spécifique du même groupe de ressources, d’un autre groupe de ressources ou d’un autre abonnement. Elle est particulièrement utile pour inclure des données de journal dans une requête Application Insights et pour interroger des données de plusieurs espaces de travail dans une requête de journal.


## <a name="syntax"></a>Syntaxe

`workspace(`*Identificateur*`)`

## <a name="arguments"></a>Arguments

- *Identificateur* : permet d’identifier l’espace de travail à l’aide de l’un des formats du tableau ci-dessous.

| Identificateur | Description | Exemples
|:---|:---|:---|
| Nom de la ressource | Nom lisible de l’espace de travail (également appelé « nom du composant ») | workspace("contosoretail") |
| Nom qualifié | Nom complet de l’espace de travail au format : « nom_abonnement/groupe_ressources/nom_composant » | workspace('Contoso/ContosoResource/ContosoWorkspace') |
| ID | GUID de l’espace de travail | workspace("b438b3f6-912a-46d5-9db1-b42069242ab4") |
| ID de la ressource Azure | Identificateur de la ressource Azure | workspace("/subscriptions/e4227-645-44e-9c67-3b84b5982/resourcegroups/ContosoAzureHQ/providers/Microsoft.OperationalInsights/workspaces/contosoretail") |


## <a name="notes"></a>Notes

* Vous devez disposer d’un accès en lecture à l’espace de travail.
* L’expression `app` associée vous permet d’effectuer des requêtes sur les applications Application Insights.

## <a name="examples"></a>Exemples

```Kusto
workspace("contosoretail").Update | count
```
```Kusto
workspace("b438b4f6-912a-46d5-9cb1-b44069212ab4").Update | count
```
```Kusto
workspace("/subscriptions/e427267-5645-4c4e-9c67-3b84b59a6982/resourcegroups/ContosoAzureHQ/providers/Microsoft.OperationalInsights/workspaces/contosoretail").Event | count
```
```Kusto
union 
(workspace("myworkspace").Heartbeat | where Computer contains "Con"),
(app("myapplication").requests | where cloud_RoleInstance contains "Con")
| count  
```
```Kusto
union 
(workspace("myworkspace").Heartbeat), (app("myapplication").requests)
| where TimeGenerated between(todatetime("2018-02-08 15:00:00") .. todatetime("2018-12-08 15:05:00"))
```

## <a name="next-steps"></a>Étapes suivantes

- Consultez l’article relatif à l’[expression app](workspace-expression.md) pour en savoir plus sur l’application Application Insights.
- En savoir plus sur le stockage des [données Log Analytics](../../azure-monitor/log-query/log-query-overview.md)