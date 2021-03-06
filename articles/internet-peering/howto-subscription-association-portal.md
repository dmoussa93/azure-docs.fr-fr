---
title: Associer un ASN de pair à un abonnement Azure en utilisant le portail
titleSuffix: Azure
description: Associer un ASN de pair à un abonnement Azure en utilisant le portail
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: cee548aff49cd5e4a57eed994b8ade2d157c6313
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75912130"
---
# <a name="associate-peer-asn-to-azure-subscription-using-the-portal"></a>Associer un ASN de pair à un abonnement Azure en utilisant le portail

Avant de soumettre une demande de Peering, vous devez d’abord associer votre NSA à un abonnement Azure en effectuant les étapes ci-dessous.

Si vous préférez, vous pouvez suivre ce guide à l’aide de [PowerShell](howto-subscription-association-powershell.md).

## <a name="create-peerasn-to-associate-your-asn-with-azure-subscription"></a>Créer un PeerAsn pour associer votre NSA à un abonnement Azure

### <a name="sign-in-to-the-portal"></a>Se connecter au portail
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name="register-for-peering-resource-provider"></a>S’inscrire auprès du fournisseur de ressources de Peering
Inscrivez-vous auprès du fournisseur de ressources de Peering dans votre abonnement en effectuant les étapes ci-dessous. Si vous n’exécutez pas cette commande, les ressources Azure nécessaires pour configurer le Peering ne sont pas accessibles.

1. Cliquez sur **Abonnements** dans le coin supérieur gauche du portail. Si cette option n’est pas visible, cliquez sur **Plus de services** et recherchez-la.

    > [!div class="mx-imgBorder"]
    > ![Ouvrir Abonnements](./media/rp-subscriptions-open.png)

1. Cliquez sur l’abonnement que vous souhaitez utiliser pour le Peering.

    > [!div class="mx-imgBorder"]
    > ![Lancer l’abonnement](./media/rp-subscriptions-launch.png)

1. Une fois l’abonnement ouvert, sur la gauche, cliquez sur **Fournisseurs de ressources**. Ensuite, dans le volet droit, recherchez *Peering* dans la fenêtre de recherche, ou utilisez la barre de défilement pour rechercher **Microsoft.Peering** et observez l’**État**. Si l’état est ***Registered***, ignorez les étapes ci-dessous et passez à la section **Créer un PeerAsn**. Si l’état est ***NotRegistered***, sélectionnez **Microsoft.Peering**, puis cliquez sur **S’inscrire**.

    > [!div class="mx-imgBorder"]
    > ![Démarrage de l’inscription](./media/rp-register-start.png)

1. Notez que l’état passe à ***Registering***.

    > [!div class="mx-imgBorder"]
    > ![Inscription en cours](./media/rp-register-progress.png)

1. Attendez environ une minute que l’inscription se termine. Ensuite, cliquez sur **Actualiser** et vérifiez que l’état est ***Registered***.

    > [!div class="mx-imgBorder"]
    > ![Inscription terminée](./media/rp-register-completed.png)

### <a name="create-peerasn"></a>Créer un PeerAsn
Vous pouvez créer une nouvelle ressource PeerAsn pour associer un numéro de système autonome (NSA) à un abonnement Azure. Vous pouvez associer plusieurs NSA à un abonnement en créant un **PeerAsn** pour chaque NSA que vous devez associer.

1. Cliquez sur **Créer une ressource** > **Afficher tout**.

    > [!div class="mx-imgBorder"]
    > ![Rechercher un PeerAsn](./media/peerasn-seeall.png)

1. Recherchez *PeerAsn* dans la barre de recherche et appuyez sur la touche *Entrer* du clavier. Dans les résultats, cliquez sur la ressource **PeerAsn**.

    > [!div class="mx-imgBorder"]
    > ![Lancer PeerAsn](./media/peerasn-launch.png)

1. Une fois **PeerAsn** lancé, cliquez sur **Créer**.

    > [!div class="mx-imgBorder"]
    > ![Créer un PeerAsn](./media/peerasn-create.png)

1. Dans la page **Associer un NSA d’homologue**, sous l’onglet **Informations de base**, remplissez les champs comme indiqué ci-dessous.

    > [!div class="mx-imgBorder"]
    > ![Onglet Informations de base de PeerAsn](./media/peerasn-basics-tab.png)

    * Le **Nom** correspond au nom de la ressource, et vous pouvez le choisir librement.  
    * Choisissez l’**Abonnement** auquel vous devez associer le NSA.
    * Le **Nom d’homologue** correspond au nom de votre société, et doit ressembler le plus possible à votre profil PeeringDB. Notez que cette valeur prend en charge uniquement les caractères a-z, A-Z et l’espace.
    * Entrez votre NSA dans le champ **Numéro ASN de l’homologue**.
    * Cliquez sur **Créer** et entrez l’**adresse e-mail** et le **numéro de téléphone** de votre Centre d’opérations réseau.
1. Ensuite, cliquez sur **Passer en revue + créer** et notez que le portail exécute la validation de base des informations que vous avez entrées. *Exécution de la validation finale…* s’affiche dans un ruban en haut de l’écran.

    > [!div class="mx-imgBorder"]
    > ![Onglet de révision de PeerAsn](./media/peerasn-review-tab-validation.png)

1. Une fois que le message *Validation réussie* s’affiche dans le ruban, vérifiez vos informations et envoyez la requête en cliquant sur **Créer**. Si la validation échoue, cliquez sur **Précédent**, répétez les étapes ci-dessus pour modifier votre requête et vérifiez que les valeurs que vous entrez n’ont pas d’erreurs.

    > [!div class="mx-imgBorder"]
    > ![Onglet de révision de PeerAsn](./media/peerasn-review-tab.png)

1. Une fois que vous avez envoyé la requête, attendez qu’elle termine le déploiement. Si le déploiement échoue, contactez l’assistance [Peering Microsoft](mailto:peering@microsoft.com). Un déploiement réussi présente l’apparence suivante.

    > [!div class="mx-imgBorder"]
    > ![Création de PeerAsn réussie](./media/peerasn-success.png)

### <a name="view-status-of-a-peerasn"></a>Afficher l’état d’un PeerAsn
Une fois que la ressource PeerAsn est déployée avec succès, vous devez attendre que Microsoft approuve la demande d’association. L’approbation peut prendre jusqu’à 12 heures. Une fois la demande approuvée, vous recevrez une notification à l’adresse e-mail entrée dans la section ci-dessus.

> [!IMPORTANT]
> Attendez que l’état de validation passe à « Approved » avant de soumettre une demande de Peering. Cette approbation peut prendre jusqu’à 12 heures.

## <a name="modify-peerasn"></a>Modifier un PeerAsn
La modification de PeerAsn n’est pas prise en charge actuellement. Si vous avez besoin de le modifier, contactez l’assistance [Peering Microsoft](mailto:peering@microsoft.com).

## <a name="delete-peerasn"></a>Supprimer un PeerAsn
La suppression d’un PeerAsn n’est pas prise en charge actuellement. Si vous avez besoin de supprimer un PeerAsn, contactez l’assistance [Peering Microsoft](mailto:peering@microsoft.com).

## <a name="next-steps"></a>Étapes suivantes

* [Créer ou modifier un Peering direct à l’aide du portail](howto-direct-portal.md)
* [Convertir un Peering direct hérité en ressource Azure à l’aide du portail](howto-legacy-direct-portal.md)
* [Créer ou modifier un Peering Exchange à l’aide du portail](howto-exchange-portal.md)
* [Convertir un Peering Exchange hérité en ressource Azure à l’aide du portail](howto-legacy-exchange-portal.md)

## <a name="additional-resources"></a>Ressources supplémentaires

Pour plus d’informations, consultez [FAQ sur le peering Internet](faqs.md).