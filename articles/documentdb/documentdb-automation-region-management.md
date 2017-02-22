---
title: "Automatización de DocumentDB: administración de regiones | Microsoft Docs"
description: Utilice la CLI de Azure y Azure Resource Manager para administrar las regiones en una cuenta de base de datos de DocumentDB. DocumentDB es una base de datos NoSQL basada en la nube para datos JSON.
services: documentdb
author: dmakwana
manager: jhubbard
editor: 
tags: azure-resource-manager
documentationcenter: 
ms.assetid: 7f765c17-8549-4108-9475-46394fc3a218
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/20/2016
ms.author: dimakwan
translationtype: Human Translation
ms.sourcegitcommit: 0782000e87bed0d881be5238c1b91f89a970682c
ms.openlocfilehash: cca2c112924c22846d5a00e0a94181669fb4cbc0


---
# <a name="automate-documentdb-account-region-management-using-azure-cli-and-azure-resource-manager-templates"></a>Automatización de la administración de las regiones de cuentas de DocumentDB con la CLI de Azure y las plantillas de Azure Resource Manager

En este artículo se muestra cómo agregar o quitar una región en la cuenta de Azure DocumentDB mediante comandos de la CLI de Azure y plantillas de Azure Resource Manager. La administración de las regiones también se puede llevar a cabo a través de [Azure Portal](documentdb-portal-global-replication.md). Tenga en cuenta que los comandos del tutorial siguiente no le permiten cambiar las prioridades de conmutación por error de las distintas regiones. Solo pueden agregarse o quitarse las regiones de lectura. La región de escritura de una cuenta de base de datos (prioridad de conmutación por error de 0) no se puede agregar ni quitar.

Las cuentas de base de datos de DocumentDB son, en este momento, el único recurso de DocumentDB que se puede crear o modificar con [plantillas de Azure Resource Manager y la CLI de Azure](documentdb-automation-resource-manager-cli.md).

## <a name="getting-ready"></a>Preparación

Para poder usar la CLI de Azure con grupos de recursos de Azure, necesitará la versión correcta de la CLI de Azure y una cuenta de Azure. Si no tiene la CLI de Azure, debe [instalarla](../xplat-cli-install.md).

### <a name="update-your-azure-cli-version"></a>Actualización de la versión de la CLI de Azure

En el símbolo del sistema, escriba `azure --version` para ver si ya tiene instalada la versión 0.10.4 o una posterior. Es posible que se le pida su participación en la recopilación de datos de la CLI de Microsoft Azure en este paso, y puede optar por hacerlo o no.

    azure --version
    0.10.4 (node: 4.2.4)

Si la versión no es 0.10.4 o una posterior, tendrá que [instalar la CLI de Azure](../xplat-cli-install.md) o actualizarla mediante uno de los instaladores nativos o a través de `npm update -g azure-cli`npm`npm install -g azure-cli`. Escriba ** para actualizarla o ** para instalarla.

### <a name="set-your-azure-account-and-subscription"></a>Definición de su cuenta y suscripción de Azure

Si aún no tiene una suscripción a Azure pero sí una suscripción a Visual Studio, puede activar sus [ventajas para los suscriptores de Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/). O puede suscribirse a una [evaluación gratuita](https://azure.microsoft.com/pricing/free-trial/).

Para usar plantillas de Administración de recursos de Azure, debe tener una identidad de cuentas Microsoft o de cuenta profesional o educativa. Si tiene una de estas cuentas, escriba el siguiente comando.

    azure login

Lo que genera el siguiente resultado: 

    info:    Executing command login
    |info:    To sign in, use a web browser to open the page https://aka.ms/devicelogin. 
    Enter the code E1A2B3C4D to authenticate.

> [!NOTE]
> Si no tiene una cuenta de Azure, verá un mensaje de error que indica que necesita un otro tipo de cuenta. Para crear una a partir su cuenta de Azure actual, consulte [Crear una identidad profesional o educativa en Azure Active Directory](../virtual-machines/virtual-machines-windows-create-aad-work-id.md).

Abra [https://aka.ms/devicelogin](https://aka.ms/devicelogin) en un explorador y escriba el código proporcionado en la salida del comando.

![Captura de pantalla que muestra la pantalla de inicio de sesión del dispositivo para la CLI de Microsoft Azure](media/documentdb-automation-resource-manager-cli/azure-cli-login-code.png)

Una vez escrito el código, seleccione la identidad que quiere usar en el explorador y, si es necesario, indique su nombre de usuario y contraseña.

![Captura de pantalla que muestra en donde debe seleccionar la cuenta de identidad de Microsoft asociada a la suscripción de Azure que quiere usar](media/documentdb-automation-resource-manager-cli/identity-cli-login.png)

Obtendrá la siguiente pantalla de confirmación cuando se haya conectado; luego puede cerrar la ventana del explorador.

![Captura de pantalla que muestra la confirmación del inicio de sesión en la interfaz de línea de comandos multiplataforma de Microsoft Azure](media/documentdb-automation-resource-manager-cli/login-confirmation.png)

El shell de comandos también proporciona el siguiente resultado.

    /info:    Added subscription Visual Studio Ultimate with MSDN
    info:    Setting subscription "Visual Studio Ultimate with MSDN" as default
    +
    info:    login command OK

Además del método de inicio de sesión interactivo que se describe aquí, existen otros métodos de inicio de sesión en la CLI de Azure. Para más información sobre otros métodos y sobre el control de varias suscripciones, consulte [Conexión a una suscripción de Azure desde la interfaz de la línea de comandos de Azure (CLI de Azure)](../xplat-cli-connect.md).

### <a name="switch-to-the-azure-cli-resource-group-mode"></a>Cambio al modo de grupo de recursos de CLI de Azure

De manera predeterminada, la CLI de Azure se inicia en el modo de administración de servicios (modo**asm** ). Escriba lo siguiente para cambiar al modo de grupo de recursos.

    azure config mode arm

Lo que genera el siguiente resultado:

    info:    Executing command config mode
    info:    New mode is arm
    info:    config mode command OK

Si fuera necesario, puede volver a cambiar al conjunto predeterminado de comandos escribiendo `azure config mode asm`.

### <a name="create-or-retrieve-your-resource-group"></a>Creación o recuperación del grupo de recursos

Para crear una cuenta de DocumentDB, necesita primero un grupo de recursos. Si ya conoce el nombre del grupo de recursos que quiere usar, vaya al [paso 2](#create-documentdb-account-cli). 

Para revisar una lista de todos los grupos de recursos actuales, ejecute el siguiente comando y tome nota del nombre del grupo de recursos que quiere usar: 

    azure group list

Para crear un grupo de recursos, ejecute el siguiente comando, especifique el nombre del nuevo grupo de recursos que se va crear y la región en la que se va a crear: 

    azure group create <resourcegroupname> <resourcegrouplocation>

 - `<resourcegroupname>` solo admite caracteres alfanuméricos, puntos, caracteres de subrayado, el carácter "-" y paréntesis, y no puede terminar en punto. 
 - `<resourcegrouplocation>` debe ser una de las regiones en que DocumentDB está disponible de forma general. Se proporciona la lista actual de regiones en la [página Regiones de Azure](https://azure.microsoft.com/regions/#services).

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

La mayoría de las aplicaciones se desarrollan a partir de una combinación de distintos tipos de recursos (por ejemplo, una o varias cuentas de DocumentDB, cuentas de almacenamiento, una red virtual o una red de entrega de contenido). La API de administración de servicios de Azure predeterminada y el portal de Azure clásico representan estos elementos mediante un enfoque de servicio por servicio, que requiere implementar y administrar servicios individuales (o buscar otras herramientas que lo hagan) y no como una unidad lógica de implementación.

*Las plantillas del Administrador de recursos de Azure* permiten implementar y administrar estos recursos diferentes como una unidad lógica de implementación de manera declarativa. En lugar de indicar imperativamente a Azure que debe implementar un comando tras otro, describa la implementación completa en un archivo JSON (todos los recursos y configuración asociada y parámetros de implementación) e indíquele a Azure que implemente esos recursos como un único grupo.

Puede obtener mucha más información sobre los grupos de recursos de Azure y su utilidad en [Información general de Azure Resource Manager](../azure-resource-manager/resource-group-overview.md). Si está interesado en la creación de plantillas, consulte [Creación de plantillas del Administrador de recursos de Azure](../azure-resource-manager/resource-group-authoring-templates.md).


## <a name="a-idadd-region-documentdb-accountatask-add-region-to-a-documentdb-account"></a><a id="add-region-documentdb-account"></a>Tarea: agregar una región a una cuenta de DocumentDB

DocumentDB puede [distribuir a escala internacional los datos][distribute-globally] entre diversas [regiones de Azure](https://azure.microsoft.com/regions/#services). Las instrucciones de esta sección describen cómo agregar una región de lectura a una cuenta de DocumentDB existente con la CLI de Azure y plantillas de Resource Manager. Puede hacerlo utilizando la CLI de Azure con o las plantillas de Resource Manager o sin ellas.

### <a name="a-idadd-region-documentdb-account-clia-add-region-to-a-documentdb-account-using-azure-cli-without-resource-manager-templates"></a><a id="add-region-documentdb-account-cli"></a> Incorporación de una región a una cuenta de DocumentDB utilizando la CLI de Azure sin las plantillas de Resource Manager

Agregue una región a una cuenta de DocumentDB existente en el grupo de recursos nuevo o existente escribiendo el siguiente comando en el símbolo del sistema. Tenga en cuenta que la matriz "locations" debe reflejar la configuración de la región actual dentro de la cuenta de DocumentDB con la excepción de la nueva región que se va a agregar. El ejemplo siguiente muestra un comando para agregar otra región a la cuenta.

Los valores de prioridad de conmutación por error deben coincidir con la configuración existente. Una de las regiones debe tener un valor de failoverPriority de 0 para indicar que esta región se mantendrá como la [región de escritura de la cuenta de DocumentDB][scaling-globally]. Los valores de prioridad de conmutación por error deben ser únicos entre las ubicaciones. Además, el valor máximo de prioridad de conmutación por error debe ser inferior al número total de regiones. La nueva región será una región de tipo "Lectura" y debe tener un valor de prioridad de conmutación por error mayor que 0.

> [!TIP]
> Si ejecuta este comando en Azure PowerShell o Windows PowerShell, recibirá un error sobre un token inesperado. En su lugar, ejecute este comando en el símbolo del sistema de Windows.

    azure resource create -g <resourcegroupname> -n <databaseaccountname> -r "Microsoft.DocumentDB/databaseAccounts" -o 2015-04-08 -l <resourcegrouplocation> -p "{\"databaseAccountOfferType\":\"Standard\",\"ipRangeFilter\":\"<ip-range-filter>\",\"locations\":["{\"locationName\":\"<databaseaccountlocation>\",\"failoverPriority\":\"<failoverPriority1>\"},{\"locationName\":\"<newdatabaseaccountlocation>\",\"failoverPriority\":\"<failoverPriority2>\"}"]}"

 - `<resourcegroupname>` solo admite caracteres alfanuméricos, puntos, caracteres de subrayado, el carácter "-" y paréntesis, y no puede terminar en punto.
 - `<resourcegrouplocation>` es la región del grupo de recursos actual.
 - `<ip-range-filter>` Especifica el conjunto de direcciones IP o intervalos de direcciones IP en formato CIDR para incluir en la lista permitida de direcciones IP de cliente para una cuenta de base de datos dada. Los intervalos o direcciones IP deben ir separados por una coma y no deben contener espacios. Para más información, consulte [Compatibilidad con el firewall de DocumentDB](documentdb-firewall-support.md).
 - `<databaseaccountname>` solo admite minúsculas, números, el carácter "-", y debe tener entre 3 y 50 caracteres.
 - `<databaseaccountlocation>` debe ser una de las regiones en que DocumentDB está disponible de forma general. Se proporciona la lista actual de regiones en la [página Regiones de Azure](https://azure.microsoft.com/regions/#services).
 - `<newdatabaseaccountlocation>` es la nueva región que se agregará y debe ser una de las regiones en que DocumentDB está disponible de forma general. Se proporciona la lista actual de regiones en la [página Regiones de Azure](https://azure.microsoft.com/regions/#services).


Ejemplo de entrada para agregar la región "este de EE. UU." como una región de lectura en la cuenta de DocumentDB: 

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

Cuando el comando responda, la cuenta se encontrará en el estado **Creando** unos minutos antes de pasar al estado **En línea**, en el que está lista para usarse. Puede comprobar el estado de la cuenta en el [Portal de Azure](https://portal.azure.com), en la hoja **Cuentas de DocumentDB** .

### <a name="a-idadd-region-documentdb-account-cli-arma-add-region-to-a-documentdb-account-using-azure-cli-with-resource-manager-templates"></a><a id="add-region-documentdb-account-cli-arm"></a> Incorporación de una región a una cuenta de DocumentDB utilizando la CLI de Azure con las plantillas de Resource Manager

En las instrucciones de esta sección se describe cómo agregar una región a una cuenta de DocumentDB existente con una plantilla de Azure Resource Manager y un archivo opcional de parámetros (los dos recursos son archivos JSON). El uso de una plantilla le permite describir exactamente lo que desea y repetirlo sin errores.

Los valores de prioridad de conmutación por error deben coincidir con la configuración existente. Una de las regiones debe tener un valor de failoverPriority de 0 para indicar que esta región se mantendrá como la [región de escritura de la cuenta de DocumentDB][scaling-globally]. Los valores de prioridad de conmutación por error deben ser únicos entre las ubicaciones. Además, el valor máximo de prioridad de conmutación por error debe ser inferior al número total de regiones. La nueva región será una región de tipo "Lectura" y debe tener un valor de prioridad de conmutación por error mayor que 0.

Cree un archivo de plantilla local similar al siguiente que coincida con la configuración actual de la región de DocumentDB. La matriz "locations" debe contener todas las regiones existentes en la cuenta de base de datos junto con la nueva región que se va a agregar. Asigne al archivo el nombre azuredeploy.json.

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

El archivo de plantilla anterior muestra un ejemplo en el que se agrega una nueva región a una cuenta de DocumentDB que ya tiene dos regiones.

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

En el archivo azuredeploy.parameters.json, actualice el campo de valor de `"databaseAccountName"` al nombre de base de datos que desee usar y, después, guarde el archivo. `"databaseAccountName"` solo admite minúsculas, números, el carácter "-", y debe tener entre 3 y 50 caracteres. Actualice los campos de valor de `"locationName1"` y `"locationName2"` a las regiones donde existe su cuenta de DocumentDB. Actualice el campo de valor de `"newLocationName"` a la región que desea agregar.

Para crear una cuenta de DocumentDB en el grupo de recursos, ejecute el comando siguiente e indique la ruta de acceso al archivo de plantilla, la ruta de acceso al archivo de parámetros o el valor del parámetro, el nombre del grupo de recursos en el que se va a implementar y un nombre de implementación (-n es opcional). 

Para usar un archivo de parámetros:

    azure group deployment create -f <PathToTemplate> -e <PathToParameterFile> -g <resourcegroupname> -n <deploymentname>

 - `<PathToTemplate>` es la ruta de acceso al archivo azuredeploy.json creado en el paso 1. Si el nombre de ruta de acceso contiene espacios, ponga comillas dobles alrededor de este parámetro.
 - `<PathToParameterFile>` es la ruta de acceso al archivo azuredeploy.parameters.json creado en el paso 1. Si el nombre de ruta de acceso contiene espacios, ponga comillas dobles alrededor de este parámetro.
 - `<resourcegroupname>` es el nombre del grupo de recursos existente en el que se va a agregar una cuenta de base de datos de DocumentDB. 
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
    data:    locationName1        String  westus
    data:    locationName2        String  eastus
    data:    newLocationName      String  eastus
    data:    databaseAccountName  String  samplearmacct
    info:    group deployment create command OK

Si se producen errores, consulte [Solución de problemas](#troubleshooting).  

Cuando el comando responda, la cuenta se encontrará en el estado **Creando** unos minutos antes de pasar al estado **En línea**, en el que está lista para usarse. Puede comprobar el estado de la cuenta en el [Portal de Azure](https://portal.azure.com), en la hoja **Cuentas de DocumentDB** .

## <a name="a-idremove-region-documentdb-accountatask-remove-region-from-a-documentdb-account"></a><a id="remove-region-documentdb-account"></a>Tarea: quitar una región de una cuenta de DocumentDB

DocumentDB puede [distribuir a escala internacional los datos][distribute-globally] entre diversas [regiones de Azure](https://azure.microsoft.com/regions/#services). Las instrucciones de esta sección describen cómo quitar una región de una cuenta de DocumentDB existente con la CLI de Azure y las plantillas de Resource Manager. Puede hacerlo utilizando la CLI de Azure con o las plantillas de Resource Manager o sin ellas.

### <a name="a-idremove-region-documentdb-account-clia-remove-region-to-a-documentdb-account-using-azure-cli-without-resource-manager-templates"></a><a id="remove-region-documentdb-account-cli"></a> Retirada de una región de una cuenta de DocumentDB utilizando la CLI de Azure sin las plantillas de Resource Manager

Para quitar una región de una cuenta de DocumentDB existente, se puede ejecutar el comando siguiente con la CLI de Azure. La matriz "locations" debería contener solo las regiones que permanecerán después de la eliminación de la región. **La ubicación omitida se quitará de la cuenta de DocumentDB**. Escriba el comando siguiente en el símbolo del sistema.

Una de las regiones debe tener un valor de failoverPriority de 0 para indicar que esta región se mantendrá como la [región de escritura de la cuenta de DocumentDB][scaling-globally]. Los valores de prioridad de conmutación por error deben ser únicos entre las ubicaciones. Además, el valor máximo de prioridad de conmutación por error debe ser inferior al número total de regiones. 

> [!TIP]
> Si ejecuta este comando en Azure PowerShell o Windows PowerShell, recibirá un error sobre un token inesperado. En su lugar, ejecute este comando en el símbolo del sistema de Windows.

    azure resource create -g <resourcegroupname> -n <databaseaccountname> -r "Microsoft.DocumentDB/databaseAccounts" -o 2015-04-08 -l <resourcegrouplocation> -p "{\"databaseAccountOfferType\":\"Standard\",\"ipRangeFilter\":\"<ip-range-filter>\",\"locations\":["{\"locationName\":\"<databaseaccountlocation>\",\"failoverPriority\":\"<failoverPriority>\"}"]}"

 - `<resourcegroupname>` solo admite caracteres alfanuméricos, puntos, caracteres de subrayado, el carácter "-" y paréntesis, y no puede terminar en punto.
 - `<resourcegrouplocation>` es la región del grupo de recursos actual.
 - `<ip-range-filter>` Especifica el conjunto de direcciones IP o intervalos de direcciones IP en formato CIDR para incluir en la lista permitida de direcciones IP de cliente para una cuenta de base de datos dada. Los intervalos o direcciones IP deben ir separados por una coma y no deben contener espacios. Para más información, consulte [Compatibilidad con el firewall de DocumentDB](documentdb-firewall-support.md).
 - `<databaseaccountname>` solo admite minúsculas, números, el carácter "-", y debe tener entre 3 y 50 caracteres.
 - `<databaseaccountlocation>` debe ser una de las regiones en que DocumentDB está disponible de forma general. Se proporciona la lista actual de regiones en la [página Regiones de Azure](https://azure.microsoft.com/regions/#services).

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

Cuando el comando responda, la cuenta se encontrará en el estado **Actualizando** unos minutos antes de pasar al estado **En línea**, en el que está lista para usarse. Puede comprobar el estado de la cuenta en el [Portal de Azure](https://portal.azure.com), en la hoja **Cuentas de DocumentDB** .

### <a name="a-idremove-region-documentdb-account-cli-arma-remove-region-from-a-documentdb-account-using-azure-cli-with-resource-manager-templates"></a><a id="remove-region-documentdb-account-cli-arm"></a> Retirada de una región a una cuenta de DocumentDB utilizando la CLI de Azure con las plantillas de Resource Manager

En las instrucciones de esta sección se describe cómo retirar una región de una cuenta de DocumentDB existente con una plantilla de Azure Resource Manager y un archivo opcional de parámetros (los dos recursos son archivos JSON). El uso de una plantilla le permite describir exactamente lo que desea y repetirlo sin errores.

Una de las regiones debe tener un valor de failoverPriority de 0 para indicar que esta región se mantendrá como la [región de escritura de la cuenta de DocumentDB][scaling-globally]. Los valores de prioridad de conmutación por error deben ser únicos entre las ubicaciones. Además, el valor máximo de prioridad de conmutación por error debe ser inferior al número total de regiones. 

Cree un archivo de plantilla local similar al siguiente que coincida con la configuración actual de la región de DocumentDB. La matriz "locations" debería contener solo las regiones que permanecerán después de la eliminación de la región. **La ubicación omitida se quitará de la cuenta de DocumentDB**. Asigne al archivo el nombre azuredeploy.json.

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

En el archivo azuredeploy.parameters.json, actualice el campo de valor de `"databaseAccountName"` al nombre de base de datos que desee usar y, después, guarde el archivo. `"databaseAccountName"` solo admite minúsculas, números, el carácter "-", y debe tener entre 3 y 50 caracteres. Actualice el campo de valor de `"locationName1"` a las regiones donde desea que exista la cuenta de DocumentDB una vez quitada la región.

Para crear una cuenta de DocumentDB en el grupo de recursos, ejecute el comando siguiente e indique la ruta de acceso al archivo de plantilla, la ruta de acceso al archivo de parámetros o el valor del parámetro, el nombre del grupo de recursos en el que se va a implementar y un nombre de implementación (-n es opcional). 

Para usar un archivo de parámetros:

    azure group deployment create -f <PathToTemplate> -e <PathToParameterFile> -g <resourcegroupname> -n <deploymentname>

 - `<PathToTemplate>` es la ruta de acceso al archivo azuredeploy.json creado en el paso 1. Si el nombre de ruta de acceso contiene espacios, ponga comillas dobles alrededor de este parámetro.
 - `<PathToParameterFile>` es la ruta de acceso al archivo azuredeploy.parameters.json creado en el paso 1. Si el nombre de ruta de acceso contiene espacios, ponga comillas dobles alrededor de este parámetro.
 - `<resourcegroupname>` es el nombre del grupo de recursos existente en el que se va a agregar una cuenta de base de datos de DocumentDB. 
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

Cuando el comando responda, la cuenta se encontrará en el estado **Actualizando** unos minutos antes de pasar al estado **En línea**, en el que está lista para usarse. Puede comprobar el estado de la cuenta en el [Portal de Azure](https://portal.azure.com), en la hoja **Cuentas de DocumentDB** .

## <a name="troubleshooting"></a>Solución de problemas

Si recibe errores como `Deployment provisioning state was not successful` al crear la cuenta de base de datos o de grupo de recursos, dispone de varias opciones de solución de problemas. 

> [!NOTE]
> Si se proporcionan caracteres incorrectos en el nombre de la cuenta de base de datos o una ubicación en la que DocumentDB no está disponible, esto causará errores de implementación. Los nombres de cuenta de base de datos solo pueden incluir minúsculas, números y el carácter "-"; y deben tener entre 3 y 50 caracteres. Se enumeran todas las ubicaciones de cuenta de base de datos válidas en la [página Regiones de Azure](https://azure.microsoft.com/regions/#services).

- Si la salida contiene lo siguiente: `Error information has been recorded to C:\Users\wendy\.azure\azure.err`, revise la información del error en el archivo azure.err.

- Puede encontrar información útil en el archivo de registro para el grupo de recursos. Para ver el archivo de registro, ejecute el siguiente comando:

        azure group log show <resourcegroupname> --last-deployment

    Entrada de ejemplo:        azure group log show new_res_group --last-deployment

    Después, consulte [Solución de problemas de implementaciones de grupo de recursos en Azure](../azure-resource-manager/resource-manager-common-deployment-errors.md) para más información.

- También hay información del error disponible en el Portal de Azure, como se muestra en la captura de pantalla siguiente. Para navegar a la información de error: haga clic en Grupos de recursos en la barra de salto, seleccione el grupo de recursos que tuvo el error. En el área Essentials de la hoja Grupo de recursos, haga clic en la fecha de la última implementación. En la hoja Historial de implementaciones, seleccione la implementación con errores. En la hoja Implementación, haga clic en los detalles de la operación con el signo de exclamación rojo. El mensaje de estado para la implementación con errores se muestra en la hoja Detalles de la operación. 

    ![Captura de pantalla del Portal de Azure que muestra cómo navegar hasta el mensaje de error de implementación](media/documentdb-automation-resource-manager-cli/portal-troubleshooting-deploy.png) 

## <a name="next-steps"></a>Pasos siguientes

Ahora que tiene una cuenta de DocumentDB, el siguiente paso es crear una base de datos de DocumentDB. Puede crear una base de datos mediante uno de los siguientes procedimientos:

- Azure Portal, tal como se describe en [Creación de una colección y una base de datos de DocumentDB](documentdb-create-collection.md).
- Los ejemplos de C# .NET del proyecto [DatabaseManagement](https://github.com/Azure/azure-documentdb-net/tree/master/samples/code-samples/DatabaseManagement) del repositorio [azure-documentdb-dotnet](https://github.com/Azure/azure-documentdb-net/tree/master/samples/code-samples) de GitHub.
- Los [SDK de DocumentDB](https://msdn.microsoft.com/library/azure/dn781482.aspx). DocumentDB tiene .NET, Java, Python, Node.js y SDK de la API de JavaScript. 

Después de crear la base de datos, hay que [agregar una o varias colecciones](documentdb-create-collection.md) a la base de datos y, después, [incorporar documentos](documentdb-view-json-document-explorer.md) a las colecciones. 

Cuando tenga documentos en una colección, puede usar [SQL de DocumentDB](documentdb-sql-query.md) para [ejecutar consultas](documentdb-sql-query.md#executing-sql-queries) en sus documentos mediante el [Explorador de consultas](documentdb-query-collections-query-explorer.md) del portal, la [API de REST](https://msdn.microsoft.com/library/azure/dn781481.aspx) o uno de los [SDK](https://msdn.microsoft.com/library/azure/dn781482.aspx).

Para más información acerca de DocumentDB, consulte estos recursos:

-   [Ruta de aprendizaje de DocumentDB](https://azure.microsoft.com/documentation/learning-paths/documentdb/)
-   [Modelo de recursos y conceptos de DocumentDB](documentdb-resources.md)

Para obtener más plantillas que puede usar, consulte [Plantillas de inicio rápido de Azure](https://azure.microsoft.com/documentation/templates/).

<!--Reference style links - using these makes the source content way more readable than using inline links-->
[distribute-globally]: https://azure.microsoft.com/en-us/documentation/articles/documentdb-distribute-data-globally
[scaling-globally]: https://azure.microsoft.com/en-us/documentation/articles/documentdb-distribute-data-globally/#scaling-across-the-planet



<!--HONumber=Jan17_HO2-->


