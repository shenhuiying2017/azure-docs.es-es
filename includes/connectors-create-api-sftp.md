### Requisitos previos

- Una cuenta de [SFTP](https://en.wikipedia.org/wiki/SSH_File_Transfer_Protocol).  


Antes de poder usar la cuenta de SFTP en una aplicación lógica, debe autorizar a la aplicación lógica a conectarse a dicha cuenta. Por suerte, esto se puede hacer fácilmente dentro de la aplicación lógica en el Portal de Azure.

Aquí se explica cómo autorizar a la aplicación lógica para conectarse a su cuenta de SFTP:
1. Para crear una conexión a SFTP, en el diseñador de aplicaciones lógicas, seleccione **Show Microsoft managed APIs** (Mostrar API administradas por Microsoft) en la lista desplegable y, luego, escriba *SFTP* en el cuadro de búsqueda. Seleccione el desencadenador o la acción que quiera usar: ![](./media/connectors-create-api-sftp/sftp-1.png)  
2. Si no ha creado ninguna conexión a SFTP antes, se le pedirá que indique sus credenciales de SFTP. Estas credenciales se usarán para autorizar a la aplicación lógica para conectarse y tener acceso a los datos de su cuenta de SFTP: ![](./media/connectors-create-api-sftp/sftp-2.png)  
3. Observe que la conexión se ha creado y que puede continuar sin problemas con el resto de pasos en la aplicación lógica: ![](./media/connectors-create-api-sftp/sftp-3.png)  

<!---HONumber=AcomDC_0525_2016-->