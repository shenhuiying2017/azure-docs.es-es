### Requisitos previos

- Una cuenta de [SQL Azure](https://www.microsoft.com/sql).  


Antes de poder usar la cuenta de SQL Azure en una aplicación lógica, debe autorizar a la aplicación lógica a conectarse a dicha cuenta. Por suerte, esto se puede hacer fácilmente dentro de la aplicación lógica en el Portal de Azure.

Aquí se explica cómo autorizar a la aplicación lógica para conectarse a su cuenta de SQL Azure:
1. Para crear una conexión a SQL Azure en el diseñador de aplicaciones lógicas, seleccione **Show Microsoft managed APIs** (Mostrar API administradas por Microsoft) en la lista desplegable y, luego, escriba *SQL Azure* en el cuadro de búsqueda. Seleccione el desencadenador o la acción que quiera usar: ![paso de creación de conexión de SQL Azure](./media/connectors-create-api-sql/sql-1.png)  
2. Si no ha creado ninguna conexión a SQL Azure antes, se le pedirá que indique sus credenciales de SQL Azure. Estas credenciales se usarán para autorizar a la aplicación lógica para conectarse y tener acceso a los datos de su cuenta de SQL Azure: ![paso de creación de conexión de SQL Azure](./media/connectors-create-api-sql/sql-2.png)  
3. Observe que la conexión se ha creado y que puede continuar sin problemas con el resto de pasos en la aplicación lógica: ![paso de creación de conexión de SQL Azure](./media/connectors-create-api-sql/sql-3.png)  

<!---HONumber=AcomDC_0525_2016-->