---
title: Protección de las comunicaciones remotas de servicios en Azure Service Fabric | Microsoft Docs
description: Aprenda a proteger las comunicaciones remotas basadas en servicios para servicios de confianza que se ejecutan en un clúster de Azure Service Fabric.
services: service-fabric
documentationcenter: .net
author: suchiagicha
manager: timlt
editor: vturecek
ms.assetid: fc129c1a-fbe4-4339-83ae-0e69a41654e0
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 04/20/2017
ms.author: suchiagicha
ms.openlocfilehash: cd7211ecda61ab2cca0f97e292d9ce2c47ed6933
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/16/2018
ms.locfileid: "34210280"
---
# <a name="secure-service-remoting-communications-for-a-service"></a>Proteger las comunicaciones remotas para un servicio
> [!div class="op_single_selector"]
> * [C# en Windows](service-fabric-reliable-services-secure-communication.md)
> * [Java en Linux](service-fabric-reliable-services-secure-communication-java.md)
>
>

La seguridad es uno de los aspectos más importantes de la comunicación. El marco de trabajo de la aplicación de Reliable Services proporciona una serie de pilas de comunicación creadas previamente y herramientas que puede utilizar para mejorar la seguridad. Este artículo trata sobre cómo mejorar la seguridad cuando se utiliza la comunicación remota de servicios.

Vamos a usar un [ejemplo](service-fabric-reliable-services-communication-remoting.md) existente que explica cómo configurar la comunicación remota para Reliable Services. Para ayudar a garantizar un servicio cuando usa la comunicación remota para los servicios, siga estos pasos:

1. Cree una interfaz, `IHelloWorldStateful`, que defina los métodos que estarán disponibles para la llamada a procedimiento remoto en su servicio. El servicio usará `FabricTransportServiceRemotingListener`, que se declara en el espacio de nombres `Microsoft.ServiceFabric.Services.Remoting.FabricTransport.Runtime`. Se trata de una implementación de `ICommunicationListener` que ofrece capacidades de comunicación remota.

    ```csharp
    public interface IHelloWorldStateful : IService
    {
        Task<string> GetHelloWorld();
    }

    internal class HelloWorldStateful : StatefulService, IHelloWorldStateful
    {
        protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
        {
            return new[]{
                    new ServiceReplicaListener(
                        (context) => new FabricTransportServiceRemotingListener(context,this))};
        }

        public Task<string> GetHelloWorld()
        {
            return Task.FromResult("Hello World!");
        }
    }
    ```
2. Agregue la configuración de escucha y las credenciales de seguridad.

    Asegúrese de que el certificado que desea utilizar para ayudar a proteger la comunicación de servicio esté instalado en todos los nodos del clúster. Hay dos formas de proporcionar la configuración de escucha y las credenciales de seguridad:

   1. Proporcionarlas directamente en el código de servicio:

       ```csharp
       protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
       {
           FabricTransportRemotingListenerSettings  listenerSettings = new FabricTransportRemotingListenerSettings
           {
               MaxMessageSize = 10000000,
               SecurityCredentials = GetSecurityCredentials()
           };
           return new[]
           {
               new ServiceReplicaListener(
                   (context) => new FabricTransportServiceRemotingListener(context,this,listenerSettings))
           };
       }

       private static SecurityCredentials GetSecurityCredentials()
       {
           // Provide certificate details.
           var x509Credentials = new X509Credentials
           {
               FindType = X509FindType.FindByThumbprint,
               FindValue = "4FEF3950642138446CC364A396E1E881DB76B48C",
               StoreLocation = StoreLocation.LocalMachine,
               StoreName = "My",
               ProtectionLevel = ProtectionLevel.EncryptAndSign
           };
           x509Credentials.RemoteCommonNames.Add("ServiceFabric-Test-Cert");
           x509Credentials.RemoteCertThumbprints.Add("9FEF3950642138446CC364A396E1E881DB76B483");
           return x509Credentials;
       }
       ```
   2. Proporcionarlas utilizando un [paquete de configuración](service-fabric-application-and-service-manifests.md):

       Agregue una sección `TransportSettings` en el archivo settings.xml.

       ```xml
       <Section Name="HelloWorldStatefulTransportSettings">
           <Parameter Name="MaxMessageSize" Value="10000000" />
           <Parameter Name="SecurityCredentialsType" Value="X509" />
           <Parameter Name="CertificateFindType" Value="FindByThumbprint" />
           <Parameter Name="CertificateFindValue" Value="4FEF3950642138446CC364A396E1E881DB76B48C" />
           <Parameter Name="CertificateRemoteThumbprints" Value="9FEF3950642138446CC364A396E1E881DB76B483" />
           <Parameter Name="CertificateStoreLocation" Value="LocalMachine" />
           <Parameter Name="CertificateStoreName" Value="My" />
           <Parameter Name="CertificateProtectionLevel" Value="EncryptAndSign" />
           <Parameter Name="CertificateRemoteCommonNames" Value="ServiceFabric-Test-Cert" />
       </Section>
       ```

       En este caso, el método `CreateServiceReplicaListeners` tendrá este aspecto.

       ```csharp
       protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
       {
           return new[]
           {
               new ServiceReplicaListener(
                   (context) => new FabricTransportServiceRemotingListener(
                       context,this,FabricTransportRemotingListenerSettings .LoadFrom("HelloWorldStatefulTransportSettings")))
           };
       }
       ```

        Si agrega una sección `TransportSettings` en el archivo settings.xml, `FabricTransportRemotingListenerSettings ` cargará toda la configuración de esta sección de forma predeterminada.

        ```xml
        <!--"TransportSettings" section .-->
        <Section Name="TransportSettings">
            ...
        </Section>
        ```
        En este caso, el método `CreateServiceReplicaListeners` tendrá este aspecto.

        ```csharp
        protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
        {
            return new[]
            {
                return new[]{
                        new ServiceReplicaListener(
                            (context) => new FabricTransportServiceRemotingListener(context,this))};
            };
        }
        ```
3. Al invocar métodos en un servicio protegido con una pila de comunicación remota, en lugar de utilizar la clase `Microsoft.ServiceFabric.Services.Remoting.Client.ServiceProxy` para crear un proxy de servicio, utilice `Microsoft.ServiceFabric.Services.Remoting.Client.ServiceProxyFactory`. Pase `FabricTransportRemotingSettings`, que contiene `SecurityCredentials`.

    ```csharp

    var x509Credentials = new X509Credentials
    {
        FindType = X509FindType.FindByThumbprint,
        FindValue = "9FEF3950642138446CC364A396E1E881DB76B483",
        StoreLocation = StoreLocation.LocalMachine,
        StoreName = "My",
        ProtectionLevel = ProtectionLevel.EncryptAndSign
    };
    x509Credentials.RemoteCommonNames.Add("ServiceFabric-Test-Cert");
    x509Credentials.RemoteCertThumbprints.Add("4FEF3950642138446CC364A396E1E881DB76B48C");

    FabricTransportRemotingSettings transportSettings = new FabricTransportRemotingSettings
    {
        SecurityCredentials = x509Credentials,
    };

    ServiceProxyFactory serviceProxyFactory = new ServiceProxyFactory(
        (c) => new FabricTransportServiceRemotingClientFactory(transportSettings));

    IHelloWorldStateful client = serviceProxyFactory.CreateServiceProxy<IHelloWorldStateful>(
        new Uri("fabric:/MyApplication/MyHelloWorldService"));

    string message = await client.GetHelloWorld();

    ```

    Si el código de cliente se ejecuta como parte de un servicio, puede cargar la clase `FabricTransportRemotingSettings` desde el archivo settings.xml. Cree una sección HelloWorldClientTransportSettings similar al código de servicio, tal y como se mostró anteriormente. Realice los siguientes cambios en el código de cliente:

    ```csharp
    ServiceProxyFactory serviceProxyFactory = new ServiceProxyFactory(
        (c) => new FabricTransportServiceRemotingClientFactory(FabricTransportRemotingSettings.LoadFrom("HelloWorldClientTransportSettings")));

    IHelloWorldStateful client = serviceProxyFactory.CreateServiceProxy<IHelloWorldStateful>(
        new Uri("fabric:/MyApplication/MyHelloWorldService"));

    string message = await client.GetHelloWorld();

    ```

    Si el cliente no se está ejecutando como parte de un servicio, puede crear un archivo client_name.settings.xml en la misma ubicación donde se encuentra client_name.exe. A continuación, cree una sección TransportSettings en ese archivo.

    De forma similar al servicio, si agrega una sección `TransportSettings` en el archivo settings.xml/client_name.settings.xml del cliente, `FabricTransportRemotingSettings` cargará toda la configuración desde esta sección de forma predeterminada.

    En ese caso, el código anterior se simplifica más aún:  

    ```csharp

    IHelloWorldStateful client = ServiceProxy.Create<IHelloWorldStateful>(
                 new Uri("fabric:/MyApplication/MyHelloWorldService"));

    string message = await client.GetHelloWorld();

    ```

A continuación, lea [Web API con OWIN en Reliable Services](service-fabric-reliable-services-communication-webapi.md).
