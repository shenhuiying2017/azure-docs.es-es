<properties
   pageTitle="Creación de particiones de los servicios de Service Fabric"
   description="Describe cómo crear particiones de los servicios de Service Fabric"
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
   ms.date="08/26/2015"
   ms.author="aprameyr"/>

# Creación de particiones de los servicios de Service Fabric
Service Fabric facilita la creación de servicios con estado escalables al admitir la creación de particiones del estado de servicio y al tener cada partición funcionando en un subconjunto del estado total. Cada partición se convierte en una unidad que se hace de [alta disponibilidad](service-fabric-availability-services.md). Las réplicas de particiones se distribuyen entre los nodos del clúster y están equilibradas.

> [AZURE.NOTE]Aunque también se crean particiones de los servicios sin estado, este escenario es poco frecuente e innecesario para la mayoría de los servicios de Service Fabric.

Hay tres esquemas de partición diferentes disponibles.

## Esquema de particiones singleton
Se usa para especificar que el servicio no necesita la creación de particiones.

## Esquema de particiones con nombre
Se usa para especificar un conjunto fijo de nombres para cada partición del servicio. Se puede buscar en las particiones individuales por su nombre.

## Esquema de particiones de intervalo
Se usa para especificar un intervalo de enteros (identificado por una clave baja y alta) y un número de particiones (n). Crea n particiones, cada una de ellas responsable de un subintervalo no superpuesto. Ejemplo: un esquema de particiones de intervalo (para un servicio con tres réplicas) con una clave baja de 0, una clave alta de 99 y un recuento de 4 crearía 4 particiones como se muestra a continuación.

![Creación de particiones por intervalos](./media/service-fabric-concepts-partitioning/range-partitioning.png)

El caso común es crear un valor hash para una clave única dentro de un conjunto de datos. Algunos ejemplos comunes de claves serían un número de identificación de vehículo (VIN), id. de empleado o una cadena única. Con esa clave única crearía un código hash largo, módulo del intervalo de claves, para usarla como la clave. Puede especificar los límites superior e inferior del intervalo de claves permitido.

Además, debería calcular el número de particiones lo suficientemente alto como para controlar la carga de recursos del peor caso (como la limitaciones de memoria o el ancho de banda de red), pero no tanto como para que las particiones estén muy dispersas.

### Selección de un algoritmo hash
Una parte importante de los algoritmos hash es seleccionar su algoritmo hash. Una cuestión importante que se debe tener en cuenta es si el objetivo es agrupar claves similares próximas entre sí (algoritmos hash sensibles a ubicación), o si la actividad se debería distribuir ampliamente en todas las particiones (algoritmos hash de distribución).

Un buen recurso para las opciones de algoritmo de código hash generales es [la página de Wikipedia en las funciones hash](http://en.wikipedia.org/wiki/Hash_function).

> [AZURE.NOTE]No es posible cambiar el número de particiones o el tipo de esquema de particiones usado para un servicio una vez creado.

## Pasos siguientes

Para obtener información sobre los conceptos de Service Fabric, vea lo siguiente:

- [Disponibilidad de los servicios de Service Fabric](service-fabric-availability-services.md)

- [Escalabilidad de los servicios de Service Fabric](service-fabric-concepts-scalability.md)
 

<!---HONumber=Oct15_HO3-->