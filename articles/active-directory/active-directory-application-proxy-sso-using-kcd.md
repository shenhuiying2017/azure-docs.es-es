<properties
	pageTitle="SSO para aplicaciones IWA locales mediante KCD con Proxy de aplicación"
	description="Explica cómo poner en funcionamiento el proxy de la aplicación de Azure AD."
	services="active-directory"
	documentationCenter=""
	authors="rkarlin"
	manager="msStevenPo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/09/2015"
	ms.author="rkarlin"/>



# SSO para aplicaciones IWA locales mediante KCD con Proxy de aplicación


Puede habilitar el inicio de sesión único (SSO) a sus aplicaciones mediante la autenticación de Windows integrada (IWA) dando permiso a los conectores del proxy de aplicación en Active Directory para suplantar a los usuarios y enviar y recibir tokens en su nombre.

> [AZURE.IMPORTANT]Proxy de aplicación es una característica que solo está disponible si actualizó a la edición Premium o Basic de Azure Active Directory. Para obtener más información, consulte [Ediciones de Azure Active Directory](active-directory-editions.md).


## Diagrama de red

![Diagrama de flujos de autenticación de Microsoft AAD](./media/active-directory-application-proxy-sso-using-kcd/AuthDiagram.png)

En este diagrama se explica el flujo cuando un usuario intenta tener acceso a una aplicación local que usa IWA. El flujo general es:

1. El usuario escribe la dirección URL para tener acceso a la aplicación local a través de Proxy de aplicación.
2. Proxy de aplicación redirige la solicitud a los servicios de autenticación de Azure AD para realizar la autenticación previa. En este momento, Azure AD aplica cualquier autenticación correspondiente, así como directivas de autorización, como la autenticación multifactor. Si se valida el usuario, Azure AD crea un token y lo envía al usuario.
3. El usuario pasa el token a Proxy de aplicación.
4. Proxy de aplicación valida el token y recupera del mismo el nombre principal del usuario (UPN), enviando a continuación la solicitud, el UPN y el nombre de entidad de seguridad de servicio (SPN) al conector a través de un canal seguro doblemente autenticado.
5. El conector realiza la negociación de la delegación limitada de Kerberos (KCD) con AD local, suplantando al usuario para obtener un token de Kerberos para la aplicación.
6. Active Directory envía el token de Kerberos para la aplicación al conector.
7. El conector envía la solicitud original al servidor de aplicaciones, con el token de Kerberos que recibió de AD.
8. La aplicación envía la respuesta al conector que, a continuación, se devuelve al servicio Proxy de aplicación y, finalmente, al usuario.

### Requisitos previos

1. Asegúrese de que sus aplicaciones, como las aplicaciones web de SharePoint, se establecen para usar la autenticación de Windows integrada. Para obtener más información, consulte [Habilitar la compatibilidad con la autenticación Kerberos](https://technet.microsoft.com/library/dd759186.aspx) o, para SharePoint, consulte [Planear la autenticación Kerberos en SharePoint 2013](https://technet.microsoft.com/library/ee806870.aspx).
2. Cree nombres de entidad de seguridad de servicio para sus aplicaciones.
3. Asegúrese de que el servidor que ejecuta el conector y el servidor que ejecuta la aplicación que va a publicar se unen mediante dominio y forman parte del mismo dominio. Para obtener más información sobre la unión a un dominio, consulte [Unir un equipo a un dominio](https://technet.microsoft.com/library/dd807102.aspx).


## Configuración de Active Directory

La configuración de Active Directory varía, dependiendo de si su conector del proxy de aplicación y el servidor publicado están en el mismo dominio o no.

### Conector y servidor publicado en el mismo dominio



1. En Active Directory, vaya a **Herramientas** > **Usuarios y equipos**. 
2. Seleccione el servidor que ejecuta el conector. 
3. Haga clic con el botón derecho y seleccione **Propiedades** > **Delegación**. 
4. Seleccione **Confiar en este equipo para la delegación solo a los servicios especificados** y en **Servicios a los que esta cuenta puede presentar credenciales delegadas**, agregue el valor de la identidad del nombre de entidad de servicio (SPN) del servidor de aplicaciones. 
5. Esto permite al conector del proxy de aplicación suplantar a los usuarios en AD en las aplicaciones definidas en la lista.

![Captura de pantalla de ventana Conector-Propiedades SVR](./media/active-directory-application-proxy-sso-using-kcd/Properties.jpg)

### Conector y servidor publicado en dominios distintos

1. Para obtener una lista de requisitos previos para trabajar con KCD entre dominios, vea [Delegación limitada Kerberos entre dominios](https://technet.microsoft.com/library/hh831477.aspx).
2. En Windows 2012 R2, use la propiedad `principalsallowedtodelegateto` en el servidor de conector para permitir que el proxy de la aplicación delegue en este, donde el servidor publicado es `sharepointserviceaccount` y el servidor de delegación es `connectormachineaccount`.

		$connector= Get-ADComputer -Identity connectormachineaccount -server dc.connectordomain.com

		Set-ADComputer -Identity sharepointserviceaccount -PrincipalsAllowedToDelegateToAccount $connector

		Get-ADComputer sharepointserviceaccount -Properties PrincipalsAllowedToDelegateToAccount


>[AZURE.NOTE]`sharepointserviceaccount` puede ser la cuenta del equipo SPS o una cuenta de servicio con la que se ejecuta el grupo de aplicaciones SPS.


## Configuración del Portal de Azure

1. Publique la aplicación según las instrucciones que se describen en [Publicación de aplicaciones con el proxy de la aplicación](active-directory-application-proxy-publish.md). Asegúrese de seleccionar **Azure Active Directory** como **Método de autenticación previa**.
2. Cuando su aplicación aparezca en la lista de aplicaciones, selecciónela y haga clic en **Configurar**.
3. En **Propiedades**, establezca **Método de autenticación interno** en **Autenticación de Windows integrada**.

![Configuración avanzada de aplicaciones](./media/active-directory-application-proxy-sso-using-kcd/cwap_auth2.png)

4. Escriba el **SPN de la aplicación interno** del servidor de aplicaciones. En este ejemplo, el SPN para nuestra aplicación publicada es http/lob.contoso.com.

>[AZURE.IMPORTANT]Los UPN en Azure Active Directory deben ser idénticos a los UPN en su Active Directory local para que funcione la autenticación previa. Asegúrese de que su Azure Active Directory se sincroniza con su Active Directory local.

| | |
| --- | --- |
| Método de autenticación interno | Si utiliza Azure AD para la autenticación previa, puede establecer un método de autenticación interno para permitir a los usuarios beneficiarse de inicio de sesión único (SSO) para esta aplicación. <br><br> Seleccione **Autenticación integrada de Windows** (IWA) si su aplicación usa IWA y podrá configurar la delegación limitada de Kerberos (KCD) para habilitar SSO en esta aplicación. Las aplicaciones que usan IWA deben configurarse mediante KCD; en caso contrario, Proxy de aplicación no podrá publicar estas aplicaciones. <br><br> Seleccione **Ninguno** si su aplicación no usa IWA. |
| SPN de la aplicación interno | Este es el nombre principal del servicio (SPN) de la aplicación interna como está configurado en Azure AD local. El conector del proxy de aplicación utiliza el SPN para obtener los tokens de Kerberos para la aplicación que usa KCD. |

<!--Image references-->
[1]: ./media/active-directory-application-proxy-sso-using-kcd/AuthDiagram.png
[2]: ./media/active-directory-application-proxy-sso-using-kcd/Properties.jpg

<!---HONumber=Sept15_HO3-->