---
title: Proxy inverso de Service Fabric | Microsoft Docs
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
ms.date: 01/04/2017
ms.author: bharatn
translationtype: Human Translation
ms.sourcegitcommit: c738b9d6461da032f216b8a51c69204066d5cfd3
ms.openlocfilehash: 9487209a8e5d976d56da50b8c70e69950d0ad129


---
# <a name="service-fabric-reverse-proxy"></a>Proxy inverso de Service Fabric
El proxy inverso de Service Fabric es un tejido de servicio integrado de proxy inverso con el que podemos comunicarnos con los microservicios del clúster de Service Fabric que expone puntos de conexión HTTP.

## <a name="microservices-communication-model"></a>Modelo de comunicación de microservicios
Normalmente, los microservicios de Service Fabric se ejecutan en un subconjunto de máquinas virtuales (VM) del clúster y pueden transferirse de una VM a otra por diversos motivos. Por tanto, los puntos de conexión de los microservicios pueden cambiar dinámicamente. El patrón típico para comunicarse con los microservicios es el bucle de resolución siguiente:

1. En primer lugar, resuelva la ubicación del servicio mediante el servicio de nombres.
2. Conéctese al servicio.
3. Determine la causa de los errores de conexión y vuelva a resolver la ubicación del servicio cuando proceda.

Para ello, normalmente hay que ajustar las bibliotecas de comunicación de cliente en un bucle de reintento que implemente la resolución del servicio y las directivas de reintento.
Para obtener más información sobre este tema, consulte [Conexión y comunicación con servicios en Service Fabric](service-fabric-connect-and-communicate-with-services.md).

### <a name="communicating-via-sf-reverse-proxy"></a>Comunicación a través del proxy inverso de Service Fabric
El proxy inverso de Service Fabric se ejecuta en todos los nodos del clúster. Este realiza todo el proceso de resolución del servicio en nombre de un cliente y, después, reenvía la solicitud de cliente. Por tanto, los clientes que se ejecutan en el clúster solo pueden usar las bibliotecas de comunicación HTTP de cliente para comunicarse con el servicio de destino a través del proxy inverso de Service Fabric que se ejecuta en el mismo nodo de manera local.

![Comunicación interna][1]

## <a name="reaching-microservices-from-outside-the-cluster"></a>Comunicación externa con los microservicios a través del clúster
La comunicación externa predeterminada de los microservicios se basa en un modelo **manual**; es decir, no se puede acceder directamente a los servicios desde los clientes externos. [Azure Load Balancer](../load-balancer/load-balancer-overview.md) actúa como límite de red entre los microservicios y los clientes externos. Además, realiza la traducción de direcciones de red y reenvía las solicitudes externas a los puntos de conexión **IP:port** internos. Para poder acceder directamente a un punto de conexión de un microservicio desde los clientes externos, primero debe configurar Azure Load Balancer para reenviar el tráfico a cada puerto que utilice el servicio del clúster. Además, la mayoría de los microservicios (sobre todo, los que tienen estado) no se encuentran en todos los nodos del clúster y pueden transferirse entre los nodos de conmutación por error. Por tanto, en estos casos, Azure Load Balancer no puede determinar de manera eficaz la ubicación del nodo de destino de las réplicas a las que reenviar el tráfico.

### <a name="reaching-microservices-via-the-sf-reverse-proxy-from-outside-the-cluster"></a>Comunicación externa con los microservicios a través del clúster mediante el proxy inverso de Service Fabric
En Azure Load Balancer solo puede configurarse el puerto del proxy inverso de Service Fabric. Gracias a esto, los clientes externos al clúster pueden comunicarse con los servicios internos de este a través del proxy inverso sin realizar más ajustes en la configuración.

![Comunicación externa][0]

> [!WARNING]
> Al configurar el puerto del proxy inverso del equilibrador de carga, se podrá acceder de manera externa a todos los microservicios del clúster que exponen un punto de conexión HTTP.
> 
> 

## <a name="uri-format-for-addressing-services-via-the-reverse-proxy"></a>Formato del URI para comunicarse con los servicios a través del proxy inverso
El proxy inverso utiliza un formato de URI específico para identificar a qué partición de servicio se debe reenviar la solicitud entrante:

```
http(s)://<Cluster FQDN | internal IP>:Port/<ServiceInstanceName>/<Suffix path>?PartitionKey=<key>&PartitionKind=<partitionkind>&Timeout=<timeout_in_seconds>
```

* **http(s)** : el proxy inverso puede configurarse para que acepte tráfico HTTP o HTTPS. En el caso del tráfico HTTPS, la terminación SSL se produce en el proxy inverso. Las solicitudes que reenvía el proxy inverso a los servicios del clúster se realizan por HTTP. **Tenga en cuenta que los servicios HTTPS no se admiten actualmente.**
* **Cluster FQDN | internal IP:** para clientes externos, se puede configurar el proxy inverso para que sea accesible a través del dominio del clúster (por ejemplo, micluster.eastus.cloudapp.azure.com). De forma predeterminada, el proxy inverso se ejecuta en cada nodo, por lo que para el tráfico interno se puede acceder en el host local o en cualquier dirección IP del nodo interno (por ejemplo, 10.0.0.1).
* **Port:** el puerto especificado para el proxy inverso. Por ejemplo: 19008.
* **ServiceInstanceName:** se trata del nombre completo de la instancia de servicio implementada con la que está tratando de comunicarse (sin el esquema "fabric:/"). Por ejemplo, para establecer conexión con el servicio *fabric:/miAplicación/miServicio/*, podría usar *miaplicación/miservicio*.
* **Suffix path** : se trata de la ruta de acceso URL real del servicio con el que quiere conectarse. Por ejemplo, *miAPI/values/add/3*
* **PartitionKey** : en un servicio particionado, corresponde a la clave de partición calculada de la partición con la que quiere conectarse. Tenga en cuenta que *no* se trata del identificador GUID de la partición. Este parámetro no es necesario para los servicios que utilizan el esquema de partición de singleton.
* **PartitionKind:** es el esquema de particiones singleton. El valor puede ser Int64Range o Con nombre. Este parámetro no es necesario para los servicios que utilizan el esquema de partición de singleton.
* **Timeout:** especifica el tiempo de espera de la solicitud HTTP que crea el proxy inverso para conectarse al servicio en nombre de la solicitud de cliente. El valor predeterminado es 60 segundos. Se trata de un parámetro opcional.

### <a name="example-usage"></a>Ejemplo de uso
A modo de ejemplo, veamos el servicio **fabric:/miAplicación/miServicio** que abre un agente de escucha HTTP en la siguiente dirección URL:

```
http://10.0.0.5:10592/3f0d39ad-924b-4233-b4a7-02617c6308a6-130834621071472715/
```

Y con estos recursos:

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
El servicio Puerta de enlace de aplicaciones trata de volver a resolver una dirección de servicio y de realizar la solicitud cuando no se puede establecer la comunicación con un servicio. Se trata de una de las principales ventajas de la puerta de enlace, ya que el código de cliente no tiene que implementar su propia resolución del servicio y bucle de resolución.

Generalmente, el motivo por el que no podemos comunicarnos con un servicio es porque su instancia o réplica se han movido a otro nodo como parte del ciclo de vida normal. Cuando esto sucede, es posible que la puerta de enlace reciba un error de conexión de red que indica que un punto de conexión ya no está abierto en la dirección resuelta originalmente.

Sin embargo, las instancias del servicio o las réplicas pueden compartir un proceso de host y un puerto cuando un servidor web basado en http.sys las hospeda; por ejemplo:

* [System.Net.HttpListener](https://msdn.microsoft.com/library/system.net.httplistener%28v=vs.110%29.aspx)
* [ASP.NET Core WebListener](https://docs.asp.net/latest/fundamentals/servers.html#weblistener)
* [Katana](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.OwinSelfHost/)

En este caso, es probable que el servidor web esté disponible en el proceso de host y que responda a las solicitudes, pero no la instancia del servicio resuelta o la réplica. Si esto ocurre, la puerta de enlace recibirá una respuesta HTTP 404 del servidor web. Este tipo de respuesta tiene dos lecturas diferentes:

1. La dirección de servicio es correcta, pero no existe el recurso que ha solicitado el usuario.
2. La dirección de servicio no es correcta y puede que el recurso que ha solicitado el usuario se encuentre realmente en otro nodo.

En el primer caso, se trata de una respuesta HTTP 404 normal: se considera un error de usuario. Sin embargo, en el segundo caso, el usuario ha solicitado un recurso que existe, pero la puerta de enlace no pudo encontrarlo porque se ha movido el servicio, en cuyo caso la puerta de enlace debe volver a resolver la dirección e intentarlo de nuevo.

Por tanto, la puerta de enlace necesita una manera de diferenciar estos dos casos. Para que pueda hacerlo, necesita una indicación del servidor.

* De forma predeterminada, Puerta de enlace de aplicaciones da por hecho que es el segundo caso y trata de volver a resolver y emitir la solicitud.
* Para que sepa que se trata del primero, el servicio debe devolver el siguiente encabezado de respuesta HTTP:

`X-ServiceFabric : ResourceNotFound`

Este encabezado de respuesta HTTP indica una situación de HTTP 404 normal; es decir, el recurso solicitado no existe y la puerta de enlace no trata de volver a resolver la dirección de servicio.

## <a name="setup-and-configuration"></a>Instalación y configuración
El proxy inverso de Service Fabric puede habilitarse en el clúster a través de la [plantilla de Azure Resource Manager](service-fabric-cluster-creation-via-arm.md).

Cuando disponga de la plantilla del clúster que quiere implementar (puede conseguirla de las plantillas de ejemplo o creando una plantilla de Resource Manager personalizada), podrá habilitar el proxy inverso en ella siguiendo estos pasos.

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
2. Especifique el puerto para cada uno de los objetos de NodeType en la **sección de tipos de recursos** [clúster](../azure-resource-manager/resource-group-authoring-templates.md)
   
    Para valores de apiVersion anteriores a '2016-09-01', el puerto se identifica mediante el nombre del parámetro ***httpApplicationGatewayEndpointPort***.
   
    ```json
    {
        "apiVersion": "2016-03-01",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
       "nodeTypes": [
          {
           ...
           "httpApplicationGatewayEndpointPort": "[parameters('SFReverseProxyPort')]",
           ...
          },
        ...
        ],
        ...
    }
    ```
   
    Para valores de apiVersion anteriores iguales o posteriores a '2016-09-01', el puerto se identifica mediante el nombre del parámetro ***reverseProxyEndpointPort***.
   
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
3. Para comunicarnos externamente con el proxy inverso a través del clúster de Azure, configure las **reglas de Azure Load Balancer** para el puerto especificado en el paso 1.
   
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
4. Para configurar certificados SSL en el puerto del proxy inverso, agregue el certificado a la propiedad httpApplicationGatewayCertificate de la **sección de tipos de recursos** [clúster](../azure-resource-manager/resource-group-authoring-templates.md)
   
    Para valores de apiVersion anteriores a '2016-09-01', el certificado se identifica mediante el nombre del parámetro ***httpApplicationGatewayCertificate***.
   
    ```json
    {
        "apiVersion": "2016-03-01",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        "dependsOn": [
            "[concat('Microsoft.Storage/storageAccounts/', parameters('supportLogStorageAccountName'))]"
        ],
        "properties": {
            ...
            "httpApplicationGatewayCertificate": {
                "thumbprint": "[parameters('sfReverseProxyCertificateThumbprint')]",
                "x509StoreName": "[parameters('sfReverseProxyCertificateStoreName')]"
            },
            ...
            "clusterState": "Default",
        }
    }
    ```
    Para valores de apiVersion iguales o posteriores a '2016-09-01', el certificado se identifica mediante el nombre del parámetro ***reverseProxyCertificate***.
   
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

## <a name="next-steps"></a>Pasos siguientes
* Vea un ejemplo de comunicación HTTP entre los servicios de un [proyecto de ejemplo en GitHub](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/tree/master/Services/WordCount).
* [Llamadas a procedimiento remoto con la comunicación remota de Reliable Services](service-fabric-reliable-services-communication-remoting.md)
* [API web que usa OWIN en Reliable Services](service-fabric-reliable-services-communication-webapi.md)
* [Comunicación WCF con la utilización de Reliable Services](service-fabric-reliable-services-communication-wcf.md)

[0]: ./media/service-fabric-reverseproxy/external-communication.png
[1]: ./media/service-fabric-reverseproxy/internal-communication.png



<!--HONumber=Jan17_HO1-->


