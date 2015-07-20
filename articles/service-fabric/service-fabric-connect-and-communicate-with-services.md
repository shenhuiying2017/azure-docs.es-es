<properties
   pageTitle="Service Fabric de Microsoft Azure Cómo comunicarse con servicios"
   description="En este artículo se describe cómo puede conectarse con los servicios y comunicarse con ellos en las aplicaciones de Service Fabric."
   services="service-fabric"
   documentationCenter=".net"
   authors="kunaldsingh"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="04/29/2015"
   ms.author="kunalds"/>


# Comunicación con los servicios
En el mundo de los microservicios la solución general se compone de muchos servicios diferentes, donde cada uno de ellos realiza una tarea especializada. Estos microservicios se comunican entre sí para habilitar el flujo de trabajo completo. También hay aplicaciones cliente que se conectan a los servicios y se comunican con ellos. En este documento se describe cómo Service Fabric le facilita la configuración de la comunicación con los servicios que crea con Service Fabric.

## Conceptos clave
Estos son algunos de los conceptos clave que debe tener en cuenta al configurar la comunicación con los servicios.
### La comunicación se representa como cliente-servidor
Las API de comunicación de Service Fabric representan el tipo de servicio de cliente de interacción, incluso en el caso donde se encuentra entre dos servicios que se ejecutan en el mismo clúster. Un servicio de destino al que se conectará desde un cliente u otro servicio actúa como un servidor y escucha las solicitudes entrantes. El cliente, que puede ser simplemente otro servicio en el clúster, se conecta al servidor y realiza llamadas para él.
### Movimiento de servicios
En un sistema distribuido las instancias de servicio que se ejecutan se pueden mover desde un equipo a otro con el tiempo por diversas razones, como el equilibrio de carga al configurarse con métricas de carga para el equilibrio de recursos o debido a actualizaciones, conmutaciones por error, escalada horizontal y otras diversas situaciones. El impacto de esto es que las direcciones del extremo de una instancia de servicio pueden cambiar y que para configurar la comunicación con la instancia del servicio, se tiene que ejecutar el siguiente bucle. Estos detalles se abstraen del usuario si usa las API de comunicación ofrecidas por Service Fabric.

* **Resolver**: todas las instancias de servicios de Service Fabric tienen URI únicos, por ejemplo, "fabric:/MyApplication/MyService" y estos URI no cambian con el tiempo. Cada instancia de servicio también expone los extremos que pueden cambiar cuando se mueven las instancias de servicio. Esto es análogo a los sitios web que tienen direcciones URL constantes, pero la dirección IP puede cambiar. Y de manera similar al DNS en World Wide Web que resuelve direcciones URL del sitio web en direcciones IP, la plataforma de Service Fabric también tiene un servicio de sistema que resuelve los URI a los extremos. Este paso implica la resolución del URI de la instancia de servicio a un extremo.

* **Conectar**: cuando se haya resuelto el URI de servicio a las direcciones de extremo, el siguiente paso es intentar conectarse a ese servicio. Esta conexión puede generar un error si la dirección del extremo ha cambiado debido a un movimiento de servicio que, por ejemplo, puede haberse debido a un error de equipo o debido al equilibrio de carga.

* **Reintentar**: si el intento de conexión produce un error, los pasos de conexión y resolución anteriores se tienen que volver a intentar y este ciclo se repite hasta que la conexión sea correcta.

## Decidir la API de comunicación que se va a usar
Como parte de Service Fabric ofrecemos algunas opciones diferentes para las API de comunicación y la decisión de que cuál funcionará mejor para usted depende de la elección del modelo de programación, del marco de comunicación y del lenguaje de programación en que se crean los servicios.
### Comunicación para los actores confiables
Para los servicios creados con la API de actores confiables, se abstraen todos los detalles de comunicación y la comunicación se produce cuando el método llama en el ActorProxy y puede dejar de leer aquí.

### Opciones de comunicación para los servicios de confianza
Hay un par de opciones diferentes si el servicio se ha escrito con la API de servicios de confianza. Su elección de un protocolo de comunicación guiará en cuanto a qué API de comunicación de Service Fabric que puede usar.

* **No hay un protocolo de comunicación específico que necesito usar y quiero algo en marcha rápidamente**: si no tiene una opción concreta de marco de comunicación, la opción idónea para usted es usar la [Pila predeterminada](service-fabric-reliable-services-communication-default.md) que permite un modelo similar como el modelo de comunicación de Actor. Esta es la manera más sencilla y rápida de empezar con la comunicación del servicio. Ofrece una comunicación RPC fuertemente tipada que abstrae la mayoría de los detalles de comunicación para que invocar un método en un servicio remoto en su código sea como llamar a un método en una instancia de objeto local. Estas clases abstraen en control de la resolución, la conexión, los reintentos y los errores al configurar el canal de comunicación y permite un modelo de comunicación basado en llamadas de método. Para ello la clase `ServiceCommunicationListener` se usará para el lado servidor y la clase `ServiceProxy` del lado cliente de la comunicación.

* **HTTP**: para aprovechar la flexibilidad que la comunicación basada en HTTP le ofrece, puede usar las API de comunicación de Service Fabric que permiten que se defina el mecanismo de comunicación a la vez que la resolución, la conexión y la lógica de reintentos se siguen abstrayendo del usuario. Por ejemplo, puede usar la API web para especificar el mecanismo de comunicación y aprovechar las clases [`ICommunicationClient` y `ServicePartitionClient` ](service-fabric-reliable-services-communication.md) para configurar la comunicación.
* **WCF**: si tiene código existente que usa WCF como su marco de comunicación, puede usar el WcfCommunicationListener para el lado servidor, y las clases WcfCommunicationClient y ServicePartitionClient para el cliente. Vea [este artículo](service-fabric-reliable-services-communication-wcf.md) para obtener más detalles.

* **Otros marcos de comunicación, incluidos los protocolos personalizados**: si está planeando el uso de cualquier otro marco de comunicación incluidos los protocolos de comunicación personalizados, hay unas API de comunicación de Service Fabric en las que puede conectar su pila de comunicación de la pila mientras que se abstrae para usted todo el trabajo que se debe detectar y conectar. Consulte [este artículo](service-fabric-reliable-services-communication.md) para obtener más detalles.

### Comunicación entre servicios creados en diferentes lenguajes de programación
Todas las API de comunicación de ServiceFabric solo están disponibles actualmente en C# por lo que si tiene un servicio que está creado en algún otro lenguaje de programación como Java o Node.JS, tendrá que crear su propio mecanismo de comunicación.

## Pasos siguientes
* [Pila de comunicación predeterminada ofrecida por el marco de servicios de confianza ](service-fabric-reliable-services-communication-default.md)
* [Modelo de comunicación de servicios de confianza](service-fabric-reliable-services-communication.md)
* [Introducción a los servicios de la API web de Service Fabric de Microsoft Azure con el autohospedaje de OWIN](service-fabric-reliable-services-communication-webapi.md)
* [Pila de comunicación basada en WCF de servicios de confianza](service-fabric-reliable-services-communication-wcf.md)
 

<!---HONumber=July15_HO2-->