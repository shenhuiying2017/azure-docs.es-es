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
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/05/2017
ms.author: mfussell
translationtype: Human Translation
ms.sourcegitcommit: 7033955fa9c18b2fa1a28d488ad5268d598de287
ms.openlocfilehash: 0e899225063e77ccef254e8aaacbf0390faa25e3


---
# <a name="overview-of-azure-service-fabric"></a>Información general de Azure Service Fabric
Azure Service Fabric es una plataforma de sistemas distribuidos que facilita el empaquetamiento, la implementación y la administración de microservicios escalables y confiables. Service Fabric también aborda los desafíos importantes en el desarrollo y la administración de aplicaciones en la nube. Los desarrolladores y administradores pueden evitar problemas complejos de infraestructura y centrarse en su lugar en las cargas de trabajo más exigentes y críticas que son escalables, confiables y fáciles de administrar. Service Fabric representa la plataforma middleware de próxima generación para crear y administrar estas aplicaciones de clase empresarial, de escala de nube de nivel&1;.

Este breve vídeo de Channel9 presenta Service Fabric y los microservicios: <center><a target="_blank" href="https://aka.ms/servicefabricvideo">  
<img src="./media/service-fabric-overview/OverviewVid.png" WIDTH="360" HEIGHT="244">  
</a></center>

En este vídeo más largo de Microsoft Virtual Academy se describen los conceptos básicos de Service Fabric: <center><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=tbuZM46yC_5206218965">  
<img src="./media/service-fabric-overview/CoreConceptsVid.png" WIDTH="360" HEIGHT="244">  
</a></center>


## <a name="applications-composed-of-microservices"></a>Aplicaciones compuestas por microservicios
Service Fabric le permite compilar y administrar aplicaciones escalables y confiables compuestas por microservicios que se ejecutan con una densidad muy alta en un grupo compartido de máquinas; que se denomina clúster. Ofrece un sofisticado tiempo de ejecución para crear microservicios con y sin estado, distribuidos y escalables. También ofrece funciones de administración de aplicaciones completas para aprovisionar, implementar, supervisar, actualizar/aplicar revisiones y eliminar aplicaciones implementadas.

¿Por qué son importantes los microservicios? Las dos razones principales son:

* Puede escalar diferentes partes de la aplicación, según las necesidades de esta.
* Los equipos de desarrollo pueden ser más ágiles en la implementación de los cambios y, por tanto, dar servicio a los clientes con mayor rapidez y con más frecuencia.

Service Fabric impulsa muchos servicios Microsoft en la actualidad, como Azure SQL Database, Azure DocumentDB, Cortana, Microsoft Power BI, Microsoft Intune, Azure Event Hubs, IoT Hub de Azure, Skype Empresarial y muchos servicios principales de Azure.

Service Fabric se adapta para crear servicios nativos de la nube que pueden empezar con un tamaño pequeño, según sea necesario, y crecer a gran escala con cientos o miles de máquinas.

Actualmente, los servicios de escala de Internet se crean a partir de microservicios. Entre los ejemplos de microservicios se encuentran las puertas de enlace de protocolo, perfiles de usuario, carros de la compra, procesamiento de inventario, colas y memorias caché. Service Fabric es una plataforma de microservicios que da a cada microservicio un nombre único con o sin estado.

Service Fabric ofrece funciones completas de administración del ciclo de vida y de tiempo de ejecución para las aplicaciones que están compuestas de estos microservicios. Hospeda microservicios dentro de contenedores que se implementan y activan en el clúster de Service Fabric. La migración desde las máquinas virtuales a los contenedores hace posible un aumento de un orden de magnitud en la densidad. De manera similar, otra orden de magnitud en la densidad es posible si se migra desde los contenedores a los microservicios. Por ejemplo, un único clúster de Azure SQL Database se compone de cientos de máquinas que ejecutan decenas de miles de contenedores que hospedan un total de cientos de miles de bases de datos. Cada base de datos es un microservicio con estado de Service Fabric. Lo mismo se aplica a los demás servicios mencionados anteriormente, lo que explica por qué se usa el término *hiperescala* para describir las funciones de Service Fabric. Si los contenedores dan una alta densidad, los microservicios proporcionan hiperescala.

Para más información sobre el enfoque de los microservicios, lea [¿Por qué usar un enfoque de microservicios para crear aplicaciones?](service-fabric-overview-microservices.md)

## <a name="container-deployment-and-orchestration"></a>Organización e implementación de contenedores
Service Fabric es un [organizador](service-fabric-cluster-resource-manager-introduction.md) de microservicios a través de un clúster de máquinas. Se pueden desarrollar microservicios de muchas maneras, desde el uso de los [modelos de programación de Service Fabric ](service-fabric-choose-framework.md) a la implementación de [ejecutables invitados](service-fabric-deploy-existing-app.md). Service Fabric puede implementar servicios en imágenes de contenedor. Lo importantes es que puede mezclar ambos servicios en procesos y servicios en contenedores en la misma aplicación. Si simplemente quiere [implementar y administrar imágenes de contenedor](service-fabric-containers-overview.md) a través de un clúster de máquinas, Service Fabric es la elección perfecta.

## <a name="create-clusters-for-service-fabric-anywhere"></a>Crear clústeres de Service Fabric en cualquier lugar
Puede crear clústeres de Service Fabric en muchos entornos, incluido Azure o en entornos locales, en Windows Server o en Linux. Además, el entorno de desarrollo del SDK es idéntico al entorno de producción; no se usa ningún emulador. En otras palabras, lo que se ejecuta en el clúster de desarrollo local, se implementa en el mismo clúster en otros entornos.

Para más información sobre la creación de clústeres locales, lea [Creación de clústeres de Service Fabric en Windows Server o Linux](service-fabric-deploy-anywhere.md) o sobre la creación de un clúster de Azure [mediante Azure Portal](service-fabric-cluster-creation-via-portal.md).

![Plataforma de Service Fabric][Image1]

## <a name="stateless-and-stateful-micrososervices-for-service-fabric"></a>Microservicios de Service Fabric con estado y sin estado
Service Fabric permite compilar aplicaciones que constan de microservicios. Los microservicios sin estado (como puertas de enlace de protocolo y servidores proxy web) no mantienen un estado mutable fuera de una petición y de su respuesta del servicio. Los roles de trabajo de los servicios en la nube de Azure son un ejemplo de servicio sin estado. Los microservicios con estado (como cuentas de usuario, bases de datos, dispositivos, carros de la compra y colas) mantienen un estado mutable y autoritativo más allá de la petición y su respuesta. Actualmente, las aplicaciones de escala de Internet se componen de una combinación de microservicios con estado y sin estado.

¿Por qué tener microservicios con estado con otros sin estado? Las dos razones principales son:

* Puede crear servicios OLTP tolerantes a errores, de latencia baja y alto rendimiento al mantener el código y los datos cerca en la misma máquina. Algunos ejemplos son interfaces de usuario interactivas, búsqueda, sistemas de Internet de las cosas (IoT), sistemas de comercialización, sistemas de detección de fraudes y procesamiento de tarjetas de crédito y, además, administración de registros personales.
* Puede simplificar el diseño de la aplicación. Los microservicios con estado eliminan la necesidad de colas y memorias caché adicionales, que son las que tradicionalmente se han necesitado para tratar los requisitos de disponibilidad y latencia de una aplicación puramente sin estado. Los servicios con estado son de alta disponibilidad y de latencia baja de forma natural, lo que disminuye la cantidad de partes en movimiento que administrar en la aplicación en su conjunto.

Para más información sobre los patrones de aplicación con Service Fabric, lea [Escenarios de aplicación de Service Fabric](service-fabric-application-scenarios.md) e [Información general del modelo de programación de Service Fabric](service-fabric-choose-framework.md) para el servicio.

También puede ver este vídeo de Microsoft Virtual Academy para obtener información general sobre servicios sin estado y con estado: <center><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=HhD9566yC_4106218965">  
<img src="./media/service-fabric-overview/ReliableServicesVid.png" WIDTH="360" HEIGHT="244">  
</a></center>

## <a name="application-lifecycle-management"></a>Administración del ciclo de vida de aplicación
Service Fabric ofrece compatibilidad para la administración completa del ciclo de vida de aplicaciones de las aplicaciones en la nube. Este ciclo de vida incluye el desarrollo mediante la implementación, la administración diaria y el mantenimiento para una posible retirada.

Las funciones de la administración del ciclo de vida de las aplicaciones de Service Fabric permiten a los administradores de aplicación y a los operadores de TI usar flujos de trabajo simples y de baja interacción para aprovisionar, implementar, aplicar revisiones y supervisar aplicaciones. Estos flujos de trabajo integrados reducen en gran medida la carga de los operadores de TI para mantener las aplicaciones continuamente disponibles.

La mayoría de las aplicaciones constan de una combinación de microservicios con estado y sin estado y otros tiempos de ejecución/ejecutables que se implementan juntos. Al tener tipos seguros en las aplicaciones y los microservicios empaquetados, Service Fabric permite la implementación de varias instancias de aplicación. Cada instancia se administra y actualiza de manera independiente. Lo importante es que Service Fabric puede implementar *cualquier* ejecutable o sistema en tiempo de ejecución y hacer que sean confiables. Por ejemplo, Service Fabric implementa .NET, ASP.NET Core, Node.js, máquinas virtuales de Java, scripts, Angular o cualquier otro elemento que forme parte de la aplicación.

Para más información sobre la administración del ciclo de vida de las aplicaciones, lea [Ciclo de vida de la aplicación de Service Fabric](service-fabric-application-lifecycle.md). Para más información sobre cómo implementar cualquier código, vea [Implementación de un ejecutable invitado en Service Fabric](service-fabric-deploy-existing-app.md).

También puede ver este vídeo de Microsoft Virtual Academy para obtener información general de la administración del ciclo de vida de las aplicaciones: <center><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=My3Ka56yC_6106218965">  
<img src="./media/service-fabric-overview/AppLifecycleVid.png" WIDTH="360" HEIGHT="244">  
</a></center>

## <a name="key-capabilities"></a>Principales capacidades
Usando Service Fabric, puede:

* Desarrollar aplicaciones escalables de forma masiva, de recuperación automática.
* Desarrollar aplicaciones que están compuestas por microservicios mediante el modelo de programación de Service Fabric. O bien, simplemente hospede ejecutables de invitado y otros entornos de aplicación que prefiera, como ASP.NET Core o Node.js.
* Desarrollar microservicios con estado y sin estado que desean de alta confianza.
* Implementar y organizar contenedores que incluyen contenedores de Windows y de Docker a través de un clúster. Estos contenedores pueden contener ejecutables invitados o microservicios con estado y sin estado confiables. En cualquier caso, obtendrá una asignación desde el puerto de contenedor al puerto de host, detectabilidad de contenedor y conmutación por error automatizada.
* Simplificar el diseño de su aplicación, mediante el uso de microservicios con estado en lugar de memorias caché y colas.
* Implementar en Azure o en centros de datos locales que ejecutan Windows o Linux con cero cambios de código. Crear una vez e implementar en cualquier clúster de Service Fabric.
* Desarrollar con un enfoque de "centro de datos en su equipo". El entorno de desarrollo local es el mismo código que se ejecuta en los centros de datos de Azure.
* Implementar aplicaciones en cuestión de segundos.
* Implementar aplicaciones en una densidad mayor que las máquinas virtuales, implementando cientos o miles de aplicaciones por equipo.
* Implementar versiones diferentes de la misma aplicación en paralelo, y actualizar cada una por separado.
* Administrar el ciclo de vida de sus aplicaciones con estado sin tiempo de inactividad, incluidas las actualizaciones de última hora y las que no lo son.
* Administrar aplicaciones mediante las API de .NET, Java (Linux), PowerShell, la interfaz de la línea de comandos de Azure (Linux) o las interfaces de REST.
* Actualizar y aplicar revisiones a microservicios dentro de las aplicaciones de manera independiente.
* Supervisar y diagnosticar el estado de sus aplicaciones y establecer directivas para realizar reparaciones automáticas.
* Escalar o reducir horizontalmente el número de nodos de un clúster, y escalar o reducir verticalmente el tamaño de cada nodo. Cuando escala nodos, sus aplicaciones se escalan automáticamente y se distribuyen según los recursos disponibles.
* Inspeccionar el equilibrador de recursos de recuperación automática organizar la redistribución de aplicaciones en todo el clúster. Service Fabric se recupera de los errores y optimiza la distribución de la carga según los recursos disponibles.
* Use el servicio de análisis de errores para realizar pruebas de caos en su servicio con el fin de encontrar problemas y errores antes de ejecutarlo en producción.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Pasos siguientes
* Para obtener más información:
  * [¿Por qué usar un enfoque de microservicios para crear aplicaciones?](service-fabric-overview-microservices.md)
  * [Información general sobre la terminología](service-fabric-technical-overview.md)
* Configuración del [entorno de desarrollo](service-fabric-get-started.md)  
* [Selección de un marco de modelo de programación](service-fabric-choose-framework.md) para el servicio
* Más información sobre las [opciones de soporte técnico de Service Fabric](service-fabric-support.md)

[Image1]: media/service-fabric-overview/Service-Fabric-Overview.png



<!--HONumber=Jan17_HO4-->


