<properties
	pageTitle="Conexión a una base de datos SQL de Azure con Excel"
	description="Hoja de cálculo Excel a Base de datos SQL de Azure para generación de informes y exploración de datos."
	services="sql-database"
	documentationCenter=""
	authors="joseidz"
	manager="jeffreyg"
	editor="jeffreyg"/>


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="10/09/2015"
	ms.author="joseidz"/>


# Conexión a una Base de datos SQL de Azure con Excel

> [AZURE.SELECTOR]
- [C#](sql-database-connect-query.md)
- [SSMS](sql-database-connect-query-ssms.md)
- [Excel](sql-database-connect-excel.md)

En este artículo se muestra cómo conectar Excel a una Base de datos SQL de Azure y crear un informe de los datos de la base de datos. En primer lugar necesitará una Base de datos SQL. Si no tiene una, consulte [Crear la primera Base de datos SQL](sql-database-get-started.md) para poner en marcha y en unos minutos una base de datos con datos de ejemplo. Este artículo se basa en los datos de ejemplo de ese artículo, pero puede seguir estos pasos con sus propios datos.

También necesitará una copia de Excel. Este artículo usa [Microsoft Excel 2016](https://products.office.com/es-ES/).

## Conexión y creación de un informe

1.	Abra Excel y cree un nuevo libro o bien abra el libro que desea conectar.

2.	En la barra de menús de la parte superior de la página, haga clic sucesivamente en **Datos**, en **De otros orígenes** y en **Desde SQL Server**.
	
	![Selección de origen de datos](./media/sql-database-connect-excel/excel_data_source.png)

	Se abre el Asistente para la conexión de datos.

3.	En el cuadro de diálogo **Conectar con el servidor de la base de datos**, escriba el **Nombre de servidor** que hospeda el servidor lógico al que quiere conectarse con este formato **<*nombreDeServidor*>.database.windows.net**. Por ejemplo, **adventureserver.database.windows.net**.

4.	En la sección **Credenciales de inicio de sesión**, haga clic en **Usar el nombre de usuario y la contraseña siguientes**, escriba el **Nombre de usuario** y la **Contraseña** que haya configurado para el servidor de Base de datos SQL cuando la creó y, finalmente, haga clic en **Siguiente**.

	> [AZURE.TIP]Ambos complementos para Excel, [PowerPivot](https://www.microsoft.com/download/details.aspx?id=102) y [Power Query](https://www.microsoft.com/download/details.aspx?id=39379), ofrecen experiencias similares.

5. En el cuadro de diálogo **Seleccionar base de datos y tabla**, seleccione la base de datos **AdventureWorks** en el menú desplegable y seleccione **vGetAllCategories** en la lista de tablas y vistas; después, haga clic en **Siguiente**.

	![Selección de una base de datos y una tabla][5]

6. En el cuadro de diálogo **Guardar archivo de conexión de datos y finalizar**, haga clic en **Finalizar**.

7. En el cuadro de diálogo **Importar datos**, seleccione **Gráfico dinámico** y haga clic en **Aceptar**.

	![Selección de los datos para importar][2]

8. En el cuadro de diálogo **Campos de gráfico dinámico**, seleccione la configuración siguiente para crear un informe del recuento de productos por categoría.

	![Configuración][3]

	Un resultado correcto tiene este aspecto:

	![Correcto][4]

## Pasos siguientes

Si es un desarrollador de software como servicio (SaaS), obtenga información sobre los [grupos de bases de datos elásticas](sql-database-elastic-pool.md). Puede administrar fácilmente grandes colecciones de bases de datos usando [trabajos de base de datos elástica](sql-database-elastic-jobs-overview.md).

<!--Image references-->
[1]: ./media/sql-database-connect-excel/connect-to-database-server.png
[2]: ./media/sql-database-connect-excel/import-data.png
[3]: ./media/sql-database-connect-excel/power-pivot.png
[4]: ./media/sql-database-connect-excel/power-pivot-results.png
[5]: ./media/sql-database-connect-excel/select-database-and-table.png

<!---HONumber=Oct15_HO3-->