---
title: Servicio DNS de Azure Service Fabric | Microsoft Docs
description: "Use el servicio DNS de Service Fabric para detectar microservicios desde dentro del clúster."
services: service-fabric
documentationcenter: .net
author: msfussell
manager: timlt
editor: vturecek
ms.assetid: 47f5c1c1-8fc8-4b80-a081-bc308f3655d3
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 7/27/2017
ms.author: msfussell
ms.openlocfilehash: 9871bc5aa4e74ab0faef401d67c4e9558eb5e14b
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/18/2017
---
# <a name="dns-service-in-azure-service-fabric"></a>Servicio DNS en Azure Service Fabric
El servicio DNS es un servicio de sistema opcional que se puede habilitar en el clúster para detectar otros servicios que usan el protocolo DNS.

Muchos servicios, sobre todo los servicios en contenedores, pueden tener un nombre de dirección URL existente. El poder resolverlos mediante el protocolo DNS estándar (en lugar del protocolo Servicio de nombres) es deseable, en particular en escenarios "lift-and-shift". El servicio DNS permite asignar nombres DNS a un nombre de servicio y, por tanto, resolver direcciones IP del punto de conexión. 

El servicio DNS asigna nombres DNS a nombres de servicio que el protocolo Servicio de nombres resuelve para devolver el punto de conexión del servicio. El nombre DNS para el servicio se proporciona en el momento de la creación. 

![puntos de conexión de servicio][0]

## <a name="enabling-the-dns-service"></a>Habilitar el servicio DNS
Primero debe habilitar el servicio DNS en el clúster. Obtenga la plantilla del clúster que desea implementar. Puede usar las [plantillas de ejemplo](https://github.com/Azure/azure-quickstart-templates/tree/master/service-fabric-secure-cluster-5-node-1-nodetype) o crear una plantilla de Resource Manager. Puede habilitar el servicio DNS con los pasos siguientes:

1. Compruebe que `apiversion` está establecido en `2017-07-01-preview` para el recurso `Microsoft.ServiceFabric/clusters` y, si no es así, actualícelo como se muestra en el siguiente fragmento de código:

    ```json
    {
        "apiVersion": "2017-07-01-preview",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
    }
    ```

2. Ahora, habilite el servicio DNS al agregar la siguiente sección `addonFeatures` después de la sección `fabricSettings`, como se muestra en el siguiente fragmento de código: 

    ```json
        "fabricSettings": [
        ...      
        ],
        "addonFeatures": [
            "DnsService"
        ],
    ```

3. Una vez actualizada la plantilla del clúster con los cambios anteriores, aplíquelos y deje que se complete la actualización. Una vez completada, el servicio de sistema DNS comienza a ejecutarse en el clúster denominado `fabric:/System/DnsService` en la sección de servicios del sistema de Service Fabric Explorer. 

También puede habilitar el servicio DNS a través del portal en el momento de la creación del clúster. El servicio DNS puede habilitarse si se activa la casilla `Include DNS service` en el menú `Cluster configuration`, como se muestra en la captura de pantalla siguiente:

![Habilitación del servicio DNS a través del portal][2]


## <a name="setting-the-dns-name-for-your-service"></a>Definición del nombre DNS para el servicio
Una vez que el servicio DNS se esté ejecutando en el clúster, puede establecer un nombre DNS para los servicios ya sea mediante declaración para los servicios predeterminados en `ApplicationManifest.xml` o a través de comandos de PowerShell.

### <a name="setting-the-dns-name-for-a-default-service-in-the-applicationmanifestxml"></a>Definición del nombre DNS de un servicio predeterminado en ApplicationManifest.xml
Abra el proyecto en Visual Studio, o su editor favorito, y abra el archivo `ApplicationManifest.xml`. Vaya a la sección de servicios predeterminados y, para cada servicio, agregue el atributo `ServiceDnsName`. En el ejemplo siguiente se muestra cómo establecer el nombre DNS del servicio en `service1.application1`.

```xml
    <Service Name="Stateless1" ServiceDnsName="service1.application1">
    <StatelessService ServiceTypeName="Stateless1Type" InstanceCount="[Stateless1_InstanceCount]">
        <SingletonPartition />
    </StatelessService>
    </Service>
```
Una vez implementada la aplicación, la instancia de servicio de Service Fabric Explorer muestra el nombre DNS de esta instancia, como se muestra en la siguiente imagen: 

![puntos de conexión de servicio][1]

### <a name="setting-the-dns-name-for-a-service-using-powershell"></a>Definición del nombre DNS de un servicio mediante PowerShell
Puede establecer el nombre DNS de un servicio cuando se crea mediante el PowerShell `New-ServiceFabricService`. En el ejemplo siguiente se crea un servicio sin estado nuevo con el nombre DNS `service1.application1`.

```powershell
    New-ServiceFabricService `
    -Stateless `
    -PartitionSchemeSingleton `
    -ApplicationName `fabric:/application1 `
    -ServiceName fabric:/application1/service1 `
    -ServiceTypeName Service1Type `
    -InstanceCount 1 `
    -ServiceDnsName service1.application1
```

## <a name="using-dns-in-your-services"></a>Uso de DNS en los servicios
Si implementa más de un servicio, puede buscar los puntos de conexión de otros servicios con los que comunicarse mediante un nombre DNS. El servicio DNS solo se aplica a los servicios sin estado, ya que el protocolo DNS no puede comunicarse con servicios con estado. En el caso de los servicios con estado, puede usar el proxy inverso integrado para llamadas HTTP para llamar a una partición de servicio determinada.

El código siguiente muestra cómo llamar a otro servicio, lo que constituye una llamada http normal donde se proporcionan el puerto y cualquier ruta de acceso opcional como parte de la dirección URL.

```csharp
public class ValuesController : Controller
{
    // GET api
    [HttpGet]
    public async Task<string> Get()
    {
        string result = "";
        try
        {
            Uri uri = new Uri("http://service1.application1:8080/api/values");
            HttpClient client = new HttpClient();
            var response = await client.GetAsync(uri);
            result = await response.Content.ReadAsStringAsync();
            
        }
        catch (Exception e)
        {
            Console.Write(e.Message);
        }

        return result;
    }
}
```

## <a name="next-steps"></a>Pasos siguientes
Para más información sobre la comunicación de servicios dentro del clúster, vea [Conexión y comunicación con servicios](service-fabric-connect-and-communicate-with-services.md).

[0]: ./media/service-fabric-connect-and-communicate-with-services/dns.png
[1]: ./media/service-fabric-dnsservice/servicefabric-explorer-dns.PNG
[2]: ./media/service-fabric-dnsservice/DNSService.PNG
