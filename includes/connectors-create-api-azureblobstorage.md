### Requisitos previos

- Una cuenta de [Almacenamiento de blobs de Azure](https://azure.microsoft.com/documentation/services/storage/).  


Antes de poder usar la cuenta de Almacenamiento de blobs de Azure en una aplicación lógica, debe autorizar a la aplicación lógica a conectarse a dicha cuenta. Por suerte, esto se puede hacer fácilmente dentro de la aplicación lógica en el Portal de Azure.

Aquí se explica cómo autorizar a la aplicación lógica para conectarse a su cuenta de Almacenamiento de blobs de Azure:
1. Para crear una conexión a Almacenamiento de blobs de Azure, en el diseñador de aplicaciones lógicas, seleccione **Show Microsoft managed APIs** (Mostrar API administradas por Microsoft) en la lista desplegable y, luego, escriba *Almacenamiento de blobs de Azure* en el cuadro de búsqueda. Seleccione el desencadenador o la acción que quiera usar: ![paso de creación de la conexión de Almacenamiento de blobs de Azure](./media/connectors-create-api-azureblobstorage/azureblobstorage-1.png)  
2. Si no ha creado ninguna conexión al Almacenamiento de blobs de Azure antes, se le pedirá que proporcione sus credenciales de Almacenamiento de blobs de Azure. Estas credenciales se usarán para autorizar a la aplicación lógica para conectarse y tener acceso a los datos de su cuenta de Almacenamiento de blobs de Azure: ![paso de creación de la conexión de Almacenamiento de blobs de Azure](./media/connectors-create-api-azureblobstorage/azureblobstorage-2.png)  
3. Observe que la conexión se ha creado y que puede continuar sin problemas con el resto de pasos en la aplicación lógica: ![paso de creación de la conexión de Almacenamiento de blobs de Azure](./media/connectors-create-api-azureblobstorage/azureblobstorage-3.png)  

<!---HONumber=AcomDC_0525_2016-->