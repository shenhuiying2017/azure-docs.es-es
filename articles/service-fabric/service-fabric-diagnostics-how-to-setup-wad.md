---
title: "Recopilación de registros con Diagnósticos de Azure | Microsoft Docs"
description: "En este artículo se describe cómo configurar Diagnósticos de Azure para recopilar registros de un clúster de Service Fabric que se ejecute en Azure."
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: 9f7e1fa5-6543-4efd-b53f-39510f18df56
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/17/2017
ms.author: dekapur
translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: 7c3311800af27016b7f993b375055bbc65d9a727
ms.lasthandoff: 04/27/2017


---
# <a name="collect-logs-by-using-azure-diagnostics"></a>Recopilación de registros con Diagnósticos de Azure
> [!div class="op_single_selector"]
> * [Windows](service-fabric-diagnostics-how-to-setup-wad.md)
> * [Linux](service-fabric-diagnostics-how-to-setup-lad.md)
> 
> 

Cuando se ejecuta un clúster de Azure Service Fabric, es conveniente recopilar los registros de todos los nodos en una ubicación central. La presencia de los registros en una ubicación central facilita el análisis y la solución de los problemas del clúster o de las aplicaciones y los servicios que se ejecutan en ese clúster.

Uno de los métodos para cargar y recopilar registros es usar la extensión Diagnósticos de Azure, que carga registros en Azure Storage, Azure Application Insights o Azure Event Hubs. Los registros no son tan útiles directamente en Storage o en Event Hubs. No obstante, puede usar un proceso externo para leer los eventos desde el almacenamiento y colocarlos en un producto como [Log Analytics](../log-analytics/log-analytics-service-fabric.md) u otra solución de análisis de registros. [Azure Application Insights](https://azure.microsoft.com/services/application-insights/) tiene integrado un servicio integral de análisis y búsqueda de registros.

## <a name="prerequisites"></a>Requisitos previos
Use estas herramientas para realizar algunas de las operaciones que se describen en este documento:

* [Diagnósticos de Azure](../cloud-services/cloud-services-dotnet-diagnostics.md) (relacionado con Azure Cloud Services, aunque con información y ejemplos adecuados)
* [Administrador de recursos de Azure](../azure-resource-manager/resource-group-overview.md)
* [Azure PowerShell](/powershell/azure/overview)
* [Cliente de Azure Resource Manager](https://github.com/projectkudu/ARMClient)
* [Plantilla de Azure Resource Manager](../virtual-machines/windows/extensions-diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="log-sources-that-you-might-want-to-collect"></a>Orígenes de registros que puede recopilar
* **Registros de Service Fabric:** emitidos por la plataforma a los canales EventSource y Seguimiento de eventos para Windows (ETW) estándar. Los registros pueden ser de uno de los varios tipos que hay:
  * Eventos operativos: se trata de registros para las operaciones realizadas por la plataforma Service Fabric. Algunos ejemplos son la creación de aplicaciones y servicios, los cambios de estado de nodo y la información sobre la actualización.
  * [Eventos del modelo de programación de Reliable Actors](service-fabric-reliable-actors-diagnostics.md)
  * [Eventos del modelo de programación de Reliable Services](service-fabric-reliable-services-diagnostics.md)
* **Eventos de aplicaciones:** son los eventos que se emiten desde el código de los servicios y que se escriben mediante la clase auxiliar EventSource proporcionada en las plantillas de Visual Studio. Para obtener más información sobre cómo escribir registros de la aplicación, vea [Supervisión y diagnóstico de servicios en una configuración de desarrollo de máquina local](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md).

## <a name="deploy-the-diagnostics-extension"></a>Implementación de la extensión de Diagnósticos
El primer paso para recopilar registros será implementar la extensión WAD en cada una de las máquinas virtuales del clúster de Service Fabric. La extensión de Diagnósticos recopila registros en cada máquina virtual y los carga a la cuenta de almacenamiento que especifique. Los pasos varían ligeramente en función de si se utiliza Azure Portal o Azure Resource Manager. Los pasos varían también en función de si la implementación forma parte de la creación del clúster o si es para un clúster que ya existe. Echemos un vistazo a los pasos para cada escenario.

### <a name="deploy-the-diagnostics-extension-as-part-of-cluster-creation-through-the-portal"></a>Implementación de la extensión de Diagnósticos como parte de la creación del clúster a través del portal
Para implementar la extensión de Diagnósticos en las máquinas virtuales del clúster como parte de la creación de dicho clúster, se usa el panel Configuración de diagnóstico que se muestra en la imagen siguiente. Para habilitar la recopilación de eventos de Reliable Actors o Reliable Services, asegúrese de que Diagnósticos esté definido en **Activado** (se trata de la configuración predeterminada). Después de crear el clúster, no puede cambiar esta configuración mediante el portal.

![Configuración de Diagnósticos de Azure en el portal para crear un clúster](./media/service-fabric-diagnostics-how-to-setup-wad/portal-cluster-creation-diagnostics-setting.png)

El equipo de soporte técnico de Azure *necesita* registros de soporte técnico para resolver las solicitudes de soporte técnico que crea. Estos registros se recopilan en tiempo real y se almacenan en una de las cuentas de almacenamiento creadas en el grupo de recursos. En Configuración de diagnóstico se configuran los eventos en el nivel de la aplicación. Estos eventos incluyen eventos de [Reliable Actors](service-fabric-reliable-actors-diagnostics.md), eventos de [Reliable Services](service-fabric-reliable-services-diagnostics.md) y algunos eventos de Service Fabric de nivel de sistema que se almacenarán en Azure Storage.

Algunos productos como [Elasticsearch](https://www.elastic.co/guide/index.html) o sus propios procesos pueden obtener los eventos de la cuenta de almacenamiento. Actualmente no existe ninguna manera de filtrar o limpiar los eventos que se envían a la tabla. Si no se implementa un proceso para quitar eventos de la tabla, la tabla seguirá aumentando.

Al crear un clúster mediante el portal, es muy recomendable descargar la plantilla **antes de hacer clic en Aceptar** para crear el clúster. Para más información, vea [Configuración de un clúster de Service Fabric con una plantilla de Azure Resource Manager](service-fabric-cluster-creation-via-arm.md). Necesitará la plantilla para realizar cambios más adelante, porque no se pueden realizar algunos cambios a través del portal.

Puede exportar plantillas desde el portal mediante los pasos siguientes. Sin embargo, estas plantillas pueden ser más difíciles de utilizar porque podrían tener valores null a los que les falta información necesaria.

1. Abra el grupo de recursos.
2. Seleccione **Configuración** para mostrar el panel Configuración.
3. Seleccione **Implementaciones** para mostrar el panel Historial de implementación.
4. Seleccione una implementación para mostrar los detalles de la implementación.
5. Seleccione **Exportar plantilla** para mostrar el panel Plantilla.
6. Seleccione **Guardar en archivo** para exportar un archivo .zip que contenga la plantilla, los parámetros y los archivos de PowerShell.

Después de exportar los archivos, debe realizar una modificación. Edite el archivo parameters.json y quite el elemento **adminPassword**. Esto provocará un símbolo del sistema para la contraseña cuando se ejecuta el script de implementación. Cuando ejecute el script de implementación, tendrá que corregir los valores de parámetro null.

Para usar la plantilla descargada para actualizar una configuración, siga estos pasos:

1. Extraiga el contenido en una carpeta del equipo local.
2. Modifique el contenido para que refleje la nueva configuración.
3. Inicie PowerShell y cambie a la carpeta donde extrajo el contenido.
4. Ejecute **deploy.ps1** y rellene el id. de suscripción, el nombre del grupo de recursos (use el mismo nombre para actualizar la configuración) y un nombre de implementación exclusivo.

### <a name="deploy-the-diagnostics-extension-as-part-of-cluster-creation-by-using-azure-resource-manager"></a>Implementación de la extensión de Diagnósticos como parte de la creación del clúster a través del Administrador de recursos de Azure
Para crear un clúster mediante el Resource Manager, tiene que agregar el JSON de la configuración de Diagnósticos a la plantilla de Resource Manager del clúster completo antes de crear el clúster. Dentro de los ejemplos de plantillas del Administrador de recursos, proporcionamos una plantilla de ejemplo del Administrador de recursos de clúster de cinco máquinas virtuales con la configuración de Diagnósticos añadida. Puede verlo en: [Ejemplo de plantilla de clúster de cinco nodos con el Administrador de recursos de Diagnósticos](https://github.com/Azure/azure-quickstart-templates/tree/master/service-fabric-secure-cluster-5-node-1-nodetype-wad)en la galería de ejemplos de Azure.

Para ver la configuración de Diagnósticos en la plantilla de Resource Manager, abra el archivo azuredeploy.json y busque **IaaSDiagnostics**. Para crear un clúster con esta plantilla, presione el botón **Deploy to Azure** (Implementar en Azure) disponible en el vínculo anterior.

También puede descargar el ejemplo de Resource Manager, modificarlo y crear un clúster con la plantilla modificada mediante el comando `New-AzureRmResourceGroupDeployment` en una ventana de Azure PowerShell. Vea el código siguiente para los parámetros que se pasan al comando. Para más información sobre cómo implementar un grupo de recursos con PowerShell, vea el artículo sobre la [implementación de un grupo de recursos con la plantilla de Azure Resource Manager](../azure-resource-manager/resource-group-template-deploy.md).

```powershell

New-AzureRmResourceGroupDeployment -ResourceGroupName $resourceGroupName -Name $deploymentName -TemplateFile $pathToARMConfigJsonFile -TemplateParameterFile $pathToParameterFile –Verbose
```

### <a name="deploy-the-diagnostics-extension-to-an-existing-cluster"></a>Implementación de la extensión de Diagnósticos en un clúster existente
Si tiene un clúster existente que no tiene implementada la extensión Diagnósticos, o bien quiere modificar una configuración existente, puede agregarla o actualizarla. Modifique la plantilla de Resource Manager usada para crear el clúster existente o descargue la plantilla desde el portal, tal como se describió anteriormente. Modifique el archivo template.json mediante la realización de las siguientes tareas.

Agregue un nuevo recurso de almacenamiento a la plantilla mediante una adición en la sección de recursos.

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

 A continuación, realice la adición en la sección de parámetros justo después de las definiciones de la cuenta de almacenamiento, entre `supportLogStorageAccountName` y `vmNodeType0Name`. Reemplace el texto de marcador de posición *aquí va el nombre de la cuenta de almacenamiento* por el nombre de la cuenta de almacenamiento.

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
Luego, actualice la sección `VirtualMachineProfile` del archivo template.json. Para ello, agregue el siguiente código dentro de la matriz de extensiones. Asegúrese de agregar una coma al principio o al final, según donde se inserte.

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

Después de modificar el archivo template.json tal como se indicó, vuelva a publicar la plantilla de Resource Manager. En caso de que la plantilla se haya exportado, si ejecuta el archivo deploy.ps1 , se vuelve a publicar dicha plantilla. Después de realizar la implementación, asegúrese de que el estado **ProvisioningState** sea **Correcto**.

## <a name="update-diagnostics-to-collect-health-and-load-events"></a>Actualización de diagnósticos para la recopilación de eventos de carga y estado de mantenimiento

A partir de la versión 5.4 de Service Fabric, los eventos de métricas de carga y estado de mantenimiento están disponibles para su recopilación. Estos eventos reflejan los eventos generados por el sistema o por el código mediante las API de generación de informes de estado de mantenimiento o carga como [ReportPartitionHealth](https://msdn.microsoft.com/library/azure/system.fabric.iservicepartition.reportpartitionhealth.aspx) o [ReportLoad](https://msdn.microsoft.com/library/azure/system.fabric.iservicepartition.reportload.aspx). Esto permite agregar y ver el estado de mantenimiento del sistema en el tiempo, y generar alertas basadas en eventos de estado de mantenimiento o carga. Para ver estos eventos en el Visor de eventos de diagnóstico de Visual Studio, agregue "Microsoft-ServiceFabric:4:0x4000000000000008" a la lista de proveedores de ETW.

Para recopilar los eventos, modifique la plantilla de Resource Manager para incluir lo siguiente:

```json
  "EtwManifestProviderConfiguration": [
    {
      "provider": "cbd93bc2-71e5-4566-b3a7-595d8eeca6e8",
      "scheduledTransferLogLevelFilter": "Information",
      "scheduledTransferKeywordFilter": "4611686018427387912",
      "scheduledTransferPeriod": "PT5M",
      "DefaultEvents": {
        "eventDestination": "ServiceFabricSystemEventTable"
      }
    }
```

## <a name="update-diagnostics-to-collect-and-upload-logs-from-new-eventsource-channels"></a>Actualización de Diagnósticos para recopilar y cargar registros desde nuevos canales EventSource
Para actualizar Diagnósticos de forma que recopile registros de canales EventSource nuevos que representan una nueva aplicación que vaya a implementar, siga los mismos pasos que en la [sección anterior](#deploywadarm) para configurar Diagnósticos para un clúster existente.

Actualice la sección `EtwEventSourceProviderConfiguration` del archivo template.json para agregar entradas para los canales EventSource nuevos antes de aplicar la actualización de la configuración mediante el comando `New-AzureRmResourceGroupDeployment` de PowerShell. El nombre del origen del evento se define como parte del código en el archivo generado en Visual Studio ServiceEventSource.cs.

Por ejemplo, si el origen del evento se denomina My-Eventsource, agregue el código siguiente para colocar los eventos de My-Eventsource en una tabla denominada MyDestinationTableName.

```json
        {
            "provider": "My-Eventsource",
            "scheduledTransferPeriod": "PT5M",
            "DefaultEvents": {
            "eventDestination": "MyDestinationTableName"
            }
        }
```

Para recopilar registros de eventos o contadores de rendimiento, modifique la plantilla de Resource Manager con los ejemplos proporcionados en [Creación de una máquina virtual de Windows con supervisión y diagnóstico mediante una plantilla de Azure Resource Manager](../virtual-machines/windows/extensions-diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Luego, vuelva a publicar la plantilla de Resource Manager.

## <a name="next-steps"></a>Pasos siguientes
Revise los eventos de diagnóstico emitidos para [Reliable Actors](service-fabric-reliable-actors-diagnostics.md) y [Reliable Services](service-fabric-reliable-services-diagnostics.md) para comprender más a fondo qué eventos debería examinar durante la solución de problemas.

## <a name="related-articles"></a>Artículos relacionados
* [Información sobre cómo recopilar registros o contadores de rendimiento mediante la extensión de Diagnósticos](../virtual-machines/windows/extensions-diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Service Fabric solution in Log Analytics](../log-analytics/log-analytics-service-fabric.md) (Solución de Service Fabric en Log Analytics)


