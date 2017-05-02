### <a name="prerequisites"></a>Requisitos previos
* Una cuenta de [MailChimp](https://www.MailChimp.com/). 

Para poder usar su cuenta de MailChimp en una aplicación lógica, debe autorizar a la aplicación lógica para que pueda conectarse a dicha cuenta. Por suerte, esto es muy fácil de hacer desde la aplicación lógica en el Portal de Azure. 

Aquí se explica cómo autorizar a la aplicación lógica para conectarse a su cuenta de MailChimp:

1. Para crear una conexión a MailChimp, en el diseñador de Logic Apps, seleccione **Mostrar API administradas por Microsoft** en la lista desplegable y, luego, escriba *MailChimp* en el cuadro de búsqueda. Seleccione el desencadenador o la acción que quiera usar:   
   ![MailChimp, paso 1](./media/connectors-create-api-mailchimp/mailchimp-1.png)
2. Si no ha creado ninguna conexión a MailChimp antes, se le pedirá que indique sus credenciales de MailChimp. Estas credenciales se usarán para autorizar a la aplicación lógica a conectarse y acceder a los datos de su cuenta de MailChimp:  
   ![MailChimp, paso 2](./media/connectors-create-api-mailchimp/mailchimp-2.png)
3. Indique su nombre de usuario y contraseña de MailChimp para autorizar a la aplicación lógica:  
   ![MailChimp, paso 3](./media/connectors-create-api-mailchimp/mailchimp-3.png)   
4. Observe que la conexión se ha creado y que puede continuar sin problemas con el resto de pasos en la aplicación lógica:   
   ![MailChimp, paso 4](./media/connectors-create-api-mailchimp/mailchimp-4.png)

