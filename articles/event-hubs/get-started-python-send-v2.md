---
title: Recevoir des événements d’Azure Event Hubs ou lui en envoyer en utilisant Python (dernière version)
description: Cet article décrit la procédure à suivre pour créer une application Python qui reçoit des événements d’Azure Event Hubs et lui en envoie à l’aide du dernier package azure-eventhub version 5.
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.workload: core
ms.topic: quickstart
ms.date: 02/11/2020
ms.author: spelluru
ms.openlocfilehash: 352ff91bf26c7ff4f6945431fe6e1357f030e1db
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/01/2020
ms.locfileid: "80477520"
---
# <a name="send-events-to-or-receive-events-from-event-hubs-by-using-python-azure-eventhub-version-5"></a>Recevoir des événements des hubs d’événements ou leur en envoyer en utilisant Python (azure-eventhub version 5)
Ce guide de démarrage rapide montre comment recevoir des événements d’un hub d’événements et lui en envoyer à l’aide du package Python **azure-eventhub version 5**.

> [!IMPORTANT]
> Ce guide de démarrage rapide utilise la dernière version du package azure-eventhub (version 5). Pour un guide de démarrage rapide qui utilise l’ancienne version du package azure-eventhub (version 1), consultez [Envoyer et recevoir des événements avec Event Hubs en utilisant Python (azure-eventhub version 1)](event-hubs-python-get-started-send.md). 

## <a name="prerequisites"></a>Prérequis
Si vous débutez avec Azure Event Hubs, consultez la [vue d’ensemble d’Event Hubs](event-hubs-about.md) avant de suivre ce guide de démarrage rapide. 

Pour effectuer ce démarrage rapide, vous avez besoin de ce qui suit :

- **Abonnement Microsoft Azure**. Pour utiliser les services Azure, y compris Azure Event Hubs, vous avez besoin d’un abonnement.  Si vous n’avez pas de compte Azure, vous pouvez vous inscrire à un [essai gratuit](https://azure.microsoft.com/free/) ou utiliser les avantages de votre abonnement MSDN quand vous [créez un compte](https://azure.microsoft.com).
- Python 2.7, 3.5 ou version ultérieure, avec PIP installé et à jour.
- Le package Python pour Event Hubs. 

    Pour installer le package, exécutez la commande suivante dans une invite de commandes dont le chemin contient Python :

    ```cmd
    pip install azure-eventhub
    ```

    Installez le package suivant pour recevoir les événements en utilisant un stockage Blob Azure comme magasin de points de contrôle :

    ```cmd
    pip install azure-eventhub-checkpointstoreblob-aio
    ```
- **Créez un espace de noms Event Hubs et un Event Hub**. La première étape consiste à utiliser le [portail Azure](https://portal.azure.com) pour créer un espace de noms de type Event Hubs et obtenir les informations de gestion nécessaires à votre application pour communiquer avec le concentrateur d’événements. Pour créer un espace de noms et un hub d’événements, suivez la procédure décrite dans [cet article](event-hubs-create.md). Ensuite, obtenez la **chaîne de connexion de l’espace de noms Event Hubs** en suivant les instructions à partir de l’article : [Obtenir la chaîne de connexion](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Vous utiliserez la chaîne de connexion plus loin dans ce guide de démarrage rapide.

## <a name="send-events"></a>Envoyer des événements
Dans cette section, vous allez créer un script Python pour envoyer des événements au hub d’événements que vous avez créé.

1. Ouvrez votre éditeur Python favori, tel que [Visual Studio Code](https://code.visualstudio.com/).
2. Créez un script appelé *send.py*. Ce script envoie un lot d’événements au hub d’événements que vous avez créé.
3. Collez le code suivant dans *send.py* :

    ```python
    import asyncio
    from azure.eventhub.aio import EventHubProducerClient
    from azure.eventhub import EventData

    async def run():
        # Create a producer client to send messages to the event hub.
        # Specify a connection string to your event hubs namespace and
            # the event hub name.
        producer = EventHubProducerClient.from_connection_string(conn_str="EVENT HUBS NAMESPACE - CONNECTION STRING", eventhub_name="EVENT HUB NAME")
        async with producer:
            # Create a batch.
            event_data_batch = await producer.create_batch()

            # Add events to the batch.
            event_data_batch.add(EventData('First event '))
            event_data_batch.add(EventData('Second event'))
            event_data_batch.add(EventData('Third event'))

            # Send the batch of events to the event hub.
            await producer.send_batch(event_data_batch)

    loop = asyncio.get_event_loop()
    loop.run_until_complete(run())

    ```

    > [!NOTE]
    > Pour obtenir le code source complet, y compris des commentaires d’informations, accédez à la [page GitHub send_async.py](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub/samples/async_samples/send_async.py).
    

## <a name="receive-events"></a>Recevoir des événements
Ce guide de démarrage rapide utilise un stockage Blob Azure comme magasin de points de contrôle. Le magasin de points de contrôle est utilisé pour conserver les points de contrôle (autrement dit, les dernières positions de lecture).  

> [!NOTE]
> Si vous exécutez sur Azure Stack Hub, cette plateforme peut prendre en charge une autre version du kit SDK Storage Blob que celles généralement disponibles sur Azure. Par exemple, si vous exécutez [sur Azure Stack Hub version 2002](https://docs.microsoft.com/azure-stack/user/event-hubs-overview), la version la plus élevée disponible pour le service Stockage est la version 2017-11-09. Dans ce cas, outre les étapes suivantes de cette section, vous devrez également ajouter du code pour cibler la version 2017-11-09 de l’API du service Stockage. Pour obtenir un exemple sur la façon de cibler une version spécifique de l’API de stockage, consultez les exemples [synchrones](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub-checkpointstoreblob/samples/receive_events_using_checkpoint_store_storage_api_version.py) et [asynchrones](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub-checkpointstoreblob-aio/samples/receive_events_using_checkpoint_store_storage_api_version_async.py) sur GitHub. Pour plus d’informations sur les versions du service Stockage Azure prises en charge sur Azure Stack Hub, reportez-vous à [Stockage Azure Stack Hub : différences et considérations](https://docs.microsoft.com/azure-stack/user/azure-stack-acs-differences).


### <a name="create-an-azure-storage-account-and-a-blob-container"></a>Créer un compte de stockage Azure et un conteneur d’objets blob
Créez un compte de stockage Azure et un conteneur d’objets blob dans celui-ci en effectuant les étapes suivantes :

1. [Création d’un compte de stockage Azure](../storage/common/storage-account-create.md?tabs=azure-portal)
2. Créer un [conteneur d’objets blob](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)
3. [Obtenir la chaîne de connexion au compte de stockage](../storage/common/storage-configure-connection-string.md?#view-and-copy-a-connection-string)

Veillez à enregistrer la chaîne de connexion et le nom du conteneur en vue de les utiliser dans le code de réception.


### <a name="create-a-python-script-to-receive-events"></a>Créer un script Python pour recevoir des événements

Dans cette section, vous allez créer un script Python pour recevoir des événements de votre Event Hub :

1. Ouvrez votre éditeur Python favori, tel que [Visual Studio Code](https://code.visualstudio.com/).
2. Créez un script appelé *recv.py*.
3. Collez le code suivant dans *recv.py* :

    ```python
    import asyncio
    from azure.eventhub.aio import EventHubConsumerClient
    from azure.eventhub.extensions.checkpointstoreblobaio import BlobCheckpointStore


    async def on_event(partition_context, event):
        # Print the event data.
        print("Received the event: \"{}\" from the partition with ID: \"{}\"".format(event.body_as_str(encoding='UTF-8'), partition_context.partition_id))

        # Update the checkpoint so that the program doesn't read the events
        # that it has already read when you run it next time.
        await partition_context.update_checkpoint(event)

    async def main():
        # Create an Azure blob checkpoint store to store the checkpoints.
        checkpoint_store = BlobCheckpointStore.from_connection_string("AZURE STORAGE CONNECTION STRING", "BLOB CONTAINER NAME")

        # Create a consumer client for the event hub.
        client = EventHubConsumerClient.from_connection_string("EVENT HUBS NAMESPACE CONNECTION STRING", consumer_group="$Default", eventhub_name="EVENT HUB NAME", checkpoint_store=checkpoint_store)
        async with client:
            # Call the receive method.
            await client.receive(on_event=on_event)

    if __name__ == '__main__':
        loop = asyncio.get_event_loop()
        # Run the main method.
        loop.run_until_complete(main())    
    ```

    > [!NOTE]
    > Pour obtenir le code source complet, y compris des commentaires d’informations supplémentaires, accédez à la [page GitHub recv_with_checkpoint_store_async.py](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub/samples/async_samples/recv_with_checkpoint_store_async.py).


### <a name="run-the-receiver-app"></a>Exécuter l’application réceptrice

Pour exécuter le script, ouvrez une invite de commandes qui a Python dans son chemin, puis exécutez cette commande :

```bash
python recv.py
```

### <a name="run-the-sender-app"></a>Exécuter l’application de l’expéditeur

Pour exécuter le script, ouvrez une invite de commandes qui a Python dans son chemin, puis exécutez cette commande :

```bash
python send.py
```

La fenêtre réceptrice doit afficher les messages qui ont été envoyés au hub d’événements.


## <a name="next-steps"></a>Étapes suivantes
Dans ce guide de démarrage rapide, vous avez envoyé et reçu des événements de manière asynchrone. Pour savoir comment envoyer et recevoir des événements de manière synchrone, accédez à la [page GitHub sync_samples](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhub/samples/sync_samples).

Pour tous les exemples (synchrones comme asynchrones) sur GitHub, accédez à la [bibliothèque cliente Azure Event Hubs des exemples Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhub/samples).
