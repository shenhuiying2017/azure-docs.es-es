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
ms.date: 9/20/2017
ms.author: msfussell
ms.openlocfilehash: 412107db2dc446eb5a6a433bfb7fc3bc5e760c27
ms.sourcegitcommit: e19f6a1709b0fe0f898386118fbef858d430e19d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/13/2018
---
# <a name="service-fabric-and-containers"></a>Service Fabric y contenedores
> [!NOTE]
> La implementación de contenedores en un clúster de Service Fabric en Windows 10 o con Docker CE no se admite. 
>   

> [!NOTE]
> La versión 6.1 de Service Fabric tiene compatibilidad de versión preliminar con la versión 1709 de Windows Server. La apertura de las redes y el servicio DNS de Service Fabric no funciona con la versión 1709 de Windows Server. 
> 

## <a name="introduction"></a>Introducción
Azure Service Fabric es un [orquestador](service-fabric-cluster-resource-manager-introduction.md) de servicios a través de un clúster de máquinas, con años de uso y optimización en servicios de escala masiva de Microsoft. Se pueden desarrollar servicios de muchas maneras, desde el uso de los [modelos de programación de Service Fabric ](service-fabric-choose-framework.md) hasta la implementación de [archivos ejecutables invitados](service-fabric-deploy-existing-app.md). De forma predeterminada Service Fabric permite implementar y activar estos servicios como procesos. Los procesos proporcionan la activación más rápida y el uso de densidad más alto de los recursos del clúster. Service Fabric puede implementar también servicios en imágenes de contenedor. Lo importantes es que puede mezclar servicios en procesos y servicios en contenedores en la misma aplicación.   

## <a name="what-are-containers"></a>¿Qué son los contenedores?
Los contenedores son componentes encapsulados, que se implementan individualmente y se ejecutan como instancias aisladas en el mismo kernel para aprovechar la virtualización de nivel del sistema operativo. Por tanto, cada aplicación y su entorno en tiempo de ejecución, las dependencias y las bibliotecas del sistema se ejecutan dentro de un contenedor con acceso completo y privado a su propia vista aislada de las construcciones del sistema operativo. Junto con la portabilidad, este grado de aislamiento de seguridad y recursos es la principal ventaja del uso de contenedores con Service Fabric, que de lo contrario ejecutará los servicios en procesos.

Los contenedores constituyen una tecnología de virtualización que permite virtualizar el sistema operativo subyacente de las aplicaciones. Proporcionan un entorno inmutable para que las aplicaciones se ejecuten con un grado de aislamiento variable. Los contenedores se ejecutan directamente en el kernel y tienen una vista aislada del sistema de archivos y de otros recursos. En comparación con las máquinas virtuales, los contenedores ofrecen las siguientes ventajas:

* **Pequeños**: usan un único espacio de almacenamiento y actualizaciones y versiones de capa para aumentar la eficacia.
* **Rápidos**: no tienen que arrancar un sistema operativo entero, así que pueden iniciarse con mayor rapidez, normalmente en unos segundos.
* **Portabilidad**: se puede portar una imagen de aplicación de contenedor para que se ejecute en la nube o localmente, dentro de las máquinas virtuales o directamente en las máquinas físicas.
* **Regulación de recursos**: un contenedor puede limitar los recursos físicos que puede consumir en el host.

## <a name="container-types-and-supported-environments"></a>Tipos de contenedor y entornos compatibles
Service Fabric admite contenedores en Linux y Windows y también admite el modo de aislamiento de Hyper-V en Windows. 

> [!NOTE]
> La implementación de contenedores en un clúster de Service Fabric en Windows 10 no se admite actualmente. 
> 

### <a name="docker-containers-on-linux"></a>Contenedores de Docker en Linux
Docker ofrece API de alto nivel para crear y administrar contenedores sobre contenedores de kernel de Linux. Docker Hub es un repositorio central para almacenar y recuperar imágenes del contenedor.
Para ver un tutorial, consulte [Implementar un contenedor de Linux en Service Fabric](service-fabric-get-started-containers-linux.md).

### <a name="windows-server-containers"></a>Contenedores de Windows Server
Windows Server 2016 proporciona dos tipos de contenedores que difieren en el nivel de aislamiento que ofrecen. Los contenedores de Windows Server y los de Docker se parecen en que tienen un aislamiento del sistema de archivos y un espacio de nombres, pero comparten el kernel con el host en que se ejecutan. En Linux, este aislamiento tradicionalmente lo han proporcionado los `cgroups` y `namespaces`, y los contenedores de Windows Server se comportan de forma parecida.

Los contenedores de Windows compatibles con Hyper-V proporcionan mayor seguridad y aislamiento, ya que los contenedores no comparten el kernel del sistema operativo entre ellos ni con el host. Con este mayor nivel de aislamiento de seguridad, los contenedores habilitados para Hyper-V están diseñados para escenarios hostiles y multiinquilino.
Para ver un tutorial, consulte [Implementación de un contenedor de Windows en Service Fabric](service-fabric-get-started-containers.md).

La ilustración siguiente muestra los diferentes tipos de niveles de aislamiento y virtualización disponibles en el sistema operativo.
![Plataforma de Service Fabric][Image1]

## <a name="scenarios-for-using-containers"></a>Escenarios de uso de contenedores
Ejemplos típicos de buena elección de contenedor:

* **Levantamiento y movimiento de IIS**: si dispone de aplicaciones [ASP.NET MVC](https://www.asp.net/mvc) existentes que quiera seguir utilizando, colóquelas en un contenedor en lugar de migrarlas a ASP.NET Core. Estas aplicaciones de ASP.NET MVC dependen de Internet Information Services (IIS). Puede empaquetarlas en imágenes de contenedor a partir de la imagen de IIS creada previamente e implementarlas con Service Fabric. Consulte [Imágenes de contenedores en Windows Server](https://docs.microsoft.com/virtualization/windowscontainers/quick-start/quick-start-windows-server) para obtener información sobre los contenedores de Windows.
* **Combinación de contenedores y microservicios de Service Fabric**: use una imagen de contenedor existente para la parte de la aplicación. Por ejemplo, podría usar el [contenedor NGINX](https://hub.docker.com/_/nginx/) para el front-end web de su aplicación y los servicios con estado para la computación de back-end más intensa.
* **Reducción del impacto de los servicios de los "vecinos ruidosos"**: puede utilizar la capacidad de regulación de los recursos de los contenedores para restringir los recursos que utiliza un servicio en un host. Si hay servicios que consuman un gran número de recursos y afecten al rendimiento de otros (por ejemplo, una consulta de larga ejecución como operación), considere la posibilidad de poner estos servicios en contenedores con regulación de recursos.

## <a name="service-fabric-support-for-containers"></a>Compatibilidad de los contenedores con Service Fabric
Service Fabric admite la implementación de contenedores de Docker en Linux y de contenedores de Windows Server en Windows Server 2016, además del modo de aislamiento de Hyper-V. 

En el [modelo de aplicación](service-fabric-application-model.md)de Service Fabric, un contenedor representa un host de la aplicación en el que se colocan varias réplicas de servicio. Service Fabric puede ejecutar cualquier contenedor, y este escenario es similar al [escenario del archivo ejecutable invitado](service-fabric-deploy-existing-app.md), donde se empaqueta una aplicación existente dentro de un contenedor. Este escenario es el caso de uso común de los contenedores, y entre los ejemplos se incluyen la ejecución de una aplicación escrita mediante cualquier lenguaje o plataforma, pero sin usar los modelos de programación integrados de Service Fabric.

También puede ejecutar los [servicios de Service Fabric dentro de contenedores](service-fabric-services-inside-containers.md). La compatibilidad con la ejecución de servicios de Service Fabric dentro de contenedores está limitada actualmente, pero se mejorará en próximas versiones.

Service Fabric tiene varias funcionalidades de contenedor que le ayudarán a crear aplicaciones que se componen de microservicios en contenedores. Service Fabric ofrece las siguientes funcionalidades para servicios en contenedores:

* Activación e implementación de la imagen de contenedor.
* Administración de recursos, incluida la configuración de valores de recursos predeterminados en clústeres de Azure.
* La autenticación de repositorios.
* La asignación de puerto a host de contenedor.
* La detección y comunicación entre contenedores.
* La capacidad de configurar y establecer variables de entorno.
* La capacidad de configurar credenciales de seguridad en el contenedor.
* Selección de diferentes modos de red para contenedores.

## <a name="next-steps"></a>pasos siguientes
En este artículo, ha aprendido acerca de los contenedores y que Service Fabric es un orquestador de contenedores y las características compatibles con los contenedores. En los siguientes pasos analizaremos algunos ejemplos de cada una de las características para mostrarle cómo utilizarlas.

[Cree la primera aplicación de contenedor en Service Fabric en Windows](service-fabric-get-started-containers.md)

[Cree la primera aplicación de contenedor de Service Fabric en Linux](service-fabric-get-started-containers-linux.md)

[Más información acerca de los contenedores de Windows](https://docs.microsoft.com/virtualization/windowscontainers/about/)

[Image1]: media/service-fabric-containers/Service-Fabric-Types-of-Isolation.png
