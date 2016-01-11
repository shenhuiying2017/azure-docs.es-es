<properties
   pageTitle="Migrar una base de datos a la Base de datos SQL de Azure"
   description="Base de datos SQL de Microsoft Azure, implementación de base de datos, migración de base de datos, importación de base de datos, exportación de base de datos, asistente para migración"
   services="sql-database"
   documentationCenter=""
   authors="carlrabeler"
   manager="jeffreyg"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management"
   ms.date="12/17/2015"
   ms.author="carlrab"/>

# Migrar una base de datos a la Base de datos SQL de Azure

En este artículo aprenderá a cómo migrar SQL Server 2005 local o una base de datos posterior en Base de datos SQL de Azure. En este proceso de migración, migre el esquema y los datos de la base de datos de SQL Server en el entorno actual en la Base de datos SQL que proporciona la base de datos que pasan las pruebas de compatibilidad. Con [Base de datos SQL V12](sql-database-v12-whats-new.md), hay muy pocos problemas de compatibilidad restantes que no sean operaciones de nivel de servidor y entre bases de datos. Tendrán que efectuarse tareas de reingeniería en las bases de datos y aplicaciones basadas en [funciones incompatibles o parcialmente compatibles](sql-database-transact-sql-information.md) para [solucionar estas incompatibilidades](sql-database-cloud-migrate-fix-compatibility-issues.md) para poder migrar la Base de datos de SQL Server.

> [AZURE.NOTE]Para migrar bases de datos que no sean de SQL Server, incluidos Microsoft Access, Sybase, MySQL Oracle y DB2 a Base de datos SQL de Azure, consulte [SQL Server Migration Assistant](http://blogs.msdn.com/b/ssma/) (Asistente para migración de SQL Server).

## Determinar si una base de datos de SQL Server es compatible para migrar a la base de datos SQL

> [AZURE.SELECTOR]
- [SqlPackage](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md)
- [SQL Server Management Studio](sql-database-cloud-migrate-determine-compatibility-ssms.md)

Para comprobar los problemas de compatibilidad de Base de datos SQL antes de iniciar el proceso de migración, use uno de los métodos siguientes:

- [SqlPackage use](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md): SqlPackage es una utilidad de línea de comandos que tendrá que comprobar y, si lo encuentra, generar un informe que contenga los problemas de compatibilidad detectados.
- [Use SQL Server Management Studio](sql-database-cloud-migrate-determine-compatibility-ssms.md): el nivel de datos de exportación mostrará el Asistente para aplicaciones en SQL Server Management Studio mostrará los errores detectados en la pantalla.

## Solución de problemas de compatibilidad

Si se detectan problemas de compatibilidad, debe corregir estos problemas de compatibilidad antes de continuar con la migración.

- Uso del [Asistente para migración a SQL Azure](sql-database-cloud-migrate-fix-compatibility-issues.md)
- Uso de [SQL Server Data Tools para Visual Studio](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md)
- Uso de [SQL Server Management Studio](sql-database-cloud-migrate-fix-compatibility-issues-ssms.md)

## Migración de una Base de datos SQL Server compatible con la Base de datos SQL

Para migrar una Base de datos SQL Server compatible, Microsoft proporciona varios métodos de migración para diversos escenarios. El método que elija depende de la tolerancia para el tiempo de inactividad, el tamaño y la complejidad de la base de datos de SQL Server y la conectividad a la nube de Microsoft Azure.

> [AZURE.SELECTOR]
- [SSMS Migration Wizard](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md)
- [Export to BACPAC File](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md)
- [Import from BACPAC File](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
- [Transactional Replication](sql-database-cloud-migrate-compatible-using-transactional-replication.md)

Para elegir el método de migración, la primera pregunta es si puede permitirse tener la base de datos fuera de la producción durante la migración. Migrar una base de datos mientras se lleva a cabo las transacciones activas puede provocar incoherencias de la base de datos y posibles daños de la base de datos. Existen diferentes modos de poner una base de datos en modo inactivo, desde la deshabilitación de la conectividad de cliente hasta la creación de una [instantánea de base de datos](https://msdn.microsoft.com/library/ms175876.aspx).

Para migrar con el tiempo de inactividad mínimo, use la [replicación de transacciones de SQL Server](sql-database-cloud-migrate-compatible-using-transactional-replication.md) si la base de datos cumple los requisitos para la replicación transaccional. Si puede permitirse algún tiempo de inactividad o va a realizar una migración de prueba de la base de datos de producción para una migración posterior, considere uno de los tres métodos siguientes.

- [Asistente para la migración SSMS](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md): en el caso de bases de datos pequeñas y medianas, la migración de bases de datos compatibles con SQL Server 2005 o versiones posteriores es un procedimiento tan sencillo como ejecutar el [Asistente para implementar bases de datos en Base de datos SQL de Microsoft Azure](sql-database-cloud-migrate-compatible-using-migration-wizard.md) en SQL Server Management Studio. 
- [Exportar a archivo BACPAC](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md) y, a continuación, [Importar desde el archivo BACPAC](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md): si tiene problemas de conectividad (sin conectividad, ancho de banda bajo o problemas de tiempo de espera) para bases de datos medianas y grandes, use un archivo [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4). Con este método, exporta el esquema de SQL Server y los datos a un archivo BACPAC y, a continuación, importa el archivo BACPAC en Base de datos SQL mediante el Asistente para exportar aplicaciones de nivel de datos en SQL Server Management Studio o la utilidad de línea de comandos [SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx).
- Use BACPAC y BCP conjuntamente: use un archivo [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) y [BCP](https://msdn.microsoft.com/library/ms162802.aspx) para bases de datos muy grandes con el fin de lograr mayor paralelización para aumentar el rendimiento, aunque con mayor complejidad. Con este método, migre el esquema y los datos por separado. 
 - [Exporte el esquema solo a un archivo BACPAC](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md).
 - [Importe el esquema solo desde el archivo BACPAC](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md) en la Base de datos SQL. 
 - Use [BCP](https://msdn.microsoft.com/library/ms162802.aspx) para extraer los datos a archivos sin formato y, a continuación, realizar una [carga paralela](https://technet.microsoft.com/library/dd425070.aspx) de estos archivos en Base de datos SQL de Azure.

	 ![Diagrama de migración de SSMS](./media/sql-database-cloud-migrate/01SSMSDiagram_new.png)

<!---HONumber=AcomDC_1223_2015-->