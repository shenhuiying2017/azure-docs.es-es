---
title: "Explicación de las unidades de proceso en Azure Database for PostgreSQL | Microsoft Docs"
description: "Azure Database for PostgreSQL: en este artículo se explica el concepto de unidades de proceso y lo que sucede cuando se alcanza el número máximo de tales unidades en la carga de trabajo."
services: postgresql
author: kamathsun
ms.author: sukamat
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 05/23/2017
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: e480671e6550b305c49442becaed3c0b42ce5cb3
ms.contentlocale: es-es
ms.lasthandoff: 07/21/2017

---
# <a name="explaining-compute-units-in-azure-database-for-postgresql"></a>Explicación de las unidades de proceso en Azure Database for PostgreSQL
En este artículo se explica el concepto de unidades de proceso y lo que sucede cuando se alcanza el número máximo de tales unidades en la carga de trabajo.

## <a name="what-are-compute-units"></a>¿Qué son las unidades de proceso?
Las unidades de proceso son una medida de la capacidad de proceso de la CPU que se garantiza que está disponible para un único servidor de Azure Database for PostgreSQL. Una unidad de proceso es una medida combinada de recursos de CPU y memoria. Por lo general, 50 unidades de proceso equivalen a medio núcleo. 100 unidades de proceso equivalen a un núcleo. 2000 unidades de proceso equivalen a una disponibilidad de rendimiento de procesamiento de veinte núcleos garantizada para el servidor.

La cantidad de memoria por unidad de proceso está optimizada para los planes de tarifa Básico y Estándar. Duplicar las unidades de proceso aumentando el nivel de rendimiento equivale duplicar el conjunto de recursos disponibles para ese único servidor de Azure Database for PostgreSQL.

Por ejemplo, 800 unidades de proceso de un plan Estándar proporcionan 8 veces más rendimiento de CPU y memoria que una configuración Estándar con 100 unidades de proceso. Sin embargo, mientras que 100 unidades de proceso de un plan Estándar proporcionan el mismo rendimiento de CPU que 100 unidades de proceso de uno Básico, la cantidad de memoria preconfigurada en el plan de tarifa Estándar es el doble de la configurada para el Básico. Por tanto, el plan de tarifa Estándar ofrece un mejor rendimiento para cargas de trabajo y una menor latencia en las transacciones que el Básico con las mismas unidades de proceso seleccionadas.

## <a name="how-can-i-determine-the-number-of-compute-units-needed-for-my-workload"></a>¿Cómo puedo determinar el número de unidades de proceso necesarias para la carga de trabajo?
Si lo que quiere es migrar un servidor de PostgreSQL que se esté ejecutando en un entorno local o una máquina virtual, puede calcular de forma estimada cuántos núcleos de rendimiento de procesamiento necesita su carga de trabajo para determinar el número de unidades de proceso. 

Si el servidor del entorno local o de la máquina virtual está usando actualmente 4 núcleos (sin contar el hiperproceso de CPU), empiece por configurar 400 unidades de proceso para el servidor de Azure Database for PostgreSQL. Las unidades de proceso se pueden escalar o reducir verticalmente y de forma dinámica en función de las necesidades de la carga de trabajo sin apenas tiempo de inactividad de las aplicaciones. 

Supervise el grafo de métricas de Azure Portal o escriba comandos de CLI de Azure para medir las unidades de proceso. Las métricas pertinentes que se deben supervisar son el porcentaje de unidades de proceso y el límite de unidades de proceso.

>[!IMPORTANT]
> Si detecta que las E/S por segundo del almacenamiento no se usan a máxima capacidad, considere supervisar también la utilización de las unidades de proceso. Aumentar el número de unidades de proceso puede incrementar el rendimiento de E/S reduciendo los cuellos de botella de rendimiento que se producen debido a limitaciones en la CPU o la memoria.

## <a name="what-happens-when-i-hit-my-maximum-compute-units"></a>¿Qué ocurre cuando se alcanza el número máximo de unidades de proceso?
Los niveles de rendimiento se calibran y regulan para proporcionar recursos para ejecutar la carga de trabajo de la base de datos hasta los límites máximos del plan de tarifa y el nivel de rendimiento seleccionados. 

Si la carga de trabajo alcanza los límites máximos de unidades de proceso o de E/S por segundo provistas, puede continuar usando los recursos al nivel máximo permitido, pero es probable que perciba un aumento de las latencias de las consultas. Alcanzar estos límites no provocará errores, sino una ralentización de la carga de trabajo, a menos que esta ralentización sea tan grave que empiece a agotarse el tiempo de espera de las consultas. 

Si la carga de trabajo alcanza los límites máximos de número de conexiones, se producen errores explícitos. Para obtener más información sobre los límites de los recursos, consulte [Limitaciones en Azure Database for PostgreSQL](concepts-limits.md).

## <a name="next-steps"></a>Pasos siguientes
Para obtener más información sobre los planes de tarifa, consulte [Planes de tarifa de Azure Database for PostgreSQL](./concepts-service-tiers.md).

