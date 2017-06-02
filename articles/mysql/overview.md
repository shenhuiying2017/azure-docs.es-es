---
title: "Introducción al servicio de base de datos relacional de Azure Database for MySQL | Microsoft Docs"
description: "Información general del servicio de base de datos relacional de Azure Database for MySQL."
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonh
ms.assetid: 
ms.service: mysql-database
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: portal
ms.date: 05/10/2017
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: c8d75b0ce8bba4d0c065f86c199538b4a51f4bee
ms.contentlocale: es-es
ms.lasthandoff: 05/10/2017

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
El servicio Azure Database for MySQL actualmente ofrece dos niveles de servicio: Básico y Estándar. Cada nivel ofrece diferentes niveles de rendimiento y funcionalidades para admitir cargas de trabajo de bases de datos, de ligeras a pesadas. Puede compilar su primera aplicación en una base de datos pequeña por poco dinero al mes, y después cambiar de nivel de servicio para escalar en función de las necesidades de la solución sin tiempo de inactividad. La escalabilidad dinámica permite a la base de datos responder de manera transparente a los cambiantes requisitos de recursos. Solo paga por los recursos que necesite y cuando los necesite.

## <a name="monitoring-and-alerting"></a>Supervisión y alertas
¿Cómo se sabe el momento correcto en el que debe hacer clic para detenerse al subir y bajar por el dial? Utilice las características de supervisión y alertas de rendimiento integradas, combinadas con la clasificación de rendimiento basado en las unidades de proceso. Con estas características puede evaluar rápidamente el impacto de escalar y reducir verticalmente en función de sus necesidades de rendimiento del proyecto o actuales. Consulte [Conceptos: niveles de servicio](concepts-service-tiers.md) para más información.

## <a name="keep-your-app-and-business-running"></a>Mantenimiento de la aplicación y el negocio en funcionamiento
El Acuerdo de Nivel de Servicio (SLA) de Azure, con una disponibilidad del 99,99 % líder del sector y con la tecnología de una red global de centros de datos administrados por Microsoft, ayuda a mantener las aplicaciones en funcionamiento de forma ininterrumpida. Con cada servidor de Azure Database for MySQL, se saca provecho de las ventajas de la seguridad integrada, la tolerancia a errores y la protección de datos, algo que de lo contrario tendría que adquirir o diseñar, compilar y administrar. Con Azure Database for MySQL puede usar la restauración a un momento dado para recuperar un servidor a un estado anterior, con un plazo máximo de 35 días.

## <a name="secure-your-data"></a>Protección de los datos
Los servicios de bases de datos de Azure tienen una tradición de seguridad de datos que conserva Azure Database for MySQL con características que limitan el acceso, protegen los datos en reposo y en movimiento, y le ayudan a supervisar la actividad. Para más información sobre la seguridad de plataforma de Azure, vaya al [Centro de confianza de Azure](https://www.microsoft.com/en-us/TrustCenter/Security/default.aspx).

## <a name="next-steps"></a>Pasos siguientes
Ahora que leyó una introducción a Azure Database for MySQL y contestó a la pregunta "¿Qué es Azure Database for MySQL?", ya está listo para:
- Consulte la página de precios para ver comparaciones de costos y calculadoras. [Precios](https://azure.microsoft.com/pricing/details/mysql/)
- Comience por crear su primer servidor. [Create an Azure Database for MySQL server using Azure Portal](quickstart-create-mysql-server-database-using-azure-portal.md) (Creación de un servidor de Azure Database for MySQL mediante Azure Portal)
- Compilación de la primera aplicación de Python, PHP, Ruby, C\#, Java, Node.js: [Bibliotecas de conectividad que se usan para conectarse a Azure Database for MySQL](concepts-connection-libraries.md)

