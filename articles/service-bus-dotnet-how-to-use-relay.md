<properties urlDisplayName="Service Bus Relay" pageTitle="Cómo usar el relé del bus de servicio (.NET) - Azure" metaKeywords="empezar a trabajar con el relé del bus de servicio de Azure C# " description="Aprenda a usar el servicio de relé del Bus de servicio de Azure para conectar dos aplicaciones hospedadas en diferentes ubicaciones." metaCanonical="" services="service-bus" documentationCenter=".NET" title="How to Use the Service Bus Relay Service" authors="sethm" solutions="" manager="timlt" editor="mattshel" />

<tags ms.service="service-bus" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="09/24/2014" ms.author="sethm" />







# Uso del servicio del relé del bus de servicio

En esta guía se muestra cómo usar el servicio de relé del bus de servicio. Los ejemplos se escriben en C# y usan la API de Windows Communication Foundation con extensiones contenidas en el conjunto del bus de servicio que forma parte del .NET Azure SDK. Para obtener más información sobre el relé del bus de servicio, consulte la sección "Pasos siguientes".

[WACOM.INCLUDE [create-account-note](../includes/create-account-note.md)]

<h2>¿Qué es el relé del bus de servicio?</h2>

El servicio del **relé** del bus de servicio le permite crear **aplicaciones híbridas** que se pueden ejecutar en los centros de datos de Azure y en su propio entorno empresarial local. El relé del bus de servicio facilita esta posibilidad, ya que le permite exponer de forma segura los servicios Windows Communication Foundation (WCF) que se encuentran en una red corporativa en la nube pública sin tener que abrir una conexión de firewall y sin que sea necesario realizar cambios intrusivos en una infraestructura de red corporativa.

![Relay Concepts](./media/service-bus-dotnet-how-to-use-relay/sb-relay-01.png)

El relé del bus de servicio le permite hospedar servicios WCF en su entorno empresarial existente. A continuación, puede delegar la escucha de las sesiones entrantes y las solicitudes a esos servicios WCF al bus de servicio que se ejecuta en Azure. Esto le permite exponer esos servicios en el código de aplicación que se ejecuta en Azure o en entornos móviles de trabajo o asociados de extranet. El bus de servicio le permite controlar de forma segura quién puede obtener acceso a estos servicios en un nivel específico. Proporciona una forma segura y eficaz de exponer los datos y la funcionalidad de aplicaciones de las soluciones empresariales existentes y aprovecharlos desde la nube.

En esta guía se muestra cómo usar el relé del bus de servicio para crear un servicio web WCF, expuesto mediante el enlace de canales TCP, que implementa una conversación segura entre dos partes.

##Creación de un espacio de nombres de servicio

Para comenzar a usar el relé del bus de servicio en Azure, primero debe crear un espacio de nombres de servicio. Un espacio de nombres proporciona un contenedor con un ámbito para el desvío de recursos del bus de servicio en la aplicación.

Para crear un nombre de espacio de servicio:

1.  Inicie sesión en el [Portal de administración de Azure][].

2.  En el panel de navegación izquierdo del Portal de administración, haga clic en
    **Bus de servicio**.

3.  En el panel inferior del Portal de administración, haga clic en **Create**.   

	![](./media/service-bus-dotnet-how-to-use-relay/sb-queues-13.png)

4.  En el cuadro de diálogo **Add a new namespace**, especifique un nombre de espacio de nombres.
    El sistema realiza la comprobación automáticamente para ver si el nombre está disponible.   

	![](./media/service-bus-dotnet-how-to-use-relay/sb-queues-04.png)


5.  Después de asegurarse de que el nombre de espacio de nombres está disponible, seleccione el país o región en el que debe hospedarse el espacio de nombres (asegúrese de que usa el mismo país o la misma región en los que está realizando la implementación de los recursos de proceso).

	IMPORTANTE: seleccione la **misma región** que vaya a seleccionar para la implementación de la aplicación. Con esto conseguirá el máximo rendimiento.

6.	Haga clic en la marca de verificación. El sistema crea ahora el espacio de nombres del servicio y lo habilita. Es posible que tenga que esperar algunos minutos mientras el sistema realiza el aprovisionamiento de los recursos para la cuenta.

	![](./media/service-bus-dotnet-how-to-use-relay/getting-started-multi-tier-27.png)

	El espacio de nombres que creó aparecerá a continuación en el Portal de administración y tardará un poco en activarse. Espere hasta que el estado sea **Active** antes de continuar.

##Obtención de credenciales de administración predeterminadas para el espacio de nombres

Para realizar operaciones de administración, como la creación de una conexión de relé, en el espacio de nombres nuevo, debe configurar la regla de autorización de la Firma de acceso compartido (SAS) para el espacio de nombres. Para obtener más información acerca de SAS, consulte [Autenticación con firma de acceso compartido en Service Bus][].

1.  En el panel de navegación izquierdo, haga clic en el nodo **Bus de servicio** para ver la lista de espacios de nombres disponibles:   
	![](./media/service-bus-dotnet-how-to-use-relay/sb-queues-13.png)


2.  Haga doble clic en el nombre del espacio de nombres que acaba de crear en la lista desplegable:   
	![](./media/service-bus-dotnet-how-to-use-relay/sb-queues-09.png)


3.  Haga clic en la ficha **Configurar** en la parte superior de la página.   
 
4.  Cuando se aprovisiona un espacio de nombres del Bus de servicio, un **SharedAccessAuthorizationRule**, con **KeyName** establecido en **RootManageSharedAccessKey**, se crea de forma predeterminada. Esta página muestra esa clave, así como las claves principales y secundarias para la regla predeterminada. 

##Obtenga el paquete NuGet del bus de servicio##

El paquete **NuGet** del bus de servicio es la forma más sencilla de obtener la API del bus de servicio y configurar su aplicación con todas las dependencias del bus de servicio. La extensión NuGet Visual Studio facilita la instalación y la actualización de las bibliotecas y las herramientas en Visual Studio y Visual Studio Express 2012 para Web. El paquete NuGet del bus de servicio es la forma más sencilla de obtener la API del bus de servicio y configurar su aplicación con todas las dependencias del bus de servicio.

Realice los pasos siguientes para instalar el paquete NuGet en su aplicación:

1.  En el Explorador de soluciones, haga clic con el botón secundario en **References** y, a continuación, en **Manage NuGet Packages**.
2.  Busque Azure y seleccione el elemento **Azure Service Bus**. Haga clic en **Install** para completar la instalación y, luego, cierre este cuadro de diálogo.

	![](./media/service-bus-dotnet-how-to-use-relay/getting-started-multi-tier-13.png)
  

##Cómo usar el bus de servicio para exponer y consumir un servicio web SOAP con TCP

Para exponer un servicio web WCF (SOAP) para consumo externo, debe realizar cambios en las direcciones y enlaces de los servicios. Esto puede requerir cambios en el archivo de configuración o en el código, según la forma en la que se hayan establecido y configurado los servicios WCF. Tenga en cuenta que WCF le permite disponer de varios extremos de red en el mismo servicio, por lo que puede retener los extremos internos existentes mientras agrega extremos del bus de servicio para el acceso externo al mismo tiempo.

En esta tarea, creará un servicio WCF simple y le agregará un agente de escucha del bus de servicio. En este ejercicio se presupone que está familiarizado con Visual Studio y, por lo tanto, no se proporcionarán todos los detalles relacionados con la creación de un proyecto. El ejercicio se centra en el código.

Antes comenzar a realizar los pasos siguientes, complete el procedimiento que aparece a continuación para configurar su entorno:

1.  En Visual Studio, cree una aplicación de consola que contenga dos proyectos en la solución: "Client" y "Service".
2.  Agregue el paquete **Azure Service Bus NuGet** a los dos proyectos.     De esa forma, se agregarán todas las referencias necesarias del conjunto a los proyectos.

### Creación del servicio

Primero cree el servicio. Cualquier servicio WCF consiste en
por lo menos tres partes distintas:

-   Definición de un contrato que describe qué mensajes se intercambian y qué operaciones se van a invocar. 
-   Implementación del contrato establecido.
-   Hospedaje de los hosts que ofrecen servicio y exponen una serie de extremos.

Los ejemplos de código de esta sección están relacionados con cada uno de estos componentes.

El contrato define una única operación, **AddNumbers**, que agrega dos números y devuelve el resultado. La interfaz **IProblemSolverChannel** permite al cliente administrar más fácilmente la duración del proxy. La creación de esta interfaz se considera una práctica recomendada. Es buena idea poner la definición del contrato en un archivo independiente para poder hacer referencia a ese archivo desde los proyectos "Client" y "Service". También puede copiar el código en los dos proyectos:

        using System.ServiceModel;
     
        [ServiceContract(Namespace = "urn:ps")]
        interface IProblemSolver
        {
            [OperationContract]
            int AddNumbers(int a, int b);
        }
     
        interface IProblemSolverChannel : IProblemSolver, IClientChannel {}

With the contract in place, the implementation is trivial:

        class ProblemSolver : IProblemSolver
        {
            public int AddNumbers(int a, int b)
            {
                return a + b;
            }
        }

####Cómo configurar un host de servicio mediante programación####

Con el contrato y la implementación en su lugar, puede hospedar el servicio. El hospedaje se produce dentro de un objeto **System.ServiceModel.ServiceHost**, que se encarga de administrar las instancias del servicio y hospeda los extremos que escuchan mensajes. El código siguiente configura el servicio con un extremo local normal y un extremo del bus de servicio para mostrar la apariencia, en paralelo, de los extremos internos y externos. Reemplace la cadena "\*\*namespace\*\*" por el nombre del espacio de nombres y "\*\*key\*\*" por la clave de emisor obtenida en el paso de configuración anterior. 

    ServiceHost sh = new ServiceHost(typeof(ProblemSolver));

    sh.AddServiceEndpoint(
       typeof (IProblemSolver), new NetTcpBinding(), 
       "net.tcp://localhost:9358/solver");

    sh.AddServiceEndpoint(
       typeof(IProblemSolver), new NetTcpRelayBinding(), 
       ServiceBusEnvironment.CreateServiceUri("sb", "**namespace**", "solver"))
        .Behaviors.Add(new TransportClientEndpointBehavior {
              TokenProvider = TokenProvider.CreateSharedSecretTokenProvider( "owner", "**key**")});

    sh.Open();

    Console.WriteLine("Press ENTER to close");
    Console.ReadLine();

    sh.Close();

En el ejemplo, se crean dos extremos que se encuentran en la misma implementación del contrato. Uno es local y el otro se proyecta a través del bus de servicio. Las diferencias clave entre ellos son los enlaces; **NetTcpBinding** para el local y **NetTcpRelayBinding** para el extremo del bus de servicio y las direcciones. El extremo local dispone de una dirección de red local con un puerto distinto. El extremo del bus de servicio cuenta con una dirección del extremo compuesta por la cadena "sb", el nombre del espacio de nombres y la ruta "solver". Esto genera el URI "sb://[serviceNamespace].servicebus.windows.net/solver", que identifica el extremo de servicio como extremo TCP del bus de servicio con un nombre de DNS externo completo. Si coloca el código reemplazando los marcadores de posición anteriores en la función **Main** de la aplicación "Service", dispondrá de servicio funcional. Si quiere que el servicio escuche exclusivamente en el bus de servicio, quite la declaración del extremo local.

####Cómo configurar un host de servicio en el archivo App.config####

También puede configurar el host con el archivo App.config. El código de hospedaje del servicio en este caso es el siguiente:

    ServiceHost sh = new ServiceHost(typeof(ProblemSolver));
    sh.Open();
    Console.WriteLine("Press ENTER to close");
    Console.ReadLine();
    sh.Close();

Las definiciones del extremo se trasladan al archivo App.config. Tenga en cuenta que el paquete **NuGet** ya ha agregado una serie de definiciones al archivo App.config, que son las extensiones de configuración necesarias para el bus del servicio. El siguiente fragmento de código, que es el equivalente exacto del código anterior, debe aparecer directamente debajo del elemento **system.serviceModel**. El fragmento presupone que el espacio de nombres C\# del proyecto tiene el nombre de "Service".
Reemplace los marcadores de posición por la clave y el espacio de nombres del servicio del bus de servicio.

    <services>
        <service name="Service.ProblemSolver">
            <endpoint contract="Service.IProblemSolver"
                      binding="netTcpBinding"
                      address="net.tcp://localhost:9358/solver"/>
            <endpoint contract="Service.IProblemSolver"
                      binding="netTcpRelayBinding"
                      address="sb://**namespace**.servicebus.windows.net/solver"
                      behaviorConfiguration="sbTokenProvider"/>
        </service>
    </services>
    <behaviors>
        <endpointBehaviors>
            <behavior name="sbTokenProvider">
                <transportClientEndpointBehavior>
                    <tokenProvider>
                        <sharedSecret issuerName="owner" issuerSecret="**key**" />
                    </tokenProvider>
                </transportClientEndpointBehavior>
            </behavior>
        </endpointBehaviors>
    </behaviors>

Una vez que realice estos cambios, el servicio se iniciará como antes, pero con dos extremos activos: uno local y otro que escucha en la nube.

### Creación del cliente

####Cómo configurar un cliente mediante programación####

Para consumir el servicio, puede construir un cliente WCF mediante un objeto **ChannelFactory**. El bus de servicio usa un modelo de seguridad basado en notificaciones implementado mediante el Servicio de control de acceso (ACS). La clase **TokenProvider** representa un proveedor de tokens de seguridad con patrones de diseño Factory Method integrados que devuelven algunos proveedores de tokens conocidos. En los ejemplos siguientes se usa **SharedSecretTokenProvider** para mantener las credenciales secretas compartidas y administrar la adquisición de los tokens apropiados desde el Servicio de control de acceso. El nombre y la clave son los que se obtienen del portal tal y como se describió en la sección anterior.

Primero, haga referencia al código del contrato **IProblemSolver** o cópielo del servicio en el proyecto del cliente. 
A continuación, reemplace el código del método **Main** del cliente, vuelva a reemplazar el texto del marcador de posición por la clave y el espacio de nombres del servicio del bus de servicio:

    var cf = new ChannelFactory<IProblemSolverChannel>(
        new NetTcpRelayBinding(), 
        new EndpointAddress(ServiceBusEnvironment.CreateServiceUri("sb", "**namespace**", "solver")));

    cf.Endpoint.Behaviors.Add(new TransportClientEndpointBehavior
                { TokenProvider = TokenProvider.CreateSharedSecretTokenProvider("owner","**key**") });
     
    using (var ch = cf.CreateChannel())
    {
        Console.WriteLine(ch.AddNumbers(4, 5));
    }

Ahora puede compilar el cliente y el servicio, ejecutarlos (ejecute primero el servicio) y el cliente llamará al servicio e imprimirá "9". Puede ejecutar el cliente y el servidor en distintas máquinas, incluso entre redes, y la comunicación seguirá funcionando. El código de cliente también puede ejecutarse en la nube o localmente.

####Cómo configurar a un cliente en el archivo App.config####

También puede configurar el cliente con el archivo App.config. El código de
cliente para esto es el siguiente:

    var cf = new ChannelFactory<IProblemSolverChannel>("solver");
    using (var ch = cf.CreateChannel())
    {
        Console.WriteLine(ch.AddNumbers(4, 5));
    }

Las definiciones del extremo se trasladan al archivo App.config. El fragmento de código
siguiente, que es el mismo que el código anterior, debería aparecer directamente bajo el elemento **system.serviceModel**. Aquí, como antes, debe reemplazar los marcadores de posición por la clave y el espacio de nombres del servicio del bus de servicio.

    <client>
        <endpoint name="solver" contract="Service.IProblemSolver"
                  binding="netTcpRelayBinding"
                  address="sb://**namespace**.servicebus.windows.net/solver"
                  behaviorConfiguration="sbTokenProvider"/>
    </client>
    <behaviors>
        <endpointBehaviors>
            <behavior name="sbTokenProvider">
                <transportClientEndpointBehavior>
                    <tokenProvider>
                        <sharedSecret issuerName="owner" issuerSecret="**key**" />
                    </tokenProvider>
                </transportClientEndpointBehavior>
            </behavior>
        </endpointBehaviors>
    </behaviors>

#Pasos siguientes

Ahora que conoce los fundamentos del servicio del **relé** del bus de servicio,
siga estos vínculos para obtener más información.

-   Creación de un servicio: [Creación de un servicio de bus de servicio][].
-   Creación del cliente: [Generación de una aplicación de Service Bus Client][].
-   Ejemplos de bus de servicio: descargar desde [Ejemplos de Azure][].

  [Creación de un espacio de nombres de servicio]: #create_namespace
  [Obtención de credenciales de administración predeterminadas para el espacio de nombres]: #obtain_credentials
  [Obtenga el paquete NuGet del bus de servicio]: #get_nuget_package
  [Cómo usar el bus de servicio para exponer y consumir un servicio web SOAP con TCP]: #how_soap
  [Portal de administración de Azure]: http://manage.windowsazure.com
  [Autenticación con firma de acceso compartido en Service Bus]: http://msdn.microsoft.com/es-es/library/dn170477.aspx
  [Creación de un servicio de bus de servicio]: http://msdn.microsoft.com/es-es/library/windowsazure/ee173564.aspx
  [Generación de una aplicación de Service Bus Client]: http://msdn.microsoft.com/es-es/library/windowsazure/ee173543.aspx
  [Ejemplos de Azure]: http://code.msdn.microsoft.com/windowsazure

<!--HONumber=35.2-->
