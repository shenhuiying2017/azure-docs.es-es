#### <a name="prerequisites"></a>Requisitos previos
* Una cuenta de Azure; puede crear una [gratuita](https://azure.microsoft.com/free)
* Una cuenta de [Dynamics CRM Online](https://www.microsoft.com/en-us/dynamics/crm-free-trial-overview.aspx). 

Antes de usar su cuenta de Dynamics en una aplicación lógica, autorice a la aplicación lógica a conectarse a su cuenta de CRM Online. Esto lo puede hacer fácilmente dentro de la aplicación lógica en el Portal de Azure. 

Autorice a la aplicación lógica a conectarse a su cuenta de CRM Online con los pasos siguientes:

1. Cree una aplicación lógica. En el diseñador de Logic Apps, seleccione **Mostrar las API administradas por Microsoft** en la lista desplegable y, luego, escriba "dynamics" en el cuadro de búsqueda. Seleccione uno de los desencadenadores o una de las acciones:  
   ![](./media/connectors-create-api-crmonline/dynamics-triggers.png)
2. Si no ha creado previamente las conexiones a Dynamics, se le pedirá que inicie sesión con sus credenciales de Dynamics:  
   ![](./media/connectors-create-api-crmonline/dynamics-signin.png)
3. Seleccione **Iniciar sesión** y escriba su nombre de usuario y contraseña. Seleccione **Iniciar sesión**. 
   
    Estas credenciales se usan para autorizar a la aplicación lógica a conectarse y tener acceso a los datos de su cuenta de Dynamics. 
4. Observe que la conexión se ha creado en el portal. Ahora, continúe con el resto de los pasos en la aplicación lógica:  
   ![](./media/connectors-create-api-crmonline/dynamics-properties.png)

