<properties pageTitle="Using Windows PowerShell with Resource Manager" metaKeywords="ResourceManager, PowerShell, Azure PowerShell" description="Use Windows PowerShell to create a resource group" metaCanonical="" services="" documentationCenter="" title="Using Windows PowerShell with Resource Manager" authors="juneb" solutions="" manager="mbaldwin" editor="mollybos" />

Uso de Windows PowerShell con el Administrador de recursos
==========================================================

El Administrador de recursos introduce un concepto totalmente nuevo acerca de los recursos de Azure. En lugar de crear y administrar recursos individuales, vamos a empezar a pensar en un servicio complejo, como un blog, una galería de fotos, un portal de SharePoint o un wiki. Vamos a usar una plantilla, es decir, un modelo de recursos del servicio, para crear un grupo de recursos con los recursos que necesita para admitir el servicio. A continuación, puede administrar e implementar dicho grupo de recursos como una unidad lógica.

En este tutorial, se ofrece información acerca de cómo usar Windows PowerShell con el Administrador de recursos para Microsoft Azure. Se explica el proceso de creación e implementación de un grupo de recursos para un sitio web o una aplicación web hospedados en Azure con una base de datos SQL, con todos los recursos que necesita para su funcionamiento.

**Duración aproximada:** 15 minutos

Requisitos previos
------------------

Para poder usar Windows PowerShell con el Administrador de recursos, necesita lo siguiente:

-   La versión 3.0 o posterior de Windows PowerShell. Para saber cuál es la versión de Windows PowerShell, escriba: `$PSVersionTable` y compruebe si el valor de `PSVersion` es 3.0 o posterior. Para instalar una versión más reciente, consulte [Windows Management Framework 3.0](http://www.microsoft.com/en-us/download/details.aspx?id=34595) o [Windows Management Framework 4.0](http://www.microsoft.com/en-us/download/details.aspx?id=40855).

-   La versión 0.8.0 o posterior de Azure PowerShell. Para instalar la última versión y asociarla a la suscripción de Azure, consulte [Instalación y configuración de Azure PowerShell](http://www.windowsazure.com/en-us/documentation/articles/install-configure-powershell/).

Este tutorial está diseñado para los principiantes de Windows PowerShell, pero se asume que se conocen los conceptos básicos, como los módulos, los cmdlets y las sesiones. Para obtener más información acerca de Windows PowerShell, consulte [Getting Started with Windows PowerShell](http://technet.microsoft.com/en-us/library/hh857337.aspx).

Para obtener ayuda detallada con cualquier cmdlet que aparezca en este tutorial, use el cmdlet Get-Help.

    Get-Help <cmdlet-name> -Detailed

Por ejemplo, para obtener ayuda para el cmdlet Add-AzureAccount, escriba:

    Get-Help Add-AzureAccount -Detailed

Apartados de este tutorial
--------------------------

-   [Acerca de los módulos de Azure Powershell](#about)
-   [Creación de un grupo de recursos](#create)
-   [Administración de un grupo de recursos](#manage)
-   [Solución de problemas de un grupo de recursos](#troubleshoot)
-   [Pasos siguientes](#next)

Acerca de los módulos de Azure Powershell
-----------------------------------------

A partir de la versión 0.8.0, la instalación de Azure PowerShell incluye tres módulos de Windows PowerShell:

-   **Azure**: Incluye los cmdlets tradicionales para administrar recursos individuales, como cuentas de almacenamiento, sitios web, bases de datos, máquinas virtuales y servicios multimedia. Para obtener más información, consulte [Azure Service Management Cmdlets](http://msdn.microsoft.com/en-us/library/jj152841.aspx).

-   **AzureResourceManager**: Incluye cmdlets para crear, administrar e implementar los recursos de Azure de un grupo de recursos. Para obtener más información, consulte [Azure Resource Manager Cmdlets](http://go.microsoft.com/fwlink/?LinkID=394765).

-   **AzureProfile**: Incluye cmdlets comunes para ambos módulos, como Add-AzureAccount, Get-AzureSubscription y Switch-AzureMode. Para obtener más información, consulte [Azure Profile Cmdlets](http://go.microsoft.com/fwlink/?LinkID=394766).

> [ WACOM.NOTE] El módulo del Administrador de recursos de Azure se encuentra disponible actualmente en modo de vista previa. Es posible que no ofrezca las mismas capacidades de administración que el módulo de administración de servicios de Azure.

Los módulos de Azure y del Administrador de recursos de Azure no están diseñados para usarse en la misma sesión de Windows PowerShell. Para facilitar el cambio entre ellos, hemos agregado un cmdlet nuevo, **Switch-AzureMode**, al módulo del perfil de Azure.

Al usar Azure PowerShell, los cmdlets de los módulos de Azure y del perfil de Azure se importan de manera predeterminada. Para cambiar al módulo del Administrador de recursos de Azure, use el cmdlet Switch-AzureMode. Este cmdlet quita el módulo de Azure de la sesión e importa los módulos del Administrador de recursos de Azure y del perfil de Azure.

Para cambiar al módulo AzureResoureManager, escriba:

    PS C:PS C:\> Switch-AzureMode -Name AzureResourceManager

Para volver al módulo de Azure, escriba:

    PS C:PS C:\> Switch-AzureMode -Name AzureServiceManagement

De manera predeterminada, Switch-AzureMode afecta solo a la sesión actual. Para que el cambio se aplique a todas las sesiones de Windows PowerShell, use el parámetro **Global** de Switch-AzureMode.

Para obtener ayuda con el cmdlet Switch-AzureMode, escriba: `Get-Help Switch-AzureMode` o consulte [Switch-AzureMode](http://go.microsoft.com/fwlink/?LinkID=394398).

Para obtener una lista de los cmdlets del módulo AzureResourceManager con una sinopsis de ayuda, escriba:

    PS C:PS C:\> Get-Command -Module AzureResourceManager | Get-Help | Format-Table Name, Synopsis

    Name                                   Synopsis
    ----                                   --------
    Get-AzureLocation                      Obtiene las ubicaciones del centro de datos de Azure y los tipos de recursos que admiten
    Get-AzureResource                      Obtiene los recursos de Azure
    Get-AzureResourceGroup                 Obtiene los grupos de recursos de Azure
    Get-AzureResourceGroupDeployment       Obtiene las implementaciones de un grupo de recursos
    Get-AzureResourceGroupGalleryTemplate  Obtiene las plantillas del grupo de recursos de la galería
    Get-AzureResourceGroupLog              Obtiene el registro de implementaciones de un grupo de recursos
    New-AzureResource                      Crea un recurso nuevo en un grupo de recursos
    New-AzureResourceGroup                 Crea un grupo de recursos de Azure y sus recursos
    New-AzureResourceGroupDeployment       Agrega una implementación de Azure a un grupo de recursos
    Remove-AzureResource                   Elimina un recurso
    Remove-AzureResourceGroup              Elimina un grupo de recursos
    Save-AzureResourceGroupGalleryTemplate Guarda una plantilla de la galería en un archivo JSON
    Set-AzureResource                      Cambia las propiedades de un recurso de Azure
    Stop-AzureResourceGroupDeployment      Cancela la implementación de un grupo de recursos
    Test-AzureResourceGroupTemplate        Detecta errores en la plantilla de un grupo de recursos o en los parámetros de la plantilla
    gt; Get-Command -Module AzureResourceManager | Get-Help | Format-Table Name, Synopsis


Para obtener toda la ayuda posible para un cmdlet, escriba un comando con el formato:

    Get-Help <cmdlet-name> -Full

Por ejemplo:

    Get-Help Get-AzureLocation -Full

Creación de un grupo de recursos para un sitio web y una base de datos
======================================================================

En esta sección del tutorial se explica el proceso de creación e implementación de un grupo de recursos para un sitio web con una base de datos SQL.

Para realizar esta tarea no es necesario que sea experto en Azure, SQL, sitios web o administración de recursos. Las plantillas ofrecen un modelo del grupo de recursos con todos los recursos que puede necesitar. Además, como vamos a usar Windows PowerShell para automatizar las tareas, puede usar estos procesos como modelo para el scripting de tareas a gran escala.

Paso 1: Cambiar al Administrador de recursos de Azure
-----------------------------------------------------

1.  Inicie Windows PowerShell. Puede usar el programa host que prefiera, como la consola de Windows PowerShell o Windows PowerShell ISE.

2.  Use el cmdlet **Switch-AzureMode** para importar los cmdlets de los módulos AzureResourceManager y AzureProfile.

    `PS C:PS C:\>Switch-AzureMode AzureResourceManager`

3.  Para agregar la cuenta de Azure a la sesión de Windows PowerShell, use el cmdlet **Add-AzureAccount**.

    `PS C:PS C:\> Add-AzureAccount`

El cmdlet le pide una dirección de correo electrónico y una contraseña. A continuación, descarga la configuración de la cuenta a fin de que esté disponible para Windows PowerShell.

La configuración de la cuenta caduca, por lo que necesita actualizarla ocasionalmente. Para actualizarla, vuelva a ejecutar **Add-AzureAccount**.

> [ WACOM.NOTE] El módulo AzureResourceManager necesita Add-AzureAccount. No basta con un archivo de configuración de publicación.

Paso 2: Seleccionar una plantilla de la galería
-----------------------------------------------

Hay varias formas de crear un grupo de recursos y sus recursos, pero la más sencilla consiste en usar la plantilla de un grupo de recursos. La *plantilla de un grupo de recursos* es una cadena JSON que define los recursos de un grupo de recursos. La cadena incluye marcadores de posición denominados "parámetros" para los valores definidos por el usuario, como nombres y tamaños.

Azure hospeda una galería de plantillas de grupo de recursos, aunque puede crear plantillas personalizadas, bien desde cero o con la edición de una plantilla de la galería. En este tutorial, vamos a usar una plantilla de la galería.

Para buscar una plantilla en la galería de plantillas de grupo de recursos de Azure, use el cmdlet **Get-AzureResourceGroupGalleryTemplate**.

En el símbolo del sistema de Windows Powershell, escriba:

    PS C:PS C:\> Get-AzureResourceGroupGalleryTemplate

El cmdlet devuelve una lista de plantillas de la galería con las propiedades Publisher y Identity. Use la propiedad **Identity** para identificar la plantilla en los comandos.

    Publisher       Identity
    ---------       --------
    Ghost           Ghost.Ghost.0.1.0-preview1
    Joomla          Joomla.Joomla.0.1.0-preview1
    Microsoft       Microsoft.ASPNETEmptySite.0.1.0-preview1
    Microsoft       Microsoft.ASPNETstarterSite.0.1.0-preview1
    Microsoft       Microsoft.Bakery.0.1.0-preview1
    Microsoft       Microsoft.Boilerplate.0.1.0-preview1
    ...

SUGERENCIA: para recuperar el último comando, presione la tecla de flecha arriba.

La plantilla Microsoft.WebSiteSQLDatabase.0.1.0-preview1.json parece interesante. Para obtener más información acerca de una plantilla de la galería, use el parámetro **Identity**. El valor del parámetro Identity es la identidad de la plantilla.

    PS C:PS C:\> Get-AzureResourceGroupGalleryTemplate -Identity Microsoft.WebSiteSQLDatabase.0.1.0-preview1.json

El cmdlet devuelve un objeto con mucha más información acerca de la plantilla, incluida una descripción.

    <p>Sitios web Azure ofrece opciones de desarrollo, implementación y 
    escalado flexibles y seguras para aplicaciones web de cualquier tamaño. Use 
    las herramientas existentes para crear e implementar aplicaciones sin la 
    complicación de administrar la infraestructura.</p>

Parece que esta plantilla cubrirá nuestras necesidades. Vamos a guardarla en el disco y a analizarla más detenidamente.

Paso 3: Examinar la plantilla
-----------------------------

Vamos a guardar la plantilla en el disco como un archivo JSON. Este paso no es obligatorio, pero facilita la visualización de la plantilla. Para guardarla, use el cmdlet **Save-AzureResourceGroupGalleryTemplate**. Use su parámetro **Identity** para especificar la plantilla y el parámetro **Path** para definir una ruta de acceso en el disco.

Save-AzureResourceGroupGalleryTemplate guarda la plantilla y devuelve la ruta de acceso al nombre de archivo del archivo de la plantilla JSON.

    PS C:PS C:\> Save-AzureResourceGroupGalleryTemplate -Identity Microsoft.WebSiteSQLDatabase.0.1.0-preview1.json -Path D:\Azure\Templates

    Ruta de acceso
    ----
    D:\Azure\Templates\Microsoft.WebSite.0.1.0-preview1.json

Puede ver el archivo de plantilla en un editor de texto, como el Bloc de notas. Cada plantilla tiene una sección dedicada a los **recursos** y otra a los **parámetros**.

En la sección **recursos** de la plantilla se enumeran los recursos que la plantilla crea. Esta plantilla crea un servidor de base de datos SQL y una base de datos SQL, una granja de servidores y un sitio web y varias configuraciones de administración.

La definición de cada recurso incluye sus propiedades, como el nombre, el tipo y la ubicación, además de los parámetros para los valores definidos por el usuario. Por ejemplo, en esta sección de la plantilla se define la base de datos SQL. Incluye los parámetros para el nombre de la base de datos ([parameters('databaseName')]), la ubicación del servidor de base de datos [parameters('serverLocation')] y la propiedad de intercalación [parameters('collation')].

     {
          "name": "[parameters('databaseName')]",
          "type": "databases",
          "location": "[parameters('serverLocation')]",
          "apiVersion": "2.0",
          "dependsOn": [
            "[concat('Microsoft.Sql/servers/', parameters('serverName'))]"
          ],
          "properties": {
            "edition": "Web",
            "collation": "[parameters('collation')]",
            "maxSizeBytes": "1073741824"
          }
        },

La sección de **parámetros** de la plantilla es una recopilación de los parámetros definidos en todos los recursos. Incluye las propiedades databaseName, serverLocation y collation.

    "parameters": {
    ...    

    "serverLocation": {
      "type": "string"
    }, 
    ...

    "databaseName": {
      "type": "string"
    },
    "collation": {
      "type": "string",
      "defaultValue": "SQL_Latin1_General_CP1_CI_AS"
    }

Algunos parámetros tienen un valor predeterminado. Al usar la plantilla, no es necesario que facilite valores para estos parámetros. Si no especifica un valor, se usa el predeterminado.

    "collation": {
          "type": "string",
          "defaultValue": "SQL_Latin1_General_CP1_CI_AS"
        }

Cuando los parámetros tienen valores enumerados, los valores válidos se muestran con el parámetro. Por ejemplo, el parámetro **sku** puede tener los valores Free, Shared, Basic o Standard. Si no especifica ningún valor para el parámetro **sku**, usa el valor predeterminado, que es Free.

    "sku": {
      "type": "string",
      "allowedValues": [
        "Free",
        "Shared",
        "Basic",
        "Standard"
      ],
      "defaultValue": "Free"
    },

Tenga en cuenta que el parámetro **administratorLoginPassword** usa una cadena protegida, no texto sin formato. Al facilitar un valor para una cadena protegida, el valor se oscurece.

    "administratorLoginPassword": {
      "type": "securestring"
    },

Ya estamos casi listos para usar la plantilla, pero antes de hacerlo, necesitamos encontrar las ubicaciones de cada uno de los recursos.

Paso 4: Encontrar las ubicaciones de los tipos de recursos
----------------------------------------------------------

La mayoría de las plantillas le piden que especifique la ubicación de cada uno de los recursos de un grupo de recursos. Cada recurso se encuentra en un centro de datos de Azure, pero no todos los centros de datos de Azure admiten todos los tipos de recursos.

Seleccione alguna ubicación que admita el tipo de recurso. Los recursos de un grupo de recursos no necesitan estar en la misma ubicación y tampoco necesitan estar en la misma ubicación que el grupo de recursos o que la suscripción.

Para encontrar las ubicaciones que admiten cada tipo de recurso, use el cmdlet **Get-AzureLocation**. A continuación, se muestran un extracto de la salida. (Esta salida puede diferir de la suya. Los detalles pueden cambiar con el tiempo).

    Name                                 Locations
    ----                                 ---------
    ResourceGroup                        East Asia, South East Asia, East US, West US, North Central US,
                                         South Central US, Central US, North Europe, West Europe

    Microsoft.Sql/servers/databases      Brazil South, Central US, East Asia, East US, East US 2, Japan
                                         East, Japan West, North Central US, North Europe, South Central US,
                                         Southeast Asia, West Europe, West US

    Microsoft.Web/serverFarms            Brazil South, East Asia, East US, Japan East, Japan West, North
                                         Central US, North Europe, West Europe, West US

    Microsoft.Web/sites                  Brazil South, East Asia, East US, Japan East, Japan West, North
                                         Central US, North Europe, West Europe, West US

Ahora ya disponemos de la información que necesitamos para crear el grupo de recursos.

Paso 5: Crear un grupo de recursos
----------------------------------

En este paso, vamos a usar la plantilla del grupo de recursos para crear el grupo de recursos. Como referencia, abra el archivo JSON Microsoft.WebSiteSQLDatabase.0.1.0-preview1 en el disco y continúe.

Para crear un grupo de recursos, use el cmdlet **New-AzureResourceGroup**.

El comando usa el parámetro **Name** para especificar un nombre para el grupo de recursos y el parámetro **Location** para definir su ubicación. Use la salida de **Get-AzureLocation** para seleccionar la ubicación del grupo de recursos. Usa el parámetro **GalleryTemplateIdentity** para especificar la plantilla de la galería.

    PS C:PS C:\> New-AzureResourceGroup ` 
            -Name TestRG1 `
            -Location "East Asia" `
            -GalleryTemplateIdentity Microsoft.WebSiteSQLDatabase.0.1.0-preview1 `
            ....

En cuanto escriba el nombre de la plantilla, New-AzureResourceGroup captura la plantilla, la analiza y agrega los parámetros de la plantilla al comando de manera dinámica. De esta forma, resulta muy fácil especificar los valores de los parámetros de la plantilla. Además, si olvida el valor de algún parámetro necesario, Windows PowerShell le pide dicho valor.

**Parámetros dinámicos de la plantilla**

Para obtener los parámetros, escriba el signo menos (-) para indicar el nombre de un parámetro y, a continuación, presione la tecla TAB. También puede escribir las primera letras del nombre de un parámetro, como siteName, y, a continuación, presionar la tecla TAB.

     PS C: PS C:\> New-AzureResourceGroup -Name TestRG1 -Location "East Asia" -GalleryTemplateIdentity Microsoft.WebSiteSQLDatabase.0.1.0-preview1 
        -si<TAB>

Windows PowerShell completa el nombre del parámetro. Para alternar entre los nombres de parámetros, presione la tecla TAB varias veces.

     PS C: PS C:\> New-AzureResourceGroup -Name TestRG1 -Location "East Asia" -GalleryTemplateIdentity Microsoft.WebSiteSQLDatabase.0.1.0-preview1 
        -siteName 


Escriba un nombre para el sitio web y repita el proceso con TAB para cada uno de los parámetros. Los parámetros con un valor predeterminado son opcionales. Para aceptar el valor predeterminado, omita el parámetro del comando.

Si el parámetro de una plantilla tiene valores enumerados, como el parámetro sku de esta plantilla, para alternar entre los valores del parámetro, presione la tecla TAB.

     PS C: PS C:\> New-AzureResourceGroup -Name TestRG1 -Location "East Asia" -GalleryTemplateIdentity Microsoft.WebSiteSQLDatabase.0.1.0-preview1 
        -siteName TestSite -sku <TAB>

        PS C:\> New-AzureResourceGroup -Name TestRG1 -Location "East Asia" -GalleryTemplateIdentity Microsoft.WebSiteSQLDatabase.0.1.0-preview1 
        -siteName TestSite -sku Free<TAB>

        PS C:\> New-AzureResourceGroup -Name TestRG1 -Location "East Asia" -GalleryTemplateIdentity Microsoft.WebSiteSQLDatabase.0.1.0-preview1 
        -siteName TestSite -sku Basic<TAB>

A continuación, se muestra un ejemplo de un comando New-AzureResourceGroup que especifica solo los parámetros necesarios de la plantilla y el parámetro común **Verbose**. Observe que **administratorLoginPassword** se omite. (La comilla simple (\`) es el carácter de continuación de línea de Windows PowerShell).

    PS C:PS C:\> New-AzureResourceGroup 
    -Name TestRG `
    -Location "East Asia" `
    -GalleryTemplateIdentity Microsoft.WebSiteSQLDatabase.0.1.0-preview1 `
    -siteName TestSite `
    -hostingPlanName TestPlan `
    -siteLocation "North Europe" `
    -serverName testserver `
    -serverLocation "West US" `
    -administratorLogin Admin01 `
    -databaseName TestDB `
    -Verbose

Al escribir el comando, se le pide el parámetro obligatorio que falta, que es **administratorLoginPassword**. Y, al escribir la contraseña, el valor de cadena segura se oscurece. Esta estrategia elimina el riesgo de ofrecer una contraseña en texto sin formato.

    cmdlet New-AzureResourceGroup at command pipeline position 1
    Supply values for the following parameters:
    (Type !
     for Help.)
    administratorLoginPassword: **********

**New-AzureResourcGroup** devuelve el grupo de recursos que ha creado e implementado. A continuación, se muestra la salida del comando, incluida la salida detallada.

    VERBOSE: 3:47:30 PM - Create resource group 'TestRG' in location 'East Asia'
    VERBOSE: 3:47:30 PM - Template is valid.
    VERBOSE: 3:47:31 PM - Create template deployment 'Microsoft.WebSiteSQLDatabase.0.1.0-preview1'
    using template https://gallerystoreprodch.blob.core.windows.net/prod-microsoft-windowsazure-gallery/8D6B920B-10F4-4B5A-B3DA-9D398FBCF3EE.PUBLICGALLERYITEMS.MICROSOFT.WEBSITESQLDATABASE.0.1.0-PREVIEW1/DeploymentTemplates/Website_NewHostingPlan_SQL_NewDB-Default.json.
    VERBOSE: 3:47:43 PM - Resource Microsoft.Sql/servers 'testserver' provisioning status is succeeded
    VERBOSE: 3:47:43 PM - Resource Microsoft.Web/serverFarms 'TestPlan' provisioning status is
    succeeded
    VERBOSE: 3:47:47 PM - Resource Microsoft.Sql/servers/databases 'testserver/TestDB' provisioning status is succeeded
    VERBOSE: 3:47:47 PM - Resource microsoft.insights/autoscalesettings 'TestPlan-TestRG'
    provisioning status is succeeded
    VERBOSE: 3:47:47 PM - Resource Microsoft.Sql/servers/firewallrules
    'testserver/AllowAllWindowsAzureIps' provisioning status is succeeded
    VERBOSE: 3:47:50 PM - Resource Microsoft.Web/Sites 'TestSite' provisioning status is succeeded
    VERBOSE: 3:47:54 PM - Resource Microsoft.Web/Sites/config 'TestSite/web' provisioning status is succeeded
    VERBOSE: 3:47:54 PM - Resource microsoft.insights/alertrules 'ServerErrors-TestSite' provisioning
    status is succeeded
    VERBOSE: 3:47:57 PM - Resource microsoft.insights/components 'TestSite' provisioning status is succeeded


    ResourceGroupName : TestRG
    Location          : eastasia
    ProvisioningState : Succeeded
    Resources         :
                    Name                   Type                                  Location
                    =====================  ====================================  =========
                    ServerErrors-TestSite  microsoft.insights/alertrules         eastus
                    TestPlan-TestRG        microsoft.insights/autoscalesettings  eastus
                    TestSite               microsoft.insights/components         centralus
                    testserver             Microsoft.Sql/servers                 westus
                    TestDB                 Microsoft.Sql/servers/databases       westus
                    TestPlan               Microsoft.Web/serverFarms             westus
                    TestSite               Microsoft.Web/sites                   westus

Con tan solo unos pasos, hemos creado e implementado los recursos necesarios para un sitio web complejo. La plantilla de la galería ha ofrecido casi toda la información que necesitábamos para esta tarea. Además, la tarea se automatiza con facilidad.

Administración de un grupo de recursos
======================================

Después de crear un grupo de recursos, puede usar los cmdlets del módulo AzureResourceManager para administrar el grupo de recursos, cambiarlo, agregarle recursos y eliminarlo.

-   Para obtener los grupos de recursos de la suscripción, use el cmdlet **Get-AzureResourceGroup**:

          PS C:>Get-AzureResourceGroup

          ResourceGroupName : TestRG
          Location          : eastasia
          ProvisioningState : Succeeded
          Resources         :
                          Name                   Type                                  Location
                          =====================  ====================================  =========
                          ServerErrors-TestSite  microsoft.insights/alertrules         eastus
                          TestPlan-TestRG        microsoft.insights/autoscalesettings  eastus
                          TestSite               microsoft.insights/components         centralus
                          testserver             Microsoft.Sql/servers                 westus
                          TestDB                 Microsoft.Sql/servers/databases       westus
                          TestPlan               Microsoft.Web/serverFarms             westus
                          TestSite               Microsoft.Web/sites                   westus

-   Para obtener los recursos del grupo de recursos, use el cmdlet **GetAzureResource** y su parámetro ResourceGroupName. Sin los parámetros, Get-AzureResource obtiene todos los recursos de la suscripción de Azure.

          PS C:  PS C:\> Get-AzureResource -ResourceGroupName TestRG
            
          Name                   ResourceType                          Location
          ----                   ------------                          --------
          ServerErrors-TestSite  microsoft.insights/alertrules         eastus
          TestPlan-TestRG        microsoft.insights/autoscalesettings  eastus
          TestSite               microsoft.insights/components         centralus
          testserver             Microsoft.Sql/servers                 westus
          TestDB                 Microsoft.Sql/servers/databases       westus
          TestPlan               Microsoft.Web/serverFarms             westus
          TestSite               Microsoft.Web/sites                   westus


-   Para agregar un recurso al grupo de recursos, use el cmdlet **New-AzureResource**. Este comando agrega un sitio web nuevo al grupo de recursos TestRG. Este comando es algo más complejo, porque no usa ninguna plantilla.

          PS C:  PS C:\>New-AzureResource -Name TestSite2 `
          -Location "North Europe" `
          -ResourceGroupName TestRG `
          -ResourceType "Microsoft.Web/sites" `
          -ApiVersion 2004-04-01 `
          -PropertyObject @{"name" = "TestSite2"; "siteMode"= "Limited"; "computeMode" = "Shared"}

-   Para agregar una implementación nueva basada en una plantilla al grupo de recursos, use el comando **New-AzureResourceGroupDeployment**.

          PS C:  PS C:\>New-AzureResourceGroupDeployment ` 
          -ResourceGroupName TestRG `
          -GalleryTemplateIdentity Microsoft.WebSite.0.1.0-preview1 `
          -siteName TestWeb2 `
          -hostingPlanName TestDeploy2 `
          -siteMode Limited `
          -computeMode Dedicated `
          -siteLocation "North Europe" 
          -subscriptionID "9b14a38b-4b93-4554-8bb0-3cefb47abcde" `
          -resourceGroup TestRG

-   Para eliminar un recurso del grupo de recursos, use el cmdlet **Remove-AzureResource**. Este cmdlet elimina el recurso, pero no el grupo de recursos.

    Este comando quita el sitio web TestSite2 del grupo de recursos TestRG.

          Remove-AzureResource -Name TestSite2 `
              -Location "North Europe" `
              -ResourceGroupName TestRG `
              -ResourceType "Microsoft.Web/sites" `
              -ApiVersion 2004-04-01

-   Para eliminar un grupo de recursos, use el cmdlet **Remove-AzureResourceGroup**. Este cmdlet elimina el grupo de recursos y sus recursos.

          PS C:\ps-test> Remove-AzureResourceGroup -Name TestRG
            
          Confirm
          Are you sure you want to remove resource group 'TestRG'
          [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y

Solución de problemas de un grupo de recursos
=============================================

A medida que experimente con los cmdlets en los módulos AzureResourceManager, es posible que se produzcan errores. Use las sugerencias de esta sección para resolverlos.

Prevención de errores
---------------------

El módulo AzureResourceManager incluye cmdlets que le ayudan a prevenir errores.

-   **Get-AzureLocation**: Este cmdlet obtiene las ubicaciones que admiten cada tipo de recurso. Antes de especificar una ubicación para un recurso, use este cmdlet para comprobar si la ubicación admite el tipo de recurso.

-   **Test-AzureResourceGroupTemplate**: Pruebe la plantilla y los parámetros de esta antes de usarlos. Especifique una plantilla personalizada o de la galería y los valores de los parámetros de la plantilla que pretende usar. Este cmdlet prueba si la plantilla es coherente internamente y si el conjunto de valores de los parámetros coincide con la plantilla.

Corrección de errores
---------------------

-   **Get-AzureResourceGroupLog**: Este cmdlet obtiene las entradas del registro para cada implementación del grupo de recursos. Si algo va mal, empiece por examinar los registros de implementación.

-   **Verbose y Debug**: Los cmdlets del módulo AzureResourceManager llaman a las API REST que hacen el trabajo real. Para ver los mensajes que las API devuelven, defina la variable \$DebugPreference en "Continue" y use el parámetro común Verbose en los comandos. Los mensajes suelen ofrecer pistas fundamentales sobre la causa de cualquier error.

-   **Your Azure credentials have not been set up or have expired**: Para actualizar las credenciales de la sesión de Windows PowerShell, use el cmdlet Add-AzureAccount. Las credenciales de un archivo de configuración de publicación no son suficientes para los cmdlets del módulo AzureResourceManager.

Pasos siguientes
================

Para obtener más información acerca de Windows PowerShell con el Administrador de recursos:

-   [Azure Resource Manager Cmdlets](http://go.microsoft.com/fwlink/?LinkID=394765&clcid=0x409): obtenga información acerca del uso de los cmdlets de los módulos AzureResourceManager.
-   [Blog sobre Azure](http://blogs.msdn.com/windowsazure) (en inglés): obtenga información acerca de las nuevas características de Azure.
-   [Blog sobre Windows PowerShell](http://blogs.msdn.com/powershell) (en inglés): obtenga información acerca de las nuevas características de Windows PowerShell.
-   ["Hey, Scripting Guy!" Blog](http://blogs.technet.com/b/heyscriptingguy/): obtenga sugerencias y trucos del mundo real de la comunidad.
-   [Uso de la interfaz de la línea de comandos para varias plataformas de Azure con el Administrador de recursos](http://www.windowsazure.com/en-us/documentation/articles/xplat-cli-azure-resource-manager/): conozca formas alternativas de automatizar las operaciones del Administrador de recursos.

