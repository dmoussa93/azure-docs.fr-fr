---
title: Intégrer Azure Event Hubs au service Azure Private Link
description: Découvrir comment intégrer Azure Event Hubs au service Azure Private Link
services: event-hubs
author: spelluru
ms.author: spelluru
ms.date: 03/12/2020
ms.service: event-hubs
ms.topic: article
ms.openlocfilehash: cff1b3b79b34d3f0bed27a2ea50799185958a8ba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79473770"
---
# <a name="integrate-azure-event-hubs-with-azure-private-link-preview"></a>Intégrer Azure Event Hubs à Azure Private Link (préversion)
Le service Azure Private Link vous permet d’accéder aux services Azure (par exemple, Azure Event Hubs, Stockage Azure et Azure Cosmos DB) ainsi qu’aux services de partenaire ou de client hébergés par Azure via un **point de terminaison privé** dans votre réseau virtuel.

Un point de terminaison privé est une interface réseau qui vous permet de vous connecter de façon privée et sécurisée à un service basé sur Azure Private Link. Le point de terminaison privé utilise une adresse IP privée de votre réseau virtuel, plaçant de fait le service dans votre réseau virtuel. Sachant que l’ensemble du trafic à destination du service peut être routé via le point de terminaison privé, il n’y a aucun besoin de passerelles, d’appareils NAT, de connexions ExpressRoute ou VPN ou d’adresses IP publiques. Le trafic entre votre réseau virtuel et le service transite par le réseau principal de Microsoft, éliminant ainsi toute exposition à l’Internet public. Vous pouvez vous connecter à une instance d’une ressource Azure, ce qui vous donne le plus haut niveau de granularité en matière de contrôle d’accès.

Pour plus d’informations, consultez [Qu’est-ce qu’Azure Private Link ?](../private-link/private-link-overview.md)

> [!NOTE]
> Cette fonctionnalité est prise en charge uniquement avec le niveau **dédié**. Pour plus d’informations sur le niveau dédié, consultez [Vue d’ensemble d’Event Hubs Dedicated](event-hubs-dedicated-overview.md). 
>
> Cette fonctionnalité est actuellement en **préversion**. 


## <a name="add-a-private-endpoint-using-azure-portal"></a>Ajouter un point de terminaison privé avec le portail Azure

### <a name="prerequisites"></a>Prérequis

Pour intégrer un espace de noms Event Hubs à Azure Private Link, vous avez besoin des entités ou autorisations suivantes :

- Un espace de noms Event Hubs.
- Un réseau virtuel Azure.
- Un sous-réseau dans le réseau virtuel.
- Des autorisations de propriétaire ou de contributeur à la fois pour l’espace de noms et le réseau virtuel.

Votre point de terminaison privé et votre réseau virtuel doivent se trouver dans la même région. Au moment de sélectionner la région du point de terminaison privé sur le portail, les réseaux virtuels qui se trouvent dans cette région sont filtrés automatiquement. Votre espace de noms peut se trouver dans une autre région.

Votre point de terminaison privé utilise une adresse IP privée de votre réseau virtuel.

### <a name="steps"></a>Étapes
Si vous avez déjà un espace de noms Event Hubs, vous pouvez créer une connexion de liaison privée en suivant ces étapes :

1. Connectez-vous au [portail Azure](https://portal.azure.com). 
2. Dans la barre de recherche, tapez **event hubs**.
3. Dans la liste, sélectionnez l’**espace de noms** auquel vous voulez ajouter un point de terminaison privé.
4. Sélectionnez l’onglet **Réseau** situé sous **Paramètres**.
5. Sélectionnez l’onglet **Connexions de point de terminaison privé (préversion)** en haut de la page. Si vous n’utilisez pas de niveau dédié Event Hubs, vous voyez le message : **Les connexions de point de terminaison privé sur Event Hubs sont prises en charge uniquement par les espaces de noms créés sous un cluster dédié**.
6. Sélectionnez le bouton **+ Point de terminaison privé** en haut de la page.

    ![Image](./media/private-link-service/private-link-service-3.png)
7. Dans la page **Informations de base**, suivez ces étapes : 
    1. Sélectionnez l’**abonnement Azure** où créer le point de terminaison privé. 
    2. Sélectionnez le **groupe de ressources** pour la ressource de point de terminaison privé.
    3. Entrez un **nom** pour le point de terminaison privé. 
    5. Sélectionnez une **région** pour le point de terminaison privé. Votre point de terminaison privé doit être dans la même région que celle de votre réseau virtuel, mais peut être différente de celle de la ressource de liaison privée à laquelle vous vous connectez. 
    6. Sélectionnez **Suivant : Bouton Ressource >** en bas de la page.

        ![Créer un point de terminaison privé - page Informations de base](./media/private-link-service/create-private-endpoint-basics-page.png)
8. Dans la page **Ressource**, suivez ces étapes :
    1. Pour la méthode de connexion, si vous sélectionnez **Se connecter à une ressource Azure dans mon répertoire**, suivez ces étapes : 
        1. Sélectionnez l’**abonnement Azure** où se trouve votre **espace de noms Event Hubs**. 
        2. Pour **Type de ressource**, sélectionnez **Microsoft.EventHub/namespaces** **** .
        3. Pour **Ressource**, sélectionnez un espace de noms Event Hubs dans la liste déroulante. 
        4. Confirmez que la **Sous-ressource cible** est définie sur **espace de noms**.
        5. Sélectionnez **Suivant : Bouton Configuration >** en bas de la page. 
        
            ![Créer un point de terminaison privé - page Ressource](./media/private-link-service/create-private-endpoint-resource-page.png)    
    2. Si vous sélectionnez **Se connecter à une ressource Azure par alias ou ID de ressource**, suivez ces étapes :
        1. Entrez l’**ID de ressource** ou l’**alias**. Il peut s’agir de l’ID de ressource ou de l’alias que des utilisateurs ont partagés avec vous.
        2. Pour **Sous-ressource cible**, entrez **espace de noms**. Il s’agit du type de la sous-ressource à laquelle votre point de terminaison privé peut accéder.
        3. (facultatif) Entrez un **message de demande**. Le propriétaire de la ressource voit ce message quand il gère la connexion de point de terminaison privé.
        4. Ensuite, sélectionnez **Suivant : Bouton Configuration >** en bas de la page.

            ![Créer un point de terminaison privé - Se connecter à l’aide d’un ID de ressource](./media/private-link-service/connect-resource-id.png)
9. Dans la page **Configuration**, vous sélectionnez le sous-réseau dans un réseau virtuel sur lequel vous voulez déployer le point de terminaison privé. 
    1. Sélectionnez un **réseau virtuel**. Seuls les réseaux virtuels dans l’abonnement et l’emplacement sélectionnés sont présents dans la liste déroulante. 
    2. Sélectionnez un **sous-réseau** dans le réseau virtuel que vous avez sélectionné. 
    3. Sélectionnez **Suivant : Bouton Étiquettes >** en bas de la page. 

        ![Créer un point de terminaison privé - page Configuration](./media/private-link-service/create-private-endpoint-configuration-page.png)
10. Dans la page **Étiquettes**, créez les étiquettes (noms et valeurs) à associer à la ressource de point de terminaison privé. Ensuite, sélectionnez le bouton **Vérifier + créer** en bas de la page. 
11. Dans la page **Vérifier + créer**, examinez tous les paramètres et sélectionnez **Créer** pour créer le point de terminaison privé.
    
    ![Créer un point de terminaison privé - page Vérifier et créer](./media/private-link-service/create-private-endpoint-review-create-page.png)
12. Vérifiez que vous voyez la connexion de point de terminaison privé que vous avez créée dans la liste des points de terminaison. Dans cet exemple, le point de terminaison privé est approuvé automatiquement, car vous êtes connecté à une ressource Azure dans votre répertoire et avez les autorisations nécessaires. 

    ![Point de terminaison privé créé](./media/private-link-service/private-endpoint-created.png)

## <a name="add-a-private-endpoint-using-powershell"></a>Ajouter un point de terminaison privé avec PowerShell
L’exemple suivant montre comment utiliser Azure PowerShell pour créer une connexion de point de terminaison privé. Il ne crée pas de cluster dédié pour vous. Suivez les étapes décrites dans [cet article](event-hubs-dedicated-cluster-create-portal.md) pour créer un cluster Event Hubs dédié. 

```azurepowershell-interactive
# create resource group

$rgName = "<RESOURCE GROUP NAME>"
$vnetlocation = "<VIRTUAL NETWORK LOCATION>"
$vnetName = "<VIRTUAL NETWORK NAME>"
$subnetName = "<SUBNET NAME>"
$namespaceLocation = "<NAMESPACE LOCATION>"
$namespaceName = "<NAMESPACE NAME>"
$peConnectionName = "<PRIVATE ENDPOINT CONNECTION NAME>"

# create virtual network
$virtualNetwork = New-AzVirtualNetwork `
                    -ResourceGroupName $rgName `
                    -Location $vnetlocation `
                    -Name $vnetName `
                    -AddressPrefix 10.0.0.0/16

# create subnet with endpoint network policy disabled
$subnetConfig = Add-AzVirtualNetworkSubnetConfig `
                    -Name $subnetName `
                    -AddressPrefix 10.0.0.0/24 `
                    -PrivateEndpointNetworkPoliciesFlag "Disabled" `
                    -VirtualNetwork $virtualNetwork

# update virtual network
$virtualNetwork | Set-AzVirtualNetwork

# create an event hubs namespace in a dedicated cluster
$namespaceResource = New-AzResource -Location $namespaceLocation `
                                    -ResourceName $namespaceName `
                                    -ResourceGroupName $rgName `
                                    -Sku @{name = "Standard"; capacity = 1} `
                                    -Properties @{clusterArmId = "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventHub/clusters/<EVENT HUBS CLUSTER NAME>"} `
                                    -ResourceType "Microsoft.EventHub/namespaces" -ApiVersion "2018-01-01-preview"

# create private endpoint connection
$privateEndpointConnection = New-AzPrivateLinkServiceConnection `
                                -Name $peConnectionName `
                                -PrivateLinkServiceId $namespaceResource.ResourceId `
                                -GroupId "namespace"

# get subnet object that you will use later
$virtualNetwork = Get-AzVirtualNetwork -ResourceGroupName  $rgName -Name $vnetName
$subnet = $virtualNetwork | Select -ExpandProperty subnets `
                                | Where-Object  {$_.Name -eq $subnetName}  
   
# create a private endpoint   
$privateEndpoint = New-AzPrivateEndpoint -ResourceGroupName $rgName  `
                                -Name $vnetName   `
                                -Location $vnetlocation `
                                -Subnet  $subnet   `
                                -PrivateLinkServiceConnection $privateEndpointConnection

(Get-AzResource -ResourceId $namespaceResource.ResourceId -ExpandProperties).Properties


```

## <a name="manage-private-endpoints-using-azure-portal"></a>Gérer des points de terminaison privés avec le portail Azure

Quand vous créez un point de terminaison privé, la connexion doit être approuvée. Si la ressource pour laquelle vous créez un point de terminaison privé se trouve dans votre répertoire, vous pouvez approuver la demande de connexion à condition d’avoir les autorisations nécessaires. Si vous vous connectez à une ressource Azure dans un autre répertoire, vous devez attendre que le propriétaire de cette ressource approuve votre demande de connexion.

Il existe quatre états de provisionnement :

| Action de service | État du point de terminaison privé de l’utilisateur du service | Description |
|--|--|--|
| None | Pending | La connexion est créée manuellement et est en attente d’approbation du propriétaire de la ressource Private Link. |
| Approbation | Approved | La connexion a été approuvée automatiquement ou manuellement et est prête à être utilisée. |
| Rejeter | Rejeté | La connexion a été rejetée par le propriétaire de la ressource Private Link. |
| Supprimer | Déconnecté | La connexion a été supprimée par le propriétaire de la ressource Private Link, le point de terminaison privé devient donc informatif et doit être supprimé dans le cadre d’un nettoyage. |
 
###  <a name="approve-reject-or-remove-a-private-endpoint-connection"></a>Approuver, rejeter ou supprimer une connexion de point de terminaison privé

1. Connectez-vous au portail Azure.
2. Dans la barre de recherche, tapez **event hubs**.
3. Sélectionnez l’**espace de noms** que vous voulez gérer.
4. Sélectionnez l’onglet **Réseau**.
5. Accédez à la section appropriée ci-dessous en fonction de l’opération souhaitée : approuver, rejeter ou supprimer.

### <a name="approve-a-private-endpoint-connection"></a>Approuver une connexion de point de terminaison privé
1. Si une connexion est en attente, elle est listée avec l’état de provisionnement **En attente**. 
2. Sélectionnez le **point de terminaison privé** à approuver
3. Sélectionnez le bouton **Approuver**.

    ![Image](./media/private-link-service/approve-private-endpoint.png)
4. Dans la page **Approuver la connexion**, ajoutez un commentaire (facultatif) et sélectionnez **Oui**. Si vous sélectionnez **Non**, il ne se passe rien. 
5. Vous devez voir que l’état de la connexion de point de terminaison privé dans la liste est remplacé par **Approuvé**. 

### <a name="reject-a-private-endpoint-connection"></a>Rejeter une connexion de point de terminaison privé

1. Si vous voulez rejeter une connexion de point de terminaison privé, qu’il s’agisse d’une demande en attente ou d’une connexion existante, sélectionnez la connexion et cliquez sur le bouton **Rejeter**.

    ![Image](./media/private-link-service/private-endpoint-reject-button.png)
2. Dans la page **Rejeter la connexion**, entrez un commentaire (facultatif) et sélectionnez **Oui**. Si vous sélectionnez **Non**, il ne se passe rien. 
3. Vous devez voir que l’état de la connexion de point de terminaison privé dans la liste est remplacé par **Rejeté**. 

### <a name="remove-a-private-endpoint-connection"></a>Supprimer une connexion de point de terminaison privé

1. Pour supprimer une connexion de point de terminaison privé, sélectionnez-la dans la liste et sélectionnez **Supprimer** dans la barre d’outils.
2. Dans la page **Supprimer la connexion**, sélectionnez **Oui** pour confirmer la suppression du point de terminaison privé. Si vous sélectionnez **Non**, il ne se passe rien.
3. Vous devez voir que l’état a été remplacé par **Déconnecté**. Vous voyez ensuite que le point de terminaison n’est plus dans la liste.

## <a name="validate-that-the-private-link-connection-works"></a>Vérifier le fonctionnement de la connexion à liaison privée

Vous devez vérifier que les ressources contenues dans le sous-réseau de la ressource de point de terminaison privé se connectent à votre espace de noms Event Hubs via une adresse IP privée, et qu’elles sont intégrées à la zone DNS privée appropriée.

Commencez par créer une machine virtuelle en suivant les étapes décrites dans [Créer une machine virtuelle Windows sur le portail Azure](../virtual-machines/windows/quick-create-portal.md).

Sous l’onglet **Réseau** :

1. Spécifiez un **réseau virtuel** et un **sous-réseau**. Vous pouvez créer un nouveau réseau virtuel ou en utiliser un existant. Si vous en sélectionnez un existant, veillez à ce que la région corresponde.
1. Spécifiez une ressource d’**adresse IP publique**.
1. Dans **Groupe de sécurité réseau de la carte réseau**, sélectionnez **Aucun**.
1. Dans **Équilibrage de charge**, sélectionnez **Non**.

Ouvrez la ligne de commande et exécutez la commande suivante :

```console
nslookup <your-event-hubs-namespace-name>.servicebus.windows.net
```

Si vous exécutez la commande ns lookup pour résoudre l’adresse IP d’un espace de noms Event Hubs via un point de terminaison public, vous obtenez un résultat semblable à ceci :

```console
c:\ >nslookup <your-event-hubs-namespae-name>.servicebus.windows.net

Non-authoritative answer:
Name:    
Address:  (public IP address)
Aliases:  <your-event-hubs-namespace-name>.servicebus.windows.net
```

Si vous exécutez la commande ns lookup pour résoudre l’adresse IP d’un espace de noms Event Hubs via un point de terminaison privé, vous obtenez un résultat semblable à ceci :

```console
c:\ >nslookup your_event-hubs-namespace-name.servicebus.windows.net

Non-authoritative answer:
Name:    
Address:  10.1.0.5 (private IP address)
Aliases:  <your-event-hub-name>.servicebus.windows.net
```

## <a name="limitations-and-design-considerations"></a>Limitations et remarques sur la conception

**Prix** : Pour plus d’informations sur les prix, consultez [Prix d’Azure Private Link](https://azure.microsoft.com/pricing/details/private-link/).

**Limitations** :  Le point de terminaison privé pour Azure Event Hubs est en préversion publique. Cette fonctionnalité est disponible dans toutes les régions publiques Azure.

**Nombre maximal de points de terminaison privés par espace de noms Event Hubs** : 120.

Pour plus d’informations, consultez [Service Azure Private Link : Limitations](../private-link/private-link-service-overview.md#limitations)

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur [Azure Private Link](../private-link/private-link-service-overview.md)
- En savoir plus sur [Azure Event Hubs](event-hubs-about.md)
