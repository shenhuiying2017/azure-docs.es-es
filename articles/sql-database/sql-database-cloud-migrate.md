<properties
   pageTitle="Migración de una base de datos de SQL Server a una Base de datos SQL | Microsoft Azure "
   description="Obtenga más información acerca de la migración de una base de datos de SQL Server local a una Base de datos SQL de Azure en la nube. Antes de migrar una base de datos, use herramientas de migración de bases de datos para determinar la compatibilidad."
   keywords="migración de base de datos, migración de base de datos de sql server, herramientas de migración de bases de datos, migración de la base de datos, migrar base de datos sql"
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
   ms.date="03/22/2016"
   ms.author="carlrab"/>

# Migración de una base de datos de SQL Server a una Base de datos SQL en la nube

En este artículo aprenderá a cómo migrar SQL Server 2005 local o una base de datos posterior en Base de datos SQL de Azure. En este proceso de migración, migrará el esquema y los datos de la base de datos de SQL Server en el entorno actual a la Base de datos SQL, siempre que la base de datos existente supere las pruebas de compatibilidad. Con [Base de datos SQL V12](sql-database-v12-whats-new.md), hay muy pocos problemas de compatibilidad restantes que no sean operaciones de nivel de servidor y entre bases de datos. Tendrán que efectuarse tareas de reingeniería en las bases de datos y aplicaciones basadas en [funciones incompatibles o parcialmente compatibles](sql-database-transact-sql-information.md) para solucionar estas incompatibilidades antes de migrar la Base de datos de SQL Server.

Para migrar, estos son los pasos que debe seguir:

- **Probar la compatibilidad**: primero debe validar la compatibilidad de base de datos con [Base de datos SQL V12](sql-database-v12-whats-new.md). 
- **Solucionar problemas de compatibilidad, si los hubiera**: si se produce un error de validación, debe corregir los errores de validación.  
- **Realizar la migración**: cuando la base de datos sea compatible, puede usar uno o varios métodos para realizar la migración. 

SQL Server ofrece varios métodos para llevar a cabo cada una de estas tareas. En este artículo se ofrece información general sobre los métodos disponibles para cada tarea. El siguiente diagrama ilustra los pasos y los métodos.

  ![Diagrama de migración de VSSSDT](./media/sql-database-cloud-migrate/03VSSSDTDiagram.png)
  
 > [AZURE.NOTE] Para migrar bases de datos que no sean de SQL Server, incluidos Microsoft Access, Sybase, MySQL Oracle y DB2 a Base de datos SQL de Azure, consulte [SQL Server Migration Assistant](http://blogs.msdn.com/b/ssma/) (Asistente para migración de SQL Server).

## Determinar si una base de datos de SQL Server es compatible para migrar a la Base de datos SQL

Para comprobar los problemas de compatibilidad de la Base de datos SQL antes de iniciar el proceso de migración, use uno de los métodos siguientes:

- [SQL Server Data Tools para Visual Studio ("SSDT")](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md): SSDT usa las reglas de compatibilidad más recientes para detectar incompatibilidades con Base de datos SQL V12. Si se detectan incompatibilidades, puede corregir los problemas detectados directamente en esta herramienta. Actualmente, este es el método recomendado para probar y corregir problemas de compatibilidad de Base de datos SQL V12. 
- [SqlPackage](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md): SqlPackage es una utilidad del símbolo del sistema que busca problemas de compatibilidad y, si encuentra alguno, genera un informe con los detectados. Si usa esta herramienta, asegúrese de utilizar la versión más reciente para usar las reglas de compatibilidad más recientes. Si se detectan errores, debe usar otra herramienta para corregir los problemas de compatibilidad detectados: se recomienda usar SSDT.  
- [Asistente para Exportar aplicación de capa de datos de SQL Server Management Studio](sql-database-cloud-migrate-determine-compatibility-ssms.md): este asistente detectará los errores y los notificará en la pantalla. Si no se detectan errores, puede continuar y completar la migración a Base de datos SQL. Si se detectan errores, debe usar otra herramienta para corregir los problemas de compatibilidad detectados: se recomienda usar SSDT.
- [Microsoft SQL Server 2016 Upgrade Advisor Preview](http://www.microsoft.com/download/details.aspx?id=48119): esta herramienta independiente, actualmente en versión preliminar, detectará incompatibilidades de Base de datos SQL V12 y generará un informe al respecto. Esta herramienta no incluye todavía las reglas de compatibilidad más recientes. Si no se detectan errores, puede continuar y completar la migración a Base de datos SQL. Si se detectan errores, debe usar otra herramienta para corregir los problemas de compatibilidad detectados: se recomienda usar SSDT. 
- [Asistente para migración a SQL Azure ("SAMW")](sql-database-cloud-migrate-fix-compatibility-issues.md): SAMW es una herramienta de CodePlex que usa las reglas de compatibilidad de Base de datos SQL de Azure V11 para detectar incompatibilidades de Base de datos SQL de Azure V12. Si se detectan incompatibilidades, puede corregir algunos de los problemas detectados directamente en esta herramienta. Puede que esta herramienta encuentre incompatibilidades que no es necesario corregir, pero fue la primera herramienta de asistencia para migración de Base de datos SQL de Azure de que se dispuso y hay gran cantidad de soporte sobre ella de la Comunidad de SQL Server. Además, esta herramienta puede completar la migración desde dentro de la propia herramienta. 

## Solución de problemas de compatibilidad de migración de bases de datos

Si se detectan problemas de compatibilidad, debe corregir estos problemas antes de continuar con la migración de la base de datos de SQL Server. Hay una gran variedad de problemas de compatibilidad que pueden surgir, en función de la versión de SQL Server de la base de datos de origen y de la complejidad de la base de datos que se migra. Cuanto más antigua sea la versión de SQL Server de la base de datos de origen, más posibles incompatibilidades encontrará. Use los siguientes recursos, además de una búsqueda específica en Internet mediante el motor de búsqueda de opciones:

- [Características de Base de datos de SQL Server no admitidas en Base de datos SQL de Azure](sql-database-transact-sql-information.md)
- [Funcionalidad del motor de base de datos no incluida en SQL Server 2016](https://msdn.microsoft.com/library/ms144262%28v=sql.130%29)
- [Funcionalidad del motor de base de datos no incluida en SQL Server 2014](https://msdn.microsoft.com/library/ms144262%28v=sql.120%29)
- [Funcionalidad del motor de base de datos no incluida en SQL Server 2012](https://msdn.microsoft.com/library/ms144262%28v=sql.110%29)
- [Funcionalidad del motor de base de datos no incluida en SQL Server 2008 R2](https://msdn.microsoft.com/library/ms144262%28v=sql.105%29)
- [Funcionalidad del motor de base de datos no incluida en SQL Server 2005](https://msdn.microsoft.com/library/ms144262%28v=sql.90%29)

Además de realizar búsquedas en Internet y usar estos recursos, otra posibilidad para saber la mejor forma de solucionar un problema de incompatibilidad consiste en usar los [foros de la Comunidad de SQL Server en MSDN](https://social.msdn.microsoft.com/Forums/sqlserver/home?category=sqlserver) o [StackOverflow](http://stackoverflow.com/).

Use una de las siguientes herramientas de migración de bases de datos para corregir los problemas detectados:

- Utilice [SQL Server Data Tools para Visual Studio ("SSDT")](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md): para usar SSDT, importe el esquema de base de datos a SQL Server Data Tools para Visual Studio ("SSDT"), compile el proyecto para una implementación de Base de datos SQL V12, solucione todos los problemas de compatibilidad detectados en SSDT y, luego, sincronice los cambios con la base de datos de origen (o una copia de la base de datos de origen). Actualmente, este es el método recomendado para probar y corregir problemas de compatibilidad de Base de datos SQL V12. Siga el vínculo para un [tutorial con SSDT](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md).
- Utilice [SQL Server Management Studio ("SSMS"](sql-database-cloud-migrate-fix-compatibility-issues-ssms.md): para usar SSMS, corrija los errores detectados con otra herramienta para ejecutar comandos de Transact-SQL a fin de corregir los errores detectados. Este método es sobre todo para que los usuarios avanzados modifiquen el esquema de base de datos directamente en la base de datos de origen. 
- Use [Asistente para migración a SQL Azure ("SAMW")](sql-database-cloud-migrate-fix-compatibility-issues.md): para usar SAMW, se genera un script de Transact-SQL de la base de datos de origen que luego transforma el asistente, siempre que sea posible, para que el esquema sea compatible con Base de datos SQL V12. Cuando finalice, SAMW puede conectarse a Base de datos SQL V12 para ejecutar el script. Esta herramienta también analizará los archivos de seguimiento para determinar problemas de compatibilidad. El script puede generarse solo con el esquema o puede incluir datos en formato BCP.

## Migración de una Base de datos SQL Server compatible con la Base de datos SQL

Para migrar una Base de datos SQL Server compatible, Microsoft proporciona varios métodos de migración para diversos escenarios. El método que elija depende de la tolerancia para el tiempo de inactividad, el tamaño y la complejidad de la base de datos de SQL Server y la conectividad a la nube de Microsoft Azure.

> [AZURE.SELECTOR]
- [Asistente para migración de SSMS](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md)
- [Exportación a un archivo BACPAC](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md)
- [Importación desde un archivo BACPAC](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
- [Replicación transaccional](sql-database-cloud-migrate-compatible-using-transactional-replication.md)

Para elegir el método de migración, la primera pregunta es si puede permitirse tener la base de datos fuera de la producción durante la migración. Migrar una base de datos mientras se lleva a cabo las transacciones activas puede provocar incoherencias de la base de datos y posibles daños de la base de datos. Existen diferentes modos de poner una base de datos en modo inactivo, desde la deshabilitación de la conectividad de cliente hasta la creación de una [instantánea de base de datos](https://msdn.microsoft.com/library/ms175876.aspx).

Para migrar con el tiempo de inactividad mínimo, use la [replicación de transacciones de SQL Server](sql-database-cloud-migrate-compatible-using-transactional-replication.md) si la base de datos cumple los requisitos para la replicación transaccional. Si puede permitirse algún tiempo de inactividad o va a realizar una migración de prueba de la base de datos de producción para una migración posterior, considere uno de los tres métodos siguientes.

- [Asistente para la migración SSMS](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md): en el caso de bases de datos pequeñas y medianas, la migración de bases de datos compatibles con SQL Server 2005 o versiones posteriores es un procedimiento tan sencillo como ejecutar el [Asistente para implementar bases de datos en Base de datos SQL de Microsoft Azure](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md) en SQL Server Management Studio.
- [Exportar a archivo BACPAC](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md) y, a continuación, [Importar desde el archivo BACPAC](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md): si tiene problemas de conectividad (sin conectividad, ancho de banda bajo o problemas de tiempo de espera) para bases de datos medianas y grandes, use un archivo [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4). Con este método, exporta el esquema de SQL Server y los datos a un archivo BACPAC y, a continuación, importa el archivo BACPAC en Base de datos SQL mediante el Asistente para exportar aplicaciones de nivel de datos en SQL Server Management Studio o la utilidad de línea de comandos [SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx).
- Use BACPAC y BCP conjuntamente: use un archivo [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) y [BCP](https://msdn.microsoft.com/library/ms162802.aspx) para bases de datos muy grandes con el fin de lograr mayor paralelización para aumentar el rendimiento, aunque con mayor complejidad. Con este método, migre el esquema y los datos por separado.
 - [Exporte el esquema solo a un archivo BACPAC](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md).
 - [Importe el esquema solo desde el archivo BACPAC](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md) en la Base de datos SQL.
 - Use [BCP](https://msdn.microsoft.com/library/ms162802.aspx) para extraer los datos a archivos sin formato y, a continuación, realizar una [carga paralela](https://technet.microsoft.com/library/dd425070.aspx) de estos archivos en Base de datos SQL de Azure.

	 ![Migración de una base de datos de SQL Server: migrar Base de datos SQL a la nube](./media/sql-database-cloud-migrate/01SSMSDiagram_new.png)

<!---HONumber=AcomDC_0323_2016-->