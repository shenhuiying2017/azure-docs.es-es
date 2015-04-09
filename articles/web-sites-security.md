<properties 
	pageTitle="Protección de una aplicación web en el Servicio de aplicaciones de Azure" 
	description="Aprenda a proteger una aplicación web de Azure." 
	services="app-service\web" 
	documentationCenter="" 
	authors="cephalin" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="03/24/2015" 
	ms.author="cephalin"/>


#Protección de una aplicación web en el Servicio de aplicaciones de Azure

Uno de los retos que plantea el desarrollo de una aplicación web es cómo proporcionar un servicio seguro y protegido para sus clientes. En este artículo conocerá las características del [Servicio de aplicaciones de Azure](http://go.microsoft.com/fwlink/?LinkId=529714) que pueden proteger su aplicación web.

> [AZURE.NOTE] No es el objetivo de este documento abarcar una completa exposición de las consideraciones de seguridad para las aplicaciones basadas en web. Como punto de inicio para conocer más en profundidad la cuestión de la protección de las aplicaciones web, consulte el [Proyecto de código abierto sobre seguridad en aplicaciones web (OWASP))]( https://www.owasp.org/index.php/Main_Page),, concretamente el [proyecto de los 10 principales](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project), que enumera los 10 principales errores de seguridad en aplicaciones web de acuerdo con los miembros de OWASP.

##<a name="https"></a> Protección de comunicaciones

Si utiliza el nombre de dominio ***.azurewebsites.net** que se crea para su aplicación web, puede usar de inmediato HTTPS, ya que se proporciona un certificado SSL para todos los nombres de dominio ***.azurewebsites.net**. Si el sitio utiliza un [nombre de dominio personalizado](web-sites-custom-domain-name.md), , puede cargar un certificado SSL para [habilitar HTTPS](web-sites-configure-ssl-certificate.md para el dominio personalizado.

##<a name="develop"></a> Protección de desarrollo 

### Perfiles de publicación y configuración de publicación

Al desarrollar aplicaciones, realizar tareas de administración o automatizar tareas mediante herramientas como **Visual Studio**, **Web Matrix**, **Azure PowerShell** o **Interfaz de línea de comandos entre plataformas** de Azure, puede utilizar un archivo de  *publish settings* o  *publishing profile*. Ambos le autentican en Azure, y deberían estar protegidos para evitar el acceso no autorizado.

* Un archivo de **configuración de publicación** contiene

	* El identificador de suscripción a Azure

	* Un certificado de administración que permite realizar tareas de administración para la suscripción *without having to provide an account name or password*.

* Un archivo de **perfil de publicación** contiene

	* Información para la publicación en su aplicación web

Si utiliza una herramienta que emplea configuración de publicación o perfil de publicación, importe el archivo que contiene la configuración o el perfil de publicación en dicha herramienta y, a continuación, **elimine** el archivo. Si debe conservar el archivo, por ejemplo para compartirlo con otros que trabajan en el proyecto, almacénelo en una ubicación segura, como un directorio **cifrado** con permisos restringidos.

Además, debería asegurarse de que se protegen las credenciales importadas. Por ejemplo, tanto **Azure PowerShell** como la **Interfaz de línea de comandos entre plataformas de Azure** almacenan la información importada en su **directorio particular** (*~* en Linux o sistemas OS X y */usuarios/sunombredeusuario* en sistemas Windows.) Para conseguir una seguridad adicional, **cifre** estas ubicaciones mediante herramientas de cifrado disponibles para su sistema operativo.

### Valores de configuración y cadenas de conexión
Es habitual almacenar cadenas de conexión, credenciales de autenticación y otra información de tipo confidencial en archivos de configuración. Lamentablemente, estos archivos pueden resultar expuestos en su sitio web o podrían aparecer en búsquedas de un repositorio público, con lo que la información podría quedar al descubierto.

El Servicio de aplicaciones de Azure le permite almacenar la información de configuración como parte del entorno de tiempo de ejecución de aplicaciones web en forma de **configuración de aplicación** y **cadenas de conexión**. Estos valores se exponen a su aplicación en tiempo de ejecución a través de  *environment variables* para la mayoría de lenguajes de programación. En el caso de las aplicaciones .NET, estos valores se insertan en la configuración de .NET en el tiempo de ejecución.

**Configuración de aplicación** y **cadenas de conexión** se pueden configurar mediante el [Portal de Azure](http://go.microsoft.com/fwlink/?LinkId=529715) o utilidades como PowerShell o la Interfaz de línea de comandos entre plataformas de Azure.

Para obtener más información sobre configuración de aplicación y cadenas de conexión, consulte [Configuración de aplicaciones web](web-sites-configure.md)..

### FTPS

Azure proporciona acceso FTP seguro al sistema de archivos para la aplicación web a través de **FTPS**. Esto le permite tener acceso seguro al código de la aplicación en la aplicación web, así como a los registros de diagnóstico. El vínculo FTPS para la aplicación web se puede encontrar en la página **Panel** del [Portal de administración de Azure](https://manage.windowsazure.com).

Para obtener más información acerca de FTPS, consulte [File Transfer Protocol](http://en.wikipedia.org/wiki/File_Transfer_Protocol).

>[AZURE.NOTE] Si desea empezar a trabajar con el Servicio de aplicaciones de Azure antes de registrarse para abrir una cuenta de Azure, vaya a [Prueba del Servicio de aplicaciones](http://go.microsoft.com/fwlink/?LinkId=523751), donde podrá crear inmediatamente una aplicación web de inicio de corta duración en el Servicio de aplicaciones. No es necesario proporcionar ninguna tarjeta de crédito ni asumir ningún compromiso.

## Pasos siguientes

Para obtener más información sobre la seguridad en la plataforma de Azure, aprender cómo notificar un **abuso o incidente de seguridad** o informar a Microsoft de que va a realizar una **prueba de penetración** en su sitio, consulte la sección de seguridad del [Centro de confianza de Microsoft Azure](http://azure.microsoft.com/support/trust-center/security/).

Para obtener más información sobre los archivos **web.config** o **applicationhost.config** en aplicaciones web, consulte [Opciones de configuración desbloqueadas en aplicaciones web del Servicio de aplicaciones de Azure](http://azure.microsoft.com/blog/2014/01/28/more-to-explore-configuration-options-unlocked-in-windows-azure-web-sites/).

Para obtener información sobre información de registro para aplicaciones web, que puede resultar útil en la detección de ataques, consulte [Habilitación del registro de diagnóstico](web-sites-enable-diagnostic-log.md).

## ¿Qué ha cambiado?
* Para obtener una guía del cambio de Sitios web a Servicio de aplicaciones, consulte: [Servicio de aplicaciones de Azure y su impacto en los servicios de Azure existentes](http://go.microsoft.com/fwlink/?LinkId=529714)

* Para obtener una guía del cambio del portal antiguo al nuevo, consulte: [Referencia para navegar por el portal de vista previa](http://go.microsoft.com/fwlink/?LinkId=529715)

<!--HONumber=49-->