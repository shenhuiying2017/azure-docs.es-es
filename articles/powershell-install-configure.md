---
title: "Cómo instalar y configurar Azure PowerShell"
description: "Obtenga información acerca de cómo instalar y configurar Azure PowerShell."
editor: tysonn
manager: dongill
documentationcenter: 
services: 
author: coreyp-at-msft
ms.assetid: 802b28e0-1a8c-4872-a9ea-c889b15b9498
ms.service: multiple
ms.workload: multiple
ms.tgt_pltfrm: powershell
ms.devlang: na
ms.topic: article
ms.date: 09/30/2016
ms.author: coreyp
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: f20adb27f41cdbd6918340ae688b4ce00dd2d593


---
# <a name="how-to-install-and-configure-azure-powershell"></a>Cómo instalar y configurar Azure PowerShell
<div class="dev-center-tutorial-selector sublanding"><a href="/manage/install-and-configure-windows-powershell/" title="PowerShell" class="current">PowerShell</a><a href="/manage/install-and-configure-cli/" title="Azure CLI">CLI de Azure</a></div>

## <a name="what-is-azure-powershell"></a>¿Qué es Azure PowerShell?
Azure PowerShell es un conjunto de módulos que ofrece cmdlet para administrar Azure con Windows PowerShell. Puede utilizar los cmdlets para crear, probar, implementar y administrar soluciones y servicios ofrecidos a través de la plataforma de Azure. En la mayoría de los casos, se pueden usar los cmdlet para las mismas tareas que Azure Portal, como la creación y configuración de servicios en la nube, máquinas virtuales, redes virtuales y aplicaciones web.

## <a name="how-versioning-works"></a>Funcionamiento del control de versiones
Azure PowerShell usa Versionamiento Semántico, lo que significa que si la versión A > que la versión B, la versión A tiene las API más actualizadas. También significa que en la versión principal hay un cambio importante en uno o varios cmdlets.  Por ejemplo, la versión 1.7.0 es una revisión para abordar un cambio importante en las versiones 1.x de Azure PowerShell.

Para más información sobre los procedimientos de Versionamiento Semántico en Azure PowerShell, consulte su especificación en: http://semver.org

Para obtener las API más recientes, debe usar la versión 2.x. Sin embargo, si tiene scripts escritos para la versión 1.x y no desea absorber los cambios importantes en la versión 2.x descrita en las [notas de la versión](https://github.com/Azure/azure-powershell/blob/dev/documentation/release-notes/migration-guide.2.0.0.md) de 2.x, debe instalar entonces 1.7.0.

Si se instala la versión más reciente del módulo del perfil, y después se carga una versión anterior de un módulo que depende de él, se pueden producir errores de coincidencia entre las versiones. La manera más sencilla de resolver este problema es instalar desde el archivo .msi más reciente. El archivo .msi limpia automáticamente las versiones anteriores de los módulos.

### <a name="installing-module-versions-side-by-side"></a>Instalación de versiones de módulo en paralelo
La versión 2.1.0, y la versión 1.2.6 para AzureStack, son las primeras versiones de módulo diseñadas para instalarse y usarse en paralelo. Como Azure PowerShell usa módulos binarios, debe abrir una nueva ventana de PowerShell y usar **Import-Module** para importar una versión específica de los cmdlets de AzureRM:

    Import-Module AzureRM -RequiredVersion 2.1.0**

Las versiones anteriores a 2.1.0 (excepto la 1.2.6) no funcionan bien en paralelo con otras versiones del módulo de Azure PowerShell. Al cargar una versión anterior de los módulos de Azure PowerShell mediante un comando similar al anterior, se cargarán versiones incompatibles del módulo **AzureRM.Profile**, lo que dará lugar a que los cmdlets le pidan que inicie sesión cada vez que se ejecute un cmdlet, incluso después de haberla ya iniciado.

La manera más fácil de resolver esto es instalar la versión más reciente de Azure PowerShell desde la fuente WebPI o el archivo .msi, ya que se quitan las versiones anteriores de los módulos instalados desde la galería. 

Tenga en cuenta que los módulos de Azure y AzureRM tienen dependencias en común, por lo que si usa ambos módulos, al actualizar uno, debe actualizar ambos. Las versiones anteriores del módulo de Azure tienen el mismo problema con la carga del módulo en paralelo que las versiones del módulo de AzureRM.

<a id="Install"></a>

## <a name="step-1-install"></a>Paso 1: Instalar
A continuación se indican los dos métodos con los que puede instalar Azure PowerShell. Puede instalarlo desde la Galería de PowerShell o la WebPI.

### <a name="installing-azure-powershell-from-the-powershell-gallery"></a>Instalación de Azure PowerShell desde la galería de PowerShell
La forma preferida es usar la Galería de PowerShell. Necesitará el módulo PowerShellGet para usar la Galería de PowerShell. Puede encontrarlo aquí: [PowerShellGallery.com](https://www.powershellgallery.com/).

Instale Azure PowerShell 1.3.0 o posterior desde la Galería de PowerShell mediante un símbolo del sistema de Windows PowerShell o el Entorno de scripting integrado (ISE) de PowerShell con privilegios elevados con los comandos siguientes:

    # Install the Azure Resource Manager modules from the PowerShell Gallery
    Install-Module AzureRM

    # Install the Azure Service Management module from the PowerShell Gallery
    Install-Module Azure

#### <a name="more-about-these-commands"></a>Más información sobre estos comandos
* **Install-Module AzureRM** instala un módulo consolidado para los cmdlets de Azure Resource Manager. El módulo AzureRM depende de 
* un intervalo de versiones específico para cada módulo de Azure Resource Manager. El intervalo de versiones incluido garantiza que no puede incluirse ningún cambio brusco al instalar los módulos de AzureRM con la misma versión principal. Cuando se instala el módulo de AzureRM, cualquier módulo de Azure Resource Manager que no se ha instalado anteriormente se descargará e instalará desde la Galería de PowerShell. Para más información sobre el Versionamiento Semántico que usan los módulos de Azure PowerShell, consulte [semver.org](http://semver.org). 
* **Install-Module Azure** instala el módulo de Azure. Se trata del módulo Administración de servicios de Azure PowerShell 0.9.x. No debe tener cambios importantes y poderse intercambiar por la versión anterior del módulo de Azure.

### <a name="installing-azure-powershell-from-webpi"></a>Instalar Azure PowerShell desde WebPI
La instalación de Azure PowerShell 1.0 y versiones posteriores desde WebPI es igual que para 0.9.x. Descargue [Azure PowerShell](http://aka.ms/webpi-azps) e inicie la instalación. Si tiene Azure PowerShell 0.9.x instalado, la versión 0.9.x se desinstalará como parte de la actualización. Si ha instalado módulos de Azure PowerShell desde la Galería de PowerShell, el instalador quita automáticamente los módulos antes de la instalación para garantizar un entorno coherente de Azure PowerShell.

> [!NOTE]
> Si ha instalado previamente módulos de Azure de la Galería de PowerShell, el instalador los quitará automáticamente. Esto tiene por objeto evitar confusiones sobre los módulos que ha instalado y dónde se encuentran. Los módulos de la Galería de PowerShell normalmente se instalarán en **%ProgramFiles%\WindowsPowerShell\Modules**. En cambio, el instalador de WebPI instalará los módulos de Azure en **%ProgramFiles(x86)%\Microsoft SDKs\Azure\PowerShell\**. Si se produce un error durante la instalación, puede quitar manualmente las carpetas de Azure* de su carpeta **%ProgramFiles%\WindowsPowerShell\Modules** y volver a intentar instalarlos de nuevo.
> 
> 

Una vez completada la instalación, la configuración ```$env:PSModulePath``` debe incluir los directorios que contienen los cmdlets de Azure PowerShell.

> [!NOTE]
> Existe un problema conocido con **$env: PSModulePath** de PowerShell que puede producirse cuando la instalación se realiza desde WebPI. Si el equipo requiere un reinicio debido a actualizaciones del sistema u otras instalaciones, puede hacer que las actualizaciones de **$env: PSModulePath** no incluyan la ruta de acceso donde está instalado Azure PowerShell. Si esto ocurre, verá el mensaje 'cmdlet not recognized' (no se reconoce el cmdlet) al intentar usar los cmdlets de Azure PowerShell después de la instalación o actualización. Si esto ocurre, reiniciar el equipo debe corregir el problema.
> 
> 

Si recibe un mensaje similar al siguiente al intentar cargar o ejecutar cmdlets:

```
    PS C:\> Get-AzureRmResource
    Get-AzureRmResource : The term 'Get-AzureRmResource' is not recognized as the name of a cmdlet, function,
    script file, or operable program. Check the spelling of the name, or if a path was included, verify that the path is
    correct and try again.
    At line:1 char:1
    + Get-AzureRmResource
    + ~~~~~~~~~~~~~~~~~~~~~~~
        + CategoryInfo          : ObjectNotFound: (get-azurermresourcefork:String) [], CommandNotFoundException
        + FullyQualifiedErrorId : CommandNotFoundException
```

Esto se puede corregir reiniciando la máquina o importando los cmdlets de C:\Archivos de programa\WindowsPowerShell\Modules\Azure\XXXX\ como sigue (donde XXXX es la versión of PowerShell instalada):

```
Import-Module "C:\Program Files\WindowsPowerShell\Modules\Azure\XXXX\azure.psd1"
Import-Module "C:\Program Files\WindowsPowerShell\Modules\Azure\XXXX\expressroute\expressroute.psd1"
```

## <a name="step-2-start"></a>Paso 2: Iniciar
Puede ejecutar los cmdlets desde la consola estándar de Windows PowerShell o desde el Entorno de scripting integrado (ISE) de PowerShell.
El método que debe seguir para abrir cualquiera de estas consolas depende de la versión de Windows que esté utilizando:

* Si el equipo dispone de Windows 8 o Windows Server 2012, o una versión posterior, puede utilizar la búsqueda integrada. En la pantalla **Inicio**, comience a escribir power. Se devolverá una lista de aplicaciones que incluyen Windows PowerShell. Haga clic en cualquier aplicación para abrir la consola (para anclar la aplicación a la pantalla **Inicio**, haga clic con el botón derecho en el icono).
* Si el equipo dispone de una versión anterior a Windows 8 o Windows Server 2012, use el menú **Inicio**. En el menú **Inicio**, haga clic sucesivamente en **Todos los programas**, **Accesorios**, carpeta **Windows PowerShell** y **Windows PowerShell**.

También puede ejecutar **Windows PowerShell ISE** para usar elementos de menú y métodos abreviados de teclado para realizar muchas de las mismas tareas que realizaría en la consola de Windows PowerShell. Para usar el ISE, en la consola de Windows PowerShell, ejecute Cmd.exe o, en el cuadro **Ejecutar**, escriba **powershell_ise.exe**.

### <a name="commands-to-help-you-get-started"></a>Comandos para ayudarle a empezar a trabajar
    # To make sure the Azure PowerShell module is available after you install
    Get-Module –ListAvailable 

    # To log in to Azure Resource Manager
    Login-AzureRmAccount

    # You can also use a specific Tenant if you would like a faster log in experience
    # Login-AzureRmAccount -TenantId xxxx

    # To view all subscriptions for your account
    Get-AzureRmSubscription

    # To select a default subscription for your current session
    Get-AzureRmSubscription –SubscriptionName “your sub” | Select-AzureRmSubscription

    # View your current Azure PowerShell session context
    # This session state is only applicable to the current session and will not affect other sessions
    Get-AzureRmContext

    # To select the default storage context for your current session
    Set-AzureRmCurrentStorageAccount –ResourceGroupName “your resource group” –StorageAccountName “your storage account name”

    # View your current Azure PowerShell session context
    # Note: the CurrentStorageAccount is now set in your session context
    Get-AzureRmContext

    # To list all of the blobs in all of your containers in all of your accounts
    Get-AzureRmStorageAccount | Get-AzureStorageContainer | Get-AzureStorageBlob


## <a name="step-3-connect"></a>Paso 3: Conectar
Los cmdlets necesitan la suscripción para poder administrar sus servicios. Puede adquirir una suscripción de Azure si no tiene una. Para obtener instrucciones, consulte [Instrucciones para contratar Azure](http://go.microsoft.com/fwlink/p/?LinkId=320795).

1. Escriba **Login-AzureRmAccount**.
2. Escriba la dirección de correo electrónico y la contraseña asociadas a su cuenta. Azure autentica y guarda las credenciales y, luego, cierra la ventana.

--O--

Inicie sesión en su cuenta profesional o educativa:

    $cred = Get-Credential
    Login-AzureRmAccount -Credential $cred
> [!NOTE]
> Si tiene más de un inquilino asociado a la cuenta de su organización, especifique el parámetro TenantId:
> 
> 

    $loadersubscription = Get-AzureRmSubscription -SubscriptionName $YourSubscriptionName -TenantId $YourAssociatedSubscriptionTenantId


> [!NOTE]
> Este método de inicio de sesión no interactivo solo funciona con una cuenta profesional o educativa. Una cuenta profesional o educativa es un usuario bajo la administración del trabajo o de la escuela y que está definido en la instancia de Azure Active Directory para el trabajo o la escuela. Si actualmente no tiene una cuenta profesional o educativa y usa una cuenta Microsoft para iniciar sesión en su suscripción de Azure, puede crear una fácilmente siguiendo los pasos que se indican a continuación.
> 
> 1. Inicie sesión en el [Portal de Azure clásico](https://manage.windowsazure.com) y haga clic en **Active Directory**.
> 2. Si no hay ningún directorio, seleccione **Cree su directorio** y proporcione la información que se le pida.
> 3. Seleccione su directorio y agregue un nuevo usuario. Este usuario nuevo puede iniciar sesión con una cuenta profesional o educativa. Durante la creación del usuario, se le proporcionará una dirección de correo electrónico para el usuario y una contraseña temporal. Guarde esta información, la usará en el paso 5 que viene a continuación.
> 4. En el Portal de Azure clásico, seleccione **Configuración** y, después, **Administradores**. Seleccione **Agregar** y agregue el usuario nuevo como coadministrador. Esto permite que la cuenta profesional o educativa administre su suscripción de Azure.
> 5. Finalmente, cierre sesión en el Portal de Azure y, después, vuelva a iniciarla usando la cuenta profesional o educativa. Si es la primera vez que inicia sesión con esta cuenta, se le pedirá que cambie la contraseña.
> 
> Para más información sobre el inicio de sesión en Microsoft Azure con una cuenta profesional o educativa, consulte [Inicio de sesión en Azure como una organización](active-directory/sign-up-organization.md).
> 
> Para más información sobre la autenticación y la administración de la suscripción en Azure, consulte [Administrar cuentas, suscripciones y roles administrativos](http://go.microsoft.com/fwlink/?LinkId=324796).
> 
> 

### <a name="view-account-and-subscription-details"></a>Visualización de los detalles de cuenta y suscripción
Puede contar con varias cuentas y suscripciones para usarlas con Azure PowerShell. Puede agregar varias cuentas ejecutando **Add-AzureRmAccount** más de una vez.

Para mostrar las cuentas de Azure disponibles, escriba **Get-AzureAccount**.

Para mostrar las suscripciones de Azure, escriba **Get AzureRmSubscription**.

## <a name="a-idhelpagetting-help"></a><a id="Help"></a>Ayuda
Estos recursos ofrecen ayuda para cmdlets concretos:

* Desde la consola, puede utilizar el sistema de ayuda integrado. El cmdlet **Get-Help** proporciona acceso a este sistema. 
* Para obtener ayuda de la comunidad, utilice estos foros populares:
  
  * [Foro de Azure en MSDN](http://go.microsoft.com/fwlink/p/?LinkId=320212)
  * [Stackoverflow](http://go.microsoft.com/fwlink/?LinkId=320213)

## <a name="learn-more"></a>Más información
Para más información sobre el uso de cmdlets, vea los siguientes recursos:

Para obtener instrucciones básicas sobre el uso de Windows PowerShell, consulte [Uso de Windows PowerShell](http://go.microsoft.com/fwlink/p/?LinkId=321939).

Para más información sobre los cmdlets, consulte [Referencia de cmdlets de Azure](https://msdn.microsoft.com/library/windowsazure/jj554330.aspx).

Para ver scripts de ejemplo e instrucciones que le ayuden a aprender a usar scripts para administrar Azure, visite el [Centro de scripts](http://go.microsoft.com/fwlink/p/?LinkId=321940).




<!--HONumber=Nov16_HO3-->


