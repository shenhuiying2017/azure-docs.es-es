En este ejemplo, le mostraremos cómo usar el desencadenador **SharePoint Online - When a new item is created** (SharePoint Online: cuando se crea un nuevo elemento) para iniciar un flujo de trabajo de aplicación lógica cuando se cree un nuevo elemento en una lista de SharePoint Online.

> [!NOTE]
> Si aún no ha creado una *conexión* a SharePoint Online, se le pedirá que inicie sesión en su cuenta de SharePoint.  
> 
> 

1. Escriba *sharepoint* en el cuadro de búsqueda del diseñador de Logic Apps y seleccione el desencadenador **SharePoint Online - When a new item is created** (SharePoint Online: cuando se crea un nuevo elemento).  
   ![Imagen del desencadenador de SharePoint Online](./media/connectors-create-api-sharepointonline/trigger-1.png)  
2. Se muestra el control **When a new item is created** (Cuando se crea un nuevo elemento).  
   ![Imagen 2 de desencadenador de SharePoint Online](./media/connectors-create-api-sharepointonline/trigger-2.png)   
3. Seleccione una **dirección URL del sitio**. Es el sitio de SharePoint Online que quiere supervisar para ver los nuevos elementos que desencadenarán el flujo de trabajo.  
   ![Imagen 3 de desencadenador de SharePoint Online](./media/connectors-create-api-sharepointonline/trigger-3.png)   
4. Seleccione un **nombre de la lista**. Es la lista del sitio de SharePoint Online que quiere supervisar para ver los nuevos elementos que desencadenarán el flujo de trabajo.  
   ![Imagen 4 de desencadenador de SharePoint Online](./media/connectors-create-api-sharepointonline/trigger-4.png)   

En este punto, la aplicación lógica se ha configurado con un desencadenador que iniciará una ejecución de los otros desencadenadores y acciones en el flujo de trabajo. Esto tendrá lugar cada vez que se cree un nuevo elemento en la lista de SharePoint Online seleccionada.  

