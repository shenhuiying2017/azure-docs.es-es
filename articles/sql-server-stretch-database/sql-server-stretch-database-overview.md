---
title: "Información general sobre Stretch Database | Microsoft Docs"
description: "Aprenda cómo Stretch Database migra los datos inactivos de forma transparente y segura a la nube de Microsoft Azure."
services: sql-server-stretch-database
documentationcenter: 
author: douglaslMS
manager: jhubbard
editor: 
ms.assetid: c360dc10-a02b-446f-91a0-278358f7a297
ms.service: sql-server-stretch-database
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 06/27/2016
ms.author: douglasl
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: f63900b0a4f32e1bc5a88c068091916bd96aafb8


---
# <a name="stretch-database-overview"></a>Información general de Stretch Database
Stretch Database migra los datos inactivos de forma transparente y segura a la nube de Microsoft Azure.

Si quiere comenzar inmediatamente a utilizar Stretch Database, consulte [Get started by running the Enable Database for Stretch Wizard](sql-server-stretch-database-wizard.md)(Comenzar a ejecutar el asistente de habilitación de la base de datos para Stretch).

## <a name="what-are-the-benefits-of-stretch-database"></a>¿Cuáles son las ventajas de Stretch Database?
Stretch Database ofrece las siguientes ventajas:

### <a name="provides-cost-effective-availability-for-cold-data"></a>Disponibilidad rentable para datos inactivos
Stretch activa y desactiva datos transaccionales dinámicamente desde SQL Server en Microsoft Azure mediante SQL Server Stretch Database. A diferencia del almacenamiento habitual de datos inactivos, los datos siempre están en línea y disponibles para la consulta. Puede proporcionar escalas de tiempo de retención de datos más largas sin romper el banco de tablas grandes como la del Historial de pedidos del cliente. Se beneficia del bajo costo de Azure en lugar de escalar un almacenamiento \-local costoso. Le permite elegir el plan de tarifa y configurar opciones en el Portal de Azure para mantener el control sobre los costos. Escalado o reducción vertical según sea necesario. Para más información, visite la página de precios de [SQL Server Stretch Database](https://azure.microsoft.com/pricing/details/sql-server-stretch-database/) .

### <a name="doesnt-require-changes-to-queries-or-applications"></a>No es preciso realizar cambios en consultas o aplicaciones
Acceda sin problemas a los datos de SQL Server, tanto si están en un \-entorno local como en la nube.  Establezca la directiva que determina dónde se almacenan los datos y SQL Server controlará el movimiento de datos en segundo plano. La tabla completa siempre está en línea y lista para consultarla. Stretch Database no requiere ningún cambio en las consultas existentes ni en las aplicaciones y la ubicación de los datos es completamente transparente para la aplicación.

### <a name="streamlines-on-premises-data-maintenance"></a>Optimiza el mantenimiento local de los datos\-
Reduzca el mantenimiento y almacenamiento local de los datos.\- Las copias de seguridad de los datos locales se ejecutan más rápidamente y terminan dentro de la ventana de tiempo de mantenimiento.\- Se ejecutan automáticamente copias de seguridad de la parte de la nube que contiene los datos. Se reducen significativamente sus necesidades de almacenamiento local. \- Azure Storage puede ser un 80 % más barato que agregar a \-SSD local.

### <a name="keeps-your-data-secure-even-during-migration"></a>Conserva los datos seguros, incluso durante la migración
Disfrute de la tranquilidad de ampliar las aplicaciones más importantes de forma segura a la nube. Always Encrypted de SQL Server proporciona cifrado para los datos en movimiento. La seguridad de nivel de fila (RLS) y otras características avanzadas de seguridad de SQL Server también funcionan junto con Stretch Database para proteger los datos.

## <a name="what-does-stretch-database-do"></a>¿Qué hace Stretch Database?
Después de habilitar Stretch Database para una instancia de SQL Server, una base de datos y al menos una tabla, Stretch Database empieza a migrar los datos inactivos a Azure en modo silencioso.

* Si almacena los datos inactivos en una tabla independiente, puede migrar toda la tabla.
* Si la tabla contiene datos activos e inactivos, puede especificar una función de filtro para seleccionar las filas que van a migrar.

**No tiene que cambiar las consultas ni las aplicaciones cliente existentes.**  Sigue teniendo acceso sin interrupción a los datos locales y remotos, incluso durante la migración de datos. Hay una pequeña cantidad de latencia para las consultas remotas, pero esta latencia solo se encuentra al consultar los datos inactivos.

**Stretch Database garantiza que no se perderán datos** si se produce un error durante la migración. También tiene una lógica de reintento para tratar los problemas de conexión que se pueden producir durante la migración. Una vista de administración dinámica proporciona el estado de la migración.

**Puede pausar la migración de datos** tanto para solucionar problemas en el servidor local como para maximizar el ancho de banda de red disponible.

![Información general de Stretch Database][StretchOverviewImage1]

## <a name="is-stretch-database-for-you"></a>¿Es Stretch Database útil para usted?
Si reúne los siguientes requisitos, Stretch Database puede ayudarle a satisfacer sus necesidades y solucionar los problemas.

| Si es la persona que toma las decisiones | Si es una persona jurídica |
| --- | --- |
| Debo mantener los datos transaccionales durante mucho tiempo. |El tamaño de las tablas empieza a estar fuera de control. |
| A veces, se deben consultar los datos inactivos. |Mis usuarios afirman que quieren acceder a los datos inactivos, pero lo hacen solo en raras ocasiones. |
| Tengo aplicaciones, incluidas aplicaciones anteriores, que no quiero actualizar. |Tengo que seguir comprando y agregando más espacio de almacenamiento. |
| Quiero encontrar una manera de ahorrar costos en almacenamiento. |No puedo realizar la copia de seguridad o restaurar tablas tan grandes con el contrato de nivel de servicio actual. |

## <a name="what-kind-of-databases-and-tables-are-candidates-for-stretch-database"></a>¿Qué tipos de bases de datos y tablas son candidatas a Stretch Database?
Stretch Database está pensado para bases de datos transaccionales con grandes cantidades de datos inactivos que normalmente están almacenados en un pequeño número de tablas. Estas tablas pueden contener más de mil millones de filas.

Si utiliza la característica de tabla temporal de SQL Server 2016, use Stretch Database para migrar toda la tabla histórica asociada o parte de ella a un almacenamiento\-rentable en Azure. Para más información, consulte [Administración de la retención de datos históricos en las tablas temporales con versiones del sistema](https://msdn.microsoft.com/library/mt637341.aspx).

Use Stretch Database Advisor, una característica del Asesor de actualizaciones de SQL Server 2016, para identificar las bases de datos y tablas para Stretch Database. Para más información, consulte [Identificar bases de datos y tablas para Stretch Database al ejecutar el Asesor de Stretch Database](sql-server-stretch-database-identify-databases.md). Para más información acerca de posibles problemas de bloqueo, consulte [Limitaciones de Stretch Database](sql-server-stretch-database-limitations.md).

## <a name="test-drive-stretch-database"></a>Comprobación de Stretch Database
**Pruebe Stretch Database con la base de datos de ejemplo de AdventureWorks.** Para obtener la base de datos de ejemplo de AdventureWorks, descargue al menos el archivo de base de datos y el archivo de ejemplos y de scripts desde [aquí](https://www.microsoft.com/download/details.aspx?id=49502)(Comenzar a ejecutar el asistente de habilitación de la base de datos para Stretch). Después de restaurar la base de datos de ejemplo en una instancia de SQL Server 2016, descomprima el archivo de ejemplos y abra el archivo de ejemplos de Stretch Database desde la carpeta correspondiente. Ejecute los scripts en este archivo para comprobar el espacio utilizado por los datos antes y después de habilitar Stretch Database, para realizar un seguimiento del progreso de la migración de datos y para confirmar que aún puede consultar los datos existentes e insertar nuevos datos durante la migración y después de la misma.

## <a name="next-step"></a>Paso siguiente
**Identificación de las bases de datos y las tablas que son candidatas a Stretch Database.**  Para identificar las bases de datos y las tablas que son candidatas a Stretch Database, descargue el Asesor de actualizaciones de SQL Server 2016 y ejecute Stretch Database Advisor. Stretch Database Advisor también identifica problemas de bloqueo. Para más información, consulte [Identificar bases de datos y tablas para Stretch Database al ejecutar el Asesor de Stretch Database](sql-server-stretch-database-identify-databases.md).

<!--Image references-->
[StretchOverviewImage1]: ./media/sql-server-stretch-database-overview/StretchDBOverview.png
[StretchOverviewImage2]: ./media/sql-server-stretch-database-overview/StretchDBOverview1.png
[StretchOverviewImage3]: ./media/sql-server-stretch-database-overview/StretchDBOverview2.png



<!--HONumber=Dec16_HO2-->


