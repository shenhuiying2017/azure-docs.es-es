<properties 
	pageTitle="Introducción a Azure AD Connect" 
	description="Obtenga información acerca de cómo descargar, instalar y ejecutar el asistente para instalación de Azure AD Connect." 
	services="active-directory" 
	documentationCenter="" 
	authors="billmath" 
	manager="terrylan" 
	editor="lisatoft"/>

<tags 
	ms.service="azure-active-directory-connect" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/02/2015" 
	ms.author="billmath"/>

# Introducción a Azure AD Connect


<div class="dev-center-tutorial-selector sublanding">
<a href="/es-es/documentation/articles/active-directory-aadconnect/" title="¿Qué es?" class="current">¿Qué es?</a> <a href="/es-es/documentation/articles/active-directory-aadconnect-how-it-works/" title="¿Cómo funciona?">¿Cómo funciona?</a> <a href="/es-es/documentation/articles/active-directory-aadconnect-get-started/" title="Introducción">Introducción</a> <a href="/es-es/documentation/articles/active-directory-aadconnect-whats-next/" title="¿Qué sigue?">¿Qué sigue?</a> <a href="/es-es/documentation/articles/active-directory-aadconnect-learn-more/" title="Más información">Más información</a>
</div>

La siguiente documentación le ayudará a empezar a trabajar con Azure Active Directory Connect.

## Descarga de Azure AD Connect



Para empezar a utilizar Azure AD Connect puede descargar la versión más reciente mediante el siguiente vínculo: [Descarga de la vista previa pública de Azure AD Connect](http://connect.microsoft.com/site1164/program8612).

## Antes de instalar Azure AD Connect
Antes de instalar Azure AD Connect, hay algunas cosas que necesitará.

- Una suscripción de Azure o una [suscripción de prueba de Azure](http://azure.microsoft.com/pricing/free-trial/)
- Azure AD Premium o una [prueba de Azure AD Premium](http://aka.ms/aadptrial)
- Una cuenta de administrador global de Azure AD para el inquilino de Azure AD con el que desea realizar la integración
- Un controlador de dominio de AD o un servidor miembro con Windows Server 2008 o posterior
- Una cuenta de administrador de organización para su Active Directory local
- Opcional: una cuenta de usuario de prueba para comprobar la sincronización. 

Si piensa usar el SSO con la opción de AD FS también necesitará lo siguiente:

- Credenciales de administrador local en los servidores de federación previstos.
- Credenciales de administrador local en cualquier servidor de grupo de trabajo (no unido a dominio) en el que pretenda implementar el rol Proxy de aplicación web.
- Un servidor Windows Server 2012 R2 para el servidor de federación.
- Un servidor Windows Server 2012 R2 para el Proxy de aplicación web.
-  un archivo .pfx con un certificado SSL para el nombre de servicio de federación previsto, como fs.contoso.com.
- la máquina en la que se ejecuta al asistente debe poder conectarse a otras máquinas en las que va a instalar AD FS o el Proxy de aplicación web a través de Administración remota de Windows.


## Instalación de Azure AD Connect

Una vez que haya descargado Azure AD Connect, utilice lo siguiente para instalar Azure AD Connect.

1. Inicie sesión en el servidor que desea para instalar Azure AD Connect como administrador de organización.
2. Navegue hasta el archivo AzureADConnect.msi y haga doble clic en él.
3. Recorra el asistente usando la configuración rápida o personalizada.
4. Opcional: use su cuenta de usuario de prueba para iniciar sesión en un servicio en la nube, como Office 365, para probar.

<center>![Azure AD Connect](./media/active-directory-aadconnect-get-started/aadConnect_Welcome.png)</center>

## Configuraciones opcionales de Servicios de sincronización
Al instalar los servicios de sincronización, puede dejar desactivada la sección de configuración opcional y Azure AD Connect configurará todo automáticamente. Esto incluye la configuración de una instancia de SQL Server 2012 Express y la creación de los grupos adecuados y asignación de permisos a los mismos. Si desea cambiar los valores predeterminados, puede utilizar la tabla siguiente para conocer las opciones de configuración opcionales que están disponibles.

Configuración opcional | Descripción 
------------- | ------------- |
Nombre de SQL Server |Permite especificar el nombre de SQL Server y el nombre de la instancia. Elija esta opción si ya dispone de un servidor de base de datos que le gustaría utilizar.
Cuenta de servicio |De forma predeterminada, Azure AD Connect creará una cuenta de servicio para los servicios de sincronización que se van a utilizar. El problema que surge es que la contraseña se genera automáticamente y es desconocida para la persona que instala Azure AD Connect. En la mayoría de los escenarios esto está bien, pero si desea realizar algunas configuraciones avanzadas como controlar las unidades organizativas que se sincronizan, deseará crear una cuenta y elegir su propia contraseña. |
Permisos | De forma predeterminada, Azure AD Connect creará cuatro grupos cuando se instalen los servicios de sincronización. Estos grupos son: grupo Administradores, grupo Operadores, grupo Examinar y grupo Restablecimiento de contraseña. Si desea especificar sus propios grupos puede hacerlo aquí.
Importar configuración |Utilice esta opción si va a importar la información de configuración de DirSync de Azure AD Sync.|



## Instalación rápida
Configuración rápida es la opción predeterminada que se selecciona y es uno de los escenarios más comunes. Al hacer esto, Azure AD Connect implementa la sincronización con la opción de sincronización de hash de contraseña. Esto es para un solo bosque y permite que los usuarios utilicen su contraseña local para iniciar sesión en la nube. Mediante la instalación rápida se activa automáticamente una sincronización una vez completada la instalación. Con esta opción, ampliará su directorio local en la nube con tan solo seis clics.

<center>! [Instalación rápida] (. / media/active-directory-aadconnect-get-started/express.png)</center>

## Instalación personalizada

Con la instalación personalizada puede seleccionar varias opciones. La tabla siguiente describe las páginas del asistente que están disponibles al seleccionar la opción de instalación personalizada.

Nombre de la página | Descripción
-------------------    | ------------- | 
Inicio de sesión de usuario|En esta página puede elegir si desea utilizar Sincronización de contraseña, Federación con AD FS o ninguna.
Conectarse a sus directorios|En esta página puede agregar uno o varios directorios con los que desea sincronizar.
 Filtrado de sincronización | Aquí puede determinar si desea sincronizar todos los usuarios y grupos, o si desea especificar un grupo por cada directorio y solo sincronizarlas.
Identidades locales|Aquí puede especificar que los usuarios o bien existen solamente una vez en todos los directorios que se agregaron en Connect a la página de directorios o que existen en varios directorios. Si los usuarios existen en varios directorios, debe elegir un atributo que identifica de forma única estos usuarios en los directorios. Por ejemplo, el atributo de correo, ObjectSID o SAMAccountName son todos los atributos comunes utilizados para identificar de forma única a los usuarios.
Identidades de Azure|En esta página se especifica el delimitador de origen que desea utilizar para la federación de identidades.
Características opcionales|Utilice la tabla siguiente para obtener una descripción breve de las características opcionales que puede seleccionar.

<center>![Instalación rápida](./media/active-directory-aadconnect-get-started/of.png)</center>


Características opcionales | Descripción
-------------------    | ------------- | 
Implementación híbrida de Exchange |La característica Implementación híbrida de Exchange permite la coexistencia de buzones de Exchange tanto a nivel local como en Azure mediante la sincronización de un conjunto específico de atributos de Azure AD en su directorio local.
Aplicación Azure AD y filtro de atributos|Al habilitar la aplicación de Azure AD y el filtro de atributos, el conjunto de atributos sincronizados se puede adaptar a un conjunto específico de una página posterior del asistente. Esto abre dos páginas de configuración adicionales en el asistente.  
Escritura diferida de contraseñas|Al habilitar la escritura diferida de contraseñas, los cambios de contraseña que se originan con Azure AD se volverán a escribir en su directorio local.
Reescritura de usuarios|Al habilitar la reescritura de usuarios, los usuarios creados en Azure AD se volverán a escribir en su directorio local. Esto abre una página de configuración adicional en el asistente.  
Sincronización de dispositivos|Al habilitar la sincronización de dispositivos, configuración del dispositivo puede escribirse en Azure AD.
Sincronización de atributos de las extensiones de directorios|Al habilitar la sincronización de atributos de las extensiones de directorios, los atributos especificados se sincronizará con Azure AD. Esto abre una página de configuración adicional en el asistente.  

Para opciones de configuración adicionales, como cambiar la configuración predeterminada, usar el editor de reglas de sincronización y el aprovisionamiento declarativo, vea [Administración de Azure AD Connect](active-directory-aadconnect-whats-next.md).

## Componentes de soporte de Azure AD Connect

La siguiente es una lista de requisitos previos y componentes de soporte que Azure AD Connect instalará en el servidor en el que configuró Azure AD Connect. Esta lista es para una instalación rápida básica. Si decide usar un servidor SQL Server diferente en la página Instalar servicios de sincronización, los componentes de SQL Server 2012 enumerados a continuación no se instalarán.

- Forefront Identity Manager Azure Active Directory Connector
- Utilidades de línea de comandos de Microsoft SQL Server 2012
- Microsoft SQL Server 2012 Native Client
- Microsoft SQL Server 2012 Express LocalDB
- Módulo de Active Directory para Windows PowerShell
- Microsoft Online Services - Ayudante para el inicio de sesión para profesionales de TI
- Paquete de redistribución de Microsoft Visual C++ 2013


**Recursos adicionales**

* [Uso de la infraestructura de identidad local en la nube](active-directory-aadconnect.md)
* [Cómo funciona Azure AD Connect](active-directory-aadconnect-how-it-works.md)
* [Qué sigue en Azure AD Connect](active-directory-aadconnect-whats-next.md)
* [Más información](active-directory-aadconnect-learn-more.md)
* [Azure AD Connect en MSDN](https://msdn.microsoft.com/library/azure/dn832695.aspx)

<!---HONumber=58-->