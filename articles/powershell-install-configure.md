<properties
	pageTitle="Cómo instalar y configurar Azure PowerShell"
	description="Obtenga información acerca de cómo instalar y configurar Azure PowerShell."
	editor="tysonn"
	manager="stevenka"
	documentationCenter=""
	services=""
	authors="coreyp69"/>

<tags
	ms.service="multiple"
	ms.workload="multiple"
	ms.tgt_pltfrm="powershell"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/20/2015"
	ms.author="coreyp"/>

# Cómo instalar y configurar Azure PowerShell#

<div class="dev-center-tutorial-selector sublanding"><a href="/manage/install-and-configure-windows-powershell/" title="PowerShell" class="current">PowerShell</a><a href="/manage/install-and-configure-cli/" title="Cross-Platform CLI">CLI multiplataforma</a></div>

Puede utilizar Windows PowerShell para realizar diversas tareas en Azure, tanto de manera interactiva en un símbolo del sistema como de manera automática mediante scripts. Azure PowerShell es un módulo que ofrece cmdlets para administrar Azure mediante Windows PowerShell. Puede utilizar los cmdlets para crear, probar, implementar y administrar soluciones y servicios ofrecidos a través de la plataforma de Azure. En la mayoría de los casos, puede utilizar los cmdlets para realizar las mismas tareas que se realizan a través del Portal de administración de Azure. Por ejemplo, puede crear y configurar servicios en la nube, máquinas virtuales, redes virtuales y aplicaciones web. 

El módulo se distribuye en forma de archivo descargable y el código fuente se administra a través de un repositorio disponible públicamente. En las instrucciones de instalación que aparecen más adelante en este tema, se proporciona un enlace a los archivos descargables. Para obtener información acerca del código fuente, consulte [Repositorio de código de Azure PowerShell](https://github.com/Azure/azure-powershell).

Esta guía ofrece información básica acerca de la instalación y la configuración de Azure PowerShell para administrar la plataforma de Azure.

### <a id="Prereq"></a>Requisitos previos para usar Azure PowerShell

Azure es una plataforma basada en suscripción. es decir, para poder usarla es necesaria una suscripción. En la mayoría de los casos, esto también implica que los cmdlets requieren información de suscripción para realizar las tareas a través de su suscripción (algunos cmdlets relacionados con el almacenamiento pueden utilizarse sin esta información). Para proporcionarla, tiene que configurar el equipo para que se conecte a su suscripción. Las instrucciones de cómo hacerlo se incluyen en este artículo, en la sección "Conexión a su suscripción".

> [AZURE.NOTE] A partir de la versión 0.8.5, los módulos de Azure PowerShell requieren Microsoft .NET Framework 4.5.

Al instalar el módulo, el instalador comprueba si el sistema contiene el software necesario e instala todas las dependencias, como la versión correcta de Windows PowerShell y .NET Framework.

<h2> <a id="Install"></a>Instalación de Azure PowerShell</h2>

Puede descargar e instalar los módulos de Azure PowerShell al ejecutar el [Instalador de plataforma web de Microsoft](http://go.microsoft.com/fwlink/p/?LinkId=320376). Cuando se le solicite, haga clic en **Ejecutar**. El instalador de plataforma web instala los módulos de Azure PowerShell y todas las dependencias. Siga las indicaciones para finalizar la instalación.

> [AZURE.NOTE] Si solo desea descargar el instalador de PowerShell, visite https://github.com/Azure/azure-powershell/releases.
En este repositorio también puede encontrar el código fuente para los cmdlets de PowerShell

Para obtener más información acerca de las herramientas de línea de comandos disponibles para Azure, consulte [Herramientas de línea de comando]( http://go.microsoft.com/fwlink/?LinkId=320796).

Al instalar el módulo también se instala una consola personalizada para Azure PowerShell. Puede ejecutar los cmdlets desde la consola estándar de Windows PowerShell o desde la consola de Azure PowerShell.

El método que debe seguir para abrir cualquiera de estas consolas depende de la versión de Windows que esté utilizando:

- Si el equipo dispone de Windows 8 o Windows Server 2012, o una versión posterior, puede utilizar la búsqueda integrada. En la pantalla de inicio, comience a escribir **power**. De este modo, se devuelve una lista de aplicaciones que incluyen Windows PowerShell y Azure PowerShell. Haga clic en cualquier aplicación para abrir la consola. (Para anclar la aplicación a la pantalla de inicio, haga clic con el botón derecho en el icono).

- Si el equipo dispone de una versión anterior a Windows 8 o Windows Server 2012, use el menú Inicio. En el menú Inicio, haga clic en **Todos los programas**, **Azure** y, a continuación, en **Azure PowerShell**.

<h2><a id="Connect"></a>Conexión a su suscripción</h2>

Para utilizar Azure es necesaria una suscripción. Si no tiene una suscripción, consulte [Introducción a Azure](http://go.microsoft.com/fwlink/p/?LinkId=320795).

Los cmdlets necesitan la suscripción para que puedan administrar los servicios. Hay dos maneras de proporcionar a Windows PowerShell la información de su suscripción. Puede usar un certificado de administración que contenga la información o puede iniciar sesión en Azure con su cuenta de Microsoft o con una cuenta profesional o educativa. Al iniciar sesión, Azure Active Directory (Azure AD) autentica las credenciales y devuelve un token de acceso que permite a Azure PowerShell administrar su cuenta.

Para elegir el método de autenticación más adecuado a sus necesidades, tenga en cuenta lo siguiente:

- Azure AD es el método de autenticación recomendado, ya que facilita la administración del acceso a una suscripción. Con la actualización en la versión 0.8.6, permite también un escenario de automatización con la autenticación de Azure AD si se usa una cuenta profesional o educativa. Además, funciona con la API de Administrador de recursos de Azure.
- Si utiliza el método del certificado, la información de suscripción está disponible mientras la suscripción y el certificado son válidos. Sin embargo, este método dificulta la administración del acceso a las suscripciones compartidas, es decir, aquellas en las que hay más de una persona autorizada para obtener acceso a la cuenta. Adicionalmente, la API de Administrador de recursos de Azure no acepta la autenticación mediante certificados.

Para obtener más información acerca de la autenticación y la administración de la suscripción en Azure, consulte [Administración de cuentas, suscripciones y roles administrativos](http://go.microsoft.com/fwlink/?LinkId=324796).

<h3>Uso del método de Azure AD</h3>

1. Abra la consola de Azure PowerShell siguiendo las instrucciones del apartado [Instalación de Azure PowerShell](#Install).

2. Escriba el siguiente comando:

		Add-AzureAccount

3. En la ventana, escriba la dirección de correo electrónico y la contraseña asociadas a su cuenta.

4. Azure autentica y guarda las credenciales y, a continuación, cierra la ventana.

5. A partir de la versión 0.8.6, si inicia sesión con una cuenta profesional o educativa, puede escribir el siguiente comando para omitir la ventana emergente. Aparecerá la ventana emergente estándar de credenciales de Windows PowerShell para que pueda escribir el nombre de usuario y la contraseña de su cuenta profesional o educativa.

        $cred = Get-Credential
        Add-AzureAccount -Credential $cred

    > Para obtener más información sobre la seguridad y el uso de credenciales, consulte [Procedimientos recomendados para implementar contraseñas y otra información confidencial en ASP.NET y en Sitios web Azure](http://www.asp.net/identity/overview/features-api/best-practices-for-deploying-passwords-and-other-sensitive-data-to-aspnet-and-azure).


	> [AZURE.NOTE] Este método de inicio de sesión no interactivo solo funciona con una cuenta profesional o educativa.  Una cuenta profesional o educativa es un usuario bajo la administración del trabajo o de la escuela y que está definido en la instancia de Azure Active Directory para el trabajo o la escuela. Si actualmente no tiene una cuenta profesional o educativa y usa una cuenta Microsoft para iniciar sesión en su suscripción de Azure, puede crear una fácilmente siguiente los pasos que se indican a continuación.
	>
	> 1. Inicie sesión en el [Portal de administración de Azure](https://manage.windowsazure.com) y haga clic en **Active Directory**.
	>
	> 2. Si no hay ningún directorio, seleccione **Create your directory** y proporcione la información que se le pida.
	>
	> 3. Seleccione su directorio y agregue un nuevo usuario. Este usuario nuevo puede iniciar sesión con una cuenta profesional o educativa.
	>
	>     Durante la creación del usuario, se le proporcionará una dirección de correo electrónico para el usuario y una contraseña temporal. Guarde esta información para usarla en otro paso.
	>
	> 4. En el portal de administración, seleccione **Settings** y, a continuación, **Administrators**. Seleccione **Agregar** y agregue el usuario nuevo como coadministrador. Esto permite que la cuenta profesional o educativa administre su suscripción de Azure.
	>
	> 5. Finalmente, cierre sesión en el portal de Azure y, a continuación, vuelva a iniciarla usando la cuenta profesional o educativa. Si es la primera vez que inicia sesión con esta cuenta, se le pedirá que cambie la contraseña.
	>
	>Para obtener más información acerca de iniciar sesión en Microsoft Azure con una cuenta profesional o educativa, consulte [Inicio de sesión como organización en Microsoft Azure](sign-up-organization.md).

<h3>Uso del método del certificado</h3>

El módulo de Azure incluye cmdlets que le ayudan a descargar e importar el certificado.

> [AZURE.NOTE] Los cmdlets en el módulo AzureResourceManager requieren el método (Add-AzureAccount). de Azure AD. Estos cmdlets no son compatibles con los archivos de configuración de publicación. Para obtener más información acerca de los cmdlets en el módulo AzureResourceManager, consulte [Cmdlets del Administrador de recursos de Azure](http://go.microsoft.com/fwlink/?LinkID=394765).


- El cmdlet **Get-AzurePublishSettingsFile** abre una página web en el
Portal de administración de Azure, desde donde puede descargar información sobre la suscripción. Esta información se presenta en forma de archivo .publishsettings.

- El cmdlet **Import-AzurePublishSettingsFile** importa el archivo .publishsettings para que lo utilice el módulo. Este archivo incluye un certificado de administración con credenciales de seguridad.

> [AZURE.IMPORTANT] Es recomendable que elimine el perfil de publicación que descargó utilizando el cmdlet <b>Get-AzurePublishSettingsFile</b> una vez que importe esta configuración. Debido a que el certificado de administración incluye credenciales de seguridad, no deberían poder obtener acceso a él usuarios no autorizados. Si necesita obtener información acerca de las suscripciones, puede obtenerla desde el [Portal de administración de Azure](http://manage.windowsazure.com/) o de [Microsoft Online Services - Portal del cliente](http://go.microsoft.com/fwlink/p/?LinkId=324875).

1. Inicie sesión en el [Portal de administración de Azure](http://manage.windowsazure.com) con las credenciales de su cuenta de Azure.

2. Abra la consola de Azure PowerShell siguiendo las instrucciones del apartado [Instalación de Azure PowerShell](#Install).

3. Escriba el siguiente comando:

		Get-AzurePublishSettingsFile

4. Cuando aparezca la opción, descargue y guarde el perfil de publicación y anote la ruta y el nombre del archivo .publishsettings. Esta información se solicita al ejecutar el cmdlet **Import-AzurePublishSettingsFile** para importar la configuración. La ubicación y el nombre predeterminados del archivo son:

			C:\\Users\<UserProfile>\\Download\\[*MySubscription*-...]-*downloadDate*-credentials.publishsettings

5. Escriba un comando similar al siguiente sustituyendo los marcadores de posición por el nombre de su cuenta de Windows y la ruta y nombre del archivo:

		Import-AzurePublishSettingsFile C:\Users\<UserProfile>\Downloads\<SubscriptionName>-credentials.publishsettings

> [AZURE.NOTE] Si se le agrega a otras suscripciones como coadministrador tras importar la configuración de publicación, deberá repetir este proceso para descargar un nuevo archivo .publishsettings y, a continuación, importar esa configuración. Si desea obtener información acerca de cómo agregar coadministradores para ayudar a administrar los servicios para una suscripción, consulte [Agregar y quitar coadministradores en las suscripciones de Azure](http://msdn.microsoft.com/library/windowsazure/gg456328.aspx).

<h3> Visualización de los detalles de las cuentas y las suscripciones</h3>
Puede tener varias cuentas y suscripciones disponibles para su uso por parte de Azure PowerShell. Puede agregar múltiples cuentas ejecutando Add-AzureAccount más de una vez.

Para obtener las cuentas de Azure disponibles, escriba lo siguiente:

	Get-AzureAccount

Para obtener sus suscripciones de Azure, escriba lo siguiente:

	Get-AzureSubscription

## <a id="Ex"></a>Uso de los cmdlets: un ejemplo ##

Después de instalar el módulo y configurar el equipo para que se conecte a su suscripción, puede crear una aplicación web de Azure. Este ejemplo le ayudará a comenzar a usar los cmdlets de Azure.

1. Inicie la consola de Azure PowerShell.

2. Elija un nombre para la aplicación web. Elija un nombre que se ajuste a las convenciones de nomenclatura de DNS. Los nombres válidos puede contener solo las letras de la 'a' a la 'z', los números entre '0' y '9', y un guión ('-'). 

	El nombre de la aplicación web debe ser único en Azure. En este ejemplo, usaremos "mySite". Sin embargo, asegúrese de elegir un nombre diferente, tal como el nombre de cuenta seguido de un número.  

	Una vez que elija un nombre, escriba un comando similar al siguiente. Sustituya el nombre de la aplicación web por "mySite".

		New-AzureWebsite mySite

	El cmdlet crea la aplicación web y devuelve un objeto que representa la nueva aplicación web. Las propiedades del objeto incluyen información útil acerca de la aplicación web.

3. Para obtener información sobre la aplicación web, escriba el comando siguiente. Devuelve un poco de información sobre todas las aplicaciones web de la suscripción, incluida la que acaba de crear.

		Get-AzureWebsite

4. Para obtener más información acerca de la aplicación web, incluya el nombre de esta en el comando. Asegúrese de sustituir el nombre de la aplicación web por "mySite".

		Get-AzureWebsite -Name mySite

5. Las aplicaciones web se inician después de crearse. Para detener la aplicación web, escriba este comando, incluido el nombre de la aplicación web.

		Stop-AzureWebsite -Name mySite

6. Para comprobar que el estado del sitio es 'stopped', vuelva a ejecutar el comando Get-AzureWebsite.

		Get-AzureWebsite

7. Para completar esta prueba, elimine la aplicación web. Escriba:  

		Remove-AzureWebsite -Name mySite

7. Para completar la tarea, confirme que la aplicación web se ha eliminado.

		Get-AzureWebsite -Name mySite

##<a id="Help"></a>Ayuda##

Estos recursos ofrecen ayuda para cmdlets concretos:


-   Desde la consola, puede utilizar el sistema de ayuda integrado. El cmdlet **Get-Help** proporciona acceso a este sistema. En la siguiente tabla se incluyen algunos ejemplos de comandos que se pueden utilizar para obtener ayuda. Puede obtener más información desde la consola escribiendo **help**.

    <table border="1" cellspacing="4" cellpadding="4">
    <tbody>
    <tr align="left" valign="top">
		<td><b>Comando</b></td>
		<td><b>Resultado</b></td>
    </tr>
    <tr align="left" valign="top">
		<td>Get-Help</td>
		<td>Describe cómo usar el sistema de ayuda. <p><b>Nota</b>: la descripción incluye información acerca de los archivos de ayuda que no se aplica al módulo de Azure. En concreto, los archivos de ayuda se instalan si el módulo está instalado. Estos archivos no pueden descargarse por separado.</p>
</td>
    </tr>
    <tr align="left" valign="top">
		<td>Get-Help Azure</td>
		<td>Permite obtener todos los cmdlets del módulo de Azure.</td>
    </tr>
	<tr align="left" valign="top">
		<td>Get-Help &lt;<b>lenguaje</b>&gt;-dev</td>
		<td>Permite obtener los cmdlets para el desarrollo y la administración de aplicaciones en un lenguaje específico. Por ejemplo, help node-dev, help php-dev o help python-dev.</td>
    </tr>
	    <tr align="left" valign="top">
		<td>Get-Help &lt;<b>cmdlet</b>&gt;</td>
		<td>Permite obtener ayuda sobre un cmdlet de Windows PowerShell. Reemplace <cmdlet> por el nombre del cmdlet.</td>
    </tr>
    <tr align="left" valign="top">
		<td>Get-Help &lt;<b>cmdlet</b>&gt; -Parameter *</td>
		<td>Permite obtener descripciones de los parámetros del cmdlet. El asterisco (*) significa "todo".</td>
    </tr>
    <tr align="left" valign="top">
		<td>Get-Help &lt;<b>cmdlet</b>&gt; -Examples</td>
		<td>Permite obtener la sintaxis y ejemplos de uso del cmdlet.</td>
    </tr>
    <tr align="left" valign="top">
		<td>Get-Help &lt;<b>cmdlet</b>&gt; -Full</td>
		<td>Permite obtener toda la ayuda de un cmdlet, incluidos los detalles técnicos.</td>
    </tr>
    </tbody>
    </table>



- En la biblioteca de Azure existe información de referencia acerca de los cmdlets de los módulos de Azure PowerShell. Para obtener información, consulte [Referencia de cmdlets de Azure](http://msdn.microsoft.com/library/windowsazure/jj554330.aspx).

Para obtener ayuda de la comunidad, utilice estos foros populares:

- [Foro de Azure en MSDN]( http://go.microsoft.com/fwlink/p/?LinkId=320212)
- [Stackoverflow](http://go.microsoft.com/fwlink/?LinkId=320213)


## <a id="Resources"></a>Recursos adicionales ##

A continuación, se indican algunos de los recursos que puede utilizar para aprender a utilizar Azure y Windows PowerShell.

- Para obtener información sobre cómo tener acceso a los componentes de Almacenamiento de Azure, consulte [Uso de Azure PowerShell con Almacenamiento de Azure](storage-powershell-guide-full.md).

- Para enviar sus comentarios acerca de los cmdlets, informar errores u obtener acceso al código fuente, consulte [Repositorio de código de Azure PowerShell](https://github.com/WindowsAzure/azure-sdk-tools).

- Para obtener información acerca del entorno de línea de comandos y scripts de Windows PowerShell, consulte el [Centro de scripts de TechNet](http://go.microsoft.com/fwlink/p/?LinkId=320211).

- Para obtener información acerca de la instalación, aprendizaje, uso y personalización de Windows PowerShell, consulte [Scripts con Windows PowerShell](http://go.microsoft.com/fwlink/p/?LinkId=320210).

- Para obtener información acerca de qué son los scripts y cómo se ejecutan en Windows PowerShell, consulte [Ejecución de scripts](http://go.microsoft.com/fwlink/p/?LinkId=320627). Este artículo incluye información básica acerca de la creación de scripts y la configuración del equipo para ejecutar scripts.

- Para obtener información acerca de los cmdlets de Azure AD, consulte [Administración de Azure AD mediante Windows PowerShell](http://go.microsoft.com/fwlink/p/?LinkId=320628).





  [Microsoft Online Services - Portal del cliente]: https://mocp.microsoftonline.com/site/default.aspx


<!--HONumber=52-->