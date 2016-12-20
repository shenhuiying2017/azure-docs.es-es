### <a name="prerequisites"></a>Requisitos previos
Debe tener una cuenta de [Service Bus](https://azure.microsoft.com/services/service-bus/).  

Para poder usar su cuenta de Bus de servicio de Azure en una aplicación lógica, debe autorizar a la aplicación lógica a conectarse a dicha cuenta. Por suerte, esto es muy fácil de hacer desde la aplicación lógica del Portal de Azure.  

Aquí se explica cómo autorizar a la aplicación lógica a conectarse a su cuenta de Bus de servicio:  

1. Para crear una conexión a Service Bus, en el diseñador de aplicaciones lógicas, seleccione **Mostrar las API administradas por Microsoft** en la lista desplegable. Después, escriba **service bus** en el cuadro de búsqueda. Seleccione el desencadenador o la acción que quiera usar.  
    ![Imagen 1 de conexión a Service Bus](./media/connectors-create-api-servicebus/servicebus-1.png)  
2. Si no ha creado ninguna conexión antes a Bus de servicio, se le pedirá que indique sus credenciales del Bus de servicio. Estas se usarán para autorizar a la aplicación lógica a conectarse y acceder a los datos de su cuenta de Bus de servicio. El conector de Bus de servicio necesita la cadena de conexión del espacio de nombres de Bus de servicio y también, permisos de **administración**. Una buena forma de saber si la cadena de conexión es del espacio de nombres o de una entidad específica consiste en ver si contiene el parámetro `EntityPath`. En caso afirmativo, no se trata de la cadena de conexión adecuada para una aplicación lógica.  
    ![Cadena de conexión de Service Bus](./media/connectors-create-api-servicebus/connectionstring.png)
3. Cuando haya recibido la cadena de conexión del espacio de nombres, puede usarla para la conexión de API de Logic Apps.  
    ![Imagen 2 de conexión a Bus de servicio](./media/connectors-create-api-servicebus/servicebus-2.png)  
4. Observe que la conexión se ha creado y que puede continuar sin problemas con el resto de pasos en la aplicación lógica.  
    ![Imagen 3 de conexión a Bus de servicio](./media/connectors-create-api-servicebus/servicebus-3.png)   



<!--HONumber=Nov16_HO3-->


