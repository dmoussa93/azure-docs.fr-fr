---
title: 'Locataire Azure AD pour les connexions VPN utilisateur : Authentification Azure AD'
description: Vous pouvez utiliser un VPN utilisateur Azure Virtual WAN (point-to-site) pour vous connecter à votre réseau virtuel à l’aide de l'authentification Azure AD
titleSuffix: Azure Virtual WAN
services: virtual-wan
author: anzaman
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 03/19/2020
ms.author: alzam
ms.openlocfilehash: 74347ce969b6a5ffd57f5ca8396517e78590f3f2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80059455"
---
# <a name="create-an-azure-active-directory-tenant-for-user-vpn-openvpn-protocol-connections"></a>Créer un locataire Azure Active Directory pour les connexions de protocole VPN OpenVPN utilisateur

Pour vous connecter à votre réseau virtuel, vous pouvez utiliser l'authentification par certificat ou l'authentification RADIUS. Toutefois, lorsque vous utilisez le protocole Open VPN, vous pouvez également utiliser l'authentification Azure Active Directory. Cet article vous aide à configurer un locataire Azure AD pour l'authentification Open VPN (point à site) Virtual WAN utilisateur.

> [!NOTE]
> L’authentification Azure AD n’est prise en charge que pour les connexions de protocole OpenVPN&reg;.
>

## <a name="1-create-the-azure-ad-tenant"></a><a name="tenant"></a>1. Créer le locataire Azure AD

Pour créer un locataire Azure AD, suivez la procédure décrite dans l'article [Créer un locataire](../active-directory/fundamentals/active-directory-access-create-new-tenant.md) :

* Nom de l'organisation
* Nom de domaine initial

Exemple :

   ![Nouveau locataire Azure AD](./media/openvpn-create-azure-ad-tenant/newtenant.png)

## <a name="2-create-azure-ad-tenant-users"></a><a name="users"></a>2. Créer des utilisateurs de locataire Azure AD

Créez ensuite deux comptes d'utilisateur. Créez un compte d'administrateur général et un compte d'utilisateur principal. Le compte d'utilisateur principal est utilisé comme compte d'intégration principal (compte de service). Lorsque vous créez un compte d'utilisateur de locataire Azure AD, vous définissez le rôle d'annuaire en fonction du type d'utilisateur que vous souhaitez créer.

Utilisez les étapes décrites dans [cet article](../active-directory/fundamentals/add-users-azure-active-directory.md) pour créer au moins deux utilisateurs pour votre locataire Azure AD. Veillez à modifier le **rôle d'annuaire** pour créer les types de compte :

* Administrateur général
* Utilisateur

## <a name="3-enable-azure-ad-authentication-on-the-vpn-gateway"></a><a name="enable-authentication"></a>3. Activer l'authentification Azure AD sur la passerelle VPN

1. Recherchez l'ID du répertoire que vous souhaitez utiliser pour l'authentification. Celui-ci est répertorié dans la section Propriétés de la page Active Directory.

    ![ID du répertoire](./media/openvpn-create-azure-ad-tenant/directory-id.png)

2. Copiez l'ID du répertoire.

3. Connectez-vous au portail Azure en tant qu'utilisateur doté du rôle d'**Administrateur général**.

4. Octroyez ensuite le consentement administrateur. Copiez et collez l'URL correspondant à votre emplacement de déploiement sur la barre d'adresse de votre navigateur :

    Public

    ```
    https://login.microsoftonline.com/common/oauth2/authorize?client_id=41b23e61-6c1e-4545-b367-cd054e0ed4b4&response_type=code&redirect_uri=https://portal.azure.com&nonce=1234&prompt=admin_consent
    ````

    Azure Government

    ```
    https://login-us.microsoftonline.com/common/oauth2/authorize?client_id=51bb15d4-3a4f-4ebf-9dca-40096fe32426&response_type=code&redirect_uri=https://portal.azure.us&nonce=1234&prompt=admin_consent
    ````

    Microsoft Cloud Allemagne

    ```
    https://login-us.microsoftonline.de/common/oauth2/authorize?client_id=538ee9e6-310a-468d-afef-ea97365856a9&response_type=code&redirect_uri=https://portal.microsoftazure.de&nonce=1234&prompt=admin_consent
    ````

    Azure China 21Vianet

    ```
    https://https://login.chinacloudapi.cn/common/oauth2/authorize?client_id=49f817b6-84ae-4cc0-928c-73f27289b3aa&response_type=code&redirect_uri=https://portal.azure.cn&nonce=1234&prompt=admin_consent
    ```

5. Si vous y êtes invité, sélectionnez le compte d'**Administrateur général**.

    ![ID du répertoire](./media/openvpn-create-azure-ad-tenant/pick.png)

6. Lorsque vous y êtes invité, sélectionnez **Accepter**.

    ![Acceptation](./media/openvpn-create-azure-ad-tenant/accept.jpg)

7. Sous votre instance d'Azure AD, dans **Applications d'entreprise**, vous pouvez voir que **VPN Azure** apparaît dans la liste.

    ![VPN Azure](./media/openvpn-create-azure-ad-tenant/azurevpn.png)

8. Configurez l’authentification Azure AD pour le VPN utilisateur et affectez-la à un hub virtuel en suivant la procédure décrite dans [Configurer l’authentification Azure AD pour une connexion point à site à Azure](virtual-wan-point-to-site-azure-ad.md)

## <a name="next-steps"></a>Étapes suivantes

Pour vous connecter à votre réseau virtuel, vous devez créer et configurer un profil client VPN et l’associer à un hub virtuel. Consultez [Configurer l’authentification Azure AD pour une connexion point à site à Azure](virtual-wan-point-to-site-azure-ad.md).
