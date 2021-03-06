---
title: Intégrer un client dans la gestion des ressources déléguées Azure
description: Découvrez comment intégrer un client à la gestion des ressources déléguées Azure, permettant ainsi que ses ressources soient accessibles et gérables via votre propre locataire.
ms.date: 03/24/2020
ms.topic: conceptual
ms.openlocfilehash: 9eef2e73de7812fc65104131dfd319bf8dfb014e
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/09/2020
ms.locfileid: "80984587"
---
# <a name="onboard-a-customer-to-azure-delegated-resource-management"></a>Intégrer un client dans la gestion des ressources déléguées Azure

Cet article explique comment, en tant que fournisseur de services, vous pouvez intégrer un client à la gestion des ressources déléguées Azure, permettant ainsi que ses ressources déléguées (abonnements et groupes de ressources) soient accessibles et gérables via votre propre locataire Azure Active Directory (Azure AD). Si nous faisons référence ici aux fournisseurs de services et aux clients, des [entreprises gérant plusieurs locataires](../concepts/enterprise.md) peuvent suivre le même processus pour consolider leur expérience de gestion.

Vous pouvez répéter ce processus si vous gérez des ressources pour plusieurs clients. Ensuite, quand un utilisateur autorisé se connecte à votre locataire, il peut être autorisé à accéder aux étendues de location des clients pour effectuer des opérations de gestion sans avoir à se connecter individuellement à chaque locataire du client.

Pour suivre votre impact sur les engagements client et recevoir une reconnaissance, associez votre ID Microsoft Partner Network (MPN) à au moins un compte d’utilisateur qui a accès à tous vos abonnements intégrés. Notez que vous devez effectuer cette association dans le locataire de votre fournisseur de services. Par souci de simplicité, nous vous recommandons de créer un compte de principal du service dans votre locataire qui est associé à votre ID MPN et de lui accorder un accès Lecteur pour chaque client que vous intégrez. Pour plus d’informations, consultez [Lier un ID partenaire à vos comptes Azure](../../billing/billing-partner-admin-link-started.md). 

> [!NOTE]
> Les clients peuvent également être intégrés quand ils achètent une offre de services managés (publics ou privés) que vous avez publiée sur la Place de marché Azure. Pour plus d’informations, voir [Publier des offres de services managés sur la Place de marché Azure](publish-managed-services-offers.md). Vous pouvez également utiliser le processus d’intégration décrit ici en même temps qu’une offre publiée sur la Place de marché Azure.

Le processus d’intégration requiert d’exécuter des actions tant dans le locataire du fournisseur de services que dans le locataire du client. Toutes ces étapes sont décrites dans cet article.

## <a name="gather-tenant-and-subscription-details"></a>Collecter les détails du locataire et de l’abonnement

Pour qu’il soit possible d’intégrer le locataire d’un client, celui-ci doit disposer d’un abonnement Azure actif. Vous devez disposer des informations suivantes :

- L’ID de locataire du locataire du fournisseur de services (où vous allez gérer les ressources du client)
- L’ID de locataire du locataire du client (dont les ressources seront gérées par le fournisseur de services)
- Les ID d’abonnement de chaque abonnement dans le locataire du client qui sera géré par le fournisseur de services (ou qui contient les groupes de ressources qui seront gérés par le fournisseur de services).

> [!NOTE]
> Même si vous souhaitez uniquement intégrer un ou plusieurs groupes de ressources au sein d’un abonnement, le déploiement doit être effectué au niveau de l’abonnement. Vous aurez donc besoin de l’ID d’abonnement.

Si vous ne disposez pas de ces valeurs d’ID, vous pouvez les récupérer de l’une des manières suivantes. Veillez à utiliser ces valeurs exactes dans votre déploiement.

### <a name="azure-portal"></a>Portail Azure

Vous pouvez afficher votre ID de locataire en pointant sur le nom de votre compte dans l’angle supérieur droit du portail Azure, ou en sélectionnant **Changer de répertoire**. Pour sélectionner et copier votre ID de locataire, recherchez « Azure Active Directory » dans le portail, puis sélectionnez **Propriétés** et copiez la valeur affichée dans le champ **ID de répertoire**. Pour trouver l’ID d’un abonnement dans un locataire du client, recherchez « Abonnements », puis sélectionnez l’ID d’abonnement approprié.

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

Select-AzSubscription <subscriptionId>
```

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

az account set --subscription <subscriptionId/name>
az account show
```

> [!NOTE]
> Lors de l’intégration d’un abonnement (ou d’un ou de plusieurs groupes de ressources au sein d’un abonnement) à l’aide du processus décrit ici, le fournisseur de ressources **Microsoft.ManagedServices** sera inscrit pour cet abonnement.

## <a name="define-roles-and-permissions"></a>Définir des rôles et des autorisations

En tant que fournisseur de services, vous souhaitez peut-être effectuer plusieurs tâches pour un seul client, ce qui requiert un accès différent pour les différentes étendues. Vous pouvez définir autant d’autorisations que nécessaire pour affecter des [rôles intégrés RBAC (contrôle d’accès basé sur un rôle](../../role-based-access-control/built-in-roles.md) aux utilisateurs de votre locataire.

Pour faciliter la gestion, nous vous recommandons d’utiliser des groupes d’utilisateurs Azure AD pour chaque rôle, ce qui vous permet d’ajouter ou de supprimer des utilisateurs dans le groupe au lieu d’attribuer directement des autorisations aux utilisateurs. Vous pouvez également attribuer des rôles à un principal de service. Veillez à suivre le principe du privilège minimum afin que les utilisateurs disposent uniquement des autorisations nécessaires pour accomplir leur travail. Pour plus de recommandations et d’informations sur les rôles pris en charge, consultez [Locataires, utilisateurs et rôles dans les scénarios Azure Lighthouse](../concepts/tenants-users-roles.md).

> [!IMPORTANT]
> Pour que vous puissiez ajouter des autorisations pour un groupe Azure AD, le **Type de groupe** doit être **Sécurité** et non **Office 365**. Cette option est sélectionnée lors de la création du groupe. Pour plus d’informations, consultez [Créer un groupe de base et ajouter des membres avec Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

Pour définir des autorisations, vous devez connaître les valeurs d’ID pour chaque utilisateur, groupe d’utilisateurs ou principal de service dans le locataire du fournisseur de services auquel vous souhaitez accorder l’accès. Vous aurez également besoin de l’ID de définition de rôle pour chaque rôle intégré que vous souhaitez attribuer. Si vous ne les avez pas déjà, vous pouvez les récupérer en exécutant les commandes ci-dessous à partir du locataire du fournisseur de services.

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

# To retrieve the objectId for an Azure AD group
(Get-AzADGroup -DisplayName '<yourGroupName>').id

# To retrieve the objectId for an Azure AD user
(Get-AzADUser -UserPrincipalName '<yourUPN>').id

# To retrieve the objectId for an SPN
(Get-AzADApplication -DisplayName '<appDisplayName>').objectId

# To retrieve role definition IDs
(Get-AzRoleDefinition -Name '<roleName>').id
```

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

# To retrieve the objectId for an Azure AD group
az ad group list --query "[?displayName == '<yourGroupName>'].objectId" --output tsv

# To retrieve the objectId for an Azure AD user
az ad user show --id "<yourUPN>" --query "objectId" --output tsv

# To retrieve the objectId for an SPN
az ad sp list --query "[?displayName == '<spDisplayName>'].objectId" --output tsv

# To retrieve role definition IDs
az role definition list --name "<roleName>" | grep name
```
> [!TIP]
> Nous vous recommandons d’attribuer le [rôle de suppression de l’affectation d’inscription des services managés](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role) lors de l’intégration d’un client, afin que les utilisateurs de votre locataire puissent [supprimer l’accès à la délégation](#remove-access-to-a-delegation) ultérieurement si nécessaire. Si ce rôle n’est pas attribué, les ressources déléguées ne peuvent être supprimées que par un utilisateur dans le locataire du client.

## <a name="create-an-azure-resource-manager-template"></a>Créer un modèle Azure Resource Manager

Pour intégrer votre client, vous devez créer un modèle [Azure Resource Manager](../../azure-resource-manager/index.yml) pour votre offre et y inclure les éléments suivants : Le client pourra voir les valeurs **mspOfferName** et **mspOfferDescription** quand il consultera les détails de l’offre dans la page [Fournisseurs de services](view-manage-service-providers.md) du Portail Azure.

|Champ  |Définition  |
|---------|---------|
|**mspOfferName**     |Nom décrivant cette définition. Cette valeur est présentée au client comme titre de l’offre.         |
|**mspOfferDescription**     |Brève description de votre offre (par exemple, « Offre de gestion des machines virtuelles Contoso »).      |
|**managedByTenantId**     |Votre ID de client.          |
|**authorizations**     |Valeurs **principalId** pour les utilisateurs/groupes/noms de principal du service de votre locataire, chacune avec un **principalIdDisplayName** pour aider votre client à comprendre l’objectif de l’autorisation, et mappées à une valeur **roleDefinitionId** intégrée pour spécifier le niveau d’accès.      |

Le processus d’intégration nécessite un modèle Azure Resource Manager (fourni dans nos [exemples de référentiel](https://github.com/Azure/Azure-Lighthouse-samples/)) et un fichier de paramètres correspondant que vous modifiez pour correspondre à votre configuration et pour définir vos autorisations.

Les modèles que vous choisissez dépendront de votre choix d’intégrer un abonnement entier, un groupe de ressources ou plusieurs groupes de ressources au sein d’un abonnement. Nous fournissons également un modèle qui peut être utilisé pour des clients qui ont acheté une offre de service managé que vous avez publiée sur la Place de marché Azure, si vous préférez intégrer leurs abonnements de cette façon.

|Pour intégrer ceci  |Utiliser ce modèle Azure Resource Manager  |Et modifier ce fichier de paramètres |
|---------|---------|---------|
|Abonnement   |[delegatedResourceManagement.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/delegated-resource-management/delegatedResourceManagement.json)  |[delegatedResourceManagement.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/delegated-resource-management/delegatedResourceManagement.parameters.json)    |
|Resource group   |[rgDelegatedResourceManagement.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/rg-delegated-resource-management/rgDelegatedResourceManagement.json)  |[rgDelegatedResourceManagement.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/rg-delegated-resource-management/rgDelegatedResourceManagement.parameters.json)    |
|Plusieurs groupes de ressources au sein d’un abonnement   |[multipleRgDelegatedResourceManagement.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/rg-delegated-resource-management/multipleRgDelegatedResourceManagement.json)  |[multipleRgDelegatedResourceManagement.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/rg-delegated-resource-management/multipleRgDelegatedResourceManagement.parameters.json)    |
|Abonnement (lors de l’utilisation d’une offre publiée sur la Place de marché Azure)   |[marketplaceDelegatedResourceManagement.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/marketplace-delegated-resource-management/marketplaceDelegatedResourceManagement.json)  |[marketplaceDelegatedResourceManagement.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/marketplace-delegated-resource-management/marketplaceDelegatedResourceManagement.parameters.json)    |

> [!IMPORTANT]
> Le processus décrit ici nécessite un déploiement au niveau de l’abonnement distinct pour chaque abonnement intégré, même si vous intégrez des abonnements dans le même locataire du client. Des déploiements distincts sont également requis si vous intégrez plusieurs groupes de ressources dans différents abonnements dans le même locataire du client. Toutefois, l’intégration de plusieurs groupes de ressources au sein d’un même abonnement peut être effectuée dans un seul déploiement au niveau de l’abonnement.
>
> De plus, vous devez déployer séparément chacune des offres qui sont appliquées à un même abonnement (ou aux groupes de ressources d’un abonnement). Chaque offre appliquée doit utiliser un **mspOfferName** différent.

L’exemple suivant montre un fichier **delegatedResourceManagement.parameters.json** modifié qui peut être utilisé pour l’intégration d’un abonnement. Les fichiers de paramètres de groupe de ressources (situés dans le dossier [rg-delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/rg-delegated-resource-management)) sont similaires, mais incluent en outre un paramètre **rgName** pour identifier les groupes de ressources à intégrer.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "mspOfferName": {
            "value": "Fabrikam Managed Services - Interstellar"
        },
        "mspOfferDescription": {
            "value": "Fabrikam Managed Services - Interstellar"
        },
        "managedByTenantId": {
            "value": "df4602a3-920c-435f-98c4-49ff031b9ef6"
        },
        "authorizations": {
            "value": [
                {
                    "principalId": "0019bcfb-6d35-48c1-a491-a701cf73b419",
                    "principalIdDisplayName": "Tier 1 Support",
                    "roleDefinitionId": "b24988ac-6180-42a0-ab88-20f7382dd24c"
                },
                {
                    "principalId": "0019bcfb-6d35-48c1-a491-a701cf73b419",
                    "principalIdDisplayName": "Tier 1 Support",
                    "roleDefinitionId": "36243c78-bf99-498c-9df9-86d9f8d28608"
                },
                {
                    "principalId": "0afd8497-7bff-4873-a7ff-b19a6b7b332c",
                    "principalIdDisplayName": "Tier 2 Support",
                    "roleDefinitionId": "acdd72a7-3385-48ef-bd42-f606fba81ae7"
                },
                {
                    "principalId": "9fe47fff-5655-4779-b726-2cf02b07c7c7",
                    "principalIdDisplayName": "Service Automation Account",
                    "roleDefinitionId": "b24988ac-6180-42a0-ab88-20f7382dd24c"
                },
                {
                    "principalId": "3kl47fff-5655-4779-b726-2cf02b05c7c4",
                    "principalIdDisplayName": "Policy Automation Account",
                    "roleDefinitionId": "18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
                    "delegatedRoleDefinitionIds": [
                        "b24988ac-6180-42a0-ab88-20f7382dd24c",
                        "92aaf0da-9dab-42b6-94a3-d43ce8d16293"
                    ]
                }
            ]
        }
    }
}
```

La dernière autorisation dans l’exemple ci-dessus ajoute un **principalId** avec le rôle Administrateur de l’accès utilisateur (18d7d88d-d35e-4fb5-A5C3-7773c20a72d9). Lorsque vous attribuez ce rôle, vous devez inclure la propriété **delegatedRoleDefinitionIds** et un ou plusieurs rôles intégrés. L’utilisateur créé dans cette autorisation pourra attribuer ces rôles intégrés aux [identités managées](../../active-directory/managed-identities-azure-resources/overview.md) dans le même locataire du client, ce qui est nécessaire au [déploiement des stratégies pouvant être corrigées](deploy-policy-remediation.md). Aucune autre autorisation normalement associée au rôle Administrateur de l’accès utilisateur ne s’appliquera à cet utilisateur.

## <a name="deploy-the-azure-resource-manager-templates"></a>Déployer les modèles Azure Resource Manager

Une fois que vous avez mis à jour votre fichier de paramètres, un utilisateur dans le locataire du client doit déployer le modèle Azure Resource Manager dans le locataire du client en tant que déploiement de niveau abonnement. Un déploiement distinct est nécessaire pour chaque abonnement que vous souhaitez intégrer à la gestion des ressources déléguées Azure (ou pour chaque abonnement contenant des groupes de ressources que vous souhaitez intégrer).

Comme il s’agit d’un déploiement au niveau de l’abonnement, il ne peut pas être lancé dans le Portail Azure. Le déploiement peut être effectué à l’aide de PowerShell ou d’Azure CLI, comme indiqué ci-dessous.

> [!IMPORTANT]
> Ce déploiement au niveau de l’abonnement doit être effectué par un compte non invité dans le locataire du client qui a le [rôle intégré Propriétaire](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner) pour l’abonnement en cours d’intégration (ou qui contient les groupes de ressources en cours d’intégration). Pour voir tous les utilisateurs qui peuvent déléguer l’abonnement, un utilisateur du locataire du client peut sélectionner l’abonnement dans le portail Azure, ouvrir **Contrôle d’accès (IAM)** et [afficher tous les utilisateurs ayant le rôle Propriétaire](../../role-based-access-control/role-assignments-list-portal.md#list-owners-of-a-subscription).

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

# Deploy Azure Resource Manager template using template and parameter file locally
New-AzDeployment -Name <deploymentName> `
                 -Location <AzureRegion> `
                 -TemplateFile <pathToTemplateFile> `
                 -TemplateParameterFile <pathToParameterFile> `
                 -Verbose

# Deploy Azure Resource Manager template that is located externally
New-AzDeployment -Name <deploymentName> `
                 -Location <AzureRegion> `
                 -TemplateUri <templateUri> `
                 -TemplateParameterUri <parameterUri> `
                 -Verbose
```

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

# Deploy Azure Resource Manager template using template and parameter file locally
az deployment create --name <deploymentName> \
                     --location <AzureRegion> \
                     --template-file <pathToTemplateFile> \
                     --parameters <parameters/parameterFile> \
                     --verbose

# Deploy external Azure Resource Manager template, with local parameter file
az deployment create --name <deploymentName> \
                     --location <AzureRegion> \
                     --template-uri <templateUri> \
                     --parameters <parameterFile> \
                     --verbose
```

## <a name="confirm-successful-onboarding"></a>Vérifier la réussite de l’intégration

Quand un abonnement de client a été correctement intégré à la gestion des ressources déléguées Azure, les utilisateurs dans le locataire du fournisseur de services peuvent voir l’abonnement et ses ressources (si l’accès à ceux-ci leur a été octroyé via le processus ci-dessus, soit individuellement, soit en tant que membres d’un groupe Azure AD disposant des autorisations appropriées). Pour vérifier cela, assurez-vous que l’abonnement s’affiche de l’une des manières suivantes.  

### <a name="azure-portal"></a>Portail Azure

Dans le locataire du fournisseur de services :

1. Accédez à la [page Mes clients](view-manage-customers.md).
2. Sélectionnez **Clients**.
3. Vérifiez que vous pouvez voir les abonnements portant le nom de l’offre que vous avez fourni dans le modèle Resource Manager.

> [!IMPORTANT]
> Pour afficher l’abonnement délégué dans [Mes clients](view-manage-customers.md), les utilisateurs du locataire du fournisseur de services doivent avoir reçu le rôle [Lecteur](../../role-based-access-control/built-in-roles.md#reader) (ou un autre rôle intégré qui comprend un accès Lecteur) lorsque l’abonnement a été intégré dans le cadre de la gestion des ressources déléguées Azure.

Dans le locataire du client :

1. Accédez à la [page Fournisseurs de services](view-manage-service-providers.md).
2. Sélectionnez **Offres de fournisseur de services**.
3. Vérifiez que vous pouvez voir les abonnements portant le nom de l’offre que vous avez fourni dans le modèle Resource Manager.

> [!NOTE]
> Quelques minutes peuvent s’écouler après votre déploiement, avant que les mises à jour apparaissent dans le portail Azure.

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

Get-AzContext
```

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

az account list
```

## <a name="remove-access-to-a-delegation"></a>Supprimer l’accès à une délégation

Par défaut, les utilisateurs résidant dans le locataire du client qui disposent des autorisations appropriées peuvent supprimer l’accès au fournisseur de services aux ressources déléguées dans la [page Fournisseurs de services](view-manage-service-providers.md#add-or-remove-service-provider-offers) du Portail Azure. Dans ce cas, aucun utilisateur du locataire du fournisseur de services ne pourra accéder aux ressources précédemment déléguées.

Si vous avez intégré des utilisateurs avec le [rôle Supprimer l’attribution de l’inscription des services managés](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role) lors de l’intégration d’un client dans le cadre de la gestion des ressources déléguées Azure, ces utilisateurs pourront également supprimer la délégation.

L’exemple ci-dessous montre une affectation octroyant le **rôle Supprimer l’attribution de l’inscription des services managés** pouvant être inclus dans un fichier de paramètres :

```json
    "authorizations": [ 
        { 
            "principalId": "cfa7496e-a619-4a14-a740-85c5ad2063bb", 
            "principalIdDisplayName": "MSP Operators", 
            "roleDefinitionId": "91c1777a-f3dc-4fae-b103-61d183457e46" 
        } 
    ] 
```

Un utilisateur disposant de cette autorisation peut supprimer une délégation de l’une des manières suivantes.

### <a name="azure-portal"></a>Portail Azure

1. Accédez à la [page Mes clients](view-manage-customers.md).
2. Sélectionnez **Délégations**.
3. Recherchez la délégation que vous souhaitez supprimer, puis sélectionnez l’icône de corbeille qui apparaît dans sa ligne.

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

# Sign in as a user from the managing tenant directory 

Login-AzAccount

# Select the subscription that is delegated - or contains the delegated resource group(s)

Select-AzSubscription -SubscriptionName "<subscriptionName>"

# Get the registration assignment

Get-AzManagedServicesAssignment -Scope "/subscriptions/{delegatedSubscriptionId}"

# Delete the registration assignment

Remove-AzManagedServicesAssignment -ResourceId "/subscriptions/{delegatedSubscriptionId}/providers/Microsoft.ManagedServices/registrationAssignments/{assignmentGuid}"
```

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

# Sign in as a user from the managing tenant directory

az login

# Select the subscription that is delegated – or contains the delegated resource group(s)

az account set -s <subscriptionId/name>

# List registration assignments

az managedservices assignment list

# Delete the registration assignment

az managedservices assignment delete --assignment <id or full resourceId>
```

## <a name="next-steps"></a>Étapes suivantes

- Découvrez les [Expériences de gestion inter-locataire](../concepts/cross-tenant-management-experience.md).
- [Affichez et gérez les clients](view-manage-customers.md) en accédant à **Mes clients** sur le portail Azure.
