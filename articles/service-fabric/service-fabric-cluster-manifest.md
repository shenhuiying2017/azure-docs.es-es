---
title: "Configuración de clústeres independientes | Microsoft Docs"
description: "En este artículo se describe cómo configurar un clúster de Service Fabric privado o independiente."
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 0c5ec720-8f70-40bd-9f86-cd07b84a219d
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/12/2016
ms.author: ryanwi
translationtype: Human Translation
ms.sourcegitcommit: 4fb6ef56d694aff967840ab26b75b66a2e799cc1
ms.openlocfilehash: 977de9160be63a91b5926daa45528e5ee205e448


---
# <a name="configuration-settings-for-standalone-windows-cluster"></a>Opciones de configuración de clústeres de Windows independientes
En este artículo se describe cómo configurar un clúster de Service Fabric independiente con el archivo ***ClusterConfig.JSON***. Puede usar este archivo para especificar información tal como los nodos de Service Fabric y sus direcciones IP, los distintos tipos de nodos en el clúster, las configuraciones de seguridad y la topología de red (dominios de error o actualización) para el clúster independiente.

Cuando [descarga el paquete de Service Fabric independiente](service-fabric-cluster-creation-for-windows-server.md#downloadpackage), se descargan unos pocos ejemplos del archivo ClusterConfig.JSON en la máquina de trabajo. Los ejemplos que tengan *DevCluster* en sus nombres, le permitirán crear un clúster con los tres nodos en la misma máquina, como nodos lógicos. Fuera de estos casos, al menos un nodo debe marcarse como un nodo principal. Este clúster es útil para un entorno de desarrollo o pruebas y no se admite como clúster de producción. Los ejemplos que tengan *MultiMachine* en sus nombres, le ayudarán a crear un clúster de calidad de producción, con cada nodo en una máquina independiente. El número de nodos principales para estos clústeres se basará en el [nivel de confiabilidad](#reliability).

1. *ClusterConfig.Unsecure.DevCluster.JSON* y *ClusterConfig.Unsecure.MultiMachine.JSON* muestran cómo crear un clúster no seguro de prueba o producción no seguro, respectivamente. 
2. *ClusterConfig.Windows.DevCluster.JSON* y  *ClusterConfig.Windows.MultiMachine.JSON* muestran cómo crear el clúster de prueba o producción, protegido con [seguridad de Windows](service-fabric-windows-cluster-windows-security.md).
3. *ClusterConfig.X509.DevCluster.JSON* y *ClusterConfig.X509.MultiMachine.JSON* muestran cómo crear el clúster de prueba o producción, protegido con [seguridad basada en certificados X509](service-fabric-windows-cluster-x509-security.md). 

Ahora examinaremos las distintas secciones de un archivo ***ClusterConfig.JSON***, tal como se muestra a continuación.

## <a name="general-cluster-configurations"></a>Opciones generales de configuración de clústeres
Describe las opciones de configuración generales del clúster, como se muestra en el siguiente fragmento de código JSON.

    "name": "SampleCluster",
    "clusterConfigurationVersion": "1.0.0",
    "apiVersion": "2016-09-26",

Asigne cualquier nombre descriptivo al clúster de Service Fabric en la variable **name** . **clusterConfigurationVersion** es el número de versión del clúster; debe aumentarlo cada vez que actualice el clúster de Service Fabric. Sin embargo, debe dejar **apiVersion** en el valor predeterminado.

<a id="clusternodes"></a>

## <a name="nodes-on-the-cluster"></a>Nodos del clúster
Puede configurar los nodos en el clúster de Service Fabric mediante la sección **nodos** , como se muestra en el siguiente fragmento de código.

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

Un clúster de Service Fabric debe contener al menos 3 nodos. Puede agregar más nodos a esta sección según su configuración. La siguiente tabla explica las opciones de configuración de cada nodo.

| **Opción de configuración del nodo** | **Descripción** |
| --- | --- |
| nodeName |Puede asignar cualquier nombre descriptivo al nodo. |
| iPAddress |Para averiguar la dirección IP del nodo, abra una ventana de comandos y escriba `ipconfig`. Anote la dirección IPV4 y asígnela a la variable **iPAddress** . |
| nodeTypeRef |Cada nodo se puede asignar a un tipo de nodo diferente. Los [tipos de nodos](#nodetypes) se definen en la sección siguiente. |
| faultDomain |Los dominios de error permiten a los administradores de clústeres definir los nodos físicos que es probable que experimenten errores al mismo tiempo debido a las dependencias físicas compartidas. |
| upgradeDomain |Los dominios de actualización describen conjuntos de nodos que se apagan para las actualizaciones de Service Fabric aproximadamente al mismo tiempo. Puede elegir qué nodos desea asignar a cada dominio de actualización porque no están limitados por ningún requisito físico. |

## <a name="cluster-properties"></a>Propiedades de clúster
La sección **properties** del archivo ClusterConfig.JSON se usa para configurar el clúster de la siguiente manera.

<a id="reliability"></a>

### <a name="reliability"></a>Confiabilidad
La sección **reliabilityLevel** define el número de copias de los servicios del sistema que se pueden ejecutar en los nodos principales del clúster. Esto aumenta la confiabilidad de estos servicios y, por lo tanto, el clúster. Puede establecer esta variable en *Bronce*, *Plata*, *Oro* o *Platino* para 3, 5, 7 o 9 copias de estos servicios, respectivamente. Vea el ejemplo siguiente.

    "reliabilityLevel": "Bronze",

Como un nodo principal ejecuta una única copia de los servicios del sistema, tenga en cuenta que necesitaría un mínimo de 3 nodos principales para el nivel de confiabilidad *Bronce*, 5 para *Plata*, 7 para *Oro* y 9 para *Platino*.

### <a name="diagnostics"></a>Diagnóstico
La sección **diagnosticsStore** permite configurar parámetros para habilitar el diagnóstico y la solución de problemas de errores de nodos o clústeres, tal y como se muestra en el siguiente fragmento de código. 

    "diagnosticsStore": {
        "metadata":  "Please replace the diagnostics store with an actual file share accessible from all cluster machines.",
        "dataDeletionAgeInDays": "7",
        "storeType": "FileShare",
        "IsEncrypted": "false",
        "connectionstring": "c:\\ProgramData\\SF\\DiagnosticsStore"
    }

**metadata** es una descripción del diagnóstico del clúster y se puede establecer según su configuración. Estas variables permiten recopilar registros de seguimiento ETW, volcados de memoria y contadores de rendimiento. Lea [Tracelog](https://msdn.microsoft.com/library/windows/hardware/ff552994.aspx) y [Seguimiento ETW](https://msdn.microsoft.com/library/ms751538.aspx) para más información sobre los registros de seguimiento ETW. Todos los registros, incluidos [volcados de memoria](https://blogs.technet.microsoft.com/askperf/2008/01/08/understanding-crash-dump-files/) y [contadores de rendimiento](https://msdn.microsoft.com/library/windows/desktop/aa373083.aspx) se pueden dirigir a la carpeta **connectionString** de su máquina. También puede usar *AzureStorage* para almacenar diagnósticos. Vea a continuación un ejemplo de fragmento de código.

    "diagnosticsStore": {
        "metadata":  "Please replace the diagnostics store with an actual file share accessible from all cluster machines.",
        "dataDeletionAgeInDays": "7",
        "storeType": "AzureStorage",
        "IsEncrypted": "false",
        "connectionstring": "xstore:DefaultEndpointsProtocol=https;AccountName=[AzureAccountName];AccountKey=[AzureAccountKey]"
    }

### <a name="security"></a>Seguridad
La sección **security** es necesaria para un clúster de Service Fabric independiente protegido. El siguiente fragmento de código muestra una parte de esta sección.

    "security": {
        "metadata": "This cluster is secured using X509 certificates.",
        "ClusterCredentialType": "X509",
        "ServerCredentialType": "X509",
        . . .
    }

**metadata** es una descripción del clúster protegido y se puede establecer según su configuración. Los valores de **ClusterCredentialType** y **ServerCredentialType** determinan el tipo de seguridad que implementarán el clúster y los nodos. Se pueden establecer en *X509* para una seguridad basada en certificados, o en *Windows* para una seguridad basada en Azure Active Directory. El resto de la sección **security** se basará en el tipo de la seguridad. Lea [Protección de un clúster independiente en Windows mediante certificados ](service-fabric-windows-cluster-x509-security.md) o [Proteger un clúster independiente en Windows mediante la seguridad de Windows](service-fabric-windows-cluster-windows-security.md) para más información sobre cómo rellenar el resto de la sección **security**.

<a id="nodetypes"></a>

### <a name="node-types"></a>Tipos de nodo
La sección **nodeTypes** describe el tipo de los nodos que tiene el clúster. Se debe especificar al menos un tipo de nodo en un clúster, tal y como se muestra en el siguiente fragmento de código. 

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

**name** es el nombre descriptivo de este tipo de nodo específico. Para crear un nodo de este tipo, asigne el nombre descriptivo a la variable **nodeTypeRef** para ese nodo, como se mencionó [anteriormente](#clusternodes). Para cada tipo de nodo, defina los puntos de conexión que se utilizarán. Puede elegir cualquier número de puerto para estos puntos de conexión, siempre que no entren en conflicto con otros puntos de conexión de este clúster. En un clúster de varios nodos, habrá uno o más nodos principales (es decir, **isPrimary** establecido en *true*), en función de [**reliabilityLevel**](#reliability). Lea [Consideraciones de planeación de capacidad del clúster de Service Fabric](service-fabric-cluster-capacity.md) para más información sobre los valores **nodeTypes** y **reliabilityLevel** y para conocer las diferencias entre los tipos de nodo principal y no principal. 

#### <a name="endpoints-used-to-configure-the-node-types"></a>Puntos de conexión que se usan para configurar los tipos de nodo
* *clientConnectionEndpointPort* es el puerto que utiliza el cliente para conectarse al clúster, cuando use las API de cliente. 
* *clusterConnectionEndpointPort* es el puerto en el que los nodos se comunican entre sí.
* *leaseDriverEndpointPort* es el puerto utilizado por el controlador de concesión de clúster para averiguar si los nodos siguen estando activos. 
* *serviceConnectionEndpointPort* es el puerto que utilizan las aplicaciones y los servicios implementados en un nodo, para comunicarse con el cliente de Service Fabric en ese nodo concreto.
* *httpGatewayEndpointPort* es el puerto que utiliza Service Fabric Explorer para conectarse con el clúster.
* *ephemeralPorts* invalida los [puertos dinámicos que utiliza el sistema operativo](https://support.microsoft.com/kb/929851). Service Fabric usará una parte de ellos como puertos de aplicación y el resto estará disponible para el sistema operativo. También asigna este intervalo al intervalo existente en el sistema operativo, por lo que para todos los propósitos, puede utilizar los intervalos dados en los archivos JSON de ejemplo. Debe asegurarse de que la diferencia entre los puertos de inicio y finalización es al menos de 255. Puede encontrarse con conflictos si esta diferencia es demasiado baja, ya que este intervalo se comparte con el sistema operativo. Vea el intervalo de puertos dinámicos configurado ejecutando `netsh int ipv4 show dynamicport tcp`.
* *applicationPorts* son los puertos que utilizarán las aplicaciones de Service Fabric. Deben ser un subconjunto de *ephemeralPorts*, suficiente para cubrir el requisito de punto de conexión de las aplicaciones. Service Fabric los usará siembre que se requieran nuevos puertos, así que tenga en cuenta que debe abrir el firewall para estos puertos. 
* *reverseProxyEndpointPort* es un punto de conexión de proxy inverso opcional. Consulte [Proxy inverso de Service Fabric](service-fabric-reverseproxy.md) para más detalles. 

### <a name="other-settings"></a>Otras configuraciones
La sección **fabricSettings** permite establecer los directorios raíz de los registros y datos de Service Fabric. Puede personalizarlos solo durante la creación inicial del clúster. Vea el siguiente fragmento de código de ejemplo de esta sección.

    "fabricSettings": [{
        "name": "Setup",
        "parameters": [{
            "name": "FabricDataRoot",
            "value": "C:\\ProgramData\\SF"
        }, {
            "name": "FabricLogRoot",
            "value": "C:\\ProgramData\\SF\\Log"
    }]

Se recomienda usar una unidad sin sistema operativo como FabricDataRoot y FabricLogRoot, dado que proporciona una mayor confiabilidad frente a bloqueos del sistema operativo. Tenga en cuenta que si solo personaliza la raíz de los datos, la raíz del registro se colocará un nivel por debajo de la raíz de los datos.

## <a name="next-steps"></a>Pasos siguientes
Cuando ya tenga un archivo ClusterConfig.JSON completo configurado según la configuración del clúster independiente, puede implementar el clúster siguiendo el artículo [Creación de un clúster de Azure Service Fabric independientes](service-fabric-cluster-creation-for-windows-server.md) y luego continúe con [Visualización del clúster mediante Service Fabric Explorer](service-fabric-visualizing-your-cluster.md).




<!--HONumber=Dec16_HO2-->


