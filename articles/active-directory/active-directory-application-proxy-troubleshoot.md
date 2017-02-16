---
title: "Solución de problemas del proxy de aplicación | Microsoft Docs"
description: "Explica cómo solucionar errores en Proxy de aplicación de Azure AD."
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
editor: 
ms.assetid: 970caafb-40b8-483c-bb46-c8b032a4fb74
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/01/2016
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 1eada96773b1d9c6adb9326c9100da7cde8abf77
ms.openlocfilehash: e3143d787104ffbba9fb0c3806b187382d9d5c5b

---


# <a name="troubleshoot-application-proxy"></a>Solucionar problemas de Proxy de aplicación
Si se producen errores al obtener acceso a una aplicación publicada o al publicar aplicaciones, compruebe las siguientes opciones para ver si Proxy de aplicación de Microsoft Azure AD funciona correctamente:

* Abra la consola de Servicios de Windows y compruebe que el servicio **Microsoft AAD Application Proxy Connector** (Conector del proxy de aplicación de Microsoft AAD) está habilitado y en ejecución. Puede que también quiera consultar la página de propiedades del servicio Proxy de aplicación, como se muestra en la imagen siguiente:   
  ![Captura de pantalla de la ventana de propiedades del conector del proxy de aplicación de Microsoft AAD](./media/active-directory-application-proxy-troubleshoot/connectorproperties.png)
* Abra el Visor de eventos y busque eventos del conector del proxy de aplicación en **Registros de aplicaciones y servicios** > **Microsoft** > ** AadApplicationProxy** > **Conector** > **Administrador**.
* Si es necesario, puede disponer de registros más detallados si activa los registros de análisis y depuración, así como el registro de sesiones del conector del proxy de aplicación.

## <a name="the-page-is-not-rendered-correctly"></a>La página no se representa correctamente.
Si no va a recibir un mensaje de error específico, es posible que tenga problemas con la representación o funcionamiento incorrecto de la aplicación. Esto puede ocurrir si se publica la ruta de acceso del artículo, pero la aplicación requiere contenido que existe fuera de dicha ruta de acceso.

Por ejemplo, si publica la ruta de acceso https://yourapp/app, pero la aplicación llama a imágenes de https://yourapp/media, no se representarán. Asegúrese de publicar la aplicación con la ruta de nivel superior que debe incluir todo el contenido relevante. En este ejemplo sería http://yourapp/.

Si cambia la ruta de acceso para incluir el contenido al que se hace referencia, pero necesita que los usuarios lleguen a un vínculo más profundo de la ruta de acceso, consulte la entrada del blog [Setting the right link for Application Proxy applications in the Azure AD access panel and Office 365 app launcher](https://blogs.technet.microsoft.com/applicationproxyblog/2016/04/06/setting-the-right-link-for-application-proxy-applications-in-the-azure-ad-access-panel-and-office-365-app-launcher/)(Establecimiento del vínculo correcto para las aplicaciones del proxy de aplicación en el panel de acceso de Azure AD y en el selector de programas de Office 365).

## <a name="general-errors"></a>Errores generales
| Error | Description | Resolución |
| --- | --- | --- |
| No se puede tener acceso a esta aplicación corporativa. No está autorizado para tener acceso a esta aplicación. Error de autorización. Asegúrese de asignar el usuario con acceso a esta aplicación. |Puede que no haya asignado el usuario para esta aplicación. |Vaya a la pestaña **Aplicación** y, en **Usuarios y grupos**, asigne este usuario o grupo de usuarios a esta aplicación. |
| No se puede tener acceso a esta aplicación corporativa. No está autorizado para tener acceso a esta aplicación. Error de autorización. Asegúrese de que el usuario tiene una licencia de Azure Active Directory Premium o Basic. |Es posible que los usuarios reciban este error al intentar acceder a la aplicación que publicó si el administrador del suscriptor no les asignó explícitamente una licencia Premium o Basic. |Vaya a la pestaña **Licencias** de Active Directory del suscriptor y asegúrese de que se asigne a este usuario o grupo de usuarios una licencia Premium o Básica. |

## <a name="connector-troubleshooting"></a>Solución de problemas del conector
Si se produce un error en el registro durante la instalación del Asistente para el conector, hay dos maneras de ver el motivo de dicho error. Se puede examinar el registro de eventos en **Registros de aplicaciones y servicios/\Microsoft\AadApplicationProxy\Connector\Admin, o bien se puede ejecutar el siguiente comando de Windows PowerShell**.

    Get-EventLog application –source “Microsoft AAD Application Proxy Connector” –EntryType “Error” –Newest 1

| Error | Description | Resolución |
| --- | --- | --- |
| Error de registro del conector: asegúrese de haber habilitado Proxy de aplicación en el Portal de administración de Azure, así como de haber escrito correctamente su nombre de usuario y contraseña de Active Directory. Error: 'Se han producido uno o más errores'. |Cerró la ventana de registro sin iniciar sesión en Azure AD. |Vuelva a ejecutar el asistente del conector y regístrelo. |
| Error de registro del conector: asegúrese de haber habilitado Proxy de aplicación en el Portal de administración de Azure, así como de haber escrito correctamente su nombre de usuario y contraseña de Active Directory. Error: 'AADSTS50001: el recurso `https://proxy.cloudwebappproxy.net /registerapp` está deshabilitado'. |Proxy de aplicación está deshabilitado. |Asegúrese de habilitar Proxy de aplicación en el Portal de Azure clásico antes de intentar registrar el conector. Para obtener más información acerca de cómo habilitar Proxy de aplicación, consulte [Habilitar servicios de Proxy de aplicación](active-directory-application-proxy-enable.md). |
| Error de registro del conector: asegúrese de haber habilitado Proxy de aplicación en el Portal de administración de Azure, así como de haber escrito correctamente su nombre de usuario y contraseña de Active Directory. Error: 'Se han producido uno o más errores'. |Si se abre la ventana de registro y, a continuación, se cierra inmediatamente sin permitirle iniciar sesión, es probable que reciba este error. Este error se produce cuando hay un error de red en el sistema. |Asegúrese de que es posible conectarse desde un explorador a un sitio web público y de que los puertos están abiertos como se especifica en [Requisitos previos del proxy de aplicación](active-directory-application-proxy-enable.md). |
| Error de registro del conector: asegúrese de que su equipo está conectado a Internet. Error: 'No había ningún extremo escuchando en `https://connector.msappproxy.net :9090/register/RegisterConnector` que pudiera aceptar el mensaje. La causa suele ser una dirección o una acción SOAP incorrecta. Consulte InnerException, si está presente, para obtener más información'. |Si inicia sesión con su nombre de usuario y contraseña de Azure AD y aparece este error, es posible que todos los puertos por encima del 8081 estén bloqueados. |Asegúrese de que los puertos necesarios están abiertos. Para más información, consulte [Requisitos previos del proxy de la aplicación](active-directory-application-proxy-enable.md). |
| Aparecerá un error de borrado en la ventana de registro. No se puede continuar, solo para cerrar la ventana |Escribió el nombre de usuario o contraseña incorrectos. |Inténtelo de nuevo. |
| Error de registro del conector: asegúrese de haber habilitado Proxy de aplicación en el Portal de administración de Azure, así como de haber escrito correctamente su nombre de usuario y contraseña de Active Directory. Error: ' AADSTS50059: No se ha producido ningún error en la información de identificación del inquilino en la solicitud o implícita en ninguna credencial proporcionada y búsqueda por identificador URI de la entidad de servicio. |Intenta iniciar sesión con una cuenta Microsoft y no un dominio que forma parte del id. de organización del directorio al que intenta tener acceso. |Asegúrese de que el administrador forma parte del mismo nombre de dominio que el dominio del inquilino, por ejemplo, si el dominio de Azure AD es contoso.com, el administrador debe ser admin@contoso.com. |
| Error al recuperar la directiva de ejecución actual para ejecutar scripts de PowerShell. |Si se produce un error en la instalación del conector, compruebe que la directiva de ejecución de PowerShell no está deshabilitada. |Abra el Editor de directivas de grupo. Vaya a **Configuración del equipo** > **Plantillas administrativas** > **Componentes de Windows** > **Windows PowerShell** y haga doble clic en **Activar la ejecución de scripts**. Aquí se pueden seleccionar **No configurado** o **Habilitado**. Si elige **Habilitado**, asegúrese de que, en Opciones, en Directiva de ejecución se selecciona en **Permitir scripts locales y scripts remotos firmados** o en **Permitir todos los scripts**. |
| El conector no pudo descargar la configuración. |El certificado de cliente del conector, que se utiliza para la autenticación, ha expirado. Esto también puede ocurrir si tiene instalado el conector detrás de un proxy. En este caso, el conector no puede acceder a Internet ni tampoco podrá proporcionar aplicaciones a usuarios remotos. |Renueve la confianza manualmente mediante el cmdlet `Register-AppProxyConnector` en Windows PowerShell. Si el conector está detrás de un proxy, es preciso otorgar acceso a Internet a las cuentas del conector "servicios de red" y "sistema local". Esto puede realizarse concediéndoles acceso al proxy o estableciéndoles para omitir el proxy. |
| Error de registro del conector: asegúrese de que es administrador global de su Active Directory para registrar el conector. Error: 'Se ha denegado la solicitud de registro'. |El alias con el que intenta iniciar sesión no es administrador en este dominio. Su conector siempre está instalado para el directorio que posee el dominio del usuario. |Asegúrese de que el administrador como el que intenta iniciar sesión tiene permisos globales para el inquilino de Azure AD. |

## <a name="kerberos-errors"></a>Errores de Kerberos
| Error | Description | Resolución |
| --- | --- | --- |
| Error al recuperar la directiva de ejecución actual para ejecutar scripts de PowerShell. |Si se produce un error en la instalación del conector, compruebe que la directiva de ejecución de PowerShell no está deshabilitada. |Abra el Editor de directivas de grupo. Vaya a **Configuración del equipo** > **Plantillas administrativas** > **Componentes de Windows** > **Windows PowerShell** y haga doble clic en **Activar la ejecución de scripts**. Aquí se pueden seleccionar **No configurado** o **Habilitado**. Si elige **Habilitado**, asegúrese de que, en Opciones, en Directiva de ejecución se selecciona en **Permitir scripts locales y scripts remotos firmados** o en **Permitir todos los scripts**. |
| 12008: Azure AD superó el número máximo de intentos de autenticación Kerberos permitidos en el servidor backend. |Este evento puede indicar una configuración incorrecta entre Azure AD y el servidor de aplicaciones backend o un problema en la configuración de fecha y hora de ambos equipos. |El servidor backend rechazó el vale Kerberos creado por Azure AD. Compruebe que tanto Azure AD como el servidor de aplicaciones back-end están configurados correctamente. Asegúrese de que la configuración de fecha y hora de Azure AD y el servidor de aplicaciones backend está sincronizada. |
| 13016: Azure AD no puede recuperar un vale Kerberos en nombre del usuario porque no hay ningún UPN en el token perimetral o en la cookie de acceso. |Hay un problema con la configuración de STS. |Corrija la configuración de notificación de UPN en STS. |
| 13019: Azure AD no puede recuperar un vale Kerberos en nombre del usuario debido al siguiente error general API. |Este evento puede indicar una configuración incorrecta entre Azure AD y el servidor controlador de dominios o un problema en la configuración de fecha y hora de ambos equipos. |El controlador de dominio rechazó el vale Kerberos creado por Azure AD. Compruebe que tanto Azure AD como el servidor de aplicaciones back-end están configurados correctamente, especialmente la configuración de SPN. Asegúrese de que Azure AD está unido mediante dominio al mismo dominio que el controlador de dominio para garantizar que este establezca la confianza con Azure AD. Asegúrese de que la configuración de fecha y hora de Azure AD y el controlador de dominio está sincronizada. |
| 13020: Azure AD no puede recuperar un vale Kerberos en nombre del usuario al no estar definido el SPN del servidor backend. |Este evento puede indicar una configuración incorrecta entre Azure AD y el servidor controlador de dominios o un problema en la configuración de fecha y hora de ambos equipos. |El controlador de dominio rechazó el vale Kerberos creado por Azure AD. Compruebe que tanto Azure AD como el servidor de aplicaciones back-end están configurados correctamente, especialmente la configuración de SPN. Asegúrese de que Azure AD está unido mediante dominio al mismo dominio que el controlador de dominio para garantizar que este establezca la confianza con Azure AD. Asegúrese de que la configuración de fecha y hora de Azure AD y el controlador de dominio está sincronizada. |
| 13022: Azure AD no puede autenticar el usuario porque el servidor backend responde a los intentos de autenticación Kerberos con un error HTTP 401. |Este evento puede indicar una configuración incorrecta entre Azure AD y el servidor de aplicaciones backend o un problema en la configuración de fecha y hora de ambos equipos. |El servidor backend rechazó el vale Kerberos creado por Azure AD. Compruebe que tanto Azure AD como el servidor de aplicaciones back-end están configurados correctamente. Asegúrese de que la configuración de fecha y hora de Azure AD y el servidor de aplicaciones backend está sincronizada. |
| El sitio web no puede mostrar la página. |Es posible que el usuario reciba este error al intentar acceder a la aplicación que publicó esta es una aplicación IWA. Es posible que el SPN definido para esta aplicación no sea correcto. |Para las aplicaciones IWA: asegúrese de que el SPN configurado para esta aplicación es correcto. |
| El sitio web no puede mostrar la página. |Es posible que el usuario reciba este error al intentar acceder a la aplicación que publicó esta es una aplicación OWA. Esto puede deberse a uno de los motivos siguientes:  <br>  - El SPN definido para esta aplicación no es correcto. <br>  - El usuario que intentó acceder a la aplicación usa una cuenta Microsoft, en lugar de la cuenta corporativa adecuada para iniciar sesión, o bien el usuario es un usuario invitado. <br>  - El usuario que intentó acceder a la aplicación no está definido correctamente para esta aplicación a nivel local. |Los pasos para solucionar estos problemas son:  <br>  - Asegúrese de que el SPN configurado para esta aplicación es correcto. <br>  - Asegúrese de que el usuario inicia sesión con la cuenta corporativa que coincide con el dominio de la aplicación publicada. Los usuarios y el invitado de la cuenta Microsoft no pueden acceder a las aplicaciones IWA. <br>  - Asegúrese de que este usuario tenga los permisos adecuados definidos para esta aplicación back-end en el equipo local. |
| No se puede tener acceso a esta aplicación corporativa. No está autorizado para tener acceso a esta aplicación. Error de autorización. Asegúrese de asignar el usuario con acceso a esta aplicación. |Los usuarios pueden recibir este error al intentar acceder a la aplicación que publicó si para iniciar sesión usan cuentas Microsoft, en lugar de su cuenta corporativa. Los usuarios invitados también pueden recibir este error. |Los usuarios y los invitados de la Cuenta Microsoft no pueden tener acceso a aplicaciones IWA. Asegúrese de que el usuario inicia sesión con la cuenta corporativa que coincide con el dominio de la aplicación publicada. |
| No se puede tener acceso a esta aplicación corporativa en este momento. Inténtelo de nuevo más tarde... Se agotó el tiempo de espera del conector. |Los usuarios pueden recibir este error al intentar acceder a la aplicación que publicó si no está definidos correctamente para esta aplicación a nivel local. |Asegúrese de que los usuarios tengan los permisos adecuados definidos para esta aplicación back-end en el equipo local. |

## <a name="see-also"></a>Otras referencias
* [Habilitación del proxy de aplicación de Azure Active Directory](active-directory-application-proxy-enable.md)
* [Publicar aplicaciones con Proxy de aplicación](active-directory-application-proxy-publish.md)
* [Habilitar el inicio de sesión único](active-directory-application-proxy-sso-using-kcd.md)
* [Habilitar el acceso condicional](active-directory-application-proxy-conditional-access.md)

Para ver las últimas noticias y actualizaciones, consulte el [blog Application Proxy](http://blogs.technet.com/b/applicationproxyblog/)

<!--Image references-->
[1]: ./media/active-directory-application-proxy-troubleshoot/connectorproperties.png
[2]: ./media/active-directory-application-proxy-troubleshoot/sessionlog.png



<!--HONumber=Dec16_HO4-->


