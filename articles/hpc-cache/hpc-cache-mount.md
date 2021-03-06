---
title: Monter un cache Azure HPC Cache
description: Comment connecter des clients à un service Azure HPC Cache
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 04/03/2020
ms.author: rohogue
ms.openlocfilehash: f176e30cfaf9a52e4f58091b7fc76098a4c88a48
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80657352"
---
# <a name="mount-the-azure-hpc-cache"></a>Monter le cache Azure HPC Cache

Une fois le cache créé, les clients NFS peuvent y accéder à l’aide d’une simple commande `mount`. La commande connecte un chemin d’accès cible de stockage spécifique sur le service Azure HPC Cache à un répertoire local sur la machine cliente.

La commande Mount est composée des éléments suivants :

* L’une des adresses de montage du cache (figurant sur la page de vue d’ensemble du cache)
* Le chemin d’accès à l’espace de noms virtuel que vous définissez lors de la création de la cible de stockage
* Chemin d’accès local à utiliser sur le client
* Paramètres de commande qui optimisent la réussite de ce type de montage NFS

La page **Instructions de montage** pour votre cache collecte les informations et les options recommandées pour vous, et crée une commande de montage prototype que vous pouvez copier. Pour plus d’informations, consultez [Utiliser l’utilitaire d’instructions de montage](#use-the-mount-instructions-utility).

## <a name="prepare-clients"></a>Préparer les clients

Assurez-vous que vos clients sont en mesure de monter le service Azure HPC Cache en suivant les instructions de cette section.

### <a name="provide-network-access"></a>Fournir un accès réseau

Les ordinateurs clients doivent disposer d’un accès réseau au réseau virtuel et au sous-réseau privé du cache.

Par exemple, créez des machines virtuelles clientes dans le même réseau virtuel, ou utilisez un point de terminaison, une passerelle ou une autre solution dans le réseau virtuel pour permettre un accès en dehors du réseau (n’oubliez pas que rien d’autre que le cache proprement dit ne doit être hébergé dans le sous-réseau du cache).

### <a name="install-utilities"></a>Installer les utilitaires

Installez le logiciel de l’utilitaire Linux approprié pour prendre en charge la commande de montage NFS :

* Pour Red Hat Enterprise Linux ou SuSE : `sudo yum install -y nfs-utils`
* Pour Ubuntu ou Debian : `sudo apt-get install nfs-common`

### <a name="create-a-local-path"></a>Créer un chemin d’accès local

Créez un chemin d’accès de répertoire local sur chaque client pour la connexion au cache. Créez un chemin d’accès pour chaque cible de stockage que vous souhaitez monter.

Exemple : `sudo mkdir -p /mnt/hpc-cache-1/target3`

## <a name="use-the-mount-instructions-utility"></a>Utiliser l’utilitaire d’instructions de montage

Ouvrez la page **Instructions de montage** à partir de la section **Configurer** de l’affichage du cache dans le portail Azure.

![Capture d’écran d’une instance Azure HPC Cache dans le portail, avec la page Configurer > Instructions de montage chargée](media/mount-instructions.png)

La page de commande de montage contient des informations sur le processus et les conditions préalables de montage du client, ainsi que des champs que vous pouvez utiliser pour créer une commande de montage copiable.

Pour utiliser cette page, procédez comme suit :

<!--1.  In step one of **Mounting your file system**, enter the path that the client will use to access the Azure HPC Cache storage target.

   * This path is local to the client.
   * After you provide the directory name, the field populates with a command you can copy. Use this command on the client directly or in a setup script to create the directory path on the client VM. -->

1. Passez en revue les conditions préalables du client et installez les utilitaires nécessaires pour utiliser la commande NFS `mount` comme décrit ci-dessus dans [Préparer les clients](#prepare-clients).

1. L’étape 1 de **montage de votre système de fichiers**<!-- label will change --> fournit un exemple de commande pour créer le chemin d’accès local sur le client. Il s’agit du chemin d’accès que le client utilisera pour accéder au contenu à partir du service Azure HPC Cache.

   Notez le nom du chemin d’accès afin de pouvoir le modifier dans la commande si nécessaire.

1. À l’étape 2, sélectionnez l’une des adresses IP disponibles. Tous les [points de montage du client](#find-mount-command-components) du cache sont répertoriés ici. Vérifiez que vous disposez d’un système pour équilibrer la charge entre toutes les adresses IP.

1. Le champ de l’étape 3 est automatiquement renseigné avec une commande de montage prototype. Cliquez sur le symbole de copie à droite du champ pour le copier automatiquement le contenu de celui-ci dans le Presse-papiers.

   > [!NOTE]
   > Vérifiez la commande de copie avant de l’utiliser. Il se peut que vous deviez personnaliser le chemin d’accès du montage du client et le chemin d’accès de l’espace de noms virtuel de la cible de stockage, qui ne sont pas encore sélectionnables dans cette interface. Vous devez également mettre à jour les options de commande de montage afin de refléter les [options recommandées](#mount-command-options) ci-dessous. Pour obtenir de l’aide, lisez [Comprendre la syntaxe de la commande de montage](#understand-mount-command-syntax).

1. Utilisez la commande de montage copiée (avec des modifications, si nécessaire) sur la machine cliente pour le connecter à la cible de stockage sur le service Azure HPC Cache. Vous pouvez émettre la commande directement à partir de la ligne de commande du client ou inclure la commande de montage dans un modèle ou un script d’installation de client.

## <a name="understand-mount-command-syntax"></a>Comprendre la syntaxe de la commande de montage

La commande de montage (mount) se présente sous la forme suivante :

> sudo mount *adresse_montage_cache*:/*chemin_espace_de_noms* *chemin_local* {*options*}

Exemple :

```bash
root@test-client:/tmp# mkdir hpccache
root@test-client:/tmp# sudo mount 10.0.0.28:/blob-demo-0722 ./hpccache/ -o hard,proto=tcp,mountproto=tcp,retry=30
root@test-client:/tmp#
```

Si l’exécution de cette commande réussit, le contenu de l’exportation de stockage doit être visible dans le répertoire ``hpccache`` sur le client.

### <a name="mount-command-options"></a>Options de la commande mount

Pour garantir un montage robuste du client, passez les paramètres et arguments suivants dans votre commande mount :

> mount -o hard,proto=tcp,mountproto=tcp,retry=30 ${CACHE_IP_ADDRESS}:/${NAMESPACE_PATH} ${LOCAL_FILESYSTEM_MOUNT_POINT}

| Paramètres recommandés pour la commande mount | |
--- | ---
``hard`` | Les montages conditionnels sur un cache Azure HPC Cache sont associés à des échecs d’application et à une perte possible de données.
``proto=tcp`` | Cette option prend en charge la gestion appropriée des erreurs réseau NFS.
``mountproto=tcp`` | Cette option prend en charge la gestion appropriée des erreurs réseau pour les opérations de montage.
``retry=<value>`` | Définissez ``retry=30`` pour éviter les échecs de montage temporaires. (Une valeur différente est recommandée dans les montages de premier plan.)

### <a name="find-mount-command-components"></a>Rechercher les composants de la commande de montage

Si vous souhaitez créer une commande de montage sans utiliser la page **Instructions de montage**, vous pouvez trouver les adresses de montage dans la page **Vue d’ensemble** du cache, et les chemins d’espaces de noms virtuels sur la page **Cibles de stockage**.

![Capture d’écran de la page Vue d’ensemble de l’instance Azure HPC Cache, avec la liste des adresses de montage mise en surbrillance en bas à droite](media/hpc-cache-mount-addresses.png)

> [!NOTE]
> Les adresses de montage du cache correspondent aux interfaces réseau du sous-réseau du cache. Dans un groupe de ressources, ces cartes réseau sont répertoriées avec des noms se terminant par `-cluster-nic-` et un nombre. Vous ne devez ni modifier ni supprimer ces interfaces, car cela rendrait le cache indisponible.

Les chemins d’espaces de noms virtuels sont affichés dans la page **Cibles de stockage**. Cliquez sur un nom de cible de stockage pour afficher ses détails, dont les chemins d’accès de l’espace de noms agrégé qui y sont associés.

![Capture d’écran du panneau Cible de stockage du cache, avec une entrée mise en surbrillance dans la colonne Chemin de la table](media/hpc-cache-view-namespace-paths.png)

## <a name="next-steps"></a>Étapes suivantes

* Pour déplacer des données vers les cibles de stockage du cache, consultez [Remplir de données un nouveau stockage Blob Azure](hpc-cache-ingest.md).
