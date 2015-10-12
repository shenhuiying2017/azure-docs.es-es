
<properties 
   pageTitle="Creación de una Puerta de enlace de aplicaciones con plantillas del Administrador de recursos de Azure | Microsoft Azure"
   description="En esta página se ofrecen instrucciones para crear una Puerta de enlace de aplicaciones con una plantilla del Administrador de recursos de Azure"
   documentationCenter="na"
   services="application-gateway"
   authors="joaoma"
   manager="jdial"
   editor="tysonn"/>
<tags 
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="hero-article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="09/21/2015"
   ms.author="joaoma"/>


# Configuración de la Puerta de enlace de aplicaciones con una plantilla ARM

La Puerta de enlace de aplicaciones es el equilibrador de carga de nivel 7. Ofrece conmutación por error, enrutamiento del rendimiento de solicitudes HTTP entra distintos servidores, ya se que se encuentren en la nube o en una implementación local. La puerta de enlace de aplicaciones tiene las siguientes características de entrega de aplicaciones: equilibrio de carga HTTP, afinidad de sesión basado en cookies, descarga SSL.

> [AZURE.SELECTOR]
- [Azure Classic Powershell steps](application-gateway-create-gateway.md)
- [Azure Resource Manager Powershell steps](application-gateway-create-gateway-arm.md)
- [Azure Resource Manager template steps](application-gateway-create-gateway-arm-template.md)


<BR>

Aprenderá a descargar y modificar una plantilla de ARM existente desde GitHub, así como a implementar la plantilla de GitHub, PowerShell y la CLI de Azure.

Si simplemente va a implementar la plantilla ARM directamente desde GitHub, sin realizar ningún cambio, vaya a implementar una plantilla desde github.


>[AZURE.IMPORTANT]Antes de trabajar con recursos de Azure, es importante comprender que Azure tiene actualmente dos modelos de implementación: el Administrador de recursos y el clásico. Asegúrese de que comprende los [modelos de implementación y las herramientas](azure-classic-rm.md) antes de trabajar con recursos de Azure. Puede ver la documentación de las distintas herramientas haciendo clic en las pestañas de la parte superior de este artículo. Este documento tratará de la creación de una Puerta de enlace de aplicaciones con el Administrador de recursos de Azure. Para usar la versión básica, vaya a [crear una implementación clásica de la Puerta de enlace de aplicaciones con PowerShell](application-gateway-create-gateway.md).




## Escenario

En este escenario, creará:

- Una Puerta de enlace de aplicaciones con dos instancias;
- Una red virtual denominada VirtualNetwork1 con un bloque CIDR reservado de 10.0.0.0/16;
- Una subred denominada Appgatewaysubnet con 10.0.0.0/28 como bloque CIDR;
- La instalación de dos direcciones IP de back-end configuradas previamente para los servidores web cuya carga de tráfico quiera equilibrar. En este ejemplo de plantilla, las direcciones IP de back-end serán 10.0.1.10 y 10.0.1.11

>[AZURE.NOTE]Esos son los parámetros para esta plantilla. Puede cambiar las reglas, el agente de escucha y la SSL abriendo el azuredeploy.json para personalizar la plantilla.



![arm-scenario](./media/application-gateway-create-gateway-arm-template/scenario-arm.png)



## Descargar y comprender la plantilla ARM

Puede descargar la plantilla ARM existente para crear una red virtual y dos subredes desde github, puede realizar los cambios que desee y volver a utilizarla. Para ello, siga estos pasos.

1. Navegue a https://github.com/Azure/azure-quickstart-templates/blob/master/101-create-applicationgateway-publicip.
2. Haga clic en **azuredeploy.json** y luego en **RAW**.
3. Guarde el archivo en un una carpeta local en su equipo.
4. Si está familiarizado con las plantillas ARM, salte al paso 7.
5. Abra el archivo que acaba de guardar y vea el contenido de la línea 5 en **parameters**. Los parámetros de plantilla ARM proporcionan un marcador de posición para los valores que se pueden rellenar durante la implementación.

	| Parámetro | Descripción |
	|---|---|
	| **ubicación** | Región de Azure donde se creará la Puerta de enlace de aplicaciones |
	| **VirtualNetwork1** | Nombre de la red virtual nueva |
	| **addressPrefix** | Espacio de direcciones de la red virtual, en formato CIDR |
	| **ApplicationGatewaysubnet** | Nombre de la subred de Puerta de enlace de aplicaciones |
	| **subnetPrefix** | Bloque CIDR de la subred de Puerta de enlace de aplicaciones |
	| **skuname** | Tamaño de la instancia de SKU |
	| **capacidad** | número de instancias |
	| **backendaddress1** | Dirección IP del primer servidor web |
	| **backendaddress2** | Dirección IP del segundo servidor web|


>[AZURE.IMPORTANT]Las plantillas ARM que se mantienen en github pueden cambiar con el tiempo. Asegúrese de comprobar la plantilla antes de usarla.
	
6. Compruebe el contenido en **resources** y observe lo siguiente:

	- **type**. Tipo de recurso que creó la plantilla. En este caso, **Microsoft.Network/applicationGateways**, que representa una Puerta de enlace de aplicaciones.
	- **name**. Nombre del recurso. Observe el uso de **[parameters('vnetName')]**, lo que significa que será el usuario quien proporcione el nombre como entrada o como archivo de parámetros durante la implementación.
	- **properties**. Lista de propiedades para el recurso. Esta plantilla usa la red virtual y la dirección IP pública durante la creación de la Puerta de enlace de aplicaciones.

7. Navegue de nuevo a https://github.com/Azure/azure-quickstart-templates/blob/master/101-create-applicationgateway-publicip.
8. Haga clic en **azuredeploy-paremeters.json** y luego en **RAW**.
9. Guarde el archivo en un una carpeta local en su equipo.
10. Abra el archivo que acaba de guardar y edite los valores de los parámetros. Use los siguientes valores para implementar la red Puerta de enlace de aplicaciones que se describe en nuestro escenario.

		{
		   "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
		   "contentVersion": "1.0.0.0",
		   "parameters": {
		     "location": {
		       "value": "East US"
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
		}

11. Guarde el archivo. Puede probar la plantilla de Json y la plantilla de parámetros mediante herramientas de validación de json en línea como [JSlint.com](http://www.jslint.com/)
 
## Implementación de la plantilla ARM con PowerShell

1. Si es la primera vez que usa Azure PowerShell, consulte [Instalación y configuración de Azure PowerShell](powershell-install-configure.md) y siga las instrucciones hasta el final para iniciar sesión en Azure y seleccionar su suscripción.
2. En un símbolo del sistema de Azure PowerShell, ejecute el cmdlet **Switch-AzureMode** para cambiar al modo de Administrador de recursos, como se muestra a continuación.

		Switch-AzureMode AzureResourceManager
	
Resultado esperado:

		WARNING: The Switch-AzureMode cmdlet is deprecated and will be removed in a future release.

>[AZURE.WARNING]El cmdlet Switch-AzureMode pronto estará en desuso. Cuando esto suceda, se cambiará el nombre de todos los cmdlets del Administrador de recursos.
	
3. En caso necesario, cree un nuevo grupo de recursos con el cmdlet `New-AzureResourceGroup`. En el ejemplo siguiente, creará un grupo de recursos denominado AppgatewayRG en la ubicación Este de EE. UU.:

		PS C:\> New-AzureResourceGroup -Name AppgatewayRG -Location "East US"
		VERBOSE: 5:38:49 PM - Created resource group 'AppgatewayRG' in location 'eastus'


		ResourceGroupName : AppgatewayRG
		Location          : eastus
		ProvisioningState : Succeeded
		Tags              :
		Permissions       :
	                 Actions  NotActions
	                 =======  ==========
	                  *

		ResourceId        : /subscriptions/################################/resourceGroups/AppgatewayRG

4. Ejecute el cmdlet New-AzureResourceGroupDeployment para implementar la nueva red virtual mediante la plantilla y los archivos de parámetros que descargó y modificó antes.

		New-AzureResourceGroupDeployment -Name TestAppgatewayDeployment -ResourceGroupName AppgatewayRG `
 		   -TemplateFile C:\ARM\azuredeploy.json -TemplateParameterFile C:\ARM\azuredeploy-parameters.json

La salida generada por la línea de comandos será la siguiente:

		DeploymentName    : testappgatewaydeployment
		ResourceGroupName : appgatewayRG
		ProvisioningState : Succeeded
		Timestamp         : 9/19/2015 1:49:41 AM
		Mode              : Incremental
		TemplateLink      :
		Parameters        :
                   Name             Type                       Value
                   ===============  =========================  ==========
                   location         String                     East US
                   addressPrefix    String                     10.0.0.0/16
                   subnetPrefix     String                     10.0.0.0/24
                   skuName          String                     Standard_Small
                   capacity         Int                        2
                   backendIpAddress1  String                     10.0.1.10
                   backendIpAddress2  String                     10.0.1.11

		Outputs           :


## Implementar la plantilla ARM mediante la CLI de Azure

Para implementar la plantilla ARM mediante la CLI de Azure, siga estos pasos.

1. Si nunca ha usado la CLI de Azure, consulte [Instalación y configuración de la CLI de Azure](xplat-cli-install.md) y siga las instrucciones hasta el punto donde deba seleccionar su cuenta y suscripción de Azure.
2. Ejecute el comando **azure config mode** para cambiar al modo de Administrador de recursos, como se muestra a continuación.

		azure config mode arm

Este es el resultado esperado del comando anterior:

		info:	New mode is arm

3. En caso necesario, ejecute **azure group create** para crear un grupo de recursos, como se muestra a continuación. Observe la salida del comando. En la lista que se muestra en la salida se explican los parámetros utilizados. Para obtener más información sobre los grupos de recursos, visite [Información general del Administrador de recursos de Azure](resource-group-overview.md).

		azure group create -n appgatewayRG -l eastus

**-n (o --name)**. Nombre del nuevo grupo de recursos. En este escenario, *appgatewayRG*.

**-l (o --location)**. Región de Azure donde se creará el nuevo grupo de recursos. En este escenario, *Eastus*.

4. Ejecute el cmdlet **azure group deployment create** para implementar la nueva red virtual mediante la plantilla y los archivos de parámetros que descargó y modificó antes. En la lista que se muestra en la salida se explican los parámetros utilizados.

		azure group deployment create -g appgatewayRG -n TestAppgatewayDeployment -f C:\ARM\azuredeploy.json -e C:\ARM\azuredeploy-parameters.json

Este es el resultado esperado del comando anterior:

		azure group deployment create -g appgatewayRG -n TestAppgatewayDeployment -f C:\ARM\azuredeploy.json -e C:\ARM\azuredeploy-parameters.json
		info:    Executing command group deployment create
		+ Initializing template configurations and parameters
		+ Creating a deployment
		info:    Created template deployment "TestAppgatewayDeployment"
		+ Waiting for deployment to complete
		data:    DeploymentName     : TestAppgatewayDeployment
		data:    ResourceGroupName  : appgatewayRG
		data:    ProvisioningState  : Succeeded
		data:    Timestamp          : 2015-09-21T20:50:27.5129912Z
		data:    Mode               : Incremental
		data:    Name               Type    Value
		data:    -----------------  ------  --------------
		data:    location           String  East US
		data:    addressPrefix      String  10.0.0.0/16
		data:    subnetPrefix       String  10.0.0.0/24	
		data:    skuName            String  Standard_Small
		data:    capacity           Int     2
		data:    backendIpAddress1  String  10.0.1.10
		data:    backendIpAddress2  String  10.0.1.11
		info:    group deployment create command OK

**-g (o --resource-group)**. Nombre del grupo de recursos en el que se creará la red virtual.

**-f (o --template-file)**. Ruta de acceso al archivo de plantilla ARM.

**-e (o --parameters-file)**. Ruta de acceso al archivo de parámetros ARM.

## Implementación de la plantilla ARM con Clic para implementar

Clic para implementar es otra forma de usar plantillas ARM. Se trata de una manera fácil de usar plantillas con el Portal de Azure.


### Paso 1 
El vínculo [Clic para implementar Puerta de enlace de aplicaciones](http://azure.microsoft.com/documentation/templates/101-create-applicationgateway-publicip/) le redirigirá a la página de plantilla del portal para Puerta de enlace de aplicaciones.


### Paso 2 

Haga clic en "implementar en Azure".

![arm-scenario](./media/application-gateway-create-gateway-arm-template/deploytoazure.png)

### Paso 3

Rellene los parámetros de la plantilla de implementación en el portal y haga clic en Aceptar.

![arm-scenario](./media/application-gateway-create-gateway-arm-template/ibiza1.png)

### Paso 4

Seleccione "Condiciones legales" y haga clic en "comprar".

### Paso 5

En la hoja "implementación personalizada", haga clic en "crear".


 
## Pasos siguientes

Si quiere configurar la descarga SSL, vea [Configuración de una Puerta de enlace de aplicaciones para descarga SSL](application-gateway-ssl.md).

Si quiere configurar una Puerta de enlace de aplicaciones para usarla con ILB, vea [Creación de una Puerta de enlace de aplicaciones con un equilibrador de carga interno (ILB)](application-gateway-ilb.md).

Si desea obtener más información acerca de opciones de equilibrio de carga en general, vea:

- [Equilibrador de carga de Azure](https://azure.microsoft.com/documentation/services/load-balancer/)
- [Administrador de tráfico de Azure](https://azure.microsoft.com/documentation/services/traffic-manager/)

<!---HONumber=Oct15_HO1-->