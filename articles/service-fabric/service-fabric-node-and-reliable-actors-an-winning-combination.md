<properties
   pageTitle="Node.js y actores confiables | Microsoft Azure"
   description="Un tutorial sobre cómo crear una aplicación express de node.js que usa actores confiables y se ejecuta sobre la plataforma Azure Service Fabric."
   services="service-fabric"
   documentationCenter="nodejs"
   authors="clca"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="nodejs"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/17/2015"
   ms.author="claudioc"/>


# Node.js y actores confiables: una excelente combinación
Este artículo es una introducción acerca de cómo se puede crear una aplicación que usa node.js y actores confiables. La solución final es una mezcla de código javascript que se usa principalmente para ofrecer el lado del frontend de la aplicación (API Web/Rest) y C# para efectuar cálculos más complejos. Al aprovechar el modelo de programación de Service Fabric, la aplicación es escalable y de confianza de fábrica. El proceso se basa en los pasos siguientes:

1. Crear un nuevo proyecto de actor con o sin estado de Service Fabric mediante herramientas de Service Fabric para Visual Studio
2. Crear un proyecto de node.js con, por ejemplo, [herramientas node.js para Visual Studio](https://github.com/Microsoft/nodejstools/releases/tag/v1.1.RC) 
3. Agregue un proyecto node.js (una aplicación express 4 básica por ejemplo) a la solución Service Fabric. Simplemente puede usar la solución/Agregar un proyecto existente para incluir el proyecto node.js. 
4. Empaquetar la aplicación node.js para poder implementarla mediante herramientas de VS para Service Fabric

## Creación de un proyecto node.js
Después de crear el proyecto node.js y agregar las dependencias, deberá cambiar la estructura de directorios del proyecto para que siga la estructura requerida por las herramientas de Service Fabric para Visual Studio necesarias para poder empaquetar e implementar la aplicación como cualquier otro servicio de Service Fabric. El objetivo es realizar los cambios en la estructura de directorios una sola vez para poder beneficiarse de poder usar el proceso de implementación en Visual Studio y, por tanto, implementar su aplicación node.js junto con los otros servicios de la solución. Los cambios que necesita hacer en la estructura de directorios son:

1. Crear un directorio de PackageRoot
2. Crear un directorio de código en PackageRoot
3. Mover todos los archivos y directorios del directorio de código

Cuando haya terminado, la estructura del proyecto de Visual Studio debe ser similar a la siguiente:

![][8]

Como puede ver, todo el código de node.js se encuentra en el directorio de PackageRoot/Code. Service Fabric no hace ninguna suposición acerca de las aplicaciones y bibliotecas instaladas en el nodo en el que se va a implementar la aplicación, por lo que necesita incluir todas las dependencias. En el caso de node.js deberá incluir node.exe para que Service Fabric pueda ejecutar el código (puede encontrar node.exe en el directorio program files\\nodejs).

![][3]

## Agregar el archivo de metadatos servicemanifest.xml
Para implementar la aplicación node.js, necesitamos agregar un archivo de metadatos necesario para especificar algunas propiedades que usará Service Fabric con el fin de determinar cómo implementar e iniciar la aplicación.

A continuación se facilita un ejemplo del aspecto que debe tener servicemanifest.xml. Consulte [este artículo](service-fabric-deploy-existing-app.md) para obtener más detalles sobre cuáles son los elementos importantes que deben actualizarse pero que normalmente deberá actualizar:

* Nombre (elemento ServiceManifest)
* ServiceTypeName (elemento StatelessServiceType)
* Argumentos (elemento ExeHost) para especificar el archivo js que debe usarse para iniciar la aplicación.


```xml

<?xml version="1.0" encoding="utf-8"?>

<ServiceManifest Name="NodejsFrontendPkg"
                 Version="1.0.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">

  <ServiceTypes>
    <StatelessServiceType ServiceTypeName="NodejsFrontendType" UseImplicitHost="true" />
  </ServiceTypes>
  <CodePackage Name="Code" Version="1.0.0.0">
    <EntryPoint>
      <ExeHost>
        <Program>node.exe</Program>
        <Arguments>server.js</Arguments>
        <WorkingFolder>CodeBase</WorkingFolder>
      </ExeHost>
    </EntryPoint>
  </CodePackage>
  <ConfigPackage Name="Config" Version="1.0.0.0" />
  <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpoint" />
    </Endpoints>
  </Resources>
</ServiceManifest>

```

> [AZURE.NOTE]Tenga en cuenta que es posible que una de las opciones de servicemanifest.xml no funcione con node.js. En algunos casos `ConsoleRedirection` no funciona si un módulo node.js (por ejemplo, Express) supuso que el fd de stdout y stderr son 1 y 2.

Una vez agregado el archivo `servicemanifest.xml` al proyecto node.js, la estructura del proyecto deberá ser similar a la siguiente:

![][4]

## Agregar archivos binarios de Service Fabric
El paso siguiente consiste en agregar archivos binarios de Service Fabric que se usan para conectar con los actores que se ejecutan en el clúster de Service Fabric. Como puede ver en el ejemplo Edge.js, hay algunas referencias a ensamblados. Para que dichos ensamblados estén disponibles para edge.js, deben copiarse junto con el código de node.js. La forma más sencilla es copiar los archivos binarios de proyectos existentes, los archivos que deben incluirse en el directorio de código (y que usará edge.js) son los siguientes:

```
Microsoft.ServiceFabric.Actors.dll
Microsoft.ServiceFabric.Collections.dll
Microsoft.ServiceFabric.Data.dll
Microsoft.ServiceFabric.Data.Log.dll
Microsoft.ServiceFabric.ReplicatedStore.dll
Microsoft.ServiceFabric.Replicator.dll
Microsoft.ServiceFabric.Services.dll
ServiceFabricServiceModel.dll
System.Fabric.Common.Internal.dll
System.Fabric.Common.Internal.Strings.resources.dll
System.Fabric.dll
```

Después de agregar los archivos binarios al proyecto, la estructura del proyecto deberá parecerse a la siguiente:

![][5]

## Agregar una referencia al proyecto node.js en el archivo applicationmanifest
El paso siguiente consiste en agregar el servicio de node.js al manifiesto de la aplicación para que pueda implementarse mediante las herramientas de Visual Studio para Service Fabric. Esto es necesario porque no hay ninguna integración en las herramientas de Service Fabric para Visual Studio con el proyecto node.js, por lo que debe agregarse manualmente.

En el archivo `applicationmanifest.xml` deberá agregar los siguientes elementos:

* `ServiceManifestImport`
* `Service`

> [AZURE.NOTE]Asegúrese de usar los mismos nombres que usó en el `servicemanifest.xml` para especificar `ServiceName` y `ServiceTypeName`. El archivo `applicationmanifest.xml` debe ser similar al siguiente:

```
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="NodeServiceFabricSampleApplication" ApplicationTypeVersion="1.0.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
    <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="NodeServiceFabricSamplePkg" ServiceManifestVersion="1.0.0.0" />
   </ServiceManifestImport>
   <ServiceManifestImport>
       <ServiceManifestRef ServiceManifestName="VisualObjectsNodejsWebServicePkg" ServiceManifestVersion="1.0.0.0" />
       </ServiceManifestImport>
   <DefaultServices>
      <Service Name="NodeServiceFabricSampleActorService">
         <StatelessService ServiceTypeName="NodeServiceFabricSampleActorServiceType">
            <UniformInt64Partition PartitionCount="9" LowKey="-9223372036854775808" HighKey="9223372036854775807" />
         </StatelessService>
      </Service>
     <Service Name="NodejsFrontendPkg">
       <StatelessService ServiceTypeName="NodejsFrontendType">
         <SingletonPartition />
       </StatelessService>
     </Service>
   </DefaultServices>
</ApplicationManifest>

```

## Uso de actores confiables en la aplicación Node.js
Ahora que la estructura del proyecto está establecida, nos podemos centrar en el código que permite a la aplicación node.js conectarse a actores confiables en el clúster, como se puede hacer desde una aplicación .NET. El escenario que deseamos habilitar es la compilación de una aplicación node.js que actúe como frontend/puerta de enlace para la aplicación cliente y exponer una aplicación basada en web o un conjunto de API de REST que pueda usar una aplicación cliente. Node.js proporciona el frontend de la aplicación mientras que actores confiables proporciona la capa de “servidor de aplicación” escalable y confiable de la aplicación. En Service Fabric, los actores confiables pueden invocarse mediante la [clase ActorProxy](service-fabric-reliable-actors-introduction.md#actor-communication). Afortunadamente hay un módulo node.js excelente que puede usarse para llamar al código .NET: [Edge.js](https://github.com/tjanczuk/edge). Puede usar las instrucciones del repositorio github para averiguar cómo Edge en su equipo.

 
![][2]

Después de instalar Edge mediante NPM o la IU de NPM en Visual Studio, debe mover el nuevo módulo instalado en el directorio node\_modules al subdirectorio Code (hemos cambiado la estructura del proyecto node.js y movido todo el código del directorio PackageRoot/Code). Puede encontrar buenos ejemplos en el repositorio de edge.js en Github acerca de cómo usar Edge para llamar al código de .NET. A continuación se facilita un ejemplo sencillo del aspecto del código cuando es necesario llamar a actores confiables mediante la clase ActorProxy.

```javascript

var getActorStatus = edge.func(function () {

/*

    #r "Microsoft.ServiceFabric.Actors.dll"
    #r "System.Globalization.dll"
    #r "System.dll"
    #r  "visualobjects.interfaces.dll"
    
    using Microsoft.ServiceFabric.Actors;
    using System.Globalization;
    using VisualObjects.Interfaces;
    using System.Threading.Tasks;
    using System; 


public class Startup
    {
         public async Task<object> Invoke(dynamic input)
        {

            var objectId = (string) input.actorId;
            var serviceUri = (string) input.serviceUri;
            var actId = new ActorId(objectId);
            var serId = new Uri(serviceUri);
            var actorProxy = ActorProxy.Create<IVisualObjectActor>(actId, serId);
            var buffer = await actorProxy.GetStateAsJsonAsync();
            if (!string.IsNullOrEmpty(buffer))
                return buffer;
                else 
                return "null";
         }
  }
 
 */ 

});

```

> [AZURE.NOTE]ActorProxy usa la interfaz de Actor para saber a qué actor se debe conectar. Para que el código administrado que se ejecuta en el proceso de node.js para poder crear una instancia de esa clase, el ensamblado de la interfaz de Actor debe copiarse en el directorio de código, como los demás archivos DLL de Service Fabric. Nota: Deberá copiar el archivo dll (o establecer una acción posterior a la compilación en VS) cada vez que vaya a realizar cambios en los métodos/parámetros en la interfaz.

![][6]

## Implementación
En este momento el proyecto puede implementarse mediante herramientas de Service Fabric para Visual Studio. El último paso del proceso es hacer referencia al proyecto en el proyecto de aplicación de Service Fabric para que se pueda incluir en el paquete de aplicación e implementar en el clúster.

![][9]
 
Puede implementar la aplicación (que incluirá la aplicación node.js) usando, por ejemplo, el menú contextual de la solución.

![][10]

## Pasos siguientes
* Obtenga más información acerca de [actores confiables](service-fabric-reliable-actors-introduction.md)
* Obtenga más información acerca del [ciclo de vida de la aplicación Service Fabric](service-fabric-application-lifecycle.md)
* Obtenga más información acerca de la actualización de una [aplicación de Service Fabric](service-fabric-application-upgrade.md) 

<!-- images -->
[1]: ./media/service-fabric-node-and-reliable-actors-app/nodejs-project-structure.PNG
[2]: ./media/service-fabric-node-and-reliable-actors-app/edge-js.PNG
[3]: ./media/service-fabric-node-and-reliable-actors-app/node-exe.PNG
[4]: ./media/service-fabric-node-and-reliable-actors-app/project-structure-with-manifest.PNG
[5]: ./media/service-fabric-node-and-reliable-actors-app/project-structure-with-dlls.PNG
[6]: ./media/service-fabric-node-and-reliable-actors-app/project-structure-interface-dll.PNG
[7]: ./media/service-fabric-node-and-reliable-actors-app/project-structure-config.PNG
[8]: ./media/service-fabric-node-and-reliable-actors-app/nodejs-project-structure1.PNG
[9]: ./media/service-fabric-node-and-reliable-actors-app/application-project-reference.PNG
[10]: ./media/service-fabric-node-and-reliable-actors-app/solution-deploy.PNG

<!---HONumber=Sept15_HO3-->