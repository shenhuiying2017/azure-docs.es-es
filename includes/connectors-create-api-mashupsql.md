### Requisitos previos
- Acceso a una base de datos de SQL Server

Para poder usar una cuenta de Mashup SQL en una aplicación lógica, debe autorizar a la aplicación lógica para que pueda conectarse a dicha cuenta. Por suerte, esto es muy fácil de hacer desde la aplicación lógica en el Portal de Azure.

Aquí se explica cómo autorizar a la aplicación lógica para conectarse a su cuenta de Mashup SQL:

1. Para crear una conexión a Mashup SQL, en el diseñador de aplicaciones lógicas, seleccione **Show Microsoft managed APIs** (Mostrar API administradas por Microsoft) en la lista desplegable y, luego, escriba *Mashup SQL* en el cuadro de búsqueda. Seleccione el desencadenador o la acción que quiera usar: ![Mashup SQL, paso 1](./media/connectors-create-api-mashupsql/mashupsql-1.png)
2. Si no ha creado ninguna conexión a Mashup SQL antes, se le pedirá que indique sus credenciales de Mashup SQL. Estas credenciales se usarán para autorizar a la aplicación lógica para conectarse y tener acceso a los datos de su cuenta de Mashup SQL: ![Mashup SQL, paso 2](./media/connectors-create-api-mashupsql/mashupsql-2.png)
5. Observe que la conexión se ha creado y que puede continuar sin problemas con el resto de pasos en la aplicación lógica: ![Mashup SQL, paso 3](./media/connectors-create-api-mashupsql/mashupsql-3.png)