---
title: Augmenter automatiquement le stockage - Portail Azure - Azure Database for MariaDB
description: Cet article explique comment activer l’augmentation automatique du stockage pour Azure Database for MariaDB à l’aide du portail Azure
author: ambhatna
ms.author: ambhatna
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 7ec4c7a61725fa02f00bf38048182d640666ae8f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79528895"
---
# <a name="auto-grow-storage-in-azure-database-for-mariadb-using-the-azure-portal"></a>Augmenter automatiquement le stockage dans Azure Database for MariaDB à l’aide du portail Azure
Cet article explique comment vous pouvez configurer l’augmentation d’un stockage de serveur Azure Database for MariaDB sans affecter la charge de travail.

Quand un serveur atteint la limite de stockage alloué, le serveur est marqué comme étant en lecture seule. Toutefois, si vous activez l’augmentation automatique du stockage, le stockage du serveur augmente pour prendre en charge le volume croissant de données. Pour les serveurs avec moins de 100 Go de stockage approvisionnés, la taille de stockage approvisionné augmente de 5 Go dès que l’espace de stockage libre est inférieur à 1 Go ou 10 % (selon la valeur la plus élevée) du stockage approvisionné. Pour les serveurs avec plus de 100 Go de stockage approvisionnés, la taille de stockage approvisionné augmente de 5 % lorsque l’espace de stockage libre est inférieur à 5 % de la taille de stockage approvisionné. Les limites de stockage maximales spécifiées [ici](https://docs.microsoft.com/azure/mariadb/concepts-pricing-tiers#storage) s’appliquent.

## <a name="prerequisites"></a>Conditions préalables requises
Pour utiliser ce guide pratique, il vous faut :
- Un [serveur Azure Database for MariaDB](./quickstart-create-mariadb-server-database-using-azure-portal.md)

## <a name="enable-storage-auto-grow"></a>Activer l’augmentation automatique du stockage 

Suivez ces étapes pour définir l’augmentation automatique du stockage de serveur MariaDB :

1. Dans le [portail Azure](https://portal.azure.com/), sélectionnez votre serveur Azure Database for MariaDB existant.

2. Dans la page de serveur MariaDB, sous le titre **Paramètres**, cliquez sur **Niveau tarifaire** pour ouvrir la page de niveau tarifaire.

3. Dans la section Augmentation automatique, sélectionnez **Oui** pour activer l’augmentation automatique du stockage.

    ![Azure Database for MariaDB - Settings_Pricing_tier - Augmentation automatique](./media/howto-auto-grow-storage-portal/3-auto-grow.png)

4. Cliquez sur **OK** pour enregistrer les modifications.

5. Une notification confirme que l’augmentation automatique a été correctement activée.

    ![Azure Database for MariaDB - Réussite de l’augmentation automatique](./media/howto-auto-grow-storage-portal/5-auto-grow-successful.png)

## <a name="next-steps"></a>Étapes suivantes

Découvrez [comment créer des alertes sur des métriques](howto-alert-metric.md).
