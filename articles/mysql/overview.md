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
ms.date: 10/20/2017
ms.custom: mvc
ms.openlocfilehash: 2a9efdd9285dfa5fca450ede64e5f6ee54cbc72b
ms.sourcegitcommit: 4ed3fe11c138eeed19aef0315a4f470f447eac0c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/23/2017
---
# <a name="what-is-azure-database-for-mysql"></a>¿Qué es Azure Database for MySQL?
Azure Database for MySQL es un servicio de base de datos relacional de Microsoft Cloud basado en el motor de base de datos de [MySQL Community Edition](https://www.mysql.com/products/community/). Este servicio está en versión preliminar pública. Azure Database for MySQL ofrece lo siguiente:

- Alta disponibilidad integrada sin coste adicional.
- Rendimiento predecible, con precios de pago por uso inclusivos.
- Escalado sobre la marcha en segundos.
- Protección de información confidencial en reposo y en movimiento.
- Copias de seguridad automáticas y restauración a un momento dado durante un máximo de 35 días.
- Seguridad y cumplimiento de nivel empresarial.

Estas funcionalidades no requieren casi ninguna tarea de administración y todas se proporcionan sin ningún costo adicional. Le permiten centrarse en el desarrollo rápido de aplicaciones y en reducir el plazo de acceso al mercado, en lugar de tener que dedicar tiempo y recursos a la administración tanto de máquinas virtuales como de infraestructura. Además, puede seguir desarrollando su aplicación con las herramientas y la plataforma de código abierto de su elección, y entregar con la velocidad y eficacia que exige su negocio sin tener que aprender nuevas habilidades.

![Diagrama conceptual de Azure Database for MySQL](media/overview/1-azure-db-for-mysql-conceptual-diagram.png)

En este artículo se presentan los principales conceptos y características de Azure Database for MySQL relacionados con el rendimiento, la escalabilidad y la manejabilidad, con vínculos para explorar los detalles. Consulte estas guías de inicio rápido para comenzar:
- [Create an Azure Database for MySQL server using Azure Portal](quickstart-create-mysql-server-database-using-azure-portal.md) (Creación de un servidor de Azure Database for MySQL mediante Azure Portal)
- [Create an Azure Database for MySQL server using Azure CLI](quickstart-create-mysql-server-database-using-azure-cli.md) (Creación de un servidor de Azure Database for MySQL mediante la CLI de Azure)

Para ver ejemplos de la CLI de Azure, consulte:
- [Ejemplos de la CLI de Azure para Azure Database for MySQL](sample-scripts-azure-cli.md)

## <a name="adjust-performance-and-scale-within-seconds"></a>Ajustar el rendimiento y la escala en cuestión de segundos
En la versión preliminar, el servicio Azure Database for MySQL ofrece dos niveles de servicio: Básico y Estándar. Cada nivel ofrece diferentes niveles de rendimiento y funcionalidades para admitir cargas de trabajo de bases de datos, de ligeras a pesadas. Puede compilar su primera aplicación en una base de datos pequeña por poco dinero al mes y, después, ajustar la escala para satisfacer las necesidades de la solución. La escalabilidad dinámica permite a la base de datos responder de manera transparente a los cambiantes requisitos de recursos. Solo paga por los recursos que necesite y cuando los necesite. Vea los [Planes de tarifa](concepts-service-tiers.md) para obtener más información.

## <a name="monitoring-and-alerting"></a>Supervisión y alertas
¿Cómo se decide cuándo adaptar la velocidad? Utilice las características de supervisión y alertas de rendimiento integradas, combinadas con la clasificación de rendimiento basada en las unidades de proceso. Con estas herramientas puede evaluar rápidamente el impacto de escalar y reducir verticalmente unidades de proceso en función de sus necesidades de rendimiento actuales o futuras. Vea las [Alertas](howto-alert-on-metric.md) para obtener más información.

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
- Cree su primera aplicación con su lenguaje preferido: [Python](./connect-python.md) | [Node.JS](./connect-nodejs.md) | [Java](./connect-java.md) | [Ruby](./connect-ruby.md)  |  [PHP](./connect-php.md) | [.NET (C#)](./connect-csharp.md) | [Go](./connect-go.md)
