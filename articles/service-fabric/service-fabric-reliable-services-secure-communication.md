<properties
   pageTitle="Protección de la comunicación de servicios de Service Fabric | Microsoft Azure"
   description="Información general sobre cómo proteger la comunicación de los servicios de confianza que se ejecutan en el clúster de Service Fabric."
   services="service-fabric"
   documentationCenter=".net"
   authors="punewa"
   manager="timlt"
   editor="vturecek"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="required"
   ms.date="03/22/2016"
   ms.author="punewa"/>

# Comunicación segura de los servicios de Service Fabric
La seguridad es uno de los aspectos más importantes de la comunicación. El marco de trabajo de la aplicación de Reliable Services proporciona una serie de pilas de comunicación creadas previamente y herramientas que puede utilizar. En este artículo hablaremos sobre cómo configurar la seguridad cuando se utiliza la comunicación remota de servicio y la pila de comunicación de WCF.

## Protección del servicio cuando se utiliza la comunicación remota de servicio
La configuración del acceso remoto seguro de un servicio se realiza mediante estos pasos:

1. Usaremos un [ejemplo](service-fabric-reliable-services-communication-remoting.md) anterior que explica cómo establecer la comunicación remota de servicios de confianza. Empezaremos con una interfaz `IHelloWorldStateful` que define los métodos que estarán disponibles para la llamada a un procedimiento remoto de su servicio, que utilizará un método `FabricTransportServiceRemotingListener` declarado en el espacio de nombres `Microsoft.ServiceFabric.Services.Remoting.FabricTransport.Runtime`. Se trata de una implementación de `ICommunicationListener` que ofrece capacidades de comunicación remota.

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

2. Adición de la configuración de escucha y las credenciales de seguridad.

    El certificado que desea utilizar para proteger la comunicación de servicio debe instalarse en todos los nodos del clúster. Hay dos formas de proporcionar la configuración de escucha y las credenciales de seguridad.

    1. Directamente en el código de servicio.

        ```csharp
        protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
        {
            FabricTransportListenerSettings listnerSettings = new FabricTransportListenerSettings
            {
                MaxMessageSize = 10000000,
                SecurityCredentials = GetSecurityCredentials()
            };
            return new[]
            {
                new ServiceReplicaListener(
                    (context) => new FabricTransportServiceRemotingListener(context,this,listnerSettings))
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
            return x509Credentials;
        }
        ```
    2. Usando un [paquete de configuración](service-fabric-application-model.md).

        Agregue una sección `TransportSettings` en el archivo settings.xml.

        ```xml
        <!--Section name should always end with "TransportSettings"-->
        <!--Here we are using a prefix "HelloWorldStateful"-->
        <Section Name="HelloWorldStatefulTransportSettings">
            <Parameter Name="MaxMessageSize" Value="10000000" />
            <Parameter Name="SecurityCredentialsType" Value="X509" />
            <Parameter Name="CertificateFindType" Value="FindByThumbprint" />
            <Parameter Name="CertificateFindValue" Value="4FEF3950642138446CC364A396E1E881DB76B48C" />
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
                        context,this,FabricTransportListenerSettings.LoadFrom("HelloWorldStateful")))
            };
        }
        ```

         Si agrega una sección `TransportSettings` en settings.xml sin ningún prefijo, `FabricTransportListenerSettings` cargará de forma predetermina toda la configuración de esta sección.

         ```xml
         <!--"TransportSettings" section without any prefix-->
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

3. Al llamar a métodos en un servicio seguro con la pila de comunicación remota, en lugar de usar la clase `Microsoft.ServiceFabric.Services.Remoting.Client.ServiceProxy` para crear un proxy de servicio, usamos `Microsoft.ServiceFabric.Services.Remoting.Client.ServiceProxyFactory` y transmitimos la clase `FabricTransportSettings` que contiene `SecurityCredentials`.

    ```csharp

    var x509Credentials = new X509Credentials
    {
        FindType = X509FindType.FindByThumbprint,
        FindValue = "4FEF3950642138446CC364A396E1E881DB76B48C",
        StoreLocation = StoreLocation.LocalMachine,
        StoreName = "My",
        ProtectionLevel = ProtectionLevel.EncryptAndSign
    };
    x509Credentials.RemoteCommonNames.Add("ServiceFabric-Test-Cert");

    FabricTransportSettings transportSettings = new FabricTransportSettings
    {
        SecurityCredentials = x509Credentials,
    };

    ServiceProxyFactory serviceProxyFactory = new ServiceProxyFactory(
        (c) => new FabricTransportServiceRemotingClientFactory(transportSettings));

    IHelloWorldStateful client = serviceProxyFactory.CreateServiceProxy<IHelloWorldStateful>(
        new Uri("fabric:/MyApplication/MyHelloWorldService"));

    string message = await client.GetHelloWorld();

    ```

    Si el código de cliente se ejecuta como parte de un servicio, puede cargar la clase `FabricTransportSettings` desde settings.xml. Cree una sección TransportSettings similar al código de servicio, tal y como se mostró anteriormente. Realice los siguientes cambios en el código de cliente.

    ```csharp

    ServiceProxyFactory serviceProxyFactory = new ServiceProxyFactory(
        (c) => new FabricTransportServiceRemotingClientFactory(FabricTransportSettings.LoadFrom("TransportSettingsPrefix")));

    IHelloWorldStateful client = serviceProxyFactory.CreateServiceProxy<IHelloWorldStateful>(
        new Uri("fabric:/MyApplication/MyHelloWorldService"));

    string message = await client.GetHelloWorld();

    ```

    Si el cliente no se está ejecutando como parte de un servicio, puede crear un archivo client\_name.settings.xml en la misma ubicación donde se encuentra client\_name.exe y crear una sección TransportSettings en ese archivo.

    De forma similar al servicio, en el archivo settings.xml/client\_name.settings.xml de cliente también, si agrega una sección `TransportSettings` sin ningún *prefijo*, `FabricTransportSettings` cargará de forma predeterminada toda la configuración de esta sección.

    En ese caso, el código anterior se simplifica más aún.

    ```csharp

    IHelloWorldStateful client = ServiceProxy.Create<IHelloWorldStateful>(
                 new Uri("fabric:/MyApplication/MyHelloWorldService"));

    string message = await client.GetHelloWorld();

    ```

## Protección del servicio cuando se usa la pila de comunicación basada en WCF

Utilizaremos un [ejemplo](service-fabric-reliable-services-communication-wcf.md) anterior que explica cómo configurar una comunicación basada en WCF en servicios de confianza y ampliarla para que sea segura.

1. Para el servicio tenemos que crear una clase `WcfCommunicationListener`, que es segura. Por tanto, tendremos que modificar el método `CreateServiceReplicaListeners`.

    ```csharp
    protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
    {
        return new[]
        {
            new ServiceReplicaListener(
                this.CreateWcfCommunicationListener)
        };
    }

    private WcfCommunicationListener<ICalculator> CreateWcfCommunicationListener(StatefulServiceContext context)
    {
       var wcfCommunicationListner = new WcfCommunicationListener<ICalculator>(
            serviceContext:context,
            wcfServiceObject:this,
            // For this example we will be using NetTcpBinding
            listenerBinding: GetNetTcpBinding(),
            endpointResourceName:"WcfServiceEndpoint");

        // Add certificate details in the servicehost credentials.
        wcfCommunicationListner.ServiceHost.Credentials.ServiceCertificate.SetCertificate(
            StoreLocation.LocalMachine,
            StoreName.My,
            X509FindType.FindByThumbprint,
            "9DC906B169DC4FAFFD1697AC781E806790749D2F");
        return wcfCommunicationListner;
    }

    private static NetTcpBinding GetNetTcpBinding()
    {
        NetTcpBinding b = new NetTcpBinding(SecurityMode.TransportWithMessageCredential);
        b.Security.Message.ClientCredentialType = MessageCredentialType.Certificate;
        return b;
    }
    ```
    
2. En el cliente, la clase `WcfCommunicationClient` que hemos creado en el [ejemplo](service-fabric-reliable-services-communication-wcf.md) anterior no se cambia. Sin embargo, tendremos que reemplazar el método `CreateClientAsync` `WcfCommunicationClientFactory`.

    ```csharp
    public class SecureWcfCommunicationClientFactory<TServiceContract> : WcfCommunicationClientFactory<TServiceContract> where TServiceContract : class
    {
        private readonly Binding clientBinding;
        private readonly object callbackObject;
        public SecureWcfCommunicationClientFactory(
            Binding clientBinding,
            IEnumerable<IExceptionHandler> exceptionHandlers = null,
            IServicePartitionResolver servicePartitionResolver = null,
            string traceId = null,
            object callback = null)
            : base(clientBinding, exceptionHandlers, servicePartitionResolver,traceId,callback)
        {
            this.clientBinding = clientBinding;
            this.callbackObject = callback;
        }

        protected override Task<WcfCommunicationClient<TServiceContract>> CreateClientAsync(string endpoint, CancellationToken cancellationToken)
        {
            var endpointAddress = new EndpointAddress(new Uri(endpoint));
            ChannelFactory<TServiceContract> channelFactory;
            if (this.callbackObject != null)
            {
                channelFactory = new DuplexChannelFactory<TServiceContract>(
                this.callbackObject,
                this.clientBinding,
                endpointAddress);
            }
            else
            {
                channelFactory = new ChannelFactory<TServiceContract>(this.clientBinding, endpointAddress);
            }
            // Add certificate details to the ChannelFactory credentials.
            // These credentials will be used by the clients created by the
            // SecureWcfCommunicationClientFactory.  
            channelFactory.Credentials.ClientCertificate.SetCertificate(
                StoreLocation.LocalMachine,
                StoreName.My,
                X509FindType.FindByThumbprint,
                "9DC906B169DC4FAFFD1697AC781E806790749D2F");
            var channel = channelFactory.CreateChannel();
            var clientChannel = ((IClientChannel)channel);
            clientChannel.OperationTimeout = this.clientBinding.ReceiveTimeout;
            return Task.FromResult(this.CreateWcfCommunicationClient(channel));
        }
    }
    ```

    Utilice el método `SecureWcfCommunicationClientFactory` para crear una clase `WcfCommunicationClient`. Use al cliente para invocar métodos de servicio.

    ```csharp
    IServicePartitionResolver partitionResolver = ServicePartitionResolver.GetDefault();

    var wcfClientFactory = new SecureWcfCommunicationClientFactory<ICalculator>(clientBinding: GetNetTcpBinding(), servicePartitionResolver: partitionResolver);

    var calculatorServiceCommunicationClient =  new WcfCommunicationClient(
        wcfClientFactory,
        ServiceUri,
        ServicePartitionKey.Singleton);

    var result = calculatorServiceCommunicationClient.InvokeWithRetryAsync(
        client => client.Channel.Add(2, 3)).Result;
    ```

## Pasos siguientes

* [Web API con OWIN en Reliable Services](service-fabric-reliable-services-communication-webapi.md)

<!---HONumber=AcomDC_0330_2016-->