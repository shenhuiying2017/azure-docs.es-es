<properties
   pageTitle="Entornos de desarrollo y pruebas | Microsoft Azure"
   description="Obtenga información acerca de cómo usar las plantillas del Administrador de recursos de Azure para crear y eliminar de manera rápida y coherente entornos de desarrollo y pruebas."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="jimdial"
   manager="carolz"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/08/2015"
   ms.author="jdial"/>

# Entornos de desarrollo y pruebas en Microsoft Azure

Las aplicaciones personalizadas se implementan normalmente en varios entornos de desarrollo y pruebas antes de hacerlo en producción. Cuando se crean entornos en local, se adquieren o se asignan recursos informáticos para cada entorno para cada aplicación. Los entornos a menudo incluyen varias máquinas físicas o virtuales con configuraciones específicas que se implementan manualmente o con scripts de automatización complejos. Las implementaciones suelen tardar horas y generar configuraciones incoherentes entre entornos.

## Escenario ##

Al aprovisionar los entornos de desarrollo y pruebas en Microsoft Azure, solo paga por los recursos que usa. En este artículo se explica cómo puede crear, mantener y eliminar de forma rápida y coherente entornos de desarrollo y pruebas mediante el uso de plantillas y archivos de parámetros del Administrador de recursos de Azure, tal como se muestra a continuación.

![Escenario](./media/solution-dev-test-environments-preview-portal/scenario.png)

Arriba se muestran tres entornos de desarrollo y pruebas. Cada uno tiene una aplicación web y una base de datos SQL. Los nombres de la aplicación y de la base de datos de cada entorno son diferentes. En este artículo se explica cómo puede usar una plantilla para implementar el mismo recurso en todos los entornos y usar archivos de parámetros únicos para especificar configuraciones distintas para los recursos en los entornos.

Si no está familiarizado con los conceptos del Administrador de recursos de Azure, se recomienda que lea el artículo [Información general sobre el Administrador de recursos de Azure](resource-group-overview.md) antes de leer este artículo.

Quizás prefiera seguir primero los pasos de este artículo sin leer ninguno de los artículos a los que se hace referencia para obtener rápidamente algo de experiencia con las plantillas del Administrador de recursos de Azure. Sin duda se le plantearán muchas preguntas mientras recorre los pasos. Pero después de haberlos realizado una vez, haber experimentado con ellos y haber leído los artículos de referencia, obtendrá respuestas a la mayoría de las preguntas.

## Planeación del uso de recursos de Azure
Una vez que disponga de un diseño de alto nivel de la aplicación, puede definir:

- Qué recursos de Azure incluirá su aplicación. Puede compilar la aplicación e implementarla como una aplicación web de Azure con una base de datos SQL de Azure. Puede compilar la aplicación en máquinas virtuales con PHP y MySQL o IIS y SQL Server, u otros componentes. El artículo [Comparación entre el Servicio de aplicaciones de Azure, Servicios en la nube y Máquinas virtuales](app-service-web/choose-web-site-cloud-service-vm.md) le ayuda a decidir qué recursos de Azure podría usar para su aplicación.
- Qué requisitos de nivel de servicio, como disponibilidad, seguridad y escalabilidad, cumplirá la aplicación.

## Uso de plantillas del Administrador de recursos de Azure
Una plantilla del Administrador de recursos de Azure define todos los recursos de Azure que usa la aplicación. Existen varias plantillas que puede implementar directamente en el Portal de vista previa de Azure, o bien descargar, modificar y guardar en un sistema de control de código fuente con el código de aplicación. Lo más probable es que exista una plantilla que incluya los recursos que quiere usar con su aplicación. Puede encontrar una lista de plantillas disponibles en el repositorio de GitHub de [plantillas de inicio rápido de Azure](https://github.com/Azure/azure-quickstart-templates/).

En la lista, verá una carpeta "[201-web-app-sql-database](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-sql-database)". Puesto que muchas aplicaciones personalizadas incluyen una aplicación web y una base de datos SQL, esta plantilla se usa como ejemplo en el resto de este artículo para ayudarle a entender cómo usar las plantillas. La explicación completa de todo lo que esta plantilla crea y configura queda fuera del ámbito de este artículo, pero si tiene la intención de usarla para crear entornos reales en su organización, querrá saber todo sobre ella, lo que puede conseguir leyendo el artículo [Aprovisionamiento de una aplicación web con una base de datos SQL](app-service-web/app-service-web-arm-with-sql-database-provision.md).

> [AZURE.NOTE]Puede implementar la plantilla en Azure directamente haciendo clic en el botón "Implementar en Azure" en el artículo [Aprovisionamiento de una aplicación web con una base de datos SQL](http://azure.microsoft.com/documentation/templates/201-web-app-sql-database/). Sin embargo, esto solo sirve de ayuda para obtener información sobre las plantillas, pero no para editar los valores de plantilla y parámetro, ni controlar su versión o guardarlos con el código de aplicación. En este artículo se explica cómo puede guardar y controlar la versión de los valores de plantilla y parámetro con el código de aplicación.

  **Paso 1:** Vea el contenido del archivo [azuredeploy.json](https://github.com/Azure/azure-quickstart-templates/blob/master/201-web-app-sql-database/azuredeploy.json) en la carpeta 201-web-app-sql-database. Este es el archivo de plantilla del Administrador de recursos de Azure. En el modo de vista, haga clic en el botón "[Sin formato](https://github.com/Azure/azure-quickstart-templates/raw/master/201-web-app-sql-database/azuredeploy.json)". Con el mouse, seleccione todo el contenido de este archivo y guárdelo en el equipo como un archivo llamado "TestApp1-Template.json".

En el archivo de plantilla, verá una sección "recursos", que define los recursos de Azure creados por esta plantilla. Entre otros tipos de recursos, esta plantilla crea recursos de [aplicación web de Azure](app-service-web/app-service-web-overview.md) y de [base de datos SQL de Azure](sql-database/sql-database-technical-overview.md).

También verá una sección "parámetros", que define los parámetros con los que se puede configurar cada recurso. Algunos de los parámetros especificados en la plantilla tienen propiedades "defaultValue", mientras que otros no. Al implementar recursos de Azure con una plantilla, debe proporcionar valores para todos los parámetros que no tengan propiedades defaultValue. Si no proporciona valores para los parámetros con propiedades defaultValue, se usará el valor especificado para el parámetro defaultValue en la plantilla.

Una plantilla define qué recursos de Azure se crean y los parámetros con los que se puede configurar cada recurso. Normalmente preferirá crear los mismos recursos en cada entorno de desarrollo y pruebas. Puede obtener más información sobre las plantillas y cómo diseñar las suyas propias en el artículo [Procedimientos recomendados para diseñar plantillas del Administrador de recursos de Azure](best-practices-resource-manager-design-templates.md).

## Creación de archivos de parámetros

Lo más probable es que prefiera crear los mismos recursos de Azure en cada entorno, pero que la configuración de los recursos sea distinta en cada uno. Y aquí es donde entran en juego los archivos de parámetros.

  **Paso 2:** Vea el contenido del archivo [azuredeploy-parameters.json](https://github.com/Azure/azure-quickstart-templates/blob/master/201-web-app-sql-database/azuredeploy-parameters.json) de la carpeta 201-web-app-sql-database. Este es el archivo de parámetros del archivo de plantilla que guardó en el paso 1. En el modo de vista, haga clic en el botón "[Sin formato](https://github.com/Azure/azure-quickstart-templates/raw/master/201-web-app-sql-database/azuredeploy-parameters.json)". Con el mouse, seleccione todo el contenido de este archivo y guárdelo en tres archivos distintos en el equipo con los siguientes nombres:

- TestApp1-Parameters-Development.json
- TestApp1-Parameters-Test.json
- TestApp1-Parameters-Pre-Production.json

  **Paso 3:** Mediante cualquier editor de texto o de JSON, edite los archivos de parámetros del entorno de desarrollo y pruebas que creó en el paso 2 sustituyendo los valores existentes en los archivos por los siguientes valores.

  --TestApp1-Parameters-Development.json--

| Parámetro | Descripción |
|---|---|
| **siteName** | TestApp1Dev |
| **hostingPlanName** | TestApp1PlanDev |
| **siteLocation** | Central EE. UU.: |
| **serverName** | testapp1dev |
| **serverLocation** | Central EE. UU.: |
| **administratorLogin** | testapp1Admin |
| **administratorLoginPassword** | #testapp1XYZ |
| **databaseName** | testapp1dev |

--TestApp1-Parameters-Test.json--

| Parámetro | Descripción |
|---|---|
| **siteName** | TestApp1Test |
| **hostingPlanName** | TestApp1PlanTest |
| **siteLocation** | Central EE. UU.: |
| **serverName** | testapp1test |
| **serverLocation** | Central EE. UU.: |
| **administratorLogin** | testapp1Admin |
| **administratorLoginPassword** | #testapp1XYZ |
| **databaseName** | testapp1test |

En el paso 6, estos archivos de parámetros se usarán para crear configuraciones únicas para los recursos de aplicación web de Azure y base de datos SQL de Azure en los entornos de desarrollo y pruebas.

 **Paso 4:** Edite el archivo de parámetros TestApp1-Parameters-Pre-Production.json que creó en el paso 2. Sustituya todo el contenido del archivo por lo siguiente:

	{
	  "$schema" : "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
	  "contentVersion" : "1.0.0.0",
	  "parameters" : {
	    "administratorLogin" : {
	      "value" : "testApp1Admin"
	    },
	    "administratorLoginPassword" : {
	      "value" : "testApp1XP49"
	    },
	    "databaseName" : {
	      "value" : "testapp1preprod"
	    },
	    "hostingPlanName" : {
	      "value" : "TestApp1PlanPreProd"
	    },
	    "serverLocation" : {
	      "value" : "Central US"
	    },
	    "serverName" : {
	      "value" : "testapp1preprod"
	    },
	    "siteLocation" : {
	      "value" : "Central US"
	    },
	    "siteName" : {
	      "value" : "TestApp1PreProd"
	    },
	    "sku" : {
	      "value" : "Standard"
	    },
		"requestedServiceObjectiveName" : {
		  "value" : "S1"
	    }
	  }
	}

En el archivo de parámetros de preproducción anterior, se *agregaron* los parámetros **sku** y **requestedServiceObjectiveName**. Estos parámetros no se agregaron a los archivos de parámetros de desarrollo y pruebas. Esto se debe a que hay valores predeterminados especificados para estos parámetros en la plantilla y, en los entornos de desarrollo y pruebas, se usan los valores predeterminados, pero en el entorno de preproducción se usan valores no predeterminados para estos parámetros.

La razón de usar valores no predeterminados para estos parámetros en el entorno de preproducción es probar valores para estos parámetros que podrían ser preferibles para su entorno de producción. Estos parámetros se relacionan con los [planes de hospedaje de aplicaciones web](http://azure.microsoft.com/pricing/details/app-service/) de Azure, o **sku**, y con la [base de datos SQL](http://azure.microsoft.com/pricing/details/sql-database/) de Azure, o **requestedServiceObjectiveName** que usa la aplicación. Diferentes SKU y nombres de objetivo de servicio tiene distintos costos y características y admiten diferentes métricas de nivel de servicio.

En la siguiente tabla se muestran los valores predeterminados de estos parámetros especificados en la plantilla y los valores que se usan en lugar de los valores predeterminados en el archivo de parámetros de preproducción.

| Parámetro | Valor predeterminado | Valor de archivo de parámetros |
|---|---|---|
| **sku** | Gratuito | Estándar |
| **requestedServiceObjectiveName** | S0 | S1 |

## Creación de entornos
Todos los recursos de Azure deben crearse dentro de un [grupo de recursos de Azure](azure-portal/resource-group-portal.md). Los grupos de recursos le permiten agrupar recursos de Azure y así administrarlos de forma colectiva. Se pueden asignar [permisos](azure-portal/resource-group-rbac.md) a grupos de recursos para que personas específicas dentro de su organización puedan crearlos, modificarlos, eliminarlos o verlos, junto con los recursos que contienen. Se pueden ver alertas e información de facturación de los recursos del grupo de recursos en el [Portal de vista previa de Azure](https://portal.azure.com). Los grupos de recursos se crean en una [ubicación](http://azure.microsoft.com/regions/) de Azure. En este artículo, todos los recursos se crean en la ubicación Centro de EE. UU. Cuando empiece a crear entornos reales, elegirá la ubicación que mejor cumpla sus requisitos.

  **Paso 5:** Creación de grupos de recursos para los entornos de desarrollo y pruebas mediante uno de los métodos siguientes. Con ambos métodos se obtiene exactamente el mismo resultado.

  **Método 1:** Interfaz de línea de comandos (CLI)

  Asegúrese de que tiene [instalado](xplat-cli-install.md) la CLI en un equipo Windows, OS X o Linux y de que ha [conectado](xplat-cli-connect.md) su Id. de organización a la suscripción de Azure. En la línea de comandos CLI, escriba el comando siguiente para crear el grupo de recursos del entorno de desarrollo.

	azure group create "TestApp1-Development" "Central US"

  El comando devolverá el siguiente resultado si se ejecuta correctamente:

	info:    Executing command group create
	+ Getting resource group TestApp1-Development
	+ Creating resource group TestApp1-Development
	info:    Created resource group TestApp1-Development
	data:    Id:                  /subscriptions/uuuuuuuu-vvvv-wwww-xxxx-yyyy-zzzzzzzzzzzz/resourceGroups/TestApp1-Development
	data:    Name:                TestApp1-Development
	data:    Location:            centralus
	data:    Provisioning State:  Succeeded
	data:    Tags: null
	data:
	info:    group create command OK

  Para crear el grupo de recursos del entorno de prueba, escriba el comando siguiente:

	azure group create "TestApp1-Test" "Central US"

  Para crear el grupo de recursos del entorno de preproducción, escriba el comando siguiente:

	azure group create "TestApp1-Pre-Production" "Central US"

  **Método 2:** PowerShell

  Asegúrese de que tiene PowerShell instalado en un equipo Windows y que está conectado a su suscripción como se detalla en el artículo [Instalación y configuración de Azure PowerShell](powershell-install-configure.md). En el símbolo del sistema de PowerShell, escriba el comando siguiente para crear el grupo de recursos del entorno de desarrollo. Si está utilizando la versión de vista previa de Azure PowerShell 1.0, el comando es **New-AzureRmResourceGroup** tal como se muestra a continuación. Si está utilizando una versión de Azure PowerShell anterior a la versión de vista previa 1.0, el comando es **New-AzureResourceGroup**.

	New-AzureRmResourceGroup -Name TestApp1-Development -Location "Central US"

  El comando devolverá el siguiente resultado si se ejecuta correctamente:

	WARNING: The output object of this cmdlet will be modified in a future release.


	ResourceGroupName : TestApp1-Development
	Location          : centralus
	ProvisioningState : Succeeded
	Tags              : 
	Permissions       : 
	                    Actions  NotActions
	                    =======  ==========
	                    *                  
	                    
	ResourceId        : /subscriptions/uuuuuuuu-vvvv-wwww-xxxx-yyyy-zzzzzzzzzzzz/resourceGroups/TestApp1-Development

  Para crear el grupo de recursos del entorno de prueba, escriba el comando siguiente:

	New-AzureRmResourceGroup -Name TestApp1-Test -Location "Central US"

  Para crear el grupo de recursos del entorno de preproducción, escriba el comando siguiente:

	New-AzureRmResourceGroup -Name TestApp1-Pre-Production -Location "Central US"

 **Paso 6:** Implementación de los recursos de Azure en grupos de recursos para cada entorno mediante el archivo de plantilla de la aplicación y los archivos de parámetros de cada entorno con uno de estos métodos. Con ambos métodos se obtiene exactamente el mismo resultado.

  **Método 1:** Interfaz de línea de comandos de Azure (CLI)

  En la línea de comandos CLI, escriba el comando siguiente para implementar recursos en el grupo de recursos que creó para el entorno de desarrollo, y sustituya [path] por la ruta de acceso a los archivos guardados en los pasos anteriores.

	azure group deployment create -g TestApp1-Development -n Deployment1 -f [path]TestApp1-Template.json -e [path]TestApp1-Parameters-Development.json 

  El comando devolverá el siguiente resultado si se ejecuta correctamente:

	info:    Executing command group deployment create
	+ Initializing template configurations and parameters
	+ Creating a deployment
	info:    Created template deployment "Deployment1"
	+ Waiting for deployment to complete
	data:    DeploymentName     : Deployment1
	data:    ResourceGroupName  : TestApp1-Development
	data:    ProvisioningState  : Succeeded
	data:    Timestamp          : XXXX-XX-XXT20:20:23.5202316Z
	data:    Mode               : Incremental
	data:    Name                           Type          Value
	data:    -----------------------------  ------------  ----------------------------
	data:    siteName                       String        TestApp1Dev
	data:    hostingPlanName                String        TestApp1PlanDev
	data:    siteLocation                   String        Central US
	data:    sku                            String        Free
	data:    workerSize                     String        0
	data:    serverName                     String        testapp1dev
	data:    serverLocation                 String        Central US
	data:    administratorLogin             String        testapp1Admin
	data:    administratorLoginPassword     SecureString  undefined
	data:    databaseName                   String        testapp1dev
	data:    collation                      String        SQL_Latin1_General_CP1_CI_AS
	data:    edition                        String        Standard
	data:    maxSizeBytes                   String        1073741824
	data:    requestedServiceObjectiveName  String        S0
	info:    group deployment create command OKx

  Si el comando no funciona, resuelva los posibles mensajes de error y vuelva a intentarlo. Algunos de los problemas más comunes se deben al uso de valores de parámetros que no cumplen las restricciones de nomenclatura de los recursos de Azure. Se pueden encontrar otras sugerencias de solución de problemas en el artículo [Solución de problemas de implementaciones de grupos de recursos en Azure](virtual-machines/resource-group-deploy-debug.md).

  En la línea de comandos CLI, escriba el comando siguiente para implementar recursos en el grupo de recursos que creó para el entorno de pruebas, y sustituya [path] por la ruta de acceso a los archivos guardados en los pasos anteriores.

	azure group deployment create -g TestApp1-Test -n Deployment1 -f [path]TestApp1-Template.json -e [path]TestApp1-Parameters-Test.json

  En la línea de comandos CLI, escriba el comando siguiente para implementar recursos en el grupo de recursos que creó para el entorno de preproducción, y sustituya [path] por la ruta de acceso a los archivos guardados en los pasos anteriores.

	azure group deployment create -g TestApp1-Pre-Production -n Deployment1 -f [path]TestApp1-Template.json -e [path]TestApp1-Parameters-Pre-Production.json
  
  **Método 2:** PowerShell

  En el símbolo del sistema de PowerShell, escriba el comando siguiente para implementar recursos en el grupo de recursos que creó para el entorno de desarrollo, y sustituya [path] por la ruta de acceso a los archivos guardados en los pasos anteriores. Si está utilizando la versión de vista previa de Azure PowerShell 1.0, el comando es **New-AzureRmResourceGroupDeployment** tal como se muestra a continuación. Si está utilizando una versión de Azure PowerShell anterior a la versión de vista previa 1.0, el comando es **New-AzureResourceGroupDeployment**.

	New-AzureRmResourceGroupDeployment -ResourceGroupName TestApp1-Development -TemplateFile [path]TestApp1-Template.json -TemplateParameterFile [path]TestApp1-Parameters-Development.json -Name Deployment1 

  El comando devolverá el siguiente resultado si se ejecuta correctamente:

	DeploymentName    : Deployment1
	ResourceGroupName : TestApp1-Development
	ProvisioningState : Succeeded
	Timestamp         : XX/XX/XXXX 2:44:48 PM
	Mode              : Incremental
	TemplateLink      : 
	Parameters        : 
	                    Name             Type                       Value     
	                    ===============  =========================  ==========
	                    siteName         String                     TestApp1Dev
	                    hostingPlanName  String                     TestApp1PlanDev
	                    siteLocation     String                     Central US
	                    sku              String                     Free      
	                    workerSize       String                     0         
	                    serverName       String                     testapp1dev
	                    serverLocation   String                     Central US
	                    administratorLogin  String                     testapp1Admin
	                    administratorLoginPassword  SecureString                         
	                    databaseName     String                     testapp1dev
	                    collation        String                     SQL_Latin1_General_CP1_CI_AS
	                    edition          String                     Standard  
	                    maxSizeBytes     String                     1073741824
	                    requestedServiceObjectiveName  String                     S0        
	                    
	Outputs           :

  Si el comando no funciona, resuelva los posibles mensajes de error y vuelva a intentarlo. Algunos de los problemas más comunes se deben al uso de valores de parámetros que no cumplen las restricciones de nomenclatura de los recursos de Azure. Se pueden encontrar otras sugerencias de solución de problemas en el artículo [Solución de problemas de implementaciones de grupos de recursos en Azure](virtual-machines/resource-group-deploy-debug.md).

  En el símbolo del sistema de PowerShell, escriba el comando siguiente para implementar recursos en el grupo de recursos que creó para el entorno de pruebas, y sustituya [path] por la ruta de acceso a los archivos guardados en los pasos anteriores.

	New-AzureRmResourceGroupDeployment -ResourceGroupName TestApp1-Test -TemplateFile [path]TestApp1-Template.json -TemplateParameterFile [path]TestApp1-Parameters-Test.json -Name Deployment1

  En el símbolo del sistema de PowerShell, escriba el comando siguiente para implementar recursos en el grupo de recursos que creó para el entorno de preproducción, y sustituya [path] por la ruta de acceso a los archivos guardados en los pasos anteriores.

	New-AzureRmResourceGroupDeployment -ResourceGroupName TestApp1-Pre-Production -TemplateFile [path]TestApp1-Template.json -TemplateParameterFile [path]TestApp1-Parameters-Pre-Production.json -Name Deployment1

Se puede controlar la versión de los archivos de plantilla y de parámetros y se pueden mantener estos archivos con el código de aplicación en el sistema de control de código fuente. También puede guardar los comandos anteriores en archivos de script y guardarlos con el código.

## Mantenimiento de los entornos
Durante el desarrollo, la configuración de los recursos de Azure en los distintos entornos se puede modificar de forma incoherente bien a propósito o por accidente. Esto puede ocasionar que se tengan que resolver problemas innecesariamente durante el ciclo de desarrollo de aplicaciones.

  **Paso 7:** Modificación de los entornos. Abra el [Portal de vista previa de Azure](https://portal.azure.com) e inicie sesión en él con la misma cuenta que usó para realizar los pasos anteriores. Como se muestra en la siguiente imagen, haga clic en Examinar todos--> Grupos de recursos (deberá desplazarse hacia abajo en la hoja Examinar para ver los grupos de recursos). Verá los tres grupos de recursos que creó mediante uno de los métodos de los pasos anteriores. Haga clic en el grupo de recursos TestApp1-Development, tal como se muestra a continuación.

  ![Portal](./media/solution-dev-test-environments-preview-portal/portal1.png)

  Tras hacer clic en el grupo de recursos TestApp1-Development, verá la hoja que muestra los recursos creados por la plantilla en la implementación del grupo de recursos realizada en un paso anterior. Elimine el recurso de aplicación web TestApp1Dev haciendo clic en TestApp1Dev--> Eliminar, tal como se muestra a continuación.

  ![Portal](./media/solution-dev-test-environments-preview-portal/portal2.png)

  Cuando el portal le pregunte si está seguro de que desea eliminar el recurso, haga clic en "Sí". El contenido del grupo de recursos ahora es diferente de lo que debería ser. Puede seguir experimentando y eliminar varios recursos de varios grupos de recursos o incluso cambiar la configuración de algunos de los recursos.

> [AZURE.NOTE]En lugar de usar el Portal de vista previa de Azure para eliminar un recurso de un grupo de recursos, puede usar el comando [Remove-AzureResource](https://msdn.microsoft.com/library/azure/dn757676.aspx) de PowerShell o el comando "azure resource delete" desde la CLI para realizar la misma tarea.

  **Paso 8:** Nueva implementación de los entornos en los grupos de recursos con los mismos comandos usados en el paso 6, pero se sustituye "Deployment1" por "Deployment2". Como se muestra en la sección Resumen de la siguiente imagen, verá que todos los recursos de la plantilla vuelven a aparecer de nuevo en el grupo de recursos de desarrollo TestApp1. Una de las ventajas de implementar los entornos con plantillas del Administrador de recursos de Azure es que puede volver a implementarlos fácilmente y devolverlos a un estado conocido en cualquier momento.

  ![Portal](./media/solution-dev-test-environments-preview-portal/portal3.png)

  Si hace clic en el texto de "Última implementación" en la imagen, verá una hoja que muestra el historial de implementación del grupo de recursos. Puesto que usó el nombre "Deployment1" para la primera implementación y "Deployment2" para la segunda, tendrá dos entradas. Al hacer clic en una implementación se mostrará una hoja con los resultados de cada implementación.

## Eliminación de entornos
Una vez que ha terminado con un entorno, querrá eliminarlo para así no incurrir en gastos de uso por recursos de Azure que ya no usa. Eliminar entornos es incluso más sencillo que crearlos. En los pasos anteriores, se crearon grupos de recursos de Azure individuales para cada entorno. Al eliminar un grupo de recursos, también se eliminan todos los recursos que contiene. Teniendo este en cuenta, con cualquiera de los métodos siguientes se eliminarán los entornos (grupos de recursos), junto con todos los recursos de Azure que contienen que implementó anteriormente.

  **Paso 9:** Eliminación de los entornos con cualquiera de los métodos siguientes. Con ambos métodos se obtiene exactamente el mismo resultado.

  **Método 1: CLI de Azure**

  En el símbolo del sistema de CLI, escriba lo siguiente:

	azure group delete "TestApp1-Development"

  El comando devolverá el siguiente resultado si especifica "y" cuando se le pregunta:

	info:    Executing command group delete
	Delete resource group TestApp1-Development? [y/n] y
	+ Deleting resource group TestApp1-Development
	info:    group delete command OK

  En un símbolo del sistema de CLI, escriba lo siguiente para eliminar el resto de entornos:

	azure group delete "TestApp1-Test"
	azure group delete "TestApp1-Pre-Production"
  
  **Método 2:** PowerShell

  Si está utilizando la versión de vista previa de Azure PowerShell 1.0, el comando es **Remove-AzureRmResourceGroup** tal como se muestra a continuación. Si está utilizando una versión de Azure PowerShell anterior a la versión de vista previa 1.0, el comando es **Remove-AzureResourceGroup**. En un símbolo del sistema de PowerShell, escriba lo siguiente:

	Remove-AzureRmResourceGroup -Name TestApp1-Development

  El comando devolverá el siguiente resultado si especifica "y" cuando se le pregunta:

	Confirm
	Are you sure you want to remove resource group 'TestApp1-Development'
	[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y

  En un símbolo del sistema de PowerShell, escriba lo siguiente para eliminar el resto de entornos:

	Remove-AzureRmResourceGroup -Name TestApp1-Test
	Remove-AzureRmResourceGroup -Name TestApp1-Pre-Production

Con independencia del método que use, una vez que los comandos terminan de ejecutarse, los grupos de recursos y todos los recursos que contienen ya no existirán y se le dejará de cobrar por estos recursos.

Para minimizar los gastos de utilización de recursos de Azure que se producen durante el desarrollo de aplicaciones, puede usar [Automatización de Azure](automation/automation-intro.md) para programar trabajos que:

- Detengan las máquinas virtuales al final del día y las reinician al inicio del día siguiente.
- Detengan los entornos enteros al final del día y los vuelvan a crear al inicio del día siguiente.
 
Ahora que ha experimentado lo fácil que es crear, mantener y eliminar entornos de desarrollo y pruebas, puede aprender más sobre lo que acaba de hacer si sigue experimentando con los pasos anteriores y lee las referencias contenidas en este artículo.

## Pasos siguientes

- [Delegue el control administrativo](role-based-access-control-configure.md) en diferentes recursos de cada entorno y asigne grupos o usuarios de Microsoft Azure AD a roles específicos que tengan la capacidad para realizar un subconjunto de operaciones en recursos de Azure.
- [Asigne etiquetas](resource-group-using-tags.md) a los grupos de recursos para cada entorno o a los recursos individuales. Puede agregar una etiqueta "Entorno" a los grupos de recursos y establecer su valor para que coincida con los nombres de su entorno. Las etiquetas pueden resultar especialmente útiles si necesita organizar recursos para facturación o administración.
- Supervise las alertas y la facturación de los recursos del grupo de recursos en el [Portal de vista previa de Azure](https://portal.azure.com).

## Recursos adicionales

- [Cree e implemente plantillas del Administrador de recursos de Azure en Visual Studio](http://msdn.microsoft.com/library/azure/Dn872471.aspx) con Azure SDK 2.6 instalado.
- Cree la aplicación mediante [Visual Studio Enterprise](https://www.visualstudio.com/products/visual-studio-enterprise-vs), [Visual Studio Code](http://www.visualstudio.com/products/code-vs) o [Web Matrix](http://www.microsoft.com/web/webmatrix/).
- [Implemente una aplicación web](app-service-web/web-sites-deploy.md) en los entornos que creó.
- Use [Visual Studio Release Management](http://msdn.microsoft.com/Library/vs/alm/Release/overview) para crear canalizaciones de implementación continuas y administradas y liberarlas de forma rápida, fácil y con frecuencia.
- Solicite una invitación para la vista previa de [Laboratorio de desarrollo y pruebas de Azure](http://azure.microsoft.com/campaigns/devtest-lab/). Esta aplicación le permite administrar entornos de laboratorio de desarrollo y pruebas mediante plantillas y configurar cuotas y directivas para su uso dentro de la organización.

<!---HONumber=Oct15_HO3-->