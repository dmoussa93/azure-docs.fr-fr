---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/18/2020
ms.author: glenga
ms.openlocfilehash: eb54439f89cc2443eeed2d3b63dfbe7fedb4bf17
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80673499"
---
## <a name="update-the-tests"></a>Mettre à jour les tests

Étant donné que l’archétype crée également un ensemble de tests, vous devez mettre à jour ces tests pour gérer le nouveau paramètre `msg` dans la signature de méthode `run`.  

Accédez à l’emplacement de votre code de test sous _src/test/Java_, ouvrez le fichier projet *Function.java* et remplacez la ligne de code sous `//Invoke` par le code suivant.

:::code language="java" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/src/test/java/com/function/FunctionTest.java" range="48-50":::
