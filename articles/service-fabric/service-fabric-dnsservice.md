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
ms.date: 5/9/2017
ms.author: msfussell
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: e0f6a3a91207b73320d60a498d635262ef730d89
ms.contentlocale: es-es
ms.lasthandoff: 05/10/2017


---
# <a name="dns-service-in-azure-service-fabric"></a>Servicio DNS en Azure Service Fabric
El servicio DNS es un servicio de sistema opcional que se puede habilitar en el clúster para detectar otros servicios que usan el protocolo DNS.

Muchos servicios, sobre todo los servicios en contenedores, pueden tener un nombre de dirección URL existente, y tener la capacidad de resolverlo mediante el protocolo DNS estándar (en lugar del protocolo de servicio de nombres) es muy conveniente, en particular en escenarios "lift-and-shift" de la aplicación. El servicio DNS permite asignar nombres DNS a un nombre de servicio y, por tanto, resolver direcciones IP del punto de conexión. 

Como se muestra en el diagrama siguiente, el servicio DNS, que se ejecuta en el clúster de Service Fabric, asigna los nombres DNS a los nombres de servicio que, a continuación, se resuelven utilizando el Servicio de nombres para devolver las direcciones del punto de conexión a las que conectarse. El nombre DNS para el servicio se proporciona en el momento de la creación. 

![puntos de conexión de servicio][0]

## <a name="enabling-the-dns-service"></a>Habilitar el servicio DNS
Primero debe habilitar el servicio DNS en el clúster. Obtenga la plantilla del clúster que desea implementar. Puede usar las [plantillas de ejemplo](https://github.com/Azure/azure-quickstart-templates/tree/master/service-fabric-secure-cluster-5-node-1-nodetype) o crear una plantilla de Resource Manager. Puede habilitar el servicio DNS con los pasos siguientes:

1. En primer lugar, compruebe que `apiversion` está establecido en `2017-07-01-preview` para el recurso `Microsoft.ServiceFabric/clusters`, tal y como se muestra en el siguiente fragmento de código. Si es diferente, debe actualizar `apiVersion` al valor `2017-07-01-preview`.

    ```json
    {
        "apiVersion": "2017-07-01-preview",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
    }
    ```

2. Ahora, habilite el servicio DNS agregando la sección `addonFeatures` después de la sección `fabricSettings`, tal y como se muestra a continuación.

    ```json
        "fabricSettings": [
        ...      
        ],
        "addonFeatures": [
            "DnsService"
        ],
    ```

3. Una vez actualizada la plantilla del clúster con estos cambios, aplíquelos para completar la actualización. Una vez completada, ya podrá ver el servicio de sistema DNS en ejecución en el clúster, denominado `fabric:/System/DnsService`, en la sección de servicios del sistema en Service Fabric Explorer. 

## <a name="setting-the-dns-name-for-your-service"></a>Definición del nombre DNS para el servicio
Ahora que el servicio DNS se ejecuta en el clúster, puede establecer un nombre DNS para los servicios mediante declaración para los servicios predeterminados en `ApplicationManifest.xml` o a través de PowerShell.

### <a name="setting-the-dns-name-for-a-default-service-in-the-applicationmanifestxml"></a>Definición del nombre DNS de un servicio predeterminado en ApplicationManifest.xml
Abra el proyecto en Visual Studio, o su editor favorito, y abra el archivo `ApplicationManifest.xml`. Vaya a la sección de servicios predeterminados y, para cada servicio, agregue el atributo `ServiceDnsName`. En el ejemplo siguiente se muestra cómo establecer el nombre DNS del servicio en `service1.application1`.

```xml
    <Service Name="Stateless1" ServiceDnsName="service1.application1">
    <StatelessService ServiceTypeName="Stateless1Type" InstanceCount="[Stateless1_InstanceCount]">
        <SingletonPartition />
    </StatelessService>
    </Service>
```
Ahora implemente la aplicación. Una vez implementada la aplicación, vaya a la instancia de servicio en Service Fabric Explorer y puede ver el nombre DNS de esta instancia, tal y como se muestra a continuación. 

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
Si implementa más de un servicio, puede buscar los puntos de conexión de otros servicios con los que comunicarse mediante un nombre DNS. Esto solo aplica a los servicios sin estado, ya que el protocolo DNS no sabe con quién se comunican los servicios sin estado. Para los servicios con estado, puede usar el proxy inverso integrado para las llamadas HTTP para llamar a una partición de servicio determinada.

El código siguiente muestra cómo llamar a otro servicio, que es simplemente una llamada HTTP normal. Tenga en cuenta que tendrá que proporcionar el puerto y cualquier ruta de acceso opcional como parte de la dirección URL.

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

