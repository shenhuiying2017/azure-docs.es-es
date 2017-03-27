---
title: Proxy inverso de Azure Service Fabric | Microsoft Docs
description: "Utilice un proxy inverso de Service Fabric para comunicarse de manera interna y externa con los microservicios a través del clúster."
services: service-fabric
documentationcenter: .net
author: BharatNarasimman
manager: timlt
editor: vturecek
ms.assetid: 47f5c1c1-8fc8-4b80-a081-bc308f3655d3
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 02/23/2017
ms.author: bharatn
translationtype: Human Translation
ms.sourcegitcommit: c1cd1450d5921cf51f720017b746ff9498e85537
ms.openlocfilehash: a68855e0b6ba436849c4de13f1439f0e70009b6a
ms.lasthandoff: 03/14/2017


---
# <a name="reverse-proxy-in-azure-service-fabric"></a>Proxy inverso en Azure Service Fabric
El proxy inverso que está integrado en Azure Service Fabric dirige los microservicios del clúster de Service Fabric que expone los puntos de conexión HTTP.

## <a name="microservices-communication-model"></a>Modelo de comunicación de microservicios
Normalmente, los microservicios de Service Fabric se ejecutan en un subconjunto de máquinas virtuales del clúster y pueden transferirse de una máquina virtual a otra por diversos motivos. Por tanto, los puntos de conexión de los microservicios pueden cambiar dinámicamente. El patrón típico para comunicarse con los microservicios es el bucle de resolución siguiente:

1. En primer lugar, resuelva la ubicación del servicio mediante el servicio de nombres.
2. Conéctese al servicio.
3. Determine la causa de los errores de conexión y vuelva a resolver la ubicación del servicio cuando proceda.

Para ello, normalmente hay que ajustar las bibliotecas de comunicación de cliente en un bucle de reintento que implemente la resolución del servicio y las directivas de reintento.
Para más información, consulte [Conexión y comunicación con los servicios](service-fabric-connect-and-communicate-with-services.md).

### <a name="communicating-by-using-the-reverse-proxy"></a>Comunicación mediante el proxy inverso
El proxy inverso de Service Fabric se ejecuta en todos los nodos del clúster. Este realiza todo el proceso de resolución del servicio en nombre de un cliente y, después, reenvía la solicitud de cliente. Por tanto, los clientes que se ejecutan en el clúster pueden usar todas las bibliotecas de comunicación HTTP de cliente para comunicarse con el servicio de destino a través del proxy inverso que se ejecuta en el mismo nodo de manera local.

![Comunicación interna][1]

## <a name="reaching-microservices-from-outside-the-cluster"></a>Comunicación externa con los microservicios a través del clúster
La comunicación externa predeterminada de los microservicios se basa en un modelo manual; es decir, no se puede acceder directamente a los servicios desde los clientes externos. [Azure Load Balancer](../load-balancer/load-balancer-overview.md) actúa como límite de red entre los microservicios y los clientes externos. Además, realiza la traducción de direcciones de red y reenvía las solicitudes externas a los puntos de conexión IP:port internos. Para hacer que los clientes externos puedan acceder directamente al punto de conexión de un microservicio, primero debe configurar Load Balancer para que reenvíe el tráfico a cada puerto que utilice el servicio del clúster. Además, la mayoría de los microservicios, especialmente los microservicios con estado, no se encuentran en todos los nodos del clúster. Los microservicios se pueden mover entre nodos durante la conmutación por error. En tales casos, el equilibrador de carga no puede determinar eficazmente la ubicación del nodo de destino de las réplicas al que debe reenviar el tráfico.

### <a name="reaching-microservices-via-the-reverse-proxy-from-outside-the-cluster"></a>Comunicación externa con los microservicios a través del clúster mediante el proxy inverso
En lugar de configurar el puerto de un servicio individual de Load Balancer, puede configurar solo el puerto del proxy inverso de Load Balancer. Esta configuración permite a los clientes externos al clúster comunicarse con los servicios internos de este a través del proxy inverso sin realizar más ajustes en la configuración.

![Comunicación externa][0]

> [!WARNING]
> Al configurar el puerto del proxy inverso de Load Balancer, se podrá acceder de manera externa a todos los microservicios del clúster que exponen un punto de conexión HTTP.
>
>


## <a name="uri-format-for-addressing-services-by-using-the-reverse-proxy"></a>Formato del URI para comunicarse con los servicios mediante el proxy inverso
El proxy inverso utiliza un formato de identificador uniforme de recursos (URI) específico para identificar a qué partición de servicio se debe reenviar la solicitud entrante:

```
http(s)://<Cluster FQDN | internal IP>:Port/<ServiceInstanceName>/<Suffix path>?PartitionKey=<key>&PartitionKind=<partitionkind>&ListenerName=<listenerName>&TargetReplicaSelector=<targetReplicaSelector>&Timeout=<timeout_in_seconds>
```

* **http(s)** : el proxy inverso puede configurarse para que acepte tráfico HTTP o HTTPS. En el caso del tráfico HTTPS, la terminación de la capa de sockets seguros (SSL) se produce en el proxy inverso. El proxy inverso utiliza HTTP para reenviar las solicitudes a los servicios del clúster.

    Tenga en cuenta que los servicios HTTPS no se admiten actualmente.
* **Cluster fully qualified domain name (FQDN) | internal IP:** para clientes externos, se puede configurar el proxy inverso para que sea accesible a través del dominio del clúster (por ejemplo, micluster.eastus.cloudapp.azure.com). De forma predeterminada, el proxy inverso se ejecuta en todos los nodos. Para el tráfico interno se puede acceder al proxy inverso en el host local o en cualquier dirección IP del nodo interno (por ejemplo, 10.0.0.1).
* **Port:** el puerto especificado para el proxy inverso. Por ejemplo, el puerto 19008.
* **ServiceInstanceName:** se trata del nombre completo de la instancia de servicio implementada con la que está tratando de comunicarse (sin el esquema "fabric:/"). Por ejemplo, para establecer conexión con el servicio *fabric:/miAplicación/miServicio/*, podría usar *miaplicación/miservicio*.
* **Suffix path**: se trata de la ruta de acceso URL real del servicio con el que quiere conectarse. Por ejemplo, *myapi/values/add/3*.
* **PartitionKey**: en un servicio particionado, corresponde a la clave de partición calculada de la partición con la que quiere conectarse. Tenga en cuenta que *no* se trata del identificador GUID de la partición. Este parámetro no es necesario para los servicios que utilizan el esquema de partición de singleton.
* **PartitionKind:** es el esquema de particiones singleton. El valor puede ser Int64Range o Con nombre. Este parámetro no es necesario para los servicios que utilizan el esquema de partición de singleton.
* **ListenerName:** los puntos de conexión del servicio presentan la forma {"Endpoints":{"Listener1":"Endpoint1","Listener2":"Endpoint2" ...}}. Cuando el servicio expone varios puntos de conexión, esto identifica a qué puntos de conexión se debe reenviar la solicitud del cliente. Esto se puede omitir si el servicio tiene solo un agente de escucha.
* **TargetReplicaSelector:** especifica cómo se debe seleccionar la instancia o la réplica de destino.
  * Cuando el servicio de destino tiene estado, el valor de TargetReplicaSelector puede ser uno de los siguientes: "PrimaryReplica", "RandomSecondaryReplica" o "RandomReplica". El valor predeterminado cuando no se especifica este parámetro es "PrimaryReplica".
  * Cuando el servicio de destino no tiene estado, el proxy inverso elige una instancia aleatoria de la partición de servicio a la que reenviar la solicitud.
* **Timeout:** especifica el tiempo de espera de la solicitud HTTP que crea el proxy inverso para conectarse al servicio en nombre de la solicitud de cliente. El valor predeterminado es 60 segundos. Se trata de un parámetro opcional.

### <a name="example-usage"></a>Ejemplo de uso
A modo de ejemplo, veamos el servicio *fabric:/miAplicación/miServicio* que abre un agente de escucha HTTP en la siguiente dirección URL:

```
http://10.0.0.5:10592/3f0d39ad-924b-4233-b4a7-02617c6308a6-130834621071472715/
```

Los recursos para el servicio son los siguientes:

* `/index.html`
* `/api/users/<userId>`

Si el servicio utiliza el esquema de particiones de singleton, no se requieren los parámetros de cadena de consulta *PartitionKey* y *PartitionKind*, y se podrá acceder a él mediante la puerta de enlace de las siguientes formas:

* Externamente: `http://mycluster.eastus.cloudapp.azure.com:19008/MyApp/MyService`
* Internamente: `http://localhost:19008/MyApp/MyService`

Si el servicio usa el esquema de particiones Int64 uniforme, deben utilizarse los parámetros de cadena de consulta *PartitionKey* y *PartitionKind* para comunicarse con una partición del servicio:

* Externamente: `http://mycluster.eastus.cloudapp.azure.com:19008/MyApp/MyService?PartitionKey=3&PartitionKind=Int64Range`
* Internamente: `http://localhost:19008/MyApp/MyService?PartitionKey=3&PartitionKind=Int64Range`

Para acceder a los recursos que expone el servicio, basta con colocar su ruta de acceso después del nombre del servicio en la URL:

* Externamente: `http://mycluster.eastus.cloudapp.azure.com:19008/MyApp/MyService/index.html?PartitionKey=3&PartitionKind=Int64Range`
* Internamente: `http://localhost:19008/MyApp/MyService/api/users/6?PartitionKey=3&PartitionKind=Int64Range`

Después, la puerta de enlace reenviará estas solicitudes a la URL del servicio:

* `http://10.0.0.5:10592/3f0d39ad-924b-4233-b4a7-02617c6308a6-130834621071472715/index.html`
* `http://10.0.0.5:10592/3f0d39ad-924b-4233-b4a7-02617c6308a6-130834621071472715/api/users/6`

## <a name="special-handling-for-port-sharing-services"></a>Control especial de los servicios de uso compartido de puertos
Azure Application Gateway trata de volver a resolver una dirección de servicio y de realizar la solicitud cuando no se puede establecer la comunicación con un servicio. Se trata de una de las principales ventajas de Application Gateway, ya que el código de cliente no tiene que implementar su propia resolución del servicio ni el bucle de resolución.

Generalmente, el motivo por el que no podemos comunicarnos con un servicio es porque su instancia o réplica se han movido a otro nodo como parte del ciclo de vida normal. Cuando esto sucede, es posible que Application Gateway reciba un error de conexión de red que indica que un punto de conexión ya no está abierto en la dirección resuelta originalmente.

Sin embargo, las instancias del servicio o las réplicas pueden compartir un proceso de host y un puerto cuando un servidor web basado en http.sys las hospeda; por ejemplo:

* [System.Net.HttpListener](https://msdn.microsoft.com/library/system.net.httplistener%28v=vs.110%29.aspx)
* [ASP.NET Core WebListener](https://docs.asp.net/latest/fundamentals/servers.html#weblistener)
* [Katana](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.OwinSelfHost/)

En este caso, es probable que el servidor web esté disponible en el proceso de host y que responda a las solicitudes, pero no la instancia del servicio resuelta o la réplica. Si esto ocurre, la puerta de enlace recibirá una respuesta HTTP 404 del servidor web. Este tipo de respuesta tiene dos lecturas diferentes:

- Caso 1: la dirección de servicio es correcta, pero no existe el recurso que ha solicitado el usuario.
- Caso 2: la dirección de servicio es incorrecta y puede que el recurso que ha solicitado el usuario exista en un nodo diferente.

En el primer caso, se trata de una respuesta HTTP 404 normal que se considera un error de usuario. Sin embargo, en el segundo caso, el usuario ha solicitado un recurso que ya existe. Application Gateway no pudo encontrarlo porque se había movido el servicio. Application Gateway debe resolver la dirección de nuevo y volver a intentar la solicitud.

Por tanto, Application Gateway necesita una manera de diferenciar estos dos casos. Para que pueda hacerlo, necesita una indicación del servidor.

* De forma predeterminada, Application Gateway da por hecho que es el segundo caso y trata de volver a resolver y emitir la solicitud.
* Para que sepa que se trata del primer caso, el servicio debe devolver el siguiente encabezado de respuesta HTTP:

  `X-ServiceFabric : ResourceNotFound`

Este encabezado de respuesta HTTP indica una situación de HTTP 404 normal; es decir, el recurso solicitado no existe y Application Gateway no trata de volver a resolver la dirección de servicio.

## <a name="setup-and-configuration"></a>Instalación y configuración
El proxy inverso de Service Fabric puede habilitarse en el clúster a través de la [plantilla de Azure Resource Manager](service-fabric-cluster-creation-via-arm.md).

Consulte [Configure HTTPS Reverse Proxy in a secure cluster](https://github.com/ChackDan/Service-Fabric/tree/master/ARM Templates/ReverseProxySecureSample#configure-https-reverse-proxy-in-a-secure-cluster) (Configuración del proxy inverso HTTPS en un clúster seguro) para ver ejemplos de plantillas de Azure Resource Manager para configurar un proxy inverso seguro con un certificado y administrar la sustitución de certificados.

En primer lugar, se obtiene la plantilla del clúster que desea implementar. Puede usar las plantillas de ejemplo o crear una plantilla personalizada de Resource Manager. A continuación, puede habilitar el proxy inverso mediante los pasos siguientes:

1. Defina un puerto para el proxy inverso en la [sección de parámetros](../azure-resource-manager/resource-group-authoring-templates.md) de la plantilla.

    ```json
    "SFReverseProxyPort": {
        "type": "int",
        "defaultValue": 19008,
        "metadata": {
            "description": "Endpoint for Service Fabric Reverse proxy"
        }
    },
    ```
2. Especifique el puerto para cada uno de los objetos de NodeType en la **sección de tipos de recursos** [clúster](../azure-resource-manager/resource-group-authoring-templates.md).

    El puerto se identifica con el nombre de parámetro reverseProxyEndpointPort.

    ```json
    {
        "apiVersion": "2016-09-01",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
       "nodeTypes": [
          {
           ...
           "reverseProxyEndpointPort": "[parameters('SFReverseProxyPort')]",
           ...
          },
        ...
        ],
        ...
    }
    ```
3. Para comunicarnos externamente con el proxy inverso a través del clúster de Azure, configure las reglas de Azure Load Balancer para el puerto especificado en el paso 1.

    ```json
    {
        "apiVersion": "[variables('lbApiVersion')]",
        "type": "Microsoft.Network/loadBalancers",
        ...
        ...
        "loadBalancingRules": [
            ...
            {
                "name": "LBSFReverseProxyRule",
                "properties": {
                    "backendAddressPool": {
                        "id": "[variables('lbPoolID0')]"
                    },
                    "backendPort": "[parameters('SFReverseProxyPort')]",
                    "enableFloatingIP": "false",
                    "frontendIPConfiguration": {
                        "id": "[variables('lbIPConfig0')]"
                    },
                    "frontendPort": "[parameters('SFReverseProxyPort')]",
                    "idleTimeoutInMinutes": "5",
                    "probe": {
                        "id": "[concat(variables('lbID0'),'/probes/SFReverseProxyProbe')]"
                    },
                    "protocol": "tcp"
                }
            }
        ],
        "probes": [
            ...
            {
                "name": "SFReverseProxyProbe",
                "properties": {
                    "intervalInSeconds": 5,
                    "numberOfProbes": 2,
                    "port":     "[parameters('SFReverseProxyPort')]",
                    "protocol": "tcp"
                }
            }  
        ]
    }
    ```
4. Para configurar certificados SSL en el puerto del proxy inverso, agregue el certificado a la propiedad ***reverseProxyCertificate*** del **Clúster**, en la [sección del tipo de recurso](../resource-group-authoring-templates.md).

    ```json
    {
        "apiVersion": "2016-09-01",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        "dependsOn": [
            "[concat('Microsoft.Storage/storageAccounts/', parameters('supportLogStorageAccountName'))]"
        ],
        "properties": {
            ...
            "reverseProxyCertificate": {
                "thumbprint": "[parameters('sfReverseProxyCertificateThumbprint')]",
                "x509StoreName": "[parameters('sfReverseProxyCertificateStoreName')]"
            },
            ...
            "clusterState": "Default",
        }
    }
    ```

### <a name="supporting-a-reverse-proxy-certificate-thats-different-from-the-cluster-certificate"></a>Compatibilidad de un certificado de proxy inverso distinto del certificado de clúster
 Si el certificado de proxy inverso es distinto del certificado que protege el clúster, el certificado especificado anteriormente debe instalarse en la máquina virtual y agregarse a la lista de control de acceso (ACL) para que Service Fabric pueda acceder a él. Esto puede hacerse a través de **virtualMachineScaleSets**, en la [sección del tipo de recurso](../resource-group-authoring-templates.md). Para la instalación, agregue ese certificado a osProfile. La sección de extensión de la plantilla puede actualizar el certificado en la ACL.

  ```json
  {
    "apiVersion": "[variables('vmssApiVersion')]",
    "type": "Microsoft.Compute/virtualMachineScaleSets",
    ....
      "osProfile": {
          "adminPassword": "[parameters('adminPassword')]",
          "adminUsername": "[parameters('adminUsername')]",
          "computernamePrefix": "[parameters('vmNodeType0Name')]",
          "secrets": [
            {
              "sourceVault": {
                "id": "[parameters('sfReverseProxySourceVaultValue')]"
              },
              "vaultCertificates": [
                {
                  "certificateStore": "[parameters('sfReverseProxyCertificateStoreValue')]",
                  "certificateUrl": "[parameters('sfReverseProxyCertificateUrlValue')]"
                }
              ]
            }
          ]
        }
   ....
   "extensions": [
          {
              "name": "[concat(parameters('vmNodeType0Name'),'_ServiceFabricNode')]",
              "properties": {
                      "type": "ServiceFabricNode",
                      "autoUpgradeMinorVersion": false,
                      ...
                      "publisher": "Microsoft.Azure.ServiceFabric",
                      "settings": {
                        "clusterEndpoint": "[reference(parameters('clusterName')).clusterEndpoint]",
                        "nodeTypeRef": "[parameters('vmNodeType0Name')]",
                        "dataPath": "D:\\\\SvcFab",
                        "durabilityLevel": "Bronze",
                        "testExtension": true,
                        "reverseProxyCertificate": {
                          "thumbprint": "[parameters('sfReverseProxyCertificateThumbprint')]",
                          "x509StoreName": "[parameters('sfReverseProxyCertificateStoreValue')]"
                        },
                  },
                  "typeHandlerVersion": "1.0"
              }
          },
      ]
    }
  ```
> [!NOTE]
> Cuando use certificados que sean distintos al certificado del clúster para habilitar el proxy inverso en un clúster existente, instale el certificado del proxy inverso y actualice la ACL en el clúster antes de habilitar el proxy inverso. Complete la implementación de la [plantilla de Azure Resource Manager](service-fabric-cluster-creation-via-arm.md) con la configuración mencionada anteriormente antes de iniciar una implementación para habilitar el proxy inverso con los pasos de 1 a 4.

## <a name="next-steps"></a>Pasos siguientes
* Vea un ejemplo de comunicación HTTP entre los servicios de un [proyecto de ejemplo en GitHub](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/tree/master/Services/WordCount).
* [Llamadas a procedimiento remoto con la comunicación remota de Reliable Services](service-fabric-reliable-services-communication-remoting.md)
* [API web que usa OWIN en Reliable Services](service-fabric-reliable-services-communication-webapi.md)
* [Comunicación WCF con la utilización de Reliable Services](service-fabric-reliable-services-communication-wcf.md)

[0]: ./media/service-fabric-reverseproxy/external-communication.png
[1]: ./media/service-fabric-reverseproxy/internal-communication.png

