---
title: Considérations relatives au stockage pour Azure Functions
description: En savoir plus sur les exigences de stockage d’Azure Functions et sur le chiffrement des données stockées.
ms.topic: conceptual
ms.date: 01/21/2020
ms.openlocfilehash: 48ff2dedd997cccb76b13acdadc895504f656ea3
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/09/2020
ms.locfileid: "80984161"
---
# <a name="storage-considerations-for-azure-functions"></a>Considérations relatives au stockage pour Azure Functions

Azure Functions nécessite un compte Stockage Azure lorsque vous créez une instance d’application de fonction. Les services de stockage suivants peuvent être utilisés par votre application de fonction :


|Service de stockage  | Utilisation de Functions  |
|---------|---------|
| [stockage d’objets blob Azure](../storage/blobs/storage-blobs-introduction.md)     | Conserve l’état des liaisons et les touches de fonction.  <br/>Également utilisé par [des hubs de tâches dans Durable Functions](durable/durable-functions-task-hubs.md). |
| [Azure Files](../storage/files/storage-files-introduction.md)  | Partage de fichiers utilisé pour stocker et exécuter le code de votre application de fonction dans un [plan de consommation](functions-scale.md#consumption-plan). |
| [Stockage File d’attente Azure](../storage/queues/storage-queues-introduction.md)     | Utilisé par [des hubs de tâches dans Durable Functions](durable/durable-functions-task-hubs.md).   |
| [Stockage Table Azure](../storage/tables/table-storage-overview.md)  |  Utilisé par [des hubs de tâches dans Durable Functions](durable/durable-functions-task-hubs.md).       |

> [!IMPORTANT]
> Lorsque vous utilisez le plan d’hébergement de la consommation, les fichiers de code de fonction et de configuration de liaison sont stockés sur le Stockage Fichier Azure dans le compte de stockage principal. Lorsque vous supprimez le compte de stockage principal, ce contenu est supprimé et ne peut pas être récupéré.

## <a name="storage-account-requirements"></a>Conditions requises pour le compte de stockage

Quand vous créez une application de fonction, vous devez créer un compte de stockage Azure à usage général qui prend en charge le stockage Blob, File d’attente et Table, ou établir un lien vers un compte de ce type. Cela est dû au fait que Functions s’appuie sur Stockage Azure pour les opérations telles que la gestion des déclencheurs et la journalisation des exécutions de fonctions. Certains comptes de stockage ne prennent pas en charge les files d’attente ni les tables. Ces comptes incluent les comptes de stockage BLOB uniquement, le Stockage Premium Azure et les comptes de stockage universels avec la réplication ZRS. Ces comptes non pris en charge sont filtrés à partir du panneau Compte de stockage lors de la création d’une application de fonction.

Pour en savoir plus sur les types de compte de stockage, consultez [Présentation des services Stockage Azure](../storage/common/storage-introduction.md#core-storage-services). 

Bien que vous puissiez utiliser un compte de stockage existant avec votre application de fonction, vous devez vous assurer qu’il répond à ces exigences. Les comptes de stockage créés dans le cadre du flux de création de l’application de fonction sont assurés de répondre à ces exigences en matière de comptes de stockage.  

## <a name="storage-account-guidance"></a>Guide du compte de stockage

Chaque application de fonction nécessite un compte de stockage afin de fonctionner. Si ce compte est supprimé, votre application de fonction ne s’exécutera pas. Pour détecter un problème lié au stockage, consultez [Comment résoudre les problèmes liés au stockage](functions-recover-storage-account.md). Les autres considérations suivantes s’appliquent au compte de stockage utilisé par les applications de fonction.

### <a name="storage-account-connection-setting"></a>Paramètre de connexion au compte de stockage

La connexion au compte de stockage est conservée dans le [paramètre d’application AzureWebJobsStorage](./functions-app-settings.md#azurewebjobsstorage). 

La chaîne de connexion du compte de stockage doit être mise à jour lorsque vous régénérez des clés de stockage. [En savoir plus sur la gestion des clés de stockage ici](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account).

### <a name="shared-storage-accounts"></a>Comptes de stockage partagés

Plusieurs applications de fonction peuvent partager le même compte de stockage sans aucun problème. Par exemple, dans Visual Studio, vous pouvez développer plusieurs applications à l’aide de l’émulateur Stockage Azure. Dans ce cas, l’émulateur agit comme un compte de stockage unique. Le même compte de stockage que celui utilisé par votre application de fonction peut également être utilisé pour stocker vos données d’application. Toutefois, cette approche n’est pas toujours recommandée dans un environnement de production.

### <a name="optimize-storage-performance"></a>Optimiser les performances de stockage

[!INCLUDE [functions-shared-storage](../../includes/functions-shared-storage.md)]

## <a name="storage-data-encryption"></a>Chiffrement des données de stockage

Le stockage Azure chiffre toutes les données dans un compte de stockage au repos. Pour plus d'informations, consultez [Fonctionnalité de chiffrement du service Stockage Azure pour les données au repos](../storage/common/storage-service-encryption.md).

Par défaut, les données sont chiffrées avec des clés managées par Microsoft Pour plus de contrôle sur les clés de chiffrement, vous pouvez fournir des clés gérées par le client à utiliser pour le chiffrement des données de fichiers et d’objets blob. Ces clés doivent être présentes dans Azure Key Vault pour que Functions puisse accéder au compte de stockage. Pour en savoir plus, consultez [Configurer des clés gérées par le client avec Azure Key Vault en utilisant le Portail Azure](../storage/common/storage-encryption-keys-portal.md).  

## <a name="mount-file-shares-linux"></a>Monter des partages de fichiers (Linux)

Vous pouvez monter des partages Azure Files existants dans vos applications de fonction Linux. Monter un partage vers votre application de fonction Linux vous permet d’accéder à des modèles Machine Learning existants ou à d’autres données dans vos fonctions. Vous pouvez utiliser la commande [`az webapp config storage-account add`](/cli/azure/webapp/config/storage-account#az-webapp-config-storage-account-add) pour monter un partage existant dans votre application de fonction Linux. 

Dans cette commande, `share-name` est le nom du partage Azure Files existant, et `custom-id` peut représenter n’importe quelle chaîne qui définit de façon unique le partage lorsqu’il est monté sur l’application de fonction. En outre, `mount-path` est le chemin d’accès à partir duquel le partage est accessible dans votre application de fonction. `mount-path` doit être au format `/dir-name`, et ne peut pas commencer par `/home`.

Pour obtenir un exemple complet, consultez les scripts dans [Créer une application de fonction Python et monter un partage Azure Files](scripts/functions-cli-mount-files-storage-linux.md). 

Actuellement, seul un stockage `storage-type` de type `AzureFiles` est pris en charge. Vous pouvez uniquement monter cinq partages sur une application de fonction donnée. Le montage d’un partage de fichiers peut augmenter le temps de démarrage à froid d’au moins 200 à 300 ms voire plus si le compte de stockage se trouve dans une autre région.

Le partage monté est disponible pour votre code de fonction à l’emplacement `mount-path` spécifié. Par exemple, lorsque `mount-path` est `/path/to/mount`, vous pouvez accéder au répertoire cible à l’aide des API du système de fichiers, comme dans l’exemple Python suivant :

```python
import os
...

files_in_share = os.listdir("/path/to/mount")
```

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les options d’hébergement d’Azure Functions.

> [!div class="nextstepaction"]
> [Échelle et hébergement dans Azure Functions](functions-scale.md)


