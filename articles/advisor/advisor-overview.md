---
title: Présentation d’Azure Advisor
description: Le conseiller Azure permet d’optimiser vos déploiements Azure.
ms.topic: article
ms.date: 02/01/2019
ms.openlocfilehash: 600bda282d46f86979d0366719826c3a6c1323e0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75443098"
---
# <a name="introduction-to-azure-advisor"></a>Présentation d’Azure Advisor

Découvrez les principales fonctionnalités d’Azure Advisor et obtenez des réponses aux questions fréquemment posées.

## <a name="what-is-advisor"></a>Qu’est-ce qu’Advisor ?
Advisor est un conseiller personnalisé basé dans le cloud qui décrit les meilleures pratiques à suivre pour optimiser vos déploiements Azure. Il analyse votre télémétrie de configuration et d’utilisation des ressources, puis recommande des solutions qui peuvent vous aider à améliorer la rentabilité, les performances, la haute disponibilité et la sécurité de vos ressources Azure.

Avec Advisor, vous pouvez :
* Bénéficier de recommandations en termes de meilleures pratiques proactives, interactives et personnalisées. 
* Améliorer les performances, la sécurité et la haute disponibilité de vos ressources en identifiant les possibilités de réduction de vos dépenses Azure globales.
* Obtenir des recommandations avec des propositions d’actions intégrées.

Accéder à Advisor via le [portail Azure](https://aka.ms/azureadvisordashboard). Connectez-vous au [portail](https://portal.azure.com), puis recherchez **Advisor** dans le menu de navigation ou dans le menu **Tous les services**.

Le tableau de bord Advisor affiche des recommandations personnalisées pour l’ensemble de vos abonnements.  Vous pouvez appliquer des filtres pour afficher les recommandations pour des abonnements et des types de ressource spécifiques.  Les recommandations sont divisées en quatre catégories : 

* **Haute disponibilité** : vous aide à garantir et à améliorer la continuité de vos applications stratégiques. Pour plus d’informations, consultez [Recommandations du conseiller en matière de haute disponibilité](advisor-high-availability-recommendations.md).
* **Sécurité** : permet de détecter les menaces et vulnérabilités pouvant conduire à des failles de sécurité. Pour plus d’informations, consultez [Recommandations du conseiller en matière de sécurité](advisor-security-recommendations.md).
* **Performances** : pour améliorer la vitesse de vos applications. Pour plus d’informations, consultez [Recommandations du conseiller en matière de performances](advisor-performance-recommendations.md).
* **Coût** : pour optimiser et réduire vos dépenses Azure globales. Pour plus d’informations, consultez [Recommandations du conseiller en matière de coût](advisor-cost-recommendations.md).
* **Excellence opérationnelle :** à des fins d'efficacité des processus et des workflows, de gestion des ressources et de déploiement. . Pour plus d'informations, consultez [Recommandations d’excellence opérationnelle Advisor](advisor-operational-excellence-recommendations.md).

  ![Types de recommandation du conseiller](./media/advisor-overview/advisor-dashboard.png)

Vous pouvez cliquer sur une catégorie pour afficher la liste des recommandations au sein de cette catégorie, puis sélectionner une recommandation pour en savoir plus sur celle-ci.  Vous pouvez également obtenir des informations sur les actions que vous pouvez effectuer pour tirer parti d’une opportunité ou résoudre un problème.

![Catégorie de recommandation d’Advisor](./media/advisor-overview/advisor-ha-category-example.png) 

Sélectionnez l’action recommandée pour une recommandation afin d’implémenter la recommandation.  Une interface simple s’ouvre. Elle vous permet d’implémenter la recommandation ou de vous reporter à la documentation correspondante.  Une fois que vous avez implémenté une recommandation, une journée peut s’avérer nécessaire pour qu’Advisor la reconnaisse.

Si vous ne prévoyez pas d’effectuer d’action immédiate sur une recommandation, vous pouvez la reporter pendant une période spécifique ou la masquer.  Si vous ne souhaitez pas recevoir de recommandations pour un abonnement ou un groupe de ressources spécifiques, vous pouvez configurer Advisor pour générer uniquement des recommandations pour les abonnements et groupes de ressources spécifiés.

## <a name="frequently-asked-questions"></a>Forum aux questions

### <a name="how-do-i-access-advisor"></a>Accès au conseiller
Accéder à Advisor via le [portail Azure](https://aka.ms/azureadvisordashboard). Connectez-vous au [portail](https://portal.azure.com), puis recherchez **Advisor** dans le menu de navigation ou dans le menu **Tous les services**.

Vous pouvez également afficher les recommandations Advisor via l’interface des ressources de machine virtuelle. Choisissez une machine virtuelle, puis accédez aux recommandations du conseiller dans le menu. 

### <a name="what-permissions-do-i-need-to-access-advisor"></a>Quelles autorisations dois-je avoir pour accéder au conseiller ?
 
Vous pouvez accéder aux recommandations d’Advisor en tant que *propriétaire*, *collaborateur* ou *lecteur* d’un abonnement.

### <a name="what-resources-does-advisor-provide-recommendations-for"></a>Pour quelles ressources le conseiller fournit-il des recommandations ?

Advisor fournit des recommandations pour Application Gateway, App Services, les groupes à haute disponibilité, le Cache Azure, Azure Data Factory, Azure Database pour MySQL, Azure Database pour PostgreSQL, Azure Database pour MariaDB, Azure ExpressRoute, Azure Cosmos DB, les adresses IP publiques Azure, SQL Data Warehouse, les serveurs SQL, les comptes de stockage, les profils Traffic Manager et les machines virtuelles.

Azure Advisor inclut également vos recommandations de [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-recommendations), pouvant inclure des recommandations pour des types de ressources supplémentaires.

### <a name="can-i-postpone-or-dismiss-a-recommendation"></a>Puis-je reporter ou ignorer une recommandation ?

Pour reporter ou ignorer une recommandation, cliquez sur le lien **Reporter**. Vous pouvez spécifier une période de report ou sélectionner **Jamais** pour faire disparaître la recommandation.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur les recommandations d’Advisor, consultez les ressources suivantes :

* [Prise en main d’Advisor](advisor-get-started.md)
* [Recommandations du conseiller en matière de haute disponibilité](advisor-high-availability-recommendations.md)
* [Recommandations du conseiller en matière de sécurité](advisor-security-recommendations.md)
* [Recommandations du conseiller en matière de performances](advisor-performance-recommendations.md)
* [Recommandations du conseiller en matière de coûts](advisor-cost-recommendations.md)
