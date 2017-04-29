### <a name="prerequisites"></a>Requisitos previos
* Creación de una cuenta de [SendGrid](https://www.SendGrid.com/) 

Para poder usar su cuenta de SendGrid en una aplicación lógica, debe autorizar a la aplicación lógica para que pueda conectarse a dicha cuenta. Por suerte, esto es muy fácil de hacer desde la aplicación lógica en el Portal de Azure. 

Aquí se explica cómo autorizar a la aplicación lógica para conectarse a su cuenta de SendGrid:

1. Para crear una conexión a SendGrid, en el diseñador de aplicaciones lógicas, seleccione **Mostrar las API administradas por Microsoft** en la lista desplegable y, luego, escriba *SendGrid* en el cuadro de búsqueda. Seleccione el desencadenador o la acción que quiera usar:   
   ![SendGrid, paso 1](./media/connectors-create-api-sendgrid/sendgrid-1.png)
2. Si no ha creado ninguna conexión a SendGrid antes, se le pedirá que indique sus credenciales de SendGrid. Estas credenciales se usarán para autorizar a la aplicación lógica para conectarse y tener acceso a los datos de su cuenta de SendGrid:  
   ![SendGrid, paso 2](./media/connectors-create-api-sendgrid/sendgrid-2.png)
3. Observe que la conexión se ha creado y que puede continuar sin problemas con el resto de pasos en la aplicación lógica:   
   ![SendGrid, paso 3](./media/connectors-create-api-sendgrid/sendgrid-3.png)   

