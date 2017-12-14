---
title: "Información general de Service Fabric en Azure | Microsoft Docs"
description: "Información general de Service Fabric donde las aplicaciones se componen de muchos microservicios para ofrecer escala y resiliencia. Service Fabric es una plataforma de sistemas distribuidos que se usa para crear aplicaciones escalables, confiables y fáciles de administrar para la nube."
services: service-fabric
documentationcenter: .net
author: msfussell
manager: timlt
editor: masnider
ms.assetid: bbcc652a-a790-4bc4-926b-e8cd966587c0
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: overview
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/20/2017
ms.author: msfussell
ms.custom: mvc
ms.openlocfilehash: 4aca25f74d3e22911ab5059a8cdec45f189dc8cf
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/08/2017
---
# <a name="overview-of-azure-service-fabric"></a>Información general de Azure Service Fabric
Azure Service Fabric es una plataforma de sistemas distribuidos que facilita el empaquetado, la implementación y la administración de microservicios y contenedores escalables y confiables. Service Fabric también aborda los desafíos importantes en el desarrollo y la administración de aplicaciones nativas en la nube. Los desarrolladores y administradores pueden evitar problemas complejos de infraestructura y centrarse en su lugar en las cargas de trabajo más exigentes y críticas que son escalables, confiables y fáciles de administrar. Service Fabric representa la plataforma de próxima generación para crear y administrar estas aplicaciones de clase empresarial, escala de nube y nivel 1 que se ejecutan en contenedores.

En este breve vídeo se presentan Service Fabric y los microservicios: <center><a target="_blank" href="https://aka.ms/servicefabricvideo">  
<img src="./media/service-fabric-overview/OverviewVid.png" WIDTH="360" HEIGHT="244">  
</a></center>

## <a name="applications-composed-of-microservices"></a>Aplicaciones compuestas por microservicios 
Service Fabric le permite compilar y administrar aplicaciones escalables y confiables compuestas por microservicios que se ejecutan con una densidad alta en un grupo compartido de máquinas, denominado clúster. Ofrece un sofisticado y ligero sistema en tiempo de ejecución para crear microservicios con y sin estado, distribuidos y escalables que se ejecutan en contenedores. También ofrece una completa funcionalidad de administración de aplicaciones para aprovisionar, implementar, supervisar, actualizar/aplicar revisiones y eliminar aplicaciones implementadas, incluidos los servicios en contenedor.

En la actualidad, Service Fabric se utiliza en muchos servicios Microsoft, como Azure SQL Database, Azure Cosmos DB, Cortana, Microsoft Power BI, Microsoft Intune, Azure Event Hubs, Azure IoT Hub, Dynamics 365, Skype Empresarial y muchos servicios principales de Azure.

Service Fabric se adapta para crear servicios nativos de la nube que pueden empezar con un tamaño pequeño, según sea necesario, y crecer a gran escala con cientos o miles de máquinas.

Actualmente, los servicios de escala de Internet se crean a partir de microservicios. Entre los ejemplos de microservicios se encuentran las puertas de enlace de protocolo, perfiles de usuario, carros de la compra, procesamiento de inventario, colas y memorias caché. Service Fabric es una plataforma de microservicios que da a cada microservicio (o contenedor) un nombre único con o sin estado.

Service Fabric ofrece funciones completas de administración del ciclo de vida y de tiempo de ejecución para las aplicaciones que están compuestas de estos microservicios. Hospeda microservicios dentro de contenedores que se implementan y activan en el clúster de Service Fabric. La migración desde las máquinas virtuales a los contenedores hace posible un aumento de un orden de magnitud en la densidad. De manera similar, se hace posible otro orden de magnitud en la densidad si se migra de contenedores a microservicios en estos contenedores. Por ejemplo, un único clúster de Azure SQL Database se compone de cientos de máquinas que ejecutan decenas de miles de contenedores que hospedan un total de cientos de miles de bases de datos. Cada base de datos es un microservicio con estado de Service Fabric. 

Para más información sobre el enfoque de los microservicios, lea [¿Por qué usar un enfoque de microservicios para crear aplicaciones?](service-fabric-overview-microservices.md)

## <a name="container-deployment-and-orchestration"></a>Organización e implementación de contenedores
Service Fabric es el [orquestador de contenedores](service-fabric-cluster-resource-manager-introduction.md) de Microsoft que implementa microservicios en un clúster de máquinas. Se pueden desarrollar microservicios de muchas maneras, desde usando los [modelos de programación de Service Fabric](service-fabric-choose-framework.md) o [ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md) hasta implementando [cualquier código que prefiera](service-fabric-deploy-existing-app.md). Lo importantes es que puede mezclar ambos servicios en procesos y servicios en contenedores en la misma aplicación. Si simplemente quiere [implementar y administrar contenedores](service-fabric-containers-overview.md), Service Fabric es la elección perfecta como orquestador de contenedores.

## <a name="any-os-any-cloud"></a>Cualquier sistema operativo, cualquier nube
Service Fabric se ejecuta en todas partes. Puede crear clústeres de Service Fabric en muchos entornos, incluido Azure o en entornos locales, en Windows Server o en Linux. Incluso puede crear clústeres en otras nubes públicas. Además, el entorno de desarrollo del SDK es **idéntico** al de producción; no se usa ningún emulador. En otras palabras, lo que se ejecuta en el clúster de desarrollo local, se implementa en los clústeres de otros entornos.

![Plataforma de Service Fabric][Image1]

Para el desarrollo de Windows, el SDK de .NET de Service Fabric se integra con Visual Studio y Powershell. Consulte [Preparación del entorno de desarrollo en Windows](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started.md). Para el desarrollo de Linux, el SDK de Java de Service Fabric se integra con Eclipse y Yeoman se usa para generar plantillas para Java, .NET Core y aplicaciones de contenedor. Consulte [Preparación del entorno de desarrollo en Linux](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started.md)

Para obtener más información sobre la creación de clústeres, lea sobre la [creación de un clúster en Windows Server o Linux](service-fabric-deploy-anywhere.md) o sobre la creación de un clúster de Azure [mediante Azure Portal](service-fabric-cluster-creation-via-portal.md).

## <a name="stateless-and-stateful-microservices-for-service-fabric"></a>Microservicios de Service Fabric con estado y sin estado
Service Fabric permite compilar aplicaciones que constan de microservicios o contenedores. Los microservicios sin estado (como puertas de enlace de protocolo y servidores proxy web) no mantienen un estado mutable fuera de una petición y de su respuesta del servicio. Los roles de trabajo de Azure Cloud Services son un ejemplo de servicio sin estado. Los microservicios con estado (como cuentas de usuario, bases de datos, dispositivos, carros de la compra y colas) mantienen un estado mutable y autoritativo más allá de la petición y su respuesta. Actualmente, las aplicaciones de escala de Internet se componen de una combinación de microservicios con estado y sin estado. 

Una diferencia clave con Service Fabric es la gran importancia que da a la creación de servicios con estado, ya sea con los [modelos de programación integrados](service-fabric-choose-framework.md) o con servicios con estado en contenedores. En los [escenarios de aplicación](service-fabric-application-scenarios.md), se describen aquellos donde se usan servicios con estado.


## <a name="application-lifecycle-management"></a>Administración del ciclo de vida de aplicación
Service Fabric ofrece compatibilidad para la administración completa del ciclo de vida de aplicaciones y de CI/CD de aplicaciones en la nube, incluidos los contenedores. Este ciclo de vida incluye el desarrollo mediante la implementación, la administración diaria y el mantenimiento para una posible retirada.

Las funciones de la administración del ciclo de vida de las aplicaciones de Service Fabric permiten a los administradores de aplicación y a los operadores de TI usar flujos de trabajo simples y de baja interacción para aprovisionar, implementar, aplicar revisiones y supervisar aplicaciones. Estos flujos de trabajo integrados reducen en gran medida la carga de los operadores de TI para mantener las aplicaciones continuamente disponibles.

La mayoría de las aplicaciones se componen de una combinación de microservicios con estado y sin estado, contenedores y otros ejecutables que se implementan juntos. Al tener tipos seguros en las aplicaciones, Service Fabric permite la implementación de varias instancias de aplicación. Cada instancia se administra y actualiza de manera independiente. Lo importante es que Service Fabric puede implementar contenedores o cualquier ejecutable y hacer que sean confiables. Por ejemplo, Service Fabric puede implementar .NET, ASP.NET Core, node.js, contenedores de Windows, contenedores de Linux, máquinas virtuales Java, scripts, Angular o literalmente todo lo que compone la aplicación.

Service Fabric se integra con herramientas de CI/CD como [Visual Studio Team Services](https://www.visualstudio.com/team-services/), [Jenkins](https://jenkins.io/index.html) y [Octopus Deploy](https://octopus.com/), y puede utilizarse con cualquier otra herramienta de CI/CD popular.

Para más información sobre la administración del ciclo de vida de las aplicaciones, lea [Ciclo de vida de la aplicación de Service Fabric](service-fabric-application-lifecycle.md). Para más información sobre cómo implementar cualquier código, consulte [Implementación de un ejecutable invitado](service-fabric-deploy-existing-app.md).

## <a name="key-capabilities"></a>Principales capacidades
Usando Service Fabric, puede:

* Implementar en Azure o en centros de datos locales que ejecutan Windows o Linux con cero cambios de código. Crear una vez e implementar en cualquier clúster de Service Fabric.
* Desarrollar aplicaciones escalables que están compuestas por microservicios mediante los modelos de programación de Service Fabric, contenedores o cualquier código.
* Desarrollar microservicios con estado y sin estado que desean de alta confianza. Simplificar el diseño de su aplicación, mediante microservicios con estado. 
* Usar el novedoso modelo de programación Reliable Actors para crear objetos de nube con estado y código autónomo.
* Implementar y orquestar contenedores que incluyen contenedores de Windows y de Linux. Service Fabric es un orquestador de contenedores, con estado y reconocimiento de datos.
* Implementar aplicaciones en segundos, con una elevada densidad de cientos o miles de aplicaciones o contenedores por máquina.
* Implementar versiones diferentes de la misma aplicación en paralelo, y actualizar cada una por separado.
* Administrar el ciclo de vida de sus aplicaciones sin tiempo de inactividad, incluidas las actualizaciones de última hora y las que no lo son.
* Escalar o reducir horizontalmente el número de nodos en un clúster. Según se escalan los nodos, las aplicaciones se escalan automáticamente.
* Supervisar y diagnosticar el estado de sus aplicaciones y establecer directivas para realizar reparaciones automáticas.
* Observar al equilibrador de recursos orquestar la redistribución de aplicaciones en todo el clúster. Service Fabric se recupera de los errores y optimiza la distribución de la carga según los recursos disponibles.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Pasos siguientes
* Para obtener más información:
  * [¿Por qué usar un enfoque de microservicios para crear aplicaciones?](service-fabric-overview-microservices.md)
  * [Información general sobre la terminología](service-fabric-technical-overview.md)
* Configuración del [entorno de desarrollo de Windows](service-fabric-get-started.md)  
* Configuración del [entorno de desarrollo de Linux](service-fabric-get-started-linux.md)
* Más información sobre las [opciones de soporte técnico de Service Fabric](service-fabric-support.md)

[Image1]: media/service-fabric-overview/Service-Fabric-Overview.png
