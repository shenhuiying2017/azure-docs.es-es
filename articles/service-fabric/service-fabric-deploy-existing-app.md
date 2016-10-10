<properties
   pageTitle="Implementación de un ejecutable invitado en Azure Service Fabric | Microsoft Azure"
   description="Tutorial sobre cómo empaquetar una aplicación existente como ejecutable invitado para implementarla en un clúster de Azure Service Fabric"
   services="service-fabric"
   documentationCenter=".net"
   authors="msfussell"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="na"
   ms.date="09/22/2016"
   ms.author="msfussell;mikhegn"/>

# Implementación de un ejecutable invitado en Service Fabric

Puede ejecutar cualquier tipo de aplicación, como node.js, Java o aplicaciones nativas, en Azure Service Fabric. Service Fabric hace referencia a estos tipos de aplicaciones como aplicaciones ejecutables invitadas. Los ejecutables invitados se tratan por Service Fabric como servicios sin estado. Por ello, se colocan en los nodos de un clúster, en función de la disponibilidad y otras métricas. En este artículo se describe cómo empaquetar e implementar un ejecutable invitado en un clúster de Service Fabric con Visual Studio o una utilidad de línea de comandos.

En este artículo se describen los pasos para empaquetar un ejecutable invitado e implementarlo en Service Fabric.

## Ventajas de ejecutar un ejecutable invitado en Service Fabric

Hay unas cuantas ventajas inherentes a la ejecución de un ejecutable invitado en un clúster de Service Fabric:

- Alta disponibilidad. Las aplicaciones que se ejecutan en Service Fabric ofrecen alta disponibilidad de modo. Service Fabric garantiza que se están ejecutando instancias de una aplicación.
- Supervisión del estado. La supervisión del mantenimiento de Service Fabric detecta si hay una aplicación en funcionamiento y proporciona información de diagnóstico si se produce un error.
- Administración del ciclo de vida de las aplicaciones. Además de actualizaciones sin tiempo de inactividad, Service Fabric proporciona reversión automática a la versión anterior, en caso de que se notifique un evento de mantenimiento incorrecto durante una actualización.
- Densidad. Se pueden ejecutar varias aplicaciones en el clúster, lo que elimina la necesidad de que cada aplicación se ejecute en su propio hardware.


## Información general de los archivos de manifiesto de servicio y aplicación

Como parte de la implementación de un ejecutable invitado, resulta útil comprender el modelo de implementación y empaquetado de Service Fabric tal como se describe en el [modelo de aplicación](service-fabric-application-model.md). El modelo de empaquetado de Service Fabric se basa en dos archivos XML: los manifiestos de aplicación y de servicio. La definición de esquema para los archivos ApplicationManifest.xml y ServiceManifest.xml se instala con el SDK de Service Fabric en *C:\\Archivos de programa\\Microsoft SDKs\\Service Fabric\\schemas\\ServiceFabricServiceModel.xsd*.

* **Manifiesto de aplicación** El manifiesto de aplicación se usa para describir la aplicación. Muestra en una lista los servicios que la componen junto con otros parámetros, como el número de instancias, que se usan para definir cómo se deben implementar uno o más servicios.

  En Service Fabric, una aplicación es una unidad de implementación y actualización. Una aplicación se puede actualizar como una sola unidad donde se administran los posibles errores y reversiones. Service Fabric garantiza que el proceso de actualización es correcto, o bien, si se produce un error en la actualización, no deja a la aplicación en un estado desconocido o inestable.

* **Manifiesto de servicio** El manifiesto de servicio describe los componentes de un servicio. Incluye datos, como el nombre y el tipo de servicio, así como su código, configuración y datos. El manifiesto de servicio también incluye algunos parámetros adicionales que pueden usarse para configurar el servicio una vez que se implementa.


## Estructura de archivo del paquete de aplicación
Para implementar una aplicación en Service Fabric, la aplicación debe seguir una estructura de directorios predefinida. Ejemplo siguiente de esta estructura.

```
|-- ApplicationPackageRoot
    |-- GuestService1Pkg
        |-- Code
            |-- existingapp.exe
        |-- Config
            |-- Settings.xml
        |-- Data
        |-- ServiceManifest.xml
    |-- ApplicationManifest.xml
```

ApplicationPackageRoot contiene el archivo ApplicationManifest.xml que define la aplicación. Se usa un subdirectorio para cada servicio incluido en la aplicación para contener todos los artefactos que requiere el servicio: ServiceManifest.xml y, por lo general, los tres directorios siguientes:

- *Code*. Este directorio contiene el código de servicio.
- *Config*. Este directorio contiene un archivo settings.xml (y otros archivos si es necesario) a los que el servicio puede tener acceso en tiempo de ejecución para recuperar la configuración específica.
- *Data*. Se trata de un directorio adicional para almacenar datos locales adicionales que el servicio puede necesitar. Nota: El directorio Data debe usarse para almacenar solamente datos efímeros. Service Fabric no copia/replica los cambios en el directorio de datos si el servicio tiene que reubicarse, por ejemplo, durante una conmutación por error.

Nota: No tiene que crear los directorios `config` y `data` si no los necesita.

## Empaquetado de un ejecutable existente

Al empaquetar un ejecutable invitado, puede usar una plantilla de proyecto de Visual Studio o [crear el paquete de aplicación manualmente](#manually). Con Visual Studio, la estructura del paquete de aplicación y los archivos de manifiesto se crean mediante el Asistente para nuevos proyectos.

>[AZURE.NOTE] La forma más sencilla de empaquetar un ejecutable de Windows existente en un servicio es mediante Visual Studio.

## Uso de Visual Studio para empaquetar un ejecutable existente

Visual Studio proporciona una plantilla de servicio de Service Fabric para ayudarle a implementar un ejecutable invitado en un clúster de Service Fabric.

Siga estos pasos para completar la publicación:

1. Seleccione Archivo -> Nuevo proyecto y cree una aplicación de Service Fabric.
2. Elija Ejecutable invitado como plantilla de servicio.
3. Haga clic en Examinar para seleccionar la carpeta que contiene el ejecutable y rellene el resto de los parámetros para crear el servicio.
	- Se puede establecer *Comportamiento del paquete de código* para que copie todo el contenido de la carpeta en el proyecto de Visual Studio, lo que resulta útil si el ejecutable no cambia. En cambio, si prevee que el ejecutable cambie y desea tener la capacidad de recopilar las nuevas versiones de forma dinámica, puede vincular directamente a la carpeta. Puede usar carpetas vinculadas al crear el proyecto de aplicación en Visual Studio. Esto vincula a la ubicación de origen desde dentro del proyecto, lo que permite actualizar el ejecutable invitado en su destino de origen; así, esas actualizaciones formarán parte del paquete de aplicación en la compilación.
	- *Programa*: permite elegir el ejecutable que se debe ejecutar para iniciar el servicio.
	- *Argumentos*: permite especificar los argumentos que se deben pasar al ejecutable. Puede ser una lista de parámetros con argumentos.
	- *WorkingFolder*: especifica el directorio de trabajo para el proceso que se va a iniciar. Puede especificar tres valores:
		- `CodeBase` especifica que el directorio de trabajo se va a establecer en el directorio Code del paquete de aplicación (directorio `Code` en la estructura de archivos mostrada anteriormente).
		- `CodePackage` especifica que el directorio de trabajo se va a establecer en la raíz del paquete de aplicación (`GuestService1Pkg` en la estructura de archivos mostrada anteriormente).
		- `Work` especifica que los archivos se colocan en un subdirectorio denominado Work.
4. Asigne un nombre a su servicio y haga clic en Aceptar.
5. Si el servicio necesita un punto de conexión para establecer comunicación, ahora podrá agregar el protocolo, el puerto y el tipo al archivo ServiceManifest.xml. Por ejemplo: `<Endpoint Name="NodeAppTypeEndpoint" Protocol="http" Port="3000" UriScheme="http" PathSuffix="myapp/" Type="Input" />`.
6. Ya puede utilizar el paquete y publicar la acción en el clúster local mediante la depuración de la solución en Visual Studio. Cuando esté listo puede publicar la aplicación en un clúster remoto o comprobar la solución en el control de código fuente.
7. Vaya al final de este artículo para saber cómo ver el servicio ejecutable invitado que se ejecuta en Service Fabric Explorer.

<a id="manually"></a>
## Empaquetado e implementación manuales de un archivo ejecutable existente
El proceso de empaquetado manual de un ejecutable invitado se basa en los siguientes pasos:

1. Crear la estructura de directorios del paquete.
2. Agregar archivos de código y configuración de la aplicación
3. Editar el archivo de manifiesto de servicio.
4. Editar el archivo de manifiesto de aplicación.

<!--
>[AZURE.NOTE] We do provide a packaging tool that allows you to create the ApplicationPackage automatically. The tool is currently in preview. You can download it from [here](http://aka.ms/servicefabricpacktool).
-->

### Crear la estructura de directorios del paquete
Puede empezar creando la estructura de directorios del modo descrito anteriormente.

### Agregar archivos de código y la configuración de la aplicación
Después de haber creado la estructura de directorios, puede agregar los archivos de código y configuración de la aplicación en los directorios de código y configuración. También puede crear directorios adicionales o subdirectorios en los directorios code o config.

Service Fabric hace una copia del contenido del directorio raíz de la aplicación para que no haya ninguna estructura predefinida para usar aparte de creación de los dos directorios principales code y config. (Puede elegir diferentes nombres si lo desea. Obtenga más detalles en la sección siguiente.)

>[AZURE.NOTE] Asegúrese de incluir todos los archivos o dependencias que necesite la aplicación. Service Fabric copia el contenido del paquete de aplicación en todos los nodos del clúster en los que se implementarán los servicios de la aplicación. El paquete debe contener todo el código que la aplicación necesita para ejecutarse. No se recomienda dar por hecho que las dependencias ya están instaladas.

### Edición del archivo de manifiesto de servicio
El siguiente paso consiste en editar el archivo de manifiesto de servicio para incluir la siguiente información:

- El nombre del tipo de servicio Se trata de un identificador que Service Fabric usa para identificar un servicio.
- El comando que se usa para iniciar la aplicación (ExeHost)
- Cualquier script que se tenga que ejecutar para instalar y configurar la aplicación (SetupEntrypoint).

A continuación, se muestra un ejemplo de un archivo `ServiceManifest.xml`:

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

Repasemos las diferentes partes del archivo que tiene que actualizar:

#### Actualización de ServiceTypes

```xml
<ServiceTypes>
  <StatelessServiceType ServiceTypeName="NodeApp" UseImplicitHost="true" />
</ServiceTypes>
```

- Puede elegir cualquier nombre que desee para `ServiceTypeName`. El valor se usa en el archivo `ApplicationManifest.xml` para identificar el servicio.
- Debe especificar `UseImplicitHost="true"`. Este atributo indica a Service Fabric que el servicio se basa en una aplicación independiente, por lo que lo único que necesita hacer Service Fabric es iniciarla como un proceso y supervisar su estado.

#### Actualización de CodePackage
El elemento CodePackage especifica la ubicación (y versión) de código del servicio.

```xml
<CodePackage Name="Code" Version="1.0.0.0">
```

El elemento `Name` se usa para especificar el nombre del directorio en el paquete de aplicación que contiene el código del servicio. `CodePackage` también tiene el atributo `version`. Esto puede usarse para especificar la versión del código y también se podría usar para actualizar el código del servicio mediante la infraestructura de administración de ciclo de vida de aplicación de Service Fabric.
#### Opcional: Actualización de SetupEntrypoint

```xml
<SetupEntryPoint>
   <ExeHost>
       <Program>scripts\launchConfig.cmd</Program>
   </ExeHost>
</SetupEntryPoint>
```
El elemento SetupEntrypoint se usa para especificar cualquier archivo ejecutable o por lotes que se deba ejecutar antes de iniciar el código del servicio. Se trata de un paso opcional, por lo que no necesita incluirse si no hay ninguna inicialización/instalación obligatoria. SetupEntrypoint se ejecuta cada vez que se reinicia el servicio.

Hay un solo elemento SetupEntrypoint, por lo que los scripts de configuración/instalación deben agruparse en un solo archivo por lotes si la instalación/configuración de la aplicación requiere varios scripts. SetupEntrypoint puede ejecutar cualquier tipo de archivo: archivos ejecutables, archivos por lotes o cmdlets de PowerShell. Para más información, vea cómo [configurar SetupEntryPoint](service-fabric-application-runas-security.md).

En el ejemplo anterior, SetupEntrypoint se ejecuta en un archivo por lotes llamado `LaunchConfig.cmd` que se encuentra en el subdirectorio `scripts` del directorio Code (suponiendo que el elemento WorkingFolder esté establecido en CodeBase).

#### Actualización de Entrypoint

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

- `Program` especifica el nombre del ejecutable que se debe ejecutar para iniciar el servicio.
- `Arguments` especifica los argumentos que se deben pasar al archivo ejecutable. Puede ser una lista de parámetros con argumentos.
- `WorkingFolder` especifica el directorio de trabajo para el proceso que se va a iniciar. Puede especificar tres valores:
	- `CodeBase` especifica que el directorio de trabajo se va a establecer en el directorio Code del paquete de aplicación (directorio `Code` en la estructura de archivos anterior).
	- `CodePackage` especifica que el directorio de trabajo se va a establecer en la raíz del paquete de aplicación (`GuestService1Pkg` en la estructura de archivos anterior).
  - `Work` especifica que los archivos se colocan en un subdirectorio denominado Work.

WorkingFolder resulta útil para establecer el directorio de trabajo correcto, de modo que tanto los scripts de aplicación como los de inicialización puedan usar rutas de acceso relativas.

#### Actualización de puntos de conexión y registro mediante el servicio de nombres para la comunicación

```xml
<Endpoints>
   <Endpoint Name="NodeAppTypeEndpoint" Protocol="http" Port="3000" Type="Input" />
</Endpoints>

```
En el ejemplo anterior, el elemento `Endpoint` especifica los puntos de conexión en los que puede escuchar la aplicación. En este ejemplo, la aplicación Node.js escucha en http en el puerto 3000.

Además, puede pedir a Service Fabric que publique este punto de conexión en el servicio de nombres para que otros servicios puedan detectar la dirección del punto de conexión para este servicio. Esto le permite comunicarse entre los servicios que sean ejecutables invitados. La dirección del punto de conexión publicada tiene el formato `UriScheme://IPAddressOrFQDN:Port/PathSuffix`. `UriScheme` y `PathSuffix` son atributos opcionales. `IPAddressOrFQDN` es la dirección IP o el nombre de dominio completo del nodo en el que se coloca este ejecutable y que se calcula automáticamente.

En el ejemplo siguiente, una vez implementado el servicio, en Service Fabric Explorer verá un punto de conexión similar a `http://10.1.4.92:3000/myapp/` publicado para la instancia de servicio o, si se trata de un equipo local, verá `http://localhost:3000/myapp/`.

```xml
<Endpoints>
   <Endpoint Name="NodeAppTypeEndpoint" Protocol="http" Port="3000"  UriScheme="http" PathSuffix="myapp/" Type="Input" />
</Endpoints>
```
Puede usar estas direcciones con el [proxy inverso](service-fabric-reverseproxy.md) para comunicarse entre servicios.

### Edición del archivo de manifiesto de aplicación

Una vez que haya configurado el archivo `Servicemanifest.xml`, tendrá que realizar algunos cambios en el archivo `ApplicationManifest.xml` para asegurarse de que se use el tipo de servicio y el nombre correctos.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="NodeAppType" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="NodeApp" ServiceManifestVersion="1.0.0.0" />
   </ServiceManifestImport>
</ApplicationManifest>
```

#### ServiceManifestImport

En el elemento `ServiceManifestImport`, puede especificar uno o varios servicios para incluirlos en la aplicación. Se hace referencia a los servicios con `ServiceManifestName`, que especifica el nombre del directorio donde se encuentra el archivo `ServiceManifest.xml`.

```xml
<ServiceManifestImport>
  <ServiceManifestRef ServiceManifestName="NodeApp" ServiceManifestVersion="1.0.0.0" />
</ServiceManifestImport>
```

## Configuración de los registros
Para los ejecutables invitados, resulta útil poder ver los registros de la consola para averiguar si los scripts de la configuración y la aplicación muestran algún error. Se puede configurar el redireccionamiento de la consola en el archivo `ServiceManifest.xml` usando el elemento `ConsoleRedirection`.

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

* `ConsoleRedirection` puede usarse para redirigir las salidas de consola (stdout y stderr) a un directorio de trabajo para que puedan usarse para comprobar que no se ha producido ningún error durante la instalación o ejecución de la aplicación en el clúster de Service Fabric.

	* `FileRetentionCount` determina cuántos archivos se guardan en el directorio de trabajo. Un valor de 5 por ejemplo, significa que se almacenarán los archivos de registro de las cinco ejecuciones anteriores en el directorio de trabajo.
	* `FileMaxSizeInKb` especifica el tamaño máximo de los archivos de registro.

Los archivos de registro se guardan en uno de los directorios de trabajo del servicio. Para determinar dónde se encuentran los archivos, tendrá que usar Service Fabric Explorer para determinar cuál es el nodo en el que se está ejecutando el servicio y cuál es el directorio de trabajo que se está usando. Este proceso se trata más adelante en este artículo.

## Implementación
El último paso es implementar la aplicación. El siguiente script de PowerShell muestra cómo implementar la aplicación en el clúster de desarrollo local e iniciar un nuevo servicio de Service Fabric.

```PowerShell

Connect-ServiceFabricCluster localhost:19000

Write-Host 'Copying application package...'
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath 'C:\Dev\MultipleApplications' -ImageStoreConnectionString 'file:C:\SfDevCluster\Data\ImageStoreShare' -ApplicationPackagePathInImageStore 'nodeapp'

Write-Host 'Registering application type...'
Register-ServiceFabricApplicationType -ApplicationPathInImageStore 'nodeapp'

New-ServiceFabricApplication -ApplicationName 'fabric:/nodeapp' -ApplicationTypeName 'NodeAppType' -ApplicationTypeVersion 1.0

New-ServiceFabricService -ApplicationName 'fabric:/nodeapp' -ServiceName 'fabric:/nodeapp/nodeappservice' -ServiceTypeName 'NodeApp' -Stateless -PartitionSchemeSingleton -InstanceCount 1

```
Se puede implementar un servicio Service Fabric en diversas "configuraciones". Por ejemplo, puede implementarse como una o varias instancias o puede implementarse de manera que haya una instancia del servicio en cada nodo del clúster de Service Fabric.

El parámetro `InstanceCount` del cmdlet `New-ServiceFabricService` se usa para especificar cuántas instancias del servicio deben iniciarse en el clúster de Service Fabric. Puede establecer el valor `InstanceCount` según el tipo de aplicación que se vaya a implementar. Los dos escenarios más comunes son:

* `InstanceCount = "1"`. En este caso, solo se implementa una instancia del servicio en el clúster. El programador de Service Fabric determina en qué nodo se va a implementar el servicio.

* `InstanceCount ="-1"`. En este caso, se implementa una instancia del servicio en cada nodo del clúster de Service Fabric. El resultado final tiene una instancia (y solo una) del servicio para cada nodo del clúster.

Se trata de una configuración útil para las aplicaciones front-end (por ejemplo, un punto de conexión REST) porque las aplicaciones cliente necesitan "conectarse" a cualquiera de los nodos del clúster para usar el punto de conexión. Esta configuración también se puede usar cuando, por ejemplo, todos los nodos del clúster de Service Fabric están conectados a un equilibrador de carga para que el tráfico del cliente se distribuya en el servicio que se está ejecutando en todos los nodos del clúster.

## Comprobación de la aplicación en ejecución

En el explorador de Service Fabric, identifique el nodo en el que se está ejecutando el servicio. En este ejemplo, se ejecuta en el nodo 1:

![Nodo donde se ejecuta el servicio](./media/service-fabric-deploy-existing-app/nodeappinsfx.png)

Si navega hasta el nodo y accede a la aplicación, ve la información esencial del nodo, incluida su ubicación en el disco.

![Ubicación en el disco](./media/service-fabric-deploy-existing-app/locationondisk2.png)

Si examina el directorio mediante el Explorador de servidores, puede encontrar el directorio de trabajo y la carpeta de registros del servicio tal y como se muestra en la imagen siguiente.

![Ubicación del registro](./media/service-fabric-deploy-existing-app/loglocation.png)


## Pasos siguientes
En este artículo, ha aprendido a empaquetar un ejecutable invitado y a implementarlo en Service Fabric. Como siguiente paso, puede consultar contenido adicional sobre este tema.

- [Ejemplo para empaquetar e implementar un ejecutable invitado en GitHub](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/tree/master/GuestExe/SimpleApplication), que incluye un vínculo a la versión preliminar de la herramienta de empaquetado
- [Implementación de varios ejecutables invitados](service-fabric-deploy-multiple-apps.md)
- [Creación de la primera aplicación de Service Fabric en Visual Studio](service-fabric-create-your-first-application-in-visual-studio.md)

<!---HONumber=AcomDC_0928_2016-->