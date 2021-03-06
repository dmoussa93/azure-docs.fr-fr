---
title: Authentification de service à service – Data Lake Storage Gen1 – API REST
description: Découvrez comment procéder à une authentification de service à service avec Azure Data Lake Storage Gen1 et Azure Active Directory à l’aide de l’API REST.
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 59d0bf20b16beda47d76e6a9940ac9fa4436da3f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73904529"
---
# <a name="service-to-service-authentication-with-azure-data-lake-storage-gen1-using-rest-api"></a>Authentification de service à service auprès d’Azure Data Lake Storage Gen1 à l’aide de l’API REST
> [!div class="op_single_selector"]
> * [À l’aide de Java](data-lake-store-service-to-service-authenticate-java.md)
> * [Utilisation du kit de développement logiciel (SDK) .NET](data-lake-store-service-to-service-authenticate-net-sdk.md)
> * [Utilisation de Python](data-lake-store-service-to-service-authenticate-python.md)
> * [Utilisation de l'API REST](data-lake-store-service-to-service-authenticate-rest-api.md)
> 
> 

Dans cet article, vous allez apprendre à utiliser l’API REST pour effectuer une authentification de service à service auprès d’Azure Data Lake Storage Gen1. Pour plus d’informations sur l’authentification des utilisateurs finaux auprès de Data Lake Storage Gen1 à l’aide de l’API REST, voir [Authentification des utilisateurs finaux auprès de Data Lake Storage Gen1 avec l’API REST](data-lake-store-end-user-authenticate-rest-api.md).

## <a name="prerequisites"></a>Conditions préalables requises

* **Un abonnement Azure**. Consultez la page [Obtention d’un essai gratuit d’Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Créez une application « web » Azure Active Directory**. Vous devez avoir suivi la procédure indiquée dans [Authentification de service à service auprès de Data Lake Storage Gen1 à l’aide d’Azure Active Directory](data-lake-store-service-to-service-authenticate-using-active-directory.md).

## <a name="service-to-service-authentication"></a>Authentification de service à service

Dans ce scénario, l’application fournit ses propres informations d’identification pour effectuer les opérations. Avec cette méthode, vous devez émettre une demande POST, comme celle illustrée dans l’extrait de code suivant :

    curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token  \
      -F grant_type=client_credentials \
      -F resource=https://management.core.windows.net/ \
      -F client_id=<CLIENT-ID> \
      -F client_secret=<AUTH-KEY>

La sortie de cette demande inclut un jeton d’autorisation (indiqué par `access-token` dans la sortie ci-dessous) que vous allez ensuite passer avec vos appels d’API REST. Enregistrez le jeton d’authentification dans un fichier texte. Vous en aurez besoin pour effectuer des appels REST auprès de Data Lake Storage Gen1.

    {"token_type":"Bearer","expires_in":"3599","expires_on":"1458245447","not_before":"1458241547","resource":"https://management.core.windows.net/","access_token":"<REDACTED>"}

Cet article utilise la méthode **non interactive** . Pour plus d’informations sur l’authentification non interactive (appels de service à service), consultez [Appels de service à service à l’aide d’informations d’identification](https://msdn.microsoft.com/library/azure/dn645543.aspx).

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris à utiliser l’authentification de service à service auprès d’Azure Data Lake Storage Gen1 avec l’API REST. Vous pouvez à présent consulter les articles ci-après, qui expliquent comment utiliser l’API REST pour travailler avec Data Lake Storage Gen1.

* [Opérations de gestion des comptes sur Data Lake Storage Gen1 à l’aide de l’API REST](data-lake-store-get-started-rest-api.md)
* [Opérations sur les données dans Data Lake Storage Gen1 à l’aide de l’API REST](data-lake-store-data-operations-rest-api.md)
