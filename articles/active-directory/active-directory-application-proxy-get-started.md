---
title: "Provisión de acceso remoto seguro a aplicaciones locales"
description: "Explica cómo utilizar el proxy de la aplicación de Azure AD para proporcionar acceso remoto seguro a sus aplicaciones locales."
services: active-directory
documentationcenter: 
author: daveba
manager: mtillman
ms.assetid: d5450da1-9e06-4d08-8146-011c84922ab5
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/04/2017
ms.author: daveba
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: 24def898f282c4e122ae53932ae86047e815595c
ms.sourcegitcommit: 3cdc82a5561abe564c318bd12986df63fc980a5a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/05/2018
---
# <a name="how-to-provide-secure-remote-access-to-on-premises-applications"></a>Provisión de acceso remoto seguro a aplicaciones locales

Hoy en día, los empleados desean ser productivos en cualquier lugar, en cualquier momento y con cualquier dispositivo. Quieren trabajar en sus propios dispositivos, con independencia de que sean equipos portátiles, teléfonos o tabletas. Asimismo, esperan tener acceso a todas sus aplicaciones, tanto las de SaaS en la nube como las de tipo empresarial en entornos locales. Para proporcionar acceso a las aplicaciones locales, siempre se han usado redes privadas virtuales (VPN) o redes perimetrales (DMZ). No obstante, estas soluciones no solo son complejas y difíciles de proteger, sino también costosas de configurar y administrar.

Hay una forma mejor de realizar esto.

Los recursos de hoy en día, que trabajan en un entorno donde la nube y la movilidad son prioritarias, necesitan una solución moderna de acceso remoto. La característica Proxy de aplicación de Azure AD forma parte de Azure Active Directory y ofrece acceso remoto como servicio. Es decir, es fácil de implementar, utilizar y administrar.

[!INCLUDE [identity](../../includes/azure-ad-licenses.md)]

## <a name="what-is-azure-active-directory-application-proxy"></a>¿Qué es el proxy de la aplicación de Azure Active Directory?
La característica Proxy de la aplicación de Azure AD proporciona un inicio de sesión único (SSO) y acceso remoto seguro para las aplicaciones web hospedadas de forma local, Algunas aplicaciones que podría publicar incluyen sitios de SharePoint, Outlook Web Access o cualquier otra aplicación web de LOB que tenga. Estas aplicaciones web locales se integran con Azure AD, la misma identidad y plataforma de control que utiliza Office 365. Los usuarios finales pueden tener acceso a sus aplicaciones locales del mismo modo que tienen acceso a Office 365 y otras aplicaciones de SaaS integradas con Azure AD. No hace falta cambiar la infraestructura de red o establecer una VPN para ofrecer esta solución a los usuarios.

## <a name="why-is-application-proxy-a-better-solution"></a>¿Por qué Proxy de aplicación es una solución mejor?
La característica Proxy de aplicación de Azure AD proporciona una solución de acceso remoto sencilla, segura y rentable a todas las aplicaciones locales.

El proxy de aplicación de Azure AD es:

* **Simple**
   * No es necesario cambiar o actualizar las aplicaciones para trabajar con el Proxy de aplicación. 
   * Los usuarios obtienen una experiencia coherente de autenticación. Pueden usar el portal Mis aplicaciones para obtener el inicio de sesión único para las aplicaciones SaaS en la nube y sus aplicaciones locales. 
* **Protección**
   * Al publicar las aplicaciones mediante la característica Proxy de la aplicación de Azure AD, puede sacar partido de los sofisticados controles de autorización y análisis de seguridad de Azure. Obtendrá características de seguridad de escala en la nube y de seguridad de Azure, como el acceso condicional y la verificación en dos pasos.
   * No tendrá que permitir ninguna conexión de entrada a través del firewall para ofrecer acceso remoto a los usuarios. 
* **Rentable**
   * Proxy de aplicación funciona en la nube, por lo que puede ahorrar tiempo y dinero. Las soluciones locales normalmente requieren la configuración y mantenimiento de redes DMZ, servidores perimetrales u otras infraestructuras complejas.  

## <a name="what-kind-of-applications-work-with-application-proxy"></a>¿Qué tipo de aplicaciones funcionan con la característica Proxy de aplicación?
Con esta característica puede tener acceso a diferentes tipos de aplicaciones internas:

* Aplicaciones web que usan la [autenticación integrada de Windows](active-directory-application-proxy-sso-using-kcd.md) para la autenticación.  
* Aplicaciones web que usan el acceso basado en formularios o [basado en encabezados](application-proxy-ping-access.md).  
* API web que desea exponer a aplicaciones sofisticadas de diferentes dispositivos  
* Aplicaciones que se hospedan detrás de una [puerta de enlace de escritorio remoto](application-proxy-publish-remote-desktop.md).  
* Aplicaciones cliente enriquecidas que se integran con la biblioteca de autenticación de Active Directory (ADAL).

## <a name="how-does-application-proxy-work"></a>¿Cómo funciona el proxy de la aplicación?
Hay dos componentes que debe configurar para que el Proxy de aplicación funcione: un conector y un punto de conexión externo. 

El conector es un agente ligero que se encuentra en un servidor de Windows dentro de la red. El conector facilita el flujo de tráfico desde el servicio de Proxy de aplicación en la nube a la aplicación local. Solo usa conexiones de salida, por lo que no tendrá que abrir los puertos de entrada ni colocar nada en la red perimetral. Los conectores no tienen estado y extraen la información de la nube según sea necesario. Para más información sobre los conectores, por ejemplo cómo se equilibra la carga y cómo se autentican, vea [Descripción de los conectores del Proxy de aplicación de Azure AD](application-proxy-understand-connectors.md). 

El punto de conexión externo es la forma que tienen los usuarios de obtener acceso a sus aplicaciones desde fuera de la red. Pueden ir directamente a una dirección URL externa que determine o pueden obtener acceso a la aplicación a través del portal Mis aplicaciones. Cuando los usuarios usan uno de estos puntos de conexión, se autentican en Azure AD y, luego, se enrutan a través del conector a la aplicación local.

 ![Diagrama de Proxy de la aplicación de Azure AD](./media/active-directory-application-proxy-get-started/azureappproxxy.png)

1. El usuario accede a la aplicación a través del servicio Proxy de aplicación y se le dirige a la página de inicio de sesión de Azure AD para que se autentique.
2. Después de iniciar sesión correctamente, se genera un token y se envía al dispositivo cliente.
3. El cliente envía el token al servicio Proxy de aplicación, que recupera el nombre principal de usuario (UPN) y el nombre de entidad de seguridad (SPN) del token y dirige la solicitud al conector del Proxy de aplicación.
4. Si se ha configurado el inicio de sesión único, el conector realiza cualquier autenticación adicional necesaria en nombre del usuario.
5. El conector envía la solicitud a la aplicación local.  
6. La respuesta se envía al usuario mediante el servicio Proxy de aplicación y el conector.

### <a name="single-sign-on"></a>Inicio de sesión único
La característica Proxy de la aplicación de Azure AD proporciona el inicio de sesión único (SSO) a las aplicaciones que usan la autenticación integrada de Windows (IWA), o bien a las aplicaciones para notificaciones. Si la aplicación utiliza IWA, el proxy de aplicación suplanta al usuario mediante la delegación limitada de Kerberos para proporcionar el inicio de sesión único. Si tiene una aplicación para notificaciones que confía en Azure Active Directory, el inicio de sesión único funciona porque el usuario ya se ha autenticado con Azure AD.

Para obtener más información sobre Kerberos, consulte [All you want to know about Kerberos Constrained Delegation (KCD)](https://blogs.technet.microsoft.com/applicationproxyblog/2015/09/21/all-you-want-to-know-about-kerberos-constrained-delegation-kcd) (Todo lo que necesita saber sobre Delegación restringida de kerberos [KCD]).

### <a name="managing-apps"></a>Administración de aplicaciones
Después de publicar la aplicación con Proxy de aplicación, la puede administrar como cualquier otra aplicación de empresa en Azure Portal. Puede usar características de seguridad de Azure Active Directory como el acceso condicional y la verificación en dos pasos, controlar los permisos de usuario y personalizar la personalización de marca para la aplicación. 

## <a name="get-started"></a>Introducción

Antes de configurar Proxy de aplicación, asegúrese de que tiene una [edición de Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/) compatible y un directorio de Azure AD para el que es un administrador global.

Introducción a Proxy de aplicación en dos pasos:

1. [Habilitar el proxy de la aplicación y configurar el conector](active-directory-application-proxy-enable.md).    
2. [Publicar aplicaciones](active-directory-application-proxy-publish.md): use el asistente rápido y sencillo para que sus aplicaciones locales se publiquen y sean accesibles de manera remota.

## <a name="whats-next"></a>Pasos siguientes
Después de publicar la primera aplicación, se puede hacer mucho más con Proxy de aplicación:

* [Habilitar el inicio de sesión único](active-directory-application-proxy-sso-using-kcd.md)
* [Publicar aplicaciones mediante su propio nombre de dominio](active-directory-application-proxy-custom-domains.md)
* [Descripción de los conectores del Proxy de aplicación de Azure AD](application-proxy-understand-connectors.md)
* [Trabajo con servidores proxy locales existentes](application-proxy-working-with-proxy-servers.md) 
* [Establecer una página principal personalizada](application-proxy-office365-app-launcher.md)

Para ver las últimas noticias y actualizaciones, consulte el [blog Application Proxy](http://blogs.technet.com/b/applicationproxyblog/)

