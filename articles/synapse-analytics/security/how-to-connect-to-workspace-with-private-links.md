---
title: Se connecter à un espace de travail Azure Synapse à l’aide de liens privés
description: Cet article explique comment se connecter à un espace de travail Azure Synapse à l’aide de liens privés.
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: how-to
ms.date: 04/15/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: 5a00fc44021278a8b910cf454b43b0bae2c3a1f9
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81427741"
---
# <a name="connect-to-your-azure-synapse-workspace-using-private-links-preview"></a>Se connecter à un espace de travail Azure Synapse à l’aide de liens privés (préversion)

Cet article explique comment créer une point de terminaison privé pour votre espace de travail Azure Synapse. Pour en savoir plus, consultez [Liens privés et points de terminaison privés](https://docs.microsoft.com/azure/private-link/).

## <a name="step-1-open-your-azure-synapse-workspace-in-azure-portal"></a>Étape 1 : Ouvrez votre espace de travail Azure Synapse dans le portail Azure

Sous **Sécurité**, sélectionnez **Connexion de point de terminaison privé**, puis **+ Point de terminaison privé**.
![Ouvrir l’espace de travail Azure Synapse dans le portail Azure](./media/how-to-connect-to-workspace-with-private-links/private-endpoint-1.png)

## <a name="step-2-select-your-subscription-and-region-details"></a>Étape 2 : Sélectionner les détails de votre abonnement et de votre région

Dans la fenêtre **Créer un point de terminaison privé**, sous l’onglet **De base**, choisissez vos **Abonnement** et **Groupe de ressources**. Donnez un **Nom** au point de terminaison privé à créer. Sélectionnez la **Région** où créer le point de terminaison privé.

Les points de terminaison privés sont créés dans un sous-réseau. L’abonnement, le groupe de ressources et la région sélectionnés filtrent les sous-réseaux du point de terminaison privé. Sélectionnez **Suivant : Ressource >** quand vous avez terminé.
![Sélectionner les détails de l’abonnement et de la région](./media/how-to-connect-to-workspace-with-private-links/private-endpoint-2.png)

## <a name="step-3-select-your-azure-synapse-workspace-details"></a>Étape 3 : Sélectionner les détails de votre espace de travail Azure Synapse

Sous l’onglet **Ressource**, sélectionnez **Se connecter à une ressource Azure dans mon annuaire**. Sélectionnez l’**Abonnement** contenant votre espace de travail Azure Synapse. Le **Type de ressource** pour la création de points de terminaison privés dans un espace de travail Azure Synapse est *Microsoft.Synapse/Workspaces*.

Sélectionnez votre espace de travail Azure Synapse en tant que **Ressources**. Chaque espace de travail Azure Synapse comprend trois **Sous-ressource cibles** sur lesquelles vous pouvez créer un point de terminaison privé : Sql, SqlOnDemand et Dev.

Sélectionnez **Suivant : Configuration >** pour passer à la partie suivante de la configuration.
![Sélectionner les détails de l’abonnement et de la région](./media/how-to-connect-to-workspace-with-private-links/private-endpoint-3.png)

Sous l’onglet **Configuration**, sélectionnez le **Réseau virtuel** et le **Sous-réseau** dans lequel créer le point de terminaison privé. Vous devez également créer un enregistrement DNS qui mappe au point de terminaison privé.

Sélectionnez **Oui** pour **Intégrer avec une zone DNS privée** afin d’intégrer votre point de terminaison privé avec une zone DNS privée. Si vous n’avez pas de zone DNS privée associée à votre réseau virtuel, une nouvelle zone DNS privée est créée. Sélectionnez **Vérifier + créer** quand vous avez terminé.

![Sélectionner les détails de l’abonnement et de la région](./media/how-to-connect-to-workspace-with-private-links/private-endpoint-4.png)

Une fois le déploiement terminé, ouvrez votre espace de travail Azure Synapse dans le portail Azure et sélectionnez **Connexions des points de terminaison privés**. Le nouveau point de terminaison privé et le nom de connexion de point de terminaison privé associé au point de terminaison privé sont affichés.

![Sélectionner les détails de l’abonnement et de la région](./media/how-to-connect-to-workspace-with-private-links/private-endpoint-5.png)

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur le [Réseau virtuel d’espace de travail managé](./synapse-workspace-managed-vnet.md)

En savoir plus sur les [Points de terminaison privés managés](./synapse-workspace-managed-private-endpoints.md)

[Créer des points de terminaison privés managés à vos sources de données](./how-to-create-managed-private-endpoints.md)
