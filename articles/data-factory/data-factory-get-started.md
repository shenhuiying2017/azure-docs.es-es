<properties
	pageTitle="Introducción al uso de la Factoría de datos de Azure"
	description="En este tutorial se muestra cómo crear una canalización de datos de ejemplo que copia datos de un blob a una instancia de base de datos de SQL de Azure."
	services="data-factory"
	documentationCenter=""
	authors="spelluru"
	manager="jhubbard"
	editor="monicar"/>

<tags
	ms.service="data-factory"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article" 
	ms.date="05/04/2015"
	ms.author="spelluru"/>

# Introducción a la Factoría de datos de Azure
> [AZURE.SELECTOR]
- [Tutorial Overview](data-factory-get-started.md)
- [Using Data Factory Editor](data-factory-get-started-using-editor.md)
- [Using PowerShell](data-factory-monitor-manage-using-powershell.md)

El tutorial de este artículo le ayuda a empezar a usar rápidamente el servicio de la factoría de datos de Azure. En este tutorial, creará una factoría de datos de Azure y creará una canalización en la factoría de datos para copiar datos desde un almacenamiento de blobs de Azure a una base de datos de SQL de Azure.

> [AZURE.NOTE]Para obtener información general detallada del servicio de la factoría de datos, vea el artículo [Introducción a la factoría de datos de Azure][data-factory-introduction].

##Requisitos previos para el tutorial
Antes de empezar este tutorial, debe contar con lo siguiente:

- **Suscripción de Azure**. Si no tiene una suscripción, puede crear una cuenta de prueba gratuita en tan solo un par de minutos. Consulte el artículo [Evaluación gratuita][azure-free-trial] para obtener información.
- **Cuenta de almacenamiento de Azure**. Usará el almacenamiento de blobs como un almacén de datos de **origen** en este tutorial. Si no tiene una cuenta de almacenamiento de Azure, vea el artículo [Crear una cuenta de almacenamiento][data-factory-create-storage] para ver los pasos para crear una.
- **Base de datos de SQL Azure**. Usará una base de datos SQL de Azure como un almacén de datos de **destino** en este tutorial. Si no dispone de una base de datos SQL de Azure que pueda usar en el tutorial, vea [Cómo crear y configurar una base de datos de SQL de Azure][data-factory-create-sql-database] para crear una.
- **SQL Server 2012/2014 o Visual Studio 2013**. Usará SQL Server Management Studio o Visual Studio para crear una base de datos de ejemplo y ver los datos de resultados de la base de datos.  

### Recopilación del nombre de cuenta y la clave de cuenta para su cuenta de almacenamiento de Azure
Necesitará el nombre de cuenta y la clave de cuenta de su cuenta de almacenamiento de Azure para realizar este tutorial. Anote el **nombre de cuenta** y la **clave de cuenta** para su cuenta de almacenamiento de Azure siguiendo las siguientes instrucciones:

1. Inicie sesión en el [Portal de vista previa de Azure][azure-preview-portal].
2. Haga clic en el centro **EXAMINAR** de la izquierda y seleccione **Cuentas de almacenamiento**.
3. En la hoja **Cuentas de almacenamiento**, seleccione la **cuenta de almacenamiento de Azure** que desea usar en este tutorial.
4. En la hoja **ALMACENAMIENTO**, haga clic en la ventana **CLAVES**.
5. En la hoja **Administrar claves**, haga clic en el botón **copiar** (imagen) que se encuentra junto al cuadro de texto **NOMBRE DE CUENTA DE ALMACENAMIENTO** y guárdelo y péguelo en algún lugar (por ejemplo: en un archivo de texto).  
6. Repita el paso anterior para copiar o anotar la **CLAVE DE ACCESO PRIMARIA**.
7. Haga clic en **X** para cerrar todas las hojas.

### Recopile el nombre del servidor, el nombre de la base de datos y la cuenta de usuario para la base de datos SQL de Azure.
Necesitará los nombres de servidor SQL de Azure, la base de datos y el usuario para realizar este tutorial. Anote los nombres de **servidor**, **base de datos** y **usuario** para la base de datos SQL de Azure siguiendo las siguientes instrucciones:

1. En el **Portal de vista previa de Azure**, haga clic en **EXAMINAR** a la izquierda y seleccione **Bases de datos SQL**.
2. En la **hoja de bases de datos SQL**, seleccione la **base de datos** que desea usar en este tutorial. Anote el **nombre de la base de datos**.  
3. En la hoja **BASE DE DATOS SQL**, haga clic en la hoja **PROPIEDADES**.
4. Anote los valores de **NOMBRE DEL SERVIDOR** e **INICIO DE SESIÓN DE ADMINISTRADOR DE SERVIDOR**.
5. Haga clic en **X** para cerrar todas las hojas.

### Permiso para que los servicios de Azure obtengan acceso al servidor de SQL de Azure
Asegúrese de que la configuración **Permitir el acceso a los servicios de Azure** está **ACTIVADA** para el servidor de SQL de Azure para que el servicio de la factoría de datos pueda acceder al servidor de SQL de Azure. Para comprobar y activar esta configuración, haga lo siguiente:

1. Haga clic en el concentrador **EXAMINAR** a la izquierda y haga clic en **Servidores SQL**.
2. Seleccione **su servidor** y haga clic en **CONFIGURACIÓN** en la hoja **SQL SERVER**.
3. En la hoja **CONFIGURACIÓN**, haga clic en **Firewall**.
4. En la hoja **Configuración de firewall**, haga clic en **ACTIVADA** para **Permitir el acceso a los servicios de Azure**.
5. Haga clic en **X** para cerrar todas las hojas.

### Preparación del almacenamiento de blobs de Azure y la base de datos SQL de Azure para el tutorial
Ahora, prepare su almacenamiento de blobs de Azure y base de datos SQL de Azure para el tutorial realizando los pasos siguientes:

1. Inicie el Bloc de notas, pegue el texto siguiente y guárdelo como **emp.txt** en la carpeta **C:\ADFGetStarted** del disco duro.

        John, Doe
		Jane, Doe

2. Use herramientas como el [Explorador de almacenamiento de Azure](https://azurestorageexplorer.codeplex.com/) para crear el contenedor **adftutorial** y cargar el archivo **emp.txt** en el contenedor.

    ![Explorador de almacenamiento de Azure][image-data-factory-get-started-storage-explorer]
3. Use el siguiente script de SQL para crear la tabla **emp** en su Base de datos SQL de Azure.  


        CREATE TABLE dbo.emp
		(
			ID int IDENTITY(1,1) NOT NULL,
			FirstName varchar(50),
			LastName varchar(50),
		)
		GO

		CREATE CLUSTERED INDEX IX_emp_ID ON dbo.emp (ID);

	**Si tiene SQL Server 2012/2014 instalado en el equipo:** siga las instrucciones del artículo [Paso 2: Conectar con la base de datos SQL de la base de datos de administración de SQL de Azure con SQL Server Management Studio][sql-management-studio] para conectarse al servidor SQL de Azure y ejecutar el script de SQL. Tenga en cuenta que en este artículo se usa el portal de administración de versión (http://manage.windowsazure.com), no el portal de vista previa (http://portal.azure.com), para configurar el firewall para un servidor SQL de Azure.

	**Si tiene instalado Visual Studio 2013 en el equipo:** en el [Portal de vista previa de Azure](http://portal.azure.com), haga clic en el concentrador **EXAMINAR** a la izquierda, en **Servidores SQL**, seleccione su base de datos y haga clic en el botón **Abrir en Visual Studio** de la barra de herramientas para conectarse a su servidor SQL de Azure y ejecutar el script. Si el cliente no tiene permiso para acceder al servidor SQL de Azure, tendrá que configurar el firewall de su servidor SQL de Azure para permitir el acceso desde su equipo (dirección IP). Consulte el artículo anterior para conocer los pasos para configurar el firewall para el servidor SQL de Azure.


Haga lo siguiente:

- Haga clic en el vínculo [Uso del editor de la Factoría de datos](data-factory-get-started-using-editor.md) en la parte superior para realizar el tutorial con el Editor de Factoría de datos, que forma parte del Portal de Azure.
- Haga clic en el vínculo [Uso de PowerShell](data-factory-monitor-manage-using-powershell.md) en la parte superior para realizar el tutorial con PowerShell de Azure.


<!--Link references-->
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[msdn-activities]: https://msdn.microsoft.com/library/dn834988.aspx
[msdn-linkedservices]: https://msdn.microsoft.com/library/dn834986.aspx
[data-factory-naming-rules]: https://msdn.microsoft.com/library/azure/dn835027.aspx

[azure-preview-portal]: https://portal.azure.com/
[download-azure-powershell]: http://azure.microsoft.com/documentation/articles/install-configure-powershell
[sql-management-studio]: http://azure.microsoft.com/documentation/articles/sql-database-manage-azure-ssms/#Step2
[sql-cmd-exe]: https://msdn.microsoft.com/library/azure/ee336280.aspx

[data-factory-editor]: data-factory-editor.md
[monitor-manage-using-powershell]: data-factory-monitor-manage-using-powershell.md
[use-onpremises-datasources]: data-factory-use-onpremises-datasources.md
[adf-tutorial]: data-factory-tutorial.md
[use-custom-activities]: data-factory-use-custom-activities.md
[use-pig-and-hive-with-data-factory]: data-factory-pig-hive-activities.md
[copy-activity]: data-factory-copy-activity.md
[troubleshoot]: data-factory-troubleshoot.md
[data-factory-introduction]: data-factory-introduction.md
[data-factory-create-storage]: http://azure.microsoft.com/documentation/articles/storage-create-storage-account/#create-a-storage-account
[data-factory-create-sql-database]: ../sql-database-create-configure.md


[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456

<!--Image references-->

[DataSlicesBySliceTime]: ./media/data-factory-get-started/DataSlicesBySliceTime.png

[image-data-factory-getstarted-new-everything]: ./media/data-factory-get-started/GetStarted-New-Everything.png

[image-data-factory-gallery-storagecachebackup]: ./media/data-factory-get-started/getstarted-gallery-datastoragecachebackup.png

[image-data-factory-gallery-storagecachebackup-seeall]: ./media/data-factory-get-started/getstarted-gallery-datastoragecachebackup-seeall.png

[image-data-factory-getstarted-data-services-data-factory-selected]: ./media/data-factory-get-started/getstarted-data-services-data-factory-selected.png

[image-data-factory-getstarted-data-factory-create-button]: ./media/data-factory-get-started/getstarted-data-factory-create-button.png

[image-data-factory-getstarted-new-data-factory-blade]: ./media/data-factory-get-started/getstarted-new-data-factory.png

[image-data-factory-get-stated-factory-home-page]: ./media/data-factory-get-started/getstarted-data-factory-home-page.png

[image-author-deploy-tile]: ./media/data-factory-get-started/getstarted-author-deploy-tile.png

[image-editor-newdatastore-button]: ./media/data-factory-get-started/getstarted-editor-newdatastore-button.png

[image-editor-blob-storage-json]: ./media/data-factory-get-started/getstarted-editor-blob-storage-json.png

[image-editor-blob-storage-deploy]: ./media/data-factory-get-started/getstarted-editor-blob-storage-deploy.png

[image-editor-azure-sql-settings]: ./media/data-factory-get-started/getstarted-editor-azure-sql-settings.png

[image-editor-newpipeline-button]: ./media/data-factory-get-started/getstarted-editor-newpipeline-button.png

[image-datafactoryblade-diagramtile]: ./media/data-factory-get-started/getstarted-datafactoryblade-diagramtile.png


[image-data-factory-get-started-startboard]: ./media/data-factory-get-started/getstarted-data-factory-startboard.png

[image-data-factory-get-started-linked-services-link]: ./media/data-factory-get-started/getstarted-data-factory-linked-services-link.png

[image-data-factory-get-started-linked-services-add-store-button]: ./media/data-factory-get-started/getstarted-linked-services-add-store-button.png

[image-data-factory-linked-services-get-started-new-data-store]: ./media/data-factory-get-started/getstarted-linked-services-new-data-store.png

[image-data-factory-get-started-new-data-store-with-storage]: ./media/data-factory-get-started/getstarted-linked-services-new-data-store-with-storage.png

[image-data-factory-get-started-storage-account-name-key]: ./media/data-factory-get-started/getstarted-storage-account-name-key.png

[image-data-factory-get-started-linked-services-list-with-myblobstore]: ./media/data-factory-get-started/getstarted-linked-services-list-with-myblobstore.png

[image-data-factory-get-started-linked-azure-sql-properties]: ./media/data-factory-get-started/getstarted-linked-azure-sql-properties.png

[image-data-factory-get-started-azure-sql-connection-string]: ./media/data-factory-get-started/getstarted-azure-sql-connection-string.png

[image-data-factory-get-started-linked-services-list-two-stores]: ./media/data-factory-get-started/getstarted-linked-services-list-two-stores.png

[image-data-factory-get-started-storage-explorer]: ./media/data-factory-get-started/getstarted-storage-explorer.png

[image-data-factory-get-started-diagram-link]: ./media/data-factory-get-started/getstarted-diagram-link.png

[image-data-factory-get-started-diagram-blade]: ./media/data-factory-get-started/getstarted-diagram-blade.png

[image-data-factory-get-started-home-page-pipeline-tables]: ./media/data-factory-get-started/getstarted-datafactory-home-page-pipeline-tables.png

[image-data-factory-get-started-datasets-blade]: ./media/data-factory-get-started/getstarted-datasets-blade.png

[image-data-factory-get-started-table-blade]: ./media/data-factory-get-started/getstarted-table-blade.png

[image-data-factory-get-started-dataslices-blade]: ./media/data-factory-get-started/getstarted-dataslices-blade.png

[image-data-factory-get-started-dataslice-blade]: ./media/data-factory-get-started/getstarted-dataslice-blade.png

[image-data-factory-get-started-sql-query-results]: ./media/data-factory-get-started/getstarted-sql-query-results.png

[image-data-factory-get-started-datasets-emptable-selected]: ./media/data-factory-get-started/DataSetsWithEmpTableFromBlobSelected.png

[image-data-factory-get-started-activity-run-details]: ./media/data-factory-get-started/ActivityRunDetails.png

[image-data-factory-create-resource-group]: ./media/data-factory-get-started/CreateNewResourceGroup.png

[image-data-factory-preview-storage-key]: ./media/data-factory-get-started/PreviewPortalStorageKey.png

[image-data-factory-database-connection-string]: ./media/data-factory-get-started/DatabaseConnectionString.png

[image-data-factory-new-datafactory-menu]: ./media/data-factory-get-started/NewDataFactoryMenu.png

[image-data-factory-sql-management-console]: ./media/data-factory-get-started/getstarted-azure-sql-management-console.png

[image-data-factory-sql-management-console-2]: ./media/data-factory-get-started/getstarted-azure-sql-management-console-2.png

[image-data-factory-name-not-available]: ./media/data-factory-get-started/getstarted-data-factory-not-available.png
 

<!---HONumber=62-->