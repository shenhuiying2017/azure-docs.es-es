<properties 
	pageTitle="Protección de un sitio web de Azure." 
	description="Obtenga información acerca de cómo proteger un sitio web de Azure." 
	services="web-sites" 
	documentationCenter="" 
	authors="blackmist" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="09/17/2014" 
	ms.author="larryfr"/>


#Protección de una aplicación web en un Sitio web Azure

Uno de los retos que plantea el desarrollo de una aplicación web es cómo proporcionar un servicio seguro y protegido para sus clientes. En este artículo conocerá las características de Sitios web Azure que pueden proteger su aplicación web.

> [AZURE.NOTE] No es el objetivo de este documento abarcar una completa exposición de las consideraciones de seguridad para las aplicaciones basadas en web. Como punto de inicio para conocer más en profundidad la cuestión de la protección de las aplicaciones web, consulte el [proyecto de código abierto sobre seguridad en aplicaciones web (OWASP)]( https://www.owasp.org/index.php/Main_Page), concretamente el [proyecto de los 10 principales](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project), que enumera los 10 principales errores de seguridad en aplicaciones web de acuerdo con los miembros de OWASP.

###Tabla de contenido

* [Protección de comunicaciones](#https)
* [Protección de desarrollo](#develop)
* [Pasos siguientes](#next)
 
##<a name="https"></a>Protección de comunicaciones

Si utiliza el nombre de dominio **.azurewebsites.net** que se crea para su sitio web, puede utilizar de inmediato HTTPS, ya que se proporciona un certificado SSL para todos los nombres de dominio del tipo **.azurewebsites.net**. Si el sitio utiliza un [nombre de dominio personalizado](http://azure.microsoft.com/es-es/documentation/articles/web-sites-custom-domain-name/), puede cargar un certificado SSL para habilitar HTTPS para el dominio personalizado.

##<a name="develop"></a>Protección de desarrollo 

###Perfiles de publicación y configuración de publicación

Al desarrollar aplicaciones, realizar tareas de mantenimiento o automatizar tareas mediante herramientas como **Visual Studio**, **Web Matrix**, **Azure PowerShell** o la **Interfaz de línea de comandos entre plataformas** de Azure, puede utilizar un archivo de *publish settings* o un *publishing profile*. Ambos le autentican en Azure, y deberían estar protegidos para evitar el acceso no autorizado.

* Un archivo de **configuración de publicación** contiene

	* El identificador de suscripción a Azure

	* Un certificado de administración que le permita realizar tareas de administración para la suscripción *without having to provide an account name or password*.

* Un archivo de **perfil de publicación** contiene

	* Información para publicación en su Sitio web Azure

Si utiliza una herramienta que emplea configuración de publicación o perfil de publicación, importe el archivo que contiene la configuración o el perfil de publicación en dicha herramienta y, a continuación, **elimine** el archivo. Si debe conservar el archivo, por ejemplo para compartirlo con otros que trabajan en el proyecto, almacénelo en una ubicación segura, como un directorio **cifrado** con permisos restringidos.

Además, debería asegurarse de que se protegen las credenciales importadas. Por ejemplo, tanto **Azure PowerShell** como la **Interfaz de línea de comandos entre plataformas de Azure** almacenan la información importada en su **directorio particular** (*~* en Linux o sistemas OS X y */usuarios/sunombredeusuario* en sistemas Windows). Para conseguir una seguridad adicional, **cifre** estas ubicaciones mediante herramientas de cifrado disponibles para su sistema operativo.

###Valores de configuración y cadenas de conexión
Es habitual almacenar cadenas de conexión, credenciales de autenticación y otra información de tipo confidencial en archivos de configuración. Lamentablemente, estos archivos pueden resultar expuestos en su sitio web o podrían aparecer en búsquedas de un repositorio público, con lo que la información podría quedar al descubierto.

Sitios web Azure le permite almacenar la información de configuración como parte del entorno de tiempo de ejecución de Sitios web en forma de **configuración de aplicación** y **cadenas de conexión**. Los valores se exponen a la aplicación en tiempo de ejecución a través de *environment variables* para la mayoría de lenguajes de programación. En el caso de las aplicaciones .NET, estos valores se insertan en la configuración de .NET en el tiempo de ejecución.

**Configuración de aplicación** y **cadenas de conexión** se pueden configurar mediante el Portal de administración de Azure o herramientas como PowerShell o la Interfaz de la línea de comandos entre plataformas de Azure.

Para obtener más información sobre configuración de aplicación y cadenas de conexión, consulte [Configuración de sitios web](/es-es/documentation/articles/web-sites-configure/).

###FTPS

Azure proporciona acceso FTP seguro al sistema de archivos para el sitio web a través de **FTPS**. Esto le permite tener acceso seguro al código de la aplicación en el Sitio web, así como a los registros de diagnóstico. El vínculo FTPS para el sitio web se puede encontrar en el **Panel** del sitio, en el [Portal de administración de Azure](https://manage.windowsazure.com).

Para obtener más información acerca de FTPS, consulte el [protocolo de transferencia de archivos](http://en.wikipedia.org/wiki/File_Transfer_Protocol).

##Pasos siguientes

Para obtener más información sobre la seguridad en la plataforma de Azure, aprender cómo notificar un **abuso o incidente de seguridad** o informar a Microsoft de que va a realizar una **prueba de penetración** en su sitio, consulte la sección de seguridad del [Centro de confianza de Microsoft Azure](/es-es/support/trust-center/security/).

Para obtener más información sobre los archivos **web.config** o **applicationhost.config** en Sitios web Azure, consulte [Opciones de configuración desbloqueadas en Sitios web Azure](http://azure.microsoft.com/blog/2014/01/28/more-to-explore-configuration-options-unlocked-in-windows-azure-web-sites/).

Para obtener información sobre información de registro para Sitios web Azure, que puede resultar útil en la detección de ataques, consulte [Habilitación del registro de diagnóstico](/es-es/documentation/articles/web-sites-enable-diagnostic-log/).




<!--HONumber=42-->
