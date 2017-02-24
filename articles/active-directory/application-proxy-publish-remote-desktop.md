---
title: "Publicación de Escritorio Remoto con el Proxy de aplicación de Azure Active Directory | Microsoft Docs"
description: "Se explican los conceptos básicos acerca de los conectores del Proxy de aplicación de Azure AD."
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/12/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 40ddf7d6648561eab855b41e76a5ca509c6ca4ef
ms.openlocfilehash: 05130d728c579e0b778bdc8ec49e01f2223c2bba


---

# <a name="publish-remote-desktop-with-azure-active-directory-application-proxy"></a>Publicación de Escritorio Remoto con el Proxy de aplicación de Azure Active Directory

En este artículo se describe cómo hacer que las implementaciones de Escritorio Remoto estén accesibles para los usuarios remotos. Tales implementaciones de Escritorio Remoto pueden residir localmente o en redes privadas, como las implementaciones de IaaS. 

> [!NOTE]
> Proxy de aplicación es una característica que solo está disponible si actualizó a la edición Premium o Basic de Azure Active Directory. Para obtener más información, consulte [Ediciones de Azure Active Directory](active-directory-editions.md).
> 
 

El tráfico de protocolo de Escritorio remoto se puede publicar a través del Proxy de aplicación como una aplicación de proxy de paso. Esta solución resuelve el problema de conectividad y proporciona protección de seguridad básica como almacenamiento en búfer de red, front-end de Internet reforzado y protección DDoS. 

##<a name="remote-desktop-deployment"></a>Implementación de Escritorio remoto

En la implementación de Escritorio remoto, la Puerta de enlace de Escritorio remoto se publica para que sea capaz de convertir el tráfico RPC sobre HTTPS en RDP sobre UDP.

Puede configurar los clientes para usar los clientes para que usen clientes de Escritorio remoto, como MSTSC.exe, para tener acceso al Proxy de aplicación de Azure AD. Así puede crear una nueva conexión HTTPS con la Puerta de enlace de Escritorio remoto mediante sus conectores. Al hacerlo, la Puerta de enlace de Escritorio remoto no se expondrá directamente a Internet y todas las solicitudes HTTPS se terminarán en primer lugar en la nube. 

En el diagrama siguiente se muestra esta topología.

 ![Local de servicios de Azure AD](./media/application-proxy-publish-remote-desktop/remote-desktop-topology.png)

## <a name="configure-the-remote-desktop-gateway-url"></a>Configuración de la dirección URL de la Puerta de enlace de Escritorio remoto

Siempre que los usuarios configuren la dirección URL de Puerta de enlace de Escritorio remoto, cuando desencadenen el tráfico de RDP como lo hacen normalmente, podrán tener acceso a archivos y otros métodos.

La publicación puede realizarse mediante el nombre de dominio proporcionado por el Proxy de aplicación (msappproxy.net) o mediante un nombre de dominio personalizado configurado en Azure AD; por ejemplo rdg.contoso.com. 

Si los dispositivos de cliente y el archivo RDP ya están configurados con una URL de Puerta de enlace de Escritorio remoto, puede utilizar el mismo nombre de dominio y, por tanto, evitar el cambio. En este caso, se debe proporcionar el certificado que abarca este dominio al Proxy de aplicación y su CRL debe estar accesible a través de Internet.

Si no se configura ninguna dirección URL de Puerta de enlace de Escritorio remoto, los usuarios o administradores pueden especificarla en los clientes de escritorio remoto (MSTSC) mediante el cuadro de conexión a Escritorio remoto, tal y como se muestra a continuación.

 ![Local de servicios de Azure AD](./media/application-proxy-publish-remote-desktop/remote-desktop-connection-advanced.png)

Aparece el cuadro de configuración de la conexión al hacer clic en **Configuración** en la pestaña **Opciones avanzadas**.

 ![Local de servicios de Azure AD](./media/application-proxy-publish-remote-desktop/remote-desktop-connection-settings.png)

## <a name="remote-desktop-web-access"></a>Acceso web de Escritorio remoto

Si su organización usa el portal de Acceso web de Escritorio remoto (RDWA), también puede publicar mediante el Proxy de aplicación de Azure AD. Puede publicar en este portal con la autenticación previa y el inicio de sesión único (SSO).

El diagrama de la sección siguiente muestra la topología para este escenario.

 ![Local de servicios de Azure AD](./media/application-proxy-publish-remote-desktop/remote-desktop-web-access-portal1.png)

En el caso anterior, los usuarios se autenticarán en Azure AD antes de acceder a RDWA. Si ya se han autenticado en AzurE AD (por ejemplo, si usan Office 365) no tienen que volver a autenticarse para RDWA.

Cuando los usuarios inician la sesión de RDP, tienen que autenticarse de nuevo en el canal de RDP. El motivo es que el SSO de RDWA en la Puerta de enlace de Escritorio remoto se basa en el almacenamiento de las credenciales de usuario final en el cliente mediante ActiveX. Este proceso se desencadena a partir de la autenticación basada en formularios de RDWA. Cuando la autenticación de RDWA utiliza Kerberos, no se presenta autenticación basada en formularios y, por tanto, no funcionará la rdwa con a RDP SSO.

Si RDWA necesita SSO para el tráfico de RDP, o si la autenticación basada en formularios de RDWA se ha personalizado en gran medida, es posible publicar en RDWA sin autenticación previa.

El diagrama siguiente muestra la topología para este escenario.

 ![Local de servicios de Azure AD](./media/application-proxy-publish-remote-desktop/remote-desktop-web-access-portal2.png)

En el caso anterior, los usuarios tendrán que autenticarse en RDWA mediante la autenticación basada en formularios, pero no necesitan autenticarse a través de RDP. 

Es importante tener en cuenta en ambos casos que no se necesita autenticación previa en el tráfico de RDP. Por lo tanto, los usuarios pueden tener acceso a este tráfico sin pasar antes por RDWA.

##<a name="next-steps"></a>Pasos siguientes

[Habilitación del acceso remoto a SharePoint con el Proxy de aplicación de Azure AD](application-proxy-enable-remote-access-sharepoint.md)<br>
[Habilitación del proxy de aplicación en Azure Portal](https://github.com/Microsoft/azure-docs-pr/blob/master/articles/active-directory/active-directory-application-proxy-enable.md)



<!--HONumber=Feb17_HO1-->


