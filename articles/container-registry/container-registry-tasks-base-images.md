---
title: Mises à jour des images de base – Tâches
description: Familiarisez-vous avec les images de base pour les images conteneurs d’application et découvrez comment la mise à jour d’une image de base peut déclencher une tâche Azure Container Registry.
ms.topic: article
ms.date: 01/22/2019
ms.openlocfilehash: 017c8f8a3a15896bd6e14a54136ba713e9f9c499
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77617729"
---
# <a name="about-base-image-updates-for-acr-tasks"></a>À propos des mises à jour des images de base pour ACR Tasks

Cet article fournit des informations générales sur les mises à jour apportées à l’image de base d’une application et sur la façon dont elles peuvent déclencher une tâche Azure Container Registry.

## <a name="what-are-base-images"></a>Qu’est-ce qu’une image de base ?

Les fichiers Dockerfile qui définissent la plupart des images conteneurs spécifient une image parente, souvent appelée *image de base*, sur laquelle sont basées les autres images. Les images de base contiennent en général le système d’exploitation, par exemple [Alpine Linux][base-alpine] ou [Windows Nano Server][base-windows], sur lequel le reste des couches du conteneur est appliqué. Elles peuvent également inclure des infrastructures d’application comme [Node.js][base-node] ou [.NET Core][base-dotnet]. Ces images de base sont elles-mêmes généralement basées sur des images publiques en amont. Plusieurs de vos images d’application peuvent partager une image de base commune.

Une image de base est souvent mise à jour par le chargé de maintenance des images pour y inclure de nouvelles fonctionnalités ou améliorations relatives au système d’exploitation ou à l’infrastructure. Les correctifs de sécurité constituent une autre cause courante conduisant à une mise à jour de l’image de base. Lorsque ces mises à jour en amont se produisent, vous devez également mettre à jour vos images de base de façon à inclure le correctif critique. Chaque image d’application doit ensuite être également recréée pour inclure ces correctifs en amont maintenant inclus dans votre image de base.

Dans certains cas, par exemple une équipe de développement privée, l’image de base spécifie plus que le système d’exploitation ou le framework. Par exemple, l’image de base peut être une image de composant de service partagé pour laquelle un suivi est nécessaire. Les membres d’une équipe peuvent avoir besoin d’effectuer le suivi de cette image de base à des fins de test ou de la mettre à jour régulièrement lors du développement d’images d’application.

## <a name="track-base-image-updates"></a>Suivi des mises à jour des images de base

ACR Tasks vous permet de générer automatiquement des images lorsque l’image de base d’un conteneur est mise à jour.

ACR Tasks découvre dynamiquement les dépendances des images de base lorsqu’il génère une image conteneur. Par conséquent, il peut détecter quand l’image de base d’une image d’application est mise à jour. Avec une tâche de build préconfigurée, ACR Tasks est capable de reconstruire automatiquement chacune des images d’application faisant référence à l’image de base. Grâce à ces détection et regénération automatiques, ACR Tasks vous permet d’économiser le temps et les efforts normalement nécessaires au suivi et à la mise à jour manuels de chaque image d’application faisant référence à votre image de base mise à jour.

## <a name="base-image-locations"></a>Emplacements des images de base

Pour les générations d’images à partir d’un fichier Dockerfile, une tâche ACR détecte les dépendances sur les images de base aux emplacements suivants :

* Le registre de conteneurs Azure dans lequel s’exécute la tâche
* Un autre registre de conteneurs Azure, dans la même région ou non 
* Un référentiel public dans Docker Hub 
* Un référentiel public dans Microsoft Container Registry

Si l’image de base spécifiée dans l’instruction `FROM` se trouve à l’un de ces emplacements, la tâche ACR ajoute un hook pour que l’image soit bien regénérée à chaque mise à jour de sa base.

## <a name="additional-considerations"></a>Considérations supplémentaires

* **Images de base pour les images d’application** : actuellement, une tâche ACR ne suit les mises à jour des images de base que pour les images d’application (*runtime*). Elles ne suivent pas les mises à jour des images de base pour les images intermédiaires (*au moment de la génération*) utilisées dans des Dockerfiles multiétapes.  

* **Activé par défaut** : lorsque vous créez une tâche ACR avec la commande [az acr task create][az-acr-task-create], elle est par défaut *activée* pour se déclencher par la mise à jour de l’image de base. Cela signifie que la propriété `base-image-trigger-enabled` est définie sur True. Si vous voulez désactiver ce comportement dans une tâche, mettez à jour la propriété sur False. Par exemple, exécutez la commande suivante [az acr task update][az-acr-task-update] :

  ```azurecli
  az acr task update --myregistry --name mytask --base-image-trigger-enabled False
  ```

* **Déclencher pour le suivi des dépendances** : pour permettre à une tâche ACR de déterminer et de suivre les dépendances d’une image conteneur, y compris son image de base, vous devez d’abord déclencher la génération de l’image **au moins une fois**. Par exemple, déclenchez la tâche manuellement en utilisant la commande [az acr task run][az-acr-task-run].

* **Balise stable pour l’image de base** : pour déclencher une tâche lors de la mise à jour de l’image de base, il faut que cette dernière possède une balise *stable*, par exemple `node:9-alpine`. Cette catégorisation est typique pour une image de base qui est mise à jour avec des correctifs de système d’exploitation et de framework vers une dernière version stable. Si l’image de base est mis à jour avec une nouvelle balise de version, elle ne déclenche pas de tâche. Pour plus d’informations, consultez la catégorisation d’image, voir [Conseils sur les meilleures pratiques](container-registry-image-tag-version.md). 

* **Autres déclencheurs de tâche** : dans le cadre d’une tâche déclenchée par les mises à jour de l’image de base, vous pouvez également activer des déclencheurs selon la [validation du code source](container-registry-tutorial-build-task.md) ou une [planification](container-registry-tasks-scheduled.md). La mise à jour d’une image de base peut également déclencher une [tâche multiétape](container-registry-tasks-multi-step.md).

## <a name="next-steps"></a>Étapes suivantes

Pour connaître les scénarios d’automatisation de la génération des images d’application après la mise à jour d’une image de base, consultez les tutoriels suivants :

* [Automatisation de la génération des images conteneur en cas de mise à jour d’une image de base dans le même registre](container-registry-tutorial-base-image-update.md)

* [Automatisation de la génération des images conteneur en cas de mise à jour d’une image de base dans un autre registre](container-registry-tutorial-base-image-update.md)


<!-- LINKS - External -->
[base-alpine]: https://hub.docker.com/_/alpine/
[base-dotnet]: https://hub.docker.com/r/microsoft/dotnet/
[base-node]: https://hub.docker.com/_/node/
[base-windows]: https://hub.docker.com/r/microsoft/nanoserver/
[sample-archive]: https://github.com/Azure-Samples/acr-build-helloworld-node/archive/master.zip
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr#az-acr-build
[az-acr-pack-build]: /cli/azure/acr/pack#az-acr-pack-build
[az-acr-task]: /cli/azure/acr/task
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[az-acr-task-update]: /cli/azure/acr/task#az-acr-task-update
[az-login]: /cli/azure/reference-index#az-login
[az-login-service-principal]: /cli/azure/authenticate-azure-cli

<!-- IMAGES -->
[quick-build-01-fork]: ./media/container-registry-tutorial-quick-build/quick-build-01-fork.png
[quick-build-02-browser]: ./media/container-registry-tutorial-quick-build/quick-build-02-browser.png
