---
title: Créer une passerelle Azure Application Gateway - Interface de ligne de commande Azure classique
description: Découvrez comment créer une passerelle Application Gateway à l’aide de l’interface de ligne de commande Azure classique dans Resource Manager
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: conceptual
ms.date: 4/15/2019
ms.author: victorh
ms.openlocfilehash: ed7bbbf55956d655115b94c8ac46432b3e36c49b
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81312644"
---
# <a name="create-an-application-gateway-by-using-the-azure-cli"></a>Créer une passerelle Application Gateway à l’aide de l’interface de ligne de commande Azure

La passerelle Azure Application Gateway est un équilibreur de charge de couche 7. Elle assure l’exécution des requêtes HTTP de basculement et de routage des performances entre serveurs locaux ou dans le cloud. Une passerelle Application Gateway offre les fonctionnalités de livraison d’applications suivantes : équilibrage de charge HTTP, affinité de session basée sur les cookies, déchargement TLS (Transport Layer Security), précédemment connu sous le nom de SSL (Secure Sockets Layer), sondes d'intégrité personnalisées et prise en charge de plusieurs sites.

## <a name="prerequisite-install-the-azure-cli"></a>Condition préalable : Installer l’interface de ligne de commande Microsoft Azure

Pour exécuter la procédure indiquée dans cet article, vous devez [installer l’interface de ligne de commande Azure](../xplat-cli-install.md) et [vous connecter dans Azure](/cli/azure/authenticate-azure-cli). 

> [!NOTE]
> Si vous n’avez pas de compte Azure, vous devez vous en procurer un. Inscrivez-vous à un [essai gratuit ici](../active-directory/fundamentals/sign-up-organization.md).

## <a name="scenario"></a>Scénario

Dans ce scénario, vous allez apprendre à créer une passerelle Application Gateway à l’aide du portail Azure.

Ce scénario va :

* créer une passerelle Application Gateway moyenne avec deux instances ;
* Créer un réseau virtuel nommé ContosoVNET avec un bloc CIDR réservé de 10.0.0.0/16.
* Créer un sous-réseau appelé subnet01 qui utilise 10.0.0.0/28 comme bloc CIDR.

> [!NOTE]
> La configuration supplémentaire de la passerelle Application Gateway, y compris les sondes d’intégrité personnalisées, les adresses de pool principal et les règles supplémentaires sont configurées après avoir configuré la passerelle Application Gateway et non lors du déploiement initial.

## <a name="before-you-begin"></a>Avant de commencer

La passerelle Application Gateway Azure requiert son propre sous-réseau. Lorsque vous créez un réseau virtuel, assurez-vous que vous laissez suffisamment d’espace d’adresse pour disposer de plusieurs sous-réseaux. Une fois que vous avez déployé une passerelle Application Gateway sur un sous-réseau, seules les passerelles Application Gateway supplémentaires peuvent être ajoutées au sous-réseau.

## <a name="sign-in-to-azure"></a>Connexion à Azure

Ouvrez l’**Invite de commandes Microsoft Azure**et connectez-vous.

```azurecli-interactive
az login
```

Une fois que vous avez tapé l’exemple précédent, un code est fourni. Accédez à https://aka.ms/devicelogin dans un navigateur pour poursuivre le processus de connexion.

![Invite de commande affichant le code de l’appareil][1]

Dans le navigateur, entrez le code que vous avez reçu. Vous êtes redirigé vers une page de connexion.

![Page de navigateur pour la saisie du code][2]

Une fois que vous êtes connecté, fermez le navigateur pour poursuivre le scénario.

![Connexion réussie][3]

## <a name="switch-to-resource-manager-mode"></a>Basculer en mode Resource Manager

```azurecli-interactive
azure config mode arm
```

## <a name="create-the-resource-group"></a>Créer le groupe de ressources

Avant de créer la passerelle Application Gateway, un groupe de ressources est créé pour contenir cette dernière. Voici la commande.

```azurecli-interactive
azure group create \
--name ContosoRG \
--location eastus
```

## <a name="create-a-virtual-network"></a>Créez un réseau virtuel

Une fois le groupe de ressources créé, un réseau virtuel est créé pour la passerelle Application Gateway.  Dans l’exemple suivant, l’espace d’adressage était 10.0.0.0/16 tel que défini dans les notes du scénario précédent.

```azurecli-interactive
azure network vnet create \
--name ContosoVNET \
--address-prefixes 10.0.0.0/16 \
--resource-group ContosoRG \
--location eastus
```

## <a name="create-a-subnet"></a>Créer un sous-réseau

Une fois le réseau virtuel créé, un sous-réseau est ajouté pour la passerelle Application Gateway.  Si vous envisagez d’utiliser la passerelle Application Gateway avec une application web hébergée dans le même réseau virtuel que la passerelle Application Gateway, veillez à laisser suffisamment de place pour un autre sous-réseau.

```azurecli-interactive
azure network vnet subnet create \
--resource-group ContosoRG \
--name subnet01 \
--vnet-name ContosoVNET \
--address-prefix 10.0.0.0/28 
```

## <a name="create-the-application-gateway"></a>Créer la passerelle Application Gateway

Une fois le réseau virtuel et le sous-réseau créés, les conditions préalables pour la passerelle Application Gateway sont remplies. En outre, un certificat .pfx précédemment exporté et le mot de passe du certificat sont requis pour l’étape suivante : Les adresses IP principales sont les adresses IP de votre serveur principal. Ces valeurs peuvent être des adresses IP privées dans le réseau virtuel, des adresses IP publiques ou des noms de domaine complets pour vos serveurs backend.

```azurecli-interactive
azure network application-gateway create \
--name AdatumAppGateway \
--location eastus \
--resource-group ContosoRG \
--vnet-name ContosoVNET \
--subnet-name subnet01 \
--servers 134.170.185.46,134.170.188.221,134.170.185.50 \
--capacity 2 \
--sku-tier Standard \
--routing-rule-type Basic \
--frontend-port 80 \
--http-settings-cookie-based-affinity Enabled \
--http-settings-port 80 \
--http-settings-protocol http \
--frontend-port http \
--sku-name Standard_Medium
```

> [!NOTE]
> Pour obtenir la liste de paramètres pouvant être fournis lors de la création, exécutez la commande suivante : **azure network application-gateway create --help**.

Cet exemple crée une passerelle Application Gateway de base avec les paramètres par défaut pour l’écouteur, le pool backend, les paramètres http backend et les règles. Vous pouvez modifier ces paramètres en fonction de votre déploiement une fois l’approvisionnement réussi.
Si vous avez déjà défini votre application web avec le pool principal aux étapes précédentes, une fois la création effective, l’équilibrage de charge commence.

## <a name="next-steps"></a>Étapes suivantes

Apprenez à créer des sondes d’intégrité personnalisées en vous rendant sur [Créer une sonde d’intégrité personnalisée](application-gateway-create-probe-portal.md)

Apprenez à configurer le déchargement TLS et à épargner un déchiffrement TLS coûteux à vos serveurs web en consultant [Configurer le déchargement TLS](application-gateway-ssl-arm.md)

<!--Image references-->

[scenario]: ./media/application-gateway-create-gateway-cli-nodejs/scenario.png
[1]: ./media/application-gateway-create-gateway-cli-nodejs/figure1.png
[2]: ./media/application-gateway-create-gateway-cli-nodejs/figure2.png
[3]: ./media/application-gateway-create-gateway-cli-nodejs/figure3.png
