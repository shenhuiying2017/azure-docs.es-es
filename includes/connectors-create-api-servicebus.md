### Requisitos previos
Debe tener una cuenta de [Bus de servicio](https://azure.microsoft.com/services/service-bus/).

Para poder usar su cuenta de Bus de servicio de Azure en una aplicación lógica, debe autorizar a la aplicación lógica a conectarse a dicha cuenta. Por suerte, esto es muy fácil de hacer desde la aplicación lógica del Portal de Azure.

Aquí se explica cómo autorizar a la aplicación lógica a conectarse a su cuenta de Bus de servicio:

1. Para crear una conexión al Bus de servicio, en el diseñador de Logic Apps, seleccione **Mostrar API administradas por Microsoft** en la lista desplegable y luego escriba *bus de servicio* en el cuadro de búsqueda. Seleccione el desencadenador o la acción que desea utilizar:  
    ![Imagen 1 de conexión al Bus de servicio](./media/connectors-create-api-servicebus/servicebus-1.png)
2. Si no ha creado ninguna conexión al Bus de servicio antes, se le pedirá que indique sus credenciales del Bus de servicio. Estas credenciales se usarán para autorizar a la aplicación lógica a conectarse y acceder a los datos de su cuenta del Bus de servicio. El conector del Bus de servicio necesita la cadena de conexión del espacio de nombres del Bus de servicio y también requiere permisos de **administración**. Una buena forma de saber si la cadena de conexión es del espacio de nombres o de una entidad específica es ver si contiene el parámetro `EntityPath`. En caso afirmativo, no es la cadena de conexión adecuada para una aplicación lógica.  
    ![Cadena de conexión al Bus de servicio](./media/connectors-create-api-servicebus/connectionstring.png)
3. Cuando haya recibido la cadena de conexión del espacio de nombres, puede usarla para la conexión de API de Logic Apps.  
    ![Imagen 2 de conexión al Bus de servicio](./media/connectors-create-api-servicebus/servicebus-2.png)
4. Observe que la conexión se ha creado y que puede continuar sin problemas con el resto de los pasos en la aplicación lógica:  
    ![Imagen 3 de conexión al Bus de servicio](./media/connectors-create-api-servicebus/servicebus-3.png)

<!---HONumber=AcomDC_0810_2016-->