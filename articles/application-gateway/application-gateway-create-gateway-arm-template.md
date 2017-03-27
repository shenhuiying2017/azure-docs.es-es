---
title: "Creación de una instancia de Azure Application Gateway mediante plantillas | Microsoft Docs"
description: "En esta página se ofrecen instrucciones para crear una puerta de enlace de aplicaciones de Azure con la plantilla del Administrador de recursos de Azure"
documentationcenter: na
services: application-gateway
author: georgewallace
manager: timlt
editor: tysonn
ms.assetid: 8192ee25-d9f0-4b32-a45e-1d74629c54e5
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/23/2017
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: 02b758b06332abe190b322c83da7c4135d358fa4
ms.lasthandoff: 03/21/2017


---
# <a name="create-an-application-gateway-by-using-the-azure-resource-manager-template"></a>Creación de una puerta de enlace de aplicaciones con la plantilla de Administrador de recursos de Azure

> [!div class="op_single_selector"]
> * [Portal de Azure](application-gateway-create-gateway-portal.md)
> * [PowerShell del Administrador de recursos de Azure](application-gateway-create-gateway-arm.md)
> * [Azure Classic PowerShell](application-gateway-create-gateway.md)
> * [Plantilla del Administrador de recursos de Azure](application-gateway-create-gateway-arm-template.md)
> * [CLI de Azure](application-gateway-create-gateway-cli.md)

Puerta de enlace de aplicaciones de Azure es un equilibrador de carga de nivel&7;. Proporciona conmutación por error, solicitudes HTTP de enrutamiento de rendimiento entre distintos servidores, independientemente de que se encuentren en la nube o en una implementación local. Application Gateway proporciona numerosas características del Controlador de entrega de aplicaciones (ADC), entre las que se incluyen el equilibrio de carga HTTP, la afinidad de sesiones basada en cookies, la descarga SSL (Capa de sockets seguros), los sondeos personalizados sobre el estado, la compatibilidad con multisitio, etc. Para obtener una lista completa de las características admitidas, visite [Introducción a Application Gateway](application-gateway-introduction.md)

Aprenderá a descargar y modificar una plantilla de Azure Resource Manager desde GitHub, así como a implementarla desde GitHub, PowerShell y la CLI de Azure.

Si simplemente va a implementar la plantilla de Administrador de recursos de Azure directamente desde GitHub sin realizar ningún cambio, vaya a la sección sobre la implementación una plantilla desde GitHub.

## <a name="scenario"></a>Escenario

En este escenario:

* Creará una puerta de enlace de aplicaciones con dos instancias.
* Creará una red virtual denominada VirtualNetwork1 con un bloque CIDR reservado de 10.0.0.0/16.
* Creará una subred denominada Appgatewaysubnet que usa 10.0.0.0/28 como bloque CIDR.
* Instalará dos IP de back-end configuradas con anterioridad para los servidores web cuya carga de tráfico desea equilibrar. En esta plantilla de ejemplo, las IP de back-end son 10.0.1.10 y 10.0.1.11.

> [!NOTE]
> Esos son los parámetros para esta plantilla. Para personalizar la plantilla, puede cambiar las reglas, el agente de escucha y la SSL que abre azuredeploy.json.

![Escenario](./media/application-gateway-create-gateway-arm-template/scenario.png)

## <a name="download-and-understand-the-azure-resource-manager-template"></a>Descarga e información sobre la plantilla del Administrador de recursos de Azure

Puede descargar la plantilla del Administrador de recursos de Azure existente para crear una red virtual y dos subredes desde GitHub, realizar todos los cambios que desee y volver a utilizarla. Para ello, siga estos pasos:

1. Vaya a [Crear puerta de enlace de aplicaciones](https://github.com/Azure/azure-quickstart-templates/tree/master/101-application-gateway-create).
2. Haga clic en **azuredeploy.json** y luego en **RAW**.
3. Guarde el archivo en un una carpeta local del equipo.
4. Si está familiarizado con las plantillas del Administrador de recursos de Azure, vaya directamente al paso 7.
5. Abra el archivo que guardó y vea el contenido de **parameters** en la línea 5. Los parámetros de plantilla del Administrador de recursos de Azure proporcionan un marcador de posición para los valores que se pueden rellenar durante la implementación.
   
   | Parámetro | Descripción |
   | --- | --- |
   | **ubicación** |Región de Azure en que se crea la puerta de enlace de aplicaciones |
   | **VirtualNetwork1** |Nombre de la nueva red virtual |
   | **addressPrefix** |Espacio de direcciones de la red virtual, en formato CIDR |
   | **ApplicationGatewaysubnet** |Nombre de la subred de la puerta de enlace de aplicaciones |
   | **subnetPrefix** |Bloque CIDR de la subred de la puerta de enlace de aplicaciones |
   | **skuname** |Tamaño de la instancia de SKU |
   | **capacidad** |Número de instancias |
   | **backendaddress1** |Dirección IP del primer servidor web |
   | **backendaddress2** |Dirección IP del segundo servidor web |

    > [!IMPORTANT]
    >Las plantillas del Administrador de recursos de Azure que se mantienen en GitHub pueden cambiar con el tiempo. Asegúrese de comprobar la plantilla antes de usarla.

6. Compruebe el contenido en **resources** y observe las propiedades siguientes:

   * **type**. Tipo de recurso que creó la plantilla. En este caso, el tipo es `Microsoft.Network/applicationGateways`, que representa una puerta de enlace de aplicaciones.
   * **nombre**. Nombre del recurso. Observe el uso de `[parameters('applicationGatewayName')]`, que significa que el nombre lo proporciona el usuario o un archivo de parámetros durante la implementación.
   * **propiedades**. Lista de propiedades para el recurso. Esta plantilla usa la red virtual y la dirección IP pública durante la creación de la puerta de enlace de aplicaciones.

7. Vuelva a [https://github.com/Azure/azure-quickstart-templates/blob/master/101-application-gateway-create/](https://github.com/Azure/azure-quickstart-templates/blob/master/101-application-gateway-create).
8. Haga clic en **azuredeploy-paremeters.json** y luego en **RAW**.
9. Guarde el archivo en un una carpeta local del equipo.
10. Abra el archivo que guardó y edite los valores de los parámetros. Use los siguientes valores para implementar la puerta de enlace de aplicaciones que se describe en nuestro escenario.

    ```json
        {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        {
        "location" : {
        "value" : "West US"
        },
        "addressPrefix": {
        "value": "10.0.0.0/16"
        },
        "subnetPrefix": {
        "value": "10.0.0.0/24"
        },
        "skuName": {
        "value": "Standard_Small"
        },
        "capacity": {
        "value": 2
        },
        "backendIpAddress1": {
        "value": "10.0.1.10"
        },
        "backendIpAddress2": {
        "value": "10.0.1.11"
        }
        }
    ```

11. Guarde el archivo . Puede probar la plantilla de JSON y la plantilla de parámetros mediante las herramientas en línea de validación de JSON como [JSlint.com](http://www.jslint.com/).

## <a name="deploy-the-azure-resource-manager-template-by-using-powershell"></a>Implementación de la plantilla del Administrador de recursos de Azure mediante PowerShell

Si es la primera vez que usa Azure PowerShell, consulte [Instalación y configuración de Azure PowerShell](/powershell/azureps-cmdlets-docs) y siga las instrucciones para iniciar sesión en Azure y seleccionar su suscripción.

### <a name="step-1"></a>Paso 1

```powershell
Login-AzureRmAccount
```

### <a name="step-2"></a>Paso 2

Compruebe las suscripciones para la cuenta.

```powershell
Get-AzureRmSubscription
```

Se le solicita que se autentique con sus credenciales.

### <a name="step-3"></a>Paso 3

Elección de la suscripción de Azure que se va a usar.

```powershell
Select-AzureRmSubscription -Subscriptionid "GUID of subscription"
```

### <a name="step-4"></a>Paso 4

Si es necesario, cree un grupo de recursos mediante el cmdlet **New-AzureResourceGroup**. En el ejemplo siguiente, se crea un grupo de recursos denominado AppgatewayRG en la ubicación Este de EE. UU.

```powershell
New-AzureRmResourceGroup -Name AppgatewayRG -Location "East US"
```

Ejecute el cmdlet **New-AzureRmResourceGroupDeployment** para implementar la nueva red virtual mediante los archivos de plantillas y parámetros que descargó y modificó anteriormente.

```powershell
New-AzureRmResourceGroupDeployment -Name TestAppgatewayDeployment -ResourceGroupName AppgatewayRG `
-TemplateFile C:\ARM\azuredeploy.json -TemplateParameterFile C:\ARM\azuredeploy-parameters.json
```

## <a name="deploy-the-azure-resource-manager-template-by-using-the-azure-cli"></a>Implementación de la plantilla del Administrador de recursos de Azure mediante la CLI de Azure

Para implementar la plantilla de Azure Resource Manager descargada mediante la CLI de Azure, siga estos pasos:

### <a name="step-1"></a>Paso 1

Si es la primera vez que usa la CLI de Azure, consulte [Instalación de la CLI de Azure](../cli-install-nodejs.md) y siga las instrucciones hasta el punto donde tiene que seleccionar su cuenta y suscripción de Azure.

### <a name="step-2"></a>Paso 2

Ejecute el comando **azure config mode** para cambiar al modo de Resource Manager, como se muestra en el siguiente fragmento de código.

```azurecli
azure config mode arm
```

Este es el resultado esperado del comando anterior:

```azurecli
info:    New mode is arm
```

### <a name="step-3"></a>Paso 3

En caso necesario, ejecute el comando **azure group create** para crear un grupo de recursos nuevo, como se muestra en el siguiente fragmento de código. Observe la salida del comando. En la lista que se muestra en la salida se explican los parámetros utilizados. Para más información sobre los grupos de recursos, visite [Información general de Azure Resource Manager](../azure-resource-manager/resource-group-overview.md).

```azurecli
azure group create -n appgatewayRG -l eastus
```

**-n (or --name)**. Nombre del nuevo grupo de recursos. En este escenario, es *appgatewayRG*.

**-l (o --location)**. Región de Azure donde se crea el nuevo grupo de recursos. En este escenario, es *eastus*.

### <a name="step-4"></a>Paso 4

Ejecute el cmdlet **azure group deployment create** para implementar la nueva red virtual mediante los archivos de plantillas y parámetros que descargó y modificó en el paso anterior. En la lista que se muestra en la salida se explican los parámetros utilizados.

```azurecli
azure group deployment create -g appgatewayRG -n TestAppgatewayDeployment -f C:\ARM\azuredeploy.json -e C:\ARM\azuredeploy-parameters.json
```

## <a name="deploy-the-azure-resource-manager-template-by-using-click-to-deploy"></a>Implementación de la plantilla del Administrador de recursos de Azure mediante el método de hacer clic para implementar

Clic para implementar es otra forma de usar las plantillas del Administrador de recursos de Azure. Se trata de una manera fácil de usar plantillas con el Portal de Azure.

### <a name="step-1"></a>Paso 1

Vaya a [Create an Application Gateway with Public IP](https://azure.microsoft.com/documentation/templates/101-application-gateway-public-ip/)(Creación de una puerta de enlace de aplicaciones con IP pública).

### <a name="step-2"></a>Paso 2

Haga clic en **Implementar en Azure**.

![Implementar en Azure](./media/application-gateway-create-gateway-arm-template/deploytoazure.png)

### <a name="step-3"></a>Paso 3

Rellene los parámetros de la plantilla de implementación en el portal y haga clic en **Aceptar**.

![parameters](./media/application-gateway-create-gateway-arm-template/ibiza1.png)

### <a name="step-4"></a>Paso 4

Seleccione **Términos legales** y haga clic en **Comprar**.

### <a name="step-5"></a>Paso 5

En la hoja Implementación personalizada, haga clic en **Crear**.

## <a name="providing-certificate-data-to-resource-manager-templates"></a>Proporciona datos de certificado a las plantillas de Resource Manager

Cuando se usa SSL con una plantilla, el certificado debe proporcionarse en una cadena en base&64; en lugar de cargarse. Para convertir un archivo .pfx o .cer a una cadena en base&64;, ejecute el siguiente comando de PowerShell. Este fragmento de código convierte el certificado en una cadena en base&64; que se puede proporcionar a la plantilla. El resultado esperado es una cadena que se puede almacenar en una variable y pegarse en la plantilla.

```powershell
[System.Convert]::ToBase64String([System.IO.File]::ReadAllBytes("<certificate path and name>.pfx"))
```

## <a name="next-steps"></a>Pasos siguientes

Si desea configurar la descarga de SSL, consulte [Configuración de una puerta de enlace de aplicaciones para la descarga SSL mediante el modelo de implementación clásica](application-gateway-ssl.md).

Si quiere configurar una puerta de enlace de aplicaciones para usarla con el equilibrador de carga interno, consulte [Creación de una puerta de enlace de aplicaciones con un equilibrador de carga interno (ILB)](application-gateway-ilb.md).

Si desea más información acerca de las opciones de equilibrio de carga en general, visite:

* [Equilibrador de carga de Azure](https://azure.microsoft.com/documentation/services/load-balancer/)
* [Administrador de tráfico de Azure](https://azure.microsoft.com/documentation/services/traffic-manager/)


