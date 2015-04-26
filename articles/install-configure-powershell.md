<properties 
	pageTitle="Instalación y configuración de Azure PowerShell" 
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
	ms.date="11/26/2014" 
	ms.author="coreyp"/>

# Instalación y configuración de Azure PowerShell

<div class="dev-center-tutorial-selector sublanding"><a href="/es-es/manage/install-and-configure-windows-powershell/" title="PowerShell" class="current">PowerShell</a><a href="/es-es/manage/install-and-configure-cli/" title="CLI entre plataformas">CLI entre plataformas</a></div>

Puede utilizar Windows PowerShell para realizar diversas tareas en Azure, tanto de manera interactiva en un símbolo del sistema como de manera automática mediante scripts. Azure PowerShell es un módulo que ofrece cmdlets para administrar Azure mediante Windows PowerShell. Puede utilizar los cmdlets para crear, probar, implementar y administrar soluciones y servicios ofrecidos a través de la plataforma de Azure. En la mayoría de los casos, puede utilizar los cmdlets para realizar las mismas tareas que se realizan a través del Portal de administración de Azure. Por ejemplo, puede crear y configurar servicios en la nube, máquinas virtuales, redes virtuales y sitios web.

El módulo se distribuye en forma de archivo descargable y el código fuente se administra a través de un repositorio disponible públicamente. En las instrucciones de instalación que aparecen más adelante en este tema, se proporciona un enlace a los archivos descargables. Para obtener información acerca del código fuente, consulte [Azure PowerShell code repository][Azure PowerShell code repository].

Esta guía ofrece información básica acerca de la instalación y la configuración de Azure PowerShell para administrar la plataforma de Azure.

## Tabla de contenido

-   [Requisitos previos para utilizar Azure PowerShell][Requisitos previos para utilizar Azure PowerShell]
-   [Direccionamiento del Azure PowerShell][Direccionamiento del Azure PowerShell]
-   [Direccionamiento del su suscripción][Direccionamiento del su suscripción]
-   [Ejemplo de uso de los cmdlets][Ejemplo de uso de los cmdlets]
-   [Ayuda][Ayuda]
-   [Recursos adicionales][Recursos adicionales]

### <span id="Prereq"></span></a>Requisitos previos para utilizar Azure PowerShell

Azure es una plataforma basada en suscripción. es decir, para poder usarla es necesaria una suscripción. En la mayoría de los casos, esto también implica que los cmdlets requieren información de suscripción para realizar las tareas a través de su suscripción (algunos cmdlets relacionados con el almacenamiento pueden utilizarse sin esta información). Para proporcionarla, tiene que configurar el equipo para que se conecte a su suscripción. Las instrucciones de cómo hacerlo se incluyen en este artículo, en la sección “Conexión a su suscripción”.

> [WACOM.NOTE] A partir de la versión 0.8.5, los módulos Azure PowerShell requieren Microsoft .NET Framework 4.5.

Al instalar el módulo, el instalador comprueba si el sistema contiene el software necesario e instala todas las dependencias, como la versión correcta de Windows PowerShell y .NET Framework.

## <span id="Install"></span></a>Direccionamiento del Azure PowerShell

Puede descargar e instalar los módulos de Azure PowerShell ejecutando el [instalador de plataforma web de Microsoft][instalador de plataforma web de Microsoft]. Cuando aparezca la opción, haga clic en **Run**. El instalador de plataforma web instala los módulos de Azure PowerShell y todas las dependencias. Siga las indicaciones para finalizar la instalación.

Para obtener más información acerca de las herramientas de línea de comandos disponibles para Azure, consulte [Herramientas de línea de comandos][Herramientas de línea de comandos].

Al instalar el módulo también se instala una consola personalizada para Azure PowerShell. Puede ejecutar los cmdlets desde la consola estándar de Windows PowerShell o desde la consola de Azure PowerShell.

El método que debe seguir para abrir cualquiera de estas consolas depende de la versión de Windows que esté utilizando:

-   Si el equipo dispone de Windows 8 o Windows Server 2012, o una versión posterior, puede utilizar la búsqueda integrada. En la pantalla de inicio, escriba **power**. De este modo, se devuelve una lista de aplicaciones que incluyen Windows PowerShell y Azure PowerShell. Haga clic en cualquier aplicación para abrir la consola. (Para anclar la aplicación a la pantalla de inicio, haga clic con el botón derecho en el icono).

-   Si el equipo dispone de una versión anterior a Windows 8 o Windows Server 2012, use el menú Inicio. En el menú Inicio, haga clic en **Todos los programas**, a continuación en **Azure** y, por último, en **Azure PowerShell**.

## <span id="Connect"></span></a>Direccionamiento del su suscripción

Para utilizar Azure es necesaria una suscripción. Si no la tiene, consulte [Introducción a Azure][Introducción a Azure].

Los cmdlets necesitan la suscripción para que puedan administrar los servicios. Hay dos maneras de proporcionar a Windows PowerShell la información de su suscripción. Puede descargar y usar un certificado de administración que contenga dicha información, o bien iniciar sesión en Azure con su cuenta de Microsoft o una cuenta de la organización. Al iniciar sesión, Azure Active Directory (Azure AD) autentica las credenciales y devuelve un token de acceso que permite a Azure PowerShell administrar su cuenta.

Para elegir el método de autenticación más adecuado a sus necesidades, tenga en cuenta lo siguiente:

-   Azure AD es el método de autenticación recomendado, ya que facilita la administración del acceso a una suscripción. Con la versión 0.8.6 actualizada, permite el escenario de automatización con la autenticación de Azure AD, así como si se usa la cuenta profesional. Además, funciona con la API de Administrador de recursos de Azure.
-   Si utiliza el método del certificado, la información de suscripción está disponible mientras la suscripción y el certificado son válidos. Sin embargo, este método dificulta la administración del acceso a las suscripciones compartidas, es decir, aquellas en las que hay más de una persona autorizada para obtener acceso a la cuenta. Adicionalmente, la API de Administrador de recursos de Azure no acepta la autenticación mediante certificados.

Para obtener más información acerca de la administración de la autenticación y la suscripción en Azure, consulte [Administrar cuentas, suscripciones y roles administrativos][Administrar cuentas, suscripciones y roles administrativos].

### Uso del método de Azure AD

1.  Abra la consola de Azure PowerShell siguiendo las instrucciones del apartado [Instalación de Azure PowerShell][Direccionamiento del Azure PowerShell].

2.  Escriba el siguiente comando:

    `Add-AzureAccount`

3.  En la ventana, escriba la dirección de correo electrónico y la contraseña asociadas a su cuenta.

4.  Azure autentica y guarda las credenciales y, a continuación, cierra la ventana.

5.  A partir de la versión 0.8.6, si dispone de una cuenta profesional, puede escribir el comando siguiente para omitir la ventana emergente. De este modo, aparecerá la ventana estándar de credenciales de Windows PowerShell para que pueda especificar el nombre de usuario y la contraseña de su cuenta profesional.

        $cred = Get-Credential
        Add-AzureAccount -Credential $cred

6.  Si usa este método en un script de automatización y desea evitar todas las ventanas emergentes, utilice el fragmento de código siguiente

        $userName = "<your organizational account user name>"
        $securePassword = ConvertTo-SecureString -String "<your organizational account password>" -AsPlainText -Force
        $cred = New-Object System.Management.Automation.PSCredential($userName, $securePassword)
        Add-AzureAccount -Credential $cred 

    > [WACOM.NOTE] Este método de inicio de sesión no interactivo solo funciona con la cuentas profesionales. Una cuenta profesional es un usuario administrado por su organización y definido en su inquilino de Azure Active Directory de la organización. Si actualmente no tiene una cuenta profesional y usa una cuenta Microsoft para iniciar sesión en su suscripción de Azure, puede crear una fácilmente siguiendo los pasos que se indican a continuación.
    >
    > 1.  Inicie sesión en el [Portal de administración de Azure][Portal de administración de Azure] y haga clic en **Active Directory**.
    >
    > 2.  Si no hay ningún directorio, seleccione **Create your directory** y proporcione la información que se le pida.
    >
    > 3.  Seleccione su directorio y agregue un nuevo usuario. Este nuevo usuario es una cuenta profesional.
    >
    >     Durante la creación del usuario, se le proporcionará una dirección de correo electrónico para el usuario y una contraseña temporal. Guarde esta información porque la usará en otro paso.
    >
    > 4.  En el portal de administración, seleccione **Settings** y, a continuación, **Administrators**. Seleccione **Add** y agregue el nuevo usuario como coadministrador. Así permite a la cuenta profesional administrar su suscripción de Azure.
    >
    > 5.  Finalmente, cierre sesión en el portal de Azure y, a continuación, vuelva a iniciarla usando la nueva cuenta profesional. Si es la primera vez que inicia sesión con esta cuenta, se le pedirá que cambie la contraseña.
    >
    > Para obtener más información acerca de la cuenta profesional con Microsoft Azure, consulte [Inicio de sesión como organización en Microsoft Azure][Inicio de sesión como organización en Microsoft Azure].

### Uso del método del certificado

El módulo de Azure incluye cmdlets que le ayudan a descargar e importar el certificado.

-   El cmdlet **Get-AzurePublishSettingsFile** abre una página web en el
    Portal de administración de Azure desde la que puede
    descargar la información de suscripción. Esta información se presenta en forma de archivo .publishsettings.

-   El cmdlet **Import-AzurePublishSettingsFile** importa el archivo .publishsettings para que lo utilice el módulo. Este archivo incluye un certificado de administración con credenciales de seguridad.

<div class="dev-callout"> 
<b>Nota:</b>
<p>Los cmdlets del m&oacute;dulo AzureResourceManager requieren el m&eacute;todo de Azure AD (Add-AzureAccount). Estos cmdlets no admiten la publicaci&oacute;n de archivos de configuraci&oacute;n. Para obtener m&aacute;s informaci&oacute;n sobre los cmdlets del m&oacute;dulo AzureResourceManager, consulte <a href="http://go.microsoft.com/fwlink/?LinkID=394765">Cmdlets del administrador de recursos de Azure</a>.</p> 
</div>

<div class="dev-callout"> 
<b>Importante</b> 
<p>Es recomendable que elimine el perfil de publicaci&oacute;n que
que descarg&oacute; utilizando el cmdlet <b>Get-AzurePublishSettingsFile</b> una vez que importe esta
configuraci&oacute;n. Debido a que el certificado de administraci&oacute;n incluye credenciales de seguridad,
no deber&iacute;an poder obtener acceso a &eacute;l usuarios no autorizados. Si necesita m&aacute;s informaci&oacute;n
acerca de sus suscripciones, puede obtenerla del <a href="http://manage.windowsazure.com/">Portal de administraci&oacute;n de Azure</a> o del <a href="http://go.microsoft.com/fwlink/p/?LinkId=324875">Portal del cliente de Microsoft Online Services (en ingl&eacute;s)</a>.</p> 
</div>

1.  Inicie sesión en el [Portal de administración de Azure][2] con las credenciales de su cuenta de Azure.

2.  Abra la consola de Azure PowerShell siguiendo las instrucciones del apartado [Instalación de Azure PowerShell][Direccionamiento del Azure PowerShell].

3.  Escriba el siguiente comando:

    `Get-AzurePublishSettingsFile`

4.  Cuando aparezca la opción, descargue y guarde el perfil de publicación y anote la ruta y
    el nombre del archivo .publishsettings. Esta información se solicita al ejecutar el cmdlet
    **Import-AzurePublishSettingsFile** para importar la configuración. La ubicación y el
    nombre predeterminados del archivo son:

	`C:\\Users\<UserProfile>\\Download\\[*MySubscription*-...]-*downloadDate*-credentials.publishsettings`

5.  Escriba un comando similar al siguiente sustituyendo los marcadores de posición por el nombre de su cuenta de Windows y la ruta y nombre del archivo:

    `Import-AzurePublishSettingsFile C:\Users\<UserProfile>\Downloads\<SubscriptionName>-credentials.publishsettings`

> [WACOM.NOTE] Si se le agrega a otras suscripciones como coadministrador tras importar la configuración de publicación, deberá repetir este
> proceso para descargar un nuevo archivo .publishsettings y, a continuación, importar esa
> configuración. Si desea obtener información acerca de cómo agregar coadministradores para ayudar a administrar los
> servicios para una suscripción, consulte [Agregar y quitar coadministradores en las suscripciones de Azure][Agregar y quitar coadministradores en las suscripciones de Azure].

### Visualización de los detalles de las cuentas y las suscripciones

Puede tener varias cuentas y suscripciones disponibles para su uso por parte de Azure PowerShell. Puede agregar múltiples cuentas ejecutando Add-AzureAccount más de una vez.

Para obtener las cuentas de Azure disponibles, escriba lo siguiente:

    Get-AzureAccount

Para obtener sus suscripciones de Azure, escriba lo siguiente:

    Get-AzureSubscription

## <span id="Ex"></span></a>Ejemplo de uso de los cmdlets

Después de instalar el módulo y configurar el equipo para que se conecte a su suscripción, puede crear un sitio web de Azure. Este ejemplo le ayudará a comenzar a usar los cmdlets de Azure.

1.  Inicie la consola de Azure PowerShell.

2.  Elija un nombre para el sitio web. Elija un nombre que se ajuste a las convenciones de nomenclatura de DNS. Los nombres válidos puede contener solo las letras de la 'a' a la 'z', los números entre '0' y '9', y un guión ('-').

    El nombre del sitio web debe ser único en Azure. En este ejemplo, usaremos "mySite". Sin embargo, asegúrese de elegir un nombre diferente, tal como el nombre de cuenta seguido de un número.

    Una vez que elija un nombre, escriba un comando similar al siguiente. Sustituya el nombre de su sitio web por "mySite".

    `New-AzureWebsite mySite`

    El cmdlet crea el sitio web y devuelve un objeto que representa el nuevo sitio web. Las propiedades del objeto incluyen información de utilidad acerca del sitio web.

3.  Para obtener información sobre el sitio web, escriba el comando siguiente. Este devuelve un poco de información sobre todos los sitios web de la suscripción, incluida la que acaba de crear.

    `Get-AzureWebsite`

4.  Para obtener más información sobre el sitio web, incluya el nombre de este en el comando. Asegúrese de sustituir el nombre del sitio web por "mySite".

    `Get-AzureWebsite -Name mySite`

5.  Los sitios web se inician después de crearse. Para detener el sitio web, escriba este comando, incluido el nombre del sitio web.

    `Stop-AzureWebsite -Name mySite`

6.  Para comprobar que el estado del sitio es 'detenido', vuelva a ejecutar el comando Get-AzureWebsite.

    `Get-AzureWebsite`

7.  Para completar esta prueba, elimine el sitio web. Tipo:

    `Remove-AzureWebsite -Name mySite`

8.  Para completar la tarea, confirme que el sitio web se ha eliminado.

    `Get-AzureWebsite -Name mySite`

## <span id="Help"></span></a>Ayuda

Estos recursos ofrecen ayuda para cmdlets concretos:

-   Desde la consola, puede utilizar el sistema de ayuda integrado. El cmdlet **Get-Help** ofrece acceso a este sistema. En la siguiente tabla se incluyen algunos ejemplos de comandos que se pueden utilizar para obtener ayuda. Puede obtener más información desde la consola escribiendo **help**.

    <table>
    <colgroup>
    <col width="50%" />
    <col width="50%" />
    </colgroup>
    <tbody>
    <tr class="odd">
    <td align="left"><strong>Comando</strong></td>
    <td align="left"><strong>Resultado</strong></td>
    </tr>
    <tr class="even">
    <td align="left">Get-Help</td>
    <td align="left">Describe cómo usar el sistema de ayuda.
    <p><strong>Nota</strong>: la descripción incluye información acerca de los archivos de ayuda que no se aplica al módulo de Azure. En concreto, los archivos de ayuda se instalan si el módulo está instalado. Estos archivos no pueden descargarse por separado.</p></td>
    </tr>
    <tr class="odd">
    <td align="left">Get-Help Azure</td>
    <td align="left">Permite obtener todos los cmdlets del módulo de Azure.</td>
    </tr>
    <tr class="even">
    <td align="left">Get-Help &lt;<strong>language</strong>&gt;-dev</td>
    <td align="left">Permite obtener los cmdlets para el desarrollo y la administración de aplicaciones en un lenguaje específico. Por ejemplo, help node-dev, help php-dev o help python-dev.</td>
    </tr>
    <tr class="odd">
    <td align="left">Get-Help &lt;<strong>cmdlet</strong>&gt;</td>
    <td align="left">Permite obtener ayuda sobre un cmdlet de Windows PowerShell. Reemplace <cmdlet> con el nombre del cmdlet.</td>
    </tr>
    <tr class="even">
    <td align="left">Get-Help &lt;<strong>cmdlet</strong>&gt; -Parameter *</td>
    <td align="left">Permite obtener descripciones de los parámetros del cmdlet. El asterisco (*) significa &quot;todo&quot;.</td>
    </tr>
    <tr class="odd">
    <td align="left">Get-Help &lt;<strong>cmdlet</strong>&gt; -Examples</td>
    <td align="left">Permite obtener la sintaxis y ejemplos de uso del cmdlet.</td>
    </tr>
    <tr class="even">
    <td align="left">Get-Help &lt;<strong>cmdlet</strong>&gt; -Full</td>
    <td align="left">Permite obtener toda la ayuda de un cmdlet, incluidos los detalles técnicos.</td>
    </tr>
    </tbody>
    </table>

-   En la biblioteca de Azure existe información de referencia acerca de los cmdlets de los módulos de Azure PowerShell. Para obtener información, consulte [Referencia de cmdlets de Azure][Referencia de cmdlets de Azure].

Para obtener ayuda de la comunidad, utilice estos foros populares:

-   [Foro de Azure en MSDN (en inglés)][Foro de Azure en MSDN (en inglés)]
-   [Stackoverflow][Stackoverflow]

## <span id="Resources"></span></a>Recursos adicionales

A continuación, se indican algunos de los recursos que puede utilizar para aprender a utilizar Azure y Windows PowerShell.

-   Para enviar sus comentarios acerca de los cmdlets, informar de errores u obtener acceso al código fuente, consulte [Azure PowerShell code repository][Azure PowerShell code repository].

-   Para obtener información acerca del entorno de línea de comandos y scripts de Windows PowerShell, consulte el [Centro de scripts de TechNet][Centro de scripts de TechNet].

-   Para obtener información acerca de la instalación, aprendizaje, uso y personalización de Windows PowerShell, consulte [Windows PowerShell][Windows PowerShell].

-   Para obtener información acerca de qué son los scripts y cómo se ejecutan en Windows PowerShell, consulte [Compatibilidad con la creación de scripts][Compatibilidad con la creación de scripts]. Este artículo incluye información básica acerca de la creación de scripts y la configuración del equipo para ejecutar scripts.

-   Para obtener información acerca de los cmdlets de Azure AD, consulte [Administrar Azure AD mediante Windows PowerShell][Administrar Azure AD mediante Windows PowerShell].

  [Azure PowerShell code repository]: https://github.com/WindowsAzure/azure-sdk-tools
  [Requisitos previos para utilizar Azure PowerShell]: #Prereq
  [Direccionamiento del Azure PowerShell]: #Install
  [Direccionamiento del su suscripción]: #Connect
  [Ejemplo de uso de los cmdlets]: #Ex
  [Ayuda]: #Help
  [Recursos adicionales]: #Resources
  [instalador de plataforma web de Microsoft]: http://go.microsoft.com/fwlink/p/?LinkId=320376
  [Herramientas de línea de comandos]: http://go.microsoft.com/fwlink/?LinkId=320796
  [Introducción a Azure]: http://go.microsoft.com/fwlink/p/?LinkId=320795
  [Administrar cuentas, suscripciones y roles administrativos]: http://go.microsoft.com/fwlink/?LinkId=324796
  [Portal de administración de Azure]: https://manage.windowsazure.com
  [Inicio de sesión como organización en Microsoft Azure]: http://azure.microsoft.com/documentation/articles/sign-up-organization/
  [2]: http://manage.windowsazure.com
  [Agregar y quitar coadministradores en las suscripciones de Azure]: http://msdn.microsoft.com/library/windowsazure/gg456328.aspx
  [Referencia de cmdlets de Azure]: http://msdn.microsoft.com/library/windowsazure/jj554330.aspx
  [Foro de Azure en MSDN (en inglés)]: http://go.microsoft.com/fwlink/p/?LinkId=320212
  [Stackoverflow]: http://go.microsoft.com/fwlink/?LinkId=320213
  [Centro de scripts de TechNet]: http://go.microsoft.com/fwlink/p/?LinkId=320211
  [Windows PowerShell]: http://go.microsoft.com/fwlink/p/?LinkId=320210
  [Compatibilidad con la creación de scripts]: http://go.microsoft.com/fwlink/p/?LinkId=320627
  [Administrar Azure AD mediante Windows PowerShell]: http://go.microsoft.com/fwlink/p/?LinkId=320628

<!--HONumber=46--> 

<!--HONumber=46--> 
