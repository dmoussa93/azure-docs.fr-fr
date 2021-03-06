---
title: Ingérer des données dans Azure Data Lake Storage Gen2 dans Azure Synapse Analytics
description: Découvrez comment ingérer des données dans Azure Data Lake Storage Gen2 dans Azure Synapse Analytics
services: synapse-analytics
author: djpmsft
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: daperlov
ms.reviewer: jrasnick
ms.openlocfilehash: 4d7d7be523749797e5dbce0e50c307fc682974f2
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81427245"
---
# <a name="ingesting-data-into-azure-data-lake-storage-gen2"></a>Ingestion de données dans Azure Data Lake Storage Gen2 

Cet article explique comment ingérer des données d’un emplacement dans un autre, dans un compte de stockage Azure Data Lake Gen 2 à l’aide d’Azure Synapse Analytics.

## <a name="prerequisites"></a>Prérequis

* **Abonnement Azure** : Si vous n’avez pas d’abonnement Azure, créez un [compte Azure gratuit](https://azure.microsoft.com/free/) avant de commencer.
* **Compte Stockage Azure** : Vous utilisez Azure Data Lake Gen 2 en tant que magasin de données *source*. Si vous ne possédez pas de compte de stockage, procédez de la manière décrite dans l’article [Créer un compte de stockage Azure](../../storage/blobs/data-lake-storage-quickstart-create-account.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) pour en créer un.

## <a name="create-linked-services"></a>Créez des services liés

Dans Azure Synapse Analytics, un service lié vous permet de définir vos informations de connexion à d’autres services. Dans cette section, vous allez ajouter Azure Synapse Analytics et Azure Data Lake Gen 2 en tant que services liés.

1. Ouvrez l’expérience utilisateur Azure Synapse Analytics et accédez à l’onglet **Gérer**.
1. Sous **Connexions externes**, sélectionnez **Services liés**.
1. Pour ajouter un service lié, cliquez sur **Nouveau**.
1. Sélectionnez la vignette Azure Data Lake Storage Gen2 dans la liste, puis cliquez sur **Continuer**.
1. Entrez vos informations d’identification d’authentification. Les types d’authentification actuellement pris en charge sont les suivants : clé de compte, principal de service et identité managée. Cliquez sur Tester la connexion pour vérifier que vos informations sont correctes. 
1. Une fois que vous avez fini, cliquez sur **Créer**.

## <a name="create-pipeline"></a>Création d’un pipeline

Un pipeline contient le flux logique pour l’exécution d’un ensemble d’activités. Dans cette section, vous allez créer un pipeline contenant une activité de copie qui ingère des données de Azure Data Lake Gen 2 dans un pool SQL.

1. Accédez à l’onglet **Orchestrer**. Cliquez sur l’icône + en regard de l’en-tête Pipelines, puis sélectionnez **Pipeline**.
1. Dans le volet des activités, sous **Déplacer et transformer**, faites glisser **Copier les données** sur le canevas du pipeline.
1. Cliquez sur l’activité de copie, puis accédez à l’onglet **Source**. Cliquez sur **Nouveau** pour créer un jeu de données source.
1. Sélectionnez Azure Data Lake Storage Gen2 en tant que magasin de données, puis cliquez sur Continuer.
1. Sélectionnez DelimitedText comme format, puis cliquez sur Continuer.
1. Dans le volet Définir les propriétés, sélectionnez le service lié ADLS que vous avez créé. Spécifiez le chemin d’accès du fichier de vos données sources, puis spécifiez si la première ligne contient un en-tête. Vous pouvez importer le schéma à partir du magasin de fichiers ou d’un exemple de fichier. Une fois que vous avez fini, cliquez sur OK.
1. Accédez à l’onglet **Récepteur**. Cliquez sur **Nouveau** pour créer un jeu de données récepteur.
1. Sélectionnez Azure Data Lake Storage Gen2 en tant que magasin de données, puis cliquez sur Continuer.
1. Sélectionnez DelimitedText comme format, puis cliquez sur Continuer.
1. Dans le volet Définir les propriétés, sélectionnez le service lié ADLS que vous avez créé. Spécifiez le chemin d’accès du dossier dans lequel vous souhaitez écrire les données. Une fois que vous avez fini, cliquez sur OK.

## <a name="debug-and-publish-pipeline"></a>Déboguer et publier le pipeline

Une fois la configuration de votre pipeline terminée, avant de publier vos artefacts, vous pouvez exécuter un débogage pour vérifier que tout est correct.

1. Pour déboguer le pipeline, sélectionnez **Déboguer** dans la barre d’outils. L’état d’exécution du pipeline apparaît dans l’onglet **Sortie** au bas de la fenêtre. 
1. Une fois que le pipeline peut s’exécuter correctement, sélectionnez **Publier tout** dans la barre d’outils supérieure. Cette action publie les entités (jeux de données et pipelines) que vous avez créées dans le service Synapse Analytics.
1. Patientez jusqu’à voir le message **Publication réussie**. Pour afficher les messages de notification, cliquez sur le bouton en forme de cloche en haut à droite. 


## <a name="trigger-and-monitor-the-pipeline"></a>Déclencher et surveiller le pipeline

Au cours de cette étape, vous déclenchez manuellement le pipeline publié à l’étape précédente. 

1. Sélectionnez **Ajouter déclencheur** dans la barre d’outils, puis **Déclencher maintenant**. Dans la page **Exécution du pipeline**, sélectionnez **Terminer**.  
1. Accédez à l’onglet **Monitor** dans la barre latérale gauche. Vous voyez un pipeline qui est déclenché par un déclencheur manuel. Vous pouvez utiliser les liens dans la colonne **Actions** pour afficher les détails de l’activité et réexécuter le pipeline.
1. Pour afficher les exécutions d’activités associées à l’exécution du pipeline, sélectionnez le lien **Afficher les exécutions d’activités** dans la colonne **Actions**. Dans cet exemple, il n’y a qu’une seule activité, vous ne voyez donc qu’une seule entrée dans la liste. Pour plus de détails sur l’opération de copie, sélectionnez le lien **Détails** (icône en forme de lunettes) dans la colonne **Actions**. Sélectionnez **Exécutions de pipeline** au sommet de la page pour revenir à la vue des exécutions du pipeline. Sélectionnez **Actualiser** pour actualiser l’affichage.
1. Vérifiez que vos données sont correctement écrites dans le pool SQL.


## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’intégration de données pour Synapse Analytics, consultez l’article [Ingérer des données dans un pool SQL](data-integration-sql-pool.md).
