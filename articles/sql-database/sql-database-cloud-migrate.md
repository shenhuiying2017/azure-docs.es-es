---
title: "Migración de una base de datos de SQL Server a Azure SQL Database | Microsoft Docs"
description: "Obtenga más información acerca de la migración de una base de datos de SQL Server local a una Base de datos SQL de Azure en la nube. Antes de migrar una base de datos, use herramientas de migración de bases de datos para determinar la compatibilidad."
keywords: "migración de base de datos, migración de base de datos de sql server, herramientas de migración de bases de datos, migración de la base de datos, migrar base de datos sql"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 9cf09000-87fc-4589-8543-a89175151bc2
ms.service: sql-database
ms.custom: migrate and move
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: sqldb-migrate
ms.date: 11/08/2016
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 86bc7d89bb5725add8ba05b6f0978467147fd3ca
ms.openlocfilehash: 61fb027dfdd5830d87fe4fcfff57f685db71475e


---
# <a name="sql-server-database-migration-to-sql-database-in-the-cloud"></a>Migración de una base de datos de SQL Server a una Base de datos SQL en la nube
En este artículo aprenderá a migrar una instancia local de SQL Server 2005 o una base de datos posterior a Base de datos SQL de Azure. En este proceso de migración, migrará el esquema y los datos de la base de datos de SQL Server en el entorno actual a la Base de datos SQL. Para que el proceso se complete correctamente, la base de datos existente debe pasar primero una prueba de compatibilidad. Con SQL Database V12, nos estamos acercando a una situación de [paridad de características](sql-database-features.md), aparte del problema relacionado con las operaciones entre bases de datos y de nivel de servidor. Es necesario efectuar tareas de reingeniería en las bases de datos y aplicaciones basadas en [funciones incompatibles o parcialmente compatibles](sql-database-transact-sql-information.md) para solucionar estas incompatibilidades antes de migrar la Base de datos de SQL Server.

Para migrar, estos son los pasos que debe realizar:

* **Probar la compatibilidad**: valide la compatibilidad de la base de datos con SQL Database. 
* **Solucionar problemas de compatibilidad, si los hubiera**: si se produce un error de validación, debe corregir los errores de validación.  
* **Realizar la migración** : cuando la base de datos sea compatible, puede usar uno o varios métodos para realizar la migración. 

SQL Server ofrece varios métodos para llevar a cabo cada una de estas tareas. En este artículo se ofrece información general sobre los métodos disponibles para cada tarea. El siguiente diagrama ilustra los pasos y los métodos.

  ![Diagrama de migración de VSSSDT](./media/sql-database-cloud-migrate/03VSSSDTDiagram.png)

> [!NOTE]
> Para migrar bases de datos que no sean de SQL Server, incluidos Microsoft Access, Sybase, MySQL Oracle y DB2 a Base de datos SQL de Azure, consulte [SQL Server Migration Assistant](http://blogs.msdn.com/b/ssma/)(Asistente para migración de SQL Server).
> 
> 

## <a name="database-migration-tools-test-sql-server-database-compatibility-with-sql-database"></a>Determinar si una base de datos de SQL Server es compatible para migrar a la Base de datos SQL
Para comprobar los problemas de compatibilidad de la Base de datos SQL antes de iniciar el proceso de migración, use uno de los métodos siguientes:

> [!div class="op_single_selector"]
> * [SSDT](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md)
> * [SqlPackage](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md)
> * [SSMS](sql-database-cloud-migrate-determine-compatibility-ssms.md)
> * [SAMW](sql-database-cloud-migrate-fix-compatibility-issues.md)
> 
> 

* [SQL Server Data Tools para Visual Studio ("SSDT")](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md): SSDT usa las reglas de compatibilidad más recientes para detectar incompatibilidades con Base de datos SQL V12. Si se detectan incompatibilidades, puede corregir los problemas detectados directamente en esta herramienta. Este es el método recomendado para probar y corregir problemas de compatibilidad de Base de datos SQL V12. 
* [SqlPackage](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md): SqlPackage es una utilidad de línea de comandos que busca problemas de compatibilidad y genera un informe donde se reflejan los que haya detectado. Si usa esta herramienta, asegúrese de utilizar la versión más reciente para usar las reglas de compatibilidad más recientes. Si se detectan errores, debe usar otra herramienta para corregir los problemas de compatibilidad detectados: se recomienda usar SSDT.  
* [Asistente para exportación de aplicaciones de capa de datos de SQL Server Management Studio](sql-database-cloud-migrate-determine-compatibility-ssms.md): este asistente detecta los errores y los notifica en la pantalla. Si no se detectan errores, puede continuar y completar la migración a Base de datos SQL. Si se detectan errores, debe usar otra herramienta para corregir los problemas de compatibilidad detectados: se recomienda usar SSDT.
* [Asistente para migración a SQL Azure ("SAMW")](sql-database-cloud-migrate-fix-compatibility-issues.md): SAMW es una herramienta de CodePlex que usa las reglas de compatibilidad de Base de datos SQL de Azure V11 para detectar incompatibilidades de Base de datos SQL de Azure V12. Si se detectan incompatibilidades, puede corregir algunos de los problemas detectados directamente en esta herramienta. Esta herramienta puede encontrar incompatibilidades que no es necesario corregir. Fue la primera herramienta de asistencia para migración de Base de datos SQL de Azure disponible y recibe soporte activo por parte de la comunidad de SQL Server. Además, esta herramienta puede completar la migración desde dentro de la propia herramienta. 

## <a name="fix-database-migration-compatibility-issues"></a>Solución de problemas de compatibilidad de migración de bases de datos
Si se detectan problemas de compatibilidad, debe corregir estos problemas antes de continuar con la migración de la base de datos de SQL Server. Hay una gran variedad de problemas de compatibilidad que pueden surgir, en función de la versión de SQL Server de la base de datos de origen y de la complejidad de la base de datos que se migra. Las versiones anteriores de SQL Server tienen más problemas de compatibilidad. Use los siguientes recursos, además de una búsqueda específica en Internet mediante el motor de búsqueda de opciones:

* [Características de Base de datos de SQL Server no admitidas en Base de datos SQL de Azure](sql-database-transact-sql-information.md)
* [Funcionalidad del motor de base de datos no incluida en SQL Server 2016](https://msdn.microsoft.com/library/ms144262%28v=sql.130%29)
* [Funcionalidad del motor de base de datos no incluida en SQL Server 2014](https://msdn.microsoft.com/library/ms144262%28v=sql.120%29)
* [Funcionalidad del motor de base de datos no incluida en SQL Server 2012](https://msdn.microsoft.com/library/ms144262%28v=sql.110%29)
* [Funcionalidad del motor de base de datos no incluida en SQL Server 2008 R2](https://msdn.microsoft.com/library/ms144262%28v=sql.105%29)
* [Funcionalidad del motor de base de datos no incluida en SQL Server 2005](https://msdn.microsoft.com/library/ms144262%28v=sql.90%29)

Además de realizar búsquedas en Internet y utilizar estos recursos, acuda a los [foros de la comunidad de MSDN de SQL Server](https://social.msdn.microsoft.com/Forums/sqlserver/home?category=sqlserver) o a [StackOverflow](http://stackoverflow.com/).

Use una de las siguientes herramientas de migración de bases de datos para corregir los problemas detectados:

> [!div class="op_single_selector"]
> * [SSDT](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md)
> * [SSMS](sql-database-cloud-migrate-fix-compatibility-issues-ssms.md)
> * [SAMW](sql-database-cloud-migrate-fix-compatibility-issues.md)
> 
> 

* Utilice [SQL Server Data Tools para Visual Studio ("SSDT")](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md): para usar SSDT, importe el esquema de base de datos en SQL Server Data Tools para Visual Studio ("SSDT") y compile el proyecto para una implementación de Base de datos SQL V12. A continuación, corrija todos los problemas de compatibilidad detectados en SSDT. Cuando haya finalizado, sincronice los cambios de nuevo con la base de datos de origen (o una copia de la base de datos de origen). Actualmente, SSDT es el método recomendado para probar y corregir problemas de compatibilidad de Base de datos SQL V12. Siga el vínculo para ver un [tutorial con SSDT](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md).
* Utilice [SQL Server Management Studio ("SSMS")](sql-database-cloud-migrate-fix-compatibility-issues-ssms.md): para usar SSMS, ejecute comandos de Transact-SQL con el fin de corregir los errores detectados mediante otra herramienta. Este método es sobre todo para que los usuarios avanzados modifiquen el esquema de base de datos directamente en la base de datos de origen. 
* Utilice el [Asistente para migración de SQL Azure ("SAMW")](sql-database-cloud-migrate-fix-compatibility-issues.md): para usar SAMW, tiene que generar un script Transact-SQL de la base de datos de origen. El asistente transforma el script, siempre que sea posible, para que el esquema sea compatible con Base de datos SQL V12. Cuando finalice, SAMW puede conectarse a Base de datos SQL V12 para ejecutar el script. Esta herramienta también analiza los archivos de seguimiento para determinar problemas de compatibilidad. El script puede generarse solo con el esquema o puede incluir datos en formato BCP.

## <a name="migrate-a-compatible-sql-server-database-to-sql-database"></a>Migración de una Base de datos SQL Server compatible con la Base de datos SQL
Para migrar una Base de datos SQL Server compatible, Microsoft proporciona varios métodos de migración para diversos escenarios. El método que elija depende de la tolerancia para el tiempo de inactividad, el tamaño y la complejidad de la base de datos de SQL Server y la conectividad a la nube de Microsoft Azure.  

> [!div class="op_single_selector"]
> * [Asistente para migración de SSMS](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md)
> * [Exportación a un archivo BACPAC](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md)
> * [Importación desde un archivo BACPAC](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
> * [Replicación transaccional](sql-database-cloud-migrate-compatible-using-transactional-replication.md)
> 
> 

Para elegir el método de migración, la primera pregunta es si puede permitirse tener la base de datos fuera de la producción durante la migración. Migrar una base de datos mientras se lleva a cabo las transacciones activas puede provocar incoherencias de la base de datos y posibles daños de la base de datos. Existen diferentes modos de poner una base de datos en modo inactivo, desde la deshabilitación de la conectividad de cliente hasta la creación de una [instantánea de base de datos](https://msdn.microsoft.com/library/ms175876.aspx).

Para migrar con el tiempo de inactividad mínimo, use la [replicación de transacciones de SQL Server](sql-database-cloud-migrate-compatible-using-transactional-replication.md) si la base de datos cumple los requisitos para la replicación transaccional. Si puede permitirse algún tiempo de inactividad o va a realizar una migración de prueba de la base de datos de producción para una migración posterior, considere uno de los tres métodos siguientes.

* [Asistente para la migración SSMS](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md): en el caso de bases de datos pequeñas y medianas, la migración de bases de datos compatibles con SQL Server 2005 o versiones posteriores es un procedimiento tan sencillo como ejecutar el [asistente para la implementación de bases de datos en Microsoft Azure SQL Database](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md) en SQL Server Management Studio.
* [Exportar a archivo BACPAC](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md) y, después, [Importar desde el archivo BACPAC](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md): si tiene problemas de conectividad (sin conectividad, ancho de banda bajo o problemas de tiempo de espera) para bases de datos medianas y grandes, use un archivo [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4). Con este método, se exporta el esquema y los datos de SQL Server a un archivo BACPAC. A continuación, se importa el archivo BACPAC en Base de datos SQL mediante el Asistente para exportación de aplicaciones de capa de datos en SQL Server Management Studio o la utilidad de símbolo del sistema [SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx) .
* Use BACPAC y BCP conjuntamente: use un archivo [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) y [BCP](https://msdn.microsoft.com/library/ms162802.aspx) para bases de datos muy grandes con el fin de lograr mayor paralelización para aumentar el rendimiento, aunque con mayor complejidad. Con este método, migre el esquema y los datos por separado.
  
  * [Exporte el esquema solo a un archivo BACPAC](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md).
  * [Importe el esquema solo desde el archivo BACPAC](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md) en la Base de datos SQL.
  * Use [BCP](https://msdn.microsoft.com/library/ms162802.aspx) para extraer los datos a archivos sin formato y, a continuación, realizar una [carga paralela](https://technet.microsoft.com/library/dd425070.aspx) de estos archivos Azure SQL Database.
    
     ![Migración de una base de datos de SQL Server: migrar Base de datos SQL a la nube](./media/sql-database-cloud-migrate/01SSMSDiagram_new.png)

## <a name="next-steps"></a>Pasos siguientes
* [Versión más reciente de SSDT](https://msdn.microsoft.com/library/mt204009.aspx)
* [Versión más reciente de SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)

## <a name="additional-resources"></a>Recursos adicionales
* [Características de SQL Database](sql-database-features.md)
  [Funciones de Transact-SQL no admitidas o parcialmente admitidas](sql-database-transact-sql-information.md)
* [Migración de bases de datos no SQL Server mediante SQL Server Migration Assistant](http://blogs.msdn.com/b/ssma/)




<!--HONumber=Jan17_HO1-->


