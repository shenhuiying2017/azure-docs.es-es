<properties
   pageTitle="Base de datos SQL de Azure crea aplicaciones multiempresa con eficacia y aislamiento."
   description="Obtenga información sobre cómo crear aplicaciones multiinquilino con Base de datos SQL de Azure"
   keywords=""
   services="sql-database"
   documentationCenter=""
   authors="carlrabeler"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management"
   ms.date="07/19/2016"
   ms.author="carlrab"/>

# Creación de aplicaciones multiempresa con Base de datos SQL de Azure con aislamiento y eficiencia

## Aprovechamiento de grupos elásticos y generación de aplicaciones multiinquilino más eficaces

Si es un desarrollador de aplicaciones SaaS que está escribiendo una aplicación multiinquilino que maneja muchos clientes, a menudo tiene que sacrificar un cierto grado de rendimiento, administración y seguridad para los clientes. Con los grupos de bases de datos elásticas de Base de datos SQL de Azure, ya no tiene que hacerlo. Estos grupos le ayudan a administrar y supervisar aplicaciones multiinquilino y a obtener los beneficios del aislamiento de un cliente por base de datos. Consulte [Modelos de diseño para las aplicaciones SaaS multiinquilino con base de datos SQL de Azure](sql-database-design-patterns-multi-tenancy-saas-applications.md).

![creación-aplicaciones-multiinquilino](./media/sql-database-build-multi-tenant-apps/sql-database-build-multi-tenant-apps.png)

## Escalado automático controlado

Los grupos escalan automáticamente el rendimiento y la capacidad de almacenamiento de las bases de datos elásticas sobre la marcha. Puede controlar el rendimiento que se asigna a un grupo, agregar o quitar bases de datos elásticas a petición y definir el rendimiento de las bases de datos elásticas sin afectar al costo general del grupo. Esto significa que no tiene que preocuparse de administrar el uso de bases de datos individuales.

[Lea la documentación](sql-database-elastic-pool.md)

## Administración inteligente del entorno

Las recomendaciones sobre ajuste de tamaño integradas identifican de manera proactiva las bases de datos que se beneficiarían de los grupos. Estas recomendaciones permiten el análisis de hipótesis para lograr una rápida optimización que le ayude a conseguir sus objetivos de rendimiento. Una potente supervisión de rendimiento junto con los paneles de solución de problemas le ayudarán a visualizar el historial de uso del grupo.

[Lea la documentación](sql-database-elastic-pool-guidance.md)

## Rendimiento y precio para satisfacer sus necesidades

Los grupos básicos, estándar y premium le proporcionan una amplia gama de rendimiento, almacenamiento y opciones de precios. Los grupos pueden contener un máximo de 400 bases de datos elásticas. Las bases de datos elásticas se pueden escalar verticalmente de forma automática hasta 1000 unidades de transacción de bases de datos elásticas (eDTU).

[Lea la documentación](https://azure.microsoft.com/pricing/details/sql-database/?b=16.50)

## Herramientas elásticas

Además de los grupos elásticos, existen características de Base de datos SQL para ayudar a administrar las actividades operativas entre varias bases de datos:

** Realice consultas e informes entre bases de datos. ** La [consulta de bases de datos elásticas](sql-database-elastic-query-overview.md) le permite ejecutar informes o consultas entre las bases de datos del grupo elástico y obtener acceso a datos remotos almacenados en muchas bases de datos del grupo a la vez.

** Ejecute transacciones entre bases de datos. ** Las [transacciones de base de datos elástica](sql-database-elastic-transactions-overview.md) le permiten ejecutar transacciones que abarcan varias bases de datos en Bases de datos SQL y realizar operaciones (por ejemplo, cuando se procesan las transacciones financieras o al actualizar el inventario y los pedidos en una base de datos).

** Ejecute las mismas operaciones en varias bases de datos. ** Los [trabajos de bases de datos elásticas](sql-database-elastic-jobs-overview.md) ejecutan operaciones administrativas, como volver a generar índices o actualizar los esquemas en cada base de datos del grupo elástico.

Vaya a la página principal para ver qué mas puede ofrecerle Base de datos SQL. [Compruébelo.](https://azure.microsoft.com/services/sql-database/)

## Pasos siguientes

Obtenga una [suscripción a Azure gratuita](https://azure.microsoft.com/get-started/) y [cree su primera base de datos SQL de Azure](sql-database-get-started.md).

## Recursos adicionales

Explore todas las [funcionalidades de Base de datos SQL](https://azure.microsoft.com/services/sql-database/).
 
Revise la [información general técnica de Base de datos SQL](sql-database-technical-overview.md).

<!---HONumber=AcomDC_0720_2016-->