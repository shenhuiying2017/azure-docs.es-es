---
title: Planes de tarifa para Azure Database for MySQL
description: En este artículo se describen los planes de tarifa para Azure Database for MySQL.
services: mysql
author: jan-eng
ms.author: janeng
manager: kfile
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 03/20/2018
ms.openlocfilehash: 9d4e42df3137108248a043bb0d9def181d766c7a
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/03/2018
---
# <a name="azure-database-for-mysql-pricing-tiers"></a>Planes de tarifa de Azure Database for MySQL

Puede crear un servidor de Azure Database for MySQL en tres planes de tarifa diferentes: Básico, De uso general y Optimizado para memoria. Los planes de tarifa se diferencian por la cantidad de proceso en núcleos virtuales que se puede aprovisionar, la cantidad de memoria por núcleo virtual y la tecnología de almacenamiento usada para almacenar los datos. Todos los recursos se aprovisionan en el nivel de servidor MySQL. Un servidor puede tener una o varias bases de datos.

|    | **Básico** | **Uso general** | **Memoria optimizada** |
|:---|:----------|:--------------------|:---------------------|
| Generación de procesos | Gen 4, Gen 5 | Gen 4, Gen 5 | Gen 5 |
| Núcleos virtuales | 1, 2 | 2, 4, 8, 16, 32 |2, 4, 8, 16 |
| Memoria por núcleo virtual | Línea base | 2x Básico | 2x Uso general |
| Tamaño de almacenamiento | De 5 GB a 1 TB | De 5 GB a 1 TB | De 5 GB a 1 TB |
| Tipo de almacenamiento | Azure Standard Storage | Azure Premium Storage | Azure Premium Storage |
| Período de retención de copias de seguridad de base de datos | De 7 a 35 días | De 7 a 35 días | De 7 a 35 días |

Para elegir un plan de tarifa, use la siguiente tabla como punto de partida.

| Nivel de precios | Carga de trabajo objetivo |
|:-------------|:-----------------|
| Básica | Cargas de trabajo que requieren proceso y rendimiento de E/S ligeros. Algunos ejemplos son los servidores que se usan para desarrollo o prueba, o bien las aplicaciones a pequeña escala que se usan con poca frecuencia. |
| Uso general | La mayoría de las cargas de trabajo de empresa que requieren un equilibrio entre proceso y memoria con rendimiento de E/S escalable. Por ejemplo, servidores para hospedar aplicaciones web y móviles, y otras aplicaciones empresariales.|
| Memoria optimizada | Cargas de trabajo de base de datos de alto rendimiento que requieren rendimiento en memoria para un procesamiento de transacciones más rápido y una mayor simultaneidad. Por ejemplo, servidores para procesar datos en tiempo real y aplicaciones de análisis y transacciones de alto rendimiento.|

Después de crear un servidor, el número de núcleos virtuales se puede aumentar o reducir en cuestión de segundos. También puede ajustar de forma independiente la cantidad de almacenamiento y aumentar o reducir el período de retención sin que las aplicaciones experimenten tiempo de inactividad. Para más información, consulte la sección "Escalado de recursos".

## <a name="compute-generations-vcores-and-memory"></a>Generaciones de procesos, núcleos virtuales y memoria

Los recursos de proceso se proporcionan como núcleos virtuales, que representan la CPU lógica del hardware subyacente. Actualmente, puede elegir entre dos generaciones de proceso, Gen 4 y Gen 5. Las CPU lógicas Gen 4 se basan en los procesadores Intel E5-2673 v3 (Haswell) de 2.4 GHz. Las CPU lógicas Gen 5 se basan en los procesadores Intel E5-2673 v4 (Broadwell) de 2.3 GHz. Las CPU lógicas Gen 4 y Gen 5 están disponibles en las siguientes regiones ("X" indica disponible). 

| **Región de Azure** | **Gen 4** | **Gen 5** |
|:---|:----------:|:--------------------:|
| Central EE. UU: |  | X |
| Este de EE. UU | X | X |
| Este de EE. UU. 2 | X |  |
| Centro-Norte de EE. UU | X |  |
| Centro-Sur de EE. UU | X |  |
| Oeste de EE. UU | X | X |
| Oeste de EE. UU. 2 |  | X |
| Centro de Canadá | X | X |
| Este de Canadá | X | X |
| Sur de Brasil | X |  |
| Europa del Norte | X | X |
| Europa occidental | X | X |
| Oeste de Reino Unido |  | X |
| Sur del Reino Unido 2 |  | X |
| Asia oriental | X |  |
| Sudeste asiático | X |  |
| Australia Oriental |  | X |
| India Central | X |  |
| Oeste de la India | X |  |
| Este de Japón | X |  |
| Oeste de Japón | X |  |
| Corea del Sur |  | X |

Según el plan de tarifa, cada núcleo virtual se aprovisiona con una cantidad específica de memoria. Al aumentar o reducir el número de núcleos virtuales para el servidor, la memoria aumenta o disminuye proporcionalmente. El plan Uso general proporciona el doble de memoria por núcleo virtual en comparación con el plan Básico. El plan Memoria optimizada proporciona el doble de memoria en comparación con el plan Uso general.

## <a name="storage"></a>Storage

El almacenamiento que se aprovisiona es la cantidad de capacidad de almacenamiento disponible para el servidor de Azure Database for MySQL. El almacenamiento se usa para los archivos de base de datos, los archivos temporales, los registros de transacciones y los registros del servidor MySQL. La cantidad total de almacenamiento que se aprovisiona también define la capacidad de E/S disponible para su servidor.

|    | **Básico** | **Uso general** | **Memoria optimizada** |
|:---|:----------|:--------------------|:---------------------|
| Tipo de almacenamiento | Azure Standard Storage | Azure Premium Storage | Azure Premium Storage |
| Tamaño de almacenamiento | De 5 GB a 1 TB | De 5 GB a 1 TB | De 5 GB a 1 TB |
| Tamaño de incremento de almacenamiento | 1 GB | 1 GB | 1 GB |
| E/S | Variable |3 IOPS/GB<br/>100 IOPS mín. | 3 IOPS/GB<br/>100 IOPS mín. |

Puede agregar capacidad de almacenamiento adicional durante y después de la creación del servidor. El plan Básico no proporciona una garantía de IOPS. En los planes de tarifa Uso general y Memoria optimizada, el valor de IOPS se escala con el tamaño de almacenamiento aprovisionado en una proporción 3:1.

Puede supervisar el consumo de E/S en Azure Portal o mediante los comandos de la CLI de Azure. Las métricas pertinentes que se deben supervisar son el [límite de almacenamiento, el porcentaje de almacenamiento, el almacenamiento usado y el porcentaje de E/S](concepts-monitoring.md).

## <a name="backup"></a>Backup

El servicio realiza automáticamente copias de seguridad del servidor. El período mínimo de retención de las copias de seguridad es siete días. Puede establecer un período de retención de hasta 35 días. La retención se puede ajustar en cualquier momento de la vigencia del servidor. Puede elegir entre copia de seguridad con redundancia geográfica y con redundancia local. Las copias de seguridad con redundancia geográfica también se almacenan en la [región emparejada geográficamente](https://docs.microsoft.com/azure/best-practices-availability-paired-regions) de la región en la que se crea el servidor. Esta redundancia proporciona un nivel de protección en caso de desastre. También ofrece la posibilidad de restaurar el servidor en cualquier otra región de Azure en la que el servicio está disponible con las copias de seguridad con redundancia geográfica. Una vez que se crea el servidor, no es posible cambiar entre las dos opciones de almacenamiento de copia de seguridad.

## <a name="scale-resources"></a>Escalado de recursos

Después de crear el servidor, puede cambiar los núcleos virtuales, la cantidad de almacenamiento y el período de retención de copia de seguridad de manera independiente. No puede cambiar el plan de tarifa o el tipo de almacenamiento de copia de seguridad. Los núcleos virtuales y el período de retención de copia de seguridad se pueden escalar o reducir verticalmente. El tamaño de almacenamiento solo se puede aumentar. El escalado de los recursos puede realizarse a través del portal o la CLI de Azure. Para ver un ejemplo de escalado con la CLI de Azure, consulte [Supervisión y escalado de un servidor de Azure Database for MySQL mediante la CLI de Azure](scripts/sample-scale-server.md).

Al cambiar el número de núcleos virtuales, se crea una copia del servidor original con la nueva asignación de proceso. Una vez que el nuevo servidor está en funcionamiento, las conexiones se transfieren al nuevo servidor. Durante el breve espacio de tiempo en que el sistema cambia al nuevo servidor, no se puede establecer ninguna nueva conexión y todas las transacciones no confirmadas se revierten. Esta ventana varía, pero en la mayoría de los casos es inferior a un minuto.

El escalado del almacenamiento y el cambio del período de retención de copia de seguridad son operaciones verdaderamente en línea. No hay ningún tiempo de inactividad y la aplicación no se ve afectada. A medida que el valor de IOPS se escala con el tamaño del almacenamiento aprovisionado, puede aumentar el número de IOPS disponibles para el servidor mediante el escalado vertical del almacenamiento.

## <a name="pricing"></a>Precios

Para conocer la información más actualizada sobre precios, consulte la [página de precios](https://azure.microsoft.com/pricing/details/mysql/) del servicio. Para ver el costo de la configuración deseada, en [Azure Portal](https://portal.azure.com/#create/Microsoft.MySQLServer) se muestra el costo mensual en la pestaña **Plan de tarifa** según las opciones que seleccione. Si no tiene una suscripción de Azure, puede usar la calculadora de precios de Azure para obtener un precio estimado. En el sitio web [Calculadora de precios de Azure](https://azure.microsoft.com/pricing/calculator/), seleccione **Agregar elementos**, expanda la categoría **Bases de datos** y elija **Azure Database for MySQL** para personalizar las opciones.

## <a name="next-steps"></a>Pasos siguientes

- Aprenda cómo [crear un servidor MySQL en el portal](howto-create-manage-server-portal.md).
- Aprenda cómo [supervisar y escalar un servidor de Azure Database for MySQL mediante la CLI de Azure](scripts/sample-scale-server.md).
- Más información sobre las [limitaciones del servicio](concepts-limits.md).
