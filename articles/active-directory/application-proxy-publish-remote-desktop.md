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
ms.date: 03/22/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: fd0ecc62fd3cfc860423acd02108648e99f44753
ms.lasthandoff: 04/03/2017


---

# <a name="publish-remote-desktop-with-azure-ad-application-proxy"></a>Publicación de Escritorio Remoto con el Proxy de aplicación de Azure AD

En este artículo se describe cómo hacer que las implementaciones de Escritorio Remoto de Windows sean accesibles para los usuarios remotos. Las implementaciones de Escritorio Remoto pueden residir localmente o en redes privadas, como las implementaciones de IaaS.

> [!NOTE]
> La característica Proxy de aplicación solo está disponible si actualizó a la edición Premium o Basic de Azure Active Directory (Azure AD). Para obtener más información, consulte [Ediciones de Azure Active Directory](active-directory-editions.md).

El tráfico de protocolo de Escritorio remoto (RDP) se puede publicar a través del Proxy de aplicación de Azure AD como una aplicación de proxy de paso. Esta solución resuelve el problema de conectividad y proporciona protección de seguridad básica como almacenamiento en búfer de red, front-end de Internet reforzado y protección frente a denegación de servicio distribuido (DDoS).

## <a name="remote-desktop-deployment"></a>Implementación de Escritorio remoto

En la implementación de Escritorio remoto, la Puerta de enlace de Escritorio remoto se publica para que pueda convertir el tráfico de la llamada a procedimiento remoto (RPC) sobre HTTPS en RDP sobre protocolo de datagramas de usuario (UDP).

Puede configurar los clientes para que usen clientes de Escritorio remoto, como MSTSC.exe, para tener acceso al Proxy de aplicación de Azure AD. De esta forma, puede crear una nueva conexión HTTPS con la Puerta de enlace de Escritorio remoto mediante sus conectores. Como resultado, la puerta de enlace no se expondrá directamente a Internet y todas las solicitudes HTTPS se terminarán en primer lugar en la nube.

La topología se muestra en el diagrama siguiente:

 ![Diagrama local de servicios de Azure AD](./media/application-proxy-publish-remote-desktop/remote-desktop-topology.png)

## <a name="configure-the-remote-desktop-gateway-url"></a>Configuración de la dirección URL de la Puerta de enlace de Escritorio remoto

Cuando los usuarios configuren la dirección URL de Puerta de enlace de Escritorio remoto y desencadenen el tráfico de RDP, como hacen normalmente, podrán tener acceso a archivos y otros métodos.

Puede publicar mediante el nombre de dominio proporcionado por el Proxy de aplicación (msappproxy.net) o mediante un nombre de dominio personalizado configurado en Azure AD; por ejemplo rdg.contoso.com.

Si los dispositivos de cliente y el archivo RDP ya están configurados con una URL de Puerta de enlace de Escritorio remoto, puede decidir utilizar el mismo nombre de dominio y, por tanto, evitar el cambio. En este caso, se debe proporcionar el certificado que abarca este dominio al Proxy de aplicación y su lista de revocación de certificados (CRL) debe ser accesible a través de Internet.

Si no se configura ninguna dirección URL de Puerta de enlace de Escritorio remoto, los usuarios o administradores pueden especificarla en los clientes de escritorio remoto (MSTSC) mediante el cuadro de conexión a Escritorio remoto, tal y como se muestra aquí.

 ![Cuadro de diálogo Conexión a Escritorio remoto](./media/application-proxy-publish-remote-desktop/remote-desktop-connection-advanced.png)

Aparece el cuadro de diálogo **Configuración de la conexión** al hacer clic en **Configuración** en la pestaña de **opciones avanzadas**.

 ![Ventana de Configuración de la conexión del cuadro de diálogo Conexión a Escritorio remoto](./media/application-proxy-publish-remote-desktop/remote-desktop-connection-settings.png)

## <a name="remote-desktop-web-access"></a>Acceso web de Escritorio remoto

Si su organización usa el portal de Acceso web de Escritorio remoto (RDWA), también puede publicar mediante el Proxy de aplicación de Azure AD. Puede publicar en este portal con la autenticación previa y el inicio de sesión único (SSO).

La topología del escenario RDWA se muestra en el diagrama siguiente:

 ![Diagrama del escenario RDWA](./media/application-proxy-publish-remote-desktop/remote-desktop-web-access-portal1.png)

En el caso anterior, los usuarios se autenticarán en Azure AD antes de acceder a RDWA. Si ya se han autenticado en Azure AD (por ejemplo, si usan Office 365) no tienen que volver a autenticarse para RDWA.

Cuando los usuarios inician la sesión de RDP, tienen que autenticarse de nuevo en el canal de RDP. El motivo es que el SSO de RDWA en la Puerta de enlace de Escritorio remoto se basa en el almacenamiento de las credenciales de usuario en el cliente mediante ActiveX. Este proceso se desencadena a partir de la autenticación basada en formularios de RDWA. Cuando la autenticación de RDWA utiliza Kerberos, no se presenta autenticación basada en formularios y, por tanto, no funcionará el RDWA a RDP SSO.

Si RDWA necesita SSO para el tráfico de RDP, o si la autenticación basada en formularios de RDWA se ha personalizado en gran medida, puede publicar en RDWA sin autenticación previa.

La topología de este escenario se muestra en el diagrama siguiente:

 ![Diagrama del escenario RDWA](./media/application-proxy-publish-remote-desktop/remote-desktop-web-access-portal2.png)

En el caso anterior, los usuarios deben autenticarse en RDWA mediante la autenticación basada en formularios, pero no necesitan autenticarse a través de RDP.

>[!NOTE]
>En ambos casos, no es necesaria ninguna autenticación previa en el tráfico de RDP. Por lo tanto, los usuarios pueden acceder a este tráfico sin pasar antes por RDWA.

## <a name="next-steps"></a>Pasos siguientes

[Habilitar el acceso remoto a SharePoint con el Proxy de aplicación de Azure AD](application-proxy-enable-remote-access-sharepoint.md)  
[Habilitación del proxy de aplicación en Azure Portal](active-directory-application-proxy-enable.md)

