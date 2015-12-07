<properties
   pageTitle="Implementación de una aplicación personalizada en Azure Service Fabric | Microsoft Azure"
   description="Tutorial sobre cómo empaquetar una aplicación existente para implementarla en un clúster de Azure Service Fabric"
   services="service-fabric"
   documentationCenter=".net"
   authors="bmscholl"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="11/17/2015"
   ms.author="bscholl"/>

# Implementación de una aplicación personalizada en Service Fabric

Puede ejecutar cualquier tipo de aplicación existente, como Node.js, Java o aplicaciones nativas, en Service Fabric. Service Fabric trata esas aplicaciones como servicios sin estado y los coloca en nodos de un clúster en función de la disponibilidad y otras métricas. Este artículo describe cómo empaquetar e implementar una aplicación existente en un clúster de Service Fabric.

## Ventajas de ejecutar una aplicación personalizada en Service Fabric

Hay un par de ventajas inherentes a la ejecución de la aplicación en un clúster de Service Fabric:

- Alta disponibilidad: las aplicaciones que se ejecutan en Service Fabric ofrecen alta disponibilidad de fábrica. Service Fabric se asegura de que siempre haya una instancia de una aplicación en funcionamiento.
- Supervisión de estado: la supervisión de fábrica del estado de Service Fabric detecta si la aplicación está en funcionamiento y proporciona información de diagnóstico en caso de que se produzca un error.   
- Administración del ciclo de vida de las aplicaciones: además las actualizaciones sin tiempo de inactividad, Service Fabric también permite volver a la versión anterior, en caso de que haya problemas durante la actualización.    
- Densidad: se pueden ejecutar varias aplicaciones en el clúster, lo que elimina la necesidad de que cada aplicación se ejecute en su propio hardware.

En este artículo, se describen los pasos básicos para empaquetar una aplicación existente e implementarla en Service Fabric.


## Introducción rápida de los archivos de manifiesto de servicio y aplicación

Antes de entrar en los detalles de la implementación de una aplicación existente, es importante comprender el modelo de implementación y empaquetado de Service Fabric. El modelo de implementación y de empaquetado de Service Fabric se basa principalmente en dos archivos:


* **Manifiesto de aplicación**

  El manifiesto de aplicación se usa para describir la aplicación y muestra en una lista los servicios que la componen junto con otros parámetros, como el número de instancias, que se usan para definir cómo se deben implementar los servicios. En el mundo de Service Fabric, las aplicaciones son “unidades que se pueden actualizar”. Es posible actualizar una aplicación como una sola unidad en la que los posibles errores (y posibles reversiones) son administrados por la plataforma para garantizar que el proceso de actualización sea completamente satisfactorio o, si se produce un error, no deje a la aplicación en un estado desconocido o inestable.


  ```xml
  <?xml version="1.0" encoding="utf-8"?>
  <ApplicationManifest ApplicationTypeName="actor2Application"
                       ApplicationTypeVersion="1.0.0.0"
                       xmlns="http://schemas.microsoft.com/2011/01/fabric"
                       xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">

    <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="actor2Pkg" ServiceManifestVersion="1.0.0.0" />
      <ConfigOverrides />
    </ServiceManifestImport>

    <DefaultServices>
      <Service Name="actor2">
        <StatelessService ServiceTypeName="actor2Type">
          <SingletonPartition />
        </StatelessService>
      </Service>
    </DefaultServices>

  </ApplicationManifest>
  ```

* **Manifiesto de servicio**

  El manifiesto de servicio describe los componentes de un servicio. Incluye datos como el nombre y tipo de servicio (información que Service Fabric usa para administrar el servicio), su código, configuración y componentes de datos y algunos parámetros adicionales que pueden usarse para configurar el servicio una vez que se implementa. No vamos a entrar en detalles sobre todos los distintos parámetros disponibles en el manifiesto de servicio, revisaremos el subconjunto que se requiere para que una aplicación existente se ejecute en Service Fabric


  ```xml
  <?xml version="1.0" encoding="utf-8"?>
  <ServiceManifest Name="actor2Pkg"
                   Version="1.0.0.0"
                   xmlns="http://schemas.microsoft.com/2011/01/fabric"
                   xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                   xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
    <ServiceTypes>
      <StatelessServiceType ServiceTypeName="actor2Type" />
    </ServiceTypes>

    <CodePackage Name="Code" Version="1.0.0.0">
      <EntryPoint>
        <ExeHost>
          <Program>actor2.exe</Program>
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

## Estructura del archivo del paquete de aplicación
Para implementar una aplicación mediante, por ejemplo, los cmdlets de Powershell, la aplicación debe seguir una estructura de directorios predefinida.

```
|-- AppplicationPackage
	|-- code
		|-- existingapp.exe
	|-- config
		|--Settings.xml
    |--data    
    |-- ServiceManifest.xml
|-- ApplicationManifest.xml
```

La raíz contiene el archivo ApplicationManifest.xml que define la aplicación. Se usa un subdirectorio para cada servicio incluido en la aplicación para contener todos los artefactos que requiere el servicio: ServiceManifest.xml y, por lo general, 3 directorios:

- *code*: contiene el código de servicio.
- *config*: contiene un archivo settings.xml (y otros archivos si es necesario) a los que el servicio puede tener acceso en tiempo de ejecución para recuperar la configuración específica.
- *data*: un directorio adicional para almacenar datos locales adicionales que el servicio puede necesitar. Nota: Se deben usar datos para almacenar solo los datos ephymeral. Service Fabric no copia/replica los cambios en el directorio de datos si el servicio debe reubicarse, por ejemplo, durante la conmutación por error.

Nota: no tiene que crear los directorios `config` y `data` si no los necesita.

## Proceso de empaquetado de una aplicación existente

El proceso de empaquetado de una aplicación existente se basa en los siguientes pasos:

- Crear la estructura de directorios del paquete
- Agregar los archivos de configuración y el código de la aplicación
- Actualizar el archivo de manifiesto de servicio
- Actualizar el manifiesto de aplicación

>[AZURE.NOTE]Se proporciona una herramienta de empaquetado que permite crear automáticamente ApplicationPackage. La herramienta se encuentra actualmente en versión de vista previa. Puede descargarla [aquí](http://aka.ms/servicefabricpacktool).

### Crear la estructura de directorios del paquete
Puede empezar creando la estructura de directorios del modo descrito anteriormente.

### Agregar archivos de código y la configuración de la aplicación
Después de haber creado la estructura de directorios, puede agregar los archivos de código y configuración de la aplicación en el directorio de código y configuración. También puede crear directorios adicionales o subdirectorios en los directorios code o config. Service Fabric hace una copia del contenido del directorio raíz de la aplicación para que no haya ninguna estructura predefinida para usar aparte de crear los dos directorios principales code y config (pero puede elegir diferentes nombres si lo desea; obtenga más detalles en la sección siguiente).

>[AZURE.NOTE]\: asegúrese de incluir todos las archivos/dependencias que necesite la aplicación. Servicio Fabric copiará el contenido del paquete de aplicación en todos los nodos del clúster en los que se implementarán los servicios de la aplicación. El paquete debe contener todo el código que la aplicación necesita para ejecutarse. No se recomienda dar por hecho que las dependencias ya están instaladas.

### Editar el archivo de manifiesto de servicio
El siguiente paso consiste en editar el archivo de manifiesto de servicio para incluir la siguiente información:

- El nombre del tipo de servicio Se trata de un 'identificador' que usa Service Fabric para identificar un servicio.
- El comando que se usa para iniciar la aplicación (ExeHost)
- Cualquier script que se debe ejecutar para instalar y configurar la aplicación (SetupEntrypoint).

Aquí tiene un ejemplo de `ServiceManifest.xml`:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Name="NodeApp" Version="1.0.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <ServiceTypes>
      <StatelessServiceType ServiceTypeName="NodeApp" UseImplicitHost="true"/>
   </ServiceTypes>
   <CodePackage Name="code" Version="1.0.0.0">
      <SetupEntryPoint>
         <ExeHost>
             <Program>scripts\launchConfig.cmd</Program>
         </ExeHost>
      </SetupEntryPoint>
      <EntryPoint>
         <ExeHost>
            <Program>node.exe</Program>
            <Arguments>bin/www</Arguments>
            <WorkingFolder>CodePackage</WorkingFolder>
         </ExeHost>
      </EntryPoint>
   </CodePackage>
   <Resources>
      <Endpoints>
         <Endpoint Name="NodeAppTypeEndpoint" Protocol="http" Port="3000" Type="Input" />
      </Endpoints>
   </Resources>
</ServiceManifest>
```

Repasemos la parte diferente del archivo que necesita actualizar:

### ServiceTypes:

```xml
<ServiceTypes>
  <StatelessServiceType ServiceTypeName="NodeApp" UseImplicitHost="true" />
</ServiceTypes>
```

- Puede elegir cualquier nombre que desee para `ServiceTypeName`. El valor se usa en el `ApplicationManifest.xml` para identificar el servicio.
- Debe especificar `UseImplicitHost="true"`. Este atributo indica a Service Fabric que el servicio se basa en una aplicación independiente, por lo que solo necesita iniciarla como un proceso y supervisar su estado.

### CodePackage
El CodePackage especifica la ubicación (y versión) de código del servicio.

```xml
<CodePackage Name="Code" Version="1.0.0.0">
```

El elemento `Name` se usa para especificar el nombre del directorio en el paquete de aplicación que contiene el código del servicio. `CodePackage` también tiene el atributo `version` que puede usarse para especificar la versión del código y, potencialmente, para actualizar el código de servicio mediante la infraestructura de ALM de Service Fabric.
### SetupEntrypoint

```xml
<SetupEntryPoint>
   <ExeHost>
       <Program>scripts\launchConfig.cmd</Program>
   </ExeHost>
</SetupEntryPoint>
```
SetupEntrypoint se usa para especificar cualquier archivo ejecutable o por lotes que se deba ejecutar antes de iniciar el código del servicio. Se trata de un elemento opcional, por lo que no necesita incluirse si no hay ninguna inicialización/instalación obligatoria. SetupEntrypoint se ejecuta cada vez que se reinicia el servicio. Hay solo un SetupEntrypoint, por lo que los script de configuración/instalación deben incluirse en un solo archivo de lote si la instalación/configuración de la aplicación requiere varios scripts. Al igual que el elemento Entrypoint, SetupEntrypoint puede ejecutar cualquier tipo de archivo: archivo ejecutable, archivos por lotes o cmdlet de Powershell. En el ejemplo anterior, SetupEntrypoint se basa en un archivo de lotes launchConfig.cmd que se encuentra en el subdirectorio `scripts` del directorio Code (suponiendo que el elemento WorkingDirectory esté establecido en Code).

### Punto de entrada

```xml
<EntryPoint>
  <ExeHost>
    <Program>node.exe</Program>
    <Arguments>bin/www</Arguments>
    <WorkingFolder>CodeBase</WorkingFolder>
  </ExeHost>
</EntryPoint>
```

El elemento `Entrypoint` del archivo de manifiesto de servicio se usa para especificar cómo se inicia el servicio. El elemento `ExeHost` especifica el archivo ejecutable (y los argumentos) que deben usarse para iniciar el servicio.

- `Program`: especifica el nombre del archivo ejecutable que se debe ejecutar para iniciar el servicio.
- `Arguments`: especifica los argumentos que se deben pasar al archivo ejecutable. Puede ser una lista de parámetros con argumentos.
- `WorkingFolder`: especifica el directorio de trabajo para el proceso que se va a iniciar. Puede especificar dos valores:
	- `CodeBase`: el directorio de trabajo se va a establecer en el directorio Code del paquete de aplicación (directorio `Code` en la estructura que se muestra a continuación).
	- `CodePackage`: el directorio de trabajo se establecerá en la raíz del paquete de aplicación (`MyServicePkg`).
- El elemento `WorkingDirectory` es útil para establecer el directorio de trabajo correcto de modo que las rutas de acceso relativas las puedan usar los scripts de aplicación o inicialización.

### Extremos

```xml
<Endpoints>
   <Endpoint Name="NodeAppTypeEndpoint" Protocol="http" Port="3000" Type="Input" />
</Endpoints>

```
El elemento `Endpoint` especifica los puntos de conexión en los que puede escuchar la aplicación. En este ejemplo, la aplicación Node.js escucha en el puerto 3000.

## Archivo de manifiesto de aplicación

Una vez que haya configurado el archivo `servicemanifest.xml`, deberá realizar algunos cambios en el archivo `ApplicationManifest.xml` para asegurarse de que se use el tipo de servicio y el nombre correctos.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="NodeAppType" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="NodeApp" ServiceManifestVersion="1.0.0.0" />
   </ServiceManifestImport>
</ApplicationManifest>
```

### ServiceManifestImport

En `ServiceManifestImport`, puede especificar uno o varios servicios para incluirlos en la aplicación. Se hace referencia a los servicios con `ServiceManifestName`, que especifica el nombre del directorio donde se encuentra el archivo `ServiceManifest.xml`.

```xml
<ServiceManifestImport>
  <ServiceManifestRef ServiceManifestName="NodeApp" ServiceManifestVersion="1.0.0.0" />
</ServiceManifestImport>
```

### Configuración de los registros
Para la aplicación existente, es muy útil poder ver los registros de la consola para averiguar si los script de la configuración y la aplicación no muestran ningún error. Se puede configurar el redireccionamiento de la consola en el archivo `ServiceManifest.xml` usando el elemento `ConsoleRedirection`.

```xml
<EntryPoint>
  <ExeHost>
    <Program>node.exe</Program>
    <Arguments>bin/www</Arguments>
    <WorkingFolder>CodeBase</WorkingFolder>
    <ConsoleRedirection FileRetentionCount="5" FileMaxSizeInKb="2048"/>
  </ExeHost>
</EntryPoint>
```

* `ConsoleRedirection` puede usarse para redirigir las salidas de consola (stdout y stderr) a un directorio de trabajo para que puedan usarse para comprobar que no hay ningún error durante la instalación o ejecución de la aplicación en el clúster de Service Fabric.

	* `FileRetentionCount` determina cuántos archivos se guardan en el directorio de trabajo. Un valor de 5 por ejemplo, significa que se almacenarán los archivos de registro para las 5 ejecuciones anteriores en el directorio de trabajo.
	* `FileMaxSizeInKb` especifica el tamaño máximo de los archivos de registro.

Los archivos de registro se guardan en uno de los directorios de trabajo del servicio. Para determinar dónde se encuentran los archivos, deberá usar el Explorador de Service Fabric para determinar en qué nodo se está ejecutando el servicio y cuál es el directorio de trabajo que se usa actualmente. Más adelante en este artículo, se enseña cómo hacerlo.

### Implementación
El último paso es implementar la aplicación. El siguiente script de PowerShell muestra cómo implementar la aplicación en el clúster de desarrollo local e iniciar un nuevo servicio de Service Fabric.

```Powershell

Connect-ServiceFabricCluster localhost:19000

Write-Host 'Copying application package...'
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath 'C:\Dev\MulitpleApplications' -ImageStoreConnectionString 'file:C:\SfDevCluster\Data\ImageStoreShare' -ApplicationPackagePathInImageStore 'Store\nodeapp'

Write-Host 'Registering application type...'
Register-ServiceFabricApplicationType -ApplicationPathInImageStore 'Store\nodeapp'

New-ServiceFabricApplication -ApplicationName 'fabric:/nodeapp' -ApplicationTypeName 'NodeAppType' -ApplicationTypeVersion 1.0

New-ServiceFabricService -ApplicationName 'fabric:/nodeapp' -ServiceName 'fabric:/nodeapp/nodeappservice' -ServiceTypeName 'NodeApp' -Stateless -PartitionSchemeSingleton -InstanceCount 1

```
Es posible implementar un servicio Service Fabric en diversas “configuraciones”, por ejemplo, puede implementarse como una o varias instancias o se puede implementar de manera que haya una instancia del servicio en cada nodo del clúster de Service Fabric.

El parámetro `InstanceCount` del cmdlet `New-ServiceFabricService` se usa para especificar cuántas instancias del servicio deben iniciarse en el clúster de Service Fabric. Puede establecer el valor `InstanceCount` según el tipo de aplicación que se vaya a implementar. Los dos escenarios más habituales son: * `InstanCount = "1"`: en este caso, solo se implementará una instancia del servicio en el clúster. El programador de Service Fabric determina en qué nodo se va a implementar el servicio.

* `InstanceCount ="-1"`: en este caso se implementará una instancia del servicio en cada nodo del clúster de Service Fabric. El resultado final tendrá una instancia (y sólo una) del servicio para cada nodo del clúster. Se trata de una configuración útil para las aplicaciones front-end (p. ej., un extremo REST) porque las aplicaciones cliente solo necesitan “conectarse” a cualquiera de los nodos del clúster para poder usar el extremo. Esta configuración también se puede usar cuando, por ejemplo, todos los nodos del clúster de Service Fabric están conectados a un equilibrador de carga para que el tráfico del cliente se pueda distribuir en el servicio que se está ejecutando en todos los nodos del clúster.

### Comprobación de la aplicación en ejecución

En el explorador de Service Fabric, identifique el nodo en el que se está ejecutando el servicio. En este ejemplo, se ejecuta en el nodo 1:

![aplicación en ejecución](./media/service-fabric-deploy-existing-app/runningapplication.png)

Si navega hasta el nodo y accede a la aplicación, verá la información del nodo esencial, incluida su ubicación en el disco.

![ubicación en el disco](./media/service-fabric-deploy-existing-app/locationondisk.png)

Si examina el directorio mediante el explorador de servidores, puede encontrar el directorio de trabajo y la carpeta de registros del servicio tal y como se muestra a continuación.

![ubicación en el disco](./media/service-fabric-deploy-existing-app/loglocation.png)


## Pasos siguientes
En este artículo, ha aprendido los pasos básicos para empaquetar una aplicación existente e implementarla en Service Fabric. Como siguiente paso, puede consultar contenido adicional sobre este tema.

- Ejemplo para empaquetar e implementar una aplicación personalizada en [Github](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/tree/master/Custom/SimpleApplication), incluido un vínculo a la versión preliminar de la herramienta de empaquetado.
- [Implementación de varias aplicaciones personalizadas](service-fabric-deploy-multiple-apps.md).
- Cómo empezar a [crear su primera aplicación de Service Fabric con Visual Studio](service-fabric-create-your-first-application-in-visual-studio.md).

<!---HONumber=AcomDC_1125_2015-->