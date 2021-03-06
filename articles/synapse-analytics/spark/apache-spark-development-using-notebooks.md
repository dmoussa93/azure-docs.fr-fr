---
title: Créer, développer et tenir à jour des blocs-notes Azure Synapse Studio (préversion)
description: Cet article explique comment créer et développer des blocs-notes Azure Synapse Studio (préversion) pour la préparation et la visualisation de données.
services: synapse analytics
author: ruixinxu
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: ruxu
ms.reviewer: ''
ms.openlocfilehash: 506339cefa90fb17bedfc946f70cb4d7d8047cf2
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81427137"
---
# <a name="create-develop-and-maintain-azure-synapse-studio-preview-notebooks"></a>Créer, développer et tenir à jour des blocs-notes Azure Synapse Studio (préversion)

Un bloc-notes Azure Synapse Studio (préversion) est une interface web permettant de créer des fichiers contenant du code, des visualisations et du texte descriptif en direct. Les blocs-notes constituent un bon endroit où valider des idées et effectuer des expérimentations rapides pour extraire des insights de vos données. Les blocs-notes sont également largement utilisés pour la préparation et la visualisation de données, l’apprentissage automatique et d’autres scénarios en lien avec le Big Data.

Un bloc-notes Azure Synapse Studio permet d’effectuer les opérations suivantes :

* Commencer à travailler sans le moindre effort de configuration.
* Sécuriser les données avec des fonctionnalités de sécurité d’entreprise intégrées.
* Analyser des données dans des formats bruts (CSV, txt, JSON, etc.), des formats de fichiers traités (Parquet, Delta Lake, ORC, etc.) et des fichiers de données tabulaires SQL sur Spark et SQL.
* Être productif grâce à des fonctionnalités de création améliorées et à la visualisation de données intégrée.

Cet article explique comment utiliser des blocs-notes dans Azure Synapse Studio.

## <a name="create-a-notebook"></a>Créer un notebook

Il existe deux façons de créer un bloc-notes. Vous pouvez créer un bloc-notes ou en importer un dans un espace de travail Azure Synapse à partir de l’**Explorateur d’objets**. Les blocs-notes Azure Synapse Studio peuvent reconnaître des fichiers IPYNB de bloc-notes Jupyter standard.

![synapse-créer-importer-bloc-notes](./media/apache-spark-development-using-notebooks/synapse-create-import-notebook.png)

## <a name="develop-notebooks"></a>Développer des blocs-notes

Les blocs-notes sont constitués de cellules qui sont des blocs individuels de code ou de texte qui peuvent être exécutés de façon indépendante ou en tant que groupe.

### <a name="add-a-cell"></a>Ajouter une cellule

Il existe plusieurs façons d’ajouter une cellule à un bloc-notes.

1. Développez le bouton supérieur gauche **+ Cellule**, puis sélectionnez **Ajouter une cellule de code** ou **Ajouter une cellule de texte**.

    ![ajouter-cellule-avec-bouton-cellule](./media/apache-spark-development-using-notebooks/synapse-add-cell-1.png)

2. Pointez sur l’espace entre deux cellules, puis sélectionnez **Ajouter du code** ou **Ajouter du texte**.

    ![ajouter-cellule-entre-espace](./media/apache-spark-development-using-notebooks/synapse-add-cell-2.png)

3. Utilisez les [touches de raccourci en mode de commande](#shortcut-keys-under-command-mode). Appuyez sur **A** pour insérer une cellule au-dessus de la cellule active. Appuyez sur **B** pour insérer une cellule en dessous de la cellule active.

### <a name="set-a-primary-language"></a>Définir un langage principal

Les blocs-notes Azure Synapse Studio prennent en charge quatre langages Spark :

* pyspark (Python)
* spark (Scala)
* sparkSQL
* Spark.NET (C#)

Vous pouvez définir le langage principal des nouvelles cellules ajoutées dans la liste déroulante de la barre de commandes supérieure.

   ![langage-Synapse-par-défaut](./media/apache-spark-development-using-notebooks/synapse-default-language.png)

### <a name="use-multiple-languages"></a>Utiliser plusieurs langages

Vous pouvez utiliser plusieurs langages dans un même bloc-notes en spécifiant la commande magic du langage approprié au début d’une cellule. Le tableau suivant répertorie les commandes magic pour basculer les langages des cellules.

|Commande magic |Langage | Description |  
|---|------|-----|
|%%pyspark| Python | Exécuter une requête **Python** sur du contexte Spark.  |
|%%spark| Scala | Exécuter une requête **Scala** sur du contexte Spark.  |  
|%%sql| SparkSQL | Exécuter une requête **SparkSQL** sur du contexte Spark.  |
|%%csharp | Spark.NET C# | Exécutez une requête **Spark.NET C#** sur du contexte Spark. |

L’image suivante illustre la façon d’écrire une requête PySpark avec la commande magic **%%PySpark**, ou une requête SparkSQL avec la commande magic **%%sql** dans un bloc-notes **Spark(Scala)** . Notez que le langage principal du bloc-notes est défini sur Scala.

   ![synapse-spark-magic](./media/apache-spark-development-using-notebooks/synapse-spark-magics.png)

### <a name="use-temp-tables-to-reference-data-across-languages"></a>Utiliser des tables temporaires pour référencer des données dans plusieurs langages

Vous ne pouvez pas référencer des données ou variables directement dans différents langages dans un bloc-notes Synapse Studio. Dans Spark, une table temporaire peut être référencée dans plusieurs langages. Voici un exemple de lecture d’une tramedonnées `Scala` en `PySpark` et `SparkSQL` en utilisant une table temporaire Spark comme solution de contournement.

1. Dans la cellule 1, lisez une tramedonnées à partir du connecteur de pool SQL en utilisant Scala, puis créez une table temporaire.

   ```scala
   %%scala
   val scalaDataFrame = spark.read.option("format", "DW connector predefined type")
   scalaDataFrame.registerTempTable( "mydataframetable" )
   ```

2. Dans la cellule 2, interrogez les données en utilisant Spark SQL.
   
   ```sql
   %%sql
   SELECT * FROM mydataframetable
   ```

3. Dans la cellule 3, utilisez les données dans PySpark.

   ```python
   %%pyspark
   myNewPythonDataFrame = spark.sql("SELECT * FROM mydataframetable")
   ```

### <a name="ide-style-intellisense"></a>IntelliSense de style IDE

Des blocs-notes Azure Synapse Studio sont intégrés avec l’éditeur de Monaco pour intégrer IntelliSense de style IDE à l’éditeur de cellule. Une mise en évidence de la syntaxe, un marqueur d’erreurs et des saisies semi-automatiques de code vous aident à écrire le code et à identifier les problèmes plus rapidement.

Les fonctionnalités IntelliSense sont à des niveaux de maturité différents pour les différents langages. Utilisez le tableau ci-dessous pour voir ce qui est pris en charge.

|Languages| Mise en évidence de la syntaxe | Marqueur d’erreur de syntaxe  | Saisie semi-automatique de code de syntaxe | Saisie semi-automatique de code variable| Saisie semi-automatique de code de fonction système| Saisie semi-automatique de code de fonction utilisateur| Mise en retrait intelligente | Pliage de code|
|--|--|--|--|--|--|--|--|--|
|PySpark (Python)|Oui|Oui|Oui|Oui|Oui|Oui|Oui|Oui|
|Spark (Scala)|Oui|Oui|Oui|Oui|-|-|-|Oui|
|SparkSQL|Oui|Oui|-|-|-|-|-|-|
|Spark.NET (C#)|Oui|-|-|-|-|-|-|-|

### <a name="format-text-cell-with-toolbar-buttons"></a>Mettre en forme une cellule de texte avec des boutons de barre d’outils

Vous pouvez utiliser les boutons de mise en forme dans la barre d’outils des cellules de texte pour effectuer des actions de markdown (démarquage) courantes. Celles-ci incluent la mise en gras et en italique de texte, l’insertion d’extraits de code, l’insertion de liste non triée, l’insertion de liste triée et l’insertion d’image à partir d’une URL.

  ![Synapse-texte-cellule-barre d’outils](./media/apache-spark-development-using-notebooks/synapse-text-cell-toolbar.png)

### <a name="undo-cell-operations"></a>Annuler des opérations sur cellule
Cliquez sur le bouton **Annuler** ou appuyez sur **Ctrl + Z** pour révoquer l’opération sur cellule la plus récente. Vous pouvez désormais annuler jusqu’aux 20 dernières actions sur cellule. 

   ![synapse-annuler-cellules](./media/apache-spark-development-using-notebooks/synapse-undo-cells.png)

### <a name="move-a-cell"></a>Déplacer une cellule

Sélectionnez les points de suspension (...) pour accéder au menu d’actions sur cellule supplémentaires tout à fait à droite. Sélectionnez ensuite **Déplacer la cellule vers le haut** ou **Déplacer la cellule vers le bas** pour déplacer la cellule active. 

Vous pouvez également utiliser des [touches de raccourci en mode de commande](#shortcut-keys-under-command-mode). Appuyez sur **Ctrl + Alt + ↑** pour déplacer la cellule active vers le haut. Appuyez sur **Ctrl + Alt + ↓** pour déplacer la cellule active vers le bas.

   ![déplacer-un-cellule](./media/apache-spark-development-using-notebooks/synapse-move-cells.png)

### <a name="delete-a-cell"></a>Supprimer une cellule

Pour supprimer une cellule, sélectionnez les points de suspension (...) pour accéder au menu d’actions sur cellule supplémentaires tout à fait à droite, puis choisissez **Supprimer la cellule**. 

Vous pouvez également utiliser des [touches de raccourci en mode de commande](#shortcut-keys-under-command-mode). Appuyez sur **D, D** pour supprimer la cellule active.
  
   ![supprimer-une-cellule](./media/apache-spark-development-using-notebooks/synapse-delete-cell.png)

### <a name="collapse-a-cell-input"></a>Réduire une entrée de cellule
Cliquez sur le bouton fléché en bas de la cellule active pour la réduire. Pour l’agrandir, cliquez sur le bouton fléché quand elle est réduite.

   ![réduire-cellule-entrée](./media/apache-spark-development-using-notebooks/synapse-collapse-cell-input.gif)

### <a name="collapse-a-cell-output"></a>Réduire une sortie de cellule

Cliquez sur le bouton **Réduire la sortie** en haut à gauche de la sortie de cellule active pour la réduire. Pour l’agrandir, cliquez sur **Afficher la sortie de cellule** quand la sortie de cellule est réduite.

   ![réduire-cellule-sortie](./media/apache-spark-development-using-notebooks/synapse-collapse-cell-output.gif)

## <a name="run-notebooks"></a>Exécuter des blocs-notes

Vous pouvez exécuter les cellules de code dans votre bloc-notes individuellement ou toutes en même temps. L’état et la progression de chaque cellule sont représentés dans le bloc-notes.

### <a name="run-a-cell"></a>Exécuter une cellule

Il existe plusieurs façons d’exécuter le code figurant dans une cellule.

1. Pointez sur la cellule à exécuter, puis sélectionnez le bouton **Exécuter la cellule** ou appuyez sur **Ctrl + Entrée**.

   ![exécuter-cellule-1](./media/apache-spark-development-using-notebooks/synapse-run-cell.png)


2. Pour accéder au menu d’actions sur cellule supplémentaires tout à fait à droite, sélectionnez les points de suspension ( **...** ). Ensuite, sélectionnez **Exécuter la cellule**.

   ![exécuter-cellule-2](./media/apache-spark-development-using-notebooks/synapse-run-cell-2.png)
   
3. Utilisez les [touches de raccourci en mode de commande](#shortcut-keys-under-command-mode). Appuyez sur **Maj + Entrée** pour exécuter la cellule active et sélectionner la cellule en dessous. Appuyez sur **Alt + Entrée** pour exécuter la cellule active et insérer une nouvelle cellule en dessous.


### <a name="run-all-cells"></a>Exécuter toutes les cellules
Cliquez sur le bouton **Exécuter tout** pour exécuter toutes les cellules du bloc-notes actuel dans l’ordre.

   ![exécuter-tout-cellules](./media/apache-spark-development-using-notebooks/synapse-run-all.png)

### <a name="run-all-cells-above-or-below"></a>Exécuter toutes les cellules au-dessus ou en dessous

Pour accéder au menu d’actions sur cellule supplémentaires tout à fait à droite, sélectionnez les points de suspension ( **...** ). Ensuite, sélectionnez **Exécuter les cellules au-dessus** pour exécuter toutes les cellules situées au-dessus de la cellule active dans l’ordre. Sélectionnez **Exécuter les cellules en dessous** pour exécuter toutes les cellules sous la cellule active dans l’ordre.

   ![exécuter-cellules-au-dessus-ou-en dessous](./media/apache-spark-development-using-notebooks/synapse-run-cells-above-or-below.png)


### <a name="cell-status-indicator"></a>Indicateur d’état de cellule

Un état d’exécution de cellule pas à pas est affiché sous la cellule pour vous aider à voir la progression en cours. Une fois l’exécution de la cellule terminée, un résumé de l’exécution avec la durée totale et l’heure de fin sont affichés et conservés là pour référence future.

![état-cellule](./media/apache-spark-development-using-notebooks/synapse-cell-status.png)

### <a name="spark-progress-indicator"></a>Indicateur de progression Spark

Le bloc-notes Azure Synapse Studio est entièrement basé sur Spark. Les cellules de code sont exécutées sur le pool Spark à distance. Un indicateur de progression du travail Spark est fourni avec une barre de progression en temps réel qui s’affiche pour vous aider à comprendre l’état d’exécution du travail.


![spark-indicateur-progression](./media/apache-spark-development-using-notebooks/synapse-spark-progress-indicator.png)

### <a name="spark-session-config"></a>Configuration de session Spark

Vous pouvez spécifier le délai d’expiration, le nombre et la taille des exécuteurs à transmettre à la session Spark en cours dans **Configurer la session**. Redémarrez la session Spark pour que les modifications apportées à la configuration prennent effet. Toutes les variables du bloc-notes mises en cache sont effacées.

![gestion-session](./media/apache-spark-development-using-notebooks/synapse-spark-session-mgmt.png)


## <a name="bring-data-to-a-notebook"></a>Importer des données dans un bloc-notes

Vous pouvez charger des données à partir du service Stockage Blob Azure, d’Azure Data Lake Store Gén. 2 et d’un pool SQL, comme illustré dans les exemples de code ci-dessous.

### <a name="read-a-csv-from-azure-data-lake-store-gen2-as-a-spark-dataframe"></a>Lire un fichier CSV à partir d’Azure Data Lake Store Gén. 2 en tant que tramedonnées Spark

```python
from pyspark.sql import SparkSession
from pyspark.sql.types import *
account_name = "Your account name"
container_name = "Your container name"
relative_path = "Your path"
adls_path = 'abfss://%s@%s.dfs.core.windows.net/%s' % (blob_container_name, blob_account_name,  blob_relative_path)

spark.conf.set("fs.azure.account.auth.type.%s.dfs.core.windows.net" %account_name, "SharedKey")
spark.conf.set("fs.azure.account.key.%s.dfs.core.windows.net" %account_name ,"Your ADLSg2 Primary Key")

df1 = spark.read.option('header', 'true') \
                .option('delimiter', ',') \
                .csv(adls_path + '/Testfile.csv')

```

#### <a name="read-a-csv-from-azure-blob-storage-as-a-spark-dataframe"></a>Lire un fichier CSV à partir du service Stockage Blob Azure en tant que tramedonnées Spark

```python

from pyspark.sql import SparkSession
from pyspark.sql.types import *

blob_account_name = "Your blob account name"
blob_container_name = "Your blob container name"
blob_relative_path = "Your blob relative path"
blob_sas_token = "Your blob sas token"

wasbs_path = 'wasbs://%s@%s.blob.core.windows.net/%s' % (blob_container_name, blob_account_name, blob_relative_path)
spark.conf.set('fs.azure.sas.%s.%s.blob.core.windows.net' % (blob_container_name, blob_account_name), blob_sas_token)

df = spark.read.option("header", "true") \
            .option("delimiter","|") \
            .schema(schema) \
            .csv(wasbs_path)

```

### <a name="read-data-from-the-primary-storage-account"></a>Lire des données à partir du compte de stockage principal

Vous pouvez accéder aux données directement dans le compte de stockage principal. Il n’est pas nécessaire de fournir les clés secrètes. Dans l’Explorateur de données, cliquez avec le bouton droit sur un fichier, puis sélectionnez **Nouveau bloc-notes** pour afficher un nouveau bloc-notes avec l’extracteur de données généré automatiquement.

![données-à-cellule](./media/apache-spark-development-using-notebooks/synapse-data-to-cell.png)

## <a name="visualize-data-in-a-notebook"></a>Visualiser des données dans un bloc-notes

### <a name="display"></a>Display()

Une vue des résultats tabulaire est fournie avec l’option permettant de créer un graphique à barres, un graphique en courbes, un graphique en secteurs, un graphique en nuages de points et un graphique en aires. Vous pouvez visualiser vos données sans devoir écrire de code. Vous pouvez personnaliser les graphiques dans les **options de graphique**. 

La sortie des commandes magic **%%sql** s’affiche par défaut dans l’affichage Table rendu. Vous pouvez appeler la commande **display(`<DataFrame name>`)** sur des tramedonnées Spark ou la fonction RDD (Resilient Distributed Datasets) pour produire l’affichage Table rendu.

   ![graphiques-intégrés](./media/apache-spark-development-using-notebooks/synapse-builtin-charts.png)

### <a name="displayhtml"></a>DisplayHTML()

Vous pouvez afficher des bibliothèques HTML ou interactives, telle **bokeh**, en utilisant la commande **displayHTML()** .

L’image suivante est un exemple de traçage de glyphes sur une carte en utilisant **bokeh**.

   ![bokeh-exemple](./media/apache-spark-development-using-notebooks/synapse-bokeh-image.png)
   

Exécutez l’exemple de code suivant pour dessiner l’image ci-dessus.

```python
from bokeh.plotting import figure, output_file
from bokeh.tile_providers import get_provider, Vendors
from bokeh.embed import file_html
from bokeh.resources import CDN
from bokeh.models import ColumnDataSource

tile_provider = get_provider(Vendors.CARTODBPOSITRON)

# range bounds supplied in web mercator coordinates
p = figure(x_range=(-9000000,-8000000), y_range=(4000000,5000000),
           x_axis_type="mercator", y_axis_type="mercator")
p.add_tile(tile_provider)

# plot datapoints on the map
source = ColumnDataSource(
    data=dict(x=[ -8800000, -8500000 , -8800000],
              y=[4200000, 4500000, 4900000])
)

p.circle(x="x", y="y", size=15, fill_color="blue", fill_alpha=0.8, source=source)

# create an html document that embeds the Bokeh plot
html = file_html(p, CDN, "my plot1")

# display this html
displayHTML(html)

```

## <a name="save-notebooks"></a>Enregistrer des blocs-notes

Vous pouvez enregistrer un seul bloc-notes ou tous les blocs-notes dans votre espace de travail.

1. Pour enregistrer les modifications apportées à un seul bloc-notes, sélectionnez le bouton **Publier** dans la barre de commandes du bloc-notes.

   ![publier-bloc-notes](./media/apache-spark-development-using-notebooks/synapse-publish-notebook.png)

2. Pour enregistrer tous les blocs-notes dans votre espace de travail, sélectionnez le bouton **Publier tout** dans la barre de commandes de l’espace de travail. 

   ![publier-tout](./media/apache-spark-development-using-notebooks/synapse-publish-all.png)

Dans les propriétés du bloc-notes, vous pouvez éventuellement configurer l’inclusion de la sortie de cellule lors de l’enregistrement.

   ![bloc-notes-propriétés](./media/apache-spark-development-using-notebooks/synapse-notebook-properties.png)

## <a name="magic-commands"></a>Commandes magic
Vous pouvez utiliser vos commandes magic Jupyter familières dans les blocs-notes Azure Synapse Studio. Vérifiez la liste ci-dessous des commandes magic actuellement disponibles. Parlez-nous de vos cas d’utilisation sur GitHub pour nous permettre de continuer à créer des commandes magic supplémentaires afin de répondre à vos besoins.

Commandes magic de ligne disponibles : [%lsmagic](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-lsmagic), [%time](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-time), [%timeit](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-timeit)

Commandes magic de cellule disponibles : [%%time](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-time), [%%timeit](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-timeit), [%%capture](https://ipython.readthedocs.io/en/stable/interactive/magics.html#cellmagic-capture), [%%writefile](https://ipython.readthedocs.io/en/stable/interactive/magics.html#cellmagic-writefile), [%%sql](#use-multiple-languages), [%%pyspark](#use-multiple-languages), [%%spark](#use-multiple-languages), [%%csharp](#use-multiple-languages)

## <a name="shortcut-keys"></a>Touches de raccourci

À l’instar des blocs-notes Jupyter, les blocs-notes Azure Synapse Studio disposent d’une interface utilisateur modale. Le clavier effectue des actions différentes selon le mode dans lequel se trouve la cellule du bloc-notes. Les blocs-notes Synapse Studio prennent en charge les deux modes suivants pour une cellule de code donnée : le mode de commande et le mode d’édition.

1. Une cellule est en mode de commande quand elle n’affiche aucun curseur texte vous invitant à saisir. Quand une cellule est en mode de commande, vous pouvez modifier le bloc-notes entier, mais pas taper dans des cellules individuelles. Entrez en mode de commande en appuyant sur `ESC` ou en utilisant la souris pour cliquer en dehors de la zone de l’éditeur d’une cellule.

   ![mode-commande](./media/apache-spark-development-using-notebooks/synapse-command-mode2.png)

2. Le mode d’édition est indiqué par un curseur texte qui vous invite à taper dans la zone de l’éditeur. Quand une cellule est en mode d’édition, vous pouvez pas saisir dans la cellule. Entrez en mode édition en appuyant sur `Enter` ou en utilisant la souris pour cliquer sur la zone de l’éditeur d’une cellule.
   
   ![mode-édition](./media/apache-spark-development-using-notebooks/synapse-edit-mode2.png)

### <a name="shortcut-keys-under-command-mode"></a>Touches de raccourci en mode de commande

Les raccourcis clavier suivants vous permettent de parcourir et d’exécuter plus facilement du code dans des blocs-notes Azure Synapse.

| Action |Raccourcis de bloc-notes Synapse Studio  |
|--|--|
|Exécuter la cellule active et sélectionner la cellule en dessous | Maj + Entrée |
|Exécuter la cellule active et insérer en dessous | Alt + Entrée |
|Sélectionner la cellule au-dessus| Haut |
|Sélectionner la cellule en dessous| Bas |
|Insérer une cellule au-dessus| Un |
|Insérer une cellule en dessous| B |
|Étendre les cellules sélectionnées au-dessus| Maj + Haut |
|Étendre les cellules sélectionnées en dessous| Maj + Bas|
|Déplacer la cellule vers le haut| Ctrl + Alt + ↑ |
|Déplacer la cellule vers le bas| Ctrl + Alt + ↓ |
|Supprimer les cellules sélectionnées| D, D |
|Basculer en mode d’édition| Entrez |

### <a name="shortcut-keys-under-edit-mode"></a>Touches de raccourci en mode d’édition

Les raccourcis clavier suivants vous permettent de naviguer et d’exécuter du code plus facilement dans des blocs-notes Azure Synapse en mode d’édition.

| Action |Raccourcis de bloc-notes Synapse Studio  |
|--|--|
|Déplacer le curseur vers le haut | Haut |
|Déplacer le curseur vers le bas|Bas|
|Annuler|Ctrl + Z|
|Rétablir|CTRL + Y|
|Commenter/Supprimer un commentaire|Ctrl + /|
|Supprimer le mot précédent|Ctrl + Retour arrière|
|Supprimer le mot suivant|Ctrl + Suppr|
|Atteindre le début de la cellule|Ctrl + Début|
|Atteindre la fin de la cellule |Ctrl + Fin|
|Atteindre le mot à gauche|CTRL + Gauche|
|Atteindre le mot à droite|Ctrl + Droite|
|Sélectionner tout|Ctrl + A|
|Retrait| Ctrl + ]|
|Retrait négatif|Ctrl + [|
|Passer en mode de commande| Échap |

## <a name="next-steps"></a>Étapes suivantes

- [Documentation .NET pour Apache Spark](/dotnet/spark?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
