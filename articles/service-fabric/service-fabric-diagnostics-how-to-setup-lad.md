<properties
   pageTitle="Recopilación de registros con Diagnósticos de Azure de Linux | Microsoft Azure"
   description="En este artículo se describe cómo configurar Diagnósticos de Azure para recopilar registros de un clúster de Service Fabric de Linux que se ejecute en Azure."
   services="service-fabric"
   documentationCenter=".net"
   authors="mani-ramaswamy"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/28/2016"
   ms.author="subramar"/>


# Recopilación de registros con Diagnósticos de Azure

> [AZURE.SELECTOR]
- [Windows](service-fabric-diagnostics-how-to-setup-wad.md)
- [Linux](service-fabric-diagnostics-how-to-setup-lad.md)

Cuando se ejecuta un clúster de Azure Service Fabric, es conveniente recopilar los registros de todos los nodos en una ubicación central. La presencia de los registros en una ubicación central facilita el análisis y la solución de los problemas, ya estén en sus servicios, aplicaciones o en el propio clúster. Uno de los métodos para cargar y recopilar registros es usar la extensión de Diagnósticos de Azure que carga los registros en Almacenamiento de Azure. Puede leer los eventos desde el almacenamiento y colocarlos en un producto como [Elastic Search](service-fabric-diagnostic-how-to-use-elasticsearch.md) u otra solución de análisis de registro.

## Diferentes orígenes de registros que podría recopilar
1. **Registros de Service Fabric:** emitidos por la plataforma mediante LTTng y cargados en la cuenta de almacenamiento. Los registros pueden ser eventos operativos o eventos de tiempo de ejecución emitidos por la plataforma. Estos registros se almacenan en la ubicación indicada en el manifiesto de clúster (busque la etiqueta "AzureTableWinFabETWQueryable" y el archivo "StoreConnectionString" para obtener los detalles de la cuenta de almacenamiento).
2. **Eventos de aplicación:** eventos emitidos desde el código de servicios. Puede utilizar cualquier solución de registro que escriba archivos de registro basados en texto, por ejemplo, [LTTng](http://lttng.org). Consulte la documentación de LTTng sobre el seguimiento de la aplicación.


## Implementación de las extensiones de Diagnósticos
El primer paso para recopilar registros será implementar la extensión WAD en cada una de las máquinas virtuales del clúster de Service Fabric. La extensión de Diagnósticos recopila registros en cada máquina virtual y los carga a la cuenta de almacenamiento que especifique. Los pasos varían en función de si se utiliza Azure Portal o Azure Resource Manager.

### Implementación de la extensión de Diagnósticos como parte de la creación del clúster 
Para implementar la extensión de Diagnósticos en las máquinas virtuales del clúster como parte de la creación de dicho clúster, establezca "Diagnósticos" en **Activado**. Una vez creado el clúster, esta opción no se puede cambiar mediante el portal.

Configure Diagnósticos de Azure de Linux (LAD) para recopilar los archivos y colocarlos en la cuenta de almacenamiento de los clientes. Este proceso se explica en el Escenario 3 ("Carga de sus propios archivos de registro") en el artículo sobre cómo [utilizar LAD para supervisar y diagnosticar máquinas virtuales Linux](../virtual-machines/virtual-machines-linux-classic-diagnostic-extension.md). Siguiendo este proceso, obtiene acceso a los seguimientos que se pueden cargar en un visualizador de su elección.


También puede implementar la extensión de Diagnósticos mediante Azure Resource Manager. El proceso es el mismo para Windows y Linux, y está documentado para los clústeres de Windows en [Recopilación de registros con Diagnósticos de Azure](service-fabric-diagnostics-how-to-setup-wad.md).

Puede usar igualmente OMS tal como se describe en [Operations Management Suite Log Analytics with Linux](https://blogs.technet.microsoft.com/hybridcloud/2016/01/28/operations-management-suite-log-analytics-with-linux/) (Análisis de registro de Operations Management Suite con Linux).

Una vez finalizada esta configuración, el agente de LAD supervisa los archivos de registro especificados. Siempre que se anexa una nueva línea al archivo, se crea una entrada syslog que se envía al almacenamiento especificado por el cliente.


## Pasos siguientes
Compruebe la [documentación de LTTng](http://lttng.org/docs) y el [uso de LAD](../virtual-machines/virtual-machines-linux-classic-diagnostic-extension.md) para entender con más detalle qué eventos debe examinar durante la solución de problemas.

<!---HONumber=AcomDC_0928_2016-->