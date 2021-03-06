---
title: Azure Service Bus – Nombre de messages
description: Récupérez le nombre de messages contenus dans les files d’attente et les abonnements à l’aide d’Azure Resource Manager et des API NamespaceManager d’Azure Service Bus.
services: service-bus-messaging
documentationcenter: ''
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/08/2020
ms.author: aschhab
ms.openlocfilehash: 8020b12ca892fbf7dec6fed6259526d958fb110f
ms.sourcegitcommit: df8b2c04ae4fc466b9875c7a2520da14beace222
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80891762"
---
# <a name="message-counters"></a>Compteurs de messages

Vous pouvez récupérer le nombre de messages contenus dans les files d’attente et les abonnements à l’aide d’Azure Resource Manager et des API [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) Service Bus dans le Kit de développement logiciel (SDK) .NET Framework.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Avec PowerShell, vous pouvez obtenir ce nombre comme suit :

```powershell
(Get-AzServiceBusQueue -ResourceGroup mygrp -NamespaceName myns -QueueName myqueue).CountDetails
```

## <a name="message-count-details"></a>Détails du nombre de messages

Le fait de connaître le nombre de messages actifs permet de déterminer si une file d’attente génère un backlog dont le traitement nécessite davantage de ressources que celles déployées actuellement. Les détails de compteur disponibles dans la classe [MessageCountDetails](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails) sont les suivants :

-   [ActiveMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.activemessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_ActiveMessageCount) : messages de la file d’attente ou de l’abonnement qui présentent l’état actif et sont prêts à être remis.
-   [DeadLetterMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.deadlettermessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_DeadLetterMessageCount) : messages figurant dans la file d’attente de lettres mortes.
-   [ScheduledMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.scheduledmessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_ScheduledMessageCount) : messages à l’état planifié.
-   [TransferDeadLetterMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.transferdeadlettermessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_TransferDeadLetterMessageCount) : messages dont le transfert vers une autre file d’attente ou rubrique a échoué et qui ont été déplacés vers la file d’attente de lettres mortes de transfert.
-   [TransferMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.transfermessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_TransferMessageCount) : messages en attente de transfert vers une autre file d’attente ou rubrique.

Si une application a besoin de mettre à l’échelle les ressources en fonction de la longueur de la file d’attente, elle doit le faire progressivement. L’acquisition des compteurs de messages est une opération onéreuse dans le répartiteur de messages, dont l’exécution fréquente compromet directement les performances des entités.

> [!NOTE]
> Les messages envoyés à une rubrique Service Bus sont transférés aux abonnements pour cette rubrique. Par conséquent, le nombre de messages actifs sur la rubrique elle-même est 0, car ces messages ont été transférés à l’abonnement. Récupérez le nombre de messages au niveau de l’abonnement et vérifiez qu’il est supérieur à 0. Même si vous voyez des messages au niveau de l’abonnement, ils se trouvent en réalité dans un stockage appartenant à la rubrique. 

Si vous examinez les abonnements, ils doivent compter un nombre de messages différent de zéro (ce qui ajoute jusqu’à 323 Mo d’espace pour cette entité entière).

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur la messagerie Service Bus, consultez les articles suivants :

* [Files d’attente, rubriques et abonnements Service Bus](service-bus-queues-topics-subscriptions.md)
* [Prise en main des files d’attente Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Utilisation des rubriques et abonnements Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)
