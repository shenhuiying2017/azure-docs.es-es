<properties
	pageTitle="Información general de desarrollo de Base de datos SQL | Microsoft Azure"
	description="Aprenda sobre las bibliotecas de conectividad disponibles y los procedimientos recomendados para las aplicaciones que se conectan a Base de datos SQL."
	services="sql-database"
	documentationCenter=""
	authors="annemill"
	manager="jhubbard"
	editor="genemi"/>


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/02/2016"
	ms.author="annemill"/>

# Información general de desarrollo de Base de datos SQL
Este artículo le lleva por las consideraciones básicas que debe tener en cuenta un desarrollador al escribir código para conectarse a Base de datos SQL de Azure.

## Plataforma y lenguaje
Existen ejemplos de código para muchos lenguajes de programación y plataformas. Puede encontrar vínculos a los ejemplos de código en:

* Más información: [Bibliotecas de conexiones para Base de datos SQL y SQL Server](sql-database-libraries.md)

## Limitaciones de recursos
Base de datos SQL de Azure administra los recursos disponibles para una base de datos mediante dos mecanismos diferentes: Regulación de recursos y Aplicación de límites.

* Más información: [Límites de recursos de Base de datos SQL](sql-database-resource-limits.md)

## Seguridad
Base de datos SQL de Azure proporciona recursos para limitar el acceso, proteger los datos y supervisar las actividades en una base de datos SQL.

* Más información: [Protección de bases de datos SQL](sql-database-security.md)

## Autenticación
* Base de datos SQL de Azure admite inicios de sesión y usuarios de autenticación de SQL Server, así como de [autenticación de Azure Active Directory](sql-database-aad-authentication.md).
* Deberá especificar una base de datos concreta, en lugar de las bases de datos *maestra* predeterminada.
* No puede usar la instrucción **USE NombreDeMiBaseDeDatos;** de Transact-SQL en Base de datos SQL para cambiar a otra base de datos.
* Más información: [Seguridad de la Base de datos SQL: administrar la seguridad del inicio de sesión y el acceso a la base de datos](sql-database-manage-logins.md)

## Resistencia
Cuando se produce un error transitorio al conectar con Base de datos SQL, el código debe reintentar la llamada. Recomendamos que en la lógica de reintento se haga uso de la lógica de interrupción, de este modo no se sobrecargará innecesariamente la Base de datos SQL con los reintentos de varios clientes a la vez.

* Ejemplos de código: puede ver ejemplos de código que ilustran la lógica de reintento en el lenguaje de su elección en: [Bibliotecas de conexiones para Base de datos SQL y SQL Server](sql-database-libraries.md).
* Más información: [Códigos de error para las aplicaciones cliente de la Base de datos SQL: error de conexión de base de datos y otros problemas](sql-database-develop-error-messages.md)

## Administración de conexiones
* En la lógica de conexión de cliente, reemplace el tiempo de espera predeterminado para que sea de 30 segundos. El valor predeterminado de 15 segundos es demasiado corto para conexiones que dependen de Internet.
* Si usa un [grupo de conexiones](http://msdn.microsoft.com/library/8xx3tyca.aspx), asegúrese de cerrar la conexión en el momento en que el programa no la esté usando activamente y no esté preparándose para volver a usarla.

## Consideraciones de red
* En el equipo que hospeda el programa cliente, asegúrese de que el firewall permita la comunicación TCP saliente en el puerto 1433. Más información: [Configuración del firewall en la Base de datos SQL de Azure mediante el Portal de Azure](sql-database-configure-firewall-settings.md)
* Si el programa cliente se conecta a Base de datos SQL V12 mientras el cliente se ejecuta en una máquina virtual (VM) de Azure, debe abrir determinados intervalos de puerto en la máquina virtual. Más información: [Puertos más allá de 1433 para ADO.NET 4.5 y Base de datos SQL V12](sql-database-develop-direct-route-ports-adonet-v12.md)
* En ocasiones, las conexiones de cliente a la base de datos de SQL Azure V12 omiten al proxy e interactúan directamente con la base de datos. Los puertos que no sean 1433 se convierten en puertos importantes. Más información: [Puertos más allá de 1433 para ADO.NET 4.5 y Base de datos SQL V12](sql-database-develop-direct-route-ports-adonet-v12.md)

## Particionamiento de datos con la escala elástica
La escala elástica simplifica el proceso de escalado y reducción horizontal.

* [Modelos de diseño para las aplicaciones SaaS multiinquilino con base de datos SQL de Azure](sql-database-design-patterns-multi-tenancy-saas-applications.md)
* [Enrutamiento dependiente de los datos](sql-database-elastic-scale-data-dependent-routing.md)
* [Introducción a la vista previa de Escalado elástico de Base de datos SQL de Azure](sql-database-elastic-scale-get-started.md)

## Pasos siguientes

Explore todas las [funcionalidades de Base de datos SQL](https://azure.microsoft.com/services/sql-database/).

<!---HONumber=AcomDC_0720_2016-->