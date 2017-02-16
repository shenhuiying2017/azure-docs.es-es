---
title: "Introducción a Service Fabric y los contenedores | Microsoft Docs"
description: "Información general sobre Service Fabric y el uso de contenedores para implementar aplicaciones de microservicios. Este artículo le proporciona información general de cómo se pueden utilizar los contenedores y las funcionalidades disponibles en Service Fabric."
services: service-fabric
documentationcenter: .net
author: msfussell
manager: timlt
editor: 
ms.assetid: c98b3fcb-c992-4dd9-b67d-2598a9bf8aab
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 1/4/2017
ms.author: msfussell
translationtype: Human Translation
ms.sourcegitcommit: 9349c6c60801c87726eb9f848706b39b08eb2b5d
ms.openlocfilehash: 7c6d232bce7ac9d364ad9d7b26c3164e00fc18ac


---
# <a name="preview-service-fabric-and-containers"></a>Vista previa: Service Fabric y contenedores
> [!NOTE]
> Esta característica está disponible en versión preliminar para Linux y Windows Server 2016. 
>   

## <a name="introduction"></a>Introducción
Azure Service Fabric es un [orquestador](service-fabric-cluster-resource-manager-introduction.md) de servicios a través de un clúster de máquinas. Se pueden desarrollar servicios de muchas maneras, desde el uso de los [modelos de programación de Service Fabric ](service-fabric-choose-framework.md) hasta la implementación de [archivos ejecutables invitados](service-fabric-deploy-existing-app.md). De forma predeterminada Service Fabric permite implementar y activar estos servicios como procesos. Los procesos proporcionan la activación más rápida y el uso de densidad más alto de los recursos del clúster. Service Fabric puede implementar también servicios en imágenes de contenedor. Lo importantes es que puede mezclar servicios en procesos y servicios en contenedores en la misma aplicación. Conseguirá lo mejor de ambos mundos, según el escenario.

## <a name="what-are-containers"></a>¿Qué son los contenedores?
Los contenedores son componentes encapsulados, que se implementan individualmente y se ejecutan como instancias aisladas en el mismo kernel para aprovechar la virtualización de nivel del sistema operativo. Esto significa que cada aplicación, su tiempo de ejecución, las dependencias y las bibliotecas del sistema se ejecutan dentro de un contenedor con acceso completo y privado a su propia vista aislada de las construcciones del sistema operativo. Junto con la portabilidad, este grado de aislamiento de seguridad y recursos es la principal ventaja del uso de contenedores con Service Fabric, que de lo contrario ejecutará los servicios en procesos.

Los contenedores constituyen una tecnología de virtualización que permite virtualizar el sistema operativo subyacente de las aplicaciones. Proporcionan un entorno inmutable para que las aplicaciones se ejecuten con un grado de aislamiento variable. Los contenedores se ejecutan directamente en el kernel y tienen una vista aislada del sistema de archivos y de otros recursos. En comparación con las máquinas virtuales, los contenedores ofrecen las siguientes ventajas:

* **Tamaño pequeño**: los contenedores usan un espacio de almacenamiento único y deltas más pequeños para cada capa, lo cual aumenta su eficacia.
* **Tiempo de arranque rápido**: los contenedores no tienen que arrancar un sistema operativo, por lo que pueden estar disponibles mucho más rápidamente que las máquinas virtuales, normalmente en cuestión de segundos.
* **Portabilidad**: se puede portar una imagen de aplicación de contenedor para que se ejecute en la nube o localmente, dentro de las máquinas virtuales o directamente en las máquinas físicas.
* **Regulación de recursos**: un contenedor puede limitar los recursos físicos que puede consumir en el host.

## <a name="container-types"></a>Tipos de contenedor
Service Fabric admite los siguientes tipos de contenedores.

### <a name="docker-containers-on-linux"></a>Contenedores de Docker en Linux
Docker ofrece API de alto nivel para crear y administrar contenedores sobre contenedores de kernel de Linux. Docker Hub es un repositorio central para almacenar y recuperar imágenes del contenedor.

Para un tutorial sobre cómo hacerlo, lea [Deploy a Docker container to Service Fabric](service-fabric-deploy-container-linux.md) (Implementación de un contenedor de Docker en Service Fabric).

### <a name="windows-server-containers"></a>Contenedores de Windows Server
Windows Server 2016 proporciona dos tipos de contenedores que difieren en el nivel de aislamiento que ofrecen. Los contenedores de Windows Server y los de Docker se parecen en que tienen un aislamiento del sistema de archivos y un espacio de nombres, pero comparten el kernel con el host en que se ejecutan. En Linux, este aislamiento tradicionalmente lo han proporcionado los cgroups y los espacios de nombres, y los contenedores de Windows Server se comportan de forma parecida.

Los contenedores de Windows Hyper-V proporcionan mayor seguridad y aislamiento, ya que los contenedores no comparten el kernel del sistema operativo entre ellos ni con el host. Con este mayor nivel de aislamiento de seguridad, los contenedores de Hyper-V están especialmente diseñados para escenarios hostiles, multiinquilino.

Para un tutorial sobre cómo hacerlo, lea el artículo sobre la [implementación de un contenedor de Windows en Service Fabric](service-fabric-deploy-container.md).

La ilustración siguiente muestra los diferentes tipos de niveles de aislamiento y virtualización disponibles en el sistema operativo.
![Plataforma de Service Fabric][Image1]

## <a name="scenarios-for-using-containers"></a>Escenarios de uso de contenedores
Ejemplos típicos de buena elección de contenedor:

* **Levantamiento y movimiento de IIS**: si dispone de aplicaciones [ASP.NET MVC](https://www.asp.net/mvc) existentes que quiera seguir utilizando, colóquelas en un contenedor en lugar de migrarlas a ASP.NET Core. Estas aplicaciones ASP.NET MVC dependen de Internet Information Services (IIS). Puede empaquetarlas en imágenes de contenedor a partir de la imagen IIS creada previamente e implementarlas con Service Fabric. Consulte [Imágenes de contenedores en Windows Server](https://msdn.microsoft.com/virtualization/windowscontainers/quick_start/quick_start_images) para información sobre cómo crear imágenes IIS.
* **Combinación de contenedores y microservicios de Service Fabric**: use una imagen de contenedor existente para la parte de la aplicación. Por ejemplo, podría usar el [contenedor NGINX](https://hub.docker.com/_/nginx/) para el front-end web de su aplicación y los servicios con estado compilados con Reliable Services para la computación de back-end más intensa. Un ejemplo de este escenario incluye las aplicaciones de juegos.
* **Reducción del impacto de los servicios de los "vecinos ruidosos"**: puede utilizar la capacidad de regulación de los recursos de los contenedores para restringir los recursos que utiliza un servicio en un host. Si hay servicios que consuman un gran número de recursos y afecten al rendimiento de otros (por ejemplo, una consulta de larga ejecución como operación), considere la posibilidad de poner estos servicios en contenedores con regulación de recursos.

## <a name="service-fabric-support-for-containers"></a>Compatibilidad de los contenedores con Service Fabric
Service Fabric admite actualmente la implementación de contenedores de Docker en contenedores de Linux y Windows Server en Windows Server 2016. La compatibilidad con contenedores de Hyper-V se agregará en una versión futura.

En el [modelo de aplicación](service-fabric-application-model.md)de Service Fabric, un contenedor representa un host de la aplicación en el que se colocan varias réplicas de servicio. Se admiten los siguientes escenarios para contenedores:

* **Contenedores invitados**: este escenario es similar al [escenario de archivo ejecutable invitado](service-fabric-deploy-existing-app.md) en el que puede implementar las aplicaciones existentes en un contenedor. Algunos ejemplos son Node.js, JavaScript o cualquier código (archivos ejecutables).
* **Servicios sin estado dentro de contenedores**: estos son los servicios sin estado que usan los modelos de programación de Reliable Services y Reliable Actors. En la actualidad, esto es solo compatible con Linux. La compatibilidad con los servicios sin estado en contenedores de Windows se agregará en una versión futura.
* **Servicios con estado dentro de contenedores**: estos son los servicios con estado que usan los modelos de programación de Reliable Actors en Linux. En la actualidad, Reliable Services no es compatible con Linux.  La compatibilidad con los servicios con estado en contenedores de Windows se agregará en una versión futura.

Service Fabric tiene varias funcionalidades de contenedor que le ayudarán a crear aplicaciones que se componen de microservicios en contenedores. Son los llamados servicios en contenedores. Entre estas funcionalidades, cabe destacar:

* Activación e implementación de la imagen de contenedor.
* La regulación de recursos.
* La autenticación de repositorios.
* La asignación de puerto a host de contenedor.
* La detección y comunicación entre contenedores.
* La capacidad de configurar y establecer variables de entorno.

## <a name="next-steps"></a>Pasos siguientes
En este artículo, ha aprendido acerca de los contenedores y que Service Fabric es un orquestador de contenedores y las características compatibles con los contenedores. En los siguientes pasos analizaremos algunos ejemplos de cada una de las características para mostrarle cómo utilizarlas.

[Implementación de un contenedor de Windows en Service fabric en Windows Server 2016](service-fabric-deploy-container.md)

[Implementación de un contenedor de Docker en Service Fabric en Linux](service-fabric-deploy-container-linux.md)

[Image1]: media/service-fabric-containers/Service-Fabric-Types-of-Isolation.png



<!--HONumber=Jan17_HO2-->


