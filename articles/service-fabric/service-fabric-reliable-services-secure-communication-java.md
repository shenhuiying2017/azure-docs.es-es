---
title: "Ayuda para garantizar la comunicación de los servicios de Azure Service Fabric | Microsoft Docs"
description: "Información general sobre cómo ayudar a proteger la comunicación de los servicios de confianza que se ejecutan en el clúster de Azure Service Fabric."
services: service-fabric
documentationcenter: java
author: PavanKunapareddyMSFT
manager: timlt
ms.assetid: 
ms.service: service-fabric
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 06/30/2017
ms.author: pakunapa
ms.openlocfilehash: 5e2f36b3de1dd04c1a3f36ae308af164d10654ea
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/21/2017
---
# <a name="help-secure-communication-for-services-in-azure-service-fabric"></a>Ayuda para garantizar la comunicación de los servicios de Azure Service Fabric
> [!div class="op_single_selector"]
> * [C# en Windows](service-fabric-reliable-services-secure-communication.md)
> * [Java en Linux](service-fabric-reliable-services-secure-communication-java.md)
>
>

## <a name="help-secure-a-service-when-youre-using-service-remoting"></a>Ayuda para garantizar un servicio cuando usa la comunicación remota para los servicios
Vamos a usar un [ejemplo](service-fabric-reliable-services-communication-remoting-java.md) existente que explica cómo configurar la comunicación remota para Reliable Services Para ayudar a garantizar un servicio cuando usa la comunicación remota para los servicios, siga estos pasos:

1. Cree una interfaz, `HelloWorldStateless`, que defina los métodos que estarán disponibles para la llamada a procedimiento remoto en su servicio. El servicio usará `FabricTransportServiceRemotingListener`, que se declara en el paquete `microsoft.serviceFabric.services.remoting.fabricTransport.runtime`. Se trata de una implementación de `CommunicationListener` que ofrece capacidades de comunicación remota.

    ```java
    public interface HelloWorldStateless extends Service {
        CompletableFuture<String> getHelloWorld();
    }

    class HelloWorldStatelessImpl extends StatelessService implements HelloWorldStateless {
        @Override
        protected List<ServiceInstanceListener> createServiceInstanceListeners() {
            ArrayList<ServiceInstanceListener> listeners = new ArrayList<>();
            listeners.add(new ServiceInstanceListener((context) -> {
                return new FabricTransportServiceRemotingListener(context,this);
            }));
        return listeners;
        }

        public CompletableFuture<String> getHelloWorld() {
            return CompletableFuture.completedFuture("Hello World!");
        }
    }
    ```
2. Agregue la configuración de escucha y las credenciales de seguridad.

    Asegúrese de que el certificado que desea utilizar para ayudar a proteger la comunicación de servicio esté instalado en todos los nodos del clúster. Hay dos formas de proporcionar la configuración de escucha y las credenciales de seguridad:

   1. Proporcionarlas utilizando un [paquete de configuración](service-fabric-application-and-service-manifests.md):

       Agregue una sección `TransportSettings` en el archivo settings.xml.

       ```xml
       <!--Section name should always end with "TransportSettings".-->
       <!--Here we are using a prefix "HelloWorldStateless".-->
        <Section Name="HelloWorldStatelessTransportSettings">
            <Parameter Name="MaxMessageSize" Value="10000000" />
            <Parameter Name="SecurityCredentialsType" Value="X509_2" />
            <Parameter Name="CertificatePath" Value="/path/to/cert/BD1C71E248B8C6834C151174DECDBDC02DE1D954.crt" />
            <Parameter Name="CertificateProtectionLevel" Value="EncryptandSign" />
            <Parameter Name="CertificateRemoteThumbprints" Value="BD1C71E248B8C6834C151174DECDBDC02DE1D954" />
        </Section>

       ```

       En este caso, el método `createServiceInstanceListeners` tendrá este aspecto.

       ```java
        protected List<ServiceInstanceListener> createServiceInstanceListeners() {
            ArrayList<ServiceInstanceListener> listeners = new ArrayList<>();
            listeners.add(new ServiceInstanceListener((context) -> {
                return new FabricTransportServiceRemotingListener(context,this, FabricTransportRemotingListenerSettings.loadFrom(HelloWorldStatelessTransportSettings));
            }));
            return listeners;
        }
       ```

        Si agrega una sección `TransportSettings` en el archivo settings.xml sin ningún prefijo, `FabricTransportListenerSettings` cargará toda la configuración de esta sección de forma predeterminada.

        ```xml
        <!--"TransportSettings" section without any prefix.-->
        <Section Name="TransportSettings">
            ...
        </Section>
        ```
        En este caso, el método `CreateServiceInstanceListeners` tendrá este aspecto.

        ```java
        protected List<ServiceInstanceListener> createServiceInstanceListeners() {
            ArrayList<ServiceInstanceListener> listeners = new ArrayList<>();
            listeners.add(new ServiceInstanceListener((context) -> {
                return new FabricTransportServiceRemotingListener(context,this);
            }));
            return listeners;
        }
       ```
3. Al invocar métodos en un servicio protegido con una pila de comunicación remota, en lugar de utilizar la clase `microsoft.serviceFabric.services.remoting.client.ServiceProxyBase` para crear un proxy de servicio, utilice `microsoft.serviceFabric.services.remoting.client.FabricServiceProxyFactory`.

    Si el código de cliente se ejecuta como parte de un servicio, puede cargar la clase `FabricTransportSettings` desde el archivo settings.xml. Cree una sección TransportSettings similar al código de servicio, tal y como se mostró anteriormente. Realice los siguientes cambios en el código de cliente:

    ```java

    FabricServiceProxyFactory serviceProxyFactory = new FabricServiceProxyFactory(c -> {
            return new FabricTransportServiceRemotingClientFactory(FabricTransportRemotingSettings.loadFrom("TransportPrefixTransportSettings"), null, null, null, null);
        }, null)

    HelloWorldStateless client = serviceProxyFactory.createServiceProxy(HelloWorldStateless.class,
        new URI("fabric:/MyApplication/MyHelloWorldService"));

    CompletableFuture<String> message = client.getHelloWorld();

    ```
