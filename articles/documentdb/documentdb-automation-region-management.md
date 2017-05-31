---
title: "Automatización de Azure Cosmos DB: administración de regiones | Microsoft Docs"
description: Use la CLI de Azure 1.0 y Azure Resource Manager para administrar regiones en una cuenta de base de datos Azure Cosmos DB.
services: cosmosdb
author: dmakwana
manager: jhubbard
editor: 
tags: azure-resource-manager
documentationcenter: 
ms.assetid: 7f765c17-8549-4108-9475-46394fc3a218
ms.service: cosmosdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/17/2017
ms.author: dimakwan
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 961336e94dca6672dfd6751c4ba27c3997058377
ms.contentlocale: es-es
ms.lasthandoff: 05/10/2017


---
# <a name="automate-azure-cosmos-db-account-region-management-using-azure-cli-10-and-azure-resource-manager-templates"></a>Automatización de la administración de regiones de cuentas de Azure Cosmos DB con la CLI de Azure 1.0 y plantillas de Azure Resource Manager

En este artículo se muestra cómo agregar o quitar una región en la cuenta de la API de DocumentDB de Azure Cosmos DB mediante comandos de la CLI de Azure 1.0 y plantillas de Azure Resource Manager. La administración de las regiones también se puede llevar a cabo a través de [Azure Portal](../cosmos-db/tutorial-global-distribution-documentdb.md). Tenga en cuenta que los comandos del tutorial siguiente no le permiten cambiar las prioridades de conmutación por error de las distintas regiones. Solo pueden agregarse o quitarse las regiones de lectura. La región de escritura de una cuenta de base de datos (prioridad de conmutación por error de 0) no se puede agregar ni quitar.

Las cuentas de base de datos de Azure Cosmos DB son, en este momento, el único recurso de Azure Cosmos DB que se puede crear o modificar con [plantillas de Azure Resource Manager y la CLI de Azure 1.0](documentdb-automation-resource-manager-cli.md). 

Para crear una cuenta de la API de DocumentDB, Table API, API Graph o Mongo DB de Azure Cosmos DB mediante la CLI 2.0, vea [Create an Azure DocumentDB account using the Azure CLI](documentdb-automation-resource-manager-cli.md) (Creación de una cuenta de Azure DocumentDB mediante la CLI de Azure).

## <a name="getting-ready"></a>Preparación

Para poder usar la CLI de Azure 1.0 con grupos de recursos de Azure, necesita la versión correcta de la CLI de Azure 1.0 y una cuenta de Azure. Si no tiene la CLI de Azure 1.0, debe [instalarla](../cli-install-nodejs.md).

### <a name="update-your-azure-cli-10-version"></a>Actualización a la versión 1.0 de la CLI de Azure

En el símbolo del sistema, escriba `azure --version` para ver si ya tiene instalada la versión 0.10.4 o una posterior. Es posible que se le pida que participe en la recopilación de datos de la CLI de Microsoft Azure 1.0 en este paso, y puede optar por hacerlo o no.

    azure --version
    0.10.4 (node: 4.2.4)

Si la versión no es 0.10.4 o posterior, necesita [instalar la CLI de Azure 1.0](../cli-install-nodejs.md) o actualizarla mediante uno de los instaladores nativos o a través de `npm update -g azure-cli`npm`npm install -g azure-cli`. Escriba  **para actualizarla o**  para instalarla.

### <a name="set-your-azure-account-and-subscription"></a>Definición de su cuenta y suscripción de Azure

Si aún no tiene una suscripción a Azure pero sí una suscripción a Visual Studio, puede activar sus [ventajas para los suscriptores de Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/). O puede suscribirse a una [evaluación gratuita](https://azure.microsoft.com/pricing/free-trial/).

Para usar plantillas de Administración de recursos de Azure, debe tener una identidad de cuentas Microsoft o de cuenta profesional o educativa. Si tiene una de estas cuentas, escriba el siguiente comando.

    azure login

Lo que genera el siguiente resultado: 

    info:    Executing command login
    |info:    To sign in, use a web browser to open the page https://aka.ms/devicelogin. 
    Enter the code E1A2B3C4D to authenticate.

> [!NOTE]
> Si no tiene una cuenta de Azure, verá un mensaje de error que indica que necesita un otro tipo de cuenta. Para crear una a partir su cuenta de Azure actual, consulte [Crear una identidad profesional o educativa en Azure Active Directory](../virtual-machines/windows/create-aad-work-id.md).

Abra [https://aka.ms/devicelogin](https://aka.ms/devicelogin) en un explorador y escriba el código proporcionado en la salida del comando.

![Captura de pantalla que muestra la pantalla de inicio de sesión del dispositivo para la CLI de Microsoft Azure 1.0](media/documentdb-automation-resource-manager-cli/azure-cli-login-code.png)

Una vez escrito el código, seleccione la identidad que quiere usar en el explorador y, si es necesario, indique su nombre de usuario y contraseña.

![Captura de pantalla que muestra en donde debe seleccionar la cuenta de identidad de Microsoft asociada a la suscripción de Azure que quiere usar](media/documentdb-automation-resource-manager-cli/identity-cli-login.png)

Obtendrá la siguiente pantalla de confirmación cuando se haya conectado; luego puede cerrar la ventana del explorador.

![Captura de pantalla que muestra la confirmación del inicio de sesión en la interfaz de línea de comandos multiplataforma de Microsoft Azure](media/documentdb-automation-resource-manager-cli/login-confirmation.png)

El shell de comandos también proporciona el siguiente resultado.

    /info:    Added subscription Visual Studio Ultimate with MSDN
    info:    Setting subscription "Visual Studio Ultimate with MSDN" as default
    +
    info:    login command OK

Además del método de inicio de sesión interactivo que se describe aquí, existen otros métodos de inicio de sesión en la CLI de Azure 1.0. Para más información sobre otros métodos y sobre el control de varias suscripciones, vea [Conexión a una suscripción de Azure desde la interfaz de la línea de comandos de Azure (CLI de Azure 1.0)](../xplat-cli-connect.md).

### <a name="switch-to-azure-cli-10-resource-group-mode"></a>Cambio al modo de grupo de recursos de la CLI de Azure 1.0

De manera predeterminada, la CLI de Azure 1.0 se inicia en el modo de Service Management (modo **asm**). Escriba lo siguiente para cambiar al modo de grupo de recursos.

    azure config mode arm

Lo que genera el siguiente resultado:

    info:    Executing command config mode
    info:    New mode is arm
    info:    config mode command OK

Si fuera necesario, puede volver a cambiar al conjunto predeterminado de comandos escribiendo `azure config mode asm`.

### <a name="create-or-retrieve-your-resource-group"></a>Creación o recuperación del grupo de recursos

Para crear una cuenta de Azure Cosmos DB, se necesita primero un grupo de recursos. Si ya conoce el nombre del grupo de recursos que quiere usar, vaya al [paso 2](#create-documentdb-account-cli). 

Para revisar una lista de todos los grupos de recursos actuales, ejecute el siguiente comando y tome nota del nombre del grupo de recursos que quiere usar: 

    azure group list

Para crear un grupo de recursos, ejecute el siguiente comando, especifique el nombre del nuevo grupo de recursos que se va crear y la región en la que se va a crear: 

    azure group create <resourcegroupname> <resourcegrouplocation>

 - `<resourcegroupname>` solo admite caracteres alfanuméricos, puntos, caracteres de subrayado, el carácter "-" y paréntesis, y no puede terminar en punto. 
 - `<resourcegrouplocation>` debe ser una de las regiones en las que Azure Cosmos DB esté generalmente disponible. Se proporciona la lista actual de regiones en la [página Regiones de Azure](https://azure.microsoft.com/regions/#services).

Entrada de ejemplo:

    azure group create new_res_group westus

Lo que genera el siguiente resultado:

    info:    Executing command group create
    + Getting resource group new_res_group
    + Creating resource group new_res_group
    info:    Created resource group new_res_group
    data:    Id:                  /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/new_res_group
    data:    Name:                new_res_group
    data:    Location:            westus
    data:    Provisioning State:  Succeeded
    data:    Tags: null
    data:
    info:    group create command OK

Si se producen errores, consulte [Solución de problemas](#troubleshooting). 

## <a name="understanding-azure-resource-manager-templates-and-resource-groups"></a>Descripción de las plantillas y grupos de recursos de Azure Resource Manager

La mayoría de las aplicaciones se crean a partir de una combinación de distintos tipos de recursos (por ejemplo, una o varias cuentas de Azure Cosmos DB, cuentas de almacenamiento, una red virtual o una red de entrega de contenido). La API de administración de servicios de Azure predeterminada y el portal de Azure clásico representan estos elementos mediante un enfoque de servicio por servicio, que requiere implementar y administrar servicios individuales (o buscar otras herramientas que lo hagan) y no como una unidad lógica de implementación.

*Las plantillas del Administrador de recursos de Azure* permiten implementar y administrar estos recursos diferentes como una unidad lógica de implementación de manera declarativa. En lugar de indicar imperativamente a Azure que debe implementar un comando tras otro, describa la implementación completa en un archivo JSON (todos los recursos y configuración asociada y parámetros de implementación) e indíquele a Azure que implemente esos recursos como un único grupo.

Puede obtener mucha más información sobre los grupos de recursos de Azure y su utilidad en [Información general de Azure Resource Manager](../azure-resource-manager/resource-group-overview.md). Si está interesado en la creación de plantillas, consulte [Creación de plantillas del Administrador de recursos de Azure](../azure-resource-manager/resource-group-authoring-templates.md).


## <a id="add-region-documentdb-account"></a>Tarea: Agregar una cuenta de la API de DocumentDB de Azure Cosmos DB

Azure Cosmos DB puede [distribuir a escala internacional los datos][distribute-globally] entre diversas [regiones de Azure](https://azure.microsoft.com/regions/#services). En las instrucciones de esta sección se describe cómo agregar una región de lectura a una cuenta de Azure Cosmos DB existente con la CLI de Azure 1.0 y plantillas de Resource Manager. Puede hacerlo con la CLI de Azure 1.0, con las plantillas de Resource Manager o sin ellas.

### <a id="add-region-documentdb-account-cli"></a> Incorporación de una región a una cuenta de Azure Cosmos DB mediante la CLI de Azure 1.0 sin plantillas de Resource Manager

Agregue una región a una cuenta de Azure Cosmos DB existente en el grupo de recursos nuevo o existente escribiendo el siguiente comando en el símbolo del sistema. Tenga en cuenta que la matriz "locations" debe reflejar la configuración de regiones actual dentro de la cuenta de Azure Cosmos DB con la excepción de la nueva región que se va a agregar. El ejemplo siguiente muestra un comando para agregar otra región a la cuenta.

Los valores de prioridad de conmutación por error deben coincidir con la configuración existente. Una de las regiones debe tener un valor de failoverPriority de 0 para indicar que esta región se mantendrá como la [región de escritura de la cuenta de Azure Cosmos DB][scaling-globally]. Los valores de prioridad de conmutación por error deben ser únicos entre las ubicaciones. Además, el valor máximo de prioridad de conmutación por error debe ser inferior al número total de regiones. La nueva región será una región de tipo "Lectura" y debe tener un valor de prioridad de conmutación por error mayor que 0.

> [!TIP]
> Si ejecuta este comando en Azure PowerShell o Windows PowerShell, recibirá un error sobre un token inesperado. En su lugar, ejecute este comando en el símbolo del sistema de Windows.

    azure resource create -g <resourcegroupname> -n <databaseaccountname> -r "Microsoft.DocumentDB/databaseAccounts" -o 2015-04-08 -l <resourcegrouplocation> -p "{\"databaseAccountOfferType\":\"Standard\",\"ipRangeFilter\":\"<ip-range-filter>\",\"locations\":["{\"locationName\":\"<databaseaccountlocation>\",\"failoverPriority\":\"<failoverPriority1>\"},{\"locationName\":\"<newdatabaseaccountlocation>\",\"failoverPriority\":\"<failoverPriority2>\"}"]}"

 - `<resourcegroupname>` solo admite caracteres alfanuméricos, puntos, caracteres de subrayado, el carácter "-" y paréntesis, y no puede terminar en punto.
 - `<resourcegrouplocation>` es la región del grupo de recursos actual.
 - `<ip-range-filter>` Especifica el conjunto de direcciones IP o intervalos de direcciones IP en formato CIDR para incluir en la lista permitida de direcciones IP de cliente para una cuenta de base de datos dada. Los intervalos o direcciones IP deben ir separados por una coma y no deben contener espacios. Para más información, consulte [Compatibilidad con el firewall de Azure Cosmos DB](documentdb-firewall-support.md).
 - `<databaseaccountname>` solo admite minúsculas, números, el carácter "-", y debe tener entre 3 y 50 caracteres.
 - `<databaseaccountlocation>` debe ser una de las regiones en las que Azure Cosmos DB esté generalmente disponible. Se proporciona la lista actual de regiones en la [página Regiones de Azure](https://azure.microsoft.com/regions/#services).
 - `<newdatabaseaccountlocation>` es la nueva región que se va a agregar y debe ser una de las regiones en las que Azure Cosmos DB esté generalmente disponible. Se proporciona la lista actual de regiones en la [página Regiones de Azure](https://azure.microsoft.com/regions/#services).


Ejemplo de entrada para agregar la región "este de EE. UU." como región de lectura en la cuenta de Azure Cosmos DB: 

    azure resource create -g new_res_group -n samplecliacct -r "Microsoft.DocumentDB/databaseAccounts" -o 2015-04-08 -l westus -p "{\"databaseAccountOfferType\":\"Standard\",\"ipRangeFilter\":\"\",\"locations\":["{\"locationName\":\"westus\",\"failoverPriority\":\"0\"},{\"locationName\":\"eastus\",\"failoverPriority\":\"1\"}"]}"

Lo que genera el siguiente resultado cuando se aprovisiona la nueva cuenta:

    info:    Executing command resource create
    + Getting resource samplecliacct
    + Creating resource samplecliacct
    info:    Resource samplecliacct is updated
    data:
    data:    Id:        /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/new_res_group/providers/Microsoft.DocumentDB/databaseAccounts/samplecliacct
    data:    Name:      samplecliacct
    data:    Type:      Microsoft.DocumentDB/databaseAccounts
    data:    Parent:
    data:    Location:  West US
    data:    Tags:
    data:
    info:    resource create command OK

Si se producen errores, consulte [Solución de problemas](#troubleshooting). 

Cuando el comando responda, la cuenta se encontrará en el estado **Creando** unos minutos antes de pasar al estado **En línea**, en el que está lista para usarse. Puede comprobar el estado de la cuenta en [Azure Portal](https://portal.azure.com), en la hoja **Azure Cosmos DB Accounts** (Cuantas de Azure Cosmos DB).

### <a id="add-region-documentdb-account-cli-arm"></a> Incorporación de una región a una cuenta de Azure Cosmos DB mediante la CLI de Azure 1.0 con plantillas de Resource Manager

En las instrucciones de esta sección se describe cómo agregar una región a una cuenta de Azure Cosmos DB existente con una plantilla de Azure Resource Manager y un archivo opcional de parámetros (los dos recursos son archivos JSON). El uso de una plantilla le permite describir exactamente lo que desea y repetirlo sin errores.

Los valores de prioridad de conmutación por error deben coincidir con la configuración existente. Una de las regiones debe tener un valor de failoverPriority de 0 para indicar que esta región se mantendrá como la [región de escritura de la cuenta de Azure Cosmos DB][scaling-globally]. Los valores de prioridad de conmutación por error deben ser únicos entre las ubicaciones. Además, el valor máximo de prioridad de conmutación por error debe ser inferior al número total de regiones. La nueva región será una región de tipo "Lectura" y debe tener un valor de prioridad de conmutación por error mayor que 0.

Cree un archivo de plantilla local similar al siguiente que coincida con la configuración actual de la región de Azure Cosmos DB. La matriz "locations" debe contener todas las regiones existentes en la cuenta de base de datos junto con la nueva región que se va a agregar. Asigne al archivo el nombre azuredeploy.json.

    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "databaseAccountName": {
                "type": "string"
            },
            "locationName1": {
                "type": "string"
            },
            "locationName2": {
                "type": "string"
            },
            "newLocationName": {
                "type": "string"
            }
        },
        "variables": {},
        "resources": [
            {
                "apiVersion": "2015-04-08",
                "type": "Microsoft.DocumentDb/databaseAccounts",
                "name": "[parameters('databaseAccountName')]",
                "location": "[resourceGroup().location]",
                "properties": {
                    "databaseAccountOfferType": "Standard",
                    "ipRangeFilter": "",
                    "locations": [
                        {
                            "failoverPriority": 0,
                            "locationName": "[parameters('locationName1')]"
                        },
                        {
                            "failoverPriority": 1,
                            "locationName": "[parameters('locationName2')]"
                        },
                        {
                            "failoverPriority": 2,
                            "locationName": "[parameters('newLocationName')]"
                        }
                    ]
                }
            }
        ]
    }

El archivo de plantilla anterior muestra un ejemplo en el que se agrega una nueva región a una cuenta de Azure Cosmos DB que ya tiene dos regiones.

Puede escribir los valores de los parámetros en la línea de comandos o crear un archivo de parámetros para especificar el valor.

Para crear un archivo de parámetros, copie el siguiente contenido en un nuevo archivo y asigne al archivo el nombre azuredeploy.parameters.json. Si va a especificar el nombre de cuenta de base de datos en el símbolo del sistema, puede continuar sin crear este archivo.

    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "databaseAccountName": {
                "value": "samplearmacct"
            },
            "locationName1": {
                "value": "westus"
            },
            "locationName2": {
                "value": "eastus"
            },
            "newLocationName": {
                "value": "northeurope"
            }
        }
    }

En el archivo azuredeploy.parameters.json, actualice el campo de valor de `"databaseAccountName"` al nombre de base de datos que desee usar y, después, guarde el archivo. `"databaseAccountName"` solo admite minúsculas, números, el carácter "-", y debe tener entre 3 y 50 caracteres. Actualice los campos de valor de `"locationName1"` y `"locationName2"` a las regiones donde existe su cuenta de Azure Cosmos DB. Actualice el campo de valor de `"newLocationName"` a la región que desea agregar.

Para crear una cuenta de Azure Cosmos DB en el grupo de recursos, ejecute el comando siguiente e indique la ruta de acceso al archivo de plantilla, la ruta de acceso al archivo de parámetros o el valor del parámetro, el nombre del grupo de recursos en donde se va a implementar y un nombre de implementación (-n es opcional). 

Para usar un archivo de parámetros:

    azure group deployment create -f <PathToTemplate> -e <PathToParameterFile> -g <resourcegroupname> -n <deploymentname>

 - `<PathToTemplate>` es la ruta de acceso al archivo azuredeploy.json creado en el paso 1. Si el nombre de ruta de acceso contiene espacios, ponga comillas dobles alrededor de este parámetro.
 - `<PathToParameterFile>` es la ruta de acceso al archivo azuredeploy.parameters.json creado en el paso 1. Si el nombre de ruta de acceso contiene espacios, ponga comillas dobles alrededor de este parámetro.
 - `<resourcegroupname>` es el nombre del grupo de recursos existente al que se agrega una cuenta de base de datos de Azure Cosmos DB. 
 - `<deploymentname>` es el nombre opcional de la implementación.

Entrada de ejemplo: 

    azure group deployment create -f azuredeploy.json -e azuredeploy.parameters.json -g new_res_group -n azuredeploy

O bien, para especificar el parámetro de nombre de cuenta de base de datos sin un archivo de parámetros y que en su lugar se le pida el valor, ejecute el siguiente comando:

    azure group deployment create -f <PathToTemplate> -g <resourcegroupname> -n <deploymentname>

Entrada de ejemplo que muestra el símbolo del sistema y una entrada para una cuenta de base de datos denominada "samplearmacct":

    azure group deployment create -f azuredeploy.json -g new_res_group -n azuredeploy
    info:    Executing command group deployment create
    info:    Supply values for the following parameters
    databaseAccountName: samplearmacct

Cuando se haya aprovisionado la cuenta, aparecerá la siguiente información: 

    info:    Executing command group deployment create
    + Initializing template configurations and parameters
    + Creating a deployment
    info:    Created template deployment "azuredeploy"
    + Waiting for deployment to complete
    + 
    + 
    info:    Resource 'new_res_group' of type 'Microsoft.Azure Cosmos DB/databaseAccounts' provisioning status is Running
    + 
    info:    Resource 'new_res_group' of type 'Microsoft.DocumentDb/databaseAccounts' provisioning status is Succeeded
    data:    DeploymentName     : azuredeploy
    data:    ResourceGroupName  : new_res_group
    data:    ProvisioningState  : Succeeded
    data:    Timestamp          : 2015-11-30T18:50:23.6300288Z
    data:    Mode               : Incremental
    data:    CorrelationId      : 4a5d4049-c494-4053-bad4-cc804d454700
    data:    DeploymentParameters :
    data:    Name                 Type    Value
    data:    -------------------  ------  ------------------
    data:    locationName1        String  westus
    data:    locationName2        String  eastus
    data:    newLocationName      String  eastus
    data:    databaseAccountName  String  samplearmacct
    info:    group deployment create command OK

Si se producen errores, consulte [Solución de problemas](#troubleshooting).  

Cuando el comando responda, la cuenta se encontrará en el estado **Creando** unos minutos antes de pasar al estado **En línea**, en el que está lista para usarse. Puede comprobar el estado de la cuenta en [Azure Portal](https://portal.azure.com), en la hoja **Azure Cosmos DB Accounts** (Cuantas de Azure Cosmos DB).

## <a id="remove-region-documentdb-account"></a>Tarea: Retirada de una región de una cuenta de base de datos de Azure Cosmos DB

Azure Cosmos DB puede [distribuir a escala internacional los datos][distribute-globally] entre diversas [regiones de Azure](https://azure.microsoft.com/regions/#services). En las instrucciones de esta sección se describe cómo quitar una región de una cuenta de Azure Cosmos DB existente con la CLI de Azure 1.0 y plantillas de Resource Manager. Puede hacerlo con la CLI de Azure 1.0, con las plantillas de Resource Manager o sin ellas.

### <a id="remove-region-documentdb-account-cli"></a> Retirada de una región de una cuenta de Azure Cosmos DB mediante la CLI de Azure 1.0 sin plantillas de Resource Manager

Para quitar una región de una cuenta de Azure Cosmos DB existente, se puede ejecutar el comando siguiente con la CLI de Azure 1.0. La matriz "locations" debería contener solo las regiones que permanecerán después de la eliminación de la región. **La ubicación omitida se quitará de la cuenta de Azure Cosmos DB**. Escriba el comando siguiente en el símbolo del sistema.

Una de las regiones debe tener un valor de failoverPriority de 0 para indicar que esta región se mantendrá como la [región de escritura de la cuenta de Azure Cosmos DB][scaling-globally]. Los valores de prioridad de conmutación por error deben ser únicos entre las ubicaciones. Además, el valor máximo de prioridad de conmutación por error debe ser inferior al número total de regiones. 

> [!TIP]
> Si ejecuta este comando en Azure PowerShell o Windows PowerShell, recibirá un error sobre un token inesperado. En su lugar, ejecute este comando en el símbolo del sistema de Windows.

    azure resource create -g <resourcegroupname> -n <databaseaccountname> -r "Microsoft.DocumentDB/databaseAccounts" -o 2015-04-08 -l <resourcegrouplocation> -p "{\"databaseAccountOfferType\":\"Standard\",\"ipRangeFilter\":\"<ip-range-filter>\",\"locations\":["{\"locationName\":\"<databaseaccountlocation>\",\"failoverPriority\":\"<failoverPriority>\"}"]}"

 - `<resourcegroupname>` solo admite caracteres alfanuméricos, puntos, caracteres de subrayado, el carácter "-" y paréntesis, y no puede terminar en punto.
 - `<resourcegrouplocation>` es la región del grupo de recursos actual.
 - `<ip-range-filter>` Especifica el conjunto de direcciones IP o intervalos de direcciones IP en formato CIDR para incluir en la lista permitida de direcciones IP de cliente para una cuenta de base de datos dada. Los intervalos o direcciones IP deben ir separados por una coma y no deben contener espacios. Para más información, consulte [Compatibilidad con el firewall de Azure Cosmos DB](documentdb-firewall-support.md).
 - `<databaseaccountname>` solo admite minúsculas, números, el carácter "-", y debe tener entre 3 y 50 caracteres.
 - `<databaseaccountlocation>` debe ser una de las regiones en las que Azure Cosmos DB esté generalmente disponible. Se proporciona la lista actual de regiones en la [página Regiones de Azure](https://azure.microsoft.com/regions/#services).

Entrada de ejemplo: 

    azure resource create -g new_res_group -n samplecliacct -r "Microsoft.DocumentDB/databaseAccounts" -o 2015-04-08 -l westus -p "{\"databaseAccountOfferType\":\"Standard\",\"ipRangeFilter\":\"\",\"locations\":["{\"locationName\":\"westus\",\"failoverPriority\":\"0\"}"]}"

Lo que genera el siguiente resultado cuando se aprovisiona la nueva cuenta:

    info:    Executing command resource create
    + Getting resource samplecliacct
    + Creating resource samplecliacct
    info:    Resource samplecliacct is updated
    data:
    data:    Id:        /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/new_res_group/providers/Microsoft.DocumentDB/databaseAccounts/samplecliacct
    data:    Name:      samplecliacct
    data:    Type:      Microsoft.DocumentDB/databaseAccounts
    data:    Parent:
    data:    Location:  West US
    data:    Tags:
    data:
    info:    resource create command OK

Si se producen errores, consulte [Solución de problemas](#troubleshooting). 

Cuando el comando responda, la cuenta se encontrará en el estado **Actualizando** unos minutos antes de pasar al estado **En línea**, en el que está lista para usarse. Puede comprobar el estado de la cuenta en [Azure Portal](https://portal.azure.com), en la hoja **Azure Cosmos DB Accounts** (Cuantas de Azure Cosmos DB).

### <a id="remove-region-documentdb-account-cli-arm"></a> Retirada de una región de una cuenta de Azure Cosmos DB mediante la CLI de Azure 1.0 con plantillas de Resource Manager

En las instrucciones de esta sección se describe cómo quitar una región de una cuenta de Azure Cosmos DB existente con una plantilla de Azure Resource Manager y un archivo opcional de parámetros (los dos recursos son archivos JSON). El uso de una plantilla le permite describir exactamente lo que desea y repetirlo sin errores.

Una de las regiones debe tener un valor de failoverPriority de 0 para indicar que esta región se mantendrá como la [región de escritura de la cuenta de Azure Cosmos DB][scaling-globally]. Los valores de prioridad de conmutación por error deben ser únicos entre las ubicaciones. Además, el valor máximo de prioridad de conmutación por error debe ser inferior al número total de regiones. 

Cree un archivo de plantilla local similar al siguiente que coincida con la configuración actual de la región de Azure Cosmos DB. La matriz "locations" debería contener solo las regiones que permanecerán después de la eliminación de la región. **La ubicación omitida se quitará de la cuenta de Azure Cosmos DB**. Asigne al archivo el nombre azuredeploy.json.

    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "databaseAccountName": {
                "type": "string"
            },
            "locationName1": {
                "type": "string"
            }
        },
        "variables": {},
        "resources": [
            {
                "apiVersion": "2015-04-08",
                "type": "Microsoft.DocumentDb/databaseAccounts",
                "name": "[parameters('databaseAccountName')]",
                "location": "[resourceGroup().location]",
                "properties": {
                    "databaseAccountOfferType": "Standard",
                    "ipRangeFilter": "",
                    "locations": [
                        {
                            "failoverPriority": 0,
                            "locationName": "[parameters('locationName1')]"
                        }
                    ]
                }
            }
        ]
    }

Puede escribir los valores de los parámetros en la línea de comandos o crear un archivo de parámetros para especificar el valor.

Para crear un archivo de parámetros, copie el siguiente contenido en un nuevo archivo y asigne al archivo el nombre azuredeploy.parameters.json. Si va a especificar el nombre de cuenta de base de datos en el símbolo del sistema, puede continuar sin crear este archivo. Asegúrese de agregar los parámetros necesarios que se definen en la plantilla de Azure Resource Manager.

    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "databaseAccountName": {
                "value": "samplearmacct"
            },
            "locationName1": {
                "value": "westus"
            }
        }
    }

En el archivo azuredeploy.parameters.json, actualice el campo de valor de `"databaseAccountName"` al nombre de base de datos que desee usar y, después, guarde el archivo. `"databaseAccountName"` solo admite minúsculas, números, el carácter "-", y debe tener entre 3 y 50 caracteres. Actualice el campo de valor de `"locationName1"` a las regiones donde desea que exista la cuenta de Azure Cosmos DB una vez quitada la región.

Para crear una cuenta de Azure Cosmos DB en el grupo de recursos, ejecute el comando siguiente e indique la ruta de acceso al archivo de plantilla, la ruta de acceso al archivo de parámetros o el valor del parámetro, el nombre del grupo de recursos en donde se va a implementar y un nombre de implementación (-n es opcional). 

Para usar un archivo de parámetros:

    azure group deployment create -f <PathToTemplate> -e <PathToParameterFile> -g <resourcegroupname> -n <deploymentname>

 - `<PathToTemplate>` es la ruta de acceso al archivo azuredeploy.json creado en el paso 1. Si el nombre de ruta de acceso contiene espacios, ponga comillas dobles alrededor de este parámetro.
 - `<PathToParameterFile>` es la ruta de acceso al archivo azuredeploy.parameters.json creado en el paso 1. Si el nombre de ruta de acceso contiene espacios, ponga comillas dobles alrededor de este parámetro.
 - `<resourcegroupname>` es el nombre del grupo de recursos existente al que se agrega una cuenta de base de datos de Azure Cosmos DB. 
 - `<deploymentname>` es el nombre opcional de la implementación.

Entrada de ejemplo: 

    azure group deployment create -f azuredeploy.json -e azuredeploy.parameters.json -g new_res_group -n azuredeploy

O bien, para especificar el parámetro de nombre de cuenta de base de datos sin un archivo de parámetros y que en su lugar se le pida el valor, ejecute el siguiente comando:

    azure group deployment create -f <PathToTemplate> -g <resourcegroupname> -n <deploymentname>

Entrada de ejemplo que muestra el símbolo del sistema y una entrada para una cuenta de base de datos denominada "samplearmacct":

    azure group deployment create -f azuredeploy.json -g new_res_group -n azuredeploy
    info:    Executing command group deployment create
    info:    Supply values for the following parameters
    databaseAccountName: samplearmacct

Cuando se haya aprovisionado la cuenta, aparecerá la siguiente información: 

    info:    Executing command group deployment create
    + Initializing template configurations and parameters
    + Creating a deployment
    info:    Created template deployment "azuredeploy"
    + Waiting for deployment to complete
    + 
    + 
    info:    Resource 'new_res_group' of type 'Microsoft.DocumentDb/databaseAccounts' provisioning status is Running
    + 
    info:    Resource 'new_res_group' of type 'Microsoft.DocumentDb/databaseAccounts' provisioning status is Succeeded
    data:    DeploymentName     : azuredeploy
    data:    ResourceGroupName  : new_res_group
    data:    ProvisioningState  : Succeeded
    data:    Timestamp          : 2015-11-30T18:50:23.6300288Z
    data:    Mode               : Incremental
    data:    CorrelationId      : 4a5d4049-c494-4053-bad4-cc804d454700
    data:    DeploymentParameters :
    data:    Name                 Type    Value
    data:    -------------------  ------  ------------------
    data:    databaseAccountName  String  samplearmacct
    data:    locationName1        String  westus
    info:    group deployment create command OK

Si se producen errores, consulte [Solución de problemas](#troubleshooting).  

Cuando el comando responda, la cuenta se encontrará en el estado **Actualizando** unos minutos antes de pasar al estado **En línea**, en el que está lista para usarse. Puede comprobar el estado de la cuenta en [Azure Portal](https://portal.azure.com), en la hoja **Azure Cosmos DB Accounts** (Cuantas de Azure Cosmos DB).

## <a name="troubleshooting"></a>Solución de problemas

Si recibe errores como `Deployment provisioning state was not successful` al crear la cuenta de base de datos o de grupo de recursos, dispone de varias opciones de solución de problemas. 

> [!NOTE]
> Si se indican caracteres incorrectos en el nombre de la cuenta de base de datos o una ubicación en la que Azure Cosmos DB no está disponible se provocarán errores de implementación. Los nombres de cuenta de base de datos solo pueden incluir minúsculas, números y el carácter "-"; y deben tener entre 3 y 50 caracteres. Se enumeran todas las ubicaciones de cuenta de base de datos válidas en la [página Regiones de Azure](https://azure.microsoft.com/regions/#services).

- Si la salida contiene lo siguiente: `Error information has been recorded to C:\Users\wendy\.azure\azure.err`, revise la información del error en el archivo azure.err.

- Puede encontrar información útil en el archivo de registro para el grupo de recursos. Para ver el archivo de registro, ejecute el siguiente comando:

        azure group log show <resourcegroupname> --last-deployment

    Entrada de ejemplo:        azure group log show new_res_group --last-deployment

    Después, consulte [Solución de problemas de implementaciones de grupo de recursos en Azure](../azure-resource-manager/resource-manager-common-deployment-errors.md) para más información.

- También hay información del error disponible en el Portal de Azure, como se muestra en la captura de pantalla siguiente. Para navegar a la información de error: haga clic en Grupos de recursos en la barra de salto, seleccione el grupo de recursos que tuvo el error. En el área Essentials de la hoja Grupo de recursos, haga clic en la fecha de la última implementación. En la hoja Historial de implementaciones, seleccione la implementación con errores. En la hoja Implementación, haga clic en los detalles de la operación con el signo de exclamación rojo. El mensaje de estado para la implementación con errores se muestra en la hoja Detalles de la operación. 

    ![Captura de pantalla del Portal de Azure que muestra cómo navegar hasta el mensaje de error de implementación](media/documentdb-automation-resource-manager-cli/portal-troubleshooting-deploy.png) 

## <a name="next-steps"></a>Pasos siguientes

Tras crear una cuenta de Azure Cosmos DB, el siguiente paso es crear una base de datos de Azure Cosmos DB. Puede crear una base de datos mediante uno de los siguientes procedimientos:

- Azure Portal, tal y como se describe en [Create an Azure Cosmos DB collection and database using the Azure portal](documentdb-create-collection.md) (Creación de una colección y una base de datos Azure Cosmos DB mediante Azure Portal).
- Los ejemplos de C# .NET del proyecto [DatabaseManagement](https://github.com/Azure/azure-documentdb-net/tree/master/samples/code-samples/DatabaseManagement) del repositorio [azure-documentdb-dotnet](https://github.com/Azure/azure-documentdb-net/tree/master/samples/code-samples) de GitHub.
- Los [SDK de Azure Cosmos DB](https://msdn.microsoft.com/library/azure/dn781482.aspx). Las API de DocumentDB de Azure Cosmos DB tienen los SDK de la API de .NET, Java, Python, Node.js y JavaScript. 

Después de crear la base de datos, hay que [agregar una o varias colecciones](documentdb-create-collection.md) a la base de datos y, después, [incorporar documentos](documentdb-view-json-document-explorer.md) a las colecciones. 

Cuando tenga documentos en una colección, puede usar [SQL](documentdb-sql-query.md) para [ejecutar consultas](documentdb-sql-query.md#ExecutingSqlQueries) en los documentos mediante el [Explorador de consultas](documentdb-query-collections-query-explorer.md) del portal, la [API de REST](https://msdn.microsoft.com/library/azure/dn781481.aspx) o uno de los [SDK](https://msdn.microsoft.com/library/azure/dn781482.aspx).

Para obtener más información sobre Azure Cosmos DB, consulte los siguientes recursos:

-    [Introducción a Azure Cosmos DB](../cosmos-db/introduction.md)

Para obtener más plantillas que puede usar, consulte [Plantillas de inicio rápido de Azure](https://azure.microsoft.com/documentation/templates/).

<!--Reference style links - using these makes the source content way more readable than using inline links-->
[distribute-globally]: https://azure.microsoft.com/documentation/articles/documentdb-distribute-data-globally
[scaling-globally]: https://azure.microsoft.com/documentation/articles/documentdb-distribute-data-globally/#scaling-across-the-planet

