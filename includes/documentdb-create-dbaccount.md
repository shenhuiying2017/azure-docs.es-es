1.	Inicie sesión en el [Portal de vista previa de Microsoft Azure](https://portal.azure.com/) en línea.
2.	En la barra de salto, haga clic en **Nuevo**, en **Datos y almacenamiento** y luego en **Azure DocumentDB**. 

	![Captura de pantalla del Portal de vista previa de Azure en la que está resaltado el botón Nuevo, Datos y almacenamiento en la hoja Crear y Azure DocumentDB en la hoja Datos y almacenamiento][1]   

	<!-- Alternatively, from the Startboard, you can browse the Azure Marketplace, select **Data + storage**, choose **DocumentDB**, and then click **Create**.  -->
	
	<!-- ![Screen shot of the Azure preview portal, showing the Marketplace blade with the DocumentDB tile highlighted, and the DocumentDB blade with the Create database button highlighted][2]    -->
   

3. En la hoja **Nueva cuenta de DocumentDB**, especifique la configuración que desee para la cuenta de DocumentDB. 
 
	![!Screen shot of the New DocumentDB (Preview) blade3][3] 


	- En el cuadro **Id.**, escriba un nombre para identificar la cuenta de DocumentDB. Cuando se valida el **identificador**, aparece una marca de verificación verde en el cuadro **Id.** El valor de **Id.** se convierte en el nombre de host dentro del URI. El **identificador** puede contener solo minúsculas, números y el carácter "-"; debe tener entre 3 y 50 caracteres. Tenga en cuenta que *documents.azure.com* se anexa al nombre de extremo que elija; su resultado se convertirá en el extremo de la cuenta de DocumentDB.

	- El modo **Nivel de cuenta** se bloquea porque DocumentDB admite un único nivel de cuenta Estándar. Para obtener más información, consulte [Precios de DocumentDB](http://go.microsoft.com/fwlink/p/?LinkID=402317&clcid=0x409).

	- En **Grupo de recursos**, seleccione o cree un grupo de recursos para la cuenta de DocumentDB. De forma predeterminada, se creará un nuevo grupo de recursos. Puede, sin embargo, optar por seleccionar un grupo de recursos existente al que agregar su cuenta de DocumentDB. Para obtener más información, consulte [Uso de grupos de recursos para administrar los recursos de Azure](resource-group-portal.md).

	- En **Suscripción**, seleccione la suscripción a Azure que quiere usar para la cuenta de DocumentDB. Si la cuenta tiene una sola suscripción, dicha cuenta se selecciona de manera predeterminada.
 
	- Use **Ubicación** para especificar la ubicación geográfica en la que se hospedará la cuenta de DocumentDB.   

4.	Una vez que las nuevas opciones de cuenta de DocumentDB estén configuradas, haga clic en **Crear**. La creación de la cuenta puede tardar unos minutos. Para comprobar el estado, puede supervisar el progreso en el Panel de inicio.  
	![Captura de pantalla del icono de creación en el Panel de inicio - creador de bases de datos en línea][4]  
  
	O bien, puede supervisar su progreso en el centro de notificaciones.  

	![Crear bases de datos rápidamente - Captura de pantalla del Centro de notificaciones en la que se muestra que se está creando la cuenta de DocumentDB - Notificación del creador de bases de datos en línea][5]  

	![!Screen shot of the Notifications hub, showing that the DocumentDB account was created successfully and deplyed to a resource group6][6]

5.	Después de crear la cuenta de DocumentDB, ya se puede usar con la configuración predeterminada en el portal en línea. Tenga en cuenta que la coherencia predeterminada de la cuenta de DocumentDB está establecida en **Sesión**. Puede ajustar la configuración de coherencia predeterminada haciendo clic en el icono **Coherencia predeterminada** en la hoja **Cuenta de DocumentDB**.

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

<!----HONumber=July15_HO4-->