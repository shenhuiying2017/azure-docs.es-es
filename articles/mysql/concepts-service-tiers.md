---
title: Planes de tarifa de Azure Database for MySQL | Microsoft Docs
description: Planes de tarifa de Azure Database for MySQL
services: mysql
author: jasonwhowell
ms.author: jasonh
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 11/03/2017
ms.openlocfilehash: ae7e57e9b40f5194c15525a48843060bbccaa956
ms.sourcegitcommit: 38c9176c0c967dd641d3a87d1f9ae53636cf8260
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/06/2017
---
# <a name="azure-database-for-mysql-options-and-performance-understand-whats-available-in-each-pricing-tier"></a>Opciones y rendimiento de Azure Database for MySQL: información sobre el contenido disponible en cada plan de tarifa
Al crear un servidor de Azure Database for MySQL, tiene que elegir entre tres opciones principales para configurar los recursos asignados a dicho servidor. Estas opciones afectan al rendimiento y la escala del servidor.
- Nivel de precios
- Unidades de proceso
- Almacenamiento (GB)

Cada plan de tarifa permite elegir entre diferentes niveles de rendimiento (unidades de proceso), en función de los requisitos de su carga de trabajo. Unos niveles de rendimiento más altos ofrecen recursos adicionales para el servidor que se han diseñado para proporcionar un mayor rendimiento. Puede cambiar el nivel de rendimiento del servidor dentro de un mismo plan de tarifa sin prácticamente ningún tiempo de inactividad para las aplicaciones.

> [!IMPORTANT]
> Mientras el servicio se encuentre en versión preliminar pública, no se garantiza ningún Acuerdo de Nivel de Servicio (SLA).

En un servidor de Azure Database for MySQL, puede tener una o varias bases de datos. Puede optar por crear una sola base de datos por servidor para que use todos los recursos, o bien crear varias para que los compartan. 

## <a name="choose-a-pricing-tier"></a>Elija un plan de tarifa.
Mientras se encuentre en versión preliminar, el servicio Azure Database for MySQL ofrece dos planes de tarifa: Básico y Estándar. El plan Premium no está disponible todavía, pero lo estará pronto. 

En la tabla siguiente se proporcionan ejemplos de los planes de tarifa más adecuados para diferentes cargas de trabajo de aplicaciones.

| Nivel de precios | Carga de trabajo objetivo |
| :----------- | :----------------|
| Básica | Opción más conveniente para pequeñas cargas de trabajo que requieren almacenamiento y proceso escalables sin garantía de IOPS. Algunos ejemplos son los servidores utilizados para desarrollo o prueba, o las aplicaciones a pequeña escala que se emplean con poca frecuencia. |
| Estándar | La opción ideal para aplicaciones en la nube que necesiten de un valor de E/S por segundo garantizado con un alto rendimiento. Como ejemplos destacan las aplicaciones web y analíticas. |
| Premium | La opción idónea para cargas de trabajo que necesiten de una baja latencia para transacciones y E/S. Proporciona la mejor compatibilidad para muchos usuarios simultáneos. Se aplica a las bases de datos que admitan aplicaciones críticas para la actividad de la empresa.<br />El plan de tarifa Premium no está disponible en la versión preliminar. |

Para decidirse por un plan de tarifa, empiece por determinar si la carga de trabajo necesita un valor garantizado de E/S por segundo. Si es así, seleccione el plan de tarifa Estándar.

| **Características del plan de tarifa** | **Básico** | **Standard** |
| :------------------------ | :-------- | :----------- |
| Unidades de proceso máximas | 100 | 800 | 
| Almacenamiento total máximo | 1 TB | 1 TB | 
| Garantía de IOPS de almacenamiento | N/D | Sí | 
| IOPS de almacenamiento máximas | N/D | 3000 | 
| Período de retención de copias de seguridad de base de datos | 7 días | 35 días | 

Durante el período de versión preliminar, no es posible cambiar el plan de tarifa una vez que se haya creado el servidor. En el futuro, será posible actualizar el servidor de un plan de tarifa a otro o cambiarlo a un plan anterior.

## <a name="understand-the-price"></a>Descripción del precio
Cuando cree una instancia de Azure Database for MySQL en [Azure Portal](https://portal.azure.com/#create/Microsoft.MySQLServer), seleccione la página **Plan de tarifa** y el costo mensual se muestra en función de las opciones seleccionadas. Si no tiene una suscripción de Azure, use la calculadora de precios de Azure para obtener un precio estimado. Visite el sitio web [Calculadora de precios de Azure](https://azure.microsoft.com/pricing/calculator/), haga clic en **Agregar elementos**, expanda la categoría **Bases de datos** y elija **Azure Database for MySQL** para personalizar las opciones.

## <a name="choose-a-performance-level-compute-units"></a>Selección de un nivel de rendimiento (unidades de proceso)
Cuando haya determinado el plan de tarifa para su servidor de Azure Database for MySQL, ya podrá calcular el nivel de rendimiento. Para ello, seleccione el número de unidades de proceso necesarias. Un buen punto de partida son 200 o 400 unidades de proceso para aplicaciones que necesiten más simultaneidad de usuarios para cargas de trabajo web o analíticas. Esta cantidad se puede incrementar en función de las necesidades correspondientes. 

Las unidades de proceso son una medida del rendimiento de procesamiento de la CPU cuya disponibilidad está garantizada para un único servidor de Azure Database for MySQL. Una unidad de proceso es una medida combinada de recursos de CPU y memoria.  Para más información, consulte [Explicación de las unidades de proceso](concepts-compute-unit-and-storage.md).

### <a name="basic-pricing-tier-performance-levels"></a>Niveles de rendimiento del plan de tarifa Básico:

| **Nivel de rendimiento** | **50** | **100** |
| :-------------------- | :----- | :------ |
| Unidades de proceso máximas | 50 | 100 |
| Tamaño de almacenamiento incluido | 50 GB | 50 GB |
| Tamaño máximo de almacenamiento en servidor\* | 1 TB | 1 TB |

### <a name="standard-pricing-tier-performance-levels"></a>Niveles de rendimiento del plan de tarifa Estándar:

| **Nivel de rendimiento** | **100** | **200** | **400** | **800** |
| :-------------------- | :------ | :------ | :------ | :------ |
| Unidades de proceso máximas | 100 | 200 | 400 | 800 |
| IOPS aprovisionadas y tamaño de almacenamiento incluidos | 125 GB<br/> 375 IOPS | 125 GB<br/> 375 IOPS | 125 GB<br/> 375 IOPS | 125 GB<br/> 375 IOPS |
| Tamaño máximo de almacenamiento en servidor\* | 1 TB | 1 TB | 1 TB | 1 TB |
| IOPS máximas aprovisionadas en servidor | 3000 IOPS | 3000 IOPS | 3000 IOPS | 3000 IOPS |
| IOPS máximas aprovisionadas en servidor por GB | 3 IOPS fijas por GB | 3 IOPS fijas por GB | 3 IOPS fijas por GB | 3 IOPS fijas por GB |

\* El tamaño máximo de almacenamiento en servidor se refiere al tamaño máximo de almacenamiento aprovisionado para el servidor.

## <a name="storage"></a>Storage 
La configuración de almacenamiento define la cantidad de capacidad de almacenamiento disponible para un servidor de Azure Database for MySQL. El almacenamiento usado por el servicio incluye los archivos de base de datos, los registros de transacciones y los registros de servidor de MySQL. Tenga en cuenta el tamaño de almacenamiento necesario para hospedar las bases de datos y los requisitos de rendimiento (E/S por segundo) al seleccionar la configuración de almacenamiento.

Se incluye cierta capacidad de almacenamiento mínima con cada plan de tarifa, denominada en la tabla anterior "Tamaño de almacenamiento incluido". Es posible agregar capacidad de almacenamiento adicional al crear el servidor en incrementos de 125 GB y hasta el valor máximo permitido. La capacidad de almacenamiento adicional puede configurarse con independencia de la configuración de las unidades de proceso. Los precios varían en función de la cantidad de almacenamiento seleccionada.

La configuración de E/S por segundo de cada nivel de rendimiento está relacionada con el plan de tarifa y el tamaño de almacenamiento que se seleccionen. El plan Básico no garantiza ningún valor de E/S por segundo. En el plan de tarifa Estándar, las E/S por segundo escalan de forma proporcional al tamaño de almacenamiento máximo en una proporción fija de 3:1. El almacenamiento incluido de 125 GB garantiza 375 IOPS aprovisionadas, cada una de ellas con un tamaño de E/S de hasta 256 KB. Puede seleccionar almacenamiento adicional hasta un valor máximo de 1 TB para garantizar una provisión de 3000 E/S por segundo.

Supervise el grafo de métricas de Azure Portal o escriba comandos de CLI de Azure para medir el consumo de almacenamiento y E/S por segundo. Las métricas pertinentes que se deben supervisar son el límite de almacenamiento, el porcentaje de almacenamiento, el almacenamiento usado y el porcentaje de E/S por segundo.

>[!IMPORTANT]
> Mientras use la versión preliminar, seleccione la cantidad de almacenamiento en el momento de crear el servidor. Todavía no es posible cambiar el tamaño del almacenamiento en un servidor existente. 

## <a name="scaling-a-server-up-or-down"></a>Escalado o reducción verticales de un servidor
Primeramente, se eligen el plan de tarifa y el nivel de rendimiento al crear la instancia de Azure Database for MySQL. Más adelante, puede escalar las unidades de proceso o reducirlas verticalmente de forma dinámica, dentro del rango del mismo plan de tarifa. En Azure Portal, deslice las unidades de proceso por la página de plan de tarifa del servidor, o bien cree un script conforme al siguiente ejemplo: [Supervisión y escalado de un servidor de Azure Database for MySQL mediante la CLI de Azure](scripts/sample-scale-server.md).

El ajuste de la cantidad de unidades de proceso se realiza con independencia del tamaño de almacenamiento máximo que haya seleccionado.

En segundo plano, al cambiar el nivel de rendimiento de un servidor, se crea una copia del servidor original en el nuevo nivel de rendimiento y, después, se cambian las conexiones al servidor copiado. Durante este proceso no se pierde ningún dato. Sin embargo, durante el breve momento en que el sistema cambia a la nueva copia del servidor, las conexiones a la base de datos estarán deshabilitadas. Por ello, es posible que se reviertan algunas transacciones en curso. Este intervalo varía, pero de media dura menos de 4 segundos, y en más del 99 % de los casos es inferior a 30 segundos. Si el número de transacciones en curso es elevado mientras las conexiones están deshabilitadas, este intervalo puede ser superior.

La duración de todo el proceso de escalado depende tanto del tamaño como del plan de tarifa del servidor antes y después del cambio. Por ejemplo, el cambio de unidades de proceso de un servidor dentro del plan de tarifa Estándar debería tardar unos pocos minutos en completarse. Las nuevas propiedades del servidor no se aplican hasta que se completan los cambios.

## <a name="next-steps"></a>Pasos siguientes
- Para obtener más información sobre unidades de proceso, consulte [Explicación de las unidades de proceso](concepts-compute-unit-and-storage.md).
- Obtenga información sobre cómo realizar las tareas de [Supervisión y escalado de un servidor de Azure Database for MySQL (Base de datos de Azure para MySQL) mediante la CLI de Azure](scripts/sample-scale-server.md).
