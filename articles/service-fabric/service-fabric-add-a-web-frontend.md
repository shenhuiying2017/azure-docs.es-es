<properties
   pageTitle="Creación de un front-end web para la aplicación | Microsoft Azure"
   description="Exposición de la aplicación de Service Fabric a la Web usando un proyecto API web de ASP.NET 5 y la comunicación entre servicios a través de ServiceProxy."
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="coreysa"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="10/17/2015"
   ms.author="seanmck"/>

# Creación de un front-end de servicio web para una aplicación

De forma predeterminada, los servicios de Service Fabric no proporcionan una interfaz pública para la Web. Para exponer la funcionalidad de su aplicación a los clientes HTTP, tendrá que crear un proyecto web para que actúe como punto de entrada y, a continuación, comunicarse desde allí con los servicios individuales.

Este tutorial le guiará por el proceso de agregar un front-end API web de ASP.NET 5 a una aplicación que ya incluye un servicio con estado. Si aún no lo ha hecho, considere la posibilidad de realizar el tutorial [Creación de su primera aplicación en Visual Studio](service-fabric-create-your-first-application-in-visual-studio.md) antes de iniciar este.

## Incorporación de un servicio ASP.NET 5 a una aplicación

ASP.NET 5 es un marco de desarrollo web ligero multiplataforma, que permite la creación de modernas interfaces de usuario web y API web. Vamos a agregar un proyecto API web de ASP.NET a la aplicación existente.

1. En el Explorador de soluciones, haga clic con el botón derecho en el proyecto de aplicación y seleccione **Agregar Service Fabric**.

	![Incorporación de un nuevo servicio a una aplicación existente][vs-add-new-service]

2. En el cuadro de diálogo de nuevo servicio, elija el servicio web ASP.NET 5 y asígnele un nombre.

	![Selección del servicio web ASP.NET en el cuadro de diálogo de nuevo servicio][vs-new-service-dialog]

3. El cuadro de diálogo siguiente proporciona un conjunto de plantillas de proyecto ASP.NET 5. Tenga en cuenta que se trata de las mismas plantillas que vería si crea un proyecto ASP.NET 5 fuera de una aplicación de Service Fabric. Para este tutorial elegimos API web, pero puede aplicar los mismos conceptos para crear una aplicación web completa.

	![Elección del tipo de proyecto ASP.NET][vs-new-aspnet-project-dialog]

  Una vez creado el proyecto API web, tendrá dos servicios en la aplicación. A medida que compila la aplicación irá agregando más servicios en la misma forma, cada uno de ellos es independiente para la versión y las actualizaciones que en ellos se realicen.

## Ejecución de la aplicación

Para obtener una idea de lo que hemos hecho, vamos a implementar la nueva aplicación y a observar el comportamiento predeterminado que proporciona la plantilla API web de ASP.NET 5.

1. Presione F5 en Visual Studio para depurar la aplicación.

2. Cuando se completa la implementación, Visual Studio iniciará el explorador con la raíz del servicio API web de ASP.NET, algo como http://localhost:33003 (el número de puerto se asigna de forma aleatoria y puede ser diferente en su equipo). La plantilla API web de ASP.NET 5 no proporciona comportamiento predeterminado para la raíz por lo que se producirá un error en el explorador.

3. Agregue `/api/values` a la ubicación en el explorador. Esto invocará el método `Get` en ValuesController en la plantilla API web y devolverá la respuesta predeterminada proporcionada por la plantilla, una matriz JSON que contiene dos cadenas:

  ![Valores predeterminados devueltos desde la plantilla API web de ASP.NET 5][browser-aspnet-template-values]

  Al final del tutorial, reemplazaremos estos valores predeterminados con el valor más reciente del contador de nuestro servicio con estado.

## Conexión de los servicios

Service Fabric proporciona una flexibilidad completa en el modo de comunicación con los servicios confiables. En una sola aplicación, es posible que tenga servicios que son accesibles a través de TCP, otros con una API de REST de HTTP y otros a través de otros sockets web. Para obtener información sobre las opciones disponibles y sus inconvenientes, consulte [Comunicación con los servicios](service-fabric-connect-and-communicate-with-services.md). En este tutorial, se sigue uno de los enfoques más sencillos y se usan las clases `ServiceProxy`/`ServiceCommunicationListener` proporcionadas en el SDK.

En el enfoque `ServiceProxy` (que se basa en llamadas a procedimiento remoto o RPC) se define una interfaz para que actúe como contrato público para el servicio y, a continuación, se usa esa interfaz para generar una clase de proxy para interactuar con el servicio.

### Creación de la interfaz

Comenzaremos por crear la interfaz para que actúe como contrato entre el servicio con estado y sus clientes, incluido el proyecto ASP.NET 5.

1. En el Explorador de soluciones, haga clic con el botón derecho en la solución y seleccione **Agregar > Nuevo proyecto**.

2. Elija la entrada de Visual C# en el panel de navegación izquierdo y, a continuación, seleccione la plantilla **Biblioteca de clases**. Asegúrese de que la versión de .NET framework está establecida en 4.5.1.

  ![Creación de un proyecto de interfaz para el servicio con estado][vs-add-class-library-project]

3. Para que `ServiceProxy` pueda usar una interfaz, esta tiene que derivar de la interfaz IService, que se incluye en uno de los paquetes de NuGet de Service Fabric. Para agregar el paquete, haga clic con el botón derecho en el nuevo proyecto de biblioteca de clases y elija **Administrar paquetes de NuGet**.

4. Asegúrese de que la casilla **Incluir versión preliminar** está seleccionada, y luego busque el paquete **Microsoft.ServiceFabric.Services** e instálelo.

  ![Incorporación del paquete de NuGet de servicios][vs-services-nuget-package]

5. En la biblioteca de clases, cree una interfaz con un único método, `GetCountAsync`, y amplíe la interfaz de IService.

  ```c# namespace MyStatefulService.Interfaces { using Microsoft.ServiceFabric.Services;

      public interface ICounter: IService
      {
          Task<long> GetCountAsync();
      }
  } ```

### Implementación de la interfaz en el servicio con estado

Ahora que hemos definido la interfaz, tenemos que implementarla en nuestro servicio con estado.

1. En el servicio con estado, agregue una referencia al proyecto de biblioteca de clase que contiene la interfaz.

  ![Incorporación de una referencia al proyecto de biblioteca de clases en el servicio con estado][vs-add-class-library-reference]

2. Busque la clase que hereda de `StatefulService`, como `MyStatefulService`, y amplíela para implementar la interfaz `ICounter`.

  ```c#
  public class MyStatefulService : StatefulService, ICounter
  {        
        // ...
  }
  ``` 3. Ahora implemente el método único definido en la interfaz `ICounter`, `GetCountAsync`.

  ```c# public async Task<long> GetCountAsync() { var myDictionary = await this.StateManager.GetOrAddAsync<IReliableDictionary<string  long>>("myDictionary");

      using (var tx = this.StateManager.CreateTransaction())
      {          
          var result = await myDictionary.TryGetValueAsync(tx, "Counter-1");
          return result.HasValue ? result.Value : 0;
      }
  } ```

### Exposición del servicio con estado mediante ServiceCommunicationListener

Con la interfaz `ICounter` implementada, el paso final para habilitar el servicio con estado al que se pueda llamar desde otros servicios es abrir un canal de comunicación. Para los servicios con estado, Service Fabric proporciona un método que se puede reemplazar denominado `CreateServiceReplicaListeners` donde puede especificar uno o varios agentes de escucha de comunicación basados en el tipo de comunicación que desea habilitar en el servicio.

>[AZURE.NOTE]El método equivalente para abrir un canal de comunicación a los servicios sin estado se llama `CreateServiceInstanceListeners`.

En este caso, proporcionaremos un `ServiceCommunicationListener`, que crea un punto de conexión RPC al que se puede llamar desde los clientes usando `ServiceProxy`.

```c#
protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
{
    return new List<ServiceReplicaListener>()
    {
        new ServiceReplicaListener(
            (initParams) =>
                new ServiceCommunicationListener<ICounter>(initParams, this))
    };
}
```


### Uso de ServiceProxy para interactuar con el servicio

Nuestro servicio con estado está listo ahora para recibir tráfico de otros servicios, no falta más que agregar el código para comunicarse con él desde el servicio web ASP.NET.

1. En el proyecto ASP.NET, agregue una referencia a la biblioteca de clase que contiene la interfaz `ICounter`.

2. Agregue el paquete Microsoft.ServiceFabric.Services al proyecto ASP.NET, de la misma forma que hizo anteriormente para el proyecto de biblioteca de clases. Esto le proporcionará la clase `ServiceProxy`.

3. En la carpeta de controladores, abra la clase `ValuesController`. Tenga en cuenta que el método `Get` actualmente solo devuelve una matriz de cadenas codificadas de forma rígida de "value1" y "value2", que coincide con lo que vimos anteriormente en el explorador. Reemplace esta implementación con el siguiente código:

  ```c# public async Task<IEnumerable<string>> Get() { ICounter counter = ServiceProxy.Create<ICounter>(0, new Uri("fabric:/MyApp/MyStatefulService"));

      long count = await counter.GetCountAsync();

      return new string[] { count.ToString() };
  } ```

  La primera línea de código es la clave. Para crear el proxy ICounter para el servicio con estado, tiene que proporcionar dos fragmentos de información: un identificador de partición y el nombre del servicio.

  La creación de particiones permite escalar los servicios con estado dividiendo su estado en depósitos distintos en función de una clave que usted define, como puede ser un identificador de cliente o un código postal. En nuestra aplicación trivial, el servicio con estado solo tiene una partición, por ello la clave no importa: cualquier clave que proporcione llevará a la misma partición.

  El nombre del servicio es un URI del tejido del formulario:/&lt;application\_name&gt;/&lt;service\_name&gt;

  Con estas dos piezas de información, Service Fabric puede identificar de forma exclusiva el equipo al que se deben enviar las solicitudes. `ServiceProxy` también controlará sin ningún problemas los casos en los que se produce un error en el equipo que aloja la partición de servicio con estado, y es necesario promover a otro equipo para ocupar su lugar. Esta abstracción simplifica considerablemente la escritura del código de cliente para tratar con otros servicios.

  Una vez que se tiene el proxy, simplemente se invoca el método `GetCountAsync` y se devuelve su resultado.

4. Presione F5 para ejecutar la aplicación modificada. Como antes, Visual Studio iniciará automáticamente el explorador en la raíz del proyecto web. Agregue la ruta de acceso "api/values", debería ver el valor actual del contador devuelto.

  ![El valor del contador con estado aparece en el explorador][browser-aspnet-counter-value]

  Actualice el explorador periódicamente para ver el valor actualizado del contador.


## ¿Y los actores?

Este tutorial se centra en la incorporación de un front-end de web que se comunica con un servicio con estado, pero puede seguir un modelo muy parecido para hablar con actores. De hecho, es algo más sencillo.

Cuando se crea un proyecto de actor, Visual Studio genera automáticamente un proyecto de interfaz. Puede usar esta interfaz para generar un proxy de actor en el proyecto web para comunicarse con el actor. El canal de comunicación se proporciona automáticamente por lo que no es necesario hacer nada equivalente a establecer un `ServiceCommunicationListener` como tuvo que hacer para el servicio con estado en este tutorial.

## Ejecución de servicios web en un clúster local

En general, puede implementar exactamente la misma aplicación Service Fabric a un clúster de varios equipo que haya implementado en el clúster local y tener total seguridad de que va a funcionar como esperaba, ya que el clúster local es simplemente una configuración de cinco nodos contraída en un único equipo.

En cuanto a los servicios web, de todas formas, existe un matiz clave. Cuando el clúster se encuentra detrás de un equilibrador de carga, como sucede en Azure, tiene que asegurarse de que los servicios web se implementan en todos los equipos, ya que el equilibrador de carga simplemente realizará un enrutamiento del tráfico round robin en todos los equipos. Esto se puede hacer estableciendo `InstanceCount` para el servicio en el valor especial -1. Por el contrario, cuando se ejecuta localmente tiene que asegurarse de que se está ejecutando una sola instancia del servicio; en caso contrario, se encontrará con conflictos de varios procesos de escucha en la misma ruta de acceso y el mismo puerto. Como resultado, el recuento de instancias de servicio web debe establecerse en 1 para deployments.git local

Para obtener información sobre cómo configurar valores diferentes para diferentes entorno, consulte [Administración de los parámetros de la aplicación en varios entornos](service-fabric-manage-multiple-environment-app-configuration.md).

## Pasos siguientes

- [Creación de un clúster en Azure con la implementación de la aplicación a la nube](service-fabric-cluster-creation-via-portal.md)
- [Más información sobre la comunicación con los servicios](service-fabric-connect-and-communicate-with-services.md)
- [Más información acerca de la partición de servicios con estado](service-fabric-concepts-partitioning.md)

<!-- Image References -->

[vs-add-new-service]: ./media/service-fabric-add-a-web-frontend/vs-add-new-service.png
[vs-new-service-dialog]: ./media/service-fabric-add-a-web-frontend/vs-new-service-dialog.png
[vs-new-aspnet-project-dialog]: ./media/service-fabric-add-a-web-frontend/vs-new-aspnet-project-dialog.png
[browser-aspnet-template-values]: ./media/service-fabric-add-a-web-frontend/browser-aspnet-template-values.png
[vs-add-class-library-project]: ./media/service-fabric-add-a-web-frontend/vs-add-class-library-project.png
[vs-add-class-library-reference]: ./media/service-fabric-add-a-web-frontend/vs-add-class-library-reference.png
[vs-services-nuget-package]: ./media/service-fabric-add-a-web-frontend/vs-services-nuget-package.png
[browser-aspnet-counter-value]: ./media/service-fabric-add-a-web-frontend/browser-aspnet-counter-value.png

<!---HONumber=Nov15_HO4-->