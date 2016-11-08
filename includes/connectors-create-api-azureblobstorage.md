### Requisitos previos
* Una cuenta de Azure; puede crear una [gratuita](https://azure.microsoft.com/free)
* Una [cuenta de Almacenamiento de blobs de Azure](../articles/storage/storage-create-storage-account.md) que incluya el nombre de la cuenta de almacenamiento y su clave de acceso. Esta información aparece en las propiedades de la cuenta de almacenamiento en el Portal de Azure. Más información sobre [Almacenamiento de Azure](../articles/storage/storage-introduction.md).

Antes de usar la cuenta de Almacenamiento de blobs de Azure en una aplicación lógica, conéctese a la cuenta de Almacenamiento de blobs de Azure. Esto lo puede hacer fácilmente dentro de la aplicación lógica en el Portal de Azure.

Para conectarse a la cuenta de Almacenamiento de blobs de Azure, siga estos pasos:

1. Cree una aplicación lógica. En el diseñador de Logic Apps, agregue un desencadenador y luego agregue una acción. Seleccione **Mostrar las API administradas por Microsoft** en la lista desplegable y luego escriba "blob" en el cuadro de búsqueda. Seleccione una de las acciones:
   
    ![paso de creación de la conexión de Almacenamiento de blobs de Azure](./media/connectors-create-api-azureblobstorage/azureblobstorage-1.png)
2. Si no ha creado anteriormente ninguna conexión al Almacenamiento de Azure, se le pedirán los detalles de conexión:
   
    ![paso de creación de la conexión de Almacenamiento de blobs de Azure](./media/connectors-create-api-azureblobstorage/connection-details.png)
3. Escriba los detalles de la cuenta de almacenamiento. Aquellas propiedades con un asterisco son obligatorias.
   
   | Propiedad | Detalles |
   | --- | --- |
   | Nombre de la conexión * |Escriba cualquier nombre para la conexión. |
   | Nombre de la cuenta de Almacenamiento de Azure * |Escriba el nombre de la cuenta de almacenamiento. El nombre de la cuenta de almacenamiento se muestra en las propiedades de almacenamiento del Portal de Azure. |
   | Clave de acceso de la cuenta de Almacenamiento de Azure * |Escriba la clave de la cuenta de almacenamiento. Las claves de acceso se muestran en las propiedades de almacenamiento del Portal de Azure. |
   
    Estas credenciales se usan para autorizar a la aplicación lógica a conectarse y acceder a sus datos.
4. Seleccione **Crear**.
5. Observe que la conexión se ha creado en el portal. Ahora, continúe con el resto de los pasos en la aplicación lógica:
   
    ![paso de creación de la conexión de Almacenamiento de blobs de Azure](./media/connectors-create-api-azureblobstorage/azureblobstorage-3.png)

<!---HONumber=AcomDC_0727_2016-->