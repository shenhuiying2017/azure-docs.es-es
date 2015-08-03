Puede crear contenedores de blobs mediante **Explorador de servidores** Visual Studio.

![Blobs del explorador de servidores][Image1]

1. En el menú **Ver**, elija **Explorador de servidores**.
2. En el Explorador de servidores, expanda el nodo **Azure** de su suscripción, el nodo **Almacenamiento** y el nodo de la cuenta de almacenamiento que especificó en el servicio conectado al Almacenamiento de Azure.
3. Seleccione el nodo **Blobs** y elija **Crear contenedor de blobs** en el menú contextual.
4. Escriba un nombre para el contenedor y elija **Aceptar**.   

De manera predeterminada, el nuevo contenedor es privado y debe especificar su clave de acceso de almacenamiento para descargar blobs de él. Si desea hacer públicos los archivos del contenedor, seleccione el contenedor en **Explorador de servidores** y presione `F4` para mostrar la ventana **Propiedades**. Establezca el **Acceso de lectura público** en **Blob**. Cualquier usuario de Internet puede ver los blobs de los contenedores públicos, pero solo es posible modificarlos o eliminarlos si se dispone de la clave de acceso apropiada.


[Image1]: ./media/vs-storage-getting-started-blobs-include/vs-storage-create-blob-containers-in-Server-Explorer.png

<!---HONumber=July15_HO4-->