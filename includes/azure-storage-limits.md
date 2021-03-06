---
title: Fichier Include
description: Fichier Include
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/23/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 84333b26ac70db4b400f7236d4255f4b57a6ca7d
ms.sourcegitcommit: 6b7c8b44361e87d18dba8af2da306666c41b9396
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/12/2018
ms.locfileid: "51572117"
---
Le tableau suivant décrit les limites par défaut du Stockage Azure. La limite *d’entrée* désigne toutes les données (demandes) envoyées à un compte de stockage. La limite de *sortie* désigne toutes les données (réponses) reçues d’un compte de stockage.

| Ressource | Limite par défaut |
| --- | --- |
| Nombre de comptes de stockage par région et par abonnement, y compris les comptes standard et premium | 250 |
| Capacité maximale du compte de stockage | 2 Po pour les États-Unis et l'Europe, 500 To pour toutes les autres régions, y compris le Royaume-Uni |
| Nombre maximal de conteneurs d'objets blob, de partages de fichiers, de tables, de files d'attente, d'entités ou de messages par compte de stockage | Aucune limite |
| Taux de demande maximal<sup>1</sup> par compte de stockage | 20 000 demandes par seconde |
| Entrée max.<sup>1</sup> par compte de stockage (régions des États-Unis) | 10 Gbit/s si RA-GRS/GRS est activé, 20 Gbit/s pour LRS/ZRS<sup>2</sup> |
| Max. d’entrées<sup>1</sup> par compte de stockage (régions hors États-Unis) | 5 Gbit/s si RA-GRS/GRS est activé, 10 Gbit/s pour LRS/ZRS<sup>2</sup> |
| Nombre maximal de sorties pour les comptes de stockage à usage général v2 et Blob (toutes les régions) | 50 Gbit/s |
| Nombre maximal de sorties pour les comptes de stockage à usage général v1 (régions des États-Unis) | 20 Gbit/s si RA-GRS/GRS est activé, 30 Gbit/s pour LRS/ZRS <sup>2</sup> |
| Nombre maximal de sorties pour les comptes de stockage à usage général v1 (régions hors États-Unis) | 10 Gbit/s si RA-GRS/GRS est activé, 15 Gbit/s pour LRS/ZRS <sup>2</sup> |

<sup>1</sup> Les comptes de stockage Azure prennent en charge des limites plus élevées pour la sortie par demande. Pour demander une augmentation des limites de compte pour la sortie, contactez le [support Azure](https://azure.microsoft.com/support/faq/).

<sup>2</sup>Les options de [réplication du Stockage Azure](https://docs.microsoft.com/azure/storage/common/storage-redundancy) sont notamment :
* **RA-GRS**: stockage géo-redondant avec accès en lecture. Si RA-GRS est activé, les cibles de sortie pour l’emplacement secondaire sont identiques à celles de l’emplacement principal.
* **GRS**: stockage géo-redondant. 
* **ZRS**: stockage redondant interzone.
* **LRS**: stockage localement redondant. 

Si les besoins de votre application dépassent les objectifs d’extensibilité d’un compte de stockage unique, vous pouvez concevoir votre application afin qu’elle utilise plusieurs comptes de stockage. Ensuite, vous pouvez partitionner vos objets de données sur ces comptes de stockage. Pour plus d’informations sur la tarification des licences en volume, consultez la page [Prix appliqués à Azure Storage](https://azure.microsoft.com/pricing/details/storage/) .

Tous les comptes de stockage s’exécutent sur la topologie de réseau plat et prennent en charge les objectifs d'extensibilité et de performances décrits dans cet article, quel que soit le moment où ils ont été créés. Pour plus d'informations sur l'architecture de réseau plat Azure Storage et sur son extensibilité, consultez le billet de blog [Microsoft Azure Storage: A Highly Available Cloud Storage Service with Strong Consistency](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx).

