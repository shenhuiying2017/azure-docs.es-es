---
title: "Explicación de las unidades de proceso y las unidades de almacenamiento de Azure DB for PostgreSQL | Microsoft Docs"
description: "Azure DB for PostgreSQL: se explican las unidades de proceso y las unidades de almacenamiento y lo que sucede cuando se alcanza el número máximo de tales unidades."
services: postgresql
author: 
ms.author: 
manager: jhubbard
editor: jasonh
ms.assetid: 
ms.service: postgresql - database
ms.tgt_pltfrm: portal
ms.topic: article
ms.date: 05/10/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 0cf1f0cf3d93dac49e75f558dcba8af002a492da
ms.contentlocale: es-es
ms.lasthandoff: 05/10/2017

---
# <a name="explaining-compute-unit-and-storage-unit"></a>Explicación de las unidades de proceso y las unidades de almacenamiento
En este artículo se explican las unidades de proceso y las unidades de almacenamiento y lo que sucede cuando se alcanza el número máximo de tales unidades.

## <a name="what-are-compute-units"></a>¿Qué son las unidades de proceso?
Las unidades de proceso son una medida de la capacidad de proceso de la CPU que se garantiza que está disponible para un único servidor de Azure Database for PostgreSQL. Una unidad de proceso es una medida combinada de recursos de CPU y memoria. En general, 50 unidades de proceso equivalen a medio núcleo, 100 unidades de proceso equivalen a un núcleo y 2000 unidades de proceso equivalen a veinte núcleos de capacidad de proceso garantizada disponible para su servidor. 

La cantidad de memoria por unidad de proceso está optimizada para los niveles de servicio Básico, Estándar y Premium. Duplicar las unidades de proceso aumentando el nivel de rendimiento equivale duplicar el conjunto de recursos disponibles para ese único servidor de Azure Database for PostgreSQL. 

Por ejemplo, un nivel Estándar de 2000 unidades de proceso proporciona 20 veces más rendimiento de CPU y memoria que un nivel Estándar configurado con 100 unidades de proceso. Sin embargo, mientras que 100 unidades de proceso de nivel Estándar proporcionan el mismo rendimiento de CPU en comparación con 100 unidades de proceso de nivel Básico, la cantidad de memoria preconfigurada en el nivel Estándar es el doble de la cantidad de memoria configurada para el nivel Básico y, por lo tanto, ofrece un mejor rendimiento de la carga de trabajo y una menor latencia en las transacciones.

>[!IMPORTANT]
>Para conseguir un rendimiento predecible de la carga de trabajo y una elevada simultaneidad de usuarios, se recomienda firmemente la elección del nivel de servicio Estándar.

En cualquier momento puede cambiar los [niveles de servicio](concepts-service-tiers.md) sin prácticamente tiempo de inactividad de las aplicaciones. Para muchas empresas y aplicaciones, el hecho de poder crear entre una y muchas bases de datos dentro de cada servidor único de Azure Database for PostgreSQL y aumentar o reducir el rendimiento a petición proporciona la flexibilidad necesaria para administrar los costos.

>[!IMPORTANT]
>Actualmente se admiten el escalado y la reducción vertical de los niveles de rendimiento dentro de un nivel de servicio. Por ejemplo, puede escalar verticalmente de 100 unidades de proceso Estándar a 400 unidades de proceso Estándar. Igualmente, puede reducir verticalmente de 400 unidades de proceso Estándar a 100 unidades de proceso Estándar. La característica para poder escalar o reducir verticalmente entre niveles de servicio, por ejemplo, entre el nivel Básico y Estándar, estará disponible en el futuro.

## <a name="what-are-storage-units"></a>¿Qué son las unidades de almacenamiento?
Las unidades de almacenamiento son una medida de capacidad de almacenamiento aprovisionada que se garantiza que está disponible para un único servidor de Azure Database for PostgreSQL. Cada nivel de servicio tiene una cantidad fija de almacenamiento aprovisionado que se incluye en el precio del nivel de servicio seleccionado.

Opcionalmente, las unidades de almacenamiento se pueden escalar con independencia de las unidades de proceso, en incrementos de 125 GB, hasta el almacenamiento máximo permitido, como se describe en la tabla siguiente.
| **Características del nivel de servicio** | **Básico** | **Standard** | **Premium\*** |
|---------------------------|-----------|--------------|---------------|
| Unidades de almacenamiento incluidas | 50 GB | 125 GB | 250 GB |
| Almacenamiento total máximo | 1050 GB | 10 000 GB | 4000 GB |
| Garantía de IOPS de almacenamiento | N/D | Sí | Sí |
| IOPS de almacenamiento máximas | N/D | 30 000 | 40.000 |
Los niveles de servicio Estándar y Premium también proporcionan garantía de IOPS aprovisionada. La cantidad de IOPS aprovisionada disponible depende del nivel de servicio y de la capacidad de almacenamiento configurada. Para los servidores implementados en el nivel Estándar, la IOPS se fija en 3 veces el almacenamiento aprovisionado. 

Por ejemplo, si tiene 125 GB de almacenamiento aprovisionado, hay 375 IOPS aprovisionadas disponibles para el servidor. El nivel Premium proporciona una latencia muy baja y un elevado almacenamiento de IOPS. Para servidores implementados en el nivel Premium , la IOPS de baja latencia aprovisionada se puede escalar entre cinco y diez veces el almacenamiento aprovisionado.
>[!IMPORTANT]
>Si la carga de trabajo forma un cuello de botella debido a las unidades de proceso configuradas, puede que no sea capaz de obtener la IOPS aprovisionada disponible. Se recomienda supervisar las unidades de proceso y considerar la posibilidad de escalarlas si no puede explotar completamente la IOPS aprovisionada disponible.

Escalar las unidades de almacenamiento aumentando el almacenamiento aprovisionado equivale a aumentar proporcionalmente la IOPS aprovisionada para los niveles Estándar y Premium.

>[!IMPORTANT]
>El nivel Básico no proporciona garantía de IOPS.

## <a name="how-can-i-determine-the-number-of-compute-units-needed-for-my-workload"></a>¿Cómo puedo determinar el número de unidades de proceso necesarias para la carga de trabajo?
Si lo que busca es migrar un entorno local existente o un servidor PostgreSQL que se ejecuta en una máquina virtual, puede determinar el número de unidades de proceso mediante la estimación de cuántos núcleos de capacidad de proceso necesita su carga de trabajo. 

Si el entorno local o la máquina virtual usan actualmente 4 núcleos (sin contar el hiperproceso de CPU), puede comenzar configurando 400 unidades de proceso para Azure Database for PostgreSQL. Las unidades de proceso se pueden escalar o reducir verticalmente en función de las necesidades de su carga de trabajo, y de forma dinámica, sin apenas tiempo de inactividad de las aplicaciones. También puede supervisar el consumo de unidades de proceso a través del portal o de la CLI para conocer el tamaño correcto de los recursos para el servidor PostgreSQL.

## <a name="how-can-i-determine-the-number-of-storage-units-needed-for-my-workload"></a>¿Cómo puedo determinar el número de unidades de almacenamiento necesarias para la carga de trabajo?
Para estimar la cantidad de unidades de almacenamiento necesarias, determine primero la cantidad de capacidad de almacenamiento que necesita. Los niveles Básico, Estándar y Premium incluyen almacenamiento integrado en la SKU. 

El segundo factor importante consiste en determinar la IOPS necesaria. El nivel Básico proporciona IOPS variable sin ninguna garantía. En el nivel Estándar el escalado sigue una relación fija de tres IOPS por GB de almacenamiento aprovisionado y se proporciona garantía de IOPS. También puede supervisar el consumo de IOPS aprovisionada mediante el portal o la CLI para determinar el uso.

>[!IMPORTANT]
>Una vez aprovisionadas las unidades de almacenamiento, no se pueden reducir verticalmente de forma dinámica.

## <a name="what-happens-when-i-hit-my-maximum-compute-units-andor-storage-units"></a>¿Qué sucede si llego al número máximo de unidades de proceso o unidades de almacenamiento?
Los niveles de rendimiento se calibran y rigen para proporcionar los recursos necesarios para ejecutar la carga de trabajo de la base de datos hasta los límites máximos permitidos para el nivel de rendimiento o de servicio seleccionado. 

Si la carga de trabajo alcanza los límites de IOPS aprovisionada/unidades de proceso, seguirá recibiendo los recursos en el nivel máximo permitido, pero es probable que perciba un aumento de las latencias en las consultas. Alcanzar uno de estos límites no provocará errores, sino una ralentización de la carga de trabajo, a menos que la ralentización sea tan severa que las consultas empiecen a agotar el tiempo de espera. 

Si alcanza los límites de conexiones máximas permitidas, verá errores explícitos. Consulte [Límites de recursos de Azure Database for PostgreSQL](https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits) para más información sobre los límites en recursos. <Need to write about the behavior if a user reaches the storage capacity limits>

## <a name="next-steps"></a>Pasos siguientes
- Consulte [Niveles de servicio de Azure Database for PostgreSQL](./concepts-service-tiers.md) para más información sobre las unidades de proceso y las unidades de almacenamiento disponibles para servidores únicos.


