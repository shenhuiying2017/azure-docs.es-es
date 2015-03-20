1.	Inicie sesión en el [portal de vista previa de Azure](https://portal.azure.com/).
2.	En la barra de salto, haga clic en **Nuevo**, a continuación, seleccione **Datos y almacenamiento**y, a continuación, haga clic en **Base de datos de documentos**. 

	![Screen shot of the Azure Preview portal, highlighting the **New** button, **Data + storage** in the Create blade, and **DocumentDB** in the Data + storage blade][1]   

	O bien, en el panel de inicio, examine el catálogo de soluciones de Azure, seleccione **Datos y análisis**, elija **Base de datos de documentos** y, a continuación, haga clic en **Crear**.  
	
	![Screen shot of the Azure Preview portal, showing the Marketplace blade with the DocumentDB tile highlighted, and the DocumentDB blade with the Create button highlighted][2]   
   

3. En la hoja **Nueva Base de datos de documentos (vista previa)**, especifique la configuración para la cuenta. 
 
	![Screen shot of the New DocumentDB (Preview) blade][3] 


	- En el cuadro **Id.**, escriba un nombre para identificar la cuenta de Base de datos de documentos. Este valor se convierte en el nombre de host dentro del URI. El id. puede contener solo letras en minúsculas, números y el carácter '-' y debe tener entre 3 y 50 caracteres. Tenga en cuenta que documents.azure.com se anexa al nombre de extremo que elija; su resultado se convertirá en el extremo de la cuenta de Base de datos de documentos.

	- La lente **Nivel de precios** lente está bloqueada porque la vista previa de Base de datos de documentos admite un nivel de precios estándar único. Para obtener más información, vea [Precios de Base de datos de documentos](http://go.microsoft.com/fwlink/p/?LinkID=402317&clcid=0x409).

	- El modo **Configuración opcional** se usa para especificar la capacidad inicial asignada a su cuenta de Base de datos de documentos.  Base de datos de documentos aprovecha las unidades de capacidad para permitirle escalar su cuenta de Base de datos de documentos, donde cada unidad de capacidad incluye almacenamiento y rendimiento de base de datos reservados.  De manera predeterminada, se aprovisiona una unidad de capacidad.  Puede ajustar el número de unidades de capacidad disponibles para su cuenta de Base de datos de documentos en cualquier momento mediante el [portal de administración de vista previa](https://portal.azure.com/#gallery/Microsoft.DocumentDB). Para obtener más información acerca de la capacidad y el rendimiento de la cuenta de Base de datos de documentos, vea el artículo [Administración de capacidad y rendimiento de Base de datos de documentos][documentdb-manage].

	- En **Grupo de recursos**, seleccione o cree un grupo de recursos para su cuenta de Base de datos de documentos.  De forma predeterminada, se creará un nuevo grupo de recursos.  Puede, sin embargo, optar por seleccionar un grupo de recursos existente al que agregar su cuenta de Base de datos de documentos. Para obtener más información, vea [Uso de los grupos de recursos para administrar los recursos de Azure](http://azure.microsoft.com/documentation/articles/azure-preview-portal-using-resource-groups/).

	- En **Suscripción**, seleccione la suscripción a Azure que quiere usar para la cuenta de Base de datos de documentos. Si la cuenta tiene solo una suscripción, dicha cuenta se seleccionará automáticamente.*
 
	- Use **Ubicación** para especificar la ubicación geográfica en la que se hospedará su cuenta de Base de datos de documentos.   

3.	Una vez que las nuevas opciones de cuenta de Base de datos de documentos estén configuradas, haga clic en **Crear**.  La creación de la cuenta puede tardar unos minutos.  Para comprobar el estado, puede supervisar el progreso en el panel de inicio.  
	![Screen shot of the Creating tile on the Startboard][4]  
  
	O bien, puede supervisar su progreso en el centro de notificaciones.  

	![Screen shot of the Notifications hub, showing that the DocumentDB account is being created][5]  

	![Screen shot of the Notifications hub, showing that the DocumentDB account was created successfully and deplyed to a resource group][6]

4.	Después de crear la cuenta de Base de datos de documentos, ya se puede usar con la configuración predeterminada.

	*Tenga en cuenta que la coherencia predeterminada de la cuenta se establecerá en Sesión.  Puede ajustar la configuración de coherencia predeterminada a través del [portal de administración de vista previa](https://portal.azure.com/#gallery/Microsoft.DocumentDB).*  
	![Screen shot of the Resource Group blade][7]  


<!--Image references-->
[1]: ./media/documentdb-create-dbaccount/ca1.png
[2]: ./media/documentdb-create-dbaccount/ca2.png
[3]: ./media/documentdb-create-dbaccount/ca3.png
[4]: ./media/documentdb-create-dbaccount/ca4.png
[5]: ./media/documentdb-create-dbaccount/ca5.png
[6]: ./media/documentdb-create-dbaccount/ca6.png
[7]: ./media/documentdb-create-dbaccount/ca7.png

[Procedimiento: de una cuenta de Base de datos de documentos]: #Howto
[Pasos siguientes]: #NextSteps
[documentdb-manage]:../documentdb-manage/
<!--HONumber=47-->
