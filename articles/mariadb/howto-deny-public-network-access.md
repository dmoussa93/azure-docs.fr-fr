---
title: Refus d’accès réseau public – Portail Azure – Azure Database for MariaDB
description: Découvrez comment configurer et gérer le refus d’accès réseau public avec le Portail Azure pour Azure Database for MariaDB.
author: kummanish
ms.author: manishku
ms.service: mariadb
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 87b6033e486e9009573436628d0183c8a022aced
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79372767"
---
# <a name="deny-public-network-access-in-azure-database-for-mariadb-using-azure-portal"></a>Refus d’accès réseau public dans Azure Database for MariaDB avec le Portail Azure

Cet article explique comment configurer un serveur Azure Database for MariaDB de façon à refuser toutes les configurations publiques et à n’autoriser que les connexions établies via des points de terminaison privés afin d’améliorer la sécurité réseau.

## <a name="prerequisites"></a>Prérequis

Pour utiliser ce guide pratique, il vous faut :

* Une instance [Azure Database for MariaDB](quickstart-create-MariaDB-server-database-using-azure-portal.md)

## <a name="set-deny-public-network-access"></a>Définition du refus d’accès réseau public

Suivez les étapes ci-dessous pour définir le refus d’accès réseau public pour un serveur MariaDB :

1. Dans le [portail Azure](https://portal.azure.com/), sélectionnez votre serveur Azure Database for MariaDB existant.

1. Sur la page du serveur MariaDB, cliquez sur **Sécurité des connexions** sous **Paramètres** afin d’ouvrir la page de configuration de la sécurité des connexions.

1. Dans Refus d’accès réseau public, sélectionnez **Oui** afin d’activer le refus d’accès réseau public pour votre serveur MariaDB.

    ![Azure Database for MariaDB – Refus d’accès réseau](./media/howto-deny-public-network-access/deny-public-network-access.PNG)

1. Cliquez sur **Enregistrer** pour enregistrer les modifications.

1. Une notification confirme que le paramètre de sécurité des connexions a bien été activé.

    ![Azure Database for MariaDB – Refus d’accès réseau réussi](./media/howto-deny-public-network-access/deny-public-network-access-success.png)

## <a name="next-steps"></a>Étapes suivantes

Découvrez [comment créer des alertes sur des métriques](howto-alert-metric.md).