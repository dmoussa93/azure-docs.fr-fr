---
title: Spécifier les images de la Place de Marché pour un labo dans Azure Lab Services
description: Cet article explique comment spécifier les images de la Place de Marché, que le créateur de labo peut utiliser pour créer des labos dans un compte lab dans Azure Lab Services.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/14/2020
ms.author: spelluru
ms.openlocfilehash: ad56041f853d030e3a286610fe4872bffecaee12
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77444894"
---
# <a name="specify-marketplace-images-available-to-lab-creators"></a>Spécifier les images de la Place de marché accessibles aux créateurs lab
En tant que propriétaire d’un compte de laboratoire, vous pouvez spécifier les images de la place de marché que les créateurs de laboratoire peuvent utiliser pour créer des laboratoires dans le compte de laboratoire. 

## <a name="select-images-available-for-labs"></a>Sélectionner les images disponibles pour les labos
Sélectionnez **Images de la Place de marché** à gauche du menu. Par défaut, la liste complète des images (activées et désactivées) s’affiche. Vous pouvez filtrer la liste pour afficher uniquement les images activées/désactivées en sélectionnant l’option **Enabled only (Activées uniquement)** /**Disabled only (Désactivées uniquement)** dans la liste déroulante en haut. 
    
![Page des images de la Place de marché](../media/tutorial-setup-lab-account/marketplace-images-page.png)

Les images de la place de marché affichées dans la liste sont uniquement celles qui correspondent aux conditions suivantes :
    
- Crée une seule machine virtuelle.
- Utilise Azure Resource Manager pour approvisionner des machines virtuelles
- Ne nécessite pas l’achat d’un plan de licences supplémentaire

## <a name="disable-images-for-a-lab"></a>Désactiver les images pour un labo 
Pour désactiver une image pour un labo, sélectionnez **... (points de suspension)** dans la dernière colonne, puis sélectionnez **Désactiver l’image**. 

![Désactiver une image](../media/tutorial-setup-lab-account/disable-one-image.png) 

Vous pouvez également activer la case à cocher à côté du nom de l’image, puis sélectionner **Désactiver les images sélectionnées** dans la barre d’outils. 

Pour désactiver plusieurs images simultanément, activez les cases à cocher à côté des noms des images, puis sélectionnez **Désactiver les images sélectionnées** dans la barre d’outils. 

![Désactiver plusieurs images](../media/tutorial-setup-lab-account/disable-multiple-images.png) 


## <a name="enable-images-for-a-lab"></a>Activer des images pour un labo
Pour activer une image désactivée, sélectionnez **... (points de suspension)** dans la dernière colonne, puis sélectionnez **Activer l’image**. Vous pouvez également activer la case à cocher à côté du nom de l’image, puis sélectionner **Activer les images sélectionnées** dans la barre d’outils. 

Pour activer plusieurs images simultanément, activez les cases à cocher à côté des noms des images, puis sélectionnez **Activer les images sélectionnées** dans la barre d’outils. 

## <a name="next-steps"></a>Étapes suivantes
Voir les articles suivants :

- [En tant que propriétaire de labo, créer et gérer des labos](how-to-manage-classroom-labs.md)
- [En tant que propriétaire de labo, configurer et publier des modèles](how-to-create-manage-template.md)
- [En tant que propriétaire de labo, configurer et contrôler l’utilisation d’un labo](how-to-configure-student-usage.md)
- [En tant qu’utilisateur du laboratoire, accéder aux laboratoires de classe](how-to-use-classroom-lab.md)
