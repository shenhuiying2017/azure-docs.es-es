Puede crear las colas de almacenamiento de Azure mediante el **Explorador de servidores**de Visual Studio.

![Blobs del explorador de servidores][Image1]

1. En el menú **Ver**, elija **Explorador de servidores**.
2. En el Explorador de servidores, expanda el nodo **Azure** de la suscripción, luego el nodo **Storage** y, por último, el nodo de la cuenta de almacenamiento que especificó en el servicio conectado Azure Storage.
3. Seleccione el nodo **Colas** y elija **Crear cola** en el menú contextual.
4. Escriba un nombre para el contenedor y elija **Aceptar**.   

De manera predeterminada, el nuevo contenedor es privado y debe especificar su clave de acceso de almacenamiento para descargar blobs de él. Si desea que los archivos del contenedor sean públicos, seleccione el contenedor en el **Explorador de servidores** y presione `F4` para mostrar la ventana **Propiedades**. En **Acceso de lectura público**, seleccione **Blob**. Cualquier usuario de Internet puede ver los blobs de los contenedores públicos, pero solo es posible modificarlos o eliminarlos si se dispone de la clave de acceso apropiada.

[Image1]: ./media/vs-create-blob-container-in-server-explorer/vs-storage-create-blob-containers-in-Server-Explorer.png

<!--HONumber=Jan17_HO3-->


