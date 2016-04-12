<properties 
	pageTitle="Tutorial: creación de una canalización mediante el Asistente para copia" 
	description="En este tutorial, creará una canalización de Data Factory de Azure con una actividad de copia mediante el Asistente para copia compatible con Data Factory." 
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
	ms.topic="get-started-article" 
	ms.date="03/07/2016" 
	ms.author="spelluru"/>

# Tutorial: crear una canalización con la actividad de copia mediante el Asistente para copia de Data Factory
> [AZURE.SELECTOR]
- [Información general del tutorial](data-factory-get-started.md)
- [Uso del Editor de Data Factory.](data-factory-get-started-using-editor.md)
- [Uso de PowerShell](data-factory-monitor-manage-using-powershell.md)
- [Uso de Visual Studio](data-factory-get-started-using-vs.md)
- [Uso del Asistente para copia](data-factory-copy-data-wizard-tutorial.md)

En este tutorial, utilizará el Asistente para copia de Data Factory para crear una canalización con una actividad de copia en una factoría de datos. En primer lugar, cree una factoría de datos mediante el Portal de Azure y luego use el Asistente para copia para crear servicios vinculados, conjuntos de datos y una canalización de Data Factory con una actividad de copia que copia datos de un Almacenamiento de blobs de Azure a una Base de datos SQL de Azure.

> [AZURE.IMPORTANT] Antes de realizar este tutorial lea todo el artículo [Tutorial: Copia de datos de Almacenamiento de blobs de Azure a Base de datos SQL de Azure](data-factory-get-started.md) y complete los pasos de los requisitos previos.

## Creación de Data Factory
En este paso, utilice el Portal de Azure para crear una factoría de datos de Azure llamada **ADFTutorialDataFactory**.

1.	Después de iniciar sesión en el [Portal de Azure](https://portal.azure.com), haga clic en **+ NUEVO** en la esquina inferior izquierda, seleccione **Análisis de datos** en la hoja **Crear** y haga clic en **Factoría de datos** en la hoja **Análisis de datos**. 

	![New->DataFactory](./media/data-factory-copy-data-wizard-tutorial/new-data-factory-menu.png)

6. En la hoja **Nueva factoría de datos**:
	1. Escriba **ADFTutorialDataFactory** como **nombre**. 
	
  		![Hoja Nueva Factoría de datos](./media/data-factory-copy-data-wizard-tutorial/getstarted-new-data-factory.png)
	2. Haga clic en **NOMBRE DE GRUPO DE RECURSOS** y haga lo siguiente:
		1. Haga clic en **Crear un nuevo grupo de recursos**.
		2. En la hoja **Crear grupo de recursos**, escriba **ADFTutorialResourceGroup** para el **nombre** del grupo de recursos y haga clic en **Aceptar**. 

			![Crear grupo de recursos](./media/data-factory-copy-data-wizard-tutorial/create-new-resource-group.png)

		En algunos de los pasos de este tutorial se supone que se usa el nombre: **ADFTutorialResourceGroup** para el grupo de recursos. Para obtener más información sobre los grupos de recursos, consulte [Uso de grupos de recursos para administrar los recursos de Azure](../resource-group-overview.md).  
7. En la hoja **Nueva factoría de datos**, observe que está seleccionada la opción **Agregar al Panel de inicio**.
8. Haga clic en **Crear** en la hoja **Nueva factoría de datos**.

	El nombre del generador de datos de Azure debe ser único global. Si recibe el error: **El nombre del generador de datos "ADFTutorialDataFactory" no está disponible**, cambie el nombre de la factoría de datos (por ejemplo, yournameADFTutorialDataFactory) e intente crearlo de nuevo. Consulte el tema [Factoría de datos: reglas de nomenclatura](data-factory-naming-rules.md) para las reglas de nomenclatura para los artefactos de Factoría de datos.
	 
	![Nombre de Factoría de datos no disponible](./media/data-factory-copy-data-wizard-tutorial/getstarted-data-factory-not-available.png)
	
	> [AZURE.NOTE] El nombre de la factoría de datos se puede registrar como un nombre DNS en el futuro y, por lo tanto, hacerse públicamente visible.

9. Haga clic en el concentrador **NOTIFICACIONES** de la izquierda y busque las notificaciones del proceso de creación. Haga clic en **X** para cerrar la hoja **NOTIFICACIONES** si está abierta.
10. Una vez completada la creación, verá la hoja **FACTORÍA DE DATOS** como se muestra a continuación:

    ![Página principal de Factoría de datos](./media/data-factory-copy-data-wizard-tutorial/getstarted-data-factory-home-page.png)

## Creación de una canalización

1. En la página principal de Data Factory, haga clic en el icono **Copiar datos** para iniciar el **Asistente para copia**. 

	> [AZURE.NOTE] Si ve que el explorador web está atascado en "Autorizando...", deshabilite o desactive la opción **Bloquear cookies y datos de sitios de terceros** o déjela habilitada y cree una excepción para **login.microsoftonline.com** e intente iniciar de nuevo el asistente.
2. En la página **Propiedades**:
	1. Escriba **CopyFromBlobToAzureSql** en **Nombre de tarea**.
	2. Escriba una **descripción** (opcional).
	3. Anote la **fecha y hora de inicio** y la **fecha y hora de finalización**. Cambie la **fecha y hora de finalización** para que sea al día siguiente de la **fecha y hora de inicio**. 
	3. Haga clic en **Siguiente**.  

	![Herramienta de copia: página Propiedades](./media/data-factory-copy-data-wizard-tutorial/copy-tool-properties-page.png) 
3. En la página **Almacén de datos de origen**, haga clic en el icono **Almacenamiento de blobs de Azure**. Use esta página para especificar el almacén de datos de origen para la tarea de copia. Puede usar un servicio vinculado del almacén de datos existente, o bien especificar un almacén de datos nuevo. Para utilizar un servicio vinculado existente, haga clic en **DE SERVICIOS VINCULADOS EXISTENTES** y seleccione el servicio vinculado correcto. 

	![Herramienta de copia: página de Almacén de datos de origen](./media/data-factory-copy-data-wizard-tutorial/copy-tool-source-data-store-page.png)
5. En la página **Especificar cuenta de Almacenamiento de blobs de Azure**:
	1. Escriba **AzureStorageLinkedService** en **Nombre de servicio vinculado**.
	2. Confirme que **De suscripciones de Azure** es el valor de **Método de selección de cuenta**. 
	3. Seleccione una **cuenta de Almacenamiento de Azure** en la lista de cuentas de Almacenamiento de Azure disponibles en la suscripción que seleccionó. También puede elegir especificar la configuración de la cuenta de almacenamiento manualmente, para lo que debe seleccionar la opción **Especificar manualmente** en **Método de selección de cuenta** y luego hacer clic en **Siguiente**. 

	![Herramienta de copia: Especificar cuenta de Almacenamiento de blobs de Azure](./media/data-factory-copy-data-wizard-tutorial/copy-tool-specify-azure-blob-storage-account.png)
6. En la página **Elegir el archivo o la carpeta de entrada**:
	1. Navegue hasta la carpeta **adftutorial**.
	2. Seleccione **emp.txt** y haga clic en **Elegir**.
	3. Haga clic en **Siguiente**. 

	![Herramienta de copia: Elegir el archivo o la carpeta de entrada](./media/data-factory-copy-data-wizard-tutorial/copy-tool-choose-input-file-or-folder.png)
7. En la página **Configuración de formato de archivo**, seleccione los valores **predeterminados** y haga clic en **Siguiente**.

	![Herramienta de copia: Configuración de formato de archivo](./media/data-factory-copy-data-wizard-tutorial/copy-tool-file-format-settings.png)  
8. En la página Almacén de datos de destino, haga clic en el icono **Base de datos SQL de Azure** y haga clic en **Siguiente**.
9. En la página **Especificar la base de datos SQL de Azure**:
	1. Escriba **AzureSqlLinkedService** en el campo **Nombre de servicio vinculado**. 
	2. Confirme que en **Método de selección de servidor y base de datos** se ha seleccionado la opción **De suscripciones de Azure**.
	3. Seleccione **Nombre de servidor** y **Base de datos**.
	4. En **Nombre de usuario** y **Contraseña**, escriba los valores pertinentes.
	5. Haga clic en **Siguiente**.  
9. En la página **Asignación de tabla**, seleccione **emp** en la lista desplegable del campo **Destino** y haga clic en **flecha abajo** (opcional) para ver el esquema y obtener una vista previa de los datos.

	![Herramienta de copia: Asignación de tabla](./media/data-factory-copy-data-wizard-tutorial/copy-tool-table-mapping-page.png) 
10. En la página **Asignación de esquema**, haga clic en **Siguiente**.
11. Revise la información de la página **Resumen** y haga clic en **Finalizar**. Se crearán dos servicios vinculados, dos conjuntos de datos (entrada y salida) y una canalización en la factoría de datos (desde donde se inició al Asistente para copia). 
12. En la página **Implementación correcta**, haga clic en **Haga clic aquí para supervisar canalización de copia**.

	![Herramienta de copia: Implementación correcta](./media/data-factory-copy-data-wizard-tutorial/copy-tool-deployment-succeeded.png)  
13. Utilice las instrucciones de [Supervisar y administrar canalización mediante Aplicación de supervisión](data-factory-monitor-manage-app.md) para más información acerca de cómo supervisar la canalización que acaba de crear.

	![Aplicación de supervisión](./media/data-factory-copy-data-wizard-tutorial/monitoring-app.png)
  

<!---HONumber=AcomDC_0330_2016-->