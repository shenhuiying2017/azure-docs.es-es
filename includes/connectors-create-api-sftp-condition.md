1. Seleccione **+ New step** (+ Nuevo paso) para agregar la acción.
2. Seleccione el vínculo **Add an action** (Agregar una acción). Se abrirá el cuadro de búsqueda en el que podrá buscar cualquier acción que quiera realizar. En este ejemplo, nos interesan las acciones de SharePoint.  
   ![Imagen 1 de condición de SFTP](./media/connectors-create-api-sftp/condition-1.png)  
3. Seleccione **Choose a value** (Elegir un valor).  
   ![Imagen 2 de condición de SFTP](./media/connectors-create-api-sftp/condition-2.png)  
4. Seleccione **File content** (Contenido del archivo) para indicar que va a evaluar el contenido del archivo en la condición.  
   ![Imagen 3 de condición de SFTP](./media/connectors-create-api-sftp/condition-3.png)  
5. Seleccione *contains* en la lista de operadores.  
   ![Imagen 4 de condición de SFTP](./media/connectors-create-api-sftp/condition-4.png)  
6. Seleccione **Choose a value** (Elegir un valor) a la derecha y escriba *ExtractMeFirst*. En este ejemplo, ExtractMeFirst es un valor que se espera que esté en un archivo para las personas que tienen acceso a la carpeta SFTP para indicar que se trata de un archivo de almacenamiento que se debe extraer.  
   ![Imagen 5 de condición de SFTP](./media/connectors-create-api-sftp/condition-5.png)  

<!---HONumber=AcomDC_0727_2016-->
