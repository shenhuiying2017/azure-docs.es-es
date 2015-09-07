<properties
	pageTitle="Configuración del acceso condicional local mediante el Registro de dispositivos de Azure Active Directory | Microsoft Azure"
	description="Guía paso a paso para habilitar el acceso condicional a aplicaciones locales mediante Servicios de federación de Active Directory (AD FS) en Windows Server 2012 R2."
	services="active-directory"
	documentationCenter=""
	authors="femila"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/19/2015"
	ms.author="femila"/>

# Configuración del acceso condicional local mediante el registro de dispositivos de Azure Active Directory

Los dispositivos de propiedad personal de los usuarios pueden marcarse como conocidos para la organización exigiendo a los usuarios la unión de sus dispositivos al área de trabajo para el servicio Registro de dispositivos de Azure Active Directory. A continuación, se ofrece una guía paso a paso para habilitar el acceso condicional a aplicaciones locales mediante Servicios de federación de Active Directory (AD FS) en Windows Server 2012 R2.

> [AZURE.NOTE]Se requiere licencia de Azure AD Premium o de Office 365 al usar dispositivos registrados en directivas de acceso condicional del servicio Registro de dispositivos de Azure Active Directory. Esto incluye las directivas que exigen los Servicios de federación de Active Directory (AD FS) con recursos locales.

Para obtener más información sobre los escenarios de acceso condicional en local, vea [Unión al área de trabajo desde cualquier dispositivo para SSO y segundo factor de autenticación sin interrupciones a través de aplicaciones de la compañía](https://technet.microsoft.com/library/dn280945.aspx). Estas capacidades están disponibles para los clientes que compren una licencia de Azure Active Directory Premium.

Dispositivos compatibles
-------------------------------------------------------------------------
* Dispositivos Windows 7 unidos a un dominio.
* Dispositivos Windows 8.1 personales y unidos a un dominio.
* iOS 6 y versiones posteriores.
* Teléfonos Android 4.0 o versiones posteriores, Samsung GS3 o modelos posteriores, tabletas Samsung Note2 o versiones posteriores.

Requisitos previos de escenario
------------------------------------------------------------------------
* Windows Server Active Directory (Windows Server 2008 o versiones posteriores)
* Esquema actualizado en Windows Server 2012 R2
* Suscripción a Azure Active Directory Premium
* Servicios de federación con Windows Server 2012 R2, configurados para SSO en Azure AD
* Proxy de aplicación web con Windows Server 2012 R2
* Herramienta de sincronización de directorios (DirSync) con reescritura de objetos de dispositivo. [Descargue la versión más reciente de dirsync.exe.](http://go.microsoft.com/fwlink/?LinkID=278924)

Problemas conocidos en esta versión
-------------------------------------------------------------------------------
* Las directivas de acceso condicional basado en dispositivos requieren reescritura de objetos de dispositivo en Active Directory de Azure Active Directory. Puede tardarse hasta tres horas para que los objetos de dispositivo se reescriban en Active Directory.
* Los dispositivos iOS7 siempre pedirán al usuario que seleccione un certificado durante la autenticación de certificados de cliente. 
* Los dispositivos iOS8 beta no funcionan en este momento.


## Implementación del servicio Registro de dispositivos de Azure Active Directory
Use esta guía para implementar y configurar el servicio Registro de dispositivos de Azure Active Directory para su organización.

En esta guía se asume que ya configuró Windows Server Active Directory y se suscribió a Microsoft Azure Active Directory. Vea los requisitos previos anteriores.

Para implementar el servicio Registro de dispositivos de Azure Active Directory con su inquilino de Azure Active Directory, complete por orden las tareas de la lista de comprobación siguiente. Cuando un vínculo de referencia le lleve a un tema conceptual, vuelva a esta lista de comprobación después de revisar el tema conceptual para continuar con las demás tareas de la lista. Algunas tareas incluirán un paso de validación del escenario que puede ayudarle a confirmar que el paso se completó correctamente.

**Lista de comprobación: Habilitación del Registro de dispositivos de Azure Active Directory**

Siga la lista de comprobación mostrada a continuación para habilitar y configurar el servicio Registro de dispositivos de Azure Active Directory.

| Tarea | Referencia |
|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------|
| Habilite el Registro de dispositivos en el inquilino de Azure Active Directory para permitir que los dispositivos se unan al área de trabajo. De forma predeterminada, Multi-Factor Authentication no está habilitada para el servicio. Aunque se recomienda usar Multi-Factor Authentication al registrar un dispositivo. Antes de habilitar Multi-Factor Authentication en ADRS, asegúrese de que AD FS está configurado para un proveedor de Multi-Factor Authentication. | [Habilitación del Registro de dispositivos de Azure Active Directory](active-directory-conditional-access-device-registration-overview.md) |
| Los dispositivos detectarán el servicio Registro de dispositivos de Azure Active Directory buscando registros DNS conocidos. Debe configurar el DNS de su compañía para que los dispositivos puedan detectar el servicio Registro de dispositivos de Azure Active Directory. | [Configuración de la detección del Registro de dispositivos de Azure Active Directory](active-directory-conditional-access-device-registration-overview.md) |

**Lista de comprobación: Implementación y configuración de Servicios de federación de Windows Server 2012 R2 Active Directory y configuración de una relación de federación con Azure Active Directory** Con el siguiente conjunto de tareas, integre los Servicios de federación de Windows Server 2012 R2 con Azure Active Directory. Esto permite usar dispositivos registrados en el servicio Registro de dispositivos de Azure Active Directory con directivas de acceso condicional en AD FS.


| Tarea | Referencia |
|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------|
| Implemente el dominio de Servicios de dominio de Active Directory con las extensiones de esquema de Windows Server 2012 R2. (No es necesario actualizar ninguno de los controladores de dominio a Windows Server 2012 R2. La actualización del esquema es el único requisito). | Actualice el esquema de Servicios de dominio de Active Directory. |
| Implemente Servicios de federación de Windows Server 2012 R2 junto con el Proxy de aplicación web. | Implemente Servicios de federación de Windows Server 2012 R2 junto con el Proxy de aplicación web. |
| El bosque de Servicios de dominio de Active Directory debe configurarse con los objetos y contenedores necesarios para admitir objetos de dispositivo. También habilitará la autenticación de dispositivos en AD FS. | Prepare el bosque de Active Directory para que admita los dispositivos. |
| Configure una relación de federación con su organización y Azure Active Directory. Este paso le guiará a través de la configuración de su inquilino de Azure Active Directory con los Servicios de federación de Windows Server 2012 R2. | Configuración de una relación de federación con Azure Active Directory y configuración de la herramienta de sincronización de directorios |
| Configure la sincronización de directorios (DirSync) para que permita la reescritura de objetos dispositivo. Los dispositivos creados en Azure Active Directory se escribirán en Active Directory. | Configuración de DirSync para permitir la reescritura de objetos de dispositivo |
| Se recomienda que configure un proveedor de Multi-Factor Authentication con Servicios de federación de Windows Server 2012 R2. Esto le permitirá que los usuarios registren sus dispositivos de forma segura con Multi-Factor Authentication. | Configuración de Servicios de federación para proporcionar Multi-Factor Authentication |


### Actualización del esquema de Servicios de dominio de Active Directory
> [AZURE.NOTE]La actualización del esquema de Active Directory no se puede revertir. Se recomienda realizar la operación en un entorno de prueba.

1. Inicie sesión en el controlador de dominio con una cuenta que tenga derechos de administrador de organización y de administrador de esquema.
2. Copie el directorio [media]\\support\\adprep y los subdirectorios en uno de los controladores de dominio de Active Directory. 
3. Donde [media] es la ruta de acceso a los medios de instalación de Windows Server 2012 R2.
4. En un símbolo del sistema, navegue hasta el directorio adprep y ejecute: adprep.exe /forestprep. Siga las instrucciones en pantalla para completar la actualización del esquema.

### Implementación de Servicios de federación de Windows Server 2012 R2 junto con el Proxy de aplicación web
Siga las dos guías que se ofrecen a continuación para implementar Servicios de federación de Windows Server 2012 R2 con el Proxy de aplicación web y vuelva a esta guía. Siga las instrucciones para implementar una granja de servidores de federación de Windows Server 2012 R2.

> [AZURE.NOTE]Al implementar AD FS, debe omitir el paso opcional indicado en Paso opcional: Configuración de un servidor de federación con el servicio Registro de dispositivos (DRS). Este paso no es necesario cuando se usa el Registro de dispositivos de Azure Active Directory. Siga las instrucciones para implementar un proxy de servidor de federación de Windows Server 2012 R2.

Instale la actualización más reciente de Windows Server 2012 R2 en todos los servidores proxy y de federación de Windows Server 2012 R2. Esto puede hacerse antes o después de configurar la granja de servidores de federación. La actualización se puede encontrar aquí: Actualización de Windows Server 2012 R2.


### Preparación del bosque de Active Directory para que admita los dispositivos

> [AZURE.NOTE]Se trata de una operación que se realiza una sola vez que debe ejecutar para preparar el bosque de Active Directory para admitir los dispositivos. Para completar este procedimiento, debió iniciar sesión con permisos de administrador de organización y el bosque de Active Directory debe tener el esquema de Windows Server 2012 R2.

1. En el servidor de federación, abra una ventana de comandos de Windows PowerShell y escriba: *Initialize-ADDeviceRegistration*

2. Cuando le pidan el ServiceAccountName, escriba el nombre de la cuenta de servicio que seleccionó como cuenta de servicio de AD FS. Si es una cuenta de gMSA, escriba la cuenta con el formato dominio\\nombreDeCuenta$. Para una cuenta de dominio, use el formato dominio\\nombreDeCuenta.


### Habilitación de la autenticación del dispositivo en AD FS

1. En el servidor de federación, abra la consola de administración de AD FS y navegue hasta **AD FS** > **Directivas de autenticación**.
2. Seleccione **Editar autenticación principal global...** en el panel **Acciones**.
3. Active **Habilitar autenticación de dispositivos** y seleccione **Aceptar**.
4. De forma predeterminada, AD FS quitará periódicamente los dispositivos no usados de Active Directory. Debe deshabilitar esta tarea cuando use el Registro de dispositivos de Azure Active Directory para que los dispositivos se pueden administrar en Azure.


### Deshabilitación de la limpieza de dispositivos no usados
1. En el servidor de federación, abra una ventana de comandos de Windows PowerShell y escriba: `*Set-AdfsDeviceRegistration -MaximumInactiveDays 0*`


###Configuración de una relación de federación con Azure Active Directory y configuración de la herramienta de sincronización de directorios
La siguiente sección le ayudará a configurar una confianza de federación entre Azure Active Directory y AD FS. Es posible que algunos pasos le hagan salir de esta página. Siga las instrucciones que se dan a continuación y luego vuelva a esta guía.

Integración de Azure Active Directory con Active Directory local y configuración de inicio de sesión único
------------------------------------------------------------------------------------------------------

1. Inicie sesión en el Portal de Azure como **Administrador**.
2. En el panel izquierdo, seleccione **Active Directory**.
3. En la pestaña **Directorio**, seleccione su directorio.
4. Seleccione la pestaña **Integración de directorios**.
5. En la sección de integración con Active Directory local, busque la opción Sincronización de directorios y seleccione Activada.
6. Siga los pasos del 1 al 4 para integrar Azure Active Directory con su directorio local.
  1. **Adición de dominios**
  2. **Configuración de inicio de sesión único y preparación de la sincronización de directorios**. Si ya implementó AD FS puede seguir estas subsecciones para configurar y validar una confianza entre AD FS y Azure AD. 
      *  [Instalación de Windows PowerShell para inicio de sesión único con AD FS](https://msdn.microsoft.com/library/azure/jj151814.aspx).
      *  [Establecimiento de una confianza entre AD FS y Azure AD](https://msdn.microsoft.com/library/azure/jj205461.aspx).
      *  [Comprobación y administración de inicio de sesión único con AD FS](https://msdn.microsoft.com/library/azure/jj151809.aspx).
  	  *  [Otras referencias de AD FS](https://msdn.microsoft.com/library/azure/dn151321.aspx)

  3. Instalación y ejecución de la herramienta de sincronización de directorios. Si ya instaló DirSync, debe actualizarla a la versión más reciente. La versión mínima necesaria es 6862.0000.
  4. Comprobación y administración de la sincronización de directorios 
  5. Configure algunas reglas de notificación adicionales en el objeto de confianza para usuario autenticado en Azure Active Directory. Este objeto de confianza para usuario autenticado normalmente se denomina Plataforma de identidad de Microsoft Office 365.

###Configuración de reglas de notificaciones de confianza para usuario autenticado de Azure Active Directory

1. Abra la consola de administración de AD FS y navegue hasta AD FS > Relaciones de confianza > **Relaciones de confianza para usuario autenticado**. Haga clic con el botón derecho en el **objeto de confianza para usuario autenticado Plataforma de identidad de Microsoft Office 365** y seleccione **Editar reglas de notificaciones…
2. En la pestaña **Reglas de transformación de emisión**, seleccione Agregar regla.**
3. Seleccione **Enviar notificaciones con una regla personalizada** en el cuadro desplegable de plantillas **Regla de notificaciones**. Seleccione **Siguiente**.
4. Escriba Regla de notificaciones de método de autenticación en el cuadro de texto **Nombre de regla de notificaciones:**.
5. Escriba la siguiente regla de notificaciones en el cuadro de texto Regla de notificaciones:

    ` c:[Type == "http://schemas.microsoft.com/claims/authnmethodsreferences"]` `=> issue(claim = c);`

6. Haga clic en **Aceptar** dos veces para completar el cuadro de diálogo.
7. Configure algunas referencias de clase de autenticación adicionales en el objeto de confianza para usuario autenticado en Azure Active Directory. Este objeto de confianza para usuario autenticado normalmente se denomina Plataforma de identidad de Microsoft Office 365.


###Configuración de la referencia de clase de autenticación de confianza para usuario autenticado de Azure Active Directory

En el servidor de federación, abra una ventana de comandos de Windows PowerShell y escriba:

*Set-AdfsRelyingPartyTrust -TargetName <RPObjectName> -AllowedAuthenticationClassReferences wiaormultiauthn*


Donde <RPObjectName> es el nombre del objeto para usuario de confianza para su objeto de confianza para usuario de confianza de Azure Active Directory. Este objeto normalmente se denomina Plataforma de identidad de Microsoft Office 365.

### Configuración de DirSync para permitir la reescritura de objetos de dispositivo
Configure la sincronización de directorios (DirSync) para que permita la reescritura de objetos de dispositivo. Los dispositivos creados en Azure Active Directory se escribirán en Active Directory. Los dispositivos creados en Azure Active Directory pueden tardar hasta tres en reescribirse en Active Directory.
>[Azure.Note]Para completar este procedimiento, debió iniciar sesión con permisos de administrador de organización. Descargue aquí la versión más reciente de dirsync.exe.

> Se requiere licencia de Azure AD Premium o de Office 365 para permitir la reescritura de dispositivos en Active Directory.

> Si acaba de completar al asistente para la instalación de DirSync, cierre la sesión y luego inicie sesión para continuar. Con esto se garantiza que dispone de un token de acceso actualizado.

>Puede tardarse hasta tres horas para que los objetos de dispositivo se reescriban en Servicios de dominio de Active Directory.

1. En el servidor de sincronización de directorios, abra una ventana de comandos de Windows PowerShell y emita los siguientes comandos:

    `Import-Module DirSync`
    
    `$AADCreds = Get-Credential`
    
    `$ADCreds = Get-Credential`

2. Cuando le pidan las credenciales, escriba las credenciales de su cuenta de administrador de servicios en la nube y sus credenciales de administrador de Active Directory.

*Enable-MSOnlineObjectManagement –ObjectTypes Device –TargetCredential $AADCreds -Credential $ADCreds*

### Configuración de los servicios de federación para proporcionar autenticación multifactor
Los dispositivos registrados con su organización pueden usarse como segundo factor de autenticación sin interrupciones. Por lo tanto, se recomienda que se requiera autenticación segura al registrar un dispositivo. Puede seguir la guía que se ofrece a continuación para configurar AD FS con Azure Multi-Factor Authentication y luego volver a esta guía. Siga las instrucciones para implementar Azure Multi-Factor Authentication.

## Unión de dispositivos al área de trabajo mediante el Registro de dispositivos de Azure Active Directory

### Unión de un dispositivo iOS al área de trabajo mediante el Registro de dispositivos de Azure Active Directory

El Registro de dispositivos de Azure Active Directory usa el proceso de inscripción de perfil móvil para dispositivos iOS. El proceso comienza con la conexión del usuario a la dirección URL de inscripción del perfil mediante el explorador web Safari. El formato de la dirección URL es el siguiente:

    https://enterpriseregistration.windows.net/enrollmentserver/otaprofile/"yourdomainname"

Donde <yourdomainname> es el nombre de dominio que se configuró con Azure Active Directory. Por ejemplo, si el nombre de dominio es contoso.com, la dirección URL sería:

    https://enterpriseregistration.windows.net/enrollmentserver/otaprofile/contoso.com

Existen varias formas de comunicar la URL a los usuarios. Una forma que se recomienda consiste en publicar esta dirección URL en un mensaje personalizado de acceso denegado a la aplicación en AD FS. Este tema se trata en la siguiente sección: Creación de una directiva de acceso a aplicaciones y mensaje personalizado de acceso denegado.

###Unión de un dispositivo Windows 8.1 al área de trabajo mediante el Registro de dispositivos de Azure Active Directory

1. En el dispositivo Windows 8.1, navegue hasta **Configuración de PC** > **Red** > **Área de trabajo**
2. Escriba su nombre de usuario en formato UPN. Por ejemplo: dan@contoso.com..
3. Seleccione **Unir**.
4. Cuando se lo pidan, inicie sesión con sus credenciales. El dispositivo ahora está unido.

### Unión de un dispositivo Android al área de trabajo mediante el Registro de dispositivos de Azure Active Directory

El tema Azure Authenticator para Android incluye instrucciones sobre cómo instalar la aplicación Azure Authenticator en el dispositivo Android y agregar una cuenta profesional. Cuando una cuenta profesional se crea correctamente en un dispositivo Android, el dispositivo queda unido al área de trabajo en la organización.

### Comprobación de que los dispositivos registrados se reescriben en Active Directory
Puede ver y comprobar que los objetos de dispositivo se reescribieron en Active Directory con LDP.exe o con el Editor ADSI. Ambos están disponibles con las herramientas del administrador de Active Directory.

De forma predeterminada, los objetos de dispositivo que se reescriben desde Azure Active Directory se colocan en el mismo dominio que la granja de AD FS.

`CN=RegisteredDevices,defaultNamingContext`

###Creación de una directiva de acceso a aplicaciones y un mensaje personalizado de acceso denegado
Considere el siguiente escenario: se crea una aplicación de confianza para usuario autenticado en AD FS y se configura una regla de autorización de emisión que solo permite dispositivos registrados. Ahora solo los dispositivos que están registrados pueden tener acceso a la aplicación. Para que a los usuarios les resulte más fácil obtener acceso a la aplicación, configure un mensaje personalizado de acceso denegado que incluya instrucciones sobre cómo deben unir el dispositivo. Ahora los usuarios disponen de una manera directa de registrar sus dispositivos para tener acceso a una aplicación.

Los pasos siguientes le indicarán cómo implementar este escenario:
>[AZURE.NOTE]En esta sección se supone que ya configuró una relación de confianza para usuario autenticado para la aplicación en AD FS.

1. Abra la herramienta MMC de AD FS y navegue hasta AD FS > Relaciones de confianza > Relaciones de confianza para usuario autenticado.
2. Busque la aplicación a la que se aplicará la nueva regla de acceso. Haga clic con el botón derecho en la aplicación y seleccione Editar reglas de notificación...
3. Seleccione la pestaña **Reglas de autorización de emisión** y luego **Agregar regla…**
4. En la lista desplegable de plantillas **Regla de notificaciones**, seleccione **Permitir o denegar a los usuarios según una notificación entrante**. Seleccione **Siguiente**.
5. En el campo Nombre de regla de notificaciones: especifique: **Permitir el acceso desde dispositivos registrados**.
6. En la lista desplegable Tipo de notificación entrante:, seleccione **Es usuario registrado**.
7. En el campo Valor: de Notificación entrante, especifique: **true**
9. Seleccione **Finalizar** y luego **Aplicar**.
10. Quite todas las reglas que son más permisivas que la regla que acaba de crear. Por ejemplo, quite la regla predeterminada Permitir acceso a todos los usuarios. 
8. Seleccione el botón de radio **Permitir acceso a usuarios con esta notificación entrante**.

Ahora, la aplicación está configurada para permitir el acceso solo cuando el usuario procede de un dispositivo registrado y unido al área de trabajo. Para ver directivas más avanzadas, consulte Administración de riesgos con control de acceso multifactor.

A continuación, configurará un mensaje de error personalizado para la aplicación. El mensaje de error notificará a los usuarios que han de unir el dispositivo al área de trabajo para tener acceso a la aplicación. Puede usar HTML personalizado y Windows PowerShell para crear un mensaje personalizado de acceso denegado a la aplicación.

En el servidor de federación, abra una ventana de comandos de Windows PowerShell y escriba el comando siguiente. Reemplazar partes del comando con elementos específicos de su sistema:

`Set-AdfsRelyingPartyWebContent -Name "relying party trust name" -ErrorPageAuthorizationErrorMessage ` Debe registrar el dispositivo para tener acceso a esta aplicación.

**Si está usa un dispositivo iOS, seleccione este vínculo para unir el dispositivo**:

    a href='https://enterpriseregistration.windows.net/enrollmentserver/otaprofile/yourdomain.com

Una el dispositivo iOS al área de trabajo.


**Si usa un dispositivo Windows 8.1**, puede unir el dispositivo yendo a **Configuración de PC ** > **Red** > **Área de trabajo**.


Donde "**nombre de confianza para usuario autenticado**" es el nombre del objeto de confianza para usuario autenticado en AD FS. Donde yourdomain.com es el nombre de dominio que se configuró con Azure Active Directory. Por ejemplo, contoso.com. Asegúrese de quitar los saltos de línea (si existen) del contenido HTML que se pasa al cmdlet S**et-AdfsRelyingPartyWebContent**.

<!---HONumber=August15_HO9-->