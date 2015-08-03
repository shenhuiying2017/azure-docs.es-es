<properties
	pageTitle="Conexión a una base de datos SQL de Azure con Excel"
	description="Hoja de cálculo Excel a Base de datos SQL de Azure para generación de informes y exploración de datos."
	services="sql-database"
	documentationCenter=""
	authors="joseidz"
	manager="joseidz"
	editor="joseidz"/>


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/09/2015"
	ms.author="joseidz"/>


# Conexión a una base de datos SQL de Azure con Excel
Conecte Excel a una Base de datos SQL de Azure y cree un informe de los datos de la base de datos.

## Requisitos previos
- Una base de datos SQL de Azure SQL aprovisionada y en ejecución. Para crear una nueva base de datos SQL, vea [Introducción a la base de datos SQL de Microsoft Azure](sql-database-get-started.md).
- [Microsoft Excel 2013](https://products.office.com/es-es/) (o Microsoft Excel 2010)

## Conexión a Base de datos SQL y creación de informes
1.	Abra Excel.
2.	En la barra de menús en la parte superior de la página, haga clic en **Datos**.
3.	Haga clic en **De otros orígenes** y, después, en **De SQL Server**. El **Asistente para la conexión de datos** se abre.

	![Asistente para la conexión de datos][1]
4.	En el cuadro **Nombre del servidor**, escriba el nombre del servidor de Base de datos SQL de Azure. Ejemplo:

	 	adventureserver.database.windows.net
5.	En la sección **Credenciales de inicio de sesión**, seleccione**Usar el nombre de usuario y la contraseña siguientes** y escriba las credenciales adecuadas para el servidor de Base de datos SQL. A continuación, haga clic en **Siguiente**.

	Nota: ambos complementos para Excel, [PowerPivot](https://www.microsoft.com/download/details.aspx?id=102) y [Power Query](https://www.microsoft.com/download/details.aspx?id=39379), ofrecen experiencias muy similares.

6. En el cuadro de diálogo **Seleccionar base de datos y tabla**, seleccione la base de datos **AdventureWorks** en el menú desplegable y seleccione **vGetAllCategories** en la lista de tablas y vistas; después, haga clic en **Siguiente**.

	![Selección de una base de datos y una tabla][5]
7. En el cuadro de diálogo **Guardar archivo de conexión de datos y finalizar**, haga clic en **Finalizar**.
8. En el cuadro de diálogo **Importar datos**, seleccione **Gráfico dinámico** y haga clic en **Aceptar**.

	![Selección de los datos para importar][2]
9. En el cuadro de diálogo **Campos de gráfico dinámico**, seleccione la configuración siguiente para crear un informe del recuento de productos por categoría.

	![Configuración][3]
10.	Un resultado correcto tiene este aspecto:

	![Correcto][4]

## Pasos siguientes

Si es un desarrollador de Software como servicio (SaaS), obtenga información sobre los [grupos de bases de datos elásticas](sql-database-elastic-pool.md). Puede administrar fácilmente grandes colecciones de bases de datos usando [trabajos de base de datos elástica](sql-database-elastic-jobs-overview.md).

<!--Image references-->
[1]: ./media/sql-database-connect-excel/connect-to-database-server.png
[2]: ./media/sql-database-connect-excel/import-data.png
[3]: ./media/sql-database-connect-excel/power-pivot.png
[4]: ./media/sql-database-connect-excel/power-pivot-results.png
[5]: ./media/sql-database-connect-excel/select-database-and-table.png

<!---HONumber=July15_HO4-->