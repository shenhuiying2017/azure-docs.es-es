#### <a name="prerequisites"></a>Requisitos previos
* Una cuenta de Azure; puede crear una [gratuita](https://azure.microsoft.com/free)
* Una cuenta de [OneDrive](https://www.microsoft.com/store/apps/onedrive/9wzdncrfj1p3). 

Para poder usar su cuenta de OneDrive en una aplicación lógica, debe autorizar a la aplicación lógica a conectarse a dicha cuenta.  Esto lo puede hacer fácilmente dentro de la aplicación lógica en el Portal de Azure. 

Autorice a la aplicación lógica a conectarse a su cuenta de OneDrive con los pasos siguientes:

1. Cree una aplicación lógica. En el diseñador de Logic Apps, seleccione **Mostrar API administradas por Microsoft** en la lista desplegable y, luego, escriba "onedrive" en el cuadro de búsqueda. Seleccione uno de los desencadenadores o una de las acciones:  
   ![](./media/connectors-create-api-onedrive/onedrive-1.png)
2. Si no ha creado previamente las conexiones a OneDrive, se le pedirá que inicie sesión con sus credenciales de OneDrive:  
   ![](./media/connectors-create-api-onedrive/onedrive-2.png)
3. Seleccione **Iniciar sesión** y escriba su nombre de usuario y contraseña. Seleccione **Iniciar sesión**:  
   ![](./media/connectors-create-api-onedrive/onedrive-3.png)   
   
    Estas credenciales se usan para autorizar a la aplicación lógica a conectarse y tener acceso a los datos de su cuenta de OneDrive. 
4. Seleccione **Sí** para autorizar a la aplicación lógica a usar la cuenta de OneDrive:  
   ![](./media/connectors-create-api-onedrive/onedrive-4.png)   
5. Observe que la conexión se ha creado en el portal. Ahora, continúe con el resto de los pasos en la aplicación lógica:  
   ![](./media/connectors-create-api-onedrive/onedrive-5.png)

