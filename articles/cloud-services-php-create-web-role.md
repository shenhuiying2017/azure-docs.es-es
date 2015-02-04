<properties urlDisplayName="Create Web and Worker Roles" pageTitle="Creación de roles web y de trabajo" metaKeywords="" description="" metaCanonical="" services="" documentationCenter="PHP" title="How to create PHP web and worker roles" authors="tomfitz" solutions="" manager="wpickett" editor="mollybos" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="PHP" ms.topic="article" ms.date="10/28/2014" ms.author="tomfitz" />

#Creación de roles de trabajo y web de PHP

En esta guía se explica cómo crear roles de trabajo o web de PHP en un entorno de desarrollo de Windows, elegir una versión específica de PHP de versiones "integradas" disponibles", cambiar la configuración de PHP, habilitar extensiones y, por último, implementar en Azure. También se describe cómo configurar un rol web o de trabajo para usar un tiempo de ejecución de PHP (con las extensiones y la configuración personalizada) proporcionado por el usuario.

##<a name="TableOfContents"></a>Tabla de contenido
* [¿Qué son los roles web y de trabajo de PHP?](#WhatIs)
* [Descarga del SDK de Azure para PHP](#DownloadSdk)
* [Creación de un proyecto de servicio en la nube](#CreateProject)
* [Incorporación de roles web y de trabajo de PHP](#AddRole)
* [Especificación de la versión de PHP integrada](#SpecifyPHPVersion)
* [Personalización del tiempo de ejecución de PHP integrado](#CustomizePHP)
* [Uso del tiempo de ejecución de PHP propio](#OwnPHP)
* [Ejecución de la aplicación en los emuladores de proceso y almacenamiento](#Emulators)
* [Publicación de la aplicación](#Publish) 

##<a name="WhatIs"></a>¿Qué son los roles web y de trabajo de PHP?
Azure proporciona tres modelos informáticos para la ejecución de aplicaciones: [Sitios web Azure][execution model-web sites], [Máquinas virtuales de Azure][execution model-vms] y [Servicios en la nube de Azure][execution model-cloud services]. Los tres modelos admiten PHP. Servicios en la nube, que incluye roles web y de trabajo, ofrece el modelo *Plataforma como servicio (PaaS)*. En un servicio en la nube, un rol web ofrece un servidor web dedicado de Internet Information Services (IIS) para hospedar aplicaciones web front-end, mientras que un rol de trabajo puede ejecutar tareas asincrónicas, de ejecución prolongada o perpetuas independientes de la entrada o la interacción del usuario.

Para obtener más información, consulte [¿Qué es un servicio en la nube?].

##<a name="DownloadSdk"></a>Descarga del SDK de Azure para PHP

El [SDK de Azure para PHP] tiene varios componentes. En este artículo se usarán dos de ellos: Azure PowerShell y los emuladores de Azure. Estos dos componentes se pueden instalar a través del instalador de plataforma web de Microsoft aquí: [Instalación de Azure PowerShell y de los emuladores de Azure][install ps and emulators].

##<a name="CreateProject"></a>Configuración de un proyecto de servicio en la nube

El primer paso para crear un rol web o de trabajo de PHP es crear un proyecto del servicio de Azure. Un proyecto del servicio de Azure sirve como un contenedor lógico para roles web y de trabajo y contiene los archivos del proyecto de [definición de servicio (.csdef)] y [configuración del servicio (.cscfg)]. 

Para crear un proyecto nuevo del servicio de Azure, ejecute el comando siguiente:

	PS C:\>New-AzureServiceProject myProject

Este comando creará un directorio nuevo (`myProject`) al que puede agregar roles web y de trabajo.

##<a name="AddRole"></a>Configuración de roles web y de trabajo de PHP

Para agregar un rol web de PHP a un proyecto, ejecute el siguiente comando desde el directorio raíz del proyecto:

	PS C:\myProject> Add-AzurePHPWebRole roleName

Para un rol de trabajo, use este comando:

	PS C:\myProject> Add-AzurePHPWorkerRole roleName

<div class="dev-callout"> 
<b>Nota:</b> 
<p>El <code>parámetro RoleName</code> es opcional. Si se omite, el nombre de rol se generará automáticamente. El primer rol web creado será <code>WebRole1</code>, el segundo <code>WebRole2</code>y así sucesivamente. El primer rol de trabajo creado será <code>WorkerRole1</code>, el segundo <code>WorkerRole2</code>y así sucesivamente.</p> 
</div>

##<a name="SpecifyPHPVersion"></a>Configuración de la versión de PHP integrada

Al agregar un rol web o de trabajo de PHP a un proyecto, los archivos de configuración del proyecto se modifican para que PHP se instale en cada instancia de trabajo o web de la aplicación cuando se implementa. Para ver la versión de PHP que se instalará de forma predeterminada, ejecute el comando siguiente:

	PS C:\myProject> Get-AzureServiceProjectRoleRuntime

La salida del comando anterior tendrá un aspecto similar al que se muestra a continuación. En este ejemplo, la etiqueta `IsDefault` se define como `true` para PHP 5.3.17, indicando que será la versión de PHP instalada de forma predeterminada. 

	Runtime Version		PackageUri						IsDefault
	------- ------- 	----------  					---------
   	Node 0.6.17      	http://nodertncu.blob.core...   False
   	Node 0.6.20         http://nodertncu.blob.core...   True
   	Node 0.8.4          http://nodertncu.blob.core...   False
	IISNode 0.1.21      http://nodertncu.blob.core...   True
  	Cache 1.8.0         http://nodertncu.blob.core...   True
    PHP 5.3.17          http://nodertncu.blob.core...   True
    PHP 5.4.0           http://nodertncu.blob.core...   False

Puede definir la versión del tiempo de ejecución de PHP con cualquier versión de PHP de la lista. Por ejemplo, para definir la versión de PHP (para un rol con nombre `roleName`) como 5.4.0, use el comando siguiente:

	PS C:\myProject> Set-AzureServiceProjectRole roleName php 5.4.0

<div class="dev-callout"> 
<b>Nota:</b> 
<p>En el futuro puede haber más versiones de PHP disponibles, y las versiones disponibles pueden cambiar.</p> 
</div>

##<a name="CustomizePHP"></a>Configuración del tiempo de ejecución de PHP integrado

Tiene el control total de la configuración del tiempo de ejecución de PHP instalado al seguir los pasos anteriores, incluida la modificación de la configuración de `php.ini` y la habilitación de las extensiones.

Para personalizar el tiempo de ejecución de PHP integrado, siga estos pasos:

1. Agregue una carpeta nueva, con nombre `php`, al directorio `bin` del rol web. Si se trata de un rol de trabajo, agréguelo al directorio raíz del rol.
2. En la carpeta `php`, cree otra carpeta denominada `ext`. Coloque cualquier archivo de extensión `.dll` (por ejemplo, `php_mongo.dll`) que desee habilitar en esta carpeta.
3. Agregue un archivo `php.ini` a la carpeta `php`. Habilite todas las extensiones personalizadas y defina todas las directivas de PHP en este archivo. Por ejemplo, si desea activar `display_errors` y habilitar la extensión `php_mongo.dll`, el contenido del archivo `php.ini` sería el siguiente:

		display_errors=On
		extension=php_mongo.dll

<div class="dev-callout"> 
<b>Nota:</b> 
<p>La configuración que no esté explícitamente establecida en el archivo <code>php.ini</code> proporcionado se establecerá automáticamente en sus valores predeterminados. Sin embargo, tenga en cuenta que puede agregar un archivo completo <code>php.ini</code> . </p> 
</div>

##<a name="OwnPHP"></a>Configuración del tiempo de ejecución de PHP propio
En algunos casos, en lugar de seleccionar un tiempo de ejecución de PHP integrado y configurarlo como se ha descrito anteriormente, puede proporcionar el tiempo de ejecución de PHP propio. Por ejemplo, puede usar el mismo tiempo de ejecución de PHP en un rol web o de trabajo que use en el entorno de desarrollo, permitiendo así de manera más fácil que garantice que la aplicación no cambiará su comportamiento en el entorno de producción.

<h3><a name="OwnPHPWebRole"></a>Configuración de un rol web para usar un tiempo de ejecución de PHP propio</h3>

Para configurar un rol web para usar un tiempo de ejecución de PHP proporcionado por el usuario, siga los pasos siguientes.

1. Cree un proyecto del servicio de Azure y agregue un rol web de PHP según se describe en las secciones anteriores [Creación de un proyecto de servicio en la nube](#CreateProject) e [Incorporación de roles web y de trabajo de PHP](#AddRole) .
2. Cree una carpeta `php` en la carpeta `bin` que se encuentra en el directorio raíz del rol web y, a continuación, agregue el tiempo de ejecución de PHP (todos los binarios, archivos de configuración, subcarpetas, etc.) a la carpeta `php`.
3. (OPCIONAL) Si el tiempo de ejecución de PHP usa los [Controladores de Microsoft para PHP en SQL Server][sqlsrv drivers] (en inglés), tendrá que configurar el rol web para instalar [SQL Server Native Client 2012][sql native client] cuando se aprovisione. Para ello, agregue el instalador `sqlncli.msi` a la carpeta `bin` en el directorio raíz del rol web. Puede descargar el instalador aquí: [instalador sqlncli.msi x64]. El script de inicio descrito en el siguiente paso ejecutará el instalador silenciosamente cuando se aprovisione el rol. Si el tiempo de ejecución de PHP no usa los controladores de Microsoft para PHP en SQL Server, puede eliminar la línea siguiente del script que se muestra en el paso siguiente:

		msiexec /i sqlncli.msi /qn IACCEPTSQLNCLILICENSETERMS=YES

4. El siguiente paso consiste en definir una tarea de inicio que configura [Internet Information Services (IIS)][iis.net] para que use el tiempo de ejecución de PHP a fin de que controle las solicitudes para las páginas .php. Para ello, abra el archivo `setup_web.cmd` (en el archivo `bin` del directorio raíz del rol web) en un editor de texto y reemplace su contenido con el script siguiente:

		@ECHO ON
		cd "%~dp0"
		
		if "%EMULATED%"=="true" exit /b 0
		
		msiexec /i sqlncli.msi /qn IACCEPTSQLNCLILICENSETERMS=YES
		
		SET PHP_FULL_PATH=%~dp0php\php-cgi.exe
		SET NEW_PATH=%PATH%;%RoleRoot%\base\x86
		
		%WINDIR%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='%PHP_FULL_PATH%',maxInstances='12',idleTimeout='60000',activityTimeout='3600',requestTimeout='60000',instanceMaxRequests='10000',protocol='NamedPipe',flushNamedPipe='False']" /commit:apphost
		%WINDIR%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='%PHP_FULL_PATH%'].environmentVariables.[name='PATH',value='%NEW_PATH%']" /commit:apphost
		%WINDIR%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='%PHP_FULL_PATH%'].environmentVariables.[name='PHP_FCGI_MAX_REQUESTS',value='10000']" /commit:apphost
		%WINDIR%\system32\inetsrv\appcmd.exe set config -section:system.webServer/handlers /+"[name='PHP',path='*.php',verb='GET,HEAD,POST',modules='FastCgiModule',scriptProcessor='%PHP_FULL_PATH%',resourceType='Either',requireAccess='Script']" /commit:apphost
		%WINDIR%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /"[fullPath='%PHP_FULL_PATH%'].queueLength:50000"

5. Agregue los archivos de la aplicación al directorio raíz del rol web. Será el directorio raíz del servidor web.

6. Publique la aplicación según lo descrito en la sección [Publicación de la aplicación](#Publish) siguiente.

<div class="dev-callout"> 
<b>Nota:</b> 
<p>El script <code>download.ps1</code> (en la carpeta <code>bin</code> del directorio raíz del rol web) se puede eliminar después de completar los pasos descritos anteriormente para usar el tiempo de ejecución de PHP propio.</p> 
</div>

<h3><a name="OwnPHPWorkerRole"></a>Configuración de un rol de trabajo para usar un tiempo de ejecución de PHP propio</h3>

Para configurar un rol de trabajo para usar un tiempo de ejecución de PHP propio, siga los pasos siguientes.

1. Cree un proyecto del servicio de Azure y agregue un rol de trabajo de PHP según se describe en las secciones [Creación de un proyecto de servicio en la nube](#CreateProject) e [Incorporación de roles web y de trabajo de PHP](#AddRole) anteriores.
2. Cree una carpeta `php` en el directorio raíz del rol de trabajo y, a continuación, agregue el tiempo de ejecución de PHP (todos los binarios, archivos de configuración, subcarpetas, etc.) a la carpeta `php`.
3. (OPCIONAL) Si el tiempo de ejecución de PHP usa los [Controladores de Microsoft para PHP en SQL Server][sqlsrv drivers] (en inglés), tendrá que configurar el rol de trabajo para instalar [SQL Server Native Client 2012][sql native client] cuando se aprovisione. Para ello, agregue el instalador `sqlncli.msi` al directorio raíz del rol de trabajo. Puede descargar el instalador aquí: [instalador sqlncli.msi x64]. El script de inicio descrito en el siguiente paso ejecutará el instalador silenciosamente cuando se aprovisione el rol. Si el tiempo de ejecución de PHP no usa los controladores de Microsoft para PHP en SQL Server, puede eliminar la línea siguiente del script que se muestra en el paso siguiente:

		msiexec /i sqlncli.msi /qn IACCEPTSQLNCLILICENSETERMS=YES

4. El paso siguiente consiste en definir una tarea de inicio que agrega el archivo ejecutable `php.exe` a la variable de entorno PATH del rol de trabajo cuando se aprovisiona el rol. Para ello, abra el archivo `setup_worker.cmd` (en el directorio raíz del rol de trabajo) en un editor de texto y reemplace su contenido por el script siguiente:

		@echo on

		cd "%~dp0"

		echo Granting permissions for Network Service to the web root directory...
		icacls ..\ /grant "Network Service":(OI)(CI)W
		if %ERRORLEVEL% neq 0 goto error
		echo OK

		if "%EMULATED%"=="true" exit /b 0

		msiexec /i sqlncli.msi /qn IACCEPTSQLNCLILICENSETERMS=YES

		setx Path "%PATH%;%~dp0php" /M

		if %ERRORLEVEL% neq 0 goto error

		echo SUCCESS
		exit /b 0

		:error

		echo FAILED
		exit /b -1	

5. Agregue los archivos de la aplicación al directorio raíz del rol de trabajo.

6. Publique la aplicación según lo descrito en la sección [Publicación de la aplicación](#Publish) siguiente.

##<a name="Emulators"></a>Configuración de la aplicación en los emuladores de proceso y almacenamiento

Los emuladores de proceso y almacenamiento de Azure ofrecen un entorno local en el que puede probar la aplicación de Azure antes de implementarla en la nube. Existen algunas diferencias entre los emuladores y el entorno de Azure. Para comprender esto mejor, consulte [Diferencias entre el emulador de proceso y Azure](http://msdn.microsoft.com/en-us/library/windowsazure/gg432960.aspx) y [Diferencias entre el emulador de almacenamiento y los servicios de almacenamiento de Azure](http://msdn.microsoft.com/en-us/library/windowsazure/gg433135.aspx).

Tenga en cuenta que debe tener instalado PHP localmente para usar el emulador de proceso. El emulador de proceso usará la instalación de PHP local para ejecutar la aplicación.

Para ejecutar el proyecto en los emuladores, ejecute el comando siguiente desde el directorio raíz del proyecto:

	PS C:\MyProject> Start-AzureEmulator

Verá una salida similar a esta:

	Creating local package...
	Starting Emulator...
	Role is running at http://127.0.0.1:81
	Started

Puede ver la aplicación en ejecución en el emulador; para ello, abra un explorador web y vaya a la dirección local que aparece en la salida (`http://127.0.0.1:81` en la salida del ejemplo anterior).

Para detener los emuladores, ejecute este comando:

	PS C:\MyProject> Stop-AzureEmulator

##<a name="Publish"></a>Configuración Publicación de la aplicación

Para publicar la aplicación, primero tiene que importar la configuración de la publicación con el cmdlet **Import-PublishSettingsFile** y, a continuación, puede publicar la aplicación con el cmdlet **Publish-AzureServiceProject**. Puede encontrar información detallada acerca del uso de cada uno de estos cmdlets en [Importación de la configuración de publicación] e [Implementación de un servicio en la nube en Azure], respectivamente.

[execution model-web sites]: /es-es/develop/net/fundamentals/compute/#WebSites
[execution model-vms]: /es-es/develop/net/fundamentals/compute/#VMachine
[execution model-cloud services]: /es-es/develop/net/fundamentals/compute/#CloudServices
[SDK de Azure para PHP]: /es-es/develop/php/common-tasks/download-php-sdk/
[install ps and emulators]: http://go.microsoft.com/fwlink/?LinkId=253447&clcid=0x409
[¿Qué es un servicio en la nube?]: /es-es/manage/services/cloud-services/what-is-a-cloud-service/
[definición de servicio (.csdef)]: http://msdn.microsoft.com/en-us/library/windowsazure/ee758711.aspx
[configuración del servicio (.cscfg)]: http://msdn.microsoft.com/en-us/library/windowsazure/ee758710.aspx
[iis.net]: http://www.iis.net/
[sql native client]: http://msdn.microsoft.com/en-us/sqlserver/aa937733.aspx
[sqlsrv drivers]: http://php.net/sqlsrv
[instalador sqlncli.msi x64]: http://go.microsoft.com/fwlink/?LinkID=239648
[Importación de la configuración de publicación]: /es-es/develop/php/how-to-guides/powershell-cmdlets/#ImportPubSettings
[Implementación de un servicio en la nube en Azure]: /es-es/develop/php/how-to-guides/powershell-cmdlets/#Deploy

<!--HONumber=35.1-->
