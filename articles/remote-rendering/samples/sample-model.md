---
title: Exemples de modèles
description: Liste les sources d’exemples de modèles.
author: florianborn71
ms.author: flborn
ms.date: 01/29/2020
ms.topic: sample
ms.openlocfilehash: 0c28d36c7934fbbac0ddd11562c8dc237b0360ee
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80678490"
---
# <a name="sample-models"></a>Exemples de modèles

Cet article liste certaines ressources d’exemples de données qui peuvent être utilisés pour tester le service Azure Remote Rendering.

## <a name="built-in-sample-model"></a>Exemple de modèle intégré

Nous vous proposons un exemple de modèle intégré qui peut toujours être chargé à l’aide de l’URL **builtin://Engine**.

![Exemple de modèle](./media/sample-model.png "Exemple de modèle")

Statistiques du modèle :

| Nom | Valeur |
|-----------|:-----------|
| [Taille de machine virtuelle nécessaire](../how-tos/session-rest-api.md#create-a-session) | standard |
| Nombre de triangles | 18,7 millions |
| Nombre de pièces mobiles | 2 073 |
| Nombre de matériaux | 94 |

## <a name="third-party-data"></a>Données tierces

Le groupe Khronos gère un ensemble d’exemples de modèles glTF à des fins de test. ARR prend en charge le format glTF pour le format texte ( *.gltf*) et pour le format binaire ( *.glb*). Nous vous suggérons d’utiliser les modèles PBR pour obtenir de meilleurs résultats visuels :

* [Exemples de modèles glTF](https://github.com/KhronosGroup/glTF-Sample-Models)

## <a name="next-steps"></a>Étapes suivantes

* [Démarrage rapide : Afficher un modèle avec Unity](../quickstarts/render-model.md)
* [Démarrage rapide : Convertir un modèle pour le rendu](../quickstarts/convert-model.md)
