<properties 
	pageTitle="Instalación personalizada de Azure AD Connect" 
	description="En este documento se explica las opciones de instalación personalizada de Azure AD Connect." 
	services="active-directory" 
	documentationCenter="" 
	authors="billmath" 
	manager="swadhwa" 
	editor="curtand"/>

<tags 
	ms.service="active-directory"  
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/28/2015" 
	ms.author="billmath"/>

# Instalación personalizada de Azure AD Connect


La documentación siguiente proporciona información acerca del uso de la opción de instalación personalizada para Azure AD Connect. Puede utilizar esta opción si tiene opciones de configuración adicionales o necesita características opcionales que no se tratan en la instalación rápida.

Para obtener información sobre una instalación rápida, consulte [Instalación rápida](active-directory-aadconnect-get-started/#express-installation-of-azure-ad-connect). Para obtener información sobre la actualización de DirSync a Azure AD Connect, consulte [Actualizar DirSync a Azure Active Directory Connect.](active-directory-aadconnect-dirsync-upgrade-get-started.md)



## Instalación de los componentes necesarios

Al instalar los servicios de sincronización, puede dejar desactivada la sección de configuración opcional y Azure AD Connect configurará todo automáticamente. Esto incluye la configuración de una instancia de SQL Server 2012 Express y la creación de los grupos adecuados y asignación de permisos a los mismos. Si desea cambiar los valores predeterminados, puede utilizar la tabla siguiente para conocer las opciones de configuración opcionales que están disponibles.

<center>![Required Components](./media/active-directory-aadconnect-get-started-custom/requiredcomponents.png) </center>

Configuración opcional | Descripción 
------------- | ------------- |
Nombre de SQL Server |Permite especificar el nombre de SQL Server y el nombre de la instancia. Elija esta opción si ya dispone de un servidor de base de datos que le gustaría utilizar.
Cuenta de servicio |De forma predeterminada, Azure AD Connect creará una cuenta de servicio local para los servicios de sincronización que se van a utilizar. El problema que surge es que la contraseña se genera automáticamente y es desconocida para la persona que instala Azure AD Connect. En la mayoría de los escenarios esto está bien, pero si desea realizar algunas configuraciones avanzadas como controlar las unidades organizativas que se sincronizan, deseará crear una cuenta y elegir su propia contraseña. Pero si usa un servidor SQL remoto, necesitará una cuenta de servicio en el dominio y conocer la contraseña. En esos casos, especifique la cuenta de servicio para usar. |
Permisos | De forma predeterminada, Azure AD Connect creará cuatro grupos locales en el servidor cuando se instalen los servicios de sincronización. Estos grupos son: grupo Administradores, grupo Operadores, grupo Examinar y grupo Restablecimiento de contraseña. Si desea especificar sus propios grupos puede hacerlo aquí.


## Inicio de sesión de usuario
Después de instalar los componentes necesarios, se le pedirá que especifique el método de inicio de sesión único que utilizarán los usuarios. En la tabla siguiente se proporciona una breve descripción de las opciones disponibles.

<center>![User Signin](./media/active-directory-aadconnect-get-started-custom/usersignin.png) </center>


Inicio de sesión único | Descripción 
------------- | ------------- |
Sincronización de contraseña |Los usuarios puedan iniciar sesión en los Servicios en la nube de Microsoft (como Office 365, Dynamics CRM y Windows InTune) con la misma contraseña que usan para iniciar sesión en la red local. La contraseña del usuario se sincroniza en Azure mediante un hash de contraseña y la autenticación se produce en la nube.
Federación con AD FS|Los usuarios puedan iniciar sesión en los Servicios en la nube de Microsoft (como Office 365, Dynamics CRM y Windows InTune) con la misma contraseña que usan para iniciar sesión en la red local. Los usuarios se redirigen a la instancia local de AD FS para iniciar sesión y la autenticación se realiza de forma local.
No configurar| No se instalará ni configurará ninguna característica. Elija esta opción si ya tiene un servidor de federación de terceros u otra solución existente ya instalada.



## Conectarse a Azure AD
En la pantalla Conectarse a Azure AD, especifique una cuenta de administrador global y una contraseña. Asegúrese de que esta cuenta no tenga habilitada Multi-Factor Authentication. Esto provocará un error de autenticación. 
Tenga en cuenta que esta cuenta solo se usa para crear una cuenta de servicio en Azure AD y no se utiliza cuando el asistente haya finalizado.

<center>![User Signin](./media/active-directory-aadconnect-get-started-custom/connectaad.png) </center>

### Conectar sus directorios
Para conectarse al Servicio de dominio de Active Directory, Azure AD Connect necesita las credenciales de una cuenta con permisos suficientes. Esta cuenta puede ser una cuenta de usuario normal porque solo tiene los permisos de lectura predeterminados. Sin embargo, según el escenario, puede que necesite permisos adicionales. Para obtener más información, consulte [Resumen de la cuenta de Azure AD Connect](active-directory-addconnect-account-summary)

<center>![User Signin](./media/active-directory-aadconnect-get-started-custom/connectdir.png) </center>

### Identificación de forma exclusiva de usuarios

La característica Correspondencia entre bosques permite definir cómo se representan los usuarios de los bosques de AD DS en Azure AD. Un usuario puede estar representado solo una vez en todos los bosques o tener una combinación de cuentas habilitadas y deshabilitadas.

<center>![User Signin](./media/active-directory-aadconnect-get-started-custom/unique.png) </center>

Configuración | Descripción 
------------- | ------------- |
Mis usuarios solo se representan una vez en todos los bosques| Todos los usuarios se crean como objetos individuales en Azure AD.<br> Los objetos no se combinan en el metaverso.
Atributo Mail | Esta opción une a los usuarios y contactos si el atributo Mail tiene el mismo valor en bosques diferentes. Se recomienda utilizar esta opción cuando se han creado los contactos mediante GALSync.
ObjectSID y msExchangeMasterAccountSID|Esta opción une a un usuario habilitado en un bosque de cuentas con un usuario deshabilitado en un bosque de recursos de Exchange. Esto también se conoce como buzón vinculado en Exchange.
sAMAccountName y MailNickName|Esta opción combina atributos donde se espera que pueda encontrarse el identificador de inicio de sesión para el usuario.
Mi propio atributo|Esta opción le permite seleccionar su propio atributo. **Limitación:** asegúrese de seleccionar un atributo que ya exista en el metaverso. Si elige un atributo personalizado, que el asistente no podrá completarse.

- **Delimitador de origen**: el atributo sourceAnchor es un atributo que es inmutable durante la duración de un objeto de usuario. Es la clave principal que vincula el usuario local con el usuario de Azure AD. Puesto que no se puede cambiar el atributo, debe pensar en un atributo que sea adecuado usar. Un buen candidato es objectGUID. Este atributo no cambiará a menos que la cuenta de usuario se mueva entre bosques o dominios. En un entorno de varios bosque donde se muevan cuentas entre bosques, debe utilizarse otro atributo, como un atributo con el identificador de empleado. Atributos que hay que evitar son aquellos que podrían cambiar si combina una persona o cambian las asignaciones. No se pueden utilizar los atributos con un signo @, por lo que no se puede utilizar el correo electrónico y userPrincipalName. El atributo también distingue mayúsculas de minúsculas por lo que si mueve un objeto entre bosques, asegúrese de conservar las mayúsculas y minúsculas. Para los atributos binarios el valor está codificado en base 64, pero para otros tipos de atributo permanecerá en su estado sin codificar. En escenarios de federación y en algunas interfaces de Azure AD, este atributo se conoce también como immutableID.

- **UserPrincipalName**: el atributo userPrincipalName es el atributo que los usuarios van a usar al iniciar sesión en Azure AD y Office 365. Los dominios utilizados, también conocidos como sufijo UPN, deben comprobarse en Azure AD antes de que se sincronicen los usuarios. Se recomienda encarecidamente mantener el atributo userPrincipalName predeterminado. Si este atributo no es enrutable y no se puede comprobar, se puede seleccionar otro atributo, por ejemplo, correo electrónico, como atributo que contiene el identificador de inicio de sesión. Advertencia: no es compatible el uso de un identificador alternativo con todas las cargas de trabajo de Office 365. Para obtener más información, consulte https://technet.microsoft.com/es-es/library/dn659436.aspx.





### Filtrado de sincronización basado en grupos
La característica de filtrado en grupos le permite ejecutar una pequeña prueba piloto en un pequeño subconjunto de objetos creado en Azure AD y Office 365. Para utilizar esta característica, cree un grupo en Active Directory y agregue los usuarios y grupos que se deben sincronizar con Azure AD como miembros directos. Posteriormente puede agregar y quitar usuarios a este grupo para mantener la lista de objetos que deban estar presentes en Azure AD. 
Para usar esta característica, en la ruta de acceso personalizada verá esta página:

<center>![Sync Filtering](./media/active-directory-aadconnect-get-started-custom/filter2.png) </center>

### Características opcionales

Esta pantalla le permite seleccionar las características opcionales para situaciones específicas. A continuación se ofrece una breve explicación de cada una de las características individuales.

<center>![Express Installation](./media/active-directory-aadconnect-get-started-custom/of.png)</center>


Características opcionales | Descripción
-------------------    | ------------- | 
Implementación híbrida de Exchange |La característica Implementación híbrida de Exchange permite la coexistencia de buzones de Exchange tanto a nivel local como en Azure mediante la sincronización de un conjunto específico de atributos de Azure AD en su directorio local.
Aplicación Azure AD y filtro de atributos|Al habilitar la aplicación de Azure AD y el filtro de atributos, el conjunto de atributos sincronizados se puede adaptar a un conjunto específico de una página posterior del asistente. Esto abre dos páginas de configuración adicionales en el asistente.  
Escritura diferida de contraseñas|Al habilitar la escritura diferida de contraseñas, los cambios de contraseña que se originan con Azure AD se volverán a escribir en su directorio local.
Reescritura de usuarios|Al habilitar la reescritura de usuarios, los usuarios creados en Azure AD se volverán a escribir en su directorio local. Esto abre una página de configuración adicional en el asistente.  
Sincronización de atributos de las extensiones de directorios|Al habilitar la sincronización de atributos de las extensiones de directorios, los atributos especificados se sincronizará con Azure AD. Esto abre una página de configuración adicional en el asistente.  

Para opciones de configuración adicionales, como cambiar la configuración predeterminada, usar el editor de reglas de sincronización y el aprovisionamiento declarativo, vea [Administración de Azure AD Connect](active-directory-aadconnect-whats-next.md).




Agregue el nombre del grupo que contiene los usuarios y grupos. Solo los miembros de este grupo se sincronizarán con Azure AD.

## Sincronización de atributos de las extensiones de directorios
Con las extensiones de directorios puede extender el esquema en Azure AD con atributos personalizados agregados por la organización o con otros atributos de Active Directory. Para utilizar esta característica, seleccione "Sincronización de atributos de las extensiones de directorios" en la página "Características opcionales". Accederá a esta página donde podrá seleccionar los atributos adicionales.

<center>![Sync Filtering](./media/active-directory-aadconnect-get-started-custom/extension2.png) </center>

Solo se admiten los atributos de un solo valor y el valor no puede ser mayor de 250 caracteres. Con los atributos seleccionados, se ampliará el metaverso y el esquema de AD de Azure. En Azure AD se agrega una nueva aplicación con los atributos.

<center>![Sync Filtering](./media/active-directory-aadconnect-get-started-custom/extension3.png) </center>

Estos atributos ahora estarán disponibles en Graph:

<center>![Sync Filtering](./media/active-directory-aadconnect-get-started-custom/extension4.png) </center>

## Reescritura de usuarios (vista previa)
Reescritura de usuarios permite tomar un usuario creado en Azure AD (a través del portal, gráfico, PowerShell o cualquier otro método) y reescribir al usuario en AD DS local. Para habilitar la característica, seleccione «Reescritura de usuarios» en la página de características opcionales. Ahora se le mostrará la ubicación donde desea que se creen estos usuarios. La configuración predeterminada creará todos los usuarios en una ubicación en AD DS.

<center>![Sync Filtering](./media/active-directory-aadconnect-get-started-custom/writeback2.png) </center>
Los usuarios se crearán con una contraseña aleatoria, de tal forma que haya que restablecer la contraseña en AD DS para que el usuario pueda realmente iniciar sesión.

>[AZURE.NOTE]La sincronización de contraseñas y la reescritura de contraseñas no son compatibles con esta característica de vista previa.

## Reescritura de grupos (vista previa)
La opción para la reescritura de grupos en las características opcionales le permitirá reescribir "Grupos de Office 365" en un bosque con Exchange instalado. Se trata de un nuevo tipo de grupo que siempre se controla en la nube. Puede encontrarlo en outlook.office365.com o en myapps.microsoft.com, tal como se muestra aquí:


<center>![Sync Filtering](./media/active-directory-aadconnect-get-started-custom/office365.png) </center>


<center>![Sync Filtering](./media/active-directory-aadconnect-get-started-custom/myapps.png) </center>

Este grupo se representará como un grupo de distribución en AD DS local. El servidor de Exchange local debe tener la actualización acumulativa 8 de Exchange 2013 (publicado en marzo de 2015) para reconocer este nuevo tipo de grupo.

**Nota:**

- Actualmente no se rellena el atributo de libreta de direcciones. La manera más fácil consiste en buscar la propiedad de libreta de direcciones de otro grupo de su organización y rellenarla fuera el motor de sincronización. **La manera más fácil de hacerlo es usar el cmdlet de PowerShell update-recipient.**
- Solo los bosques con el esquema de Exchange son destinos válidos para los grupos. Si no se ha detectado ningún Exchange, no se podrá habilitar la reescritura de grupos.
- La característica Reescritura de grupos no controla actualmente los grupos de seguridad ni los grupos de distribución.

Puede encontrar más información [aquí](http://blogs.office.com/2014/09/25/delivering-first-chapter-groups-office-365/).

## Reescritura de dispositivos (vista previa)
La característica de reescritura de dispositivos le permitirá tomar un dispositivo registrado en la nube, por ejemplo en Intune, y tenerlo en AD DS para un acceso condicional. Para habilitar la característica, debe prepararse AD DS. Si instala AD FS y el servicio de registro de dispositivos (DRS), DRS proporciona cmdlets de PowerShell para preparar a AD para la reescritura de dispositivos. Si no tienes DRS instalado, puede ejecutar C:\Archivos de programa\Microsoft Azure Active Directory Connect\AdPrep\AdSyncAdPrep.psm1 como administrador empresarial.



## Modo provisional
Con el modo provisional es posible el proceso de instalación de un nuevo servidor de sincronización en paralelo con un servidor existente. Solo se admite para tener un servidor de sincronización conectado a un directorio en la nube. Pero si deseamos mover desde otro servidor, por ejemplo, un servidor que esté ejecutando la sincronización de directorios, podemos habilitar Azure AD Connect en modo provisional. Cuando se habilita el motor de sincronización, se importarán y sincronizarán los datos de la forma habitual, pero no se exportará nada a Azure AD y se desactivará la sincronización de contraseñas y la reescritura de contraseñas.

<center>![Sync Filtering](./media/active-directory-aadconnect-get-started-custom/stagingmode.png) </center>

En modo provisional, es posible realizar los cambios necesarios en el motor de sincronización y revisar lo que se va a exportar. Cuando la configuración esté correcta, vuelva a ejecutar al Asistente para la instalación y deshabilite el modo provisional. Esto permitirá que los datos se exporten a Azure AD. Asegúrese de deshabilitar al otro servidor al mismo tiempo para que solo un servidor esté exportando activamente.

### Evitar eliminaciones accidentales
Al instalar Azure AD Connect, se habilita de forma predeterminada la característica Evitar eliminaciones accidentales y se configura para que no permita ninguna exportación con más de 500 eliminaciones. Este valor de 500 es predeterminado y puede modificarse. Con esta característica habilitada, si hay demasiadas eliminaciones, la exportación no continuará y recibirá un mensaje similar al siguiente:

<center>![Sync Filtering](./media/active-directory-aadconnect-get-started-custom/email.png) </center>

Si no es lo esperado, investigue y tome las medidas correctivas oportunas.

Para deshabilitar temporalmente esta protección y permitir realizar estas eliminaciones, ejecute: 
Disable-ADSyncExportDeletionThreshold.

Para volver a habilitar la protección o para cambiar el valor de umbral predeterminado, ejecute: 
Enable-ADSyncExportDeletionThreshold.


## Configuración de Federación con AD FS
La configuración de AD FS con Azure AD Connect es muy sencilla y solo se necesitan unos cuantos clics. Es necesario lo siguiente antes de la instalación.

- Un servidor Windows Server 2012 R2 para el servidor de federación con la administración remota habilitada.
- Un servidor Windows Server 2012 R2 para el servidor Proxy de aplicación web con la administración remota habilitada.
- Un certificado SSL para el nombre del servicio de federación que desea usar (por ejemplo, adfs.contoso.com)

### Creación de una nueva granja de AD FS o utilización de una granja de AD FS existente
Puede utilizar una granja de AD FS existente o puede crear una nueva granja de AD FS. Si decide crear una nueva, deberá proporcionar el certificado SSL. Si el certificado SSL está protegido mediante contraseña, se le pedirá que proporcione la contraseña.

<center>![AD FS Farm](./media/active-directory-aadconnect-get-started-custom/adfs1.png) </center>
**Nota:** si decide utilizar una granja de servidores existente de AD FS, omitirá algunas páginas y se le dirigirá directamente a la configuración de la relación de confianza entre la pantalla de AD FS y Azure AD.

### Especificación de los servidores de AD FS

Aquí deberá especificar los servidores específicos que desea instalar en AD FS. Puede agregar uno o más servidores según su necesidades de planeación de capacidad. Estos servidores deben estar unidos a un dominio de Active Directory antes de realizar esta configuración. Se recomienda instalar a un único servidor de AD FS para las implementaciones piloto y de prueba e implementar servidores adicionales mediante la apertura de Azure AD Connect y la implementación de AD FS en servidores adicionales para satisfacer las necesidades de escalabilidad.


> [AZURE.NOTE]Asegúrese de que todos los servidores están unidos a un dominio de AD antes de realizar esta configuración.

<center>![AD FS Servers](./media/active-directory-aadconnect-get-started-custom/adfs2.png) </center>
### Especificación de los servidores Proxy de aplicación web
Aquí deberá especificar los servidores específicos que desee como servidores Proxy de aplicación web. El servidor Proxy de aplicación web se implementa en la red perimetral (a través de extranet) y admite solicitudes de autenticación desde la extranet. Puede agregar uno o más servidores según su necesidades de planeación de capacidad. Se recomienda instalar a un único servidor Proxy de aplicación web para las implementaciones piloto y de prueba e implementar servidores adicionales mediante la apertura de Azure AD Connect e implementar el servidor Proxy de aplicación web en servidores adicionales. Normalmente, se recomienda tener un número equivalente de servidores proxy para cumplir con la autenticación de la intranet.

> [AZURE.NOTE]<li>Si la cuenta que está usando para instalar Azure AD Connect no es una cuenta de administración local en los servidores de AD FS, se le pedirán las credenciales de una cuenta con permisos suficientes.</li><li>Asegúrese de que hay conectividad HTTP/HTTPS entre el servidor de Azure AD Connect y el servidor Proxy de aplicación web antes de configurar este paso.</li><li> Además, asegúrese de que hay conectividad HTTP/HTTPS entre el Servidor de aplicaciones web y el servidor de AD FS para permitir que fluyan las solicitudes de autenticación.</li>


<center>![Web App](./media/active-directory-aadconnect-get-started-custom/adfs3.png) </center>

Se le pedirá que escriba las credenciales para que el Servidor de aplicaciones web pueda establecer una conexión segura con el servidor de AD FS. Estas credenciales deben ser un administrador local en el servidor de AD FS.

<center>![Proxy](./media/active-directory-aadconnect-get-started-custom/adfs4.png) </center>
### Especificación de la cuenta de servicio para el servicio AD FS
El servicio de AD FS requiere una cuenta de servicio de dominio para autenticar usuarios y la información de usuario de búsqueda en Active Directory. Puede admitir dos tipos de cuentas de servicio:

- **Cuentas de servicio administradas de grupo**: se trata de un tipo de cuenta de servicio que se introdujo en Servicio de dominio de Active Directory con Windows Server 2012. Este tipo de cuenta proporciona servicios como AD FS para utilizar una única cuenta sin necesidad de actualizar la contraseña de la cuenta de forma periódica. Utilice esta opción si ya tiene controladores de dominio de Windows Server 2012 en el dominio al que pertenecerán los servidores de AD FS.
- **Cuenta de usuario de dominio**: en este tipo de cuenta deberá proporcionar una contraseña y actualizarla regularmente cuando cambia la contraseña. Utilice esta opción solo si no tiene controladores de dominio de Windows Server 2012 en el dominio al que pertenecerán los servidores de AD FS.

Azure AD Connect creará automáticamente la cuenta de servicio administrada de grupo si ha iniciado sesión como administrador de dominio.
 
<center>![AD FS Service Account](./media/active-directory-aadconnect-get-started-custom/adfs5.png) </center>

### Selección del dominio de Azure AD que desee federar
Esta configuración se utiliza para configurar la relación de federación entre AD FS y Azure AD. Configura a AD FS para que emitir tokens de seguridad en Azure AD y configura Azure AD para confiar en los tokens de esta instancia de AD FS específica. Esta página solo permitirá configurar un único dominio la primera vez. Puede configurar dominios adicionales en cualquier momento si abre de nuevo Azure AD Connect y realiza esta tarea.

 
<center>![Azure AD Domain](./media/active-directory-aadconnect-get-started-custom/adfs6.png) </center>
### Realización de tareas adicionales para completar la configuración de federación
Las siguientes tareas adicionales deben estar completadas para finalizar la configuración de federación.

- Configure registros DNS para el nombre de servicio de federación de AD FS (por ejemplo, adfs.contoso.com) para la intranet (servidor DNS interno) y la extranet (DNS público a través de su registrador de dominios). Para el registro DNS de la intranet, asegúrese de que usa registros A y no registros CNAME. Esto es necesario para que funcione correctamente la autenticación de Windows desde la máquina unida al dominio.
- Si va a implementar más de un servidor de AD FS o servidor Proxy de aplicación web, asegúrese de que ha configurado el equilibrador de carga y que los registros DNS para el nombre de servicio de federación de AD FS (por ejemplo, adfs.contoso.com) indican el equilibrador de carga.
- Para que la autenticación integrada de Windows funcione para las aplicaciones del explorador que usan Internet Explorer en la intranet, asegúrese de que se agrega el nombre de servicio de federación de AD FS (por ejemplo, adfs.contoso.com) a la zona de intranet en Internet Explorer. Esto se puede controlar mediante la directiva de grupo e implementarlo en todos los equipos unidos a un dominio. 

### Comprobación de la configuración de la federación

Azure AD Connect comprobará la configuración de DNS al hacer clic en el botón Comprobar.

<center>![Complete](./media/active-directory-aadconnect-get-started-custom/adfs7.png) </center>
Además, realice los pasos de comprobación siguientes:

- Valide el inicio de sesión del explorador desde una máquina unida a un dominio de Internet Explorer desde la intranet: conéctese a https://myapps.microsoft.com y compruebe el inicio de sesión con su cuenta conectada.
- Valide el inicio de sesión del explorador desde cualquier dispositivo de la extranet: en un equipo doméstico o un dispositivo móvil, conéctese a https://myapps.microsoft.com y proporcione la credencial del identificador de inicio de sesión y contraseña.
- Valide el inicio de sesión de cliente enriquecido: conéctese a https://testconnectivity.microsoft.com, elija la pestaña 'Office 365' y elija 'Prueba de inicio de sesión único de Office 365'.

### Configuración opcional en el servicio de AD FS
Puede personalizar la imagen de logotipo y la ilustración para las páginas de inicio de sesión de AD FS si inicia sesión en AD FS y usa PSH para realizar esta configuración.
	
	Set-AdfsWebTheme -TargetName default -Logo @{path="c:\Contoso\logo.png"} –Illustration @{path=”c:\Contoso\illustration.png”}






**Recursos adicionales**

* [Uso de la infraestructura de identidad local en la nube](active-directory-aadconnect.md)
* [Cómo funciona Azure AD Connect](active-directory-aadconnect-how-it-works.md)
* [Qué sigue en Azure AD Connect](active-directory-aadconnect-whats-next.md)
* [Más información](active-directory-aadconnect-learn-more.md)
* [Azure AD Connect en MSDN](https://msdn.microsoft.com/library/azure/dn832695.aspx) 

<!---HONumber=62-->

<!--HONumber=62-->
<!--HONumber=62-->