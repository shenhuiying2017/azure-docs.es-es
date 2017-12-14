---
title: "Configuración del clúster de Azure Service Fabric independiente | Microsoft Docs"
description: "Aprenda a configurar un clúster de Azure Service Fabric independiente o local."
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: 0c5ec720-8f70-40bd-9f86-cd07b84a219d
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/06/2017
ms.author: dekapur
ms.openlocfilehash: bd6e5c1591d01329d95ccb168e5a14e436920baf
ms.sourcegitcommit: cc03e42cffdec775515f489fa8e02edd35fd83dc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/07/2017
---
# <a name="configuration-settings-for-a-standalone-windows-cluster"></a>Opciones de configuración de un clúster de Windows independiente
En este artículo se describe cómo configurar un clúster de Azure Service Fabric independiente con el archivo ClusterConfig.json. Usará este archivo para especificar la información acerca de los nodos del clúster y las configuraciones de seguridad, así como la topología de red en cuanto a dominios de error y actualización.

Cuando se [descarga el paquete de Service Fabric independiente](service-fabric-cluster-creation-for-windows-server.md#downloadpackage), también se incluyen ejemplos de ClusterConfig.json. Los ejemplos que tengan "DevCluster" en sus nombres crean un clúster con los tres nodos en la misma máquina, con nodos lógicos. Fuera de estos nodos, al menos un nodo debe marcarse como nodo principal. Este tipo de clúster es útil para un entorno de desarrollo o de prueba. No se admite como un clúster de producción. Los ejemplos que tengan "MultiMachine" en sus nombres ayudan a crear un clúster de calidad de producción, con cada nodo en una máquina independiente. El número de nodos principales en estos clústeres se basa en el [nivel de confiabilidad](#reliability) del clúster. En la API 5.7, versión de mayo de 2017, se eliminó la propiedad de nivel de confiabilidad. En su lugar, el código calcula el nivel de confiabilidad más optimizado para su clúster. No intente establecer un valor para esta propiedad en la versión 5.7 y posteriores.


* ClusterConfig.Unsecure.DevCluster.json y ClusterConfig.Unsecure.MultiMachine.json muestran cómo crear un clúster no seguro de prueba o producción, respectivamente.

* ClusterConfig.Windows.DevCluster.json y ClusterConfig.Windows.MultiMachine.json muestran cómo crear un clúster de prueba o producción protegido con [seguridad de Windows](service-fabric-windows-cluster-windows-security.md).

* ClusterConfig.X509.DevCluster.json y ClusterConfig.X509.MultiMachine.json muestran cómo crear un clúster de prueba o producción protegido con [seguridad basada en certificados X509](service-fabric-windows-cluster-x509-security.md).

Ahora examinaremos las distintas secciones de un archivo ClusterConfig.json.

## <a name="general-cluster-configurations"></a>Opciones generales de configuración de clústeres
Las configuraciones de clúster generales abarcan las configuraciones específicas del clúster amplias, como se muestra en el siguiente fragmento JSON:

```json
    "name": "SampleCluster",
    "clusterConfigurationVersion": "1.0.0",
    "apiVersion": "01-2017",
```

Asigne cualquier nombre descriptivo al clúster de Service Fabric en la variable name. El valor de clusterConfigurationVersion representa el número de versión del clúster. Increméntelo cada vez que actualice el clúster de Service Fabric. Deje el elemento apiVersion con el valor predeterminado.

## <a name="nodes-on-the-cluster"></a>Nodos del clúster

    <a id="clusternodes"></a>

Puede configurar los nodos en el clúster de Service Fabric mediante la sección nodes, como se muestra en el siguiente fragmento de código:

    "nodes": [{
        "nodeName": "vm0",
        "iPAddress": "localhost",
        "nodeTypeRef": "NodeType0",
        "faultDomain": "fd:/dc1/r0",
        "upgradeDomain": "UD0"
    }, {
        "nodeName": "vm1",
        "iPAddress": "localhost",
        "nodeTypeRef": "NodeType1",
        "faultDomain": "fd:/dc1/r1",
        "upgradeDomain": "UD1"
    }, {
        "nodeName": "vm2",
        "iPAddress": "localhost",
        "nodeTypeRef": "NodeType2",
        "faultDomain": "fd:/dc1/r2",
        "upgradeDomain": "UD2"
    }],

Un clúster de Service Fabric debe contener al menos tres nodos. Puede agregar más nodos a esta sección según su configuración. En la siguiente tabla se explican las opciones de configuración de cada nodo:

| **Opción de configuración del nodo** | **Descripción** |
| --- | --- |
| nodeName |Puede asignar cualquier nombre descriptivo al nodo. |
| iPAddress |Para averiguar la dirección IP del nodo, abra una ventana de comandos y escriba `ipconfig`. Anote la dirección IPV4 y asígnela a la variable iPAddress. |
| nodeTypeRef |Cada nodo se puede asignar a un tipo de nodo diferente. Los [tipos de nodos](#node-types) se definen en la sección siguiente. |
| faultDomain |Los dominios de error permiten a los administradores de clústeres definir los nodos físicos que es probable que experimenten errores al mismo tiempo debido a las dependencias físicas compartidas. |
| upgradeDomain |Los dominios de actualización describen conjuntos de nodos que se apagan para las actualizaciones de Service Fabric aproximadamente al mismo tiempo. Puede elegir qué nodos desea asignar a cada dominio de actualización, porque no están limitados por ningún requisito físico. |

## <a name="cluster-properties"></a>Propiedades de clúster
La sección properties del archivo ClusterConfig.json se usa para configurar el clúster de la siguiente manera:

### <a name="reliability"></a>Confiabilidad
El concepto de reliabilityLevel define el número de réplicas o instancias de los servicios del sistema de Service Fabric que se pueden ejecutar en los nodos principales del clúster. Esto determina la confiabilidad de estos servicios y, por lo tanto, el clúster. El sistema calcula el valor en la creación del clúster y durante la actualización.

    <a id="reliability"></a>

### <a name="diagnostics"></a>Diagnóstico
La sección diagnosticsStore permite configurar parámetros para habilitar el diagnóstico y la solución de problemas de errores de nodos o clústeres, tal y como se muestra en el siguiente fragmento de código: 

    "diagnosticsStore": {
        "metadata":  "Please replace the diagnostics store with an actual file share accessible from all cluster machines.",
        "dataDeletionAgeInDays": "7",
        "storeType": "FileShare",
        "IsEncrypted": "false",
        "connectionstring": "c:\\ProgramData\\SF\\DiagnosticsStore"
    }

metadata es una descripción del diagnóstico del clúster y se puede establecer según su configuración. Estas variables permiten recopilar registros de seguimiento ETW, volcados de memoria y contadores de rendimiento. Lea [Tracelog](https://msdn.microsoft.com/library/windows/hardware/ff552994.aspx) y [Seguimiento ETW](https://msdn.microsoft.com/library/ms751538.aspx) para más información sobre los registros de seguimiento ETW. Todos los registros, incluidos [volcados de memoria](https://blogs.technet.microsoft.com/askperf/2008/01/08/understanding-crash-dump-files/) y [contadores de rendimiento](https://msdn.microsoft.com/library/windows/desktop/aa373083.aspx), se pueden dirigir a la carpeta connectionString de su máquina. También puede usar AzureStorage para almacenar los diagnósticos. Vea el siguiente fragmento de ejemplo:

    "diagnosticsStore": {
        "metadata":  "Please replace the diagnostics store with an actual file share accessible from all cluster machines.",
        "dataDeletionAgeInDays": "7",
        "storeType": "AzureStorage",
        "IsEncrypted": "false",
        "connectionstring": "xstore:DefaultEndpointsProtocol=https;AccountName=[AzureAccountName];AccountKey=[AzureAccountKey]"
    }

### <a name="security"></a>Seguridad
La sección security es necesaria para un clúster de Service Fabric independiente protegido. El siguiente fragmento de código muestra una parte de esta sección:

    "security": {
        "metadata": "This cluster is secured using X509 certificates.",
        "ClusterCredentialType": "X509",
        "ServerCredentialType": "X509",
        . . .
    }

El valor metadata es una descripción del clúster protegido y se puede establecer según su configuración. Los valores de ClusterCredentialType y ServerCredentialType determinan el tipo de seguridad que implementan el clúster y los nodos. Se pueden establecer en *X509* para una seguridad basada en certificados, o en *Windows* para una seguridad basada en Azure Active Directory. El resto de la sección security se basará en el tipo de la seguridad. Lea [Protección de un clúster independiente en Windows mediante certificados ](service-fabric-windows-cluster-x509-security.md) o [Proteger un clúster independiente en Windows mediante la seguridad de Windows](service-fabric-windows-cluster-windows-security.md) para obtener más información sobre cómo rellenar el resto de la sección security.

### <a name="node-types"></a>Tipos de nodo

    <a id="nodetypes"></a>

La sección nodeTypes describe el tipo de los nodos que tiene el clúster. Se debe especificar al menos un tipo de nodo en un clúster, tal y como se muestra en el siguiente fragmento de código: 

    "nodeTypes": [{
        "name": "NodeType0",
        "clientConnectionEndpointPort": "19000",
        "clusterConnectionEndpointPort": "19001",
        "leaseDriverEndpointPort": "19002"
        "serviceConnectionEndpointPort": "19003",
        "httpGatewayEndpointPort": "19080",
        "reverseProxyEndpointPort": "19081",
        "applicationPorts": {
            "startPort": "20575",
            "endPort": "20605"
        },
        "ephemeralPorts": {
            "startPort": "20606",
            "endPort": "20861"
        },
        "isPrimary": true
    }]

El valor name es el nombre descriptivo de este tipo de nodo específico. Para crear un nodo de este tipo, asigne el nombre descriptivo a la variable nodeTypeRef para ese nodo, [como se mencionó anteriormente](#nodes-on-the-cluster). Para cada tipo de nodo, defina los puntos de conexión que se utilizan. Puede elegir cualquier número de puerto para estos puntos de conexión, siempre que no entren en conflicto con otros puntos de conexión de este clúster. En un clúster de varios nodos, hay uno o varios nodos principales (es decir, isPrimary establecido en *true*), en función de [reliabilityLevel](#reliability). Lea [Consideraciones de planeación de capacidad del clúster de Service Fabric](service-fabric-cluster-capacity.md) para obtener más información sobre los valores nodeTypes y reliabilityLevel y para conocer las diferencias entre los tipos de nodo principal y no principal. 

#### <a name="endpoints-used-to-configure-the-node-types"></a>Puntos de conexión que se usan para configurar los tipos de nodo
* clientConnectionEndpointPort es el puerto que utiliza el cliente para conectarse al clúster cuando use las API de cliente. 
* clusterConnectionEndpointPort es el puerto en el que los nodos se comunican entre sí.
* leaseDriverEndpointPort es el puerto utilizado por el controlador de concesión de clúster para averiguar si los nodos siguen estando activos. 
* serviceConnectionEndpointPort es el puerto que utilizan las aplicaciones y los servicios implementados en un nodo para comunicarse con el cliente de Service Fabric en ese nodo concreto.
* httpGatewayEndpointPort es el puerto que utiliza Service Fabric Explorer para conectarse con el clúster.
* ephemeralPorts invalida los [puertos dinámicos que utiliza el sistema operativo](https://support.microsoft.com/kb/929851). Service Fabric usará una parte de estos puertos como puertos de aplicación, y el resto estará disponible para el sistema operativo. También asigna este intervalo al intervalo existente en el sistema operativo, por lo que para todos los propósitos, puede utilizar los intervalos dados en los archivos JSON de ejemplo. Asegúrese de que la diferencia entre los puertos de inicio y finalización es al menos de 255. Puede encontrarse con conflictos si esta diferencia es demasiado baja, ya que este intervalo se comparte con el sistema operativo. Para ver el intervalo de puertos dinámicos configurado, ejecute `netsh int ipv4 show dynamicport tcp`.
* applicationPorts son los puertos que utilizarán las aplicaciones de Service Fabric. El intervalo de puertos de las aplicaciones debería ser lo suficientemente amplio como para contemplar el requisito de punto de conexión de estas. Este intervalo debería quedar excluido del intervalo de puertos dinámicos de la máquina, es decir, el intervalo ephemeralPorts tal y como se establece en la configuración. Service Fabric los usa siempre que se requieren nuevos puertos, y toma en cuenta la apertura del firewall para estos. 
* reverseProxyEndpointPort es un punto de conexión de proxy inverso opcional. Para obtener más información, consulte [Proxy inverso de Azure Service Fabric](service-fabric-reverseproxy.md). 

### <a name="log-settings"></a>Configuración del registro
La sección fabricSettings permite establecer los directorios raíz de los registros y datos de Service Fabric. Puede personalizarlos solo durante la creación inicial del clúster. Vea el siguiente fragmento de ejemplo de esta sección:

    "fabricSettings": [{
        "name": "Setup",
        "parameters": [{
            "name": "FabricDataRoot",
            "value": "C:\\ProgramData\\SF"
        }, {
            "name": "FabricLogRoot",
            "value": "C:\\ProgramData\\SF\\Log"
    }]

Se recomienda que use una unidad que no sea de SO como FabricDataRoot y FabricLogRoot. Proporciona más confiabilidad al evitar situaciones en las que el sistema operativo deja de responder. Si solo personaliza la raíz de los datos, la raíz del registro se coloca un nivel por debajo de la raíz de los datos.

### <a name="stateful-reliable-services-settings"></a>Configuración de Reliable Services con estado
La sección KtlLogger permite definir la configuración global de Reliable Services. Para obtener más información sobre estas opciones, lea [Configurar Reliable Services con estado](service-fabric-reliable-services-configuration.md). En el ejemplo siguiente se muestra cómo cambiar el registro de transacciones compartido que se crea para realizar copias de cualquier colección confiable de servicios con estado:

    "fabricSettings": [{
        "name": "KtlLogger",
        "parameters": [{
            "name": "SharedLogSizeInMB",
            "value": "4096"
        }]
    }]

### <a name="add-on-features"></a>Características complementarias
Para configurar características complementarias, el valor de apiVersion debe configurarse como 04-2017 o superior y addonFeatures como se muestra aquí:

    "apiVersion": "04-2017",
    "properties": {
      "addOnFeatures": [
          "DnsService",
          "RepairManager"
      ]
    }

### <a name="container-support"></a>Compatibilidad con los contenedores
Para habilitar la compatibilidad con un contenedor de Windows Server o Hyper-V para clústeres independientes, debe habilitarse la característica complementaria DnsService.


## <a name="next-steps"></a>Pasos siguientes
Una vez que haya configurado un archivo ClusterConfig.json completo de acuerdo con la configuración de clúster independiente, puede implementar el clúster. Siga los pasos de [Creación de un clúster de Azure Service Fabric independiente](service-fabric-cluster-creation-for-windows-server.md). A continuación, continúe con [Visualización del clúster mediante Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) y siga los pasos.

