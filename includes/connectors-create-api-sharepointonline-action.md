Ahora que ha agregado un desencadenador, es hora de hacer algo interesante con los datos que genera. Siga estos pasos para agregar la acción **SharePoint Online - Create file** (SharePoint Online: crear archivo). Esta acción crea un archivo en SharePoint Online cada vez que se activa el nuevo desencadenador de elementos.

Para configurar esta acción, deberá proporcionar la siguiente información. Cuando proporcione esta información, observará que resulta sencillo usar los datos generados por el desencadenador como entrada para algunas de las propiedades del nuevo archivo:

|Propiedad Crear archivo|Descripción|
|---|---|
|Dirección URL del sitio|Se trata de la dirección URL del sitio de SharePoint Online donde desea crear el nuevo archivo. Seleccione el sitio de la lista presentada.|
|Ruta de acceso a la carpeta|Se trata de la carpeta (en la dirección URL del sitio seleccionado en el paso anterior) donde se colocará el nuevo archivo. Busque y seleccione la carpeta.|
|Nombre de archivo|Es el nombre del archivo que se va a crear.|
|Contenido del archivo|El contenido que se escribirá en el archivo.|

1. Seleccione **+ New step** (+ Nuevo paso) para agregar la acción.  
![Imagen 1 de acción de SharePoint Online](./media/connectors-create-api-sharepointonline/action-1.png)  
- Seleccione el vínculo **Add an action** (Agregar una acción). Se abrirá el cuadro de búsqueda en el que podrá buscar cualquier acción que quiera realizar. En este ejemplo, nos interesan las acciones de SharePoint.  
![Imagen 2 de acción de SharePoint Online](./media/connectors-create-api-sharepointonline/action-2.png)  
- Escriba *sharepoint* para buscar las acciones relacionadas con SharePoint.
- Seleccione **SharePoint Online - Create file** (SharePoint Online: crear archivo). Esta es la acción que se realizará. **Nota**: Si no ha creado una conexión a SharePoint anteriormente, se le pedirá que autorice a la aplicación lógica el acceso a su cuenta de SharePoint.  
![Imagen 3 de acción de SharePoint Online](./media/connectors-create-api-sharepointonline/action-3.png)  
- Se abre el control **Create file** (Crear archivo).  
![Imagen 4 de acción de SharePoint Online](./media/connectors-create-api-sharepointonline/action-4.png)  
- Seleccione la **dirección URL del sitio** y busque el sitio donde desea crear el archivo.  
![Imagen 5 de acción de SharePoint Online](./media/connectors-create-api-sharepointonline/action-5.png)  
- Seleccione **Folder path** (Ruta de acceso de la carpeta) y busque la carpeta donde se colocará el nuevo archivo.  
![Imagen 6 de acción de SharePoint Online](./media/connectors-create-api-sharepointonline/action-6.png)  
- Seleccione el control **File name** (Nombre de archivo) y escriba el nombre del archivo que quiere crear. Aquí, puede escribir el nombre de archivo directamente o usar cualquiera de las propiedades del desencadenador que creó anteriormente. Para ello, seleccione propiedades en la lista de **salidas de cuando se crea un nuevo elemento**. Esta lista es la única pantalla después de seleccionar el control **File name** (Nombre de archivo). En este tutorial, hemos seleccionado ID (Id.) (el id. del nuevo elemento de la lista) como el nombre del archivo que se crea mediante la acción **SharePoint Online - Create file** (SharePoint Online: crear archivo).  
![Imagen 7 de acción de SharePoint Online](./media/connectors-create-api-sharepointonline/action-7.png)  
- Seleccione el control **File content** (Contenido del archivo) y escriba el contenido que se escribirá en el archivo que se creará. Para el contenido del archivo, tenga en cuenta que puede usar cualquiera de las propiedades del desencadenador que creó anteriormente. Simplemente seleccione las propiedades de la lista presentada. También puede escribir el texto de **File content** (Contenido del archivo) directamente en el control. En este ejemplo, se han seleccionado algunas propiedades y se han agregado espacios y un guión entre cada propiedad.  
![Imagen 8 de acción de SharePoint Online](./media/connectors-create-api-sharepointonline/action-8.png)  
- Guarde los cambios en el flujo de trabajo.  
- Enhorabuena, ahora tiene una aplicación lógica totalmente funcional que se desencadena cuando se agrega un nuevo elemento a una lista de SharePoint Online. La aplicación crea entonces un archivo, con algunas de las propiedades del nuevo elemento de la lista. Ahora puede probarla mediante la creación de un nuevo elemento en la lista de SharePoint. 

<!---HONumber=AcomDC_0727_2016-->
