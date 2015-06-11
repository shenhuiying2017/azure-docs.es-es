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

El tutorial en este artículo le ayudará a empezar a trabajar rápidamente con el uso del servicio de generador de datos de Azure. En este tutorial, creará un generador de datos de Azure y crear una canalización en el generador de datos para copiar datos desde un almacenamiento Azure blob en una base de datos de SQL Azure.

> [AZURE.NOTE]Para obtener una descripción detallada del servicio en la factoría de datos, vea el [Introducción al generador de datos de Azure][data-factory-introduction] artículo.

##Requisitos previos para el tutorial
Antes de empezar este tutorial, debe contar con lo siguiente:

- **Suscripción de azure**. Si no tiene una suscripción, puede crear una cuenta de evaluación gratuita en un par de minutos. Consulte la [gratuita][azure-free-trial] artículo para obtener más información.
- **Cuenta de almacenamiento de azure**. Va a usar el almacenamiento de blobs como un **origen** almacenan datos en este tutorial. Si no tiene una cuenta de almacenamiento de Azure, consulte la [crear una cuenta de almacenamiento][data-factory-create-storage] artículo para conocer los pasos crear uno.
- **Base de datos SQL azure**. Va a utilizar una base de datos de SQL Azure como un **destino** de almacén de datos en este tutorial. Si no dispone de una base de datos de SQL Azure que puede utilizar en el tutorial, vea [cómo crear y configurar una base de datos de SQL Azure][data-factory-create-sql-database] para crear uno.
- **SQL Server 2012/2014 o Visual Studio 2013**. SQL Server Management Studio o Visual Studio se utilizará para crear una base de datos de ejemplo y ver los datos de resultados de la base de datos.  

### Recopilar el nombre de cuenta y clave de su cuenta de almacenamiento de Azure
Necesitará el nombre de cuenta y la clave de cuenta de la cuenta de almacenamiento de Azure para este tutorial. Anote el **nombre de la cuenta** y **clave de cuenta** para su cuenta de almacenamiento de Azure siguiendo las instrucciones siguientes:

1. Inicie sesión en el [Portal de vista previa de Azure][azure-preview-portal].
2. Haga clic en **Examinar** concentrador de la izquierda y seleccione **cuentas de almacenamiento**.
3. En el **cuentas de almacenamiento** blade, seleccione el **cuenta de almacenamiento de Azure** que desea utilizar en este tutorial.
4. En el **almacenamiento** blade, haga clic en **claves** en mosaico.
5. En el **administrar claves** blade, haga clic en **copia** botón (imagen) junto a **STORAGE ACCOUNT NAME** texto cuadro y guardar, ¡ pegar en algún lugar (por ejemplo: en un archivo de texto).  
6. Repita el paso anterior para copiar o anote la **clave de acceso principal**.
7. Haga clic en Cerrar todos los módulos **X**.

### Recopilar el nombre del servidor, nombre de la base de datos y cuenta de usuario para la base de datos de SQL Azure
Necesitará los nombres de servidor SQL Azure, base de datos y usuario para seguir este tutorial. Anote los nombres de **server**, **base de datos**, y **usuario** para la base de datos de SQL Azure siguiendo las instrucciones siguientes:

1. En el **Portal de vista previa de Azure**, haga clic en **Examinar** a la izquierda y seleccione **bases de datos SQL**.
2. En el **blade de bases de datos SQL**, seleccione la **base de datos** que desea utilizar en este tutorial. Anote el **nombre de base de datos**.  
3. En el **base de datos SQL** blade, haga clic en **propiedades** en mosaico.
4. Anote los valores de **nombre del servidor** y **Inicio de sesión de administrador de servidor**.
5. Haga clic en Cerrar todos los módulos **X**.

### Permitir que los servicios de Azure tener acceso al servidor de SQL Azure
Asegúrese de que **Permitir el acceso a los servicios de Azure** configuración girada **ON** para el servidor de SQL Azure para que el servicio de generador de datos puede tener acceso al servidor de SQL Azure. Para comprobar y activar esta configuración, haga lo siguiente:

1. Haga clic en **Examinar** concentrador a la izquierda y haga clic en **servidores SQL**.
2. Seleccione **su servidor**, y haga clic en **configuración** en el **SQL SERVER** blade.
3. En el **configuración** blade, haga clic en **Firewall**.
4. En el **configuración de Firewall** blade, haga clic en **ON** para **Permitir el acceso a los servicios de Azure**.
5. Haga clic en Cerrar todos los módulos **X**.

### Preparación del almacenamiento de blobs de Azure y la base de datos SQL de Azure para el tutorial
Ahora, prepare el almacenamiento de blobs de Azure y base de datos de SQL Azure para el tutorial realizando los pasos siguientes:

1. Inicie el Bloc de notas, pegue el texto siguiente y guárdelo como **emp.txt** a **C:\ADFGetStarted** carpeta en el disco duro.

        John, Doe
		Jane, Doe

2. Usar herramientas como [Azure Storage Explorer](https://azurestorageexplorer.codeplex.com/) para crear la **adftutorial** contenedor y cargar la **emp.txt** archivo al contenedor.

    ![Explorador de almacenamiento de Azure][image-data-factory-get-started-storage-explorer]
3. Use el siguiente script SQL para crear el **emp** tabla en la base de datos de SQL Azure.  


        CREATE TABLE dbo.emp
		(
			ID int IDENTITY(1,1) NOT NULL,
			FirstName varchar(50),
			LastName varchar(50),
		)
		GO

		CREATE CLUSTERED INDEX IX_emp_ID ON dbo.emp (ID);

	**Si tiene SQL Server 2012/2014 instalado en el equipo:** siga las instrucciones de [paso 2: conectar con base de datos SQL de la administración de SQL de Azure con SQL Server Management Studio][sql-management-studio] artículo para conectarse al servidor SQL Azure y ejecutar la secuencia de comandos SQL. Tenga en cuenta que este artículo utiliza el portal de administración de versión (http://manage.windowsazure.com), no en el portal de vista previa (http://portal.azure.com), para configurar el firewall para un servidor de SQL Azure.

	**Si tiene instalado en el equipo de Visual Studio 2013:** en el [Portal de vista previa de Azure](http://portal.azure.com), haga clic en **Examinar** concentrador izquierdo, haga clic en la **servidores SQL Server**, seleccione la base de datos y haga clic en **abierta en Visual Studio** en la barra de herramientas para conectarse al servidor SQL Azure y ejecutar la secuencia de comandos. Si el cliente no tiene permiso para tener acceso al servidor de SQL Azure, necesitará configurar el firewall de su servidor de SQL Azure para permitir el acceso desde su equipo (dirección IP). Consulte el artículo anterior para conocer los pasos configurar el firewall para el servidor de SQL Azure.


Haga lo siguiente:

- Haga clic en [utilizando un Editor de generador de datos](data-factory-get-started-using-editor.md) vínculo en la parte superior para realizar el tutorial con Editor de generador de datos, que forma parte del Portal de Azure.
- Haga clic en [con PowerShell](data-factory-monitor-manage-using-powershell.md) vínculo en la parte superior para realizar el tutorial con Azure PowerShell.


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

<!---HONumber=GIT-SubDir--> 