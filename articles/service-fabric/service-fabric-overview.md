<properties
   pageTitle="Información general de Service Fabric | Microsoft Azure"
   description="Información general de Service Fabric donde las aplicaciones se componen de muchos microservicios para ofrecer escala y resiliencia. Service Fabric es una plataforma de sistemas distribuidos que se usa para crear aplicaciones escalables, confiables y fáciles de administrar para la nube."
   services="service-fabric"
   documentationCenter=".net"
   authors="msfussell"
   manager="timlt"
   editor="masnider"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/03/2016"
   ms.author="mfussell"/>

# Información general de Service Fabric
Service Fabric es una plataforma de sistemas distribuidos que facilita el empaquetamiento, la implementación y la administración de microservicios escalables y confiables. Service Fabric también aborda los desafíos importantes en el desarrollo y la administración de aplicaciones en la nube. Los desarrolladores y administradores pueden evitar solucionar problemas complejos de infraestructura y centrarse en su lugar en la implementación de cargas de trabajo exigentes y críticas, sabiendo que son escalables, confiables y fáciles de administrar. Service Fabric representa la plataforma middleware de próxima generación para crear y administrar estas aplicaciones de clase empresarial, de escala de nube de nivel 1.

## Aplicaciones compuestas por microservicios
Service Fabric permite compilar y administrar aplicaciones escalables y confiables compuestas por microservicios que se ejecutan con una densidad muy alta en un grupo compartido de máquinas (que se denomina clúster). Ofrece un sofisticado tiempo de ejecución para crear microservicios con y sin estado distribuidos y escalables. También proporciona capacidades de administración de aplicaciones completas para el aprovisionamiento, la implementación, la supervisión, la actualización/aplicación de revisiones y eliminación de aplicaciones implementadas.

¿Por qué son importantes los microservicios? Las dos razones principales son:

1. Le permiten escalar diferentes partes de la aplicación, según sus necesidades.

2. Los equipos de desarrollo pueden ser más ágiles en la implementación de los cambios y, por tanto, dar servicio a los clientes con mayor rapidez y con más frecuencia.

Service Fabric impulsa muchos servicios de Microsoft en la actualidad, como Base de datos SQL de Azure, DocumentDB de Azure, Cortana, Power BI, Microsoft Intune, Centros de eventos de Azure, IoT de Azure, Skype Empresarial y muchos servicios principales de Azure.

Service Fabric se adapta para crear servicios "nacidos en la nube" que pueden comenzar con un tamaño pequeño, según sea necesario y crecer a gran escala con cientos o miles de máquinas.

Actualmente, los servicios de escala de Internet se crean a partir de microservicios. Entre los ejemplos de microservicios se encuentran las puertas de enlace de protocolo, perfiles de usuario, carros de la compra, procesamiento de inventario, colas y memorias caché. Service Fabric es una plataforma de microservicios que da a cada microservicio un nombre único con o sin estado.

Service Fabric ofrece capacidades completas de administración del ciclo de vida y de tiempo de ejecución para las aplicaciones compuestas de estos microservicios. Hospeda microservicios dentro de contenedores implementados y activados en el clúster de Service Fabric. La migración desde VM a contenedores hace posible un aumento de un orden de magnitud en la densidad. De manera similar, otra orden de magnitud en la densidad es posible si se migra desde los contenedores a los microservicios. Por ejemplo, un único clúster de base de datos SQL de Azure se compone de cientos de máquinas que ejecutan decenas de miles de contenedores que hospedan un total de cientos de miles de bases de datos. Cada base de datos es un microservicio con estado de Service Fabric. Lo mismo se aplica a los demás servicios mencionados anteriormente, lo que explica por qué se usa el término "hiperescala" para describir las funcionalidades de Service Fabric. Si los contenedores dan una alta densidad, los microservicios proporcionan hiperescala.

Para más información sobre el enfoque de los microservicios, lea [¿Por qué usar un enfoque de microservicios para crear aplicaciones?](service-fabric-overview-microservices.md)

## Crear clústeres de Service Fabric en cualquier lugar
Puede crear clústeres de Service Fabric en muchos entornos, incluido Azure o entornos locales, en Windows Server o en Linux. Además, el entorno de desarrollo del SDK es idéntico al entorno de producción; no se usa ningún emulador. En otras palabras, si se ejecuta en el clúster de desarrollo local, se implementará en el mismo clúster en otros entornos.

Para más información sobre la creación de clústeres locales, lea sobre la [creación de un clúster en Windows Server o Linux](service-fabric-deploy-anywhere.md) o sobre la creación de un clúster de Azure [mediante el Portal de Azure](service-fabric-cluster-creation-via-portal.md).

![Plataforma de Service Fabric][Image1]

## Microservicios de Service Fabric con estado y sin estado

Service Fabric permite compilar aplicaciones que constan de microservicios. Los microservicios sin estado (puertas de enlace de protocolo, servidores proxy web, etc.) no mantienen ningún estado mutable fuera de ninguna solicitud determinada y su respuesta del servicio. Los roles de trabajo de los servicios en la nube de Azure son un ejemplo de servicio sin estado. Los microservicios con estado (cuentas de usuario, bases de datos, dispositivos, carros de la compra, colas, etc.) mantienen un estado mutable y autoritativo más allá de la solicitud y su respuesta. Actualmente, las aplicaciones de escala de Internet se componen de una combinación de microservicios con estado y sin estado.

¿Por qué tener microservicios con estado con otros sin estado? Las dos razones principales son:

1. La capacidad de crear servicios OLTP tolerantes a errores, de baja latencia y alto rendimiento al mantener el código y los datos cerca en la misma máquina. Algunos ejemplos son interfaces de usuario interactivas, búsqueda, sistemas de Internet de las cosas (IoT), sistemas de comercialización, sistemas de detección de fraudes y procesamiento de tarjetas de crédito y, además, administración de registros personales.

2. Simplificación del diseño de la aplicación. Los microservicios con estado eliminan la necesidad de colas y memorias caché adicionales, las que tradicionalmente se han requerido para tratar los requisitos de disponibilidad y latencia de una aplicación puramente sin estado. Los servicios con estado son de alta disponibilidad y de baja latencia de forma natural, lo que disminuye la cantidad de partes en movimiento que administrar en la aplicación en su conjunto.

Para más información sobre los patrones de aplicación con Service Fabric, lea [Escenarios de aplicación](service-fabric-application-scenarios.md) y [Selección de un marco de modelo de programación](service-fabric-choose-framework.md) para el servicio.

## Administración del ciclo de vida de aplicación
Service Fabric ofrece compatibilidad de primera clase para toda la administración del ciclo de vida de aplicación (ALM) de las aplicaciones de nube: desde el desarrollo hasta la implementación, la administración diaria, el mantenimiento y, finalmente, la retirada.

Las capacidades de ALM de Service Fabric permiten a los administradores de aplicación/operadores de TI usar flujos de trabajo simples y de baja interacción para aprovisionar, implementar, aplicar revisiones y supervisar aplicaciones. Estos flujos de trabajo integrados reducen en gran medida la carga de los operadores de TI para mantener las aplicaciones continuamente disponibles.

La mayoría de las aplicaciones constan de una combinación de microservicios con estado y sin estado y otros tiempos de ejecución/ejecutables que se implementan juntos. Al tener tipos seguros en las aplicaciones y los microservicios empaquetados, Service Fabric permite la implementación de varias instancias de aplicación. Cada instancia se administra y actualiza de manera independiente. Lo importante es que Service Fabric puede implementar *cualquier* ejecutable o sistema en tiempo de ejecución y hacer que sean confiables. Por ejemplo, Service Fabric implementa ASP.NET Core 1, Node.js, VM de Java, scripts o cualquier otro elemento que conforme la aplicación.

Para más información sobre la administración del ciclo de vida de las aplicaciones, lea [Ciclo de vida de la aplicación](service-fabric-application-lifecycle.md). Si quiere informarse sobre la implementación de cualquier código, consulte [Implementación de un ejecutable invitado](service-fabric-deploy-existing-app.md).

## Principales capacidades
Usando Service Fabric, puede:

- Desarrollar aplicaciones escalables de forma masiva, de recuperación automática.

- Desarrollar aplicaciones compuestas por microservicios mediante el modelo de programación de Service Fabric. O bien, simplemente hospedar ejecutables invitados y otros marcos de trabajo de la aplicación de su preferencia, como ASP.NET Core 1 o Node.js.

- Desarrollar microservicios con estado y sin estado que desean de alta confianza.

- Simplificar el diseño de su aplicación, mediante el uso de microservicios con estado en lugar de memorias caché y colas.

- Implementar en Azure o en nubes locales que ejecutan Windows Server o Linux con cero cambios de código. Crear una vez e implementar en cualquier clúster de Service Fabric.

- Desarrollar con un enfoque de "centro de datos en su equipo". El entorno de desarrollo local es el mismo código que se ejecuta en los centros de datos de Azure.

- Implementar aplicaciones en cuestión de segundos.

- Implementar aplicaciones en una densidad mayor que las máquinas virtuales, implementando cientos o miles de aplicaciones por equipo.

- Implementar versiones diferentes de la misma aplicación en paralelo, siendo cada una de ellas actualizable por separado.

- Administrar el ciclo de vida de sus aplicaciones con estado sin tiempo de inactividad, incluidas las actualizaciones de última hora y las que no lo son.

- Administrar aplicaciones mediante las API de .NET, PowerShell o las interfaces de REST.

- Actualizar y aplicar revisiones a microservicios dentro de las aplicaciones de manera independiente.

- Supervisar y diagnosticar el estado de sus aplicaciones y establecer directivas para realizar reparaciones automáticas.

- Escalar o reducir verticalmente su clúster de Service Fabric con facilidad, sabiendo que las aplicaciones se escalan según los recursos disponibles.

- Inspeccionar el equilibrador de recursos de recuperación automática organizar la redistribución de aplicaciones en todo el clúster. Service Fabric se recupera de los errores y optimiza la distribución de la carga según los recursos disponibles.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Pasos siguientes

* Para obtener más información:
    * [¿Por qué usar un enfoque de microservicios para crear aplicaciones?](service-fabric-overview-microservices.md)
    * [Información general sobre la terminología](service-fabric-technical-overview.md)
* Configuración del [entorno de desarrollo](service-fabric-get-started.md) de Service Fabric
* [Selección de un marco de modelo de programación](service-fabric-choose-framework.md) para el servicio

[Image1]: media/service-fabric-overview/Service-Fabric-Overview.png

<!---HONumber=AcomDC_0810_2016-->