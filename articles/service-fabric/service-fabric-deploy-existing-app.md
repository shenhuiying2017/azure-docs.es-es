<properties
   pageTitle="Implementación de una aplicación existente en Azure Service Fabric | Microsoft Azure"
   description="Tutorial sobre cómo empaquetar una aplicación existente para implementarla en un clúster de Azure Service Fabric"
   services="service-fabric"
   documentationCenter=".net"
   authors="clca"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/09/2015"
   ms.author="claudioc"/>

# Implementación de una aplicación existente en Azure Service Fabric

Azure Service Fabric puede usarse para implementar aplicaciones existentes, les permite beneficiarse de la supervisión de estado lo que y de la administración del ciclo de vida de las aplicaciones (ALM).

En este tutorial se explica el proceso y los conceptos básicos implicados en el empaquetado de una aplicación existente para su implementación en un clúster de Service Fabric.


## Introducción rápida de los archivos de manifiesto de servicio y aplicación

Antes de entrar en los detalles de la implementación de una aplicación existente, resulta útil comprender el modelo de implementación de Service Fabric. El modelo de implementación de Service Fabric se basa principalmente en dos archivos:


* **Manifiesto de aplicación**

  El manifiesto de aplicación se usa para describir la aplicación y enumera los servicios que la componen junto con otros parámetros, como el número de instancias, que se usan para definir cómo se deben implementar los servicios. En el mundo de Service Fabric, la aplicaciones son “unidades que se pueden actualizar”. Es posible actualizar una aplicación como una sola unidad en la que los posibles errores (y posibles reversiones) son administrados por la plataforma para garantizar que el proceso de actualización sea completamente satisfactorio o, si se produce un error, no deje a la aplicación en un estado desconocido o inestable.

  Este es un ejemplo de un manifiesto de aplicación:

  ```xml <?xml version="1.0" encoding="utf-8"?> <ApplicationManifest ApplicationTypeName="actor2Application" ApplicationTypeVersion="1.0.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">

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

  </ApplicationManifest> ```

* **Manifiesto de servicio**

  El manifiesto de servicio describe los componentes de un servicio. Incluye datos como el nombre y tipo de servicio (información que Service Fabric usa para administrar el servicio), su código, configuración y componentes de datos y algunos parámetros adicionales que pueden usarse para configurar el servicio una vez que se implementa. No vamos a entrar en detalles sobre todos los distintos parámetros disponibles en el manifiesto de servicio, revisaremos el subconjunto que se requiere para que una aplicación existente se ejecute en Service Fabric

  Este es un ejemplo de un manifiesto de servicio

  ```xml <?xml version="1.0" encoding="utf-8"?> <ServiceManifest Name="actor2Pkg" Version="1.0.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"> <ServiceTypes> <StatelessServiceType ServiceTypeName="actor2Type" /> </ServiceTypes>

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
  </ServiceManifest> ```

## Estructura del archivo del paquete de aplicación
Para implementar una aplicación mediante, por ejemplo, los cmdlets de Powershell, la aplicación debe seguir una estructura de directorios predefinida.

```
\applicationmanifest.xml
\MyServicePkg
    \servicemanifest.xml
    \code
    \config
    \data
```

La raíz contiene el archivo applicationmanifest.xml que define la aplicación. Se usa un subdirectorio para cada servicio incluido en la aplicación para contener todos los artefactos que requiere el servicio: el servicemanifest.xml y, por lo general, 3 directorios:

- *code*: contiene el código de servicio.
- *config*: contiene un archivo settings.xml (y otros archivos si es necesario) a los que el servicio puede tener acceso en tiempo de ejecución para recuperar la configuración específica.
- *data*: un directorio adicional para almacenar datos locales adicionales que el servicio puede necesitar. Nota: Se deben usar datos para almacenar solo los datos ephymeral. Service Fabric no copia/replica los cambios en el directorio de datos si el servicio debe reubicarse, por ejemplo, durante la conmutación por error.

Nota: Puede usar cualquier nombre de directorio arbitrario para Código, Configuración y Datos. Solo tiene que asegurarse de usar el mismo valor en el archivo ApplicationManifest.

## Proceso de empaquetado de una aplicación existente

El proceso de empaquetado de una aplicación existente se basa en los siguientes pasos:

- Crear la estructura de directorios del paquete
- Agregar archivos de código y la configuración de la aplicación
- Actualizar el archivo de manifiesto de servicio
- Actualizar el manifiesto de aplicación


### Crear la estructura de directorios del paquete
Puede empezar creando la estructura de directorios del modo descrito anteriormente. He creado un directorio y he copiado la aplicación y el manifiesto de servicio desde un proyecto existente que anteriormente había creado en Visual Studio.

![][1] ![][2]


### Agregar archivos de código y la configuración de la aplicación
Después de haber creado la estructura de directorios, puede agregar los archivos de código y configuración de la aplicación en el directorio de código y configuración. También puede crear directorios adicionales o subdirectorios en los directorios de código o configuración. Service Fabric hace una copia del contenido del directorio raíz de la aplicación para que no haya ninguna estructura predefinida para usar aparte de crear dos directorios principales de Código y Configuración (pero puede elegir diferentes nombres si lo desea; obtenga más detalles en la sección siguiente).

>[AZURE.NOTE]\: asegúrese de incluir todos las archivos/dependencias que necesite la aplicación. Servicio Fabric copiará el contenido del paquete de aplicación en todos los nodos del clúster en los que se implementarán los servicios de la aplicación. El paquete debe contener todo el código que la aplicación necesita para ejecutarse. No se recomienda dar por hecho que las dependencias ya están instaladas.

### Editar el archivo de manifiesto de servicio
El siguiente paso consiste en editar el archivo de manifiesto de servicio para incluir la siguiente información:

- El nombre del tipo de servicio Se trata de un 'identificador' que usa Service Fabric para identificar un servicio.
- El comando que se usa para iniciar la aplicación (ExeHost)
- Cualquier script que se debe ejecutar para instalar y configurar la aplicación (SetupEntrypoint

Este es un ejemplo de un archivo `servicemanifest.xnml` que "empaqueta" una aplicación node.js:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="VisualObjectsNodejsWebServicePkg"
                 Version="1.0.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <StatelessServiceType ServiceTypeName="VisualObjectsNodejsWebServiceType" UseImplicitHost="true" />
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

  <ConfigPackage Name="Config" Version="1.0.0.0"/>

  <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpoint" />
    </Endpoints>
  </Resources>

</ServiceManifest>
```

Repasemos la parte diferente del archivo que necesita actualizar:

### ServiceTypes:

```xml
<ServiceTypes>
  <StatelessServiceType ServiceTypeName="VisualObjectsNodejsWebServiceType" UseImplicitHost="true" />
</ServiceTypes>
```

- Puede elegir cualquier nombre que desee para `ServiceTypeName`. El valor se usa en el `applicationmanifest.xml` para identificar el servicio.
- Debe especificar `UserImplicitHost = "true"`. Este atributo indica a Service Fabric que el servicio se basa en una aplicación independiente, por lo que solo necesita iniciarla como un proceso y supervisar su estado.

### CodePackage
El CodePackage especifica la ubicación (y versión) de código del servicio.

```xml
<CodePackage Name="Code" Version="1.0.0.0">
```

El elemento `Name` se usa para especificar el nombre del directorio en el paquete de aplicación que contiene el código del servicio. `CodePackage` también tiene el atributo `version` que puede usarse para especificar la versión del código y, potencialmente, para actualizar el código de servicio mediante la infraestructura de ALM de Service Fabric.


### Punto de entrada

```xml
<EntryPoint>
  <ExeHost>
    <Program>node.exe</Program>
    <Arguments>server.js</Arguments>
    <WorkingFolder>CodeBase</WorkingFolder>
  </ExeHost>
</EntryPoint>
```


El elemento `Entrypoint` del archivo de manifiesto de servicio se usa para especificar cómo iniciar el servicio. El elemento `ExeHost` especifica el archivo ejecutable (y los argumentos) que deben usarse para iniciar el servicio.

- `Program`: especifica el nombre del archivo ejecutable que se debe ejecutar para iniciar el servicio.
- `Arguments`: especifica los argumentos que se deben pasar al archivo ejecutable. Puede ser una lista de parámetros con argumentos.
- `WorkingFolder`: especifica el directorio de trabajo para el proceso que se va a iniciar. Puede especificar dos valores:
	- `CodeBase`: el directorio de trabajo se va a establecer en el directorio "code" del paquete de aplicación (directorio `Code` en la estructura que se muestra a continuación).
	- `CodePackage`: el directorio de trabajo se establecerá en la raíz del paquete de aplicación (`MyServicePkg`).
- El elemento `WorkingDirectory` es útil para establecer el directorio de trabajo correcto de modo que las rutas de acceso relativas se puedan usar por los scripts de aplicación o inicialización.

También hay otro valor que puede especificar para el elemento `WorkingFolder` (`Work`), pero no es muy útil para traer una aplicación existente.


```
\applicationmanifest.xml
\MyServicePkg
	\servicemanifest.xml
	\code
		 \bin
			  \ ...
	\config
	\data
		\...
```


#### Punto de entrada del programa de instalación

```xml
<SetupEntryPoint>
  <ExeHost>
    <Program>scripts\myAppsetup.cmd</Program>
  </ExeHost>
</SetupEntryPoint>
```

`SetupEntrypoint` se usa para especificar cualquier archivo ejecutable o por lotes que se deba ejecutar antes de iniciar el código del servicio. Se trata de un elemento opcional, por lo que no necesita incluirse si no hay ninguna inicialización/instalación obligatoria. El punto de entrada se ejecuta cada vez que se reinicia el servicio. Hay solo un SetupEntrypoint, por lo que los script de configuración/instalación deben incluirse en un solo archivo de lote si la instalación/configuración de la aplicación requiere varios scripts. Al igual que con el elemento `Entrypoint`, `SetupEntrypoint` puede ejecutar cualquier tipo de archivo: archivo ejecutable, archivos por lotes, cmdlet de Powershell. En el ejemplo anterior, `SetupEntrypoint` se basa en un archivo de lotes myAppsetup.cmd que se encuentra en el subdirectorio de scripts del directorio "code" (suponiendo que el elemento `WorkingDirectory` esté establecido en `Code`).

## Archivo de manifiesto de aplicación

Una vez haya configurado el archivo `servicemanifest.xml`, deberá realizar algunos cambios en el archivo `applicationmanifest.xml` para asegurarse de que se usan el tipo de servicio y el nombre correctos.

```xml
<ServiceManifestImport>
  <ServiceManifestRef ServiceManifestName="MyServicePkg" ServiceManifestVersion="1.0.0.0" />
</ServiceManifestImport>
<DefaultServices>
  <Service Name="actor2">
    <StatelessService ServiceTypeName="MyServiceType" InstanceCount = "1">
    </StatelessService>
  </Service>
</DefaultServices>
```

### ServiceManifestImport

En `ServiceManifestImport`, puede especificar uno o más servicios que desea incluir en la aplicación. Se hace referencia a los servicios con `ServiceManifestName`, que especifica el nombre del directorio donde se encuentra el archivo `servicemanifest.xml`.

```xml
<ServiceManifestImport>
  <ServiceManifestRef ServiceManifestName="MyServicePkg" ServiceManifestVersion="1.0.0.0" />
</ServiceManifestImport>
```

### DefaultServices

El elemento `DefaultServices` del archivo de manifiesto de aplicación se usa para definir algunas propiedades del servicio.

```xml
<DefaultServices>
  <Service Name="actor2">
    <StatelessService ServiceTypeName="MyServiceType" InstanceCount="1">
    </StatelessService>
  </Service>
</DefaultServices>
```

* `ServiceTypeName` se usa como "identificador" para el servicio. En el contexto de trasladar una aplicación existente, `ServiceTypeName` solo necesita ser una identificación única para el servicio.
* `StatelessService`: Service Fabric admite dos tipos de servicios: con y sin estado. En el caso de trasladar una aplicación existente, el servicio es un servicio sin estado, por lo que `StatelessService` deberá usarse siempre.

Es posible implementar un servicio Service Fabric en diversas “configuraciones”, por ejemplo, puede implementarse como una o varias instancias o se puede implementar de manera que haya una instancia del servicio en cada nodo del clúster de Service Fabric. En el archivo `applicationmanifest.xml`, puede especificar cómo desea que se implemente la aplicación.

* `InstanceCount`: se usa para especificar cuántas instancias del servicio deben iniciarse en el clúster de Service Fabric. Puede establecer el valor `InstanceCount` según el tipo de aplicación que se va a implementar. Los dos escenarios más comunes son:

	* `InstanCount = "1"`: en este caso, solo se implementará una instancia del servicio en el clúster. El programador de Service Fabric determina en qué nodo se va a implementar el servicio. Un recuento de instancia única también es recomendable para aplicaciones que requieren una configuración diferente si se ejecutan en varias instancias. En ese caso, es más fácil definir varios servicios en el mismo archivo de manifiesto de aplicación y usar `InstanceCount = "1"`. Por lo que el resultado final será tener varias instancias del mismo servicio, pero cada una con una configuración concreta. Un valor de `InstanceCount` mayor que uno solo tiene sentido si el objetivo es tener varias instancias de la misma configuración exacta.

	* `InstanceCount ="-1"`: en este caso se implementará una instancia del servicio en cada nodo del clúster de Service Fabric. El resultado final tendrá una instancia (y sólo una) del servicio para cada nodo del clúster. Se trata de una configuración útil para las aplicaciones front-end (p. ej., un extremo REST) porque las aplicaciones cliente solo necesitan “conectarse” a cualquiera de los nodos del clúster para poder usar el extremo. Esta configuración también se puede usar cuando, por ejemplo, todos los nodos del clúster de Service Fabric están conectados a un equilibrador de carga para que el tráfico del cliente se pueda distribuir en el servicio que se está ejecutando en todos los nodos del clúster.


### Prueba
Para la aplicación existente es muy útil poder ver los registros de la consola para averiguar si los script de la configuración y la aplicación no muestran ningún error. Se puede configurar el redireccionamiento de la consola en el archivo `servicemanifest.xml` usando el elemento `ConsoleRedirection`.

```xml
<EntryPoint>
  <ExeHost>
    <Program>node.exe</Program>
    <Arguments>server.js</Arguments>
    <WorkingFolder></WorkingFolder>
    <ConsoleRedirection FileRetentionCount="5" FileMaxSizeInKb="2048"/>
  </ExeHost>
</EntryPoint>
```

* `ConsoleRedirection` puede usarse para redirigir la salida de consola (stdout y stderr) a un directorio de trabajo para que puedan usarse para comprobar que no hay ningún error durante la instalación o ejecución de la aplicación en el clúster de Service Fabric.

	* `FileRetentionCount` determina cuántos archivos se guardan en el directorio de trabajo. Un valor de 5 por ejemplo, significa que se almacenarán los archivos de registro para las 5 ejecuciones anteriores en el directorio de trabajo.
	* `FileMaxSizeInKb` especifica el tamaño máximo de los archivos de registro.

Los archivos de registro se guardan en uno de los directorios de trabajo del servicio. Para determinar dónde se encuentran los archivos, deberá usar el Explorador de Service Fabric para determinar en qué nodo se está ejecutando el servicio y cuál es el directorio de trabajo que se usa actualmente.

En el Explorador de Service Fabric, identifique el nodo en el que se está ejecutando el servicio

![][3]

Una vez que conozca el nodo donde se está ejecutando el servicio, puede averiguar qué directorio de trabajo se está usando

![][4]

Al seleccionar el nombre del servicio, en el panel derecho podrá ver dónde están almacenados el código del servicio y la configuración

![][5]

Si hace clic en el vínculo del campo “Ubicación del disco”, puede tener acceso al directorio en el que se están ejecutando los servicios.

![][6]

El directorio de registro contiene todos los archivos de registro.

Nota: En este ejemplo se muestra el caso de una única instancia del servicio que se está ejecutando en el clúster. Si hay varias instancias, puede que necesite comprobar el archivo de registro en todos los nodos en los que se está ejecutando el servicio.


## Pasos siguientes
Estamos trabajando en una herramientas que sirva para empaquetar una aplicación existente simplemente orientándola a la raíz de la estructura de directorios de la aplicación. La herramienta se encarga de generar los archivos de manifiesto y establecer la configuración básica que se requiere para “transformar” la aplicación en un servicio de Service Fabric.

Consulte [esto](service-fabric-develop-your-service-index.md) si desea obtener más información sobre cómo desarrollar una aplicación Service Fabric tradicional.

[1]: ./media/service-fabric-deploy-existing-app/directory-structure-1.png
[2]: ./media/service-fabric-deploy-existing-app/directory-structure-2.png
[3]: ./media/service-fabric-deploy-existing-app/service-node-1.png
[4]: ./media/service-fabric-deploy-existing-app/service-node-2.png
[5]: ./media/service-fabric-deploy-existing-app/service-node-3.png
[6]: ./media/service-fabric-deploy-existing-app/service-node-4.png

<!---HONumber=Oct15_HO3-->