---
title: "Introducción al servicio de base de datos relacional de Azure Database for MySQL | Microsoft Docs"
description: "Información general del servicio de base de datos relacional de Azure Database for MySQL."
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 09/15/2017
ms.custom: mvc
ms.openlocfilehash: 3eff97a9e7568566cd01a4eb3c7ee66806eac4cc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="what-is-azure-database-for-mysql-service-introduction"></a>¿Qué es Azure Database for MySQL? Introducción al servicio
Azure Database for MySQL es un servicio de base de datos relacional de Microsoft Cloud basado en el motor de base de datos de [MySQL Community Edition](https://www.mysql.com/products/community/).  Azure Database for MySQL ofrece lo siguiente:

- Rendimiento predecible en varios niveles de servicio
- Escalabilidad dinámica sin tiempo de inactividad de la aplicación
- Alta disponibilidad integrada
- Protección de datos

Estas funcionalidades no requieren casi ninguna tarea de administración y todas se proporcionan sin ningún costo adicional. Le permiten centrarse en el desarrollo rápido de aplicaciones y en reducir el plazo de acceso al mercado, en lugar de tener que dedicar tiempo y recursos a la administración tanto de máquinas virtuales como de infraestructura. Además, puede seguir desarrollando su aplicación con las herramientas y la plataforma de código abierto de su elección, y entregar con la velocidad y eficacia que exige su negocio sin tener que aprender nuevas habilidades.

![Diagrama conceptual de Azure Database for MySQL](media/overview/1-azure-db-for-mysql-conceptual-diagram.png)

En este artículo se presentan los principales conceptos y características de Azure Database for MySQL relacionados con el rendimiento, la escalabilidad y la manejabilidad, con vínculos para explorar los detalles. Consulte estas guías de inicio rápido para comenzar:
- [Create an Azure Database for MySQL server using Azure Portal](quickstart-create-mysql-server-database-using-azure-portal.md) (Creación de un servidor de Azure Database for MySQL mediante Azure Portal)
- [Create an Azure Database for MySQL server using Azure CLI](quickstart-create-mysql-server-database-using-azure-cli.md) (Creación de un servidor de Azure Database for MySQL mediante la CLI de Azure)

Para ver ejemplos de la CLI de Azure, consulte:
- [Ejemplos de la CLI de Azure para Azure Database for MySQL](sample-scripts-azure-cli.md)

## <a name="adjust-performance-and-scale-without-downtime"></a>Ajuste del rendimiento y escalabilidad sin tiempo de inactividad
El servicio Azure Database for MySQL ofrece actualmente dos niveles de servicio: Básico y Estándar. Cada nivel ofrece diferentes niveles de rendimiento y funcionalidades para admitir cargas de trabajo de bases de datos, de ligeras a pesadas. Puede compilar su primera aplicación en una base de datos pequeña por poco dinero al mes, y después cambiar de nivel de servicio para escalar en función de las necesidades de la solución sin tiempo de inactividad. La escalabilidad dinámica permite a la base de datos responder de manera transparente a los cambiantes requisitos de recursos. Solo paga por los recursos que necesite y cuando los necesite.

## <a name="monitoring-and-alerting"></a>Supervisión y alertas
¿Cómo se sabe el momento correcto en el que debe hacer clic para detenerse al subir y bajar por el dial? Utilice las características de supervisión y alertas de rendimiento integradas, combinadas con la clasificación de rendimiento basado en las unidades de proceso. Con estas características puede evaluar rápidamente el impacto de escalar y reducir verticalmente en función de sus necesidades de rendimiento previstas o actuales. Consulte [Conceptos: niveles de servicio](concepts-service-tiers.md) para más información.

## <a name="keep-your-app-and-business-running"></a>Mantenimiento de la aplicación y el negocio en funcionamiento
El Acuerdo de Nivel de Servicio (SLA) de Azure, con una disponibilidad del 99,99 % líder del sector y con la tecnología de una red global de centros de datos administrados por Microsoft, ayuda a mantener las aplicaciones en funcionamiento de forma ininterrumpida. Con cada servidor de Azure Database for MySQL, se saca provecho de las ventajas de la seguridad integrada, la tolerancia a errores y la protección de datos, algo que de lo contrario tendría que adquirir o diseñar, compilar y administrar. Con Azure Database for MySQL puede usar la restauración a un momento dado para recuperar un servidor a un estado anterior, con un plazo máximo de 35 días.

## <a name="secure-your-data"></a>Protección de los datos
La tradicional seguridad de los datos de los servicios de bases de datos de Azure se mantiene en Azure Database for MySQL con características que limitan el acceso, protegen los datos en reposo y en movimiento, y le ayudan a supervisar la actividad. Para más información sobre la seguridad de plataforma de Azure, vaya al [Centro de confianza de Azure](https://www.microsoft.com/en-us/TrustCenter/Security/default.aspx).

El servicio Azure Database for MySQL usa el cifrado de almacenamiento de los datos en reposo. Los datos, incluidas las copias de seguridad, se cifran en el disco (a excepción de los archivos temporales creados por el motor durante la ejecución de consultas). El servicio usa el cifrado AES de 256 bits que se incluye en el cifrado de almacenamiento de Azure y las claves están administradas por el sistema. El cifrado de almacenamiento siempre está activado y no se puede deshabilitar.

De forma predeterminada, el servicio Azure Database for MySQL está configurado para requerir la [seguridad de la conexión SSL](./concepts-ssl-connection-security.md) para los datos en movimiento en la red. La exigencia de conexiones SSL entre el servidor de base de datos y las aplicaciones cliente ayuda a proteger contra los ataques de tipo "man in the middle" dado que los datos se cifran entre el servidor y la aplicación.  Tiene la opción de deshabilitar el requisito de SSL para conectar el servicio de base de datos en caso de que la aplicación cliente no admita la conectividad SSL.

## <a name="next-steps"></a>Pasos siguientes
Ahora que leyó una introducción a Azure Database for MySQL y contestó a la pregunta "¿Qué es Azure Database for MySQL?", ya está listo para:
- Consulte la página de precios para ver comparaciones de costos y calculadoras. [Precios](https://azure.microsoft.com/pricing/details/mysql/)
- Comience por crear su primer servidor. [Create an Azure Database for MySQL server using Azure Portal](quickstart-create-mysql-server-database-using-azure-portal.md) (Creación de un servidor de Azure Database for MySQL mediante Azure Portal)
- Compilación de la primera aplicación de Python, PHP, Ruby, C\#, Java, Node.js: [Bibliotecas de conectividad que se usan para conectarse a Azure Database for MySQL](concepts-connection-libraries.md)
