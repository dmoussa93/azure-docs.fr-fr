---
title: Locataires, rôles et utilisateurs dans les scénarios Azure Lighthouse
description: Découvrez les concepts d’Azure Active Directory pour les locataires, les utilisateurs et les rôles, ainsi que la façon dont ils peuvent être utilisés dans les scénarios Azure Lighthouse.
ms.date: 04/03/2020
ms.topic: conceptual
ms.openlocfilehash: 32d9214e4d0d204db39b6e6decab4665e9b55069
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80754086"
---
# <a name="tenants-roles-and-users-in-azure-lighthouse-scenarios"></a>Locataires, rôles et utilisateurs dans les scénarios Azure Lighthouse

Avant d’intégrer des clients pour la [gestion des ressources déléguées Azure](azure-delegated-resource-management.md), il est important de comprendre comment les locataires, les utilisateurs et les rôles fonctionnent dans Azure Active Directory (Azure AD), ainsi que la façon dont ils peuvent être utilisés dans les scénarios Azure Lighthouse.

Un *locataire* est une instance dédiée et approuvée d’Azure AD. En général, chaque locataire représente une seule organisation. La gestion des ressources déléguées Azure permet d’opérer une projection logique des ressources d’un locataire sur un autre. Cela permet aux utilisateurs du locataire de gestion (appartenant par exemple à un fournisseur de services) d’accéder à des ressources déléguées dans le locataire d’un client, ou permet aux [entreprises avec plusieurs locataires de centraliser leurs opérations de gestion](enterprise.md).

Pour atteindre cette projection logique, un abonnement (ou un ou plusieurs groupes de ressources au sein d’un abonnement) dans le locataire client doit être *intégré* pour la gestion des ressources déléguées Azure. Ce processus d’intégration peut être effectué [par le biais de modèles Azure Resource Manager](../how-to/onboard-customer.md) ou par [la publication d’une offre publique ou privée sur la Place de marché Azure](../how-to/publish-managed-services-offers.md).

Quelle que soit la méthode d’intégration choisie, vous devez définir des *autorisations*. Chaque autorisation spécifie un compte d’utilisateur dans le locataire gérant qui aura accès aux ressources déléguées, et un rôle intégré qui définit les autorisations dont chacun de ces utilisateurs aura besoin pour ces ressources.

## <a name="role-support-for-azure-delegated-resource-management"></a>Prise en charge des rôles pour la gestion des ressources déléguées Azure

Lors de la définition d’une autorisation, chaque compte utilisateur doit recevoir un des [rôles de contrôle d'accès en fonction du rôle intégrés](../../role-based-access-control/built-in-roles.md). Les rôles personnalisés et les [Rôles Administrateur classique de l’abonnement](../../role-based-access-control/classic-administrators.md) ne sont pas pris en charge.

Tous les [rôles intégrés](../../role-based-access-control/built-in-roles.md) sont actuellement pris en charge avec la gestion des ressources déléguées Azure, avec les exceptions suivantes :

- Le rôle [propriétaire](../../role-based-access-control/built-in-roles.md#owner) n’est pas pris en charge.
- Les rôles intégrés disposant d’une autorisation [DataActions](../../role-based-access-control/role-definitions.md#dataactions) ne sont pas pris en charge.
- Le rôle intégré [Administrateur de l’accès utilisateur](../../role-based-access-control/built-in-roles.md#user-access-administrator) est pris en charge, mais uniquement pour les besoins limités [d’affectation de rôles à une identité gérée dans le locataire client](../how-to/deploy-policy-remediation.md#create-a-user-who-can-assign-roles-to-a-managed-identity-in-the-customer-tenant). Aucune autre autorisation généralement accordée par ce rôle ne s’applique. Si vous définissez un utilisateur avec ce rôle, vous devez également spécifier le ou les rôles intégrés que cet utilisateur peut affecter aux identités gérées.

> [!NOTE]
> Une fois qu'un nouveau rôle intégré applicable a été ajouté à Azure, il peut être attribué lors de l'[intégration d'un client à l'aide des modèles Azure Resource Manager](../how-to/onboard-customer.md). Un certain temps peut s'écouler avant que le nouveau rôle ne soit disponible sur le Portail Cloud Partner lors de la [publication d'une offre de services gérés](../how-to/publish-managed-services-offers.md).

## <a name="best-practices-for-defining-users-and-roles"></a>Meilleures pratiques pour la définition des utilisateurs et des rôles

Lorsque vous créez vos autorisations, nous vous recommandons de suivre ces meilleures pratiques :

- Dans la plupart des cas, vous affectez des autorisations à un groupe d’utilisateurs ou à un principal de service Azure AD, plutôt qu’à une série de comptes d’utilisateur individuels. Cela vous permet d’ajouter ou de supprimer l’accès d’utilisateurs individuels sans devoir mettre à jour et republier le plan lorsque vos conditions d’accès changent.
- Veillez à suivre le principe du privilège minimum afin que les utilisateurs disposent uniquement des autorisations nécessaires pour accomplir leur travail, ce qui contribue à réduire le risque d’erreurs accidentelles. Pour plus d’informations, voir [Pratiques de sécurité recommandées](../concepts/recommended-security-practices.md).
- Incluez un utilisateur avec le [rôle Supprimer l’attribution de l’inscription des services gérés](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role) afin de pouvoir [supprimer l’accès à la délégation](../how-to/onboard-customer.md#remove-access-to-a-delegation) ultérieurement si nécessaire. Si ce rôle n’est pas attribué, les ressources déléguées ne peuvent être supprimées que par un utilisateur dans le locataire du client.
- Assurez-vous que tous les utilisateurs qui ont besoin [d’afficher la page Mes clients dans le portail Azure](../how-to/view-manage-customers.md) possèdent le rôle de [Lecteur](../../role-based-access-control/built-in-roles.md#reader) (ou un autre rôle intégré qui inclut l’accès Lecteur).

> [!IMPORTANT]
> Pour que vous puissiez ajouter des autorisations pour un groupe Azure AD, le **Type de groupe** doit être **Sécurité** et non **Office 365**. Cette option est sélectionnée lors de la création du groupe. Pour plus d’informations, consultez [Créer un groupe de base et ajouter des membres avec Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

## <a name="next-steps"></a>Étapes suivantes

- Consultez les [pratiques de sécurité recommandées pour la gestion des ressources déléguées Azure](recommended-security-practices.md).
- Intégrez vos clients à la gestion des ressources déléguées Azure [en utilisant des modèles Azure Resource Manager](../how-to/onboard-customer.md) ou [en publiant une offre de services managés privés ou publics sur la Place de marché Azure](../how-to/publish-managed-services-offers.md).
