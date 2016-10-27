<properties
    pageTitle="Inicio de sesión de usuarios de Azure AD Connect | Microsoft Azure"
    description="Inicio de sesión de usuarios de Azure AD Connect para la configuración personalizada."
    services="active-directory"
    documentationCenter=""
    authors="billmath"
    manager="femila"
    editor="curtand"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/08/2016"
    ms.author="billmath"/>




# <a name="azure-ad-connect-user-sign-on-options"></a>Opciones para el inicio de sesión de los usuarios en Azure AD Connect

Azure AD Connect permite que los usuarios inicien sesión en los recursos en la nube y locales con las mismas contraseñas.  Puede elegir entre varias maneras de habilitar esta opción.

## <a name="choosing-a-user-sign-in-method"></a>Elección de un método de inicio de sesión de usuario
Para la mayoría de las organizaciones que simplemente desean habilitar el inicio de sesión de usuarios en Office 365, aplicaciones SaaS y otros recursos basados en Azure AD, se recomienda la opción de sincronización de contraseña predeterminada.
Sin embargo, algunas organizaciones tienen razones concretas para usar una opción de inicio de sesión federado, como AD FS.  Entre ellos se incluyen los siguientes:

- Su organización ya tiene implementado AD FS o un proveedor de federación de terceros.
- La directiva de seguridad prohíbe la sincronización de los valores hash de contraseña en la nube.
- Se requiere que los usuarios experimenten un inicio de sesión único fluido (sin solicitudes de contraseña adicionales) al tener acceso a recursos en la nube a partir de equipos unidos a dominio en la red corporativa.
- Se requieren algunas de las capacidades específicas de AD FS:
    - Multi-Factor Authentication a nivel local mediante un proveedor de terceros o tarjetas inteligentes (obtenga información acerca de los proveedores de MFA de terceros para AD FS en Windows Server 2012 R2).
    - Características de integración de Active Directory, como el bloqueo de cuenta no rígido o la directiva de horas de trabajo y contraseña de AD.
    - Acceso condicional a los recursos locales y en la nube mediante el registro de dispositivos, la unión a Azure AD o las directivas MDM de Intune.

### <a name="password-synchronization"></a>Sincronización de contraseñas
Con la sincronización de contraseñas, se sincronizan los valores hash de las contraseñas de usuario de Active Directory local con Azure AD.  Cuando las contraseñas se cambian o se restablecen de forma local, las nuevas contraseñas se sincronizan de inmediato con Azure AD para que los usuarios puedan utilizar siempre en los recursos en la nube la misma contraseña que utilizan a nivel local.  Las contraseñas nunca se envían a Azure AD ni se almacenan en Azure AD en texto no cifrado.
La sincronización de contraseñas puede utilizarse junto con la escritura diferida de contraseñas para habilitar el autoservicio de restablecimiento de contraseña en Azure AD.

<center>![Nube](./media/active-directory-aadconnect-user-signin/passwordhash.png)</center>

[Más información acerca de la sincronización de contraseñas](active-directory-aadconnectsync-implement-password-synchronization.md)


### <a name="federation-using-a-new-or-existing-ad-fs-in-windows-server-2012-r2-farm"></a>Federación con AD FS nuevo o existente en granja de Windows Server 2012 R2
Con el inicio de sesión federado, los usuarios pueden iniciar sesión en los servicios basados en Azure AD con sus contraseñas locales y, mientras se encuentren en la red corporativa, sin necesidad de volver a introducir sus contraseñas.  La opción de federación con AD FS le permite implementar servicios AD FS nuevos o especificar servicios AD FS existentes en la granja de Windows Server 2012 R2.  Si decide especificar una granja existente, Azure AD Connect configurará la confianza entre la granja y Azure AD para que los usuarios puedan iniciar sesión.

<center>![Nube](./media/active-directory-aadconnect-user-signin/federatedsignin.png)</center>

#### <a name="to-deploy-federation-with-ad-fs-in-windows-server-2012-r2,-you-will-need-the-following"></a>Para implementar la federación con AD FS en Windows Server 2012 R2, necesitará lo siguiente.

Si va a implementar una nueva granja:

- Un servidor Windows Server 2012 R2 para el servidor de federación.
- Un servidor Windows Server 2012 R2 para el Proxy de aplicación web.
- un archivo .pfx con un certificado SSL para el nombre de servicio de federación previsto, como fs.contoso.com.

Si va a implementar una nueva granja o va a utilizar una granja existente:

- Credenciales de administrador local en los servidores de federación.
- Credenciales de administrador local en cualquier servidor de grupo de trabajo (no unido a dominio) en el que pretenda implementar el rol Proxy de aplicación web.
- La máquina en la que se ejecuta al asistente debe poder conectarse a otras máquinas en las que va a instalar AD FS o el Proxy de aplicación web a través de Administración remota de Windows.

#### <a name="sign-on-using-an-earlier-version-of-ad-fs-or-a-third-party-solution"></a>Inicio de sesión con una versión anterior de AD FS o una solución de terceros

Si ya ha configurado el inicio de sesión en la nube con una versión anterior de AD FS (por ejemplo, AD FS 2.0) o un proveedor de federación de terceros, puede optar por omitir la configuración del inicio de sesión de usuarios a través de Azure AD Connect.  De este modo, podrá obtener la sincronización más reciente y otras capacidades de Azure AD Connect mientras sigue usando la solución existente para el inicio de sesión.

## <a name="user-sign-in-and-user-principal-name-(upn)"></a>Inicio de sesión de usuario y nombre principal de usuario (UPN)

### <a name="understanding-user-principal-name"></a>Descripción del nombre principal de usuario

En Active Directory, el sufijo UPN predeterminado es el nombre DNS del dominio en el que se creó la cuenta de usuario. En la mayoría de los casos, se trata del nombre de dominio registrado como dominio de empresa en Internet. Sin embargo, puede agregar más sufijos UPN usando Dominios y confianzas de Active Directory.

El UPN del usuario tiene el formato username@domai. Por ejemplo, para una instancia de Active Directory como "contoso.com", el usuario John podría tener un UPN john@contoso.com. El UPN del usuario se basa en la especificación RFC 822. Aunque el UPN y el correo electrónico comparten el mismo formato, el valor de UPN para un usuario puede o no ser igual a la dirección de correo electrónico del usuario.

### <a name="user-principal-name-in-azure-ad"></a>Nombre principal de usuario en Azure AD

El Asistente para Azure AD Connect usará el atributo userPrincipalName o le permiten especificar el atributo (en la instalación personalizada) para su uso local como nombre principal de usuario en Azure AD. Este es el valor que se usará para iniciar sesión en Azure AD. Si el valor del atributo de nombre principal del usuario no corresponde a un dominio comprobado en Azure AD, a continuación, Azure AD lo reemplazará con un valor predeterminado ".onmicrosoft.com".

Todos los directorios de Azure Active Directory vienen con un nombre de dominio integrado con el formato "contoso.onmicrosoft.com", lo que le permite empezar a utilizar Azure u otros servicios de Microsoft. Puede mejorar y simplificar la experiencia de inicio de sesión utilizando dominios personalizados. Para informarse sobre los nombres de dominio personalizado en Azure AD y cómo comprobar un dominio, consulte [Incorporación de su nombre de dominio personalizado a Azure Active Directory](active-directory-add-domain.md#add-your-custom-domain-name-to-azure-active-directory)

## <a name="azure-ad-sign-in-configuration"></a>Configuración de inicio de sesión de Azure AD

### <a name="azure-ad-sign-in-configuration-with-azure-ad-connect"></a>Configuración de inicio de sesión de Azure AD con Azure AD Connect
La experiencia de inicio de sesión de Azure AD depende de si este puede hacer coincidir el sufijo del nombre principal del usuario que se esté sincronizando con uno de los dominios personalizados comprobados en el directorio de Azure AD. Azure AD Connect proporciona ayuda durante la configuración del inicio de sesión de Azure AD, para que la experiencia de inicio de sesión de usuario en la nube sea similar a la experiencia local. Azure AD Connect muestra los sufijos UPN definidos para los dominios e intenta hacerlos coincidir con un dominio personalizado en Azure AD, ayudándole con la acción apropiada que sea necesario realizar.
La página de inicio de sesión de Azure AD enumera los sufijos UPN definidos para Active Directory local y muestra el estado correspondiente en cada sufijo. Los valores de estado pueden ser uno de los siguientes:

* Verified (Comprobado): Azure AD Connect encontró una coincidencia comprobada de dominio en Azure AD y no es necesario realizar ninguna acción
* Not verified (Sin comprobar): Azure AD Connect encontró una coincidencia de dominio personalizado en Azure AD pero no está comprobada. El usuario debe comprobar el dominio personalizado con el fin de asegurarse de que el sufijo UPN de los usuarios no se cambia al sufijo predeterminado ".onmicrosoft.com" después de la sincronización.
* Not added (Sin agregar): Azure AD Connect no encontró un dominio personalizado que se correspondiera con el sufijo UPN. El usuario tiene que agregar y comprobar un dominio personalizado que se corresponda con el sufijo UPN con el fin de asegurarse de que el sufijo UPN del usuario no se cambia al sufijo predeterminado ".onmicrosoft.com" después de la sincronización.

Página de inicio de sesión de AD Azure muestra los sufijos UPN definidos para Active Directory local y el dominio personalizado correspondiente en Azure AD con el estado actual de comprobación. En una instalación personalizada, ahora puede seleccionar el atributo de nombre principal de usuario en la página de **Inicio de sesión en Azure AD** .

![Página de inicio de sesión de AD Azure](.\media\active-directory-aadconnect-user-signin\custom_azure_sign_in.png)

Puede hacer clic en el botón de actualización para volver a capturar el estado más reciente de los dominios personalizados de Azure AD.

###<a name="selecting-attribute-for-user-principal-name-in-azure-ad"></a>Seleccione el atributo de nombre principal de usuario en Azure AD

UserPrincipalName: userPrincipalName es el atributo que los usuarios utilizarán al iniciar sesión en Azure AD y Office 365. Los dominios utilizados, también conocidos como sufijo UPN, deben comprobarse en Azure AD antes de que se sincronicen los usuarios. Se recomienda encarecidamente mantener el atributo userPrincipalName predeterminado. Si este atributo no es enrutable y no se puede comprobar, se puede seleccionar otro atributo, por ejemplo, correo electrónico, como atributo que contiene el identificador de inicio de sesión. Esto se conoce como un identificador alternativo. El valor del atributo Alternate ID debe seguir el estándar RFC822. El atributo Alternate ID puede usarse como solución de inicio de sesión tanto con el inicio de sesión único (SSO) de contraseña como con el SSO de federación.

>[AZURE.NOTE] El uso de un identificador alternativo no es compatible con todas las cargas de trabajo de Office 365. Para obtener más información, vea [Configuración del identificador de inicio de sesión alternativo](https://technet.microsoft.com/library/dn659436.aspx).

#### <a name="different-custom-domain-states-and-effect-on-azure-sign-in-experience"></a>Diferentes estados de los dominios personalizados y su efecto en el inicio de sesión en Azure
Es muy importante comprender la relación entre los estados de un dominio personalizado en el directorio de Azure AD y los sufijos UPN definidos de forma local. Vamos a revisar las distintas experiencias de inicio de sesión de Azure cuando se configura la sincronización usando Azure AD Connnect.

Para la información a continuación, supongamos que estamos interesados en el sufijo UPN "contoso.com" que se usa en el directorio local como parte del UPN, por ejemplo user@contoso.com.

###### <a name="express-settings-/-password-synchronization"></a>Configuración rápida / Sincronización de contraseñas
| Estado         | Efecto en la experiencia de usuario sobre el inicio de sesión de Azure |
|:-------------:|:----------------------------------------|
| Not added (Sin agregar) | En este caso no se ha agregado ningún dominio personalizado para "contoso.com" en el directorio de Azure AD. Los usuarios que tienen el UPN local con el sufijo @contoso.com,, no podrán usar su UPN local para iniciar sesión en Azure. En su lugar, tendrán que utilizar un UPN nuevo que les proporcionará Azure AD, agregando el sufijo para el directorio predeterminado de Azure AD. Por ejemplo, si la sincronización de los usuarios es con el directorio de Azure AD azurecontoso.onmicrosoft.com, al usuario local user@contoso.com se le dará un UPN user@azurecontoso.onmicrosoft.com|
| Not verified (Sin comprobar) | En este caso tenemos un dominio personalizado de "contoso.com" agregado en el directorio de Azure AD pero que no se ha comprobado. Si continúa con la sincronización de los usuarios sin comprobar el dominio, Azure AD asignará a los usuarios un UPN nuevo igual que lo hizo en el escenario "Sin agregar".|
| Verified | En este caso, tenemos un dominio personalizado de "contoso.com" que ya está agregado y comprobado en Azure AD para el sufijo UPN. Los usuarios podrán utilizar su nombre principal de usuario local, por ejemplo, user@contoso.com,, para iniciar sesión en Azure una vez que se hayan sincronizado con Azure AD.|

###### <a name="ad-fs-federation"></a>Federación de AD FS
En Azure AD no se puede crear una federación con el dominio predeterminado ".onmicrosoft.com", ni un dominio personalizado no comprobado. Cuando se ejecuta el Asistente para Azure AD Connect, si selecciona un dominio no comprobado para crear una federación, Azure AD Connect le pedirá que cree los registros necesarios en donde esté hospedado el DNS para el dominio. Para más información, visite [esta página](active-directory-aadconnect-get-started-custom.md#verify-the-azure-ad-domain-selected-for-federation).

Si seleccionó la opción de inicio de sesión de usuario como "Federación con AD FS", tiene que tener un dominio personalizado para continuar con la creación de una federación en Azure AD. Para el caso que estamos tratando, esto significa que deberíamos tener un dominio personalizado "contoso.com" agregado en el directorio de Azure AD.

| Estado         | Efecto en la experiencia de usuario sobre el inicio de sesión de Azure |
|:-------------:|:----------------------------------------|
| Not added (Sin agregar) | En este caso Azure AD Connect no pudo encontrar un dominio personalizado coincidente para el sufijo UPN "contoso.com" en el directorio de Azure AD. Tiene que agregar un dominio personalizado "contoso.com" si los usuarios necesitan iniciar sesión usando AD FS con su UPN local como user@contoso.com.|
| Not verified (Sin comprobar) | En este caso, Azure AD Connect le indicará los detalles sobre cómo puede comprobar el dominio en una etapa posterior|
| Verified | En este caso puede proseguir con la configuración sin realizar ninguna acción|  

## <a name="changing-user-sign-in-method"></a>Cambio del método de inicio de sesión de usuario

Puede cambiar el método de inicio de sesión de usuario de federación a sincronización de contraseñas, utilizando las tareas que estarán disponibles en Azure AD Connect después de la configuración inicial de Azure AD Connect realizada con el Asistente. Vuelva a ejecutar al Asistente para Azure AD Connect y verá una lista de tareas que puede realizar. Seleccione **Cambiar inicio de sesión de usuario** de la lista de tareas. 

![Cambio de inicio de sesión de usuario](./media/active-directory-aadconnect-user-signin/changeusersignin.png)

En la página siguiente, deberá proporcionar las credenciales de Azure AD.

![Conectarse a Azure](./media/active-directory-aadconnect-user-signin/changeusersignin2.png)

En la página **Inicio de sesión de usuario**, seleccione **Sincronización de contraseña**. Esto cambiará el directorio de federado a administrado.

![Conectarse a Azure](./media/active-directory-aadconnect-user-signin/changeusersignin3.png)

>[AZURE.NOTE] Si el cambio a sincronización de contraseña es solo temporal seleccione **No convertir las cuentas de usuario**. Si no selecciona esta opción se realizará la conversión de cada usuario a federado lo que puede llevar varias horas.
  
## <a name="next-steps"></a>Pasos siguientes
Obtenga más información sobre la [Integración de las identidades locales con Azure Active Directory](active-directory-aadconnect.md).

Más información sobre el tema [Azure AD Connect: Conceptos de diseño](active-directory-aadconnect-design-concepts.md)





<!--HONumber=Oct16_HO2-->


