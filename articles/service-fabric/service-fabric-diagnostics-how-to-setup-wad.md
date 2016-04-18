<properties
   pageTitle="Recopilación de registros con Diagnósticos de Azure | Microsoft Azure"
   description="En este artículo se describe cómo configurar Diagnósticos de Azure para recopilar registros de un clúster de Service Fabric que se ejecute en Azure."
   services="service-fabric"
   documentationCenter=".net"
   authors="ms-toddabel"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="03/30/2016"
   ms.author="toddabel"/>


# Recopilación de registros con Diagnósticos de Azure

Cuando se ejecuta un clúster de Azure Service Fabric, es conveniente recopilar los registros de todos los nodos en una ubicación central. La presencia de los registros en una ubicación central facilita el análisis y la solución de los problemas del clúster o de las aplicaciones y los servicios que se ejecutan en ese clúster. Uno de los métodos para cargar y recopilar registros es usar la extensión de Diagnósticos de Azure que carga los registros en Almacenamiento de Azure. Los registros no son útiles directamente en el almacenamiento, pero puede usarse un proceso externo para leer los eventos del almacenamiento y colocarlos en un producto como [Elastic Search](service-fabric-diagnostic-how-to-use-elasticsearch.md) u otra solución.

## Requisitos previos
Estas herramientas se usarán para realizar algunas de las operaciones que se describen en este documento:

* [Diagnósticos de Azure](../cloud-services/cloud-services-dotnet-diagnostics.md) (relacionado con los Servicios en la nube de Azure, aunque con información y ejemplos adecuados)
* [Administrador de recursos de Azure](../resource-group-overview.md)
* [Azure PowerShell](../powershell-install-configure.md)
* [Cliente de Azure Resource Manager](https://github.com/projectkudu/ARMClient)

## Diferentes orígenes de registros que podría recopilar
1. **Registros de Service Fabric:** emitidos por la plataforma a los canales ETW y EventSource estándar. Los registros pueden ser de uno de los varios tipos que hay:
  - Eventos operativos: se trata de registros para las operaciones realizadas por la plataforma Service Fabric. Algunos ejemplos son la creación de aplicaciones y servicios, los cambios de estado de nodo y la información sobre la actualización.
  - [Eventos del modelo de programación de actor](service-fabric-reliable-actors-diagnostics.md)
  - [Eventos del modelo de programación de Reliable Services](service-fabric-reliable-services-diagnostics.md)
2. **Eventos de aplicaciones:** son los eventos que se emiten desde el código de los servicios y que se escriben mediante la clase auxiliar EventSource proporcionada en las plantillas de Visual Studio. Para obtener más información sobre cómo escribir registros de la aplicación, consulte [este artículo sobre la supervisión y diagnóstico de servicios en una configuración de equipo local](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md).


## Implementación de la extensión de Diagnósticos
El primer paso para recopilar registros será implementar la extensión WAD en cada una de las máquinas virtuales del clúster de Service Fabric. La extensión de Diagnósticos recopila registros en cada máquina virtual y los carga a la cuenta de almacenamiento que especifique. Los pasos varían ligeramente en función de si se usa el Portal de Azure o el Administrador de recursos de Azure, y de si se realiza la implementación como parte de la creación del clúster o para un clúster que ya existe. Echemos un vistazo a los pasos para cada escenario.

### Implementación de la extensión de Diagnósticos como parte de la creación del clúster a través del portal
Para implementar Diagnósticos en las máquinas virtuales del clúster como parte de la creación de dicho clúster, se usa el panel Configuración de Diagnósticos que se muestra en la imagen siguiente. De forma predeterminada, los *Registros de soporte técnico* se encuentran en estado **Habilitado** y el *Diagnóstico de aplicaciones* se encuentra en estado **Deshabilitado**. Una vez creado el clúster, esta configuración no se puede cambiar mediante el portal.

![Configuración de Diagnósticos en el portal para crear un clúster](./media/service-fabric-diagnostics-how-to-setup-wad-operational-insights/portal-cluster-creation-diagnostics-setting.png)

Los registros de soporte técnico son **necesarios** para que el equipo de soporte técnico de Azure pueda resolver las solicitudes que se creen. Estos registros se recopilan en tiempo real y se almacenarán en la cuenta de almacenamiento creada en el grupo de recursos actual. Diagnóstico de aplicaciones configura los eventos del nivel de aplicación, incluidos los eventos de [Actor](service-fabric-reliable-actors-diagnostics.md), los eventos del [Servicio de confianza](service-fabric-reliable-services-diagnostics.md) y algunos eventos de Service Fabric del nivel de sistema de modo que se almacenen en Almacenamiento de Azure. Algunos productos como [Elastic Search](service-fabric-diagnostic-how-to-use-elasticsearch.md) o su propio proceso pueden seleccionar los eventos de la cuenta de almacenamiento. Actualmente no existe ninguna manera de filtrar o limpiar los eventos que se envían a la tabla. Si no se implementa un proceso para quitar eventos de la tabla, la tabla seguirá aumentando. Cuando se crea un clúster mediante el portal, se recomienda exportar la plantilla una vez finalizada la implementación. Puede exportar las plantillas desde el portal de la siguiente manera:
1. Abra el grupo de recursos
2. Seleccione Configuración para mostrar el panel Configuración
3. Seleccione Implementaciones para mostrar el panel Historial de implementaciones
4. Seleccione una implementación para mostrar los detalles de la implementación
5. Seleccione Exportar plantilla para mostrar el panel Plantilla
6. Seleccione Guardar en archivo para exportar un archivo .zip que contenga la plantilla, los parámetros y los archivos de PowerShell.

Después de exportar los archivos, debe llevar a cabo una modificación. Edite el archivo **parameters.json** y quite el elemento **adminPassword**. Esto provocará un símbolo del sistema para la contraseña cuando se ejecuta el script de implementación.

### Implementación de la extensión de Diagnósticos como parte de la creación del clúster a través de Azure Resource Manager
Para crear un clúster mediante el Administrador de recursos, tiene que agregar el JSON de la configuración de Diagnósticos a la plantilla del Administrador de recursos del clúster completo antes de crear el clúster. Dentro de los ejemplos de plantillas del Administrador de recursos, proporcionamos una plantilla de ejemplo del Administrador de recursos de clúster de cinco máquinas virtuales con la configuración de Diagnósticos añadida. Puede verlo en: [Ejemplo de plantilla de clúster de cinco nodos con el Administrador de recursos de Diagnósticos](https://github.com/Azure/azure-quickstart-templates/tree/master/service-fabric-secure-cluster-5-node-1-nodetype-wad) en la galería de ejemplos de Azure. Para ver la configuración de Diagnósticos en la plantilla del Administrador de recursos, abra el archivo **azuredeploy.json** y busque **IaaSDiagnostics**. Para crear un clúster con esta plantilla, basta con presionar el botón **Implementar en Azure** disponible en el vínculo anterior.

También puede descargar el ejemplo del Administrador de recursos, modificarlo y crear un clúster con la plantilla modificada mediante el comando `New-AzureResourceGroupDeployment` en una ventana de Azure PowerShell. Consulte la información a continuación para los parámetros que necesitará pasar al comando. Para obtener información detallada sobre cómo implementar un grupo de recursos con PowerShell, consulte el artículo [Implementación de un grupo de recursos con la plantilla de Azure Resource Manager](../resource-group-template-deploy.md).

```powershell

New-AzureResourceGroupDeployment -ResourceGroupName $resourceGroupName -Name $deploymentName -TemplateFile $pathToARMConfigJsonFile -TemplateParameterFile $pathToParameterFile –Verbose
```

### Implementación de la extensión de Diagnósticos en un clúster existente
Si tiene un clúster existente que no tiene Diagnósticos implementado, puede agregarlo siguiendo estos pasos. Modifique la plantilla de ARM usada para crear el clúster existente o descargue la plantilla desde el portal, tal como se describió anteriormente. Modifique el archivo **template.json** realizando las siguientes tareas:

Agregue un nuevo recurso de almacenamiento a la plantilla mediante una adición en la sección de recursos.

##### Actualización de la sección de recursos
```json
{
  "apiVersion": "2015-05-01-preview",
  "type": "Microsoft.Storage/storageAccounts",
  "name": "[parameters('applicationDiagnosticsStorageAccountName')]",
  "location": "[parameters('computeLocation')]",
  "properties": {
    "accountType": "[parameters('applicationDiagnosticsStorageAccountType')]"
  },
  "tags": {
    "resourceType": "Service Fabric",
    "clusterName": "[parameters('clusterName')]"
  }
},
```

 A continuación, lleve a cabo una adición en la sección de parámetros justo después de las definiciones de la cuenta de almacenamiento, entre "supportLogStorageAccountName" y "vmNodeType0Name". Reemplace el texto de marcador de posición *aquí va el nombre de la cuenta de almacenamiento* por el nombre de la cuenta de almacenamiento deseada.

##### Actualización de la sección de parámetros
```json
    "applicationDiagnosticsStorageAccountType": {
      "type": "string",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS"
      ],
      "defaultValue": "Standard_LRS",
      "metadata": {
        "description": "Replication option for the application diagnostics storage account"
      }
    },
    "applicationDiagnosticsStorageAccountName": {
      "type": "string",
      "defaultValue": "storage account name goes here",
      "metadata": {
        "description": "Name for the storage account that contains application diagnostics data from the cluster"
      }
    },
```
A continuación, actualice la sección *VirtualMachineProfile* de **template.json**. Para ello, agregue lo siguiente en la matriz "extensiones". Asegúrese de agregar una coma al principio o al final, según donde se inserte.

##### Adición en la matriz de extensiones de VirtualMachineProfile
```json
{
	"name": "[concat(parameters('vmNodeType0Name'),'_Microsoft.Insights.VMDiagnosticsSettings')]",
	"properties": {
		"type": "IaaSDiagnostics",
		"autoUpgradeMinorVersion": true,
		"protectedSettings": {
		"storageAccountName": "[parameters('applicationDiagnosticsStorageAccountName')]",
		"storageAccountKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('applicationDiagnosticsStorageAccountName')),'2015-05-01-preview').key1]",
		"storageAccountEndPoint": "https://core.windows.net/"
		},
		"publisher": "Microsoft.Azure.Diagnostics",
		"settings": {
		"WadCfg": {
			"DiagnosticMonitorConfiguration": {
			"overallQuotaInMB": "50000",
			"EtwProviders": {
				"EtwEventSourceProviderConfiguration": [
				{
					"provider": "Microsoft-ServiceFabric-Actors",
					"scheduledTransferKeywordFilter": "1",
					"scheduledTransferPeriod": "PT5M",
					"DefaultEvents": {
					"eventDestination": "ServiceFabricReliableActorEventTable"
					}
				},
				{
					"provider": "Microsoft-ServiceFabric-Services",
					"scheduledTransferPeriod": "PT5M",
					"DefaultEvents": {
					"eventDestination": "ServiceFabricReliableServiceEventTable"
					}
				}
				],
				"EtwManifestProviderConfiguration": [
				{
					"provider": "cbd93bc2-71e5-4566-b3a7-595d8eeca6e8",
					"scheduledTransferLogLevelFilter": "Information",
					"scheduledTransferKeywordFilter": "4611686018427387904",
					"scheduledTransferPeriod": "PT5M",
					"DefaultEvents": {
					"eventDestination": "ServiceFabricSystemEventTable"
					}
				}
				]
			}
			}
		},
		"StorageAccount": "[parameters('applicationDiagnosticsStorageAccountName')]"
		},
		"typeHandlerVersion": "1.5"
	}
}
```

Después de modificar el archivo **template.json** tal como se indicó, vuelva a publicar la plantilla de ARM. En caso de que la plantilla se haya exportado, si ejecuta el archivo **deploy.ps1** la plantilla se volverá a publicar. Después de realizar la implementación, asegúrese de que *ProvisioningState* sea *Correcto*.


## Actualización de Diagnósticos para recopilar y cargar registros desde nuevos canales EventSource
Para actualizar Diagnósticos de forma que recopile registros desde canales EventSource nuevos que representen una nueva aplicación que vaya a implementar, basta con seguir los mismos pasos que en la [sección anterior](#deploywadarm), que describe la configuración de Diagnósticos para un clúster existente. Tendrá que actualizar la sección *EtwEventSourceProviderConfiguration* en **template.json** para agregar entradas para el nuevo EventSources antes de aplicar la actualización de la configuración mediante el comando *New-AzureResourceGroupDeployment* de PowerShell.


## Pasos siguientes
Revise los eventos de diagnóstico emitidos para [Reliable Actors](service-fabric-reliable-actors-diagnostics.md) y [Reliable Services](service-fabric-reliable-services-diagnostics.md) para comprender más a fondo qué eventos debería examinar durante la solución de problemas.

<!---HONumber=AcomDC_0406_2016-->