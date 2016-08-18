Vamos a agregar un desencadenador.

1. Escriba *sftp* en el cuadro de búsqueda del diseñador de Logic Apps y seleccione el desencadenador **SFTP - When a file is added or modified** (SFTP: cuando se agrega o modifica un archivo).  
![Imagen 1 de desencadenador de SFTP](./media/connectors-create-api-sftp/trigger-1.png)  
- Se abre el control **When a file is added or modified** (Cuando se agrega o modifica un archivo).  
![Imagen 2 de desencadenador de SFTP](./media/connectors-create-api-sftp/trigger-2.png)  
- Seleccione los puntos suspensivos **...** situados a la derecha del control. Se abrirá el control de selector de carpeta.  
![Imagen 3 de desencadenador de SFTP](./media/connectors-create-api-sftp/action-1.png)  
- Seleccione **SFTP** para seleccionar la carpeta raíz como la carpeta para supervisar los archivos nuevos o modificados. Observe que la carpeta raíz se muestra ahora en el control **Folder** (Carpeta).  
![Imagen 4 de desencadenador de SFTP](./media/connectors-create-api-sftp/action-2.png)  

En este punto, la aplicación lógica se ha configurado con un desencadenador que activará otros desencadenadores y acciones del flujo de trabajo cuando se cree o modifique un archivo en la carpeta SFTP especificada.

>[AZURE.NOTE]Para que una aplicación lógica sea funcional, debe contener al menos un desencadenador y una acción. Siga los pasos que se describen en la sección siguiente para agregar una acción.

<!---HONumber=AcomDC_0727_2016-->
