<properties
   pageTitle="Introducción técnica"
   description="Introducción técnica a Service Fabric. Explica los conceptos claves y la introducción a la arquitectura"
   services="service-fabric"
   documentationCenter=".net"
   authors="msfussell"
   manager="timlt"
   editor="chackdan;subramar"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="04/14/2015"
   ms.author="mfussell"/>

# Introducción técnica a Service Fabric

Service Fabric es una plataforma de sistemas distribuidos que facilita la creación de aplicaciones escalables, con latencia reducida, fiables y fáciles de administrar para la nube. Esto significa que puede centrarse en las necesidades empresariales y permitir a Service Fabric encargarse de asegurarse de que la aplicación está siempre disponible y que se escala.

## Conceptos clave

**Clúster**: conjunto de máquinas físicas o virtuales conectado a red en las que se implementan instancias de aplicación. Los clústeres pueden escalar a miles de equipos.

**Nodo**: unidad direccionable de un clúster. Los nodos tienen características como propiedades de colocación e identificadores únicos. Los nodos pueden unirse a un clúster y correlacionarse con una instancia de sistema operativo con Fabric.exe ejecutándose.

**Aplicación/tipo de aplicación**: colección de (micro)servicios. Piense en un tipo de aplicación como un contenedor para uno o más tipos de servicio. Consulte el artículo [Modelo de aplicación](service-fabric-application-model.md) para entender cómo puede un clúster (que a su vez está compuesto por varios nodos) estar compuesto por varios ApplicationTypes.

**Servicio/tipo de servicio**: código y configuración que ejecuta una función independiente (puede iniciar y ejecutarse de forma independiente), por ejemplo, un servicio de cola o el servicio de base de datos. Un ApplicationType puede estar compuesto de uno o más ServiceTypes. Hay dos tipos de servicios:

- Servicio sin estado: servicio cuyo estado se conserva en el almacenamiento externo, como almacén de bases de datos de Azure o de tabla de Azure. Si se desactiva un nodo en el que está activa una instancia de este servicio, se inicia automáticamente otra instancia en otro nodo.

- Servicio con estado: servicio que tiene estado y consigue fiabilidad mediante la replicación entre réplicas en otros nodos del clúster. Servicios con estado tienen una réplica principal y varias réplicas secundarias. Si se desactiva un nodo en el que está activa una réplica de este servicio, se iniciará una nueva réplica en otro nodo y si se trataba de la réplica principal, se promocionará una réplica secundaria automáticamente a una nueva principal.

**Instancia de la aplicación**: en un clúster puede crear muchas instancias de aplicación de un tipo de aplicación, cada uno de los cuales tiene un nombre específico. Cada instancia de la aplicación puede administrarse independientemente y cambiar de versión desde otras instancias de la aplicación del mismo tipo o de un tipo diferente. Además definen el aislamiento de seguridad y recursos.

**Instancia de servicio**: código del que se ha creado una instancia para un tipo de servicio. Cada instancia de servicio tiene un nombre único que empieza por `fabric:/` y está asociado a una instancia de aplicación con nombre determinada.

**Paquete de aplicación**: colección de paquetes de código de servicio y archivos de configuración combinados para una aplicación determinada. Estos son los archivos físicos que se implementan y es simplemente un diseño de formato de archivo y carpeta. Por ejemplo, un paquete de aplicación para una aplicación de correo electrónico podría contener un paquete de servicio de cola, un paquete de servicio de front-end y un paquete de servicios de base de datos.

**Modelos de programación**: hay dos modelos de programación disponibles en Service Fabric para crear aplicaciones:

- Servicios fiables: una API para crear servicios sin estado y con estado basados en las clases .NET `StatelessService` y `StatefulService`, almacenando el estado en colecciones de .NET fiables (diccionario y cola) y con la capacidad de conectar una amplia variedad de pilas de comunicación como API web y WCF. Este modelo de programación es adecuado para aplicaciones en las que necesita realizar el proceso en varias unidades de estado.

- Actores fiables: API para crear objetos de estado y sin estado a través del modelo de programación de actor virtual que es adecuado para las aplicaciones con varias unidades independientes de estado y proceso.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Pasos siguientes
Para obtener más información acerca de Service Fabric, consulte:

- [Modelo de aplicación](service-fabric-application-model.md)
- [Escenarios de aplicación](service-fabric-application-scenarios.md)
 

<!---HONumber=August15_HO6-->