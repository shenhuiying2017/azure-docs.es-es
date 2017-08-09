---
title: "Creación de una aplicación .NET para Service Fabric | Microsoft Docs"
description: "Obtenga información sobre cómo crear una aplicación con un front-end de ASP.NET Core y un back-end de servicio de confianza con estado e implementar la aplicación en un clúster."
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/12/2017
ms.author: ryanwi, mikhegn
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 8f824e451abd2ca87875c416184e4d7265e8c72b
ms.contentlocale: es-es
ms.lasthandoff: 07/21/2017

---

# <a name="create-and-deploy-an-application-with-an-aspnet-core-web-api-front-end-service-and-a-stateful-back-end-service"></a>Creación e implementación de una aplicación con un servicio de front-end de ASP.NET Core Web API y un servicio back-end con estado
En este tutorial se muestra cómo crear una aplicación de Azure Service Fabric con un front-end de ASP.NET Core Web API y un servicio back-end con estado para almacenar los datos.

![Diagrama de aplicaciones](./media/service-fabric-tutorial-create-dotnet-app/application-diagram.png)

En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Crear un servicio de ASP.NET Core Web API como un servicio de confianza
> * Crear un servicio de confianza con estado
> * Implementar la comunicación remota de servicio y usar un proxy de servicio

El tutorial se divide en tres artículos; este artículo es el primero de la serie.

## <a name="prerequisites"></a>Requisitos previos
Antes de empezar este tutorial:
- Si no tiene ninguna suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Instale Visual Studio 2017](https://www.visualstudio.com/) y las cargas de trabajo de **desarrollo de Azure** y de **desarrollo web y de ASP.NET**.
- [Instale el SDK de Service Fabric](service-fabric-get-started.md).

## <a name="create-an-aspnet-web-api-service-as-a-reliable-service"></a>Creación de un servicio de ASP.NET Web API como un servicio de confianza
ASP.NET Core es un marco de desarrollo web ligero multiplataforma, que puede usar para crear modernas interfaces de usuario web y API web. Para obtener una descripción completa de cómo se integra ASP.NET Core con Service Fabric, se recomienda fehacientemente leer el artículo [ASP.NET Core en Reliable Services de Service Fabric](service-fabric-reliable-services-communication-aspnetcore.md). De momento, puede seguir este tutorial para empezar a trabajar rápidamente. Para más información sobre ASP.NET Core, vea [Documentación de ASP.NET Core](https://docs.microsoft.com/aspnet/core/).

> [!NOTE]
> Este tutorial se basa en las [herramientas de ASP.NET Core para Visual Studio de 2017](https://docs.microsoft.com/aspnet/core/tutorials/first-mvc-app/start-mvc). Las herramientas de .NET Core para Visual Studio 2015 ya no se van a actualizar.

1. Inicie Visual Studio como **administrador**.

2. Cree un proyecto con **Archivo**->**Nuevo**->**Proyecto**.

3. En el cuadro de diálogo **Nuevo proyecto**, elija **Nube > Aplicación de Service Fabric**.

4. Asigne el nombre **MyApplication** a la aplicación y presione **Aceptar**.

   ![Cuadro de diálogo de proyecto nuevo en Visual Studio.](./media/service-fabric-tutorial-create-dotnet-app/new-project-dialog.png)

5. En la página **Nuevo servicio de Service Fabric**, elija **ASP.NET Core sin estado** y asigne el nombre de **MyWebAPIFrontEnd** a su servicio.
   
   ![Selección del servicio web ASP.NET en el cuadro de diálogo de nuevo servicio](./media/service-fabric-tutorial-create-dotnet-app/new-project-dialog-2.png) 

6. En la página siguiente se proporciona un conjunto de plantillas de proyecto ASP.NET Core. Para este tutorial, elija **API Web**. Sin embargo, puede aplicar los mismos conceptos a la creación de una aplicación web completa.
   
   ![Elección del tipo de proyecto ASP.NET](./media/service-fabric-tutorial-create-dotnet-app/vs-new-aspnet-project-dialog.png)

   Visual Studio crea una aplicación y un proyecto de servicio, y los muestra en el Explorador de soluciones.

   ![Explorador de soluciones después de la creación de una aplicación con el servicio ASP.NET Core Web API]( ./media/service-fabric-tutorial-create-dotnet-app/solution-explorer-aspnetcore-service.png)

### <a name="deploy-and-debug-the-application-locally"></a>Implementación y depuración local de la aplicación
Ahora puede continuar y depurar la aplicación; después, examine el comportamiento predeterminado que proporciona la plantilla de ASP.NET Core Web API.

Presione `F5` en Visual Studio para implementar la aplicación, con el fin de depurarla. `F5` produce un error si previamente no ha abierto Visual Studio como **administrador**.

> [!NOTE]
> La primera vez que ejecute e implemente la aplicación localmente, Visual Studio creará un clúster local para la depuración, lo que puede tardar algún tiempo. El estado de creación del clúster se muestra en la ventana de salida de Visual Studio.

Cuando el clúster esté listo, recibirá una notificación de la aplicación de administración de clúster local de Service Fabric en la bandeja del sistema.

1. Para depurar la aplicación, presione F5 en Visual Studio.
2. Cuando se complete la implementación, Visual Studio iniciará el explorador en la raíz del servicio ASP.NET Web API. La plantilla de ASP.NET Core Web API no proporciona comportamiento predeterminado para la raíz, por lo que el explorador muestra un error.
3. Agregue `/api/values` a la dirección URL del explorador. Esta solicitud invoca el método `Get` de la clase ValuesController de la plantilla de Web API. devuelve la respuesta predeterminada de la plantilla, una matriz JSON con dos cadenas:
   
![Valores predeterminados devueltos desde la plantilla API web de ASP.NET Core](./media/service-fabric-tutorial-create-dotnet-app/browser-aspnet-template-values.png)

> [!NOTE]
> Para cambiar el comportamiento de Visual Studio 2017 de forma predeterminada al depurar una aplicación, puede cambiar las propiedades del proyecto de aplicación de Service Fabric **MyApplication**.
> Para este tutorial, si se establece el modo de depuración de aplicaciones en **Actualizar aplicación** y se agrega **'/api/values'** a la propiedad de la dirección URL de la aplicación, se optimiza la experiencia de depuración.
> 

Para detener la depuración de la aplicación, vuelva a Visual Studio y presione **Mayús+F5**.

### <a name="understanding-the-service-fabric-application-and-service"></a>Descripción de la aplicación y servicio de Service Fabric
La solución de Visual Studio contiene ahora dos proyectos.
1. Proyecto de la aplicación de Service Fabric - **MyApplication**
    - Este proyecto no contiene código directamente. En su lugar, hace referencia a un conjunto de proyectos de servicio. Además, contiene otros tipos de contenido para especificar cómo se compone e implementa la aplicación.
2. Proyecto de servicio - **MyWebAPIFrontEnd**
    - Este proyecto es el proyecto de ASP.NET Core Web API, que contiene el código y configuración para el servicio. Si se examina el archivo de código ValuesController.cs en la carpeta Controllers, verá que es un controlador de ASP.NET Core Web API normal. No hay ningún requisito específico para el código que se escribe como parte de los controladores, cuando se ejecuta ASP.NET Core Web API como un servicio de confianza de Service Fabric.

Para más información sobre el modelo de aplicación de Service Fabric, consulte [Modelar una aplicación en Service Fabric](service-fabric-application-model.md).

Para más información sobre el contenido del proyecto de servicio, consulte [Introducción a los servicios de confianza](service-fabric-reliable-services-quick-start.md).

## <a name="add-a-stateful-back-end-service-to-your-application"></a>Adición de un servicio back-end con estado a la aplicación
Ahora que tenemos un servicio de ASP.NET Web API en ejecución en nuestra aplicación, sigamos adelante y agreguemos un servicio de confianza con estado para almacenar algunos datos en nuestra aplicación.

Service Fabric permite almacenar de forma coherente y confiable su estado justo dentro de su servicio mediante Reliable Collections. Reliable Collections es un conjunto sencillo de clases de colecciones de alta disponibilidad y confiabilidad que le resultará familiar a cualquiera que haya usado colecciones de C#.

En este tutorial creará un servicio, que almacena un valor de contador en una colección de confianza.

1. En el Explorador de soluciones, haga clic con el botón derecho en **Servicios**, en el proyecto de aplicación y seleccione **Agregar > Nuevo servicio de Service Fabric**.
   
    ![Incorporación de un nuevo servicio a una aplicación existente](./media/service-fabric-tutorial-create-dotnet-app/vs-add-new-service.png)

2. En el cuadro de diálogo **Nuevo servicio de Service Fabric**, elija **Servicio con estado** y asigne el nombre de **MyStatefulService** al servicio y presione **Aceptar**.

    ![Cuadro de diálogo de servicio nuevo en Visual Studio.](./media/service-fabric-tutorial-create-dotnet-app/add-stateful-service.png)

    Una vez creado el proyecto de servicio, tendrá dos servicios en la aplicación. Mientras continúa la creación de la aplicación, puede agregar más servicios de la misma forma. Cada uno puede tener versiones y actualizaciones independientes.

### <a name="deploy-and-debug-the-application-locally"></a>Implementación y depuración local de la aplicación
Con el nuevo servicio agregado a la aplicación, vamos a depurar la aplicación completa y a examinar el comportamiento predeterminado del nuevo servicio.

Para depurar la aplicación, presione F5 en Visual Studio.

> [!NOTE]
> Si opta por usar **Actualizar aplicación** como modo de depuración de aplicaciones, se le pedirá que conceda el acceso del clúster de Service Fabric local a la carpeta de salida de compilación de la aplicación.
> 

Ambos servicios de la aplicación se compilan, implementan y se establece su estado en el clúster de Service Fabric local. Cuando se inician los servicios, Visual Studio sigue iniciando el explorador, pero también ofrece automáticamente el **visor de eventos de diagnóstico**, donde se pueden ver los resultados del seguimiento del servicio.
   
![Visor de eventos de diagnóstico](./media/service-fabric-tutorial-create-dotnet-app/diagnostic-events-viewer.png)

El visor de eventos de diagnóstico muestra los mensajes de seguimiento de todos los servicios que forman parte de la solución de Visual Studio que se está depurando. Al hacer una pausa en el visor de eventos de diagnóstico, podrá expandir uno de los mensajes de servicio para inspeccionar sus propiedades. De esta forma, podrá ver qué servicio del clúster ha generado el mensaje, qué nodo que de esa instancia de servicio está en ejecución en y cualquier otra información.

![Visor de eventos de diagnóstico](./media/service-fabric-tutorial-create-dotnet-app/expanded-diagnostics-viewer.png)

Verá que los mensajes del servicio proceden del servicio con estado que se ha creado, ya que **serviceTypeName** es **MyStatefulServiceType**. También puede ver que se están enviando mensajes con el texto "El contador actual es...". Este mensaje lo genera esta línea de código resaltada en el método `RunAsync` de **MyStatefulService.cs**, que se ve en la captura de pantalla siguiente.

![Código de servicio de mensajes](./media/service-fabric-tutorial-create-dotnet-app/service-message-code.png)

Para más información acerca de la emisión de información de diagnóstico desde los servicios y aplicaciones, consulte [Supervisión y diagnóstico para Azure Service Fabric](service-fabric-diagnostics-overview.md).

Para detener la depuración de la aplicación, vuelva a Visual Studio y presione **Mayús+F5**.

### <a name="understanding-the-mystatefulservice-code"></a>Descripción del código de MyStatefulService
Para entender cómo se usa un diccionario de confianza para almacenar datos en el clúster, dediquemos un poco de tiempo a examinar el código del servicio **MyStatefulService**.

Hay cinco líneas de código del servicio que están relacionadas con la creación, actualización y lectura del diccionario de confianza.

![Código de diccionario de confianza](./media/service-fabric-tutorial-create-dotnet-app/reliable-dictionary-code.png)

1. Cada vez que el método `RunAsync` del servicio se ejecuta (lo que sucede cuando se inicia el servicio), esta línea de código obtiene o agrega al servicio un diccionario de confianza con el nombre `myDictionary`.
2. Todas las interacciones con valores de un diccionario de confianza requieren una transacción. Esta instrucción using introducida en esta línea de código crea dicha transacción.
3. Esta línea de código obtiene el valor asociado a la clave especificada en la llamada al método, por ejemplo, `Counter`.
4. Esta línea de código actualiza el valor asociado a la clave `Counter`, al incrementar el valor.
5. Esta llamada al método confirma la transacción y se devuelve cuando el valor actualizado se almacena en un cuórum de nodos en el clúster.

Para más información sobre los diccionarios de confianza y Reliable Collections, consulte [Introducción a Reliable Collections en los servicios con estado de Azure Service Fabric](service-fabric-reliable-services-reliable-collections.md).

## <a name="connect-the-services"></a>Conexión de los servicios
En este paso se van a conectar los dos servicios y hacer que Web API de front-end devuelva el valor actual del diccionario de servicios de confianza de back-end.

Service Fabric proporciona una flexibilidad completa en el modo de comunicación con los servicios confiables. Dentro de una única aplicación, es posible que tenga servicios que sean accesibles a través de TCP. Puede haber también otros servicios que podrían estar accesibles a través de una API de REST de HTTP e incluso otros servicios que pueden estar accesibles a través de sockets web. Para más información sobre las opciones disponibles y sus inconvenientes, consulte [Conexión y comunicación con servicios en Service Fabric](service-fabric-connect-and-communicate-with-services.md).

En este tutorial, estamos utilizando la [comunicación remota de servicio con servicios de confianza](service-fabric-reliable-services-communication-remoting.md).

En el enfoque de comunicación remota de servicios (basado en llamadas a procedimientos remotos o RPC), se define una interfaz que actúa como el contrato público del servicio. Luego, se usa esa interfaz para generar una clase de proxy para interactuar con el servicio.

### <a name="create-the-remoting-interface"></a>Creación de la interfaz remota
Comenzamos mediante la creación de la interfaz para que actúe como contrato entre los dos servicios. La interfaz debe de hacerse referencia por todos los servicios que la usan, por lo que la creamos en un proyecto independiente de biblioteca de clases.

1. En el Explorador de soluciones, haga clic con el botón derecho en la solución y seleccione **Agregue** > **Nuevo proyecto**.
2. Elija la entrada **Visual C#** en el panel de navegación izquierdo y, después, seleccione la plantilla **Biblioteca de clases (.NET Framework)**. Asegúrese de que el valor de la versión de .NET Framework es **4.5.2** o superior.
   
    ![Creación de un proyecto de interfaz para el servicio con estado](./media/service-fabric-tutorial-create-dotnet-app/vs-add-class-library-project.png)

3. Llame a la biblioteca de clases **MyStatefulService.Interface** y haga clic en **Aceptar**.

4. En el Explorador de soluciones, haga clic con el botón derecho en la solución y elija **Administrar paquetes NuGet para la solución...**.

5. Elija **Examinar** y busque **Microsoft.ServiceFabric.Services.Remoting**. Elija la opción de instalación para los tres proyectos de servicio en la solución: 
   
    ![Incorporación del paquete de NuGet de servicios](./media/service-fabric-tutorial-create-dotnet-app/add-nuget.png)

6. En la biblioteca de clases, cree una interfaz con un único método, `GetCountAsync` y extienda la interfaz de `Microsoft.ServiceFabric.Services.Remoting.IService`. La interfaz de comunicación remota debe derivar de esta interfaz para indicar que es una interfaz de comunicación remota de servicio. 
   
    ```c#
    using Microsoft.ServiceFabric.Services.Remoting;  
    using System.Threading.Tasks;
    ...
    namespace MyStatefulService.Interface
    {           
        public interface ICounter: IService
        {
            Task<long> GetCountAsync();
        }
    }
    ```
7. Haga clic con el botón derecho en el proyecto **MyStatefulService.Interface** en el Explorador de soluciones y seleccione **Propiedades**. Seleccione la pestaña **Compilar** y, después, seleccione el valor **x64** en el menú desplegable **Destino de la plataforma**. 

8. Guarde todos los cambios.

### <a name="implement-the-interface-in-your-stateful-service"></a>Implementación de la interfaz en el servicio con estado
Ahora que hemos definido la interfaz, tenemos que implementarla en el servicio con estado.

1. Agregue una referencia al proyecto de biblioteca de clases que contiene la interfaz desde el proyecto **MyStatefulService**.
   
    ![Incorporación de una referencia al proyecto de biblioteca de clases en el servicio con estado](./media/service-fabric-tutorial-create-dotnet-app/vs-add-class-library-reference.png)

    ![Incorporación de una referencia al proyecto de biblioteca de clases en el servicio con estado](./media/service-fabric-tutorial-create-dotnet-app/vs-add-class-library-reference-2.png)

2. Abra el archivo `MyStatefulService.cs` y extiéndalo para implementar la interfaz `ICounter`, que hemos creado.
   
    ```c#
    using MyStatefulService.Interface;
    ...
   
    public class MyStatefulService : StatefulService, ICounter
    {        
          // ...
    }
    ```

3. Ahora, implemente el método único que se define en la interfaz `ICounter`, `GetCountAsync`.
   
    ```c#
    public async Task<long> GetCountAsync()
    {
        var myDictionary =
          await this.StateManager.GetOrAddAsync<IReliableDictionary<string, long>>("myDictionary");
   
        using (var tx = this.StateManager.CreateTransaction())
        {          
            var result = await myDictionary.TryGetValueAsync(tx, "Counter");
            return result.HasValue ? result.Value : 0;
        }
    }
    ```

    - Este método devuelve el valor almacenado de la clave `Counter` en el diccionario de confianza denominado `myDictionary`. 

### <a name="expose-the-stateful-service-using-service-remoting"></a>Exposición del servicio con estado mediante la comunicación remota del servicio
Con la interfaz `ICounter` implementada, el paso final consiste en abrir el punto de conexión de la comunicación remota de servicio. Para los servicios con estado, Service Fabric proporciona un método reemplazable denominado " `CreateServiceReplicaListeners`". Con este método, puede especificar uno o varios agentes de escucha de comunicación, según el tipo de comunicación que desee habilitar para el servicio.

En este caso, reemplazamos el método `CreateServiceReplicaListeners` actual e incluimos una instancia de `ServiceRemotingListener`, que crea un punto de conexión RPC al que los clientes pueden llamar usando `ServiceProxy`.  

Cambie el método **CreateServiceReplicaListeners** en el archivo **MyStatefulService.cs** y agregue una instrucción using al espacio de nombres `Microsoft.ServiceFabric.Services.Remoting.Runtime`.

```c#
using Microsoft.ServiceFabric.Services.Remoting.Runtime;

...

protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
{
    return new List<ServiceReplicaListener>()
    {
        new ServiceReplicaListener(
            (context) =>
                this.CreateServiceRemotingListener(context))
    };
}
```

Nuestro servicio con estado está ahora preparado para recibir tráfico de otros servicios a través de RPC, mediante la comunicación remota del servicio.

### <a name="call-the-stateful-back-end-service-from-the-front-end-service"></a>Llame al servicio de back-end con estado desde el servicio front-end
Ahora que nuestro servicio back-end ha expuesto una interfaz, todo lo que sigue es agregar el código para comunicarse con él desde el servicio ASP.NET Web API. Para comunicarse de manera remota con el servicio, estamos usando un proxy de servicio desde nuestro controlador Values.

1. En el Explorador de soluciones, expanda **MyWebAPIFrontEnd**, haga clic con el botón derecho en **Dependencias** y seleccione **Agregar referencia**.  Seleccione **MyStatefulService.Interface** y haga clic en **Aceptar**.
   
2. En la carpeta **Controladores**, abra el archivo `ValuesController.cs`. Agregue las siguientes instrucciones using al archivo:

    ```c#
    using MyStatefulService.Interface;
    using Microsoft.ServiceFabric.Services.Client;
    using Microsoft.ServiceFabric.Services.Remoting.Client;
    ```
    
3. En estos momentos, el método `Get` solo devuelve una matriz de cadenas codificadas de forma rígida de value1 y value2, que coincide con lo que vimos anteriormente en el explorador. Reemplace esta implementación con el siguiente código:
   
    ```
    public async Task<IEnumerable<string>> Get()
    {
        ICounter counter =
            ServiceProxy.Create<ICounter>(new Uri("fabric:/MyApplication/MyStatefulService"), new ServicePartitionKey(0));
   
        long count = await counter.GetCountAsync();
   
        return new string[] { count.ToString() };
    }
    ```
 
    La primera línea de código de este método crea el objeto ServiceProxy para el servicio con estado mediante la interfaz ICounter. Al crear un objeto ServiceProxy, tiene que proporcionar dos fragmentos de información: un identificador de partición y el nombre del servicio.
   
    Se pueden crear particiones para escalar en los servicios con estado mediante el sombreado de los datos en cubos diferentes, en función de una clave como un identificador de cliente o un código postal. En nuestra sencilla aplicación, el servicio con estado tiene solo una partición, por lo que no importa la clave. Cualquier clave que proporcione lleva a la misma partición. Consulte [Partición de Service Fabric Reliable Services](service-fabric-concepts-partitioning.md)para obtener más información sobre los servicios de partición.
   
    El nombre del servicio es un identificador URI con el formato fabric:/&lt;nombre_de_aplicación&gt;/&lt;nombre_de_servicio&gt;.
   
    Con estas dos piezas de información, Service Fabric puede identificar de forma exclusiva el equipo al que se deben enviar las solicitudes. La clase `ServiceProxy` también controlará sin ningún problema los casos en los que se produce un error en la máquina que hospeda la partición de servicio con estado y hay que promover otra máquina para que ocupe su lugar. Esta abstracción simplifica la escritura del código de cliente para tratar con otros servicios.
   
    Una vez que se tiene el proxy, se invoca el método `GetCountAsync` y se devuelve su resultado.

4. Presione F5 de nuevo para ejecutar la aplicación modificada. Como antes, Visual Studio inicia automáticamente el explorador en la raíz del proyecto web. Agregue la ruta de acceso "api/values", debería ver el valor actual del contador devuelto.
   
    ![El valor del contador con estado aparece en el explorador](./media/service-fabric-tutorial-create-dotnet-app/browser-aspnet-counter-value.png)
   
    Actualice el explorador periódicamente para ver el valor actualizado del contador.

Para detener la depuración de la aplicación, vuelva a Visual Studio y presione **Mayús+F5**.

## <a name="next-steps"></a>Pasos siguientes
En esta parte del tutorial, ha aprendido a:

> [!div class="checklist"]
> * Crear un servicio de ASP.NET Core Web API como un servicio de confianza
> * Crear un servicio de confianza con estado
> * Implementar la comunicación remota de servicio y usar un proxy de servicio

Avance hasta el siguiente tutorial:
> [!div class="nextstepaction"]
> [Implementación de la aplicación en Azure](service-fabric-tutorial-deploy-app-to-party-cluster.md)
