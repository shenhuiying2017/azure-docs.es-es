---
title: "Provisión de acceso remoto seguro a aplicaciones locales"
description: "Explica cómo utilizar el proxy de la aplicación de Azure AD para proporcionar acceso remoto seguro a sus aplicaciones locales."
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
editor: 
ms.assetid: d5450da1-9e06-4d08-8146-011c84922ab5
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/25/2016
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 2e7815702f2d2f4ce935826c4769838727a83696
ms.openlocfilehash: 7d1be1dea6ed4ecda196743f592456a5b977e9b0
ms.lasthandoff: 02/14/2017


---
# <a name="how-to-provide-secure-remote-access-to-on-premises-applications"></a>Provisión de acceso remoto seguro a aplicaciones locales
> [!NOTE]
> Proxy de aplicación es una característica que solo está disponible si actualizó a la edición Premium o Basic de Azure Active Directory. Para obtener más información, consulte [Ediciones de Azure Active Directory](active-directory-editions.md).
> 
> 

Hoy en día, los empleados desean ser productivos en cualquier lugar, en cualquier momento y con cualquier dispositivo. Quieren trabajar en sus propios dispositivos, con independencia de que sean equipos portátiles, teléfonos o tabletas. Asimismo, esperan tener acceso a todas sus aplicaciones, tanto las de SaaS en la nube como las de tipo empresarial en entornos locales. Para proporcionar acceso a las aplicaciones locales, siempre se han utilizado redes privadas virtuales (VPN), zonas desmilitarizadas (DMZ) o servidores proxy inversos locales. No obstante, estas soluciones no solo son complejas y difíciles de proteger, sino también costosas de configurar y administrar.

Hay una forma mejor de realizar esto.

Los recursos de hoy en día, que trabajan en un entorno donde la nube y la movilidad son prioritarias, necesitan una solución moderna de acceso remoto. La característica Proxy de la aplicación de Azure AD forma parte de la oferta de Azure Active Directory Premium y brinda acceso remoto como servicio. Es decir, es fácil de implementar, utilizar y administrar.

## <a name="what-is-azure-active-directory-application-proxy"></a>¿Qué es el proxy de la aplicación de Azure Active Directory?
La característica Proxy de la aplicación de Azure AD proporciona un inicio de sesión único (SSO) y acceso remoto seguro para las aplicaciones web hospedadas de forma local, como los sitios de SharePoint y Outlook Web Access, o bien cualquier otra aplicación web de LOB que tenga. Estas aplicaciones web locales se integran con Azure AD, la misma identidad y plataforma de control que utiliza Office&365;. Los usuarios finales pueden acceder a sus aplicaciones locales del mismo modo en que acceden a Office&365; y otras aplicaciones de SaaS integradas con Azure AD. No hace falta cambiar la infraestructura de red o establecer una VPN para ofrecer esta solución a los usuarios.

## <a name="why-is-this-a-better-solution"></a>¿Por qué es una solución más eficaz?
La característica Proxy de aplicación de Azure AD proporciona una solución de acceso remoto sencilla, segura y rentable a todas las aplicaciones locales.

Proxy de aplicación de Azure AD ofrece estas ventajas:  

* Funciona en la nube, así que puede ahorrarle tiempo y dinero. Las soluciones locales requieren que configure y mantenga DMZ, servidores perimetrales u otras infraestructuras complejas.  
* Es más fácil de configurar y segura que las soluciones locales, ya que no es necesario abrir conexiones entrantes a través del firewall.  
* Ofrece una gran seguridad. Al publicar las aplicaciones mediante la característica Proxy de la aplicación de Azure AD, puede sacar partido de los sofisticados controles de autorización y análisis de seguridad de Azure. Es decir, obtendrá funcionalidades de seguridad avanzadas para todas las aplicaciones existentes sin tener que cambiar aplicaciones.  
* Ofrece a los usuarios una experiencia coherente de autenticación. Gracias al inicio de sesión único, los usuarios podrán acceder mediante una contraseña, con facilidad y rapidez, a todas las aplicaciones que necesitan para ser productivos con una contraseña.  

## <a name="what-kind-of-applications-work-with-azure-ad-application-proxy"></a>¿Qué tipo de aplicaciones funcionan con la característica Proxy de la aplicación de Azure AD?
Con esta característica puede tener acceso a diferentes tipos de aplicaciones internas:

* Aplicaciones web que utilizan la autenticación integrada de Windows para autenticarse  
* Aplicaciones web que utilizan el acceso basado en formularios  
* API web que desea exponer a aplicaciones sofisticadas de diferentes dispositivos  
* Aplicaciones que se encuentran detrás de una puerta de enlace de escritorio remoto  

## <a name="how-does-the-service-work-with-connectors"></a>¿Cómo funciona el servicio con conectores?
La característica Proxy de la aplicación funciona instalando dentro de la red un servicio ligero de Windows denominado "conector". Con este conector, no debe abrir los puertos de entrada ni colocar elementos en la red perimetral. Si sus aplicaciones reciben un elevado volumen de tráfico, puede agregar más conectores, y el servicio se encarga de mantener el equilibrio de carga. Los conectores no tienen estado y extraen todo el contenido de la nube según sea necesario.

Para obtener más información sobre los conectores, consulte [Understand Azure AD Application Proxy connectors](application-proxy-understand-connectors.md) (Descripción de los conectores del proxy de aplicación de Azure AD). 

Cuando los usuarios acceden a las aplicaciones de forma remota, se conectan al punto de conexión publicado. Los usuarios se autentican en Azure AD y, luego, se enrutan a través del conector a la aplicación local.

 ![Diagrama de Proxy de la aplicación de Azure AD](./media/active-directory-appssoaccess-whatis/azureappproxxy.png)

1. El usuario accede a la aplicación a través de Proxy de aplicación y se le dirige a la página de inicio de sesión de Azure AD para que se autentique.
2. Después de iniciar sesión correctamente, se genera un token y se envía al usuario.
3. El usuario envía el token al proxy de la aplicación, que recupera el nombre principal de usuario (UPN) y el nombre de entidad de seguridad (SPN) del token y, después, dirige la solicitud al conector.
4. En nombre del usuario, el conector solicita un vale Kerberos que se puede usar para la autenticación interna (Windows). Esto se conoce como delegación limitada de Kerberos.
5. Active Directory recupera el vale de Kerberos.
6. El vale se envía al servidor de aplicaciones y se comprueba.
7. La respuesta se envía al usuario mediante Proxy de la aplicación.

### <a name="single-sign-on"></a>Inicio de sesión único
La característica Proxy de la aplicación de Azure AD proporciona el inicio de sesión único (SSO) a las aplicaciones que usan la autenticación integrada de Windows (IWA), o bien a las aplicaciones para notificaciones. Si la aplicación utiliza IWA, el proxy de aplicación suplanta al usuario mediante la delegación limitada de Kerberos para proporcionar el inicio de sesión único. Si tiene una aplicación para notificaciones que confía en Azure Active Directory, el inicio de sesión único se logra porque el usuario ya se ha autenticado con Azure AD.

Para más información sobre Kerberos, consulte [All you want to know about Kerberos Constrained Delegation (KCD)](https://blogs.technet.microsoft.com/applicationproxyblog/2015/09/21/all-you-want-to-know-about-kerberos-constrained-delegation-kcd) (Todo lo que necesita saber sobre Delegación restringida de kerberos [KCD]).

## <a name="how-to-get-started"></a>Primeros pasos
Asegúrese de que tiene una suscripción de nivel Básico o Premium de Azure AD y un directorio de Azure AD del cual es usted administrador global. También necesita licencias de nivel Básico o Premium de Azure AD para el administrador de directorios y los usuarios que tienen acceso a las aplicaciones. Consulte [Ediciones de Azure Active Directory](active-directory-editions.md) para obtener más información.

La configuración del proxy de la aplicación se realiza en dos pasos:

1. [Habilitar el proxy de la aplicación y configurar el conector](active-directory-application-proxy-enable.md).    
2. [Publicar aplicaciones](active-directory-application-proxy-publish.md): use el asistente rápido y sencillo para que sus aplicaciones locales se publiquen y sean accesibles de manera remota.

## <a name="whats-next"></a>Pasos siguientes
Hay mucho más que puede hacer con el proxy de la aplicación:

* [Publicar aplicaciones mediante su propio nombre de dominio](active-directory-application-proxy-custom-domains.md)
* [Trabajar con servidores proxy locales existentes](application-proxy-working-with-proxy-servers.md) 
* [Habilitar el inicio de sesión único](active-directory-application-proxy-sso-using-kcd.md)
* [Trabajar con las aplicaciones para notificaciones](active-directory-application-proxy-claims-aware-apps.md)
* [Habilitar el acceso condicional](active-directory-application-proxy-conditional-access.md)

Para ver las últimas noticias y actualizaciones, consulte el [blog Application Proxy](http://blogs.technet.com/b/applicationproxyblog/)


