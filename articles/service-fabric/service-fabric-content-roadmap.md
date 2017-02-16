---
title: "Información acerca de Azure Service Fabric | Microsoft Docs"
description: "Información general y guía de introducción a Service Fabric, donde las aplicaciones se componen de muchos microservicios para ofrecer escalado y resiliencia."
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/09/2017
ms.author: ryanwi
translationtype: Human Translation
ms.sourcegitcommit: 7033955fa9c18b2fa1a28d488ad5268d598de287
ms.openlocfilehash: 8e7c14d9eeeab43d596eec99cb380f06cf9c39b3


---
# <a name="so-you-want-to-learn-about-service-fabric"></a>¿Qué desea saber sobre Service Fabric?
Esta ruta de aprendizaje le ayudará a comenzar a desarrollar aplicaciones escalables, confiables y fácilmente administradas en Service Fabric.

## <a name="the-five-minute-overview"></a>Información general en cinco minutos
Azure Service Fabric es una plataforma de sistemas distribuidos que facilita el empaquetado, la implementación y la administración de microservicios escalables y de confianza, además de abordar los importantes desafíos a los que se enfrentan el desarrollo y la administración de aplicaciones en la nube. Mediante el uso de Service Fabric, los desarrolladores y administradores pueden evitar solucionar problemas complejos de infraestructura y centrarse en su lugar en la implementación de cargas de trabajo exigentes y críticas, sabiendo que son escalables, confiables y fáciles de administrar. Service Fabric representa la plataforma middleware de próxima generación para crear y administrar estas aplicaciones de clase empresarial, de escala de nube de nivel&1;.  

Este breve vídeo de Channel9 presenta Service Fabric y los microservicios: <center><a target="_blank" href="https://aka.ms/servicefabricvideo">  
<img src="./media/service-fabric-content-roadmap/OverviewVid.png" WIDTH="360" HEIGHT="244">  
</a></center>

## <a name="the-detailed-overview"></a>Información detallada
Service Fabric le permite compilar y administrar aplicaciones escalables y confiables compuestas por microservicios que se ejecutan con una densidad muy alta en un grupo compartido de máquinas; que se denomina clúster. Ofrece un sofisticado tiempo de ejecución para crear microservicios con y sin estado, distribuidos y escalables. También ofrece funciones de administración de aplicaciones completas para aprovisionar, implementar, supervisar, actualizar/aplicar revisiones y eliminar aplicaciones implementadas.  Lea [Información general acerca de Service Fabric](service-fabric-overview.md) para obtener más información.

¿Por qué un diseño de microservicios? Todas las aplicaciones evolucionan con el tiempo. Las aplicaciones que triunfan evolucionan por ser útiles a sus usuarios. ¿Cuánto sabe acerca de sus requisitos actuales y cuáles serán en el futuro? A veces, terminar un servicio como prueba de concepto es el factor determinante (sabiendo que el diseño de la aplicación podrá modificarse más adelante). Por otra parte, cuando las empresas hablan sobre la creación de aplicaciones para la nube, lo que esperan es crecimiento y uso. El problema es que el crecimiento y la escala son imprevisibles. Nos gustaría poder crear un prototipo rápidamente sabiendo que la aplicación se puede escalar para responder a un uso y un crecimiento impredecibles.  [¿Qué son los microservicios? ](service-fabric-overview-microservices.md), describe la manera en que el diseño de microservicios aborda estos desafíos y cómo crear microservicios que se puedan escalar verticalmente u horizontalmente, probar, implementar y administrar de forma independiente.

Service Fabric es una plataforma confiable y flexible que le permite escribir y ejecutar muchos tipos de aplicaciones y servicios empresariales.  También puede ejecutar cualquiera de sus aplicaciones existentes (escritas en cualquier lenguaje).  Estas aplicaciones y microservicios pueden tener estado o no tenerlo, y se equilibran mediante recursos en las máquinas virtuales para maximizar su eficacia. La arquitectura única de Service Fabric le permite realizar análisis de datos casi en tiempo real, cálculo en memoria, transacciones paralelas y procesamiento de eventos en sus aplicaciones. Puede escalar o reducir verticalmente sus aplicaciones con facilidad (en realidad hacia dentro o hacia fuera), dependiendo de sus requisitos de recursos variables. Lea [Escenarios de aplicación](service-fabric-application-scenarios.md) para obtener información acerca de las categorías de aplicaciones y servicios que puede crear, así como casos prácticos de clientes.

En este vídeo más largo de Microsoft Virtual Academy se describen los conceptos básicos de Service Fabric: <center><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=tbuZM46yC_5206218965">  
<img src="./media/service-fabric-content-roadmap/CoreConceptsVid.png" WIDTH="360" HEIGHT="244">  
</a></center>

## <a name="get-started-and-create-your-first-app"></a>Introducción y creación de la primera aplicación 
Con el SDK y las herramientas de Service Fabric, puede desarrollar aplicaciones en entornos Windows, Linux o Mac OS e implementarlas en clústeres que se ejecutan en Windows o Linux.  Las siguientes guías le ayudarán a implementar una aplicación en minutos.  Una vez ejecutada la primera aplicación, descargue y ejecute algunas de nuestras [aplicaciones de ejemplo](http://aka.ms/servicefabricsamples).

### <a name="on-windows"></a>En Windows
El SDK de Service Fabric incluye un complemento para Visual Studio que proporciona herramientas y plantillas para crear, implementar y depurar aplicaciones de Service Fabric. Estos temas le guiarán por el proceso de creación de su primera aplicación en Visual Studio, y su ejecución en el equipo de desarrollo.

[Configuración del entorno de desarrollo](service-fabric-get-started.md)
[Creación de la primera aplicación (C#)](service-fabric-create-your-first-application-in-visual-studio.md)

Pruebe este completo [laboratorio de prácticas](https://msdnshared.blob.core.windows.net/media/2016/07/SF-Lab-Part-I.docx) para familiarizarse con el flujo de desarrollo completo de Service Fabric.  Aprenda a crear un servicio sin estado, configure informes de supervisión y estado, y realice una actualización de la aplicación. 

El siguiente vídeo de Channel9 le guiará por el proceso de creación de una aplicación de C# en Visual Studio:  
<center><a target="_blank" href="https://channel9.msdn.com/Blogs/Azure/Creating-your-first-Service-Fabric-application-in-Visual-Studio">  
<img src="./media/service-fabric-content-roadmap/first-app-vid.png" WIDTH="360" HEIGHT="244">  
</a></center>

### <a name="on-linux"></a>En Linux
Service Fabric ofrece SDK para compilar servicios en Linux tanto en .NET Core como Java. Estos temas le guiarán por el proceso de creación de su primera aplicación de Java o C# en Linux, y su ejecución en el equipo de desarrollo.
[Configuración del entorno de desarrollo](service-fabric-get-started-linux.md)
[Creación de la primera aplicación (C#)](service-fabric-create-your-first-linux-application-with-java.md)
[Creación de la primera aplicación (C#)](service-fabric-create-your-first-linux-application-with-csharp.md)

El siguiente vídeo de Microsoft Virtual Academy le guiará a través del proceso de creación de una aplicación de Java en Linux:  
<center><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=DOX8K86yC_206218965">  
<img src="./media/service-fabric-content-roadmap/LinuxVid.png" WIDTH="360" HEIGHT="244">  
</a></center>

### <a name="on-macos"></a>En MacOS
Puede crear aplicaciones de Service Fabric en Mac OS X que se ejecutan en clústeres de Linux. En estos artículos se describe cómo configurar su Mac para desarrollo y le guiarán por el proceso de creación de su primera aplicación de Java en MacOS, y su ejecución en una máquina virtual de Ubuntu.
[Configuración del entorno de desarrollo](service-fabric-get-started-mac.md)
[Creación de la primera aplicación (Java)](service-fabric-create-your-first-linux-application-with-java.md)

## <a name="core-concepts"></a>Conceptos principales
[Terminología de Service Fabric](service-fabric-technical-overview.md), [Modelo de aplicación](service-fabric-application-model.md) y [Modelos de programación admitidos](service-fabric-choose-framework.md) ofrecen varios conceptos y descripciones, pero estos son los conceptos básicos.

<table><tr><th>Conceptos principales</th><th>Tiempo de diseño</th><th>Tiempo de ejecución</th></tr>
<tr><td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=tbuZM46yC_5206218965">
<img src="./media/service-fabric-content-roadmap/CoreConceptsVid.png" WIDTH="240" HEIGHT="162"></a></td>
<td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=tlkI046yC_2906218965"><img src="./media/service-fabric-content-roadmap/RunTimeVid.png" WIDTH="240" HEIGHT="162"></a></td>
<td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=x7CVH56yC_1406218965">
<img src="./media/service-fabric-content-roadmap/RunTimeVid.png" WIDTH="240" HEIGHT="162"></a></td></tr>
</table>

### <a name="design-time-app-type-service-type-app-package-and-manifest-service-package-and-manifest"></a>Tiempo de diseño: tipo de aplicación, tipo de servicio, paquete y manifiesto de aplicación, paquete y manifiesto de servicio.
Un tipo de aplicación es el nombre o versión asignados a una colección de tipos de servicios. Esta información se define en un archivo ApplicationManifest.xml, insertado en un directorio del paquete de aplicación, que luego se copia en el almacén de imágenes del clúster de Service Fabric. Después, puede crear una aplicación con nombre a partir de este tipo de aplicación y ejecutarla después en el clúster. 

Un tipo de servicio es el nombre o versión asignados a los paquetes de código, paquetes de datos y paquetes de configuración de un servicio. Se define en un archivo ServiceManifest.xml, insertado en un directorio del paquete de servicio, al que después hace referencia un archivo ApplicationManifest.xml del paquete de aplicación. En el clúster, después de crear una aplicación con nombre se pude crear un servicio con nombre desde uno de los tipos de servicio del tipo de aplicación. Un tipo de servicio se describe en un archivo ServiceManifest.xml y se compone de las opciones de configuración del servicio del código ejecutable que se cargan en tiempo de ejecución, y los datos estáticos que el servicio consume.

![Tipos de aplicaciones de Service Fabric y tipos de servicio][cluster-imagestore-apptypes]

El paquete de aplicación es un directorio de disco que contiene el archivo ApplicationManifest.xm del tipo de aplicación, que hace referencia a los paquetes de servicio para cada tipo de servicio que compone el tipo de aplicación. Por ejemplo, un paquete de aplicación de un tipo de aplicación de correo electrónico puede contener referencias a un paquete de servicios de cola, un paquete de servicios de front-end y un paquete de servicios de base de datos. Los archivos en el directorio del paquete de aplicación se copian en el almacén de imágenes del clúster de Service Fabric. 

Un paquete de servicio es un directorio de disco que contiene el archivo ServiceManifest.xml del tipo de servicio, que hace referencia al código, los datos estáticos y los paquetes de configuración del tipo de servicio. El archivo ApplicationManifest.xml del tipo de aplicación hace referencia a los archivos del directorio del paquete de servicio. Por ejemplo, un paquete de servicio puede hacer referencia al código, a los datos estáticos y a los paquetes de configuración que conforman un servicio de base de datos.

### <a name="run-time-clusters-and-nodes-named-apps-named-services-partitions-and-replicas"></a>Tiempo de ejecución: clústeres y nodos, aplicaciones con nombre, servicios con nombre, particiones y réplicas.
Un [clúster de Service Fabric](service-fabric-deploy-anywhere.md) es un conjunto de máquinas físicas o virtuales conectadas a la red, en las que se implementan y administran los microservicios. Los clústeres pueden escalar a miles de equipos.

Cada una de las máquinas físicas o virtuales que forman parte de un clúster se denominan nodo. A cada nodo se le asigna un nombre de nodo (una cadena). Los nodos tienen características como las propiedades de colocación. Cada máquina física o virtual tiene un servicio de Windows de inicio automático, FabricHost.exe, que empieza a ejecutarse en el arranque y luego inicia dos ejecutables: Fabric.exe y FabricGateway.exe. Estos dos ejecutables conforman el nodo. En los escenarios de prueba o desarrollo, se pueden hospedar varios nodos en una sola máquina física o virtual mediante la ejecución de varias instancias de Fabric.exe y FabricGateway.exe.

Una aplicación con nombre es una colección de servicios con nombre que realizan una o varias funciones determinadas. Un servicio realiza una función completa e independiente (puede iniciarse y ejecutarse independientemente de otros servicios) y se compone de código, configuración y datos. Una vez que un paquete de aplicación se copia en el almacén de imágenes, se crea una instancia de la aplicación en el clúster especificando el tipo de aplicación del paquete de aplicación (mediante su nombre o versión). A cada instancia del tipo de aplicación se le asigna un nombre de URI similar al siguiente: *fabric:/MyNamedApp*. En un clúster se pueden crear varias aplicaciones con nombre de un único tipo de aplicación. También se pueden crear aplicaciones con nombre de diferentes tipos de aplicación. Cada aplicación con nombre se administra de manera independiente y tiene versiones independientes.

Después de crear una aplicación con nombre puede crear una instancia de uno de sus tipos de servicio (un servicio con nombre) en el clúster, especificando el tipo de servicio (con su nombre o versión). A cada instancia del tipo de servicio se le asigna un nombre de URI cuyo ámbito es el URI de su aplicación con nombre. Por ejemplo, si crea un servicio con nombre "MyDatabase" en la aplicación con nombre "MyNamedApp", el URI será similar al siguiente: *fabric:/MyNamedApp/MyDatabase*. En una aplicación con nombre, puede crear uno o varios servicios con nombre. Todos los servicios con nombre pueden tener su propio esquema de partición y cuentas de instancia o réplica. 

Hay dos tipos de servicios: con y sin estado.  El estado persistente del servicio sin estado se almacena en un servicio de almacenamiento externo como Azure Storage, Azure SQL Database o Azure DocumentDB. Utilice un servicio sin estado en el caso de que el servicio no tenga almacenamiento persistente. Un servicio con estado usa Service Fabric para administrar el estado del servicio mediante sus modelos de programación Reliable Collections o Reliable Actors.  

Al crear un servicio con nombre, especifique un esquema de partición. Los servicios con grandes cantidades de estado dividen los datos entre las particiones que los distribuyen entre los nodos del clúster. Esto permite el escalado del estado del servicio con nombre. Dentro de una partición, los servicios con nombre sin estado tienen instancias, mientras que los servicios con nombre con estado tienen réplicas. Normalmente, los servicios con nombre sin estado solo tienen una partición, ya que no tienen un estado interno. Las instancias de la partición se proporcionan para disponibilidad; si se produce un error en una instancia, otras instancias seguirán operando con normalidad y luego Service Fabric creará una nueva instancia. Los servicios con nombre con estado mantienen su estado dentro de las réplicas y cada partición tiene su propio conjunto de réplicas en los que todos los estados se mantienen sincronizados. Si se produce algún error en una réplica, Service Fabric creará una nueva réplica a partir de las réplicas existentes.

En el siguiente diagrama se muestra la relación entre aplicaciones e instancias de servicio, particiones y réplicas.

![Particiones y réplicas dentro de un servicio][cluster-application-instances]

## <a name="supported-programming-models"></a>Modelos de programación admitidos
Service Fabric ofrece varias maneras de escribir y administrar los servicios. Los servicios pueden elegir usar las API de Service Fabric para aprovechar al máximo las características y los marcos de aplicaciones de la plataforma, o los servicios pueden ser simplemente un programa ejecutable compilado escrito en cualquier lenguaje y hospedado simplemente en un clúster de Service Fabric. Para más información, consulte [Modelos de programación admitidos](service-fabric-choose-framework.md).

### <a name="guest-executables"></a>Ejecutables de invitado
Un [ejecutable de invitado](service-fabric-deploy-existing-app.md) es un ejecutable arbitrario, escrito en cualquier lenguaje, por lo que puede tomar las aplicaciones existentes y hospedarlas en un clúster de Service Fabric junto con otros servicios. Sin embargo, como los ejecutables de invitado no se integran directamente con las API de Service Fabric, no se benefician del conjunto completo de características que ofrece la plataforma, como el estado personalizado y la carga de informes, el punto de conexión de servicio y el proceso con estado.

### <a name="containers"></a>Contenedores
De forma predeterminada, Service Fabric implementa y activa estos servicios como procesos. Service Fabric puede implementar también servicios en [imágenes de contenedor](service-fabric-containers-overview.md). Lo importantes es que puede mezclar servicios en procesos y servicios en contenedores en la misma aplicación.  Service Fabric admite actualmente la implementación de contenedores de Docker en contenedores de Linux y Windows Server en Windows Server 2016. En el modelo de aplicación de Service Fabric, un contenedor representa un host de la aplicación en el que se colocan varias réplicas de servicio.  Puede usar Service Fabric para implementar aplicaciones existentes, servicios sin estado o servicios con estado en un contenedor.  

### <a name="reliable-services"></a>Reliable Services
[Reliable Services](service-fabric-reliable-services-introduction.md) es un entorno de trabajo ligero para escribir servicios que se integran con la plataforma Service Fabric y que se benefician de todo el conjunto de características de la plataforma. Reliable Services puede no tener estado (de forma similar a la mayoría de las plataformas de servicios, como servidores web o roles de trabajo de Azure Cloud Services), y el estado se conserva en una solución externa, como Azure DB o Azure Table Storage. Reliable Services también puede tener estado, y este se conserva directamente en el propio servicio mediante Reliable Collections. El estado cuenta con una alta disponibilidad mediante la replicación y se distribuye a través de particiones, todo administrado automáticamente por Service Fabric.

### <a name="reliable-actors"></a>Reliable Actors
Basado en Reliable Services, el entorno de [Reliable Actor](service-fabric-reliable-actors-introduction.md) es un entorno de aplicación que implementa el modelo de Virtual Actor, basado en el modelo de diseño de actores. El marco de Reliable Actor usa unidades independientes del proceso y el estado con la ejecución de subproceso único denominadas actores. El marco de Reliable Actor proporciona comunicaciones integradas para actores y las configuraciones de escalado horizontal y persistencia de estado establecidas previamente.

## <a name="next-steps"></a>Pasos siguientes
* Aprenda a crear un [clúster en Azure](service-fabric-cluster-creation-via-portal.md) o un [clúster independiente en Windows](service-fabric-cluster-creation-for-windows-server.md).
* Intente crear un servicio con los modelos de programación [Reliable Services](service-fabric-reliable-services-quick-start.md) o [Reliable Actors](service-fabric-reliable-actors-get-started.md).
* Más información acerca del [ciclo de vida de la aplicación](service-fabric-application-lifecycle.md).
* Más información acerca de la [inspección del estado de un clúster y una aplicación](service-fabric-health-introduction.md).
* Más información acerca de la [supervisión y el diagnóstico de servicios](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md). 
* Más información sobre las [opciones de soporte técnico de Service Fabric](service-fabric-support.md).


[cluster-application-instances]: media/service-fabric-content-roadmap/cluster-application-instances.png
[cluster-imagestore-apptypes]: ./media/service-fabric-content-roadmap/cluster-imagestore-apptypes.png


<!--HONumber=Jan17_HO4-->


