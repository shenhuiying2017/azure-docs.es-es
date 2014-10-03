<properties linkid="dev-net-tutorials-hybrid-solution" urlDisplayName="Hybrid Application" pageTitle="Hybrid On-Premises/ Cloud Application (.NET) - Azure" metaKeywords="Azure Service Bus tutorial,hybrid .NET" description="Learn how to create a .NET On-Premises/Cloud Hybrid Application Using the Azure Service Bus Relay." metaCanonical="" services="service-bus" documentationCenter=".NET" title=".NET On-Premises/Cloud Hybrid Application Using Service Bus Relay" authors="sethm" solutions="" manager="dwrede" editor="mattshel" />

<tags ms.service="service-bus" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="sethm"></tags>

# Aplicación híbrida en la nube/local .NET mediante el relé del bus de servicio

## <span class="short-header">INTRODUCCIÓN</span>INTRODUCCIÓN

Desarrollar aplicaciones híbridas en la nube con Azure es muy sencillo con Visual Studio 2013 y el SDK de Azure para .NET gratuito. En esta guía se asume que no tiene ninguna experiencia previa con Azure. En menos de 30 minutos, dispondrá de una aplicación que utiliza varios recursos de Windows Azure funcionando en la nube.

Aprenderá a:

-   Crear o adaptar un servicio web existente para su consumo por una solución web.
-   Utilizar el relé del bus de servicio de Azure para compartir datos entre una aplicación de Azure y un servicio web hospedado en otra parte.

[WACOM.INCLUDE [create-account-note][]]

### CÓMO AYUDA EL RELÉ DEL BUS DE SERVICIO CON SOLUCIONES HÍBRIDAS

Las soluciones de negocio por lo general están compuestas por una combinación de código personalizado escrito para afrontar los nuevos y exclusivos requisitos empresariales, así como la funcionalidad existente proporcionada por soluciones y sistemas que ya están instalados.

Los arquitectos de soluciones están comenzando a utilizar la nube para abordar con más facilidad los requisitos de escala y reducir los costes operativos. De esta manera, se dan cuenta de que los activos de servicio existentes que les gustaría aprovechar como base de sus soluciones se encuentran dentro del firewall corporativo y no resulta sencillo para la solución en la nube el acceso a ellos. Muchos de los servicios internos no están construidos ni hospedados de una forma que se puedan exponer fácilmente en los servidores perimetrales de la red corporativa.

El *relé del bus de servicio* está diseñado para el caso de uso de tomar los servicios web Windows Communication Foundation (WCF) existentes y permitir que se pueda tener acceso de forma segura a soluciones que residan fuera del perímetro corporativo, sin que sea necesario realizar cambios intrusivos en la infraestructura de red corporativa. Los servicios del relé del bus de servicio se siguen hospedando en su entorno existente, pero delegan la escucha de las sesiones y solicitudes entrantes al bus de servicio hospedado en la nube. El bus de servicio también protege dichos servicios contra el acceso no autorizado, para lo que usa el control de acceso de Active Directory de Azure.

### EL ESCENARIO DE LA SOLUCIÓN

En este tutorial, se creará un sitio web de ASP.NET MVC 4 que permitirá ver una lista de productos en la página del inventario de productos.

![][]

En este tutorial se asume que la información de los productos se encuentra en un sistema local y se usa el relé del bus de servicio para obtener acceso a dicho sistema. Esto lo simula un servicio web que se ejecuta en una aplicación de consola simple y el respaldo lo proporciona un conjunto de productos en memoria. Dicha aplicación de consola se puede ejecutar en el equipo propio y el rol web se puede implementar en Azure. Al hacerlo, observará que el rol web que se ejecuta en el centro de datos de Azure llamará a su equipo, aunque casi seguro que este residirá al menos detrás de un firewall y una capa de traducción de direcciones de red (NAT).

Esta es una captura de pantalla de la página de inicio de la aplicación web finalizada.

![][1]

## <span class="short-header">CONFIGURACIÓN DEL ENTORNO</span>CONFIGURACIÓN DEL ENTORNO DE DESARROLLO

Antes de comenzar a desarrollar la aplicación de Azure, debe obtener las herramientas y configurar el entorno de desarrollo.

1.  Para instalar el SDK de Azure para .NET, haga clic en el botón siguiente:

    [Obtención de herramientas y de SDK][]

2.  Haga clic en **install the SDK**.

3.  Elija el vínculo de la versión de Visual Studio que está utilizando. Para los pasos de este tutorial se utiliza Visual Studio 2013:

    ![][2]

4.  Cuando se le solicite ejecutar o guardar **WindowsAzureSDKForNet.exe**, haga clic en
    **Run**:

    ![][3]

5.  En el instalador de plataforma web, haga clic en **Install** y continúe con la instalación:

    ![][4]

6.  Cuando la instalación se complete, dispondrá de todo lo necesario para iniciar el desarrollo. El SDK incluye las herramientas que le permiten desarrollar fácilmente aplicaciones Azure en Visual Studio. Si no tiene instalado Visual Studio, instale también el programa gratuito Visual Studio Express.

## <span class="short-header">CREACIÓN DE UN ESPACIO DE NOMBRES</span>CREACIÓN DE UN ESPACIO DE NOMBRES DE SERVICIO

Para comenzar a usar las características del bus de servicio en Azure, primero debe crear un espacio de nombres de servicio. Un espacio de nombres de servicio proporciona un contenedor
con un ámbito para el desvío de recursos del Bus de servicio en la aplicación.

Los espacios de nombres y las entidades de mensajería del bus de servicio se pueden administrar a través del [Portal de administración de Azure][] o del Explorador de servidores de Visual Studio, pero los espacios de nombres solo se pueden crear desde el portal.

### Para crear un de espacio de nombres de servicio mediante el portal:

1.  Inicie sesión en el [Portal de administración de Azure][].

2.  En el panel de navegación izquierdo del Portal de administración, haga clic en
    **Bus de servicio**.

3.  En el panel inferior del Portal de administración, haga clic en **Crear**.
    ![][5]

4.  En el cuadro de diálogo **Agregar un nuevo espacio de nombres**, especifique un nombre de espacio de nombres.
    El sistema realiza la comprobación de inmediato para ver si el nombre está disponible.
    ![][6]

5.  Después de asegurarse de que el nombre de espacio de nombres esté disponible, seleccione el
    país o región en el que debe hospedarse el espacio de nombres (asegúrese
    de que usa el mismo país o la misma región en los que está realizando la implementación de los
    recursos de proceso).

    IMPORTANTE: Seleccione la **misma región** que vaya a seleccionar para la
    implementación de la aplicación. Con esto conseguirá el máximo rendimiento.

6.  Haga clic en la marca de verificación. El sistema crea ahora el espacio de nombres del
    servicio y lo habilita. Es posible que tenga que esperar algunos minutos mientras
    el sistema realiza el aprovisionamiento de los recursos para la cuenta.

    ![][7]

El espacio de nombres que creó aparecerá a continuación en el Portal de administración y
tardará un poco en activarse. Espere hasta que el estado sea **Active** antes de continuar.

## <span class="short-header">OBTENCIÓN DE CREDENCIALES DE ADMINISTRACIÓN</span>OBTENCIÓN DE CREDENCIALES DE ADMINISTRACIÓN PREDETERMINADAS PARA EL ESPACIO DE NOMBRES

Para realizar operaciones de administración (como la creación de una cola) en el nuevo espacio de nombres, debe obtener las credenciales de administración para el espacio de nombres.

1.  En la ventana principal, haga clic en el nombre del espacio de nombres de servicio.

    ![][8]

2.  Haga clic en **Connection Information**.

    ![][9]

3.  En el panel **Access connection information**, busque las entradas **Default Issuer** y **Default Key**.

4.  Anote la clave o cópiela en el Portapapeles.

### Administración de espacios de nombres de servicio mediante el Explorador de servidores de Visual Studio:

Para administrar un espacio de nombres y obtener la información de conexión utilizando Visual Studio en vez del Portal de administración, siga el procedimiento descrito [aquí][], en la sección titulada **Para conectarse a Azure desde Visual Studio**. Al iniciar sesión en Azure, el nodo **Bus de servicio** bajo el árbol **Microsoft Azure** del Explorador de servidores se rellena automáticamente con los espacios de nombre que ya haya creado. Haga clic con el botón secundario en cualquier espacio de nombre, a continuación haga clic en **Propiedades** para ver la cadena de conexión y otros metadatos asociados a este nombre de espacio en el panel **Propiedades** de Visual Studio.

![][10]

Anote el valor de **SharedAccessKey** o cópielo en el Portapapeles.

## <span class="short-header">CREACIÓN DE UN SERVIDOR LOCAL</span>CREACIÓN DE UN SERVIDOR LOCAL

En primer lugar, cree un sistema de catálogo de productos local (ficticio). Será bastante simple; puede considerar que representa un sistema de catálogo de productos local real con una superficie de servicio completa que se intenta integrar.

Este proyecto comenzará como una aplicación de consola de Visual Studio. El proyecto usa el paquete Service Bus NuGet para incluir las bibliotecas y los ajustes de configuración del bus de servicio. La extensión NuGet Visual Studio facilita la instalación y la actualización de las bibliotecas y las herramientas en Visual Studio y Visual Studio Express. El paquete NuGet del bus de servicio es la forma más sencilla de obtener la API del bus de servicio y configurar su aplicación con todas las dependencias del bus de servicio. Para obtener más información acerca del uso del paquete NuGet y del bus de servicio, consulte [Usar el paquete NuGet de Bus de servicio][].

### CREACIÓN DEL PROYECTO

1.  Con privilegios de administrador, inicie Microsoft Visual Studio 2013 o Microsoft Visual Studio Express. Para iniciar Visual Studio con privilegios de administrador, haga clic con el botón secundario en **Microsoft Visual Studio 2013 (o Microsoft Visual Studio Express)** y, a continuación, haga clic en **Ejecutar como administrador**.
2.  En Visual Studio, en el menú **Archivo**, haga clic en **Nuevo** y, a continuación, en **Proyecto**.

    ![][11]

3.  En **Plantillas instaladas**, en **Visual C\#**, haga clic en **Aplicación de consola**. En el cuadro **Nombre**, escriba el nombre
    **ProductsServer**:

    ![][12]

4.  Haga clic en **Aceptar** para crear el proyecto **ProductsServer**.

5.  En el **Explorador de soluciones**, haga clic con el botón secundario en **ProductsServer** y, a continuación, en <strong>Propiedades<strong>.
6.  Haga clic en la pestaña **Application** de la izquierda y asegúrese de que **.NET
    Framework 4** o **.NET Framework 4.5** aparecen en la lista desplegable **Marco de trabajo de destino**. Si no aparecen, selecciónelas en la lista desplegable y haga clic en **Yes**
     cuando se le solicite volver a cargar el proyecto.

    ![][13]

7.  Si ya ha instalado el administrador del paquete NuGet para Visual Studio, vaya al paso siguiente. De lo contrario, visite [NuGet][] y haga clic en [Install NuGet][]. Siga las indicaciones para instalar el administrador del paquete NuGet y, a continuación, reinicie Visual Studio.

8.  En el **Explorador de soluciones**, haga clic con el botón secundario en **References** y, a continuación, haga clic en
    **Manage NuGet Packages**...
9.  En la columna de la izquierda del cuadro de diálogo de NuGet, haga clic en **Online**.

10. En la columna de la derecha, haga clic en el cuadro **Search**, escriba "**WindowsAzure**" y seleccione el elemento **Bus de servicio de Microsoft Azure**. Haga clic en **Install** para completar la instalación y, a continuación, cierre este cuadro de diálogo.

    ![][14]

    Tenga en cuenta que ahora se hace referencia a los ensamblados del cliente requeridos.

11. Agregue una clase nueva para el contrato de su producto. En el **Explorador de soluciones**, haga clic con el botón secundario en el proyecto **ProductsServer**, a continuación en **Add** y, por último, en **Class**.

    ![][15]

12. En el cuadro **Name**, escriba el nombre **ProductsContract.cs**. A continuación, haga clic en <strong>Add<strong>.
13. En **ProductsContract.cs**, sustituya la definición del espacio de nombres por el siguiente código, que define el contrato del servicio:

        namespace ProductsServer
        {
            using System.Collections.Generic;
            using System.Runtime.Serialization;
            using System.ServiceModel;

            // Define the data contract for the service
            [DataContract]
            // Declare the serializable properties
            public class ProductData
            {
                [DataMember]
                public string Id { get; set; }
                [DataMember]
                public string Name { get; set; }
                [DataMember]
                public string Quantity { get; set; }
            }

            // Define the service contract.
            [ServiceContract]
            interface IProducts
            {
                [OperationContract]
                IList<ProductData> GetProducts();

            }

            interface IProductsChannel : IProducts, IClientChannel
            {
            }
        }

14. En Program.cs, sustituya la definición del espacio de nombres por el siguiente código, que agrega el servicio de perfil y su host:

        namespace ProductsServer
        {
            using System;
            using System.Linq;
            using System.Collections.Generic;
            using System.ServiceModel;

            // Implement the IProducts interface
            class ProductsService : IProducts
            {

                // Populate array of products for display on Website
                ProductData[] products = 
                    new []
                        {
                            new ProductData{ Id = "1", Name = "Rock", 
                                             Quantity = "1"},
                            new ProductData{ Id = "2", Name = "Paper", 
                                             Quantity = "3"},
                            new ProductData{ Id = "3", Name = "Scissors", 
                                             Quantity = "5"},
                            new ProductData{ Id = "4", Name = "Well", 
                                             Quantity = "2500"},
                        };

                // Display a message in the service console application 
                // when the list of products is retrieved
                public IList<ProductData> GetProducts()
                {
                    Console.WriteLine("GetProducts called.");
                    return products;
                }

            }

            class Program
            {
                // Define the Main() function in the service application
                static void Main(string[] args)
                {
                    var sh = new ServiceHost(typeof(ProductsService));
                    sh.Open();

                    Console.WriteLine("Press ENTER to close");
                    Console.ReadLine();

                    sh.Close();
                }
            }
        }

15. En el **Explorador de soluciones**, haga doble clic en el archivo **app.config** para abrirlo en el editor de **Visual Studio**. Sustituya el contenido de
    **\<system.ServiceModel\>** por el siguiente código XML. Asegúrese de sustituir *yourServiceNamespace* por el nombre del espacio de nombres del servicio y *yourIssuerSecret* por la clave que recuperó del Portal de administración de Azure:

        <system.serviceModel>
          <extensions>
             <behaviorExtensions>
                <add name="transportClientEndpointBehavior" type="Microsoft.ServiceBus.Configuration.TransportClientEndpointBehaviorElement, Microsoft.ServiceBus, Version=2.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
              </behaviorExtensions>
              <bindingExtensions>
                 <add name="netTcpRelayBinding" type="Microsoft.ServiceBus.Configuration.NetTcpRelayBindingCollectionElement, Microsoft.ServiceBus, Version=2.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
              </bindingExtensions>
          </extensions>
          <services>
             <service name="ProductsServer.ProductsService">
               <endpoint address="sb://yourServiceNamespace.servicebus.windows.net/products" binding="netTcpRelayBinding" contract="ProductsServer.IProducts"
        behaviorConfiguration="products"/>
             </service>
          </services>
          <behaviors>
             <endpointBehaviors>
               <behavior name="products">
                 <transportClientEndpointBehavior>
                    <tokenProvider>
                       <sharedSecret issuerName="owner" issuerSecret="yourIssuerSecret" />
                    </tokenProvider>
                 </transportClientEndpointBehavior>
               </behavior>
             </endpointBehaviors>
          </behaviors>
        </system.serviceModel>

16. Presione **F6**, o bien en el menú **Build**, haga clic en **Build Solution** para compilar la aplicación para comprobar la precisión del trabajo realizado hasta el momento.

## <span class="short-header">CREACIÓN DE UNA APLICACIÓN ASP.NET MVC</span>CREACIÓN DE UNA APLICACIÓN ASP.NET MVC

En esta sección se creará una aplicación ASP.NET simple que mostrará los datos recuperados del servicio de producto.

### CREACIÓN DEL PROYECTO

1.  Asegúrese de que Microsoft Visual Studio 2013 se ejecuta con privilegios de administrador. Si no es así, inicie Visual Studio con privilegios de administrador, haga clic con el botón secundario en **Microsoft Visual Studio 2013 (o Microsoft Visual Studio Express)** y, a continuación, haga clic en **Ejecutar como administrador**. El emulador de proceso de Azure, que se describirá más adelante, requiere que se inicie Visual Studio con privilegios de administrador.

2.  En Visual Studio, en el menú **Archivo**, haga clic en **Nuevo** y, a continuación, en **Proyecto**.

3.  En **Installed Templates**, en **Visual C\#**, haga clic en **ASP.NET Web Application**. Asigne al proyecto el nombre **ProductsPortal**. A continuación, haga clic en **Aceptar**.

    ![][16]

4.  En la lista **Select a template**, haga clic en **MVC** y, a continuación, en **Aceptar**.

    ![][17]

5.  En el **Explorador de soluciones**, haga clic con el botón secundario en la carpeta **Modelos** y, a continuación, en **Add** y, por último, en <strong>Class<strong>. En el cuadro **Name**, escriba el nombre
    **Product.cs**. A continuación, haga clic en **Add**.

    ![][18]

### MODIFICACIÓN DE LA APLICACIÓN WEB

1.  En el archivo Product.cs en Visual Studio, sustituya la definición del espacio de nombres existente por el código siguiente:

        // Declare properties for the products inventory
        namespace ProductsWeb.Models
        {
            public class Product
            {
                public string Id { get; set; }
                public string Name { get; set; }
                public string Quantity { get; set; }
            }
        }

2.  En el archivo HomeController.cs en Visual Studio, sustituya la definición del espacio de nombres existente por el código siguiente:

        namespace ProductsWeb.Controllers
        {
            using System.Collections.Generic;
            using System.Web.Mvc;
            using Models;

            public class HomeController : Controller
            {
                // Return a view of the products inventory
                public ActionResult Index(string Identifier, string ProductName)
                {
                    var products = new List<Product> 
                        {new Product {Id = Identifier, Name = ProductName}};
                    return View(products);
                }

            }
        }

3.  En el **Explorador de soluciones**, expanda Views\\Shared:

    ![][19]

4.  A continuación, haga doble clic en \_Layout.cshtml para abrirlo en el editor de Visual Studio.

5.  Cambie todas las ocurrencias de **My ASP.NET Application** por **LITWARE'S Products**.

6.  Suprima los vínculos **Home**, **About** y **Contact**. Elimine el código resaltado:

    ![][20]

7.  En el **Explorador de soluciones**, expanda Views\\Home:

    ![][21]

8.  Haga doble clic en Index.cshtml para abrirlo en el editor de Visual Studio.
    Sustituya todo el contenido del archivo por el código siguiente:

        @model IEnumerable<ProductsWeb.Models.Product>

        @{
            ViewBag.Title = "Index";
        }

        <h2>Prod Inventory</h2>

        <table>
            <tr>
                <th>
                    @Html.DisplayNameFor(model => model.Name)
                </th>
                <th></th>
                <th>
                    @Html.DisplayNameFor(model => model.Quantity)
                </th>
            </tr>

        @foreach (var item in Model) {
            <tr>
                <td>
                    @Html.DisplayFor(modelItem => item.Name)
                </td>
                <td>
                    @Html.DisplayFor(modelItem => item.Quantity)
                </td>
            </tr>   
        }

        </table>

9.  Para comprobar la precisión del trabajo realizado hasta el momento, puede presionar **F6** o
    **Ctrl+Mayús+B** para compilar el proyecto.

### EJECUCIÓN LOCAL DE LA APLICACIÓN

Ejecute la aplicación para comprobar que funciona.

1.  Asegúrese de que **ProductsPortal** es el proyecto activo. Haga clic con el botón secundario en el nombre del proyecto en el **Explorador de soluciones** y seleccione **Set As Startup Project**.
2.  En **Visual Studio**, presione **F5**.
3.  La aplicación debería aparecer ejecutándose en un explorador:

    ![][22]

    ## <span class="short-header">IMPLEMENTACIÓN EN AZURE</span>PREPARACIÓN DE LA APLICACIÓN PARA QUE SE IMPLEMENTE EN AZURE

    Cualquier aplicación se puede implementar en un servicio en la nube de Azure o en un sitio web de Azure. Para obtener más información sobre la diferencia entre sitios web y servicios en la nube, consulte [Modelos de ejecución de Azure][]. Para obtener información sobre cómo implementar la aplicación en un sitio web de Azure, consulte [Implementación de una aplicación web ASP.NET en un sitio web de Azure][]. Esta sección contiene los pasos para implementar la aplicación en un servicio en la nube de Azure.

    Para implementar una aplicación en un servicio en la nube, va a agregar a la solución un proyecto de implementación de un proyecto de servicio en la nube.
    El proyecto de implementación contiene información
    de configuración necesaria para ejecutar correctamente la aplicación en
    la nube.

    1.  Para que la aplicación pueda implementarse en la nube, haga clic con el botón secundario en el proyecto **ProductsPortal** en el **Explorador de soluciones**, haga clic en **Convert** y, a continuación, en **Convert to Azure Cloud Service Project**.

        ![][23]

    2.  Para probar la aplicación, presione **F5**.
    3.  Al hacerlo, se iniciará el emulador de proceso de Azure. Dicho emulador utiliza el equipo local para emular la ejecución de la aplicación en Azure. Para confirmar que el emulador se ha iniciado, observe la bandeja del sistema:

        ![][24]

    4.  El explorador seguirá mostrando la ejecución local de la aplicación y tendrá el mismo aspecto y funcionamiento que tenía cuando se ejecutó antes como aplicación ASP.NET MVC 4 regular.

    ## <span class="short-header">COMBINACIÓN DE TODOS LOS COMPONENTES</span>COMBINACIÓN DE TODOS LOS COMPONENTES

    El siguiente paso es conectar el servidor de productos local con la aplicación ASP.NET MVC.

    1.  Si no está abierto, vuelva a abrir en Visual Studio el proyecto **ProductsPortal** que ha creado en la sección "Creación de una aplicación ASP.NET MVC".

    2.  Agregue el paquete NuGet a las referencias del proyecto de forma similar al paso de la sección "Creación de un servidor local". En el Explorador de soluciones, haga clic con el botón secundario en **References** y, a continuación, en **Manage NuGet Packages**.

    3.  Busque "WindowsAzure.ServiceBus" y seleccione el elemento **Bus de servicio de Microsoft Azure**. Después finalice la instalación y cierre este cuadro de diálogo.

    4.  En el Explorador de soluciones, haga clic con el botón secundario en el proyecto **ProductsPortal**
         y, a continuación, haga clic en **Add** y, finalmente, en **Existing Item**.

    5.  Desplácese al archivo **ProductsContract.cs** desde el proyecto de consola
        **ProductsServer**. Haga clic para resaltar ProductsContract.cs. Haga clic en la flecha abajo que hay junto a <strong>Add<strong> y, a continuación, en **Add as Link**.

        ![][25]

    6.  Ahora abra el archivo **HomeController.cs** en el editor de Visual Studio y sustituya la definición del espacio de nombres por el código siguiente. Asegúrese de sustituir *yourServiceNamespace* por el nombre
        de su espacio de nombres de servicio y *yourIssuerSecret* por su clave.
        Esto permitirá que el cliente llame al servicio local y que se
        devuelva el resultado de la llamada.

            namespace ProductsWeb.Controllers
            {
                using System.Linq;
                using System.ServiceModel;
                using System.Web.Mvc;
                using Microsoft.ServiceBus;
                using Models;
                using ProductsServer;

                public class HomeController : Controller
                {
                    // Declare the channel factory
                    static ChannelFactory<IProductsChannel> channelFactory;

                    static HomeController()
                    {
                        // Create shared secret token credentials for authentication 
                        channelFactory = new ChannelFactory<IProductsChannel>(new NetTcpRelayBinding(), 
                            "sb://yourServiceNamespace.servicebus.windows.net/products");
                        channelFactory.Endpoint.Behaviors.Add(new TransportClientEndpointBehavior { 
                            TokenProvider = TokenProvider.CreateSharedSecretTokenProvider(
                                "owner", "yourIssuerSecret") });
                    }

                    public ActionResult Index()
                    {
                        using (IProductsChannel channel = channelFactory.CreateChannel())
                        {
                            // Return a view of the products inventory
                            return this.View(from prod in channel.GetProducts()
                                             select
                                                 new Product { Id = prod.Id, Name = prod.Name, 
                                                     Quantity = prod.Quantity });
                        }
                    }
                }
            }

    7.  En el Explorador de soluciones, haga clic con el botón secundario en la solución **ProductsPortal**
         y, a continuación, haga clic en **Add** y, finalmente, en **Existing Project**.

    8.  Desplácese al proyecto **ProductsServer** y haga doble clic en la solución **ProductsServer.csproj** para agregarla.

    9.  En el Explorador de soluciones, haga clic con el botón secundario en la solución **ProductsPortal**
         y, a continuación, haga clic en **Properties**.

    10. A la izquierda, haga clic en **Startup Project**. A la derecha, haga clic en **Multiple startup projects**. Asegúrese de que **ProductsServer**, **ProductsPortal.Azure** y **ProductsPortal** aparezcan, en ese orden, con **Start** establecido como acción para **ProductsServer** y **ProductsPortal.Azure**, y **None** establecido como acción para **ProductsPortal**. Por ejemplo:

        ![][26]

    11. Aún en el cuadro de diálogo Properties, haga clic en **ProjectDependencies** a la izquierda.

    12. En la lista desplegable **Projects**, haga clic en
        **ProductsServer**. Asegúrese de que **ProductsPortal** está desactivado y que **ProductsPortal.Azure** está activado. A continuación, haga clic en **Aceptar**:

        ![][27]

    ## <span class="short-header">EJECUCIÓN DE LA APLICACIÓN</span>EJECUCIÓN DE LA APLICACIÓN

    1.  En el menú **Archivo** de Visual Studio, haga clic en **Guardar todo**.

    2.  Presione **F5** para compilar y ejecutar la aplicación. Primero debe iniciarse el servidor local (la aplicación de consola **ProductsServer**) y, a continuación, debe iniciarse la aplicación **ProductsWeb** en una ventana del explorador, tal como se muestra en la captura de pantalla siguiente. Esta vez verá que el inventario de productos muestra los datos recuperados del sistema local del servicio de productos.

        ![][1]

    ## <span class="short-header">IMPLEMENTACIÓN DE LA APLICACIÓN</span>IMPLEMENTACIÓN DE LA APLICACIÓN EN AZURE

    1.  Haga clic con el botón secundario en el proyecto **ProductsPortal** en el **Explorador de soluciones** y haga clic en **Publish to Azure**.

    2.  Para ver todas sus suscripciones, es posible que tenga que iniciar sesión.

        Haga clic en **Sign in to see more subscriptions**:

        ![][28]

    3.  Inicie sesión con su cuenta de Microsoft.

    4.  Haga clic en **Next**. Si la suscripción no contiene servicios hospedados, se le pedirá que cree uno. El servicio hospedado actúa como contenedor para su aplicación en su suscripción de Azure. Escriba un nombre que identifique su aplicación y elija la región para la que debe optimizarse la aplicación (cabe esperar que los usuarios que obtengan acceso a ella desde esta región tengan tiempos de carga inferiores).

        ![][29]

    5.  Seleccione el servicio hospedado en el que desea publicar la aplicación. En la configuración restante, conserve los valores predeterminados, como se muestra a continuación. Haga clic en **Next**:

        ![][30]

    6.  En la última página, haga clic en **Publish** para iniciar el proceso de implementación:

        ![][31]

        Esta operación durará aproximadamente 5-7 minutos. Al ser la primera vez que realiza una publicación, Azure aprovisiona una máquina virtual (VM), endurece la seguridad, crea un rol web en la VM para hospedar la aplicación, implementa el código en dicho rol web y, por último, configura el equilibrador de carga y las redes a fin de que la aplicación esté disponible para el público.

    7.  Mientras la publicación esté en curso podrá supervisar la actividad en la ventana **Registro de actividad de Azure**, que suele estar acoplada a la parte inferior de Visual Studio o Visual Web Developer:

        ![][32]

    8.  Cuando la implementación haya finalizado, puede ver el sitio web haciendo clic en el vínculo **Web site URL** de la ventana de supervisión.

        ![][1]

        El sitio web depende del servidor local, por lo que debe ejecutar la aplicación **ProductsServer** localmente para que el sitio web funcione correctamente. Al realizar solicitudes en el sitio web en la nube, verá que las solicitudes entran en su aplicación de consola local, tal como indica la salida de "GetProducts called" que se muestra en la captura de pantalla siguiente.

        ![][33]

Para obtener más información sobre la diferencia entre sitios web y servicios en la nube, consulte [Modelos de ejecución de Azure][].

## <span class="short-header">ELIMINACIÓN DE LA APLICACIÓN</span>DETENCIÓN Y ELIMINACIÓN DE UNA APLICACIÓN

Después de implementar su aplicación, puede deshabilitarla, por lo que puede compilar e implementar otras aplicaciones dentro de las 750 horas gratuitas al mes (31 días al mes) de tiempo del servidor.

Azure factura las instancias de rol web por hora consumida de tiempo de servidor. El tiempo de servidor se empieza a consumir una vez implementada
su aplicación, incluso si las instancias no se están ejecutando y se encuentran detenidas.
Una cuenta gratuita incluye 750 horas gratuitas al mes (31 días al mes)
de tiempo de servidor de la máquina virtual para hospedar estas instancias de rol web.

Los siguientes pasos muestran cómo detener y eliminar su aplicación.

1.  Inicie sesión en el [Portal de administración de Azure][], haga clic en Servicios en la nube y, a continuación, haga clic en el nombre del servicio.

2.  Haga clic en la pestaña **Panel** y, a continuación, en **Detener** para suspender temporalmente la aplicación. Para volver a iniciarla, haga clic en Iniciar. Haga clic en **Eliminar** para quitar la aplicación totalmente de Azure sin la posibilidad de restaurarla.

    ![][34]

## <a name="nextsteps"></a><span class="short-header">Pasos siguientes</span>Pasos siguientes

Para obtener más información sobre el bus de servicio, consulte los siguientes recursos:

-   [Service Bus][]
-   [Procedimientos del bus de servicio][]
-   [Utilización de las colas del bus de servicio][]

  [create-account-note]: ../includes/create-account-note.md
  []: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hybrid.png
  [1]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/App2.png
  [Obtención de herramientas y de SDK]: http://go.microsoft.com/fwlink/?LinkId=271920
  [2]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-41.png
  [3]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-3.png
  [4]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-4-2-WebPI.png
  [Portal de administración de Azure]: http://manage.windowsazure.com
  [5]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/sb-queues-03.png
  [6]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/sb-queues-04.png
  [7]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-multi-tier-27.png
  [8]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/sb-queues-09.png
  [9]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/sb-queues-06.png
  [aquí]: http://http://msdn.microsoft.com/en-us/library/windowsazure/ff687127.aspx
  [10]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/VSProperties.png
  [Usar el paquete NuGet de Bus de servicio]: http://go.microsoft.com/fwlink/?LinkId=234589
  [11]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hy-web-1.png
  [12]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hy-con-1.png
  [13]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hy-con-3.png
  [NuGet]: http://nuget.org
  [Install NuGet]: http://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c
  [14]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-multi-tier-13.png
  [15]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hy-con-4.png
  [16]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hy-web-2.png
  [17]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hy-web-4.png
  [18]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hy-web-7.jpg
  [19]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hy-web-10.jpg
  [20]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-multi-tier-40.png
  [21]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hy-web-11.png
  [22]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/App1.png
  [Modelos de ejecución de Azure]: http://www.windowsazure.com/en-us/develop/net/fundamentals/compute/
  [Implementación de una aplicación web ASP.NET en un sitio web de Azure]: http://www.windowsazure.com/en-us/develop/net/tutorials/get-started/
  [23]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-21.png
  [24]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-22.png
  [25]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hy-web-12.png
  [26]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hy-web-13.png
  [27]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hy-web-14.png
  [28]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-33.png
  [29]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-38.png
  [30]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-39.png
  [31]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-40.png
  [32]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-41.png
  [33]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hy-service1.png
  [34]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-43.png
  [Service Bus]: http://msdn.microsoft.com/en-us/library/windowsazure/ee732537.aspx
  [Procedimientos del bus de servicio]: /en-us/documentation/services/service-bus/
  [Utilización de las colas del bus de servicio]: /en-us/develop/net/how-to-guides/service-bus-queues/
