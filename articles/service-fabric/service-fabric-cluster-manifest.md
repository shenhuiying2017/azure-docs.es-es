<properties
   pageTitle="Configuración de un clúster independiente | Microsoft Azure"
   description="En este artículo se describe cómo configurar un clúster de Service Fabric privado o independiente."
   services="service-fabric"
   documentationCenter=".net"
   authors="dsk-2015"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="06/23/2016"
   ms.author="dkshir"/>


# Opciones de configuración de clústeres de Windows independientes

En este artículo se describe cómo configurar un clúster de Service Fabric independiente con el archivo _**ClusterConfig.JSON**_. Este archivo se descarga en el equipo de trabajo cuando se [descarga el paquete de Service Fabric independiente](service-fabric-cluster-creation-for-windows-server.md#downloadpackage). El archivo ClusterConfig.JSON permite especificar información tal como los nodos de Service Fabric y sus direcciones IP, los distintos tipos de nodos en el clúster, las configuraciones de seguridad y la topología de red (dominios de error o actualización) para el clúster de Service Fabric.

A continuación examinaremos las diversas secciones de este archivo.

## Opciones generales de configuración de clústeres
Describe las opciones de configuración generales del clúster, como se muestra en el siguiente fragmento de código JSON.

    "name": "SampleCluster",
    "clusterConfigurationVersion": "1.0.0",
    "apiVersion": "2016-09-26",

Asigne cualquier nombre descriptivo al clúster de Service Fabric en la variable **name**. Puede cambiar el valor de **clusterManifestVersion** según su configuración; deberá actualizarlo antes de actualizar la configuración de Service Fabric. Puede dejar el valor predeterminado de **apiVersion**.


<a id="clusternodes"></a>
## Nodos del clúster
Puede configurar los nodos en el clúster de Service Fabric mediante la sección **nodos**, como se muestra en el siguiente fragmento de código.

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

Un clúster de Service Fabric necesita un mínimo de 3 nodos. Puede agregar más nodos a esta sección según su configuración. La siguiente tabla explica las opciones de configuración de cada nodo.

|**Opción de configuración del nodo**|**Descripción**|
|-----------------------|--------------------------|
|nodeName|Puede asignar cualquier nombre descriptivo al nodo.|
|iPAddress|Para averiguar la dirección IP del nodo, abra una ventana de comandos y escriba `ipconfig`. Anote la dirección IPV4 y asígnela a la variable **iPAddress**.|
|nodeTypeRef|Cada nodo se puede asignar a un tipo de nodo diferente. Los [tipos de nodos](#nodetypes) se definen en la sección siguiente.|
|faultDomain|Los dominios de error permiten a los administradores de clústeres definir los nodos físicos que es probable que experimenten errores al mismo tiempo debido a las dependencias físicas compartidas.|
|upgradeDomain|Los dominios de actualización describen conjuntos de nodos que se apagan para las actualizaciones de Service Fabric aproximadamente al mismo tiempo. Puede elegir qué nodos desea asignar a cada dominio de actualización porque no están limitados por ningún requisito físico.| 


## **Propiedades** del clúster

La sección **properties** del archivo ClusterConfig.JSON se usa para configurar el clúster de la siguiente manera.

### **diagnosticsStore**
Para configurar los parámetros que habilitan el diagnóstico y la solución de problemas de errores de nodos y clústeres, use la sección **diagnosticsStore** tal y como se muestra en el siguiente fragmento de código.

    "diagnosticsStore": {
        "metadata":  "Please replace the diagnostics store with an actual file share accessible from all cluster machines.",
        "dataDeletionAgeInDays": "7",
        "storeType": "FileShare",
        "IsEncrypted": "false",
        "connectionstring": "c:\\ProgramData\\SF\\DiagnosticsStore"
    }

**metadata** es una descripción del diagnóstico del clúster y se puede establecer según su configuración. Estas variables permiten recopilar registros de seguimiento ETW, volcados de memoria y contadores de rendimiento. Lea [Tracelog](https://msdn.microsoft.com/library/windows/hardware/ff552994.aspx) y [Seguimiento ETW](https://msdn.microsoft.com/library/ms751538.aspx) para más información sobre los registros de seguimiento ETW. Todos los registros, incluidos [volcados](https://blogs.technet.microsoft.com/askperf/2008/01/08/understanding-crash-dump-files/) y [contadores de rendimiento](https://msdn.microsoft.com/library/windows/desktop/aa373083.aspx) se pueden dirigir a la carpeta **connectionString** de su máquina. También puede usar **AzureStorage** para almacenar diagnósticos. Vea a continuación un ejemplo de fragmento de código.

	"diagnosticsStore": {
        "metadata":  "Please replace the diagnostics store with an actual file share accessible from all cluster machines.",
        "dataDeletionAgeInDays": "7",
        "storeType": "AzureStorage",
        "IsEncrypted": "false",
        "connectionstring": "xstore:DefaultEndpointsProtocol=https;AccountName=[AzureAccountName];AccountKey=[AzureAccountKey]"
    }

### **security** 
La sección **security** es necesaria para un clúster de Service Fabric independiente protegido. El siguiente fragmento de código muestra una parte de esta sección.

    "security": {
        "metadata": "This cluster is secured using X509 certificates.",
        "ClusterCredentialType": "X509",
        "ServerCredentialType": "X509",
		. . .
	}

**metadata** es una descripción del clúster protegido y se puede establecer según su configuración. Los valores de **ClusterCredentialType** y **ServerCredentialType** determinan el tipo de seguridad que implementarán el clúster y los nodos. Se pueden establecer en *X509* para una seguridad basada en certificados, o en *Windows* para una seguridad basada en Azure Active Directory. El resto de la sección **security** se basará en el tipo de la seguridad. Lea [Seguridad basada en certificados en un clúster independiente](service-fabric-windows-cluster-x509-security.md) o [Seguridad de Windows en un clúster independiente](service-fabric-windows-cluster-windows-security.md) para más información sobre cómo rellenar el resto de la sección **security**.

### **reliabilityLevel**
**reliabilityLevel** define el número de copias de los servicios del sistema que se pueden ejecutar en los nodos principales del clúster. Esto aumenta la confiabilidad de estos servicios y, por lo tanto, el clúster. Puede establecer esta variable en *Bronce*, *Silver*, *Gold* o *Platinum* para 3, 5, 7 o 9 copias de estos servicios, respectivamente. Vea el ejemplo siguiente.

	"reliabilityLevel": "Bronze",
	
Como un nodo principal ejecuta una única copia de los servicios del sistema, tenga en cuenta que necesitaría un mínimo de 3 nodos principales para el nivel de confiabilidad *Bronce*, 5 para *Silver*, 7 para *Gold* y 9 para *Platinum*.


<a id="nodetypes"></a>
### **nodeTypes**
La sección **nodeTypes** describe el tipo de los nodos que tiene el clúster. Se debe especificar al menos un tipo de nodo en un clúster, tal y como se muestra en el siguiente fragmento de código.

	"nodeTypes": [{
        "name": "NodeType0",
        "clientConnectionEndpointPort": "19000",
        "clusterConnectionEndpoint": "19001",
        "httpGatewayEndpointPort": "19080",
        "applicationPorts": {
			"startPort": "20001",
            "endPort": "20031"
        },
        "ephemeralPorts": {
            "startPort": "20032",
            "endPort": "20062"
        },
        "isPrimary": true
    }]

**name** es el nombre descriptivo de este tipo de nodo específico. Para crear un nodo de este tipo, debe asignar el nombre descriptivo de este tipo de nodo a la variable **nodeTypeRef** de ese nodo, tal y como se mencionó en la sección [Nodos del clúster](#clusternodes) anterior. Para cada tipo de nodo, puede definir varios puntos de conexión a este clúster. Puede elegir cualquier número de puerto para estos puntos de conexión, siempre que no entren en conflicto con otros puntos de conexión de este clúster. En un clúster con varios tipos de nodos, habrá un tipo de nodo principal, cuyo valor de **isPrimary** es *true*. En el resto de los nodos, el valor de **isPrimary** es *false*. Lea [Consideraciones de planeación de capacidad del clúster de Service Fabric](service-fabric-cluster-capacity.md) para más información sobre los valores **nodeTypes** y **reliabilityLevel** según la capacidad de su clúster, y para conocer las diferencias entre los tipos de nodo principal y no principal.


### **fabricSettings**
Esta sección permite establecer los directorios raíz de los registros y datos de Service Fabric. Puede personalizarlos solo durante la creación inicial del clúster. Vea el siguiente fragmento de código de ejemplo de esta sección.

    "fabricSettings": [{
        "name": "Setup",
        "parameters": [{
            "name": "FabricDataRoot",
            "value": "C:\ProgramData\SF"
        }, {
            "name": "FabricLogRoot",
            "value": "C:\ProgramData\SF\Log"
    }]

Se recomienda usar una unidad sin sistema operativo como FabricDataRoot y FabricLogRoot, dado que proporciona una mayor confiabilidad frente a bloqueos del sistema operativo. Tenga en cuenta que si solo personaliza la raíz de los datos, la raíz del registro se colocará un nivel por debajo de la raíz de los datos.


## Pasos siguientes

Cuando ya tenga un archivo ClusterConfig.JSON completo configurado según la configuración del clúster independiente, puede implementar el clúster siguiendo el artículo [Creación de un clúster de Azure Service Fabric local o en la nube](service-fabric-cluster-creation-for-windows-server.md) y luego continúe con [Visualización del clúster mediante Service Fabric Explorer](service-fabric-visualizing-your-cluster.md).

<!---HONumber=AcomDC_0921_2016-->