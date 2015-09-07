<properties
   pageTitle="Migración a Base de datos SQL mediante SSMS"
	description="Base de datos SQL de Microsoft Azure, migrar la base de datos sql, migrar con ssms"
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
	ms.date="08/24/2015"
	ms.author="carlrab"/>

#Migración de una base de datos compatible con SSMS

![Diagrama de migración de SSMS](./media/sql-database-migrate-ssms/01SSMSDiagram.png)

Si un esquema de base de datos es compatible con la Base de datos de SQL de Azure, la migración solo requiere la importación de la base de datos a Azure. Esto puede hacerse en un solo paso mediante SSMS con la implementación de la base de datos en Base de datos SQL de Azure, o como un proceso de dos pasos exportando primero un BACPAC de la base de datos e importando a continuación ese BACPAC a un Azure SQL Server como una base de datos nueva.

Al exportar un BACPAC, puede exportarlo a un archivo local o directamente a un blob de Azure. Si lo exporta localmente, puede cargar el archivo BACPAC exportado a un blob de Azure. Una vez que se almacena el archivo BACPAC en un blob de Azure, puede importar el BACPAC como una base de datos mediante el Portal de Azure. La ejecución de la importación en el Portal de Azure reducirá la latencia en el paso de importación que mejorará el rendimiento y la confiabilidad de la migración con bases de datos grandes.

Implementar directamente desde SSMS implementará siempre todo el esquema y todos los datos, mientras que la exportación mediante un BACPAC le permitirá seleccionar el subconjunto de objetos para exportar al BACPAC. El archivo BACPAC exportado siempre incluye el esquema de la base de datos completa y, de manera predeterminada, los datos de todas las tablas. Si se implementa desde SSMS o se exporta y después de importar desde SSMS (o el Portal de Azure), se usa la misma tecnología DAC interna y el resultado es el mismo.

Esta opción también se usa como el último paso de la opción 2 para migrar las bases de datos después de que se haya actualizado para que sea compatible con Base de datos SQL de Azure.

## Obtenga la versión más reciente de SQL Server Management Studio

Use la versión más reciente de Microsoft SQL Server Management Studio para SQL Server para asegurarse de que tiene las actualizaciones más recientes para las herramientas de SSMS y para interactuar con el Portal de Azure. Para obtener la última versión de Microsoft SQL Server Management Studio para SQL Server, [descárguelo](https://msdn.microsoft.com/library/mt238290.aspx) e instálelo en un equipo cliente con conectividad a la base de datos que va a migrar y a Internet.

##Uso de SSMS para implementar en Base de datos SQL de Azure
1.	Aprovisione un servidor lógico mediante el Portal de administración de Azure.
2.	Busque la base de datos de origen en el Explorador de objetos de SSMS y ejecute la tarea **Exportar aplicación del nivel de datos...**.

	![Implementación en Azure desde el menú Tareas](./media/sql-database-migrate-ssms/02MigrateusingSSMS.png)

3.	En el asistente para la implementación configure la conexión con el servidor lógico de Base de datos SQL de Azure de destino que se aprovisionó en el paso 1.
4.	Proporcione el **nombre** para la base de datos y establezca la **edición** (capa de servicio) y el **objetivo de servicio** (nivel de rendimiento). Consulte [Niveles de servicio de Base de datos SQL de Azure](sql-database-service-tiers.md) para obtener más información sobre cómo configurar estos valores.

	![Exportar configuración](./media/sql-database-migrate-ssms/03MigrateusingSSMS.png)

5.	Complete el asistente para migrar la base de datos. Según el tamaño y la complejidad de la base de datos, es posible que la implementación tarde desde unos minutos hasta varias horas. Si encuentra alguna incompatibilidad, las rutinas de validación de esquema detectarán errores rápidamente antes de que se produzca realmente cualquier implementación en Azure. Si los errores indican que el esquema de base de datos es incompatible con la Base de datos SQL, use la opción de migración 2. Consulte [Actualización de la base de datos in situ para su posterior implementación en Base de datos SQL de Azure](sql-database-migrate-visualstudio-ssdt.md).

##Uso de SSMS para exportar un BACPAC y después importarlo en Base de datos SQL
El proceso de implementación se puede dividir en dos pasos: exportar e importar. En el primer paso, se crea un archivo BACPAC que después se usa como entrada en el segundo paso.

1.	Aprovisione un servidor lógico mediante el Portal de administración de Azure.
2.	Busque la base de datos de origen en el Explorador de objetos de SSMS y seleccione la tarea **Implementación de bases de datos en Base de datos SQL de Azure...**

	![Exportar una aplicación de capa de datos desde el menú Tareas](./media/sql-database-migrate-ssms/04MigrateusingSSMS.png)

3. En el asistente para la exportación, configure la exportación para guardar el archivo BACPAC en una ubicación de disco local o en un blob de Azure. El archivo BACPAC exportado siempre incluye el esquema de la base de datos completa y, de manera predeterminada, los datos de todas las tablas. Si desea excluir los datos de algunas o todas las tablas, use la pestaña de opciones avanzadas. Por ejemplo, puede exportar únicamente los datos de las tablas de referencia.

	![Exportar configuración](./media/sql-database-migrate-ssms/05MigrateusingSSMS.png)

4.	Una vez creado el archivo BACPAC, conéctese al servidor que creó en el paso 1, haga clic con el botón derecho en la carpeta **Bases de datos** y seleccione **Importar aplicación de capa de datos...**
	
	>[AZURE.NOTE]Nota: También puede importar el archivo BACPAC almacenado en un blob de Azure desde dentro del Portal de administración de Azure.

	![Importar elemento de menú de aplicación de capa de datos](./media/sql-database-migrate-ssms/06MigrateusingSSMS.png)

5.	En el Asistente para importación, elija el archivo BACPAC que acaba de exportar para crear la nueva base de datos en Base de datos SQL de Azure.

	![Importar configuración](./media/sql-database-migrate-ssms/07MigrateusingSSMS.png)

6.	Proporcione el nombre para la base de datos y establezca la edición (capa de servicio) y el objetivo de servicio (nivel de rendimiento).

7.	Complete el asistente para importar el archivo BACPAC y cree la base de datos en Base de datos SQL de Azure.

	![Configuración de base de datos](./media/sql-database-migrate-ssms/08MigrateusingSSMS.png)

##Alternativas
También puede usar la utilidad de línea de comandos sqlpackage.exe para implementar la base de datos o exportar e importar un archivo BACPAC. Sqlpackage.exe usa la misma tecnología DAC como SSMS, por lo que el resultado es el mismo. Para obtener más información, consulte [SqlPackage.exe en MSDN](https://msdn.microsoft.com/library/hh550080.aspx).

<!---HONumber=August15_HO9-->