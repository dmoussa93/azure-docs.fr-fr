---
title: 'Démarrage rapide : Créer un pare-feu Azure avec plusieurs adresses IP publiques - modèle Resource Manager'
description: Découvrez comment utiliser un modèle Resource Manager pour créer un pare-feu Azure avec plusieurs adresses IP publiques.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: quickstart
ms.date: 04/14/2020
ms.author: victorh
ms.openlocfilehash: 3d58173d239e7a9249b588ff038ea46cfedb27a3
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/17/2020
ms.locfileid: "81605207"
---
# <a name="quickstart-create-an-azure-firewall-with-multiple-public-ip-addresses---resource-manager-template"></a>Démarrage rapide : Créer un pare-feu Azure avec plusieurs adresses IP publiques - modèle Resource Manager

Dans ce guide de démarrage rapide, vous utilisez un modèle Resource Manager pour déployer un pare-feu Azure avec plusieurs adresses IP publiques.

Le pare-feu déployé présente des règles de collection de règles NAT qui autorisent les connexions RDP à deux machines virtuelles Windows Server 2019.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Pour plus d’informations sur le pare-feu Azure avec plusieurs adresses IP publiques, consultez [Déployer un pare-feu Azure avec plusieurs adresses IP publiques à l’aide d’Azure PowerShell](deploy-multi-public-ip-powershell.md).

## <a name="prerequisites"></a>Prérequis

- Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-an-azure-firewall"></a>Créer un pare-feu Azure

Ce modèle crée un pare-feu Azure avec deux adresses IP publiques ainsi que les ressources nécessaires à la prise en charge du pare-feu Azure.

### <a name="review-the-template"></a>Vérifier le modèle

Le modèle utilisé dans ce guide de démarrage rapide est tiré des [modèles de démarrage rapide Azure](https://github.com/Azure/azure-quickstart-templates/blob/master/fw-docs-qs/azuredeploy.json).

:::code language="json" source="~/quickstart-templates/fw-docs-qs/azuredeploy.json" range="001-391" highlight="238-370":::

Plusieurs ressources Azure sont définies dans le modèle :

- [**Microsoft.Network/publicIPAddresses**](/azure/templates/microsoft.network/publicipaddresses)
- [**Microsoft.Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups)
- [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
- [**Microsoft.Compute/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines)
- [**Microsoft.Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces)
- [**Microsoft.Storage/storageAccounts**](/azure/templates/microsoft.storage/storageAccounts)
- [**Microsoft.Network/azureFirewalls**](/azure/templates/microsoft.network/azureFirewalls)
- [**Microsoft.Network/routeTables**](/azure/templates/microsoft.network/routeTables)


### <a name="deploy-the-template"></a>Déployer le modèle

Déployez le modèle Resource Manager sur Azure :

1. Sélectionnez **Déployer sur Azure** pour vous connecter à Azure et ouvrir le modèle. Le modèle crée un pare-feu Azure, l’infrastructure réseau et deux machines virtuelles.

   [![Déployer sur Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Ffw-docs-qs%2Fazuredeploy.json)

2. Dans le portail, affichez la page **Créer un pare-feu Azure avec plusieurs adresses IP publiques**, puis tapez ou sélectionnez les valeurs suivantes :
   - Abonnement : sélectionnez à partir d’abonnements existants. 
   - Groupe de ressources :  sélectionnez à partir de groupe de ressources existants, ou sélectionnez **Créer**, puis **OK**.
   - Localisation : Sélectionner un emplacement
   - Nom de l’utilisateur administrateur : tapez le nom d’utilisateur du compte de l’utilisateur administrateur. 
   - Mot de passe administrateur : tapez une clé ou un mot de passe administrateur.

3. Sélectionnez **J’accepte les conditions générales mentionnées ci-dessus**, puis **Acheter**. Le déploiement peut prendre 10 minutes ou plus.

## <a name="validate-the-deployment"></a>Valider le déploiement

Dans le portail Azure, passez en revue les ressources déployées. Notez les adresses IP publiques du pare-feu.  

Utilisez une connexion Bureau à distance pour vous connecter aux adresses IP publiques du pare-feu. Pour que les connexions réussissent, les règles NAT de pare-feu doivent autoriser la connexion aux serveurs back-end.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Quand vous n’avez plus besoin des ressources que vous avez créées avec le pare-feu, supprimez le groupe de ressources. Vous supprimez ainsi le pare-feu et toutes les ressources associées.

Pour supprimer le groupe de ressources, appelez l’applet de commande `Remove-AzResourceGroup` :

```azurepowershell-interactive
Remove-AzResourceGroup -Name "<your resource group name>"
```

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Tutoriel : Déployer et configurer un pare-feu Azure dans un réseau hybride à l’aide du portail Azure](tutorial-hybrid-portal.md)