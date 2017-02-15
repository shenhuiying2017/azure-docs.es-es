---
title: "Inicio de sesión único con el proxy de aplicación | Microsoft Docs"
description: "Explica cómo proporcionar el inicio de sesión único mediante el proxy de aplicación de Azure AD."
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
editor: 
ms.assetid: ded0d9c9-45f6-47d7-bd0f-3f7fd99ab621
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/01/2016
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 1eada96773b1d9c6adb9326c9100da7cde8abf77
ms.openlocfilehash: 8df936a03868412adf34672108f40829c41f33ab

---

# <a name="single-sign-on-with-application-proxy"></a>Inicio de sesión único con el proxy de aplicación
El inicio de sesión único es un elemento clave del proxy de aplicación de Azure AD. Proporciona la mejor experiencia de usuario con los siguientes pasos:

1. Un usuario inicia sesión en la nube.  
2. Todas las validaciones de seguridad se realizan en la nube (autenticación previa).  
3. Cuando se envía la solicitud a la aplicación local, el conector del proxy de aplicación suplanta al usuario. La aplicación de back-end piensa que se trata de un usuario normal procedente de un dispositivo unido a dominio.

![Diagrama de acceso desde el usuario final, a través del Proxy de aplicación, a la red corporativa](./media/active-directory-application-proxy-sso-using-kcd/app_proxy_sso_diff_id_diagram.png)

Proxy de la aplicación de Azure AD lo ayuda a brindar una experiencia de inicio de sesión único (SSO) a los usuarios. Use las siguientes instrucciones para publicar las aplicaciones mediante SSO:

## <a name="sso-for-on-prem-iwa-apps-using-kcd-with-application-proxy"></a>SSO para aplicaciones IWA locales mediante KCD con Proxy de aplicación
Puede habilitar el inicio de sesión único a sus aplicaciones mediante la autenticación de Windows integrada (IWA) dando permiso a los conectores del proxy de aplicación en Active Directory para suplantar a los usuarios y enviar y recibir tokens en su nombre.

### <a name="network-diagram"></a>Diagrama de red
En este diagrama se explica el flujo cuando un usuario intenta tener acceso a una aplicación local que usa IWA.

![Diagrama de flujos de autenticación de Microsoft AAD](./media/active-directory-application-proxy-sso-using-kcd/AuthDiagram.png)

1. El usuario escribe la dirección URL para tener acceso a la aplicación local a través de Proxy de aplicación.
2. Proxy de aplicación redirige la solicitud a los servicios de autenticación de Azure AD para realizar la autenticación previa. En este momento, Azure AD aplica cualquier autenticación correspondiente, así como directivas de autorización, como la autenticación multifactor. Si se valida el usuario, Azure AD crea un token y lo envía al usuario.
3. El usuario pasa el token a Proxy de aplicación.
4. Proxy de aplicación valida el token y recupera del mismo el nombre principal del usuario (UPN), enviando a continuación la solicitud, el UPN y el nombre de entidad de seguridad de servicio (SPN) al conector a través de un canal seguro doblemente autenticado.
5. El conector realiza la negociación de la delegación limitada de Kerberos (KCD) con AD local, suplantando al usuario para obtener un token de Kerberos para la aplicación.
6. Active Directory envía el token de Kerberos para la aplicación al conector.
7. El conector envía la solicitud original al servidor de aplicaciones, con el token de Kerberos que recibió de AD.
8. La aplicación envía la respuesta al conector y, después, se devuelve al servicio Application Proxy y, por último, al usuario.

### <a name="prerequisites"></a>Requisitos previos
Antes de empezar a trabajar con SSO para Proxy de la aplicación, asegúrese de que el entorno está preparado con los siguientes ajustes y configuraciones:

* Sus aplicaciones, como las aplicaciones web de SharePoint, están establecidas para usar la autenticación de Windows integrada. Para más información, consulte [Habilitar la compatibilidad con la autenticación Kerberos](https://technet.microsoft.com/library/dd759186.aspx) o, para SharePoint, consulte [Planear la autenticación Kerberos en SharePoint 2013](https://technet.microsoft.com/library/ee806870.aspx).
* Todas las aplicaciones tienen nombres de entidad de seguridad de servicio.
* El servidor que ejecuta el conector y el que ejecuta la aplicación están unidos a dominio y forman parte del mismo dominio o dominios de confianza. Para obtener más información sobre la unión a un dominio, consulte [Unir un equipo a un dominio](https://technet.microsoft.com/library/dd807102.aspx).
* El servidor que ejecuta el conector tiene acceso para leer el atributo TokenGroupsGlobalAndUniversal de los usuarios. Se trata de una configuración predeterminada que podría verse afectada por la seguridad del entorno. Puede obtener más ayuda sobre esta configuración en el artículo [KB2009157](https://support.microsoft.com/en-us/kb/2009157).

### <a name="active-directory-configuration"></a>Configuración de Active Directory
La configuración de Active Directory varía, dependiendo de si su conector del proxy de aplicación y el servidor publicado están en el mismo dominio o no.

#### <a name="connector-and-published-server-in-the-same-domain"></a>Conector y servidor publicado en el mismo dominio
1. En Active Directory, vaya a **Herramientas** > **Usuarios y equipos**.
2. Seleccione el servidor que ejecuta el conector.
3. Haga clic en el botón derecho y seleccione **Propiedades** > **Delegación**.
4. Seleccione **Confiar en este equipo para la delegación solo a los servicios especificados**. En **Servicios a los que esta cuenta puede presentar credenciales delegadas**, agregue el valor de la identidad SPN del servidor de aplicaciones.
5. Esto permite al conector del proxy de aplicación suplantar a los usuarios en AD en las aplicaciones definidas en la lista.

![Captura de pantalla de ventana Conector-Propiedades SVR](./media/active-directory-application-proxy-sso-using-kcd/Properties.jpg)

#### <a name="connector-and-published-server-in-different-domains"></a>Conector y servidor publicado en dominios distintos
1. Para obtener una lista de requisitos previos para trabajar con KCD entre dominios, vea [Delegación limitada Kerberos entre dominios](https://technet.microsoft.com/library/hh831477.aspx).
2. En Windows 2012 R2, utilice la propiedad `principalsallowedtodelegateto` en el servidor de Conector para permitir a Proxy de aplicación delegar para el servidor de Conector, donde el servidor publicado es `sharepointserviceaccount` y, el servidor de delegación, `connectormachineaccount`.

        $connector= Get-ADComputer -Identity connectormachineaccount -server dc.connectordomain.com

        Set-ADComputer -Identity sharepointserviceaccount -PrincipalsAllowedToDelegateToAccount $connector

        Get-ADComputer sharepointserviceaccount -Properties PrincipalsAllowedToDelegateToAccount

> [!NOTE]
> `sharepointserviceaccount` puede ser la cuenta del equipo SPS o una cuenta de servicio con la que se ejecuta el grupo de aplicaciones SPS.
>
>

### <a name="azure-classic-portal-configuration"></a>Configuración del Portal de Azure clásico
1. Publique la aplicación según las instrucciones de [Publicar aplicaciones con el proxy de aplicación](active-directory-application-proxy-publish.md). Asegúrese de seleccionar **Azure Active Directory** como **Método de autenticación previa**.
2. Cuando su aplicación aparezca en la lista de aplicaciones, selecciónela y haga clic en **Configurar**.
3. En **Propiedades**, establezca **Método de autenticación interno** en **Autenticación integrada de Windows**.  
   ![Configuración avanzada de aplicaciones](./media/active-directory-application-proxy-sso-using-kcd/cwap_auth2.png)  
4. Escriba el **SPN de la aplicación interno** del servidor de aplicaciones. En este ejemplo, el SPN para nuestra aplicación publicada es http/lob.contoso.com.  

> [!IMPORTANT]
> Si el UPN local y el UPN de Azure Active Directory no son idénticos, es preciso configurar la [identidad de inicio de sesión delegada](#delegated-login-identity) para que funcione la autenticación previa.
>
>

|  |  |
| --- | --- |
| Método de autenticación interno |Si utiliza Azure AD para la autenticación previa, puede establecer un método de autenticación interno para permitir a los usuarios beneficiarse del inicio de sesión único (SSO) para esta aplicación. <br><br> Seleccione **Autenticación integrada de Windows** (IWA) si su aplicación usa IWA y puede configurar la delegación limitada de Kerberos (KCD) para habilitar SSO en esta aplicación. Las aplicaciones que usan IWA deben configurarse mediante KCD, ya que, de lo contrario, caso contrario, Application Proxy no puede publicar dichas aplicaciones. <br><br> Seleccione **Ninguno** si la aplicación no usa IWA. |
| SPN de la aplicación interno |Este es el nombre principal del servicio (SPN) de la aplicación interna como está configurado en Azure AD local. El conector del proxy de aplicación utiliza el SPN para obtener los tokens de Kerberos para la aplicación que usa KCD. |

## <a name="sso-for-non-windows-apps"></a>SSO para las aplicaciones no son de Windows
El flujo de la delegación de Kerberos del Proxy de aplicación de Azure AD se inicia cuando Azure AD autentica al usuario en la nube. Una vez que la solicitud se recibe en local, el conector de Proxy de aplicación de Azure AD emite un vale Kerberos en nombre del usuario mediante la interacción con Active Directory local. Este proceso se conoce como Delegación limitada de Kerberos (KCD). En la fase siguiente, se envía una solicitud a la aplicación back-end con este vale Kerberos. Haya varios protocolos que definen cómo enviar dichas solicitudes. La mayoría de los servidores que no son Windows esperan Negotiate/SPNego, que ahora se admite en el proxy de aplicación de Azure AD.

![Diagrama de SSO que no es de Windows](./media/active-directory-application-proxy-sso-using-kcd/app_proxy_sso_nonwindows_diagram.png)

### <a name="delegated-login-identity"></a>identidad de inicio de sesión delegada
La identidad de inicio de sesión delegada le ayuda a controlar los dos escenarios de inicio de sesión diferentes:

* Aplicaciones que no son de Windows que suelen obtener la identidad de usuario en forma de un nombre de usuario o nombre de cuenta SAM, no como una dirección de correo electrónico (username@domain).
* Aquellas configuraciones de inicio de sesión alternativas en las que el UPN de Azure AD y el UPN de Active Directory local son diferentes.

Con el proxy de aplicación, puede seleccionar qué identidad utilizar para obtener el vale de Kerberos. Esta configuración es por aplicación. Algunas de estas opciones son adecuadas para los sistemas que no aceptan el formato de dirección de correo electrónico, otras están pensadas para el inicio de sesión alternativo.

![Captura de pantalla de parámetro de identidad de inicio de sesión delegado](./media/active-directory-application-proxy-sso-using-kcd/app_proxy_sso_diff_id_upn.png)

Si se utiliza la identidad de inicio de sesión delegada, el valor no puede ser único para todos los dominios o bosques de la organización. Puede evitar este problema al publicar estas aplicaciones dos veces con dos grupos diferentes de conector. Puesto que cada aplicación tiene una audiencia de usuarios diferente, puede unir sus conectores a un dominio diferente.

## <a name="working-with-sso-when-on-premises-and-cloud-identities-are-not-identical"></a>Trabajar con SSO cuando las identidades locales y en la nube no son idénticas
A menos que se configuren de otra manera, el Proxy de aplicación supone que los usuarios tienen exactamente la misma identidad en la nube y en local. Para cada aplicación, puede configurar qué identidad se debe usar al realizar el inicio de sesión único.  

Esta funcionalidad permite a muchas organizaciones que tienen identidades locales y en la nube diferentes disponer de SSO en aplicaciones locales desde aplicaciones en la nube sin que los usuarios tengan que escribir nombres de usuario y contraseñas diferentes. Esto incluye las organizaciones que:

* Tienen varios dominios internamente (joe@us.contoso.com, joe@eu.contoso.com) y un único dominio en la nube (joe@contoso.com).
* Tienen un nombre de dominio no enrutable internamente ((joe@contoso.usa)) y uno legal en la nube.
* No utilizan nombres de dominio internamente (joe)
* Usan distintos alias locales y en la nube. Por ejemplo, joe-johns@contoso.com frente a joej@contoso.com  

También le ayuda con aplicaciones que no aceptan direcciones en forma de dirección de correo electrónico, que es un escenario muy común en servidores back-end que no son Windows.

### <a name="setting-sso-for-different-cloud-and-on-prem-identities"></a>Configuración de SSO para diferentes identidades en la nube y locales diferentes
1. Configure Azure AD Connect para que la identidad principal sea la dirección de correo electrónico (correo). Esto se realiza como parte del proceso de personalización, al cambiar el campo **Nombre principal de usuario** en la configuración de sincronización. Esta configuración también determina la forma en que los usuarios inician sesión en dispositivos con Office 365 y Windows10, y otras aplicaciones que usan Azure AD como almacén de identidades.  
   ![Captura de pantalla de la identificación de usuarios - lista desplegable de Nombre principal de usuario](./media/active-directory-application-proxy-sso-using-kcd/app_proxy_sso_diff_id_connect_settings.png)  
2. En las opciones de Configuración de la aplicación correspondientes a la aplicación que quiere modificar, seleccione la información en **Identidad de inicio de sesión delegada** que quiere usar:

   * Nombre principal de usuario: joe@contoso.com  
   * Nombre principal de usuario alternativo: joed@contoso.local  
   * Parte de nombre de usuario de Nombre principal de usuario: joe  
   * Parte de nombre de usuario de Nombre principal de usuario alternativo: joed  
   * Nombre de cuenta SAM local: depende de la configuración de controlador de dominio local.

   ![Captura de pantalla del menú desplegable de identidad de inicio de sesión delegada](./media/active-directory-application-proxy-sso-using-kcd/app_proxy_sso_diff_id_upn.png)  

### <a name="troubleshooting-sso-for-different-identities"></a>Solución de problemas de SSO para diferentes identidades
Si se produce un error en el proceso de SSO, aparece en el registro de eventos de la máquina del conector, como se explica en [Solución de problemas](active-directory-application-proxy-troubleshoot.md).
Pero, en algunos casos, la solicitud se envía correctamente a la aplicación del back-end mientras que esta aplicación responde en otras respuestas HTTP. La solución de problemas de estos casos debe empezar examinando el número de evento 24029 en la máquina del conector en el registro de eventos de sesión del Proxy de aplicación. La identidad del usuario que se usó para la delegación aparece en el campo “usuario” de los detalles del evento. Para activar el registro de sesión, seleccione **Mostrar registros analíticos y de depuración** en el menú de vista del Visor de eventos.

## <a name="see-also"></a>Otras referencias
* [Publicar aplicaciones con Proxy de aplicación](active-directory-application-proxy-publish.md)
* [Solucionar los problemas que tiene con el Proxy de aplicación](active-directory-application-proxy-troubleshoot.md)
* [Trabajar con las aplicaciones para notificaciones](active-directory-application-proxy-claims-aware-apps.md)
* [Habilitar el acceso condicional](active-directory-application-proxy-conditional-access.md)

Para ver las últimas noticias y actualizaciones, consulte el [blog Application Proxy](http://blogs.technet.com/b/applicationproxyblog/)

<!--Image references-->
[1]: ./media/active-directory-application-proxy-sso-using-kcd/AuthDiagram.png
[2]: ./media/active-directory-application-proxy-sso-using-kcd/Properties.jpg



<!--HONumber=Feb17_HO2-->


