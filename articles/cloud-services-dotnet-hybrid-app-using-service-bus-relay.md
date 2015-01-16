<properties urlDisplayName="Hybrid Application" pageTitle="Aplicación híbrida en la nube/local (.NET) - Azure" metaKeywords="Azure Service Bus tutorial,hybrid .NET" description="Obtenga información acerca de cómo crear una aplicación híbrida en la nube o local de .NET con la retransmisión del Bus de servicio de Azure." metaCanonical="" services="service-bus" documentationCenter=".NET" title=".NET On-Premises/Cloud Hybrid Application Using Service Bus Relay" authors="sethm" solutions="" manager="timlt" editor="mattshel" />

<tags ms.service="service-bus" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="09/15/2014" ms.author="sethm" />






# Aplicación híbrida en la nube/local .NET mediante el relé del bus de servicio

<h2>INTRODUCCIÓN</h2>

Desarrollar aplicaciones híbridas en la nube con Azure es sencillo con
Visual Studio 2013 y el SDK de Azure para. NET gratuito. En esta guía
se asume que no tiene experiencia previa con Azure. En menos de
30 minutos, contará con una aplicación que usa varios recursos de Microsoft
Azure funcionando en la nube.

Aprenderá a:

-   Crear o adaptar un servicio web existente para su consumo
    por una solución web.
-   Usar la Retransmisión de bus de servicio de Azure para compartir datos entre
    una aplicación de Azure y un servicio web hospedado en otro lugar.

[WACOM.INCLUDE [create-account-note](../includes/create-account-note.md)]

### CÓMO AYUDA EL RELÉ DEL BUS DE SERVICIO CON SOLUCIONES HÍBRIDAS

Las soluciones de negocio por lo general están compuestas por una combinación de código
personalizado escrito para afrontar los nuevos y exclusivos requisitos empresariales, así como
la funcionalidad existente proporcionada por soluciones y sistemas que ya están
instalados.

Los arquitectos de soluciones están comenzando a utilizar la nube para abordar con más facilidad
los requisitos de escala y reducir los costes operativos. De esta manera, se dan cuenta
de que los activos de servicio existentes que les gustaría aprovechar como base de sus
soluciones se encuentran dentro del firewall corporativo y no resulta sencillo
para la solución en la nube el acceso a ellos. Muchos de los servicios internos no están
construidos ni hospedados de una forma que se puedan exponer fácilmente en los
servidores perimetrales de la red corporativa.

La *Retransmisión de bus de servicio* está diseñada para el caso de uso de tomar los servicios
web Windows Communication Foundation (WCF) existentes y permitir que se
pueda tener acceso de forma segura a soluciones que residan fuera del
perímetro corporativo, sin que sea necesario realizar cambios intrusivos en la
infraestructura de red corporativa. Esos servicios de retransmisión de bus de servicio se siguen hospedando
en su entorno existente, pero delegan la escucha de las
sesiones y solicitudes entrantes al bus de servicio hospedado en la nube. El
bus de servicio también protege dichos servicios contra el acceso no autorizado,
para lo que usa el control de acceso de Active Directory de Azure.

### EL ESCENARIO DE LA SOLUCIÓN

En este tutorial, creará un sitio web de ASP.NET MVC 4 que
permitirá ver una lista de productos en la página del inventario de productos.

![][0]

En este tutorial se asume que la información de los productos se encuentra en un
sistema local existente y se usa la retransmisión de bus de servicio para obtener acceso a
ese sistema. Esto lo simula un servicio web que se ejecuta en una aplicación de consola
simple y el respaldo lo proporciona un conjunto de productos en memoria. Dicha
aplicación de consola se puede ejecutar en el equipo propio y
el rol web se puede implementar en Azure. Al hacerlo, verá que el
rol web que se ejecuta en el centro de datos de Azure llamará a
su equipo, aunque casi seguro que este residirá al menos detrás
de un firewall y una capa de traducción de direcciones de red
(NAT).

Esta es una captura de pantalla de la página de inicio de la aplicación web finalizada.

![][1]

<h2>CONFIGURACIÓN DEL ENTORNO DE DESARROLLO</h2>

Antes de comenzar a desarrollar la aplicación de Azure, debe
obtener las herramientas y configurar el entorno de desarrollo.

1.  Para instalar el SDK de Azure para .NET, haga clic en el botón siguiente:

    [Obtención de herramientas y de SDK][]

2. 	Haga clic en **install the SDK**.

3. 	Elija el vínculo de la versión de Visual Studio que está utilizando. Para los pasos de este tutorial se usa Visual Studio 2013:

	![][42]

4.  Cuando se le solicite ejecutar o guardar **WindowsAzureSDKForNet.exe**, haga clic en
    **Ejecutar**:

    ![][2]

5.  En el instalador de plataforma web, haga clic en **Install** y continúe con la instalación:

    ![][3]

6.  Una vez completada la instalación, tendrá todo
    lo necesario para comenzar a desarrollar. El SDK incluye herramientas que le permitirán
    desarrollar fácilmente aplicaciones de Azure en Visual Studio. Si
    no tiene instalado Visual Studio, también instala la versión de
    Visual Studio Express.

<h2>CREACIÓN DE UN ESPACIO DE NOMBRES DE SERVICIO</h2>

Para comenzar a usar las características de bus de servicio en Azure, primero debe
crear un espacio de nombres de servicio. Un espacio de nombres de servicio proporciona un contenedor
con un ámbito para el desvío de recursos del bus de servicio en la aplicación. 

Los espacios de nombres y las entidades de mensajería del bus de servicio se pueden administrar a través del [Portal de administración de Azure][] o del Explorador de servidores de Visual Studio, pero los espacios de nombres solo se pueden crear desde el portal.

###Para crear un de espacio de nombres de servicio mediante el portal:

1.  Inicie sesión en el [Portal de administración de Azure][].

2.  En el panel de navegación izquierdo del Portal de administración, haga clic en
    **Bus de servicio**.

3.  En el panel inferior del Portal de administración, haga clic en **Crear**.   
    ![][5]

4.  En el cuadro de diálogo **Add a new namespace**, escriba un nombre de espacio de nombres.
    El sistema realiza la comprobación automáticamente para ver si el nombre está disponible.   
    ![][6]

5.  Después de asegurarse de que el espacio de nombres está disponible, elija el
    país o región donde se debe hospedar el espacio de nombres (asegúrese
    de usar el mismo país o región donde va a implementar sus
    recursos de proceso).

    IMPORTANTE: seleccione la **misma región** que vaya a seleccionar para
    implementación de la aplicación. Con esto conseguirá el máximo rendimiento.

6.	Haga clic en la marca de verificación. El sistema ahora crea el espacio
    de nombres del servicio y lo habilita. Es posible que tenga que esperar algunos minutos mientras el
    sistema realiza el aprovisionamiento de los recursos para la cuenta.

	![][38]

El espacio de nombres que creó aparecerá a continuación en el Portal de administración y
tardará un poco en activarse. Espere hasta que el estado sea **Active** antes de
continuar.

<h2>OBTENCIÓN DE CREDENCIALES DE ADMINISTRACIÓN PREDETERMINADAS PARA EL ESPACIO DE NOMBRES</h2>

Para realizar operaciones de administración (como la creación de una cola) en el
nuevo espacio de nombres, debe obtener las credenciales de administración para el
espacio de nombres.

1.  En la ventana principal, haga clic en el nombre del espacio de nombres de servicio.   

	![][39]
  

2.  Haga clic en **Connection Information**.   

	![][40]


3.  En el panel **Access connection information**, busque las entradas **Default Issuer** y **Default Key**.   
    

4.  Anote la clave o cópiela en el Portapapeles.

###Administración de espacios de nombres de servicio mediante el Explorador de servidores de Visual Studio:

Para administrar un espacio de nombres y obtener la información de conexión utilizando Visual Studio en vez del Portal de administración, siga el procedimiento descrito [aquí](http://http://msdn.microsoft.com/es-es/library/windowsazure/ff687127.aspx), en la sección titulada **Para conectarse a Azure desde Visual Studio**. Al iniciar sesión en Azure, el nodo **Bus de servicio** bajo el árbol **Microsoft Azure** del Explorador de servidores se rellena automáticamente con los espacios de nombre que ya haya creado. Haga clic con el botón secundario en cualquier espacio de nombres y, a continuación, haga clic en **Propiedades** para ver la cadena de conexión y otros metadatos asociados a este espacio de nombres en el panel **Propiedades** de Visual Studio. 

![][44]

Anote el valor de **SharedAccessKey** o cópielo en el Portapapeles.


<h2>CREACIÓN DE UN SERVIDOR LOCAL</h2>

En primer lugar, cree un sistema de catálogo de productos local (ficticio). Será
bastante simple; puede considerar que representa un sistema de catálogo
de productos local real con una superficie de servicio completa que
se intenta integrar.

Este proyecto comenzará como una aplicación de consola de Visual Studio. El
proyecto usa el paquete Service Bus NuGet para incluir las bibliotecas y
los ajustes de configuración del bus de servicio. La extensión NuGet Visual Studio
facilita la instalación y la actualización de las bibliotecas y las herramientas en Visual Studio
y Visual Studio Express. El paquete NuGet del bus de servicio es la forma más sencilla
de obtener la API del bus de servicio y configurar su aplicación con
todas las dependencias del bus de servicio. Para obtener más detalles acerca del uso del paquete NuGet y
del bus de servicio, consulte [Using the NuGet Service Bus Package][].

### CREACIÓN DEL PROYECTO

1.  Con privilegios de administrador, inicie Microsoft Visual
    Studio 2013 o Microsoft Visual Studio Express. Para
    iniciar Visual Studio con privilegios de administrador, haga clic con el botón secundario en
    **Microsoft Visual Studio 2013 (o Microsoft Visual Studio Express)** y, a continuación, haga clic en **Ejecutar como administrador**.
2.  En Visual Studio, en el menú **Archivo**, haga clic en **Nuevo** y, a continuación,
    en **Proyecto**.

    ![][10]

3.  En **Plantillas instaladas**, en **Visual C#**, haga clic en **Aplicación
    de consola**. En el cuadro **Nombres**, escriba el nombre
    **ProductsServer**:

    ![][11]

4.  Haga clic en **Aceptar** para crear el proyecto **ProductsServer**.

5.  En el **Explorador de soluciones**, haga clic con el botón secundario en **ProductsServer** y, a continuación,
    haga clic en **Propiedades**.
6.  Haga clic en la pestaña **Application** de la izquierda y asegúrese de que **.NET
    Framework 4** o **.NET Framework 4.5** aparecen en la lista desplegable **Target framework:** . Si no es así, selecciónelo en la lista desplegable y, a continuación, haga clic en **Yes**
    cuando se le solicite volver a cargar el proyecto.

    ![][12]

7.  Si ya ha instalado el administrador del paquete NuGet para Visual Studio, vaya al paso siguiente. De lo contrario, visite [NuGet][] y haga clic en [Install NuGet](http://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c). Siga las indicaciones para instalar el administrador del paquete NuGet y, a continuación, reinicie Visual Studio.

7.  En el **Explorador de soluciones**, haga clic con el botón secundario en **Referencias** y, a continuación, haga clic en
    **Administración de paquetes de NuGet**...
8.  En la columna de la izquierda del cuadro de diálogo de NuGet, haga clic en **Online**.

9. 	En la columna de la derecha, haga clic en el cuadro **Search**, escriba "**WindowsAzure**" y seleccione el elemento **Bus
    de servicio de Azure**. Haga clic en **Install** para completar la
    instalación y, a continuación, cierre este cuadro de diálogo.

    ![][13]

    Tenga en cuenta que ahora se hace referencia a los ensamblados del cliente requeridos.

9.  Agregue una clase nueva para el contrato de su producto. En el **Explorador de soluciones**,
    haga clic con el botón secundario en el proyecto **ProductsServer** y, a continuación, haga clic en **Agregar** y en
    **Class**.

    ![][14]

10. En el cuadro **Name**, escriba el nombre **ProductsContract.cs**. A continuación,
    haga clic en**Add**.
11. En **ProductsContract.cs**, reemplace la definición del espacio de nombres por
    el siguiente código, el que define el contrato del servicio:

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

12. En Program.cs, reemplace la definición del espacio de nombres por el siguiente
    código, el que agrega el servicio de perfil y su host:

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

13. En el **Explorador de soluciones**, haga doble clic en el archivo **app.config** para
    abrirlo en el editor de **Visual Studio**. Reemplace el contenido de
    **<system.ServiceModel>** por el siguiente código XML. Asegúrese de
    reemplazar *yourServiceNamespace* por su espacio de nombres de servicio
    y *yourIssuerSecret* por la clave que recuperó anteriormente
    del Portal de administración de Azure:

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

14. Presione **F6**, o bien en el menú **Build**, haga clic en **Build Solution** para compilar la aplicación para comprobar la precisión del trabajo realizado hasta el momento.

<h2>CREACIÓN DE UNA APLICACIÓN ASP.NET MVC</h2>

En esta sección se creará una aplicación ASP.NET simple que mostrará
los datos recuperados del servicio de producto.

### CREACIÓN DEL PROYECTO

1.  Asegúrese de que Microsoft Visual Studio 2013 se ejecuta con privilegios de administrador. Si no es así, para
    iniciar Visual Studio con privilegios de administrador, haga clic con el botón secundario en
    **Microsoft Visual Studio 2013 (o Microsoft Visual Studio Express)** y, a continuación, haga clic en **Ejecutar como administrador**. El emulador
    de proceso de Azure, que se describirá más adelante en esta guía, requiere que
    se inicie Visual Studio con privilegios de administrador.

2.  En Visual Studio, en el menú **Archivo**, haga clic en **Nuevo** y, a continuación,
    en **Proyecto**.

3.  En **Installed Templates**, en **Visual C#**, haga clic en **ASP.NET Web Application**. Asigne al proyecto el nombre **ProductsPortal**. A continuación,
    haga clic en **Aceptar**.

    ![][15]

4.  En la lista **Select a template**, haga clic en **MVC** y,
    a continuación, en **Aceptar**.

    ![][16]

5.  En el **Explorador de soluciones**, haga clic con el botón secundario en **Modelos** y, a continuación, haga clic en **Agregar**,
    y luego en **Clase**. En el cuadro **Nombres**, escriba el nombre
    **Product.cs**. A continuación, haga clic en **Agregar**.

    ![][17]

### MODIFICACIÓN DE LA APLICACIÓN WEB

1.  En el archivo Product.cs en Visual Studio, reemplace la definición de
    espacio de nombres existente por el siguiente código:

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

2.  In the HomeController.cs file in Visual Studio, replace the existing
    namespace definition with the following code:

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

3.  En el **Explorador de soluciones**, expanda Views\Shared:

    ![][18]

4.  A continuación, haga doble clic en _Layout.cshtml para abrirlo en el editor de Visual Studio.

5.  Cambie todas las ocurrencias de **My ASP.NET Application** por **LITWARE's Products**.

6. Quite los vínculos **Home**, **About** y **Contact**. Elimine el código resaltado:

	![][41]

7.  En el **Explorador de soluciones**, expanda Views\Home:

    ![][20]

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

1.  Asegúrese de que **ProductsPortal** es el proyecto activo. Haga clic con el botón secundario en
    el nombre del proyecto en el **Explorador de soluciones** y seleccione **Set As
    Startup Project**
2.  En **Visual Studio**, presione **F5**.
3.  La aplicación debería aparecer ejecutándose en un explorador:

    ![][21]

    <h2>PREPARACIÓN DE LA APLICACIÓN PARA QUE SE IMPLEMENTE EN AZURE</h2>

    Cualquier aplicación se puede implementar en un servicio en la nube de Azure o en un sitio web de Azure. Para obtener más información sobre la diferencia entre sitios web y servicios en la nube, consulte [Modelos de ejecución de Azure][executionmodels]. Para obtener información sobre cómo implementar la aplicación en un sitio web de Azure, consulte [Implementación de una aplicación web ASP.NET en un sitio web de Azure](http://www.windowsazure.com/es-es/develop/net/tutorials/get-started/). Esta sección contiene los pasos detallados para implementar la aplicación en un servicio en la nube de Azure.

    Para implementar una aplicación en un servicio en la nube, va a agregar a la solución un proyecto de implementación de un proyecto de servicio en la nube.
    El proyecto de implementación contiene información de
    configuración necesaria para ejecutar correctamente la aplicación en la
    nube.

    1.  Para que la aplicación pueda implementarse en la nube, haga clic con el botón secundario
        en el proyecto **ProductsPortal** en el **Explorador de soluciones** y, a continuación,
        haga clic en **Convert** y en **Convert to Azure Cloud Service Project**.

        ![][22]

    2.  Para probar la aplicación, presione **F5**.
    3.  Al hacerlo, se iniciará el emulador de proceso de Azure. Dicho
        emulador usa el equipo local para emular la ejecución de la aplicación
        en Azure. Para confirmar que el emulador se ha iniciado,
        observe la bandeja del sistema:

        ![][23]

    4.  El explorador seguirá mostrando la ejecución local de la aplicación
        y tendrá el mismo aspecto y funcionamiento que tenía cuando se ejecutó
        antes como aplicación ASP.NET MVC4 regular.

    <h2>COMBINACIÓN DE TODOS LOS COMPONENTES</h2>

    El siguiente paso es conectar el servidor de productos local con la
    aplicación ASP.NET MVC.

    1.  Si todavía no está abierto, en Visual Studio vuelva a abrir el proyecto
        **ProductsPortal** que creó en la sección "Creación de una
        aplicación ASP.NET MVC".

    2.  Agregue el paquete NuGet a las referencias del proyecto de forma
        similar al paso de la sección "Creación de un servidor local". En el
        Explorador de soluciones, haga clic con el botón secundario en **Referencias** y, a continuación, en
        **Administración de paquetes de NuGet**.

    3.  Busque "WindowsAzure.ServiceBus" y seleccione el elemento **Bus de
        servicio de Azure**. Después finalice la instalación y
        cierre este cuadro de diálogo.

    4.  En el Explorador de soluciones, haga clic con el botón secundario en la solución **ProductsPortal**
         y, a continuación, haga clic en **Add** y luego en **Existing Item**.

    5.  Vaya al archivo **ProductsContract.cs** desde el proyecto de consola
        **ProductsServer**. Haga clic para resaltar
        ProductsContract.cs. Haga clic en la flecha abajo que hay junto a **Add** y, a continuación,
        haga clic en **Add as Link**.

        ![][24]

    6.  Ahora abra el archivo **HomeController.cs** en el editor de Visual Studio
        y reemplace la definición de espacios de nombres con el siguiente
        código. Asegúrese de reemplazar *yourServiceNamespace* por el nombre de
        su espacio de nombres de servicio y *yourIssuerSecret* por su clave.
        Esto le permitirá que el cliente llame al servicio local
        que se devuelva el resultado de la llamada.

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
        , haga clic en **Add** y, a continuación, haga clic en un **proyecto existente**.

    8.  Vaya al proyecto **ProductsServer** y haga doble clic en
        el archivo de solución **ProductsServer.csproj** para agregarlo.

    9.  En el Explorador de soluciones, haga clic con el botón secundario en la solución **ProductsPortal**
        y haga clic en **Propiedades**.

    10. A la izquierda, haga clic en **Startup Project**. A la
        derecha, haga clic en **Multiple startup projects**. Asegúrese de que
        **ProductsServer**, **ProductsPortal.Azure** y
        **ProductsPortal** aparecen, en ese orden, con **Start** establecido como
        la acción para **ProductsServer** y **ProductsPortal.Azure**,
        y **None** establecido como la acción para **ProductsPortal**. Por
        ejemplo:

        ![][25]

    11. Aún en el cuadro de diálogo Properties, haga clic en **ProjectDependencies** a
        la izquierda.

    12. En la lista desplegable **Projects**, haga clic en
        **ProductsServer**. Asegúrese de que **ProductsPortal** está desactivado
        y que **ProductsPortal.Azure** está activado. A continuación, haga clic en **Aceptar**:

        ![][26]

    <h2>EJECUCIÓN DE LA APLICACIÓN</h2>

    1.  En el menú **Archivo** de Visual Studio, haga clic en **Guardar todo**.

    2.  Presione **F5** para compilar y ejecutar la aplicación. Primero debe iniciarse el
        servidor local(la aplicación de consola **ProductsServer** y, a continuación,
        debe iniciarse la aplicación **ProductsWeb** en una ventana del explorador,
        tal como se muestra en la captura de pantalla siguiente. Esta vez verá
        que el inventario de productos muestra los datos recuperados del
        sistema local del servicio de productos.

        ![][1]

    <h2>IMPLEMENTACIÓN DE LA APLICACIÓN EN AZURE</h2>

    1.  Haga doble clic en el proyecto **ProductsPortal** en el **Explorador
        de soluciones** haga clic en **Publicación en Azure**.

    2.  Para ver todas sus suscripciones, es posible que tenga que iniciar sesión.

        Haga clic en **Sign in to see more subscriptions**:

        ![][27]

    3.  Inicie sesión con su cuenta de Microsoft.


    8.  Haga clic en **Next**. Si la suscripción no contiene todavía servicios
        hospedado, se le pedirá que cree uno. El servicio hospedado
        actúa como contenedor para su aplicación en la suscripción de
        Azure. Escriba un nombre que identifique su
        aplicación y elija la región para la que debe optimizarse
        la aplicación. (cabe esperar que los usuarios que obtengan acceso
        a ella desde esta región tengan tiempos de carga inferiores).

        ![][32]

    9.  Seleccione el servicio hospedado en el que desea publicar la
        aplicación. En la configuración restante, conserve los
        valores predeterminados, como se muestra a continuación. Haga clic en **Next**:

        ![][33]

    10. En la última página, haga clic en **Publish** para comenzar el proceso
        de implementación:

        ![][34]

        Esta operación durará aproximadamente cinco a siete minutos. Como esta es la
        primera vez que realiza una publicación, Azure aprovisiona a una
        máquina virtual (VM), endurece la seguridad, crea un rol
        web en la VM para hospedar la aplicación, implementa el código en dicho
        rol web y, por último, configura el equilibrador de carga y las
        redes de fin de año a fin de que la aplicación esté disponible para el público.

    11. Mientras la publicación esté en curso podrá supervisar la
        actividad en la ventana **Registro de actividad de Azure**, que suele estar
        acoplada a la parte inferior de Visual Studio o Visual Web
        Developer:

        ![][35]

    12. Cuando la implementación haya finalizado, puede ver el sitio web
        haciendo clic en el vínculo **Website URL** de la ventana de supervisión.

        ![][36]

        El sitio web depende del servidor local, por lo que debe
        ejecutar la aplicación **ProductsServer** localmente para que el
        sitio web funcione correctamente. Al realizar solicitudes en el sitio web
        en la nube, verá que las solicitudes entran en su aplicación de consola
        local, tal como lo indica la salida de "GetProducts called"
        que se muestra en la captura de pantalla siguiente.

        ![][37]

Para obtener más información sobre la diferencia entre sitios web y servicios en la nube, consulte [Modelos de ejecución de Azure][executionmodels].

<h2>DETENCIÓN Y ELIMINACIÓN DE UNA APLICACIÓN</h2>

Después de implementar su aplicación, es posible que desee deshabilitarla
para así poder compilar e implementar otras aplicaciones dentro de las 750
horas gratuitas al mes (31 días al mes) de tiempo del servidor.

Azure factura las instancias de rol web por hora consumida de tiempo de
servidor. El tiempo de servidor se empieza a consumir una vez implementada la aplicación,
incluso si las instancias no se están ejecutando y se encuentran detenidas.
Una cuenta gratuita incluye 750 horas gratuitas al mes (31 días al mes) de tiempo de servidor
de la máquina virtual dedicada para hospedar estas instancias de rol web.

Los siguientes pasos le muestran cómo detener y eliminar una
aplicación.

1.  Inicie sesión en el [Portal de administración de Azure],
        haga clic en Servicios en la nube y, a continuación, haga clic en el nombre del servicio.

2.  Haga clic en la pestaña **Panel** y, a continuación, en **Detener** para suspender temporalmente la aplicación. Para volver a iniciarla,
        haga clic en Iniciar. Haga clic en **Eliminar** para eliminar la aplicación totalmente de Azure
        sin la posibilidad de restaurarla.

	![][43]

<h2><a name="nextsteps"></a>Pasos siguientes</h2>  

Para obtener más información sobre el bus de servicio, consulte los siguientes recursos:  
  
* [Bus de servicio de Azure][sbmsdn]  
* [Procedimientos del bus de servicio][sbwacom]  
* [Utilización de las colas del bus de servicio][sbwacomqhowto]  


  [0]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hybrid.png
  [1]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/App2.png
  [Obtención de herramientas y de SDK]: http://go.microsoft.com/fwlink/?LinkId=271920
  [NuGet]: http://nuget.org
  [2]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-3.png
  [3]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-4-2-WebPI.png
  
  
  [Portal de administración de Azure]: http://manage.windowsazure.com
  [5]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/sb-queues-03.png
  [6]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/sb-queues-04.png
  
  
  
  [Using the NuGet Service Bus Package]: http://go.microsoft.com/fwlink/?LinkId=234589
  [10]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hy-web-1.png
  [11]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hy-con-1.png
  [12]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hy-con-3.png
  [13]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-multi-tier-13.png
  [14]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hy-con-4.png
  [15]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hy-web-2.png
  [16]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hy-web-4.png
  [17]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hy-web-7.jpg
  [18]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hy-web-10.jpg
  
  [20]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hy-web-11.png
  [21]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/App1.png
  [22]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-21.png
  [23]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-22.png
  [24]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hy-web-12.png
  [25]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hy-web-13.png
  [26]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hy-web-14.png
  [27]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-33.png
  
  
  [30]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-36.png
  [31]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-37.png
  [32]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-38.png
  [33]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-39.png
  [34]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-40.png
  [35]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-41.png
  [36]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/App2.png
  [37]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hy-service1.png
  [38]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-multi-tier-27.png
  [39]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/sb-queues-09.png
  [40]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/sb-queues-06.png
  [41]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-multi-tier-40.png
  [42]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-41.png
  [43]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-43.png
  [44]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/VSProperties.png

  [sbmsdn]: http://msdn.microsoft.com/es-es/library/windowsazure/ee732537.aspx  
  [sbwacom]: /es-es/documentation/services/service-bus/  
  [sbwacomqhowto]: /es-es/develop/net/how-to-guides/service-bus-queues/
  [executionmodels]: http://www.windowsazure.com/es-es/develop/net/fundamentals/compute/
