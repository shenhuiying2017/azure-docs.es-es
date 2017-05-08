---
title: "Información acerca de Azure Service Fabric | Microsoft Docs"
description: "Información general y guía de introducción sobre Azure Service Fabric Obtenga información sobre Service Fabric y una introducción sobre el desarrollo de aplicaciones escalables, confiables y de fácil administración compuestas de microservicios."
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
ms.date: 04/18/2017
ms.author: ryanwi
translationtype: Human Translation
ms.sourcegitcommit: db7cb109a0131beee9beae4958232e1ec5a1d730
ms.openlocfilehash: 94b697d01d31463d1744d7e8d4f1169a2f366242
ms.lasthandoff: 04/18/2017


---
# <a name="so-you-want-to-learn-about-service-fabric"></a>¿Qué desea saber sobre Service Fabric?
En este manual se proporciona una breve introducción a Service Fabric, una introducción a los conceptos básicos y la terminología, una guía de introducción y una descripción general de cada área de Service Fabric.  Este manual no contiene una lista completa de contenido, pero incluye vínculos a información general y artículos de introducción de cada área de Service Fabric. 

## <a name="the-five-minute-overview"></a>Información general en cinco minutos
Azure Service Fabric es una plataforma de sistemas distribuidos que facilita el empaquetamiento, la implementación y la administración de microservicios escalables y confiables.  Service Fabric aborda los desafíos importantes en el desarrollo y la administración de aplicaciones en la nube. Mediante el uso de Service Fabric, los desarrolladores y administradores pueden librarse de solucionar problemas complejos de infraestructura.  En su lugar, pueden centrarse en la implementación de cargas de trabajo críticas y exigentes con la seguridad de que son escalables, confiables y administrables. Service Fabric representa la plataforma middleware de próxima generación para crear y administrar estas aplicaciones de clase empresarial, de escala de nube de nivel 1.  

Este breve vídeo de Channel9 presenta Service Fabric y los microservicios: <center><a target="_blank" href="https://aka.ms/servicefabricvideo">  
<img src="./media/service-fabric-content-roadmap/OverviewVid.png" WIDTH="360" HEIGHT="244">  
</a></center>

## <a name="the-detailed-overview"></a>Información detallada
Service Fabric permite crear y administrar aplicaciones escalables y confiables compuestas de microservicios.  Estos microservicios se ejecutan con una densidad muy alta en un grupo compartido de máquinas, al que se hace referencia como un clúster. Ofrece un sofisticado tiempo de ejecución para crear microservicios con y sin estado, distribuidos y escalables. También ofrece funciones de administración de aplicaciones completas para aprovisionar, implementar, supervisar, actualizar/aplicar revisiones y eliminar aplicaciones implementadas.  Lea [Información general acerca de Service Fabric](service-fabric-overview.md) para obtener más información.

¿Por qué un diseño de microservicios? Todas las aplicaciones evolucionan con el tiempo. Las aplicaciones que triunfan evolucionan por ser útiles a sus usuarios. ¿Cuánto sabe acerca de sus requisitos actuales y cuáles serán en el futuro? A veces, terminar un servicio como prueba de concepto es el factor determinante, sabiendo que el diseño de la aplicación podrá modificarse más adelante. Por otra parte, cuando las empresas hablan sobre la creación de aplicaciones para la nube, lo que esperan es crecimiento y uso. El problema es que el crecimiento y la escala son imprevisibles. Los desarrolladores desean crear un prototipo rápidamente sabiendo que la aplicación se puede escalar para responder a un uso y un crecimiento impredecibles.  En [¿Qué son los microservicios?](service-fabric-overview-microservices.md) se describe la manera en que el diseño de microservicios aborda estos desafíos y cómo crear microservicios que se puedan escalar verticalmente u horizontalmente, probar, implementar y administrar de forma independiente.

Service Fabric es una plataforma confiable y flexible que le permite escribir y ejecutar muchos tipos de aplicaciones y servicios empresariales.  También puede ejecutar cualquiera de sus aplicaciones existentes (escritas en cualquier lenguaje).  Estas aplicaciones y microservicios pueden tener estado o no tenerlo, y se equilibran mediante recursos en las máquinas virtuales para maximizar su eficacia. La arquitectura única de Service Fabric le permite realizar análisis de datos casi en tiempo real, cálculo en memoria, transacciones paralelas y procesamiento de eventos en sus aplicaciones. Puede [escalar o reducir verticalmente las aplicaciones](service-fabric-concepts-scalability.md) con facilidad (en realidad hacia dentro o hacia fuera), dependiendo de los requisitos de recursos variables. Lea [Escenarios de aplicación](service-fabric-application-scenarios.md) y [Escenarios y patrones](service-fabric-patterns-and-scenarios.md) para obtener información sobre las categorías de aplicaciones y servicios que puede crear, así como casos prácticos de clientes.

En este vídeo más largo de Microsoft Virtual Academy se describen los conceptos básicos de Service Fabric: <center><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=tbuZM46yC_5206218965">  
<img src="./media/service-fabric-content-roadmap/CoreConceptsVid.png" WIDTH="360" HEIGHT="244">  
</a></center>

## <a name="get-started-and-create-your-first-app"></a>Introducción y creación de la primera aplicación 
Con el SDK y las herramientas de Service Fabric, puede desarrollar aplicaciones en entornos Windows, Linux o Mac OS e implementarlas en clústeres que se ejecutan en Windows o Linux.  Las siguientes guías le ayudarán a implementar una aplicación en minutos.  Una vez ejecutada la primera aplicación, descargue y ejecute algunas de nuestras [aplicaciones de ejemplo](http://aka.ms/servicefabricsamples). En concreto, comience con los [ejemplos de introducción](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)

### <a name="on-windows"></a>En Windows
El SDK de Service Fabric incluye un complemento para Visual Studio que proporciona herramientas y plantillas para crear, implementar y depurar aplicaciones de Service Fabric. Estos temas le guiarán por el proceso de creación de su primera aplicación en Visual Studio, y su ejecución en el equipo de desarrollo.

[Configuración del entorno de desarrollo](service-fabric-get-started.md)
[Creación de la primera aplicación (C#)](service-fabric-create-your-first-application-in-visual-studio.md)

#### <a name="practical-hands-on-labs"></a>Laboratorios prácticos
Pruebe este completo [laboratorio de prácticas parte 1](https://msdnshared.blob.core.windows.net/media/2016/07/SF-Lab-Part-I.docx) para familiarizarse con el flujo de desarrollo completo de Service Fabric.  Aprenda a crear un servicio sin estado, configure informes de supervisión y estado, y realice una actualización de la aplicación. Después de la parte 1, vaya al [laboratorio de prácticas parte 2](http://aka.ms/sflab2) que le guiará por los servicios con estado.

El siguiente vídeo de Channel9 le guiará por el proceso de creación de una aplicación de C# en Visual Studio:  
<center><a target="_blank" href="https://channel9.msdn.com/Blogs/Azure/Creating-your-first-Service-Fabric-application-in-Visual-Studio">  
<img src="./media/service-fabric-content-roadmap/first-app-vid.png" WIDTH="360" HEIGHT="244">  
</a></center>

### <a name="on-linux"></a>En Linux
Service Fabric ofrece SDK para compilar servicios en Linux tanto en .NET Core como Java. Estos temas le guiarán por el proceso de creación de su primera aplicación de Java o C# en Linux, y su ejecución en el equipo de desarrollo: [Configurar el entorno de desarrollo](service-fabric-get-started-linux.md), [Crear su primera aplicación (Java)](service-fabric-create-your-first-linux-application-with-java.md) y [Crear su primera aplicación (C#)](service-fabric-create-your-first-linux-application-with-csharp.md).

El siguiente vídeo de Microsoft Virtual Academy le guiará a través del proceso de creación de una aplicación de Java en Linux:  
<center><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=DOX8K86yC_206218965">  
<img src="./media/service-fabric-content-roadmap/LinuxVid.png" WIDTH="360" HEIGHT="244">  
</a></center>

### <a name="on-macos"></a>En MacOS
Puede crear aplicaciones de Service Fabric en Mac OS X que se ejecutan en clústeres de Linux. En estos artículos se describe cómo configurar su Mac para desarrollo y se proporciona orientación para crear su primera aplicación de Java en MacOS, y su ejecución en una máquina virtual de Ubuntu: [Configurar el entorno de desarrollo](service-fabric-get-started-mac.md) y [Crear su primera aplicación (Java)](service-fabric-create-your-first-linux-application-with-java.md).

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

Un tipo de servicio es el nombre o versión asignados a los paquetes de código, paquetes de datos y paquetes de configuración de un servicio. Se define en un archivo ServiceManifest.xml, insertado en un directorio del paquete de servicio, al que después hace referencia un archivo ApplicationManifest.xml del paquete de aplicación. En el clúster, después de crear una aplicación con nombre se pude crear un servicio con nombre desde uno de los tipos de servicio del tipo de aplicación. Un tipo de servicio se describe en un archivo ServiceManifest.xml.  Además, se compone de las opciones de configuración del servicio del código ejecutable que se cargan en tiempo de ejecución, y los datos estáticos que el servicio consume.

![Tipos de aplicaciones de Service Fabric y tipos de servicio][cluster-imagestore-apptypes]

El paquete de aplicación es un directorio de disco que contiene el archivo ApplicationManifest.xm del tipo de aplicación, que hace referencia a los paquetes de servicio para cada tipo de servicio que compone el tipo de aplicación. Por ejemplo, un paquete de aplicación de un tipo de aplicación de correo electrónico puede contener referencias a un paquete de servicios de cola, un paquete de servicios de front-end y un paquete de servicios de base de datos. Los archivos en el directorio del paquete de aplicación se copian en el almacén de imágenes del clúster de Service Fabric. 

Un paquete de servicio es un directorio de disco que contiene el archivo ServiceManifest.xml del tipo de servicio, que hace referencia al código, los datos estáticos y los paquetes de configuración del tipo de servicio. El archivo ApplicationManifest.xml del tipo de aplicación hace referencia a los archivos del directorio del paquete de servicio. Por ejemplo, un paquete de servicio puede hacer referencia al código, a los datos estáticos y a los paquetes de configuración que conforman un servicio de base de datos.

### <a name="run-time-clusters-and-nodes-named-apps-named-services-partitions-and-replicas"></a>Tiempo de ejecución: clústeres y nodos, aplicaciones con nombre, servicios con nombre, particiones y réplicas.
Un [clúster de Service Fabric](service-fabric-deploy-anywhere.md) es un conjunto de máquinas físicas o virtuales conectadas a la red, en las que se implementan y administran los microservicios. Los clústeres pueden escalar a miles de equipos.

Cada una de las máquinas físicas o virtuales que forman parte de un clúster se denominan nodo. A cada nodo se le asigna un nombre de nodo (una cadena). Los nodos tienen características como las propiedades de colocación. Cada máquina física o virtual tiene un servicio de Windows de inicio automático, FabricHost.exe, que empieza a ejecutarse en el arranque y luego inicia dos ejecutables: Fabric.exe y FabricGateway.exe. Estos dos ejecutables conforman el nodo. En los escenarios de prueba o desarrollo, se pueden hospedar varios nodos en una sola máquina física o virtual mediante la ejecución de varias instancias de Fabric.exe y FabricGateway.exe.

Una aplicación con nombre es una colección de servicios con nombre que realizan una o varias funciones determinadas. Un servicio realiza una función completa e independiente (puede iniciarse y ejecutarse independientemente de otros servicios) y se compone de código, configuración y datos. Una vez que un paquete de aplicación se copia en el almacén de imágenes, se crea una instancia de la aplicación en el clúster especificando el tipo de aplicación del paquete de aplicación (mediante su nombre o versión). A cada instancia del tipo de aplicación se le asigna un nombre de identificador URI similar a *fabric:/MyNamedApp*. En un clúster se pueden crear varias aplicaciones con nombre de un único tipo de aplicación. También se pueden crear aplicaciones con nombre de diferentes tipos de aplicación. Cada aplicación con nombre se administra de manera independiente y tiene versiones independientes.

Después de crear una aplicación con nombre puede crear una instancia de uno de sus tipos de servicio (un servicio con nombre) en el clúster, especificando el tipo de servicio (con su nombre o versión). A cada instancia del tipo de servicio se le asigna un nombre de URI cuyo ámbito es el URI de su aplicación con nombre. Por ejemplo, si crea un servicio con nombre "MyDatabase" en la aplicación con nombre "MyNamedApp", el URI será similar al siguiente: *fabric:/MyNamedApp/MyDatabase*. En una aplicación con nombre, puede crear uno o varios servicios con nombre. Todos los servicios con nombre pueden tener su propio esquema de partición y cuentas de instancia o réplica. 

Hay dos tipos de servicios: con y sin estado.  Los servicios sin estado almacenan el estado persistente en un servicio de almacenamiento externo como Azure Storage, Azure SQL Database o Azure DocumentDB. Utilice un servicio sin estado en el caso de que el servicio no tenga almacenamiento persistente. Un servicio con estado usa Service Fabric para administrar el estado del servicio mediante sus modelos de programación Reliable Collections o Reliable Actors.  

Al crear un servicio con nombre, especifique un esquema de partición. Los servicios con grandes cantidades de estado dividen los datos entre las particiones.  Cada partición es responsable de una parte de todo el estado del servicio, que se reparte entre los nodos del clúster.  Dentro de una partición, los servicios con nombre sin estado tienen instancias, mientras que los servicios con nombre con estado tienen réplicas. Normalmente, los servicios con nombre sin estado solo tienen una partición, ya que no tienen un estado interno. Los servicios con nombre con estado mantienen su estado dentro de las réplicas y cada partición tiene su propio conjunto de réplicas.  Las operaciones de lectura y escritura se realizan en una réplica (denominada principal). Los cambios en el estado debidos a las operaciones de escritura se replican en muchas otras réplicas (denominadas secundarias activas).  

En el siguiente diagrama se muestra la relación entre aplicaciones e instancias de servicio, particiones y réplicas.

![Particiones y réplicas dentro de un servicio][cluster-application-instances]

### <a name="partitioning-scaling-and-availability"></a>Creación de particiones, escalado y disponibilidad
La [creación de particiones](service-fabric-concepts-partitioning.md) no es exclusiva de Service Fabric. Una forma conocida de partición es la partición de datos o particionamiento. Los servicios con estado que tienen grandes cantidades de estados dividen los datos entre las particiones.  Cada partición es responsable de una parte de todo el estado del servicio. 

Las réplicas de cada partición se reparten entre los nodos del clúster, lo que permite [escalar](service-fabric-concepts-scalability.md) el estado del servicio con nombre. A medida que los datos crecen, las particiones también crecen y Service Fabric vuelve a equilibrar las particiones entre los nodos, a fin de usar los recursos de hardware con eficacia. Si agrega nuevos nodos al clúster, Service Fabric reequilibrará las réplicas de la partición entre en el número aumentado de nodos.  El rendimiento general de la aplicación mejora y se reduce la contención para el acceso a la memoria.  Si los nodos del clúster no se usan de forma eficaz, puede reducir su número de nodos.  Service Fabric vuelve a reequilibrar las réplicas de la partición entre el número reducido de nodos para aprovechar mejor el hardware de cada nodo.

Dentro de una partición, los servicios con nombre sin estado tienen instancias, mientras que los servicios con nombre con estado tienen réplicas. Normalmente, los servicios con nombre sin estado solo tienen una partición, ya que no tienen un estado interno. Las instancias de la partición proporcionan [disponibilidad](service-fabric-availability-services.md).  Si se produce un error en una instancia, otras instancias seguirán operando con normalidad y luego Service Fabric crea una nueva instancia. Los servicios con nombre con estado mantienen su estado dentro de las réplicas y cada partición tiene su propio conjunto de réplicas.  Las operaciones de lectura y escritura se realizan en una réplica (denominada principal). Los cambios en el estado debidos a las operaciones de escritura se replican en muchas otras réplicas (denominadas secundarias activas).  Si se produce algún error en una réplica, Service Fabric creará una nueva réplica a partir de las réplicas existentes.

## <a name="supported-programming-models"></a>Modelos de programación admitidos
Service Fabric ofrece varias maneras de escribir y administrar los servicios. Los servicios pueden optar por usar las API de Service Fabric para sacar el máximo provecho de los marcos de aplicaciones y las características de la plataforma.  Los servicios también pueden ser cualquier programa ejecutable compilado escrito en cualquier lenguaje y hospedado en un clúster de Service Fabric. Para más información, consulte [Modelos de programación admitidos](service-fabric-choose-framework.md).

### <a name="guest-executables"></a>Ejecutables de invitado
Un [ejecutable de invitado](service-fabric-deploy-existing-app.md) es un ejecutable arbitrario y existente, escrito en cualquier lenguaje, que se hospeda en un clúster de Service Fabric junto con otros servicios. Sin embargo, los ejecutables de invitado no se integran directamente con las API de Service Fabric.  Estos ejecutables no se benefician del conjunto completo de características que ofrece la plataforma, como el estado personalizado y la carga de informes, el registro del punto de conexión de servicio y el proceso con estado.

### <a name="containers"></a>Contenedores
De forma predeterminada, Service Fabric implementa y activa estos servicios como procesos. Service Fabric puede implementar también servicios en [imágenes de contenedor](service-fabric-containers-overview.md). Lo importantes es que puede mezclar servicios en procesos y servicios en contenedores en la misma aplicación.  Service Fabric admite actualmente la implementación de contenedores de Docker en contenedores de Linux y Windows Server en Windows Server 2016. En el modelo de aplicación de Service Fabric, un contenedor representa un host de la aplicación en el que se colocan varias réplicas de servicio.  Puede usar Service Fabric para implementar aplicaciones existentes, servicios sin estado o servicios con estado en un contenedor.  

### <a name="reliable-services"></a>Reliable Services
[Reliable Services](service-fabric-reliable-services-introduction.md) es un entorno de trabajo ligero para escribir servicios que se integran con la plataforma Service Fabric y que se benefician de todo el conjunto de características de la plataforma. Reliable Services puede no tener estado, como ocurre con la mayoría de las plataformas de servicio, como los servidores web o los roles de trabajo de Azure Cloud Services. El estado persiste en una solución externa, como Azure DB o Azure Table Storage. Reliable Services también puede tener estado, y este se conserva directamente en el propio servicio mediante Reliable Collections. El estado cuenta con una [alta disponibilidad](service-fabric-availability-services.md) mediante la replicación y se distribuye a través de [particiones](service-fabric-concepts-partitioning.md), todo administrado automáticamente por Service Fabric.

### <a name="reliable-actors"></a>Reliable Actors
Basado en Reliable Services, el entorno de [Reliable Actor](service-fabric-reliable-actors-introduction.md) es un entorno de aplicación que implementa el modelo de Virtual Actor, basado en el modelo de diseño de actores. El marco de Reliable Actor usa unidades independientes del proceso y el estado con la ejecución de subproceso único denominadas actores. El marco de Reliable Actor proporciona comunicaciones integradas para actores y las configuraciones de escalado horizontal y persistencia de estado establecidas previamente.

## <a name="app-lifecycle"></a>Ciclo de vida de la aplicación
Al igual que sucede con otras plataformas, una aplicación en Service Fabric normalmente pasa por las siguientes fases: diseño, desarrollo, prueba, implementación, actualización, mantenimiento y eliminación. Service Fabric ofrece compatibilidad de primera clase para todo el ciclo de vida de aplicación de las aplicaciones de nube: desde el desarrollo hasta la implementación, la administración diaria, el mantenimiento y, finalmente, la retirada. El modelo de servicio habilita varios roles distintos para participar de manera independiente en el ciclo de vida de la aplicación. En [Ciclo de vida de la aplicación de Service Fabric](service-fabric-application-lifecycle.md) se proporciona información general de las API y cómo las utilizan los distintos roles durante todas las fases del ciclo de vida de la aplicación de Service Fabric.  

El ciclo de vida completo de la aplicación se puede administrar con [cmdlets de PowerShell](/powershell/module/ServiceFabric/), [API de C#](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), [API de Java](/java/api/system.fabric._application_management_client) y [API de REST](/rest/api/servicefabric/). También puede configurar canalizaciones de implementación continua e integración continua con herramientas como [Visual Studio Team Services](service-fabric-set-up-continuous-integration.md) o [Jenkins](service-fabric-cicd-your-linux-java-application-with-jenkins.md)

En el siguiente vídeo de Microsoft Virtual Academy describe cómo administrar el ciclo de vida de la aplicación: <center><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=My3Ka56yC_6106218965">
<img src="./media/service-fabric-content-roadmap/AppLifecycleVid.png" WIDTH="360" HEIGHT="244">
</a></center>

## <a name="test-apps-and-services"></a>Comprobación de aplicaciones y servicios
Para crear verdaderos servicios de escala en la nube, es fundamental verificar que los servicios pueden resistir errores reales.  El servicio de análisis de errores se ha diseñado para probar los servicios incorporados en Service Fabric. Con el [servicio de análisis de errores](service-fabric-testability-overview.md) puede provocar errores significativos y ejecutar escenarios de prueba completos con sus aplicaciones. Estos errores y escenarios ejercen y validan los numerosos estados y transiciones que experimentará un servicio durante su vigencia, y todo ello de forma segura, controlada y uniforme.

Las [acciones](service-fabric-testability-actions.md) están orientadas a un servicio de prueba mediante errores individuales. Un programador del servicio puede utilizar como bloques de creación para escribir escenarios complicados. Los ejemplos de errores simulados son:

* Reiniciar un nodo para simular cualquier número de situaciones en que un equipo o máquina virtual se reinicia.
* Mover una réplica de un servicio con estado para simular el equilibrio de carga, la conmutación por error o la actualización de la aplicación.
* Invocar la pérdida de quórum en un servicio con estado para crear una situación en la que las operaciones de escritura no pueden continuar porque no hay suficientes réplicas "secundarias" o "de copia de seguridad" para aceptar los datos nuevos.
* Invocar la pérdida de datos en un servicio con estado para crear una situación en la que se borra completamente todo el estado en memoria.

Los [escenarios](service-fabric-testability-scenarios.md) son operaciones complejas compuestas por una o varias acciones. El servicio de análisis de errores proporciona dos escenarios completos integrados:

* [Escenario de caos](service-fabric-controlled-chaos.md): simula errores continuos intercalados (tanto correctos como incorrectos) en todo el clúster durante períodos prolongados.
* [Escenario de conmutación por error](service-fabric-testability-scenarios.md#failover-test): una versión del escenario de prueba de caos dirigida a una partición de servicio específica, que no afecta a los demás servicios.

## <a name="clusters"></a>Clústeres
Un [clúster de Service Fabric](service-fabric-deploy-anywhere.md) es un conjunto de máquinas físicas o virtuales conectadas a la red, en las que se implementan y administran los microservicios. Los clústeres pueden escalar a miles de equipos. Una máquina física o virtual que forma parte de un clúster se denomina nodo del clúster. A cada nodo se le asigna un nombre de nodo (una cadena). Los nodos tienen características como las propiedades de colocación. Cada máquina física o virtual tiene un servicio de inicio automático, FabricHost.exe, que empieza a ejecutarse en el arranque y luego inicia dos ejecutables: Fabric.exe y FabricGateway.exe. Estos dos ejecutables conforman el nodo. En los escenarios de prueba, se pueden hospedar varios nodos en una sola máquina física o virtual mediante la ejecución de varias instancias de Fabric.exe y FabricGateway.exe.

Los clústeres de Service Fabric se pueden crear en cualquier máquina virtual o equipo con Windows Server o Linux. Puede implementar y ejecutar aplicaciones de Service Fabric en cualquier entorno donde haya un conjunto de equipos de Windows Server o Linux que estén conectados entre sí, ya sea de manera local, en Microsoft Azure o con algún proveedor en la nube.

En el siguiente vídeo de Microsoft Virtual Academy se describen los clústeres de Service Fabric: <center><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=tbuZM46yC_5206218965">
<img src="./media/service-fabric-content-roadmap/ClusterOverview.png" WIDTH="360" HEIGHT="244">
</a></center>

### <a name="clusters-on-azure"></a>Clústeres en Azure
La ejecución de clústeres de Service Fabric en Azure proporciona integración con otras características y servicios de Azure, lo que facilita las operaciones y la administración del clúster y hace que sea más confiable.  Un clúster es un recurso de Azure Resource Manager, que permite modelar clústeres como cualquier otro recurso de Azure. Resource Manager también permite realizar una administración sencilla de todos los recursos utilizados por el clúster como una sola unidad.  Los clústeres de Azure se integran con Diagnósticos de Azure y Log Analytics.  Los clústeres nodetypes son [conjuntos de escalado de máquina virtual](/azure/virtual-machine-scale-sets/index), por lo que la funcionalidad de escalado automático está integrada.

Puede crear un clúster en Azure a través de [Azure Portal](service-fabric-cluster-creation-via-portal.md), a partir de una [plantilla](service-fabric-cluster-creation-via-arm.md) o en [Visual Studio](service-fabric-cluster-creation-via-visual-studio.md).

La versión preliminar de Service Fabric en Linux permite compilar, implementar y administrar aplicaciones de alta disponibilidad y escalabilidad en ese entorno de la misma forma que en Windows. Los marcos de Service Fabric (Reliable Services y Reliable Actors) se encuentran disponibles en Java en Linux, además de en C# (.NET Core).  Puede compilar igualmente [servicios ejecutables invitados](service-fabric-deploy-existing-app.md) con cualquier lenguaje o marco. Además, la vista previa también admite contenedores de Docker de organización. Los contenedores de Docker pueden ejecutar archivos ejecutables invitados o servicios de Service Fabric nativos que utilizan los marcos de Service Fabric. Para más información, vea [Service Fabric en Azure](service-fabric-linux-overview.md).

Como Service Fabric en Linux es una versión preliminar, hay algunas características que se admiten en Windows pero no en Linux. Para más información, vea [Diferencias entre Service Fabric en Linux (versión preliminar) y Windows (disponible con carácter general)](service-fabric-linux-windows-differences.md).

### <a name="standalone-clusters"></a>Clústeres independientes
Service Fabric proporciona un paquete de instalación para que cree clústeres independientes de Service Fabric de forma local o en cualquier proveedor en la nube.  Los clústeres independientes le dan la libertad de hospedar un clúster siempre que lo desee.  Si los datos están sujetos a restricciones legales o de cumplimiento o si desea mantener los datos locales, puede hospedar clústeres y aplicaciones propios.  Las aplicaciones de Service Fabric se pueden ejecutar en varios entornos de hospedaje sin realizar cambios, de tal forma que puede transferir las conocimientos sobre la creación de aplicaciones de un entorno de hospedaje a otro. 

[Creación del primer clúster independiente de Service Fabric](service-fabric-get-started-standalone-cluster.md)

Aún no se admiten clústeres de Linux independientes.

### <a name="cluster-security"></a>Seguridad de clúster
Los clústeres deben estar protegidos para evitar que usuarios no autorizados se conecten a su clúster, especialmente cuando en él se están ejecutando cargas de trabajo de producción. Aunque se puede crear un clúster no seguro, si lo hace, permite que cualquier usuario anónimo se conecte a él si expone los puntos de conexión de administración a una red pública de Internet.  No se puede habilitar la seguridad de un clúster no seguro en una fase posterior, ya que la seguridad se habilita en el momento en que se crea el clúster.

Estos son los escenarios de seguridad de clúster:
* Seguridad de nodo a nodo
* Seguridad de cliente a nodo
* Control de acceso basado en roles (RBAC)

Para más información, lea [Proteger un clúster](service-fabric-cluster-security.md).

### <a name="scaling"></a>Escalado
Si agrega nuevos nodos al clúster, Service Fabric reequilibra las réplicas e instancias de la partición entre el número aumentado de nodos.  El rendimiento general de la aplicación mejora y se reduce la contención para el acceso a la memoria.  Si los nodos del clúster no se usan de forma eficaz, puede reducir su número de nodos.  Service Fabric vuelve a reequilibrar las réplicas e instancias de la partición en el número reducido de nodos para aprovechar mejor el hardware de cada nodo.  Puede escalar clústeres en Azure [manualmente](service-fabric-cluster-scale-up-down.md) o [mediante programación](service-fabric-cluster-programmatic-scaling.md).  Los clústeres independientes se pueden escalar [manualmente](service-fabric-cluster-windows-server-add-remove-nodes.md).

### <a name="cluster-upgrades"></a>Actualizaciones de clústeres
Periódicamente se publican nuevas versiones de Service Fabric en tiempo de ejecución.  Realice actualizaciones en tiempo de ejecución o de Service Fabric en el clúster, a fin de que siempre ejecute una [versión compatible](service-fabric-support.md).  Además de las actualizaciones de Service Fabric, también puede actualizar la configuración de clúster, como los certificados o los puertos de la aplicación.

Un clúster de Service Fabric es un recurso de su propiedad administrado parcialmente por Microsoft.  Microsoft es responsable de la aplicación de revisiones del sistema operativo subyacente y de realizar actualizaciones de Service Fabric en el clúster.  Puede configurar el clúster para recibir las actualizaciones automáticas de Service Fabric cuando Microsoft publica una versión nueva, o bien seleccionar una versión de Service Fabric compatible que desee.  Las actualizaciones de configuración y de Service Fabric pueden definirse en Azure Portal o en Resource Manager.  Para más información, lea [Actualización de un clúster de Azure Service Fabric](service-fabric-cluster-upgrade.md).  

Un clúster independiente es un recurso que es totalmente de su propiedad.  Por tanto, usted es el responsable de la aplicación de revisiones del sistema operativo subyacente y de iniciar las actualizaciones de Service Fabric.  Si el clúster se puede conectar a [https://www.microsoft.com/download](https://www.microsoft.com/download), puede configurar el clúster para que se descargue automáticamente y aprovisione el nuevo paquete en tiempo de ejecución de Service Fabric.  A continuación, podría iniciar la actualización.  En cambio, si el clúster no puede acceder a [https://www.microsoft.com/download](https://www.microsoft.com/download), puede descargar manualmente el nuevo paquete en tiempo de ejecución desde una máquina conectada a Internet y después iniciar la actualización.  Para más información, lea [Actualización de un clúster independiente de Azure Service Fabric](service-fabric-cluster-upgrade-windows-server.md).

## <a name="health-monitoring"></a>Supervisión del estado
Service Fabric presenta un [modelo de estado](service-fabric-health-introduction.md) diseñado para marcar las condiciones poco favorables del clúster o de la aplicación en entidades específicas, como nodos de clúster y réplicas de servicio. El modelo de mantenimiento utiliza informadores de estado (componentes del sistema y guardianes). El objetivo es obtener un diagnóstico y reparación sencillo y rápido. El escritor del servicio debe pensar de antemano en el estado y en cómo [diseñar informes de estado](service-fabric-report-health.md#design-health-reporting). Cualquier condición que pueda afectar al estado debe notificarse, sobre todo si puede ayudar a marcar la causa raíz de los problemas. La información de estado puede ahorrar tiempo y esfuerzo en la depuración y la investigación una vez que el servicio está en funcionamiento a escala en producción.

Los generadores de informes de Service Fabric identificaron las condiciones de interés. Informan sobre esas condiciones en función de su vista local. El [almacén de estado](service-fabric-health-introduction.md#health-store) agrega datos de mantenimiento que todos los informadores envían para determinar si el estado de las entidades es bueno en general. La intención es que el modelo sea completo, flexible y fácil de usar. La calidad de los informes de mantenimiento determina la precisión de la vista de estado del clúster. Los falsos positivos que muestran erróneamente problemas pueden afectar negativamente a las actualizaciones u otros servicios que usan datos de mantenimiento. Ejemplos de estos servicios son los servicios de reparación y los mecanismos de alerta. Por lo tanto, se necesita cierto grado de reflexión para proporcionar informes que capturen condiciones que resulten interesantes de la mejor manera posible.

Los informes pueden elaborarse a partir de:
* Réplicas o instancias de servicio de Service Fabric supervisado.
* Guardianes internos implementados como un servicio de Service Fabric (por ejemplo, un servicio sin estado de Service Fabric que supervisa las condiciones y los informes de problemas). Los guardianes pueden implementarse un todos los nodos o pueden afinarse en el servicio supervisado.
* Guardianes internos que se ejecutan en los nodos de Service Fabric pero que no se implementan como servicios de Service Fabric.
* Guardianes externos que sondean el recurso desde fuera del clúster de Service Fabric (por ejemplo, un servicio de supervisión de tipo Gomez).

Desde el principio, los componentes de Service Fabric informan del estado de todas las entidades del clúster.  Los [informes de mantenimiento del sistema](service-fabric-understand-and-troubleshoot-with-system-health-reports.md) proporcionan visibilidad en el clúster y funcionalidad de la aplicación, además de marcar problemas a través del estado. Para aplicaciones y servicios, los informes de estado del sistema comprueban que las entidades se implementan y que se comportan correctamente desde la perspectiva de Service Fabric en tiempo de ejecución. Los informes no proporcionan ninguna supervisión de estado de la lógica de negocios del servicio o de la detección de los procesos bloqueados. Para agregar información de estado específica a la lógica del servicio, [implemente informes de estado personalizados](service-fabric-report-health.md) en los servicios.

Service Fabric permite [ver los informes de estado](service-fabric-view-entities-aggregated-health.md) agregados al almacén de estado de varias formas:
* [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) y otras herramientas de visualización
* Consultas de estado (a través de [PowerShell](/powershell/module/ServiceFabric/), [API FabricClient de C#](/api/system.fabric.fabricclient.healthclient), [API FabricClient de Java](/java/api/system.fabric._health_client) o [API de REST](/rest/api/servicefabric)).
* Consultas generales que devuelven una lista de entidades con el estado como una de las propiedades (a través de PowerShell, la API o REST).

En el siguiente vídeo de Microsoft Virtual Academy se describe el modelo de estado de Service Fabric y cómo se utiliza: <center><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=tevZw56yC_1906218965">
<img src="./media/service-fabric-content-roadmap/HealthIntroVid.png" WIDTH="360" HEIGHT="244">
</a></center>

## <a name="next-steps"></a>Pasos siguientes
* Aprenda a crear un [clúster en Azure](service-fabric-cluster-creation-via-portal.md) o un [clúster independiente en Windows](service-fabric-cluster-creation-for-windows-server.md).
* Intente crear un servicio con los modelos de programación [Reliable Services](service-fabric-reliable-services-quick-start.md) o [Reliable Actors](service-fabric-reliable-actors-get-started.md).
* Más información sobre la [migración desde Cloud Services](service-fabric-cloud-services-migration-differences.md).
* Más información acerca de la [supervisión y el diagnóstico de servicios](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md). 
* Más información sobre las [pruebas de aplicaciones y servicios](service-fabric-testability-overview.md).
* Más información sobre la [administración y orquestación de recursos de clúster](service-fabric-cluster-resource-manager-introduction.md).
* Examine los [ejemplos de Service Fabric](http://aka.ms/servicefabricsamples).
* Más información sobre las [opciones de soporte técnico de Service Fabric](service-fabric-support.md).
* Lea el [blog del equipo](https://blogs.msdn.microsoft.com/azureservicefabric/) para consultar artículos y anuncios.


[cluster-application-instances]: media/service-fabric-content-roadmap/cluster-application-instances.png
[cluster-imagestore-apptypes]: ./media/service-fabric-content-roadmap/cluster-imagestore-apptypes.png

