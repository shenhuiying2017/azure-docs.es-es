<properties
   pageTitle="Creación de Almacenamiento de datos SQL en el Portal de Azure | Microsoft Azure"
   description="Aprenda a crear un almacenamiento de datos SQL de Azure en el Portal de Azure"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="barbkess"
   manager="jhubbard"
   editor=""
   tags="azure-sql-data-warehouse"/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="07/23/2016"
   ms.author="barbkess;lodipalm;sonyama"/>

# Creación de una instancia de Almacenamiento de datos SQL de Azure

> [AZURE.SELECTOR]
- [Portal de Azure](sql-data-warehouse-get-started-provision.md)
- [TSQL](sql-data-warehouse-get-started-create-database-tsql.md)
- [PowerShell](sql-data-warehouse-get-started-provision-powershell.md)

En este tutorial, usará el Portal de Azure para crear una instancia de Almacenamiento de datos SQL que contiene una base de datos de ejemplo AdventureWorksDW.


## Requisitos previos

Para empezar, necesitará lo siguiente:

- **Cuenta de Azure**: visite [Evaluación gratuita de Azure][] o [Crédito mensual de Azure para suscriptores de Visual Studio][] para crear una cuenta.
- **SQL Server de Azure**: consulte [Creación de un servidor lógico de Base de datos SQL de Azure con el Portal de Azure][] para más información.

> [AZURE.NOTE] La creación de una nueva instancia de Almacenamiento de datos SQL puede dar lugar a un nuevo servicio facturable. Consulte [Precios Almacenamiento de datos SQL][] para más información sobre los precios.

## Creación de Almacenamiento de datos SQL

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com).

2. Haga clic en **+ Nuevo** > **Datos y almacenamiento** > **Almacenamiento de datos SQL**.

    ![Crear](./media/sql-data-warehouse-get-started-provision/create-sample.gif)

3. En la hoja **Almacenamiento de datos SQL**, rellene la información necesaria y presione 'Crear' para crearlo.

    ![Crear base de datos](./media/sql-data-warehouse-get-started-provision/create-database.png)

	- **Servidor**: se recomienda que seleccione primero su servidor. Puede seleccionar uno existente o [crearlo](./sql-data-warehouse-get-started-new-server.md).

	- **Nombre de la base de datos**: el nombre que se usará para hacer referencia a Almacenamiento de datos SQL. Debe ser exclusivo en el servidor.
	
    - **Rendimiento**: se recomienda empezar con 400 [DWU][DWU]. Puede mover el control deslizante hacia la izquierda o la derecha para ajustar el rendimiento de su almacén de datos, o bien para reducirlo o escalarlo verticalmente una vez creado. Para aprender más sobre las DWU, consulte la documentación sobre el [escalado](./sql-data-warehouse-manage-compute-overview.md) o nuestra [página de precios][SQL Data Warehouse pricing].

    - **Suscripción**: seleccione la [suscripción] a la que se facturará por esta instancia de Almacenamiento de datos SQL.

    - **Grupo de recursos**: los [grupos de recursos][Resource group] son contenedores diseñados para ayudarle a administrar una colección de recursos de Azure. Obtenga más información sobre los [grupos de recursos](../resource-group-overview.md).

    - **Seleccionar origen**: haga clic en **Seleccionar origen** > **Muestra**. Dado que hay solo una base de datos de ejemplo disponible en este momento, al seleccionar Ejemplo, Azure rellena automáticamente la opción **Seleccionar muestra** con AdventureWorksDW.

4. Haga clic en **Crear** para crear su instancia de Almacenamiento de datos SQL.

5. Espere unos minutos a que la instancia de Almacenamiento de datos SQL esté lista. Cuando finalice, debería volver al [Portal de Azure](https://portal.azure.com). Encontrará Almacenamiento de datos SQL en el panel, entre las bases de datos SQL o en el grupo de recursos que usó para crearlo.

    ![Vista del portal](./media/sql-data-warehouse-get-started-provision/database-portal-view.png)

[AZURE.INCLUDE [Creación de servidor de base de datos SQL](../../includes/sql-database-create-new-server-firewall-portal.md)]

## Pasos siguientes

Ahora que ha creado una instancia de Almacenamiento de datos SQL, está listo para [conectarse](./sql-data-warehouse-connect-overview.md) y empezar a realizar consultas.

Para cargar datos en Almacenamiento de datos SQL, consulte la [información general sobre la carga](./sql-data-warehouse-overview-load.md).

Si intenta migrar una base de datos a Almacenamiento de datos SQL, consulte [Información general sobre migración](./sql-data-warehouse-overview-migrate.md) o use la [utilidad de migración](./sql-data-warehouse-migrate-migration-utility.md).

Las reglas de firewall también se pueden configurar mediante Transact-SQL. Para más información, consulte [sp\_set\_firewall\_rule][] y [sp\_set\_database\_firewall\_rule][].

También es una buena idea echar un vistazo a nuestros [procedimientos recomendados][].

<!--Article references-->
[Creación de un servidor lógico de Base de datos SQL de Azure con el Portal de Azure]: ../sql-database/sql-database-get-started.md#create-an-azure-sql-database-logical-server
[Create an Azure SQL Database logical server with PowerShell]: ../sql-database/sql-database-get-started-powershell.md#database-setup-create-a-resource-group-server-and-firewall-rule
[resource groups]: ../resource-group-template-deploy-portal.md
[procedimientos recomendados]: ./sql-data-warehouse-best-practices.md
[DWU]: ./sql-data-warehouse-overview-what-is.md#data-warehouse-units
[suscripción]: ../azure-glossary-cloud-terminology.md#subscription
[resource group]: ../azure-glossary-cloud-terminology.md#resource-group

<!--MSDN references-->
[sp\_set\_firewall\_rule]: https://msdn.microsoft.com/library/dn270017.aspx
[sp\_set\_database\_firewall\_rule]: https://msdn.microsoft.com/library/dn270010.aspx

<!--Other Web references-->
[SQL Data Warehouse pricing]: https://azure.microsoft.com/pricing/details/sql-data-warehouse/
[Precios Almacenamiento de datos SQL]: https://azure.microsoft.com/pricing/details/sql-data-warehouse/
[Evaluación gratuita de Azure]: https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F
[Crédito mensual de Azure para suscriptores de Visual Studio]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F

<!---HONumber=AcomDC_0727_2016-->