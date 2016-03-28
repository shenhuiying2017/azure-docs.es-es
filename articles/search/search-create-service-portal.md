<properties
	pageTitle="Creación de un servicio Búsqueda de Azure en el Portal de Azure | Microsoft Azure | Servicio de búsqueda hospedado en la nube"
	description="Más información sobre cómo aprovisionar un servicio Búsqueda de Azure mediante el Portal de Azure."
	services="search"
	authors="ashmaka"
	documentationCenter=""/>

<tags
	ms.service="search"
	ms.devlang="NA"
	ms.workload="search"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.date="03/10/2016"
	ms.author="ashmaka"/>

# Creación de un servicio Búsqueda de Azure mediante el Portal de Azure

Esta guía le llevará por el proceso de crear (o aprovisionar) un servicio Búsqueda de Azure con el [Portal de Azure](https://portal.azure.com/).

En esta guía se asume que ya tiene una suscripción de Azure y puede iniciar sesión en el Portal de Azure.

## I. Encontrar Búsqueda de Azure en el Portal de Azure
1. Abra el [Portal de Azure](https://portal.azure.com/) e inicie sesión.
1. Haga clic en el signo más ("+") en la esquina superior izquierda.
2. Seleccione "Datos y almacenamiento".
3. Seleccione "Búsqueda de Azure".

![](./media/search-create-service-portal/find-search.png)

## II. Elegir un nombre de servicio y el punto de conexión de la dirección URL del servicio
1. El nombre del servicio será parte de la dirección URL del punto de conexión del servicio Búsqueda de Azure contra el que hará las llamadas de API para administrar y usar el servicio de búsqueda.
2. Escriba el nombre del servicio en el campo 'Dirección URL'. El nombre del servicio debe cumplir los siguientes requisitos:
  * Solo debe contener letras minúsculas, números o guiones ("-").
  * No puede utilizar guiones ("-") en los dos primeros caracteres o en el último carácter.
  * No puede contener guiones consecutivos ("--").
  * Está limitada a una longitud de entre 2 y 60 caracteres.


## III. Seleccionar una suscripción donde mantendrá el servicio
Si tiene más de una suscripción, puede seleccionar cuál incluirá este servicio Búsqueda de Azure.

## IV. Seleccionar un grupo de recursos para el servicio
Cree un nuevo grupo de recursos o seleccione uno existente. Un grupo de recursos es una colección de servicios y recursos de Azure que se usan juntos. Por ejemplo, si utiliza Búsqueda de Azure para indexar una base de datos SQL, ambos servicios deberían formar parte del mismo grupo de recursos.

## V. Seleccionar la ubicación donde se hospedará el servicio
En cuanto servicio de Azure, Búsqueda de Azure está disponible para hospedarse en centros de datos de todo el mundo. Tenga en cuenta que los [precios pueden variar](https://azure.microsoft.com/pricing/details/search/) según la región geográfica.

## VI. Seleccionar el plan de tarifa
[Búsqueda de Azure se ofrece actualmente en varios planes de tarifa](https://azure.microsoft.com/pricing/details/search/): Gratis, Básico y Estándar. Cada plan tiene su propia [capacidad y sus propios límites](search-limits-quotas-capacity.md).

En este caso, hemos elegido el nivel Estándar para nuestro servicio.

## VII. Seleccionar el botón "Crear" para aprovisionar el servicio

![](./media/search-create-service-portal/create-service.png)

## VIII. Escalar el servicio

Cuando el servicio haya terminado de aprovisionarse, puede escalarlo de acuerdo con sus necesidades. Si ha elegido el nivel Estándar para el servicio Búsqueda de Azure, puede escalar el servicio en dos dimensiones: réplicas y particiones. Si ha elegido el nivel Básico, solo puede agregar réplicas.

Las *__particiones__* permiten que el servicio almacene y busque en más documentos.

Las *__réplicas__* permiten que el servicio gestione una carga más elevada de consultas de búsqueda: [un servicio requiere 2 réplicas para lograr un SLA de solo lectura y 3 réplicas para lograr un SLA de lectura y escritura](https://azure.microsoft.com/support/legal/sla/search/v1_0/).

1. Vaya a la hoja de administración de su servicio Búsqueda de Azure en el Portal de Azure.
2. En la hoja "Configuración", seleccione "Escala".
3. Puede escalar el servicio mediante la adición de particiones o réplicas.
  * No se puede escalar el servicio por encima de 36 unidades de búsqueda. El número total de unidades de búsqueda es el producto de sus réplicas y particiones (réplicas * particiones = total de unidades de búsqueda).
  * Si ha elegido el nivel Básico, solo puede escalar a 3 réplicas. Los servicios básicos están enlazados a una sola partición.

![](./media/search-create-service-portal/scale-service.png)

## Pasos siguientes
Después de aprovisionar un servicio Búsqueda de Azure, estará listo para [definir un índice de Búsqueda de Azure](search-what-is-an-index.md) para que pueda cargar y buscar los datos.

<!---HONumber=AcomDC_0316_2016-->