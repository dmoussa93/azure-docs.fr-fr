---
title: Utiliser Stockage Blob comme magasin de points de contrôle sur Azure Stack Hub (préversion)
description: Cet article explique comment utiliser Stockage Blob comme magasin de points de contrôle dans Event Hubs sur Azure Stack Hub (préversion).
services: event-hubs
documentationcenter: na
author: spelluru
ms.service: event-hubs
ms.topic: how-to
ms.date: 03/18/2020
ms.author: spelluru
ms.openlocfilehash: 1f0e4dea44007ef82cb4b700ff0be4a5579541d8
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2020
ms.locfileid: "80398754"
---
# <a name="use-blob-storage-as-checkpoint-store---event-hubs-on-azure-stack-hub-preview"></a>Utiliser Stockage Blob comme magasin de points de contrôle – Event Hubs sur Azure Stack Hub (préversion)
Si vous utilisez Stockage Blob Azure comme magasin de points de contrôle dans un environnement qui prend en charge une autre version du Kit de développement logiciel (SDK) Stockage Blob que ceux généralement disponibles sur Azure, vous devez utiliser du code pour remplacer la version de l’API du service de stockage par la version prise en charge par cet environnement. Par exemple, si vous exécutez [Event Hubs sur Azure Stack Hub version 2002](https://docs.microsoft.com/azure-stack/user/event-hubs-overview), la version la plus élevée disponible pour le service Stockage est la version 2017-11-09. Dans ce cas, vous devez utiliser du code pour cibler la version de l’API du service Stockage sur 2017-11-09. Pour obtenir un exemple sur la façon de cibler une version spécifique de l’API Stockage, consultez les exemples suivants sur GitHub : 

- [.NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor/samples/Sample10_RunningWithDifferentStorageVersion.cs)
- [Java](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs-checkpointstore-blob/src/samples/java/com/azure/messaging/eventhubs/checkpointstore/blob/EventProcessorWithOlderStorageVersion.java). 
- [JavaScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/receiveEventsWithDownleveledStorage.js) ou [TypeScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/receiveEventsWithDownleveledStorage.ts) 
- Python : [synchrone](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub-checkpointstoreblob/samples/event_processor_blob_storage_example_with_storage_api_version.py), [asynchrone](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub-checkpointstoreblob-aio/samples/event_processor_blob_storage_example_with_storage_api_version.py)

> [!IMPORTANT]
> Event Hubs sur Azure Stack Hub est actuellement disponible en [préversion](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) et gratuitement. 

Si vous exécutez le récepteur Event Hubs qui utilise Stockage Blob comme magasin de points de contrôle sans cibler la version prise en charge par Azure Stack Hub, vous recevrez le message d’erreur suivant :

```
The value for one of the HTTP headers is not in the correct format
```


## <a name="sample-error-message-in-python"></a>Exemple de message d’erreur en Python
Pour Python, une erreur `azure.core.exceptions.HttpResponseError` est transmise au gestionnaire d’erreurs `on_error(partition_context, error)` de `EventHubConsumerClient.receive()`. Toutefois, la méthode `receive()` ne lève pas d’exception. `print(error)` imprimera les informations d’exception suivantes :

```bash
The value for one of the HTTP headers is not in the correct format.

RequestId:f048aee8-a90c-08ba-4ce1-e69dba759297
Time:2020-03-17T22:04:13.3559296Z
ErrorCode:InvalidHeaderValue
Error:None
HeaderName:x-ms-version
HeaderValue:2019-07-07
```

L’enregistreur d’événements consignera deux avertissements comme ceux-ci :

```bash
WARNING:azure.eventhub.extensions.checkpointstoreblobaio._blobstoragecsaio: 
An exception occurred during list_ownership for namespace '<namespace-name>.eventhub.<region>.azurestack.corp.microsoft.com' eventhub 'python-eh-test' consumer group '$Default'. 

Exception is HttpResponseError('The value for one of the HTTP headers is not in the correct format.\nRequestId:f048aee8-a90c-08ba-4ce1-e69dba759297\nTime:2020-03-17T22:04:13.3559296Z\nErrorCode:InvalidHeaderValue\nError:None\nHeaderName:x-ms-version\nHeaderValue:2019-07-07')

WARNING:azure.eventhub.aio._eventprocessor.event_processor:EventProcessor instance '26d84102-45b2-48a9-b7f4-da8916f68214' of eventhub 'python-eh-test' consumer group '$Default'. An error occurred while load-balancing and claiming ownership. 

The exception is HttpResponseError('The value for one of the HTTP headers is not in the correct format.\nRequestId:f048aee8-a90c-08ba-4ce1-e69dba759297\nTime:2020-03-17T22:04:13.3559296Z\nErrorCode:InvalidHeaderValue\nError:None\nHeaderName:x-ms-version\nHeaderValue:2019-07-07'). Retrying after 71.45254944090853 seconds
```



## <a name="next-steps"></a>Étapes suivantes

Consultez les articles suivants pour en savoir plus sur le partitionnement et les points de contrôle : [Équilibrer la charge de partition sur plusieurs instances de votre application](event-processor-balance-partition-load.md)
