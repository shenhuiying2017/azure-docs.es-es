<properties
   pageTitle="Recursos de manifiesto de Service Fabric"
   description="Cómo se describen los recursos de un manifiesto de servicio"
   services="service-fabric"
   documentationCenter=".net"
   authors="sumukhs"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="04/23/2015"
   ms.author="sumukhs"/>

# Recursos de manifiesto de servicio

## Información general

El manifiesto de servicio permite que los recursos que utilizará el servicio sean declarados o modificados sin cambiar el código compilado. Service Fabric admite la configuración de recursos de extremo para el servicio. El acceso a los recursos especificados en el manifiesto de servicio puede controlarse a través de SecurityGroup en el manifiesto de aplicación. La declaración de recursos permite cambiar estos recursos durante la implementación en lugar de solicitar que el servicio introduzca un nuevo mecanismo de configuración.

## Extremos

Cuando se define un recurso de extremo en el manifiesto de servicio, Service Fabric asigna puertos desde el intervalo de puertos reservados de aplicación. Además, los servicios también pueden solicitar un puerto específico en un recurso. Es posible asignar números de puerto diferentes a réplicas de servicio que se ejecutan en nodos de clúster, mientras que las réplicas del mismo servicio que se ejecuta en el mismo nodo comparten el mismo puerto. Estos puertos pueden ser utilizados por las réplicas de servicio para varios propósitos, como la replicación, la escucha de solicitudes de cliente, etc.

```xml
<Resources>
  <Endpoints>
    <Endpoint Name="ServiceEndpoint" Protocol="http"/>
    <Endpoint Name="ServiceInputEndpoint" Protocol="http" Port="80"/>
    <Endpoint Name="ReplicatorEndpoint" Protocol="tcp"/>
  </Endpoints>
</Resources>
```

Consulte [Configurar servicios fiables con estado](../Service-Fabric/service-fabric-reliable-services-configuration.md) para obtener más información acerca de cómo hacer referencia a los extremos del archivo de configuración del paquete de configuración (settings.xml).

## Muestra
El siguiente manifiesto de servicio define 1 recurso de extremo TCP y 2 recursos de extremo HTTP en el elemento &lt;Recursos&gt;.

Service Fabric hace ACL automáticamente en los extremos HTTP.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Name="SP1" Version="V1" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Description>Test Service</Description>
  <ServiceTypes>
    <StatefulServiceType ServiceTypeName="PersistType" HasPersistedState="true" />
  </ServiceTypes>
  <CodePackage Name="CP1" Version="V1">
    <EntryPoint>
      <ExeHost>
        <Program>CB\Code.exe</Program>
      </ExeHost>
    </EntryPoint>
  </CodePackage>
  <ConfigPackage Name="CP1.Config0" Version="V1" />
  <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpoint" Protocol="http"/>
      <Endpoint Name="ServiceInputEndpoint" Protocol="http" Port="80"/>
      <Endpoint Name="ReplicatorEndpoint" Protocol="tcp"/>
    </Endpoints>
  </Resources>
</ServiceManifest>
```
 

<!---HONumber=August15_HO6-->