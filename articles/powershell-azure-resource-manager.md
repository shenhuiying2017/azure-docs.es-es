<properties 
	pageTitle="Uso de Azure PowerShell con Administrador de recursos de Azure" 
	description="Utilice Azure PowerShell para implementar varios recursos como un grupo de recursos en Azure." 
	services="azure-resource-manager" 
	documentationCenter="" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="azure-resource-manager" 
	ms.workload="multiple" 
	ms.tgt_pltfrm="powershell" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/15/2015" 
	ms.author="tomfitz"/>

# Uso de Azure PowerShell con Administrador de recursos de Azure

> [AZURE.SELECTOR]
- [Azure PowerShell](powershell-azure-resource-manager.md)
- [Azure CLI](xplat-cli-azure-resource-manager.md)

Administrador de recursos de Azure presenta un concepto totalmente nuevo acerca de los recursos de Azure. En lugar de crear y administrar recursos individuales, vamos a empezar a pensar en un servicio complejo, como un blog, una galería de fotos, un portal de SharePoint o un wiki. Vamos a usar una plantilla, es decir, un modelo de recursos del servicio, para crear un grupo de recursos con los recursos que necesita para admitir el servicio. A continuación, puede administrar e implementar dicho grupo de recursos como una unidad lógica.

En este tutorial, se ofrece información acerca de cómo usar Azure PowerShell con Administrador de recursos de Azure para Microsoft Azure. Se explica el proceso de creación e implementación de un grupo de recursos para una aplicación web hospedada en Azure con una base de datos SQL, con todos los recursos que necesita para su funcionamiento.

## Requisitos previos

Para realizar este tutorial, debe tener Azure PowerShell versión 0.8.0 o posterior. Para instalar la última versión y asociarla a la suscripción de Azure, consulte [Instalación y configuración de Azure PowerShell](powershell-install-configure.md).

Este tutorial está diseñado para los principiantes de PowerShell, pero se asume que se conocen los conceptos básicos, como los módulos, los cmdlets y las sesiones. Para obtener más información acerca de Windows PowerShell, consulte [Introducción a Windows PowerShell](http://technet.microsoft.com/library/hh857337.aspx).

Para obtener ayuda detallada con cualquier cmdlet que aparezca en este tutorial, use el cmdlet Get-Help.

	Get-Help <cmdlet-name> -Detailed

Por ejemplo, para obtener ayuda para el cmdlet Add-AzureAccount, escriba:

	Get-Help Add-AzureAccount -Detailed

## Acerca de los módulos de Azure Powershell
A partir de la versión 0.8.0, la instalación de Azure PowerShell incluye más de un módulo de PowerShell. Debe decidir explícitamente si se va a utilizar los comandos que están disponibles en el módulo de Azure o en el módulo Administrador de recursos de Azure. Para facilitar el cambio entre ellos, hemos agregado un cmdlet nuevo, **Switch-AzureMode**, al módulo del perfil de Azure.

Cuando usa Azure PowerShell, los cmdlets del módulo de Azure se importan de modo predeterminado. Para cambiar al módulo del Administrador de recursos de Azure, use el cmdlet Switch-AzureMode. Quita el módulo de Azure de la sesión e importa los módulos de administrador de recursos de Azure y de perfiles de Azure.

Para cambiar al módulo AzureResoureManager, escriba:

    PS C:> Switch-AzureMode -Name AzureResourceManager

Para volver al módulo de Azure, escriba:

    PS C:> Switch-AzureMode -Name AzureServiceManagement

De manera predeterminada, Switch-AzureMode afecta solo a la sesión actual. Para que el cambio se aplique a todas las sesiones de PowerShell, use el parámetro **Global** de Switch-AzureMode.

Para obtener ayuda con el cmdlet Switch-AzureMode, escriba: `Get-Help Switch-AzureMode` o consulte [Switch-AzureMode](http://go.microsoft.com/fwlink/?LinkID=394398).
  
Para obtener una lista de los cmdlets del módulo AzureResourceManager con una sinopsis de ayuda, escriba:

    PS C:> Get-Command -Module AzureResourceManager | Get-Help | Format-Table Name, Synopsis

El resultado será similar al siguiente extracto:

	Name                                   Synopsis
	----                                   --------
	Add-AlertRule                          Adds or updates an alert rule of either metric, event, o...
	Add-AzureAccount                       Adds the Azure account to Windows PowerShell
	Add-AzureEnvironment                   Creates an Azure environment
	Add-AzureKeyVaultKey                   Creates a key in a vault or imports a key into a vault.
        ...

Para obtener toda la ayuda posible para un cmdlet, escriba un comando con el formato:

	Get-Help <cmdlet-name> -Full

Por ejemplo,

	Get-Help Get-AzureLocation -Full

Para obtener el conjunto completo de comandos de Administrador de recursos de Azure, consulte [Cmdlets de Administrador de recursos de Azure](http://go.microsoft.com/fwlink/?LinkID=394765).
  
## Crear un grupo de recursos

En esta sección del tutorial se explica el proceso de creación e implementación de un grupo de recursos para una aplicación web con una base de datos SQL.

Para realizar esta tarea no es necesario que sea experto en Azure, SQL, aplicaciones web o administración de recursos. Las plantillas ofrecen un modelo del grupo de recursos con todos los recursos que puede necesitar. Además, como vamos a usar Windows PowerShell para automatizar las tareas, puede usar estos procesos como modelo para el scripting de tareas a gran escala.

### Paso 1: Cambio a Administrador de recursos de Azure 
1. Inicie PowerShell. Puede usar el programa host que prefiera, como la consola de Azure PowerShell o Windows PowerShell ISE.

2. Use el cmdlet **Switch-AzureMode** para importar los cmdlets en los módulos AzureResourceManager y AzureProfile.

        PS C:> Switch-AzureMode AzureResourceManager

3. Para agregar la cuenta de Azure a la sesión de Windows PowerShell, use el cmdlet **Add-AzureAccount**.

        PS C:> Add-AzureAccount

El cmdlet pide las credenciales de inicio de sesión para la cuenta de Azure. Una vez iniciada la sesión, descarga la configuración de la cuenta a fin de que esté disponible para Windows PowerShell.

La configuración de la cuenta caduca, por lo que necesita actualizarla ocasionalmente. Para actualizarla, vuelva a ejecutar **Add-AzureAccount**.

>[AZURE.NOTE]El módulo AzureResourceManager necesita Add-AzureAccount. No basta con un archivo de configuración de publicación.

### Paso 2: Selección de una plantilla de la galería

Hay varias formas de crear un grupo de recursos y sus recursos, pero la más sencilla consiste en usar la plantilla de un grupo de recursos. La *plantilla de grupo de recursos* es una cadena JSON que define los recursos de un grupo de recursos. La cadena incluye marcadores de posición denominados "parámetros" para los valores definidos por el usuario, como nombres y tamaños.

Azure hospeda una galería de plantillas de grupo de recursos, aunque puede crear plantillas personalizadas, bien desde cero o con la edición de una plantilla de la galería. En este tutorial, vamos a usar una plantilla de la galería.

Para ver todas las plantillas en la galería de plantillas de grupo de recursos de Azure, use el cmdlet **Get-AzureResourceGroupGalleryTemplate**; sin embargo, este comando devuelve un gran número de plantillas. Para ver un número más manejable de plantillas, especifique un parámetro de publicador.

En el símbolo de sistema de PowerShell, escriba:
    
    PS C:> Get-AzureResourceGroupGalleryTemplate -Publisher Microsoft

El cmdlet devuelve una lista de plantillas de galería con Microsoft como publicador. Use la propiedad **Identity** para identificar la plantilla en los comandos.

La plantilla Microsoft.WebSiteSQLDatabase.0.2.6-preview parece interesante. Al ejecutar el comando, la versión de la plantilla puede ser ligeramente diferente porque se ha lanzado una nueva versión. Use la versión más reciente de la plantilla. Para obtener más información acerca de una plantilla de galería, use el parámetro **Identity**. El valor del parámetro Identity es la identidad de la plantilla.

    PS C:> Get-AzureResourceGroupGalleryTemplate -Identity Microsoft.WebSiteSQLDatabase.0.2.6-preview

El cmdlet devuelve un objeto con mucha más información acerca de la plantilla, incluidos un resumen y una descripción.

Parece que esta plantilla cubrirá nuestras necesidades. Vamos a guardarla en el disco y a analizarla más detenidamente.

### Paso 3: Examen de la plantilla

Vamos a guardar la plantilla en el disco como un archivo JSON. Este paso no es obligatorio, pero facilita la visualización de la plantilla. Para guardarla, use el cmdlet **Save-AzureResourceGroupGalleryTemplate**. Use su parámetro **Identity** para especificar la plantilla y el parámetro **Path** para definir una ruta de acceso en el disco.

Save-AzureResourceGroupGalleryTemplate guarda la plantilla y devuelve la ruta de acceso al nombre de archivo del archivo de la plantilla JSON.

	PS C:> Save-AzureResourceGroupGalleryTemplate -Identity Microsoft.WebSiteSQLDatabase.0.2.6-preview -Path C:\Azure\Templates\New_WebSite_And_Database.json

	Path
	----
	C:\Azure\Templates\New_WebSite_And_Database.json


Puede ver el archivo de plantilla en un editor de texto, como el Bloc de notas. Cada plantilla tiene una sección **parameters** y una sección **resources**.

La sección **parameters** de la plantilla es una recopilación de los parámetros definidos en todos los recursos. Incluye valores de propiedad que se pueden proporcionar al configurar el grupo de recursos.

    "parameters": {
      "siteName": {
        "type": "string"
      },
      "hostingPlanName": {
        "type": "string"
      },
      "siteLocation": {
        "type": "string"
      },
      ...
    }

Algunos parámetros tienen un valor predeterminado. Al usar la plantilla, no es necesario que facilite valores para estos parámetros. Si no especifica un valor, se usa el predeterminado.

    "collation": {
      "type": "string",
      "defaultValue": "SQL_Latin1_General_CP1_CI_AS"
    },

Cuando los parámetros tienen valores enumerados, los valores válidos se muestran con el parámetro. Por ejemplo, el parámetro **sku** puede tener los valores Free, Shared, Basic o Standard. Si no especifica ningún valor para el parámetro **sku**, se usa el valor predeterminado, Free.

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


Tenga en cuenta que el parámetro **administratorLoginPassword** usa una cadena segura, no texto sin formato. Al facilitar un valor para una cadena protegida, el valor se oscurece.

	"administratorLoginPassword": {
      "type": "securestring"
    },

En la sección **resources** de la plantilla se enumeran los recursos que la plantilla crea. Esta plantilla crea un servidor de base de datos SQL y una base de datos SQL, una granja de servidores y un sitio web, y varias configuraciones de administración.
  
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
          "edition": "[parameters('edition')]",
          "collation": "[parameters('collation')]",
          "maxSizeBytes": "[parameters('maxSizeBytes')]",
          "requestedServiceObjectiveId": "[parameters('requestedServiceObjectiveId')]"
        }
    },


Ya estamos casi listos para usar la plantilla, pero antes de hacerlo, necesitamos encontrar las ubicaciones de cada uno de los recursos.

### Paso 4: Obtención de las ubicaciones de los tipos de recursos

La mayoría de las plantillas le piden que especifique la ubicación de cada uno de los recursos de un grupo de recursos. Cada recurso se encuentra en un centro de datos de Azure, pero no todos los centros de datos de Azure admiten todos los tipos de recursos.

Seleccione alguna ubicación que admita el tipo de recurso. No es necesario que cree todos los recursos de un grupo de recursos en la misma ubicación; sin embargo, siempre que sea posible, será recomendable crearlos en la misma ubicación para optimizar el rendimiento. En concreto, será conveniente asegurarse de que la base de datos está en la misma ubicación que la aplicación que accede a ella.

Para obtener las ubicaciones que admiten cada tipo de recurso, use el cmdlet **Get-AzureLocation**. Para limitar los resultados a un tipo específico de recurso, como ResourceGroup, utilice:

    Get-AzureLocation | Where-Object Name -eq "ResourceGroup" | Format-Table Name, LocationsString -Wrap

El resultado será similar al siguiente:

    Name                                 LocationsString
    ----                                 ---------------
    ResourceGroup                        East Asia, South East Asia, East US, West US, North
                                         Central US, South Central US, Central US, North Europe,
                                         West Europe

Ahora ya disponemos de la información que necesitamos para crear el grupo de recursos.

### Paso 5: Creación de un grupo de recursos
 
En este paso, vamos a usar la plantilla del grupo de recursos para crear el grupo de recursos. Como referencia, abra el archivo New\_WebSite\_And\_Database.json en el disco y continúe. El archivo de plantilla puede ser muy útil para determinar los valores de parámetro que se deben pasar, por ejemplo el valor ApiVersion correcto para un recurso.

Para crear un grupo de recursos, use el cmdlet **New-AzureResourceGroup**.

El comando usa el parámetro **Name** para especificar un nombre para el grupo de recursos y el parámetro **Location** para definir su ubicación. Use la salida de **Get-AzureLocation** para seleccionar la ubicación del grupo de recursos. Usa el parámetro **GalleryTemplateIdentity** para especificar la plantilla de la galería.

	PS C:> New-AzureResourceGroup -Name TestRG1 -Location "East Asia" -GalleryTemplateIdentity Microsoft.WebSiteSQLDatabase.0.2.6-preview
            ....

En cuanto escriba el nombre de la plantilla, New-AzureResourceGroup captura la plantilla, la analiza y agrega los parámetros de la plantilla al comando de manera dinámica. De esta forma, resulta muy fácil especificar los valores de los parámetros de la plantilla. Además, si olvida el valor de algún parámetro necesario, Windows PowerShell le pide dicho valor.

**Parámetros dinámicos de la plantilla**

Para obtener los parámetros, escriba el signo menos (-) para indicar el nombre de un parámetro y, a continuación, presione la tecla TAB. También puede escribir las primera letras del nombre de un parámetro, como siteName, y, a continuación, presionar la tecla TAB.

    PS C:> New-AzureResourceGroup -Name TestRG1 -Location "East Asia" -GalleryTemplateIdentity Microsoft.WebSiteSQLDatabase.0.2.6-preview -si<TAB>

PowerShell completa el nombre del parámetro. Para alternar entre los nombres de parámetros, presione la tecla TAB varias veces.

    PS C:> New-AzureResourceGroup -Name TestRG1 -Location "East Asia" -GalleryTemplateIdentity Microsoft.WebSiteSQLDatabase.0.2.6-preview -siteName 

Escriba un nombre para el sitio web y repita el proceso con TAB para cada uno de los parámetros. Los parámetros con un valor predeterminado son opcionales. Para aceptar el valor predeterminado, omita el parámetro del comando.

Si el parámetro de una plantilla tiene valores enumerados, como el parámetro sku de esta plantilla, para alternar entre los valores del parámetro, presione la tecla TAB.

    PS C:> New-AzureResourceGroup -Name TestRG1 -Location "East Asia" -GalleryTemplateIdentity Microsoft.WebSiteSQLDatabase.0.2.6-preview -siteName TestSite -sku <TAB>

    PS C:> New-AzureResourceGroup -Name TestRG1 -Location "East Asia" -GalleryTemplateIdentity Microsoft.WebSiteSQLDatabase.0.2.6-preview -siteName TestSite -sku Basic<TAB>

    PS C:> New-AzureResourceGroup -Name TestRG1 -Location "East Asia" -GalleryTemplateIdentity Microsoft.WebSiteSQLDatabase.0.2.6-preview -siteName TestSite -sku Free<TAB>

A continuación, se muestra un ejemplo de un comando New-AzureResourceGroup que especifica solo los parámetros de plantilla necesarios y el parámetro común **Verbose**. Observe que **administratorLoginPassword** se omite.

	PS C:> New-AzureResourceGroup -Name TestRG -Location "East Asia" -GalleryTemplateIdentity Microsoft.WebSiteSQLDatabase.0.2.6-preview -siteName TestSite -hostingPlanName TestPlan -siteLocation "East Asia" -serverName testserver -serverLocation "East Asia" -administratorLogin Admin01 -databaseName TestDB -Verbose

Al escribir el comando, se le pide el parámetro obligatorio que falta, que es **administratorLoginPassword**. Y, al escribir la contraseña, el valor de cadena segura se oscurece. Esta estrategia elimina el riesgo de ofrecer una contraseña en texto sin formato.

	cmdlet New-AzureResourceGroup at command pipeline position 1
	Supply values for the following parameters:
	(Type !? for Help.)
	administratorLoginPassword: **********

**New-AzureResourcGroup** devuelve el grupo de recursos que ha creado e implementado.

Con tan solo unos pasos, hemos creado e implementado los recursos necesarios para un sitio web complejo. La plantilla de la galería ha ofrecido casi toda la información que necesitábamos para esta tarea. Además, la tarea se automatiza con facilidad.

## Obtención de información acerca de los grupos de recursos

Después de crear un grupo de recursos, puede usar los cmdlets del módulo AzureResourceManager para administrar los grupos de recursos.

- Para obtener todos los grupos de recursos de la suscripción, use el cmdlet **Get-AzureResourceGroup**:

		PS C:>Get-AzureResourceGroup

		ResourceGroupName : TestRG
		Location          : eastasia
		ProvisioningState : Succeeded
		Tags              :
		ResourceId        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG
		
		...

- Para obtener los recursos del grupo de recursos, use el cmdlet **GetAzureResource** y su parámetro ResourceGroupName. Sin los parámetros, Get-AzureResource obtiene todos los recursos de la suscripción de Azure.

		PS C:> Get-AzureResource -ResourceGroupName TestRG
		
		ResourceGroupName : TestRG
		Location          : eastasia
		ProvisioningState : Succeeded
		Tags              :
		
		Resources         :
				Name                   Type                          Location
				----                   ------------                  --------
				ServerErrors-TestSite  microsoft.insights/alertrules         eastasia
	        	TestPlan-TestRG        microsoft.insights/autoscalesettings  eastus
	        	TestSite               microsoft.insights/components         centralus
	         	testserver             Microsoft.Sql/servers                 eastasia
	        	TestDB                 Microsoft.Sql/servers/databases       eastasia
	        	TestPlan               Microsoft.Web/serverFarms             eastasia
	        	TestSite               Microsoft.Web/sites                   eastasia
		ResourceId        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG

## Incorporación a un grupo de recursos

- Para agregar un recurso al grupo de recursos, use el cmdlet **New-AzureResource**. Este comando agrega un sitio web nuevo al grupo de recursos TestRG. Este comando es algo más complejo, porque no usa ninguna plantilla. 

        PS C:>New-AzureResource -Name TestSite2 -Location "North Europe" -ResourceGroupName TestRG -ResourceType "Microsoft.Web/sites" -ApiVersion 2014-06-01 -PropertyObject @{"name" = "TestSite2"; "siteMode"= "Limited"; "computeMode" = "Shared"}

- Para agregar una implementación nueva basada en plantilla al grupo de recursos, use el comando **New-AzureResourceGroupDeployment**.

		PS C:>New-AzureResourceGroupDeployment ` 
		-ResourceGroupName TestRG `
		-GalleryTemplateIdentity Microsoft.WebSite.0.2.6-preview `
		-siteName TestWeb2 `
		-hostingPlanName TestDeploy2 `
		-siteLocation "North Europe" 

## Movimiento de un recurso

Puede mover recursos existentes a un nuevo grupo de recursos. Para obtener ejemplos, vea [Movimiento de recursos a un grupo de recursos o una suscripción nuevos](resource-group-move-resources.md).

## Eliminación de un grupo de recursos

- Para eliminar un recurso del grupo de recursos, use el cmdlet **Remove-AzureResource**. Este cmdlet elimina el recurso, pero no el grupo de recursos.

	Este comando quita el sitio web TestSite2 del grupo de recursos TestRG.

		Remove-AzureResource -Name TestSite2 -ResourceGroupName TestRG -ResourceType "Microsoft.Web/sites" -ApiVersion 2014-06-01

- Para eliminar un grupo de recursos, use el cmdlet **Remove-AzureResourceGroup**. Este cmdlet elimina el grupo de recursos y sus recursos.

		PS C:\ps-test> Remove-AzureResourceGroup -Name TestRG
		
		Confirm
		Are you sure you want to remove resource group 'TestRG'
		[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y


## Solución de problemas de un grupo de recursos
A medida que experimente con los cmdlets en los módulos AzureResourceManager, es posible que se produzcan errores. Use las sugerencias de esta sección para resolverlos.

### Prevención de errores

El módulo AzureResourceManager incluye cmdlets que le ayudan a prevenir errores.


- **Get-AzureLocation**: este cmdlet obtiene las ubicaciones que admiten cada tipo de recurso. Antes de especificar una ubicación para un recurso, use este cmdlet para comprobar si la ubicación admite el tipo de recurso.


- **Test-AzureResourceGroupTemplate**: pruebe la plantilla y el parámetro de plantilla antes de utilizarlos. Especifique una plantilla personalizada o de la galería y los valores de los parámetros de la plantilla que pretende usar. Este cmdlet prueba si la plantilla es coherente internamente y si el conjunto de valores de los parámetros coincide con la plantilla.



### Corrección de errores

- **Get-AzureResourceGroupLog**: este cmdlet obtiene las entradas del registro para cada implementación del grupo de recursos. Si algo va mal, empiece por examinar los registros de implementación. 

- **Verbose y Debug**: los cmdlets del módulo AzureResourceManager llaman a las API de REST que hacen el trabajo real. Para ver los mensajes que las API devuelven, defina la variable $DebugPreference en "Continue" y use el parámetro común Verbose en los comandos. Los mensajes suelen ofrecer pistas fundamentales sobre la causa de cualquier error.

- **Las credenciales de Azure no se han configurado o han caducado**: para actualizar las credenciales de la sesión de Windows PowerShell, use el cmdlet Add-AzureAccount. Las credenciales de un archivo de configuración de publicación no son suficientes para los cmdlets del módulo AzureResourceManager.


## Pasos siguientes
Introducción

- [Información general del Administrador de recursos de Azure](./resource-group-overview.md)
- [Uso de la interfaz de la línea de comandos entre plataformas de Azure con el Administrador de recursos de Azure](./xplat-cli-azure-resource-manager.md)
- [Uso del Portal de Azure para administrar los recursos de Azure](./resource-group-portal.md)

Creación e implementación de aplicaciones

- [Creación de plantillas del Administrador de recursos de Azure](./resource-group-authoring-templates.md)
- [Implementación de una aplicación con la plantilla del Administrador de recursos de Azure](./resource-group-template-deploy.md)
- [Implementación predecible de una aplicación compleja en Azure](app-service-web/app-service-deploy-complex-application-predictably.md)
- [Solución de problemas de implementaciones de grupo de recursos en Azure](./resource-group-deploy-debug.md)
- [Funciones de la plantilla del Administrador de recursos de Azure](./resource-group-template-functions.md)
- [Operaciones avanzadas de plantilla](./resource-group-advanced-template.md)

Organización de los recursos

- [Uso de etiquetas para organizar los recursos de Azure](./resource-group-using-tags.md)

Administración y auditoría del acceso

- [Administración y auditoría del acceso a los recursos](./resource-group-rbac.md)
- [Autenticación de una entidad de servicio con el Administrador de recursos de Azure](./resource-group-authenticate-service-principal.md)
- [Creación de una nueva entidad de servicio de Azure mediante el portal clásico de Azure](./resource-group-create-service-principal-portal.md)

<!---HONumber=July15_HO5-->