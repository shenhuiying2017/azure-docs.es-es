1.	Inicie sesión en el [Portal de vista previa de Microsoft Azure](https://portal.azure.com/).
2.	En la barra de salto, haga clic en **Nuevo**, seleccione **Datos y almacenamiento** y, a continuación, haga clic en **DocumentDB**. 

	![!Screen shot of the Azure Preview portal, highlighting the New button, Data + storage in the Create blade, and DocumentDB in the Data + storage blade1][1]

	<!-- Alternatively, from the Startboard, you can browse the Azure Marketplace, select **Data + storage**, choose **DocumentDB**, and then click **Create**.  --><!-- ![Screen shot of the Azure Preview portal, showing the Marketplace blade with the DocumentDB tile highlighted, and the DocumentDB blade with the Create button highlighted][2]    -->
   

3. En la hoja **Nueva DocumentDB**, especifique la configuración que desee para la cuenta de DocumentDB.
 
	![!Screen shot of the New DocumentDB (Preview) blade3][3]


	- En el cuadro **Id.**, escriba un nombre para identificar la cuenta de DocumentDB. Este valor se convierte en el nombre de host dentro del URI. El **id.** puede contener solo letras en minúsculas, números y el carácter “-” y debe tener entre 3 y 50 caracteres. Tenga en cuenta que *documents.azure.com* se anexa al nombre de extremo que elija; su resultado se convertirá en el extremo de la cuenta de DocumentDB.

	- El modo **Nivel de cuenta** se bloquea porque DocumentDB admite un único nivel de cuenta estándar. Para obtener más información, consulte [Precios de DocumentDB](http://go.microsoft.com/fwlink/p/?LinkID=402317&clcid=0x409).

	- En **Grupo de recursos**, seleccione o cree un grupo de recursos para la cuenta de DocumentDB. De forma predeterminada, se creará un nuevo grupo de recursos. Puede, sin embargo, optar por seleccionar un grupo de recursos existente al que agregar su cuenta de DocumentDB. Para obtener más información, consulte [Uso de grupos de recursos para administrar los recursos de Azure](resource-group-portal.md).

	- En **Suscripción**, seleccione la suscripción a Azure que quiere usar para la cuenta de DocumentDB. Si la cuenta tiene solo una suscripción, dicha cuenta se seleccionará automáticamente.
 
	- Use **Ubicación** para especificar la ubicación geográfica en la que se hospedará la cuenta de DocumentDB.

4.	Una vez que las nuevas opciones de cuenta de DocumentDB estén configuradas, haga clic en **Crear**. La creación de la cuenta puede tardar unos minutos. Para comprobar el estado, puede supervisar el progreso en el Panel de inicio. ![!Screen shot of the Creating tile on the Startboard4][4]
  
	O bien, puede supervisar su progreso en el centro de notificaciones.

	![!Screen shot of the Notifications hub, showing that the DocumentDB account is being created5][5]

	![!Screen shot of the Notifications hub, showing that the DocumentDB account was created successfully and deplyed to a resource group6][6]

5.	Después de crear la cuenta de DocumentDB, ya se puede usar con la configuración predeterminada. Tenga en cuenta que la coherencia predeterminada de la cuenta de DocumentDB está establecida en Sesión. Puede cambiar la configuración de la coherencia predeterminada en el [Portal de vista previa de Azure](https://portal.azure.com/#gallery/Microsoft.DocumentDB).

    ![!Screen shot of the Resource Group blade7][7]

<!--Image references-->
[1]: media/documentdb-create-dbaccount/ca1.png
[2]: media/documentdb-create-dbaccount/ca2.png
[3]: media/documentdb-create-dbaccount/ca3.png
[4]: media/documentdb-create-dbaccount/ca4.png
[5]: media/documentdb-create-dbaccount/ca5.png
[6]: media/documentdb-create-dbaccount/ca6.png
[7]: media/documentdb-create-dbaccount/ca7.png

[How to: Create a DocumentDB account]: #Howto
[Next steps]: #NextSteps
[documentdb-manage]: ../articles/documentdb/documentdb-manage.md

<!---HONumber=62-->