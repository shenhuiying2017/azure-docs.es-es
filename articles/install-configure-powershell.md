<properties  linkid="Install-Config-Windows-Azure-PowerShell" urlDisplayName="Azure PowerShell" pageTitle="How to install and configure Azure PowerShell" description="Learn how to install and configure Azure PowerShell." umbracoNaviHide="0" disqusComments="1" editor="tysonn" manager="jeffreyg" documentationCenter="" services="" solutions="" authors="kathydav" title="How to install and configure Azure PowerShell" />

# Instalación y configuración de Azure PowerShell

 
<div  class="dev-center-tutorial-selector sublanding"><a href="/en-us/manage/install-and-configure-windows-powershell/" title="PowerShell" class="current">PowerShell</a><a href="/en-us/manage/install-and-configure-cli/" title="CLI para varias plataformas">CLI para varias plataformas</a></div>

 Puede utilizar Windows PowerShell para realizar diversas tareas en Azure, tanto de manera interactiva en un símbolo del sistema como de manera automática mediante scripts. Azure PowerShell es un módulo que ofrece cmdlets para administrar Azure mediante Windows PowerShell. Puede utilizar los cmdlets para crear, probar, implementar y administrar soluciones y servicios ofrecidos a través de la plataforma de Azure. En la mayoría de los casos, puede utilizar los cmdlets para realizar las mismas tareas que se realizan a través del Portal de administración de Azure. Por ejemplo, puede crear y configurar servicios en la nube, máquinas virtuales, redes virtuales y sitios web.

El módulo se distribuye en forma de archivo descargable y el código fuente se administra a través de un repositorio disponible públicamente. En las instrucciones de instalación que aparecen más adelante en este tema, se proporciona un enlace a los archivos descargables. Para obtener información acerca del código fuente, consulte [Azure PowerShell code repository][1].

Esta guía ofrece información básica acerca de la instalación y la configuración de Azure PowerShell para administrar la plataforma de Azure.

## Tabla de contenido

* [Requisitos previos para utilizar Azure PowerShell](#Prereq)
* [Instalación de Azure PowerShell](#Install)
* [Conexión con su suscripción](#Connect)
* [Ejemplo de uso de los cmdlets](#Ex)
* [Ayuda](#Help)
* [Recursos adicionales](#Resources)

### <a id="Prereq" ></a>Requisitos previos para utilizar Azure PowerShell

Azure es una plataforma basada en suscripción, es decir, para poder usarla es necesaria una suscripción. En la mayoría de los casos, esto también implica que los cmdlets requieren información de suscripción para realizar las tareas a través de su suscripción (algunos cmdlets relacionados con el almacenamiento pueden utilizarse sin esta información). Para proporcionarla, tiene que configurar el equipo para que se conecte a su suscripción. Las instrucciones de cómo hacerlo se incluyen en este artículo, en la sección "Conexión a su suscripción".

> [WACOM.NOTE] Existen diferentes opciones de suscripción disponibles.
> Para obtener información, consulte [Introducción a Azure][2].

Al instalar el módulo, el instalador comprueba si el sistema contiene el software necesario e instala todas las dependencias, como la versión correcta de Windows PowerShell y .NET Framework.

<h2> <a id="Install" ></a>Instalación de Azure PowerShell</h2>


Puede descargar e instalar el módulo de Azure PowerShell ejecutando el [instalador de plataforma web de Microsoft][3]. Cuando aparezca la opción, haga clic en **Run**. Se carga el instalador de plataforma web de Microsoft con el módulo **Azure PowerShell** disponible para su instalación. El instalador de plataforma web instala todas las dependencias de los cmdlets de Azure PowerShell. Siga las indicaciones para finalizar la instalación.

Para obtener más información acerca de las herramientas de línea de comandos disponibles para Azure, consulte [Herramientas de línea de comandos][4].

Al instalar el módulo también se instala una consola personalizada para Azure PowerShell. Puede ejecutar los cmdlets desde la consola estándar de Windows PowerShell o desde la consola de Azure PowerShell.

El método que debe seguir para abrir cualquiera de estas consolas depende de la versión de Windows que esté utilizando:

* Si el equipo dispone de Windows 8 o Windows Server 2012, o una versión posterior, puede utilizar la búsqueda integrada. En la pantalla de inicio, escriba **power**. De este modo, aparecerá una lista de aplicaciones que incluyen Windows PowerShell y Azure PowerShell. Haga clic en cualquiera de ellas para abrir la ventana de la consola. (Para anclar la aplicación a la pantalla de inicio, haga clic con el botón derecho en el icono).

* Si el equipo dispone de una versión anterior a Windows 8 o Windows Server 2012, puede utilizar el menú Inicio. En el menú Inicio, haga clic en **Todos los programas**, a continuación en **Azure** y, por último, en **Azure PowerShell**.

<h2><a id="Connect" ></a>Conexión con su suscripción</h2>


Para utilizar Azure es necesaria una suscripción. Si no la tiene, consulte [Introducción a Azure][2].

Los cmdlets requieren información de suscripción para utilizarse a fin de administrar los servicios. En la versión .0.7 del módulo, hay dos maneras de proporcionar esta información. Puede descargar y usar un certificado de administración que contenga dicha información, o bien iniciar sesión en Azure con su cuenta de Microsoft o un identificador de la organización. Una vez que inicie sesión, Azure Active Directory (Azure AD) autenticará las credenciales.

Para elegir el método de autenticación más adecuado a sus necesidades, tenga en cuenta lo siguiente:

* El método de Azure AD puede facilitar la administración del acceso a una suscripción, pero podría interrumpir la automatización. Las credenciales están disponibles para Azure PowerShell durante 12 horas. Una vez que caducan, tiene que volver a iniciar sesión.
* Si utiliza el método del certificado, la información de suscripción está disponible mientras la suscripción y el certificado son válidos. Este método facilita el uso de la automatización para las tareas de ejecución prolongada. Una vez que descarga e importa la información, no tiene que volver a facilitarla. Sin embargo, este método dificulta la administración del acceso a las suscripciones compartidas, es decir, aquellas en las que hay más de una persona autorizada para obtener acceso a la cuenta.

Para obtener más información acerca de la administración de la autenticación y la suscripción en Azure, consulte [Administrar cuentas, suscripciones y roles administrativos][5].

<h3>Uso del método de Azure AD</h3>


1.  Abra la consola de Azure PowerShell siguiendo las instrucciones del apartado [Instalación de Azure PowerShell](#Install)

2.  Escriba el siguiente comando:
    
    `Add-AzureAccount`

3.  En la ventana, escriba la dirección de correo electrónico y la contraseña asociadas con su cuenta.

4.  Azure autentica y guarda las credenciales y, a continuación, cierra la ventana.

<h3>Uso del método del certificado</h3>


El módulo de Azure PowerShell incluye cmdlets que le ayudan a descargar e importar el certificado.

* El cmdlet **Get-AzurePublishSettingsFile** abre una página web en el Portal de administración de Azure desde la que puede descargar la información de suscripción. Esta información se presenta en forma de archivo .publishsettings.

* El cmdlet **Import-AzurePublishSettingsFile** importa el archivo .publishsettings para que lo utilice el módulo. Este archivo incluye un certificado de administración con credenciales de seguridad.

 
<div  class="dev-callout"> 
<b>Importante</b> 
<p>Es recomendable que elimine el perfil de publicación que descargó utilizando el cmdlet <b>Get-AzurePublishSettingsFile</b> una vez que importe esta configuración. Debido a que el certificado de administración incluye credenciales de seguridad, no deberían poder obtener acceso a él usuarios no autorizados. Si necesita información acerca de sus suscripciones, puede obtenerla del <a href="http://manage.windowsazure.com/">Portal de administración de Azure</a> o del <a href="http://go.microsoft.com/fwlink/p/?LinkId=324875">Portal del cliente de Microsoft Online Services (en inglés)</a>.</p> 
</div>

 1.  Inicie sesión en el [Portal de administración de Azure][6] con las credenciales de su cuenta de Azure.

2.  Abra la consola de Azure PowerShell siguiendo las instrucciones del apartado [Instalación de Azure PowerShell](#Install)

3.  Escriba el siguiente comando:
    
    `Get-AzurePublishSettingsFile`

4.  Cuando aparezca la opción, descargue y guarde el perfil de publicación y anote la ruta y el nombre del archivo .publishsettings. Esta información se solicita al ejecutar el cmdlet **Import-AzurePublishSettingsFile** para importar la configuración. La ubicación y el nombre predeterminados del archivo son:
    
    C:\\Usuarios\\<PerfilUsuario>\\Download\\[*MiSuscripción*-...]-*FechaDescarga*-credentials.publishsettings

5.  Escriba un comando similar al siguiente sustituyendo los marcadores de posición por el nombre de su cuenta de Windows y la ruta y nombre del archivo:
    
    Import-AzurePublishSettingsFile
    C:\\Usuarios\\<PerfilUsuario>\\Downloads\\<NombreSuscripción>-credentials.publishsettings

> [WACOM.NOTE] Si se le agrega a otras suscripciones como
> coadministrador tras importar la configuración de publicación, deberá
> repetir este proceso para descargar un nuevo archivo .publishsettings
> y, a continuación, importar esa configuración. Si desea obtener
> información acerca de cómo agregar coadministradores para ayudar a
> administrar los servicios para una suscripción, consulte [Agregar y
> quitar coadministradores en las suscripciones de Azure][7].

<h3> Visualización de los detalles de las cuentas y las suscripciones</h3>


Puede tener varias cuentas y suscripciones disponibles para su uso por parte de Azure PowerShell. Puede agregar múltiples cuentas ejecutando Add-AzureAccount más de una vez.

Para ver las cuentas disponibles, escriba:

    `Get-AzureAccount`

Para ver la información de suscripción, escriba:

    `Get-AzureSubscription`

## <a id="Ex" ></a>Ejemplo de uso de los cmdlets

Después de instalar el módulo y configurar el equipo para que se conecte a su suscripción, puede crear un sitio web de ejemplo que muestre cómo usar los cmdlets.

1.  Abra Azure PowerShell, a menos que ya esté abierto.

2.  Elija un nombre para el sitio. El nombre elegido deberá seguir las convenciones de nomenclatura de DNS. Son nombres válidos aquellos que contienen solo letras de la "a" a la "z", números del "0" al "9" y guiones ("-"). Además, debe ser un nombre exclusivo en Azure.
    
    Una vez que elija un nombre, escriba un comando similar al siguiente. Por ejemplo, para crear un sitio web utilizando su cuenta y un número (para poder utilizar esta convención más de una vez), escriba lo siguiente incluyendo el nombre de su cuenta:
    
    `New-AzureWebsite my-site-name-1`
    
    El cmdlet crea el sitio web y, a continuación, muestra su información.

3.  Para comprobar el estado del sitio web, escriba:
    
    `Get-AzureWebsite`
    
    El cmdlet muestra el nombre y el estado, así como el nombre de host del nuevo sitio web.


   
    > [WACOM.NOTE] Si se ejecuta como se indica, este comando muestra
    > información acerca de todos los sitios web asociados con la
    > suscripción actual.

4.  Los sitios web se inician después de crearse. Para detener el sitio web, escriba:
    
    `Stop-AzureWebsite -Name account-name-1`

5.  Ejecute de nuevo el comando Get-AzureWebsite para comprobar que el estado del sitio indique que está detenido.

6.  Para finalizar esta prueba, puede eliminar el sitio web. Escriba:
    
    `Remove-AzureWebsite -Name account-name-1`
    
    Confirme la eliminación para finalizar la tarea.

## <a id="Help" ></a>Ayuda

Estos recursos ofrecen ayuda para cmdlets concretos:

* Desde la consola, puede utilizar el sistema de ayuda integrado. El cmdlet **Get-Help** ofrece acceso a este sistema. En la siguiente tabla se incluyen algunos ejemplos de comandos que se pueden utilizar para obtener ayuda. Puede obtener más información desde la consola escribiendo **help**.
  
  <table  border="1" cellspacing="4" cellpadding="4">
  <tbody>
  <tr  align="left" valign="top">
      <td><b>Comando</b>
  </td>
  
      <td><b>Resultado</b>
  </td>
  
  </tr>
  
  <tr  align="left" valign="top">
      <td>help</td>
  
      <td>Describe cómo usar el sistema de ayuda. <p><b>Nota</b>
  : la descripción incluye información acerca de los archivos de ayuda que no se aplica al módulo de Azure. En concreto, los archivos de ayuda se instalan si el módulo está instalado. Estos archivos no pueden descargarse por separado.</p>
  
  </td> </tr> <tr  align="left"
valign="top"> <td >help azure</td>
<td >Enumera todos los cmdlets del módulo de
Azure PowerShell.</td> </tr> <tr  align="left" valign="top"> <td >help
<<b>language</b>>-dev</td> <td
>Enumera los cmdlets para desarrollar y
administrar aplicaciones en un lenguaje concreto. Por ejemplo, help node-dev, help php-dev o help python-dev.</td> </tr> <tr
 align="left" valign="top"> <td
>help <<b>cmdlet</b>></td> <td
>Muestra información de ayuda para un cmdlet de
Windows PowerShell.</td> </tr> <tr  align="left" valign="top"> <td >help
<<b>cmdlet</b>> -parameter *</td>
<td >Muestra definiciones de parámetros para un
cmdlet. Por ejemplo, help get-azuresubscription -parameter *</td>
</tr> <tr  align="left" valign="top">
<td >help <<b>cmdlet</b>> -examples</td> <td
> Muestra la sintaxis y descripción de comandos de
ejemplo para un cmdlet.</td> </tr> <tr
 align="left" valign="top"> <td
>help <<b>cmdlet</b>>
-full</td> <td >Muestra los requisitos
técnicos de un cmdlet.</td> </tr> </tbody>
</table>

* En la biblioteca de Azure existe información de referencia acerca de los cmdlets del módulo de Azure PowerShell. Para obtener información, consulte [Referencia de cmdlets de Azure][8].

Para obtener ayuda de la comunidad, utilice estos foros populares:

* [Foro de Azure en MSDN (en inglés)][9]
* [Stackoverflow][10]

## <a id="Resources" ></a>Recursos adicionales

A continuación, se indican algunos de los recursos que puede utilizar para aprender a utilizar Azure y Windows PowerShell.

* Para enviar sus comentarios acerca de los cmdlets, informar de errores u obtener acceso al código fuente, consulte [Azure PowerShell code repository][1].

* Para obtener información acerca del entorno de línea de comandos y scripts de Windows PowerShell, consulte el [Centro de scripts de TechNet][11].

* Para obtener información acerca de la instalación, aprendizaje, uso y personalización de Windows PowerShell, consulte [Windows PowerShell][12].

* Para obtener información acerca de qué son los scripts y cómo se ejecutan en Windows PowerShell, consulte [Compatibilidad con la creación de scripts][13]. Este artículo incluye información básica acerca de la creación de scripts y la configuración del equipo para ejecutar scripts.

* Para obtener información acerca de los cmdlets de Azure AD, consulte [Administrar Azure AD mediante Windows PowerShell][14].



[1]: https://github.com/WindowsAzure/azure-sdk-tools
[2]: http://go.microsoft.com/fwlink/p/?LinkId=320795
[3]: http://go.microsoft.com/fwlink/p/?LinkId=320376
[4]: http://go.microsoft.com/fwlink/?LinkId=320796
[5]: http://go.microsoft.com/fwlink/?LinkId=324796
[6]: http://manage.windowsazure.com
[7]: http://msdn.microsoft.com/en-us/library/windowsazure/gg456328.aspx
[8]: http://msdn.microsoft.com/en-us/library/windowsazure/jj554330.aspx
[9]: http://go.microsoft.com/fwlink/p/?LinkId=320212
[10]: http://go.microsoft.com/fwlink/?LinkId=320213
[11]: http://go.microsoft.com/fwlink/p/?LinkId=320211
[12]: http://go.microsoft.com/fwlink/p/?LinkId=320210
[13]: http://go.microsoft.com/fwlink/p/?LinkId=320627
[14]: http://go.microsoft.com/fwlink/p/?LinkId=320628