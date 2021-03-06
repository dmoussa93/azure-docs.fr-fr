---
title: Rôles et exigences pour Azure Data Share
description: En savoir plus sur les autorisations requises pour partager et recevoir des données à l’aide d’Azure Data Share.
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 36a492f6a3e86cfb2fc9505550cc2d9f4746e070
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79231477"
---
# <a name="roles-and-requirements-for-azure-data-share"></a>Rôles et exigences pour Azure Data Share 

Cet article décrit les rôles et les autorisations nécessaires pour partager et recevoir des données à l’aide du service Azure Data Share. 

## <a name="roles-and-requirements"></a>Rôles et conditions requises

À l’aide du service Azure Data Share, vous pouvez partager des données sans échanger les informations d’identification entre le fournisseur de données et le consommateur de données. Le service Azure Data Share utilise des identités managées (précédemment appelées MSI) pour s’authentifier auprès du magasin de données Azure. 

L’identité managée de la ressource Azure Data Share doit être autorisée à accéder au magasin de données Azure. Le service Azure Data Share utilise ensuite cette identité managée pour lire et écrire des données pour le partage basé sur une capture instantanée et pour établir un lien symbolique pour le partage sur place. 

Pour partager ou recevoir des données à partir d’un magasin de données Azure, l’utilisateur doit au moins disposer des autorisations suivantes. Des autorisations supplémentaires sont requises pour le partage basé sur SQL.
* Autorisation d’écrire dans le magasin de données Azure. En règle générale, cette autorisation existe dans le rôle **Contributeur**.
* Autorisation de créer une attribution de rôle dans le magasin de données Azure. En règle générale, l’autorisation de créer des attributions de rôles existe dans le rôle **Propriétaire**, le rôle Administrateur des accès utilisateur ou un rôle personnalisé doté de l’autorisation Microsoft.Authorization/role assignments/write. Cette autorisation n’est pas requise si l’identité managée de la ressource de partage de données est déjà autorisée à accéder au magasin de données Azure. Consultez le tableau ci-dessous pour connaître le rôle requis.

Voici un résumé des rôles attribués à l’identité managée de la ressource Data Share :

| |  |  |
|---|---|---|
|**Type de magasin de données**|**Magasin de données source de fournisseurs de données**|**Magasin de données cible de consommateurs de données**|
|Stockage Blob Azure| Lecteur des données blob du stockage | Contributeur aux données Blob du stockage
|Azure Data Lake Gen1 | Propriétaire | Non pris en charge
|Azure Data Lake Gen2 | Lecteur des données blob du stockage | Contributeur aux données Blob du stockage
|Azure SQL Server | Contributeur de base de données SQL | Contributeur de base de données SQL
|Cluster Azure Data Explorer | Contributeur | Contributeur
|

Pour le partage basé sur SQL, un utilisateur SQL doit être créé à partir d’un fournisseur externe dans la base de données SQL portant le même nom que la ressource Azure Data Share. Voici un résumé de l’autorisation requise par l’utilisateur SQL.

| |  |  |
|---|---|---|
|**Type de base de données SQL**|**Autorisation de l’utilisateur SQL fournisseur de données**|**Autorisation de l’utilisateur SQL consommateur de données**|
|Azure SQL Database | db_datareader | db_datareader, db_datawriter, db_ddladmin
|Azure Synapse Analytics (anciennement SQL DW) | db_datareader | db_datareader, db_datawriter, db_ddladmin
|


### <a name="data-provider"></a>Fournisseur de données 
Pour ajouter un jeu de données dans Azure Data Share, l’identité managée de la ressource de partage de données du fournisseur doit être autorisée à accéder au magasin de données Azure source. Par exemple, dans le cas d’un compte de stockage, l’identité managée de la ressource de partage de données se voit octroyer le rôle de lecteur des données Blob du stockage. 

Cette opération est effectuée automatiquement par le service Azure Data Share lorsque l’utilisateur ajoute un jeu de données via le Portail Azure et que l’utilisateur dispose de l’autorisation appropriée. Par exemple, l’utilisateur est propriétaire du magasin de données Azure ou est membre d’un rôle personnalisé qui dispose de l’autorisation Microsoft.Authorization/role assignments/write. 

L’utilisateur peut également demander au propriétaire du magasin de données Azure d’ajouter manuellement l’identité managée de la ressource de partage de données au magasin de données Azure. Cette action ne doit être effectuée qu’une seule fois par ressource de partage de données.

Pour créer une attribution de rôle pour l’identité managée de la ressource de partage de données, suivez les étapes ci-dessous :

1. Accédez au magasin de données Azure.
1. Sélectionnez **Contrôle d’accès (IAM)** .
1. Sélectionnez **Ajouter une attribution de rôle**.
1. Sous *Rôle*, sélectionnez le rôle dans le tableau d’attribution de rôle ci-dessus (par exemple, pour un compte de stockage, sélectionnez *Lecteur des données Blob du stockage*).
1. Sous *Sélectionner*, saisissez le nom de votre ressource Azure Data Share.
1. Cliquez sur *Enregistrer*.

Pour les sources basées sur SQL, en plus des étapes ci-dessus, un utilisateur SQL doit être créé à partir d’un fournisseur externe dans la base de données SQL portant le même nom que la ressource Azure Data Share. Cet utilisateur doit disposer de l’autorisation *db_datareader*. Vous trouverez un exemple de script avec d’autres conditions préalables pour le partage basé sur SQL dans le didacticiel [Partager vos données](share-your-data.md). 

### <a name="data-consumer"></a>Consommateur de données
Pour recevoir des données, l’identité managée de la ressource de partage de données du consommateur doit être autorisée à accéder au magasin de données Azure cible. Par exemple, dans le cas d’un compte de stockage, l’identité managée de la ressource de partage de données se voit octroyer le rôle de contributeur aux données Blob du stockage. 

Cette opération est effectuée automatiquement par le service Azure Data Share si l’utilisateur spécifie une banque de données cible via le Portail Azure et que l’utilisateur dispose de l’autorisation appropriée. Par exemple, l’utilisateur est propriétaire du magasin de données Azure ou est membre d’un rôle personnalisé qui dispose de l’autorisation Microsoft.Authorization/role assignments/write. 

L’utilisateur peut également demander au propriétaire du magasin de données Azure d’ajouter manuellement l’identité managée de la ressource de partage de données au magasin de données Azure. Cette action ne doit être effectuée qu’une seule fois par ressource de partage de données.

Pour créer une attribution de rôle pour l’identité managée de la ressource de partage de données manuellement, suivez les étapes ci-dessous :

1. Accédez au magasin de données Azure.
1. Sélectionnez **Contrôle d’accès (IAM)** .
1. Sélectionnez **Ajouter une attribution de rôle**.
1. Sous *Rôle*, sélectionnez le rôle dans le tableau d’attribution de rôle ci-dessus (par exemple, pour un compte de stockage, sélectionnez *Lecteur des données Blob du stockage*).
1. Sous *Sélectionner*, saisissez le nom de votre ressource Azure Data Share.
1. Cliquez sur *Enregistrer*.

Pour une cible basée sur SQL, en plus des étapes ci-dessus, un utilisateur SQL doit être créé à partir d’un fournisseur externe dans la base de données SQL portant le même nom que la ressource Azure Data Share. Cet utilisateur doit disposer de l’autorisation *db_datareader, db_datawriter, db_ddladmin*. Vous trouverez un exemple de script avec d’autres conditions préalables pour le partage basé sur SQL dans le didacticiel [Accepter et recevoir des données](subscribe-to-data-share.md). 

Si vous partagez des données à l’aide d’API REST, vous devez créer ces attributions de rôles manuellement. 

Pour en savoir plus sur l’ajout d’une attribution de rôle, reportez-vous à [cette documentation,](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal#add-a-role-assignment). 

## <a name="resource-provider-registration"></a>Inscription du fournisseur de ressources 

Pour afficher une invitation Azure Data Share pour la première fois dans votre locataire Azure, vous devrez peut-être inscrire manuellement le fournisseur de ressources Microsoft.DataShare dans votre abonnement Azure. Suivez ces étapes pour inscrire le fournisseur de ressources Microsoft.DataShare dans votre abonnement Azure. Vous avez besoin d’un accès *Contributeur* à l’abonnement Azure pour inscrire le fournisseur de ressources.

1. Dans le portail Azure, accédez à **Abonnements**.
1. Sélectionnez l’abonnement que vous utilisez pour Azure Data Share.
1. Cliquez sur **Fournisseurs de ressources**.
1. Recherchez Microsoft.DataShare.
1. Cliquez sur **S'inscrire**.

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur les rôles dans Azure : [Comprendre les définitions de rôles](../role-based-access-control/role-definitions.md)

