Ahora que ha agregado un desencadenador, es hora de hacer algo interesante con los datos que genera. Siga estos pasos para agregar la acción **SFTP - extract folder** (SFTP: extraer carpeta). Esta acción extrae el contenido de un archivo si se cumplen las condiciones definidas. 

Para configurar esta acción, deberá proporcionar la siguiente información. Observará que resulta sencillo usar los datos generados por el desencadenador como entrada para algunas de las propiedades del nuevo archivo:

| SFTP - extract folder property (SFTP: extraer propiedad de carpeta) | Descripción |
| --- | --- |
| Ruta de acceso del archivo de origen |Se trata de la ruta de acceso del archivo que se va a extraer. Puede seleccionar uno de los tokens de una acción anterior o examinar el servidor SFTP para encontrar la ruta de acceso del archivo. |
| Ruta de acceso a la carpeta de destino |Se trata de la ruta de acceso donde se colocarán los archivos extraídos. Puede seleccionar uno de los tokens de una acción anterior como ruta de acceso de destino o examinar el servidor SFTP y seleccionar una ruta de acceso. |
| ¿Sobrescribir? |Indica si en caso de que un archivo con el mismo nombre que el archivo extraído se encuentre en la ruta de acceso de la carpeta de destino, el archivo existente se debe sobrescribir. |

Para comenzar, vamos a agregar la acción para extraer los archivos si la condición definida anteriormente se evalúa como *True*. 

1. Seleccione **Add an action**(Agregar una acción).        
   ![Imagen 6 de condición de acción de SFTP](./media/connectors-create-api-sftp/condition-6.png)   
2. Seleccione la acción **SFTP - Extract folder** (SFTP: extraer carpeta).      
   ![Imagen 7 de condición de acción de SFTP](./media/connectors-create-api-sftp/condition-7.png)   
3. Seleccione **Ruta de acceso del archivo de origen**            .  
   ![Imagen 9 de condición de acción de SFTP](./media/connectors-create-api-sftp/condition-9.png)   
4. Seleccione el token **File path** (Ruta de acceso del archivo). Esto indica que se utilizará la ruta de acceso del archivo que encontró el desencadenador como la ruta de acceso del archivo de almacenamiento.           
   ![Imagen 10 de condición de acción de SFTP](./media/connectors-create-api-sftp/condition-10.png)   
5. Seleccione **Ruta de acceso de la carpeta de destino**         .  
   ![Imagen 11 de condición de acción de SFTP](./media/connectors-create-api-sftp/condition-11.png)   
6. Seleccione el token **File path** (Ruta de acceso del archivo). Esto indica que se utilizará la ruta de acceso del archivo que encontró el desencadenador como la ruta de acceso de destino de los archivos extraídos.   
7. Escriba *\ExtractedFile* en el control **Destination folder path** (Ruta de acceso de la carpeta de destino). Haga esto justo después del token File path (Ruta de acceso del archivo) del control Destination folder path (Ruta de acceso de la carpeta de destino).         
   ![Imagen 12 de condición de acción de SFTP](./media/connectors-create-api-sftp/condition-12.png)   
8. Escriba *True* en el control **Overwrite?* (¿Sobrescribir?) para indicar que los archivos existentes se deben sobrescribir si tienen el mismo nombre que lo archivos extraídos.      
   ![Imagen 13 de condición de acción de SFTP](./media/connectors-create-api-sftp/condition-13.png)   
9. Guarde los cambios en el flujo de trabajo.  

