---
title: Options d’ingestion des données
titleSuffix: Azure Machine Learning
description: En savoir plus sur les options d’ingestion des données pour la formation de vos modèles Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: nibaccam
author: nibaccam
ms.author: nibaccam
ms.date: 02/26/2020
ms.openlocfilehash: 6b1c671d2079c7d8ab59e9afe981ccef3f58ef27
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79086882"
---
# <a name="data-ingestion-in-azure-machine-learning"></a>Ingestion des données dans Azure Machine Learning

Dans cet article, vous allez découvrir les avantages et les inconvénients des options d’ingestion des données suivantes disponibles dans Azure Machine Learning. 

1. Pipelines [Azure Data Factory](#use-azure-data-factory)
2. [SDK Python Azure Machine Learning](#use-the-python-sdk)

L’ingestion des données est le processus dans lequel les données non structurées sont extraites d’une ou de plusieurs sources, puis préparées pour la formation de modèles Machine Learning. Ce processus prend également beaucoup de temps, en particulier s’il est effectué manuellement et si vous avez de grandes quantités de données provenant de plusieurs sources. L’automatisation de ce travail libère des ressources et garantit que vos modèles utilisent les données les plus récentes et les plus pertinentes.

Azure Data Factory (ADF) est spécifiquement conçu pour extraire, charger et transformer des données. Toutefois, le Kit de développement logiciel (SDK) Python vous permet de développer une solution de code personnalisée pour les tâches d’ingestion de données de base. Si aucun des deux n’est exactement ce dont vous avez besoin, vous pouvez également utiliser ADF et le Kit de développement logiciel (SDK) Python ensemble pour créer un workflow global d’ingestion de données qui répond à vos besoins. 

## <a name="use-azure-data-factory"></a>Utilisation d’Azure Data Factory

[Azure Data Factory](https://docs.microsoft.com/azure/data-factory/introduction) offre une prise en charge native de la surveillance des sources de données et des déclencheurs pour les pipelines d’ingestion des données.  

Le tableau suivant récapitule les avantages et les inconvénients de l’utilisation d’Azure Data Factory pour vos workflows d’ingestion des données.

|Avantages|Inconvénients
---|---
Conçu spécifiquement pour extraire, charger et transformer des données.|Offre actuellement un ensemble limité de tâches de pipeline Azure Data Factory. 
Vous permet de créer des workflows basés sur les données afin d’orchestrer le déplacement et les transformations des données à grande échelle.|Coûteux à construire et à entretenir. Pour plus d’informations, consultez la [page de tarification](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/) d’Azure Data Factory.
Intégré à différents outils Azure comme [Azure Databricks](https://docs.microsoft.com/azure/data-factory/transform-data-using-databricks-notebook) et [Azure Functions](https://docs.microsoft.com/azure/data-factory/control-flow-azure-function-activity). | N’exécute pas les scripts en mode natif, et s’appuie plutôt sur un calcul distinct pour l’exécution des scripts. 
Prend en charge l’ingestion des données déclenchée par la source de données en mode natif.| 
Les processus de préparation des données et de formation des modèles sont distincts.|
Capacité de traçabilité des données incorporées pour les dataflows Azure Data Factory.|
Fournit une [interface utilisateur](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-portal) à faible expérience de code pour les approches sans script. |

Ces étapes et le diagramme suivant illustrent le workflow d’ingestion des données d’Azure Data Factory.

1. Extrayez les données de leurs sources.
1. Transformez et enregistrez les données dans un conteneur de blobs de sortie, qui sert de stockage des données pour Azure Machine Learning.
1. Avec les données préparées stockées, le pipeline de Azure Data Factory appelle un pipeline Machine Learning de formation qui reçoit les données préparées pour la formation du modèle.


    ![Ingestion des données ADF](media/concept-data-ingestion/data-ingest-option-one.svg)
    
Découvrez comment créer un pipeline d’ingestion de données pour Machine Learning avec [Azure Data Factory](how-to-data-ingest-adf.md).

## <a name="use-the-python-sdk"></a>Utiliser le SDK Python 

Avec le [Kit de développement logiciel (SDK) Python](https://docs.microsoft.com/python/api/overview/azure/ml), vous pouvez incorporer des tâches d’ingestion des données dans une étape de [pipeline Azure Machine Learning](how-to-create-your-first-pipeline.md).

Le tableau suivant récapitule les avantages et les inconvénients de l’utilisation du Kit de développement logiciel (SDK) et d’une étape de pipelines ML pour les tâches d’ingestion des données.

Avantages| Inconvénients
---|---
Configure vos propres scripts Python. | Ne prend pas en charge le déclenchement par la modification des sources de données en mode natif. Nécessite l’implémentation d’une application logique ou d’une fonction Azure.
Prépare les données dans le cadre de chaque exécution de formation de modèle.|Requiert des qualifications de développement pour créer un script d’ingestion des données.
Prend en charge les scripts de préparation des données sur différentes cibles de calcul, y compris [le calcul Azure Machine Learning](concept-compute-target.md#azure-machine-learning-compute-managed). |Ne fournit pas d’interface utilisateur pour créer le mécanisme d’ingestion.

Dans le diagramme suivant, le pipeline Azure Machine Learning se compose de deux étapes : l’ingestion des données et la formation du modèle. L’étape d’ingestion des données englobe des tâches qui peuvent être accomplies à l’aide de bibliothèques Python et du Kit de développement logiciel (SDK) Python, telles que l’extraction de données à partir de sources locales/web, et des transformations de données de base, comme l’imputation des valeurs manquantes. L’étape de formation utilise ensuite les données préparées comme entrée de votre script d’apprentissage pour effectuer l’apprentissage de votre modèle Machine Learning. 

![Pipeline Azure + Kit de développement logiciel (SDK) d’ingestion des données](media/concept-data-ingestion/data-ingest-option-two.png)

## <a name="next-steps"></a>Étapes suivantes

* Découvrez comment créer un pipeline d’ingestion de données pour Machine Learning avec [Azure Data Factory](how-to-data-ingest-adf.md).
* Découvrez comment automatiser et gérer les cycles de vie du développement de vos pipelines d’ingestion des données avec [Azure Pipelines](how-to-cicd-data-ingestion.md).
