<properties
   pageTitle="Definición y administración del estado"
   description="Cómo definir y administrar el estado de servicio en Service Fabric"
   services="service-fabric"
   documentationCenter=".net"
   authors="appi101"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="04/13/2015"
   ms.author="aprameyr"/>

# Estado de servicio
**Estado del servicio** son los datos que el servicio requiere para poder funcionar. Son las variables y las estructuras de datos que el servicio lee y escribe para realizar el trabajo.

Por ejemplo: piense en un servicio de calculadora simple. Este servicio toma dos números y devuelve su suma. Se trata de un servicio puramente sin estado que no tiene datos asociados.

Ahora, considere la misma calculadora, pero además de calcular la suma, también dispone de un método para devolver la última suma que había calculado. Este servicio es ahora con estado; contiene algún estado en el que escribe (cuando calcula una nueva suma) y del que lee (cuando devuelve la última suma calculada).

En Service Fabric, el primer servicio se denomina Servicio sin estado. El segundo servicio se denomina servicio con estado.

## Almacenamiento del estado de servicio
El estado se puede externalizar o colocar con el código que está manipulando el estado. Externalización del estado normalmente se realiza mediante el uso de un almacén o una base de datos externa. En nuestro ejemplo de calculadora, esto podría tratarse de una base de datos SQL donde el resultado actual se almacena en una tabla. Cada solicitud para calcular la suma realiza una actualización en esta fila.

El estado también se puede colocar con el código que manipula este código. Los servicios con estado de Service Fabric se crean con este modelo. Service Fabric ofrece la infraestructura para garantizar que este estado es de alta disponibilidad y tolerante a errores en caso de errores.

## Pasos siguientes

Para obtener información sobre los conceptos de Service Fabric, vea lo siguiente:

- [Disponibilidad de los servicios de Service Fabric](service-fabric-availability-services.md)

- [Escalabilidad de los servicios de Service Fabric](service-fabric-concepts-scalability.md)

- [Creación de particiones de los servicios de Service Fabric](service-fabric-concepts-partitioning.md)
 

<!---HONumber=July15_HO4-->