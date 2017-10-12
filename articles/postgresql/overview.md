---
title: "Introducción al servicio de base de datos relacional de Azure Database for PostgreSQL | Microsoft Docs"
description: "Proporciona información general sobre el servicio de base de datos relacional de Azure Database for MySQL."
services: postgresql
author: kamathsun
ms.author: sukamat
manager: jhubbard
editor: jasonwhowell
ms.custom: mvc
ms.service: postgresql
ms.topic: overview
ms.date: 08/01/2017
ms.openlocfilehash: 0dba4db0ae62a4f7e8440e80e830e5f64f5b49e0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="what-is-azure-database-for-postgresql"></a>¿Qué es Azure Database for PostgreSQL?

Azure Database for PostgreSQL es un servicio de base de datos relacional en Microsoft Cloud creado para los desarrolladores y basado en la versión de comunidad de código abierto del motor de base de datos [PostgreSQL](https://www.postgresql.org/). Este servicio está en versión preliminar pública. Azure Database for PostgreSQL ofrece:
- Rendimiento predecible en varios niveles de servicio
- Escalabilidad dinámica sin tiempo de inactividad de la aplicación
- Alta disponibilidad integrada
- Protección de datos

Todas estas funcionalidades apenas requieren tareas de administración y todas se proporcionan sin costo adicional. Estas funcionalidades permiten centrarse en el desarrollo rápido de aplicaciones y en reducir el plazo de acceso al mercado, en lugar de tener que dedicar tiempo y recursos a la administración tanto de máquinas virtuales como de infraestructura. Además, puede seguir desarrollando su aplicación con las herramientas y la plataforma de código abierto de su elección, y entregar con la velocidad y eficacia que exige su negocio sin tener que aprender nuevas habilidades. 

En este artículo se presentan los principales conceptos y características de Azure Database for PostgreSQL relacionados con el rendimiento, la escalabilidad y la manejabilidad. Consulte estas guías de inicio rápido para comenzar:

- [Create an Azure Database for PostgreSQL using Azure portal](quickstart-create-server-database-portal.md) (Creación de una base de datos Azure Database for PostgreSQL con Azure Portal)
- [Create an Azure Database for PostgreSQL using the Azure CLI](quickstart-create-server-database-azure-cli.md) (Creación de una base de datos Azure Database for PostgreSQL con la CLI de Azure)

Para obtener ejemplos de la CLI de Azure y de PowerShell, consulte:

- [Azure CLI samples for Azure Database for PostgreSQL](./sample-scripts-azure-cli.md) (Ejemplos de la CLI de Azure para Azure Database for PostgreSQL)

## <a name="adjust-performance-and-scale-without-downtime"></a>Ajuste del rendimiento y escalabilidad sin tiempo de inactividad

El servicio Azure Database for PostgreSQL actualmente ofrece dos niveles de servicio: Básico y Estándar. Cada nivel de servicio ofrece [diferentes niveles de rendimiento, garantías de IOPS y funcionalidades](concepts-service-tiers.md) para admitir todos los tipos de cargas de trabajo de bases de datos, de ligeras a pesadas. Puede compilar su primera aplicación en un servidor pequeño por poco dinero al mes y, después, [cambiar el nivel de rendimiento](scripts/sample-scale-server-up-or-down.md) dentro del servicio, ya sea manualmente o mediante programación, en cualquier momento para adecuarla a las necesidades de su solución. Y todo esto se puede hacer que la aplicación o los clientes sufran ningún tipo de inactividad. La escalabilidad dinámica permite que una base de datos responda transparentemente a los requisitos de recursos, que cambian con rapidez, y le permite pagar solo por los recursos que necesite cuando los necesite.

## <a name="monitoring-and-alerting"></a>Supervisión y alertas
¿Cómo se decide cuándo adaptar la velocidad? Utilice las características de supervisión y alertas de rendimiento integradas, combinadas con la clasificación de rendimiento basada en las unidades de proceso. Con estas herramientas puede evaluar rápidamente el impacto de escalar y reducir verticalmente unidades de proceso en función de sus necesidades de rendimiento actuales o futuras. Para más información, consulte [Opciones y rendimiento de Azure Database for PostgreSQL: información sobre lo que está disponible en cada nivel de servicio](./concepts-service-tiers.md).

## <a name="keep-your-app-and-business-running"></a>Mantenimiento de la aplicación y el negocio en funcionamiento
El Acuerdo de Nivel de Servicio (SLA) de Azure, con una disponibilidad del 99,99 % líder del sector (no disponible en la versión preliminar) y con la tecnología de una red global de centros de datos administrados por Microsoft, ayuda a mantener las aplicaciones en funcionamiento de forma ininterrumpida. Con cada servidor de Azure Database for PostgreSQL, se saca provecho de las ventajas de la seguridad integrada, la tolerancia a errores y la protección de datos, algo que de lo contrario tendría que adquirir o diseñar, compilar y administrar. Con Azure Database for PostgreSQL, cada nivel de servicio ofrece un conjunto completo de opciones y características de continuidad empresarial que puede usar para empezar a trabajar y seguir haciéndolo. Puede usar la [restauración a un momento dado](howto-restore-server-portal.md) para devolver una base de datos a un estado anterior, con un plazo máximo de 35 días. Además, si el centro de datos que hospeda las bases de datos sufre una interrupción, puede restaurar las bases de datos a partir de copias con redundancia geográfica de copias de seguridad recientes.

## <a name="secure-your-data"></a>Protección de los datos
Los servicios de bases de datos de Azure tienen una tradición de seguridad de datos que conserva Azure Database for PostgreSQL con características que limitan el acceso, protegen los datos en reposo y en movimiento, y le ayudan a supervisar la actividad. Para más información sobre la seguridad de plataforma de Azure, vaya al [Centro de confianza de Azure](https://www.microsoft.com/TrustCenter/Security/default.aspx).

La base de datos de Azure para el servicio PostgreSQL usa el cifrado de almacenamiento de los datos en reposo. Los datos, incluidas las copias de seguridad, se cifran en el disco (a excepción de los archivos temporales creados por el motor durante la ejecución de consultas). El servicio usa el cifrado AES de 256 bits que se incluye en el cifrado de almacenamiento de Azure y las claves están administradas por el sistema. El cifrado de almacenamiento siempre está activado y no se puede deshabilitar.

De forma predeterminada, la base de datos de Azure para el servicio MySQL está configurada para requerir la [seguridad de la conexión SSL](./concepts-ssl-connection-security.md) para los datos en movimiento en la red. Aplicar conexiones SSL entre el servidor de base de datos y las aplicaciones cliente ayuda a proteger contra los ataques de tipo "man in the middle" mediante el cifrado del flujo de datos entre el servidor y la aplicación.  Tiene la opción de deshabilitar el requisito de SSL para conectar el servicio de base de datos en caso de que la aplicación cliente no admita la conectividad SSL.

## <a name="next-steps"></a>Pasos siguientes
- Consulte la [página de precios](https://azure.microsoft.com/pricing/details/postgresql/) para ver comparaciones de costos y calculadoras.
- Comience por [crear su primera base de datos de Azure Database for PostgreSQL](./quickstart-create-server-database-portal.md).
- Compile la primera aplicación de Python, PHP, Ruby, C\#, Java, Node.js: [Bibliotecas de conexiones](./concepts-connection-libraries.md)
