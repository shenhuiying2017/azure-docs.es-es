---
title: Niveles de servicio de Azure Database for PostgreSQL | Microsoft Docs
description: Niveles de servicio de Azure Database for PostgreSQL
services: postgresql
author: kamathsun
ms.author: sukamat
manager: jhubbard
editor: jasonh
ms.assetid: 
ms.service: postgresql-database
ms.tgt_pltfrm: portal
ms.topic: article
ms.date: 05/16/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 44eac1ae8676912bc0eb461e7e38569432ad3393
ms.openlocfilehash: a946c114824597cc55e435a455cd888816789dbf
ms.contentlocale: es-es
ms.lasthandoff: 05/17/2017

---
# <a name="azure-database-for-postgresql-options-and-performance-understand-whats-available-in-each-service-tier"></a>Opciones y rendimiento de Azure Database for PostgreSQL: información sobre lo que está disponible en cada nivel de servicio

Mientras se encuentra en versión preliminar, Azure Database for PostgreSQL solo ofrece los niveles de servicio Básico y Estándar. Premium no está disponible todavía.

Cada nivel de servicio tiene varios niveles de rendimiento para controlar los diferentes tipos de requisitos de las cargas de trabajo. Unos niveles de rendimiento más altos ofrecen recursos adicionales diseñados para proporcionar un mayor rendimiento. Puede cambiar los niveles de rendimiento dentro de un nivel de servicio de forma dinámica sin que la aplicación experimente tiempos de inactividad.

En el futuro, será posible actualizar o degradar de un nivel de servicio a otro.

> [!IMPORTANT]
> El servicio se encuentra disponible actualmente como versión preliminar pública, por lo que aún no se ofrece ningún Acuerdo de Nivel de Servicio (SLA).

Puede crear servidores PostgreSQL únicos con recursos dedicados en un nivel de servicio con un nivel de rendimiento específico. También puede crear de una a varias bases de datos en un servidor en el que los recursos se comparten entre varias bases de datos. Los recursos disponibles para un único servidor PostgreSQL se expresan en términos de unidades de proceso y de unidades de almacenamiento. Para más información sobre las unidades de proceso y el almacenamiento, vea [Explicación de las unidades de proceso y las unidades de almacenamiento](concepts-compute-unit-and-storage.md).

## <a name="choosing-a-service-tier"></a>Selección de un nivel de servicio

En la tabla siguiente se proporcionan ejemplos de los niveles más adecuados para las diferentes cargas de trabajo de las aplicaciones.

| Nivel de servicio | Carga de trabajo objetivo |
| :----------- | :----------------|
| Básica | Opción más conveniente para pequeñas cargas de trabajo que requieren almacenamiento y proceso escalables sin garantía de IOPS. Algunos ejemplos son los servidores utilizados para desarrollo o prueba, o las aplicaciones a pequeña escala que se emplean con poca frecuencia. |
| Estándar | La opción deseada para aplicaciones de nube que necesitan garantía de IOPS con la capacidad de escalar a unidades de proceso y almacenamiento más grandes, con independencia de que el rendimiento sea alto. Como ejemplos destacan las aplicaciones web y analíticas. |
| Premium | Opción más conveniente para cargas de trabajo que precisan de latencias muy breves para transacciones y E/S, además de un rendimiento alto de E/S y cargas de trabajo. Proporciona la mejor compatibilidad para muchos usuarios simultáneos. Se aplica a las bases de datos que admiten aplicaciones críticas.<br />El nivel de servicio Premium no está disponible en la versión preliminar. |
| &nbsp; | &nbsp; |

Para decidir un nivel de servicio, empiece por determinar si la carga de trabajo necesita garantías de E/S por segundo. Después, determine las características mínimas que necesita:

| **Características del nivel de servicio** | **Básico** | **Standard** | **Premium** * |
| :------------------------ | :-------- | :----------- | :------------ |
| Unidades de proceso máximas | 100 | 2.000 | No disponible en versión preliminar |
| Almacenamiento total máximo | 1050 GB | 10 000 GB | No disponible en versión preliminar |
| Garantía de IOPS de almacenamiento | N/D | Sí | No disponible en versión preliminar |
| IOPS de almacenamiento máximas | N/D | 3000 | No disponible en versión preliminar |
| Período de retención de copias de seguridad de base de datos | 7 días | 35 días | 35 días |


> [!NOTE]
> El nivel de servicio Estándar en la versión preliminar actualmente admite hasta 800 unidades de proceso y un máximo de 1000 GB de almacenamiento.

Cuando haya determinado el nivel de servicio mínimo, ya puede determinar el nivel de rendimiento del servidor PostgreSQL, es decir, las unidades de proceso. Las 200 y 400 unidades de proceso estándar suelen ser un buen punto de partida para las aplicaciones que necesitan más simultaneidad de usuarios para las cargas de trabajo web o analíticas. 

Sin embargo, puede escalar o reducir verticalmente las unidades de proceso con independencia de las unidades de almacenamiento, en función de los requisitos de la carga de trabajo. Si la carga de trabajo necesita un ajuste de recursos de proceso, puede aumentar o reducir las unidades de proceso de forma dinámica. Si la carga de trabajo precisa de más E/S por segundo o almacenamiento, entonces puede escalar el almacenamiento.

> [!NOTE]
> En la versión preliminar, los niveles Estándar y Básico no admiten actualmente el escalado dinámico de almacenamiento. Se prevé agregar la característica en el futuro.

> [!NOTE]
> En el nivel de servicio Estándar, las E/S por segundo se escalan en proporción con el tamaño de almacenamiento aprovisionado, con una relación fija de 3:1. El almacenamiento incluido de 125 GB garantiza 375 IOPS aprovisionadas, cada una de ellas con un tamaño de IOPS de hasta 256 KB. Si aprovisiona 1000 GB, obtendrá 3000 IOPS aprovisionadas. Debe supervisar el consumo de unidades de proceso de servidor y escalar verticalmente para poder utilizar todas las IOPS aprovisionadas disponibles.

## <a name="service-tiers-and-performance-levels"></a>Niveles de servicio y niveles de rendimiento

Azure Database for PostgreSQL ofrece varios niveles de rendimiento en cada nivel de servicio. Tiene la flexibilidad de elegir el nivel más adecuado a las exigencias de su carga de trabajo, mediante alguna de las siguientes opciones:

- [Azure Portal](quickstart-create-server-database-portal.md), ubicado en [http://portal.azure.com](http://portal.azure.com)
- [CLI de Azure](quickstart-create-server-database-azure-cli.md)

Independientemente de la cantidad de bases de datos hospedadas en cada servidor PostgreSQL, la base de datos recibe un conjunto de recursos garantizado, y las características de rendimiento previstas del servidor no se verán afectadas.

### <a name="basic-service-tier"></a>Nivel de servicio Básico:

| **Nivel de rendimiento** | **50** | **100** |
| --------------------: | :----- | :------ |
| Unidades de proceso máximas | 50 | 100 |
| Tamaño de almacenamiento incluido | 50 GB | 50 GB |
| Tamaño máximo de almacenamiento en servidor\* | 1050 GB | 1050 GB |

### <a name="standard-service-tier"></a>Nivel de servicio Estándar:

| **Nivel de rendimiento** | **100** | **200** | **400** | **800** |
| --------------------: | :------ | :------ | :------ | :------ |
| Unidades de proceso máximas | 100 | 200 | 400 | 800 |
| IOPS aprovisionadas y tamaño de almacenamiento incluidos | 125 GB<br/> 375 IOPS | 125 GB<br/> 375 IOPS | 125 GB<br/> 375 IOPS | 125 GB<br/> 375 IOPS |
| Tamaño máximo de almacenamiento en servidor\* | 1 TB | 1 TB | 1 TB | 1 TB |
| IOPS máximas aprovisionadas en servidor | 3000 IOPS | 3000 IOPS | 3000 IOPS | 3000 IOPS |
| IOPS máximas aprovisionadas en servidor por GB | 3 IOPS fijas por GB | 3 IOPS fijas por GB | 3 IOPS fijas por GB | 3 IOPS fijas por GB |

\* El tamaño máximo de almacenamiento en servidor se refiere al tamaño máximo de almacenamiento aprovisionado para el servidor.

## <a name="scaling-up-or-down-a-server"></a>Escalado o reducción vertical de un servidor

Después de elegir inicialmente un nivel de servicio y un nivel de rendimiento, puede escalar o reducir verticalmente de forma dinámica, en función de los requisitos de la carga de trabajo. Si necesita escalar o reducir verticalmente, puede cambiar fácilmente el nivel de la base de datos con la utilización de Azure Portal o de la CLI de Azure.

Al cambiar el nivel de servicio o de rendimiento de una base de datos, se crea una réplica de la base de datos original en el nuevo nivel de rendimiento y, a continuación, se cambian las conexiones a la réplica. Durante este proceso, no se pierde ningún dato; sin embargo, durante el breve momento en que se cambie a la réplica, las conexiones a la base de datos estarán deshabilitadas, por tanto, es posible que se reviertan algunas transacciones en curso. Este intervalo varía, pero de media dura menos de 4 segundos, y en más del 99 % de los casos es inferior a 30 segundos. Si el número de transacciones en curso es elevado mientras las conexiones están deshabilitadas, este intervalo puede ser superior.

La duración de todo el proceso de escalado vertical depende del nivel de servicio y del tamaño del servidor antes y después del cambio. Por ejemplo, el cambio de unidades de proceso de un servidor de o a un nivel de servicio Estándar o dentro de dicho nivel debe tardar unos minutos en completarse. Las nuevas propiedades del servidor no se aplican hasta que se completan los cambios.

Puede utilizar Azure Portal para escalar o reducir verticalmente, o bien usar la CLI de Azure para supervisar y escalar el servidor. Consulte [Supervisión y escalado de un solo servidor PostgreSQL mediante la CLI de Azure](scripts/sample-scale-server-up-or-down.md).

### <a name="details-about-scaling-up-or-down"></a>Información sobre el escalado o la reducción vertical

- Para degradar un servidor, las unidades de almacenamiento del servidor deben ser más pequeñas que el tamaño máximo permitido del nivel de servicio de destino.
- Las ofertas del servicio de restauración son diferentes para los distintos niveles de servicio. Si cambia a un nivel inferior, puede perder la capacidad de restaurar a un momento dado o tener un período de retención de copias de seguridad más breve. Para más información, vea [Copia de seguridad y restauración de un servidor de Azure Database for PostgreSQL en Azure Portal](howto-restore-server-portal.md).
- Las nuevas propiedades del servidor no se aplican hasta que se completan los cambios.

