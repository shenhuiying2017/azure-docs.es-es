### Requisitos previos

- Una cuenta de [ServiceBus](https://azure.microsoft.com/services/service-bus/).  


Antes de poder usar la cuenta de ServiceBus en una aplicación lógica, debe autorizar a la aplicación lógica a conectarse a dicha cuenta. Por suerte, esto se puede hacer fácilmente dentro de la aplicación lógica en el Portal de Azure.

Aquí se explica cómo autorizar a la aplicación lógica para conectarse a su cuenta de ServiceBus:
1. Para crear una conexión a ServiceBus, en el diseñador de aplicaciones lógicas, seleccione **Show Microsoft managed APIs** (Mostrar API administradas por Microsoft) en la lista desplegable y luego escriba *ServiceBus* en el cuadro de búsqueda. Seleccione el desencadenador o la acción que desea utilizar: ![](./media/connectors-create-api-servicebus/servicebus-1.png)  
2. Si no ha creado ninguna conexión a ServiceBus antes, se le pedirá que indique sus credenciales de ServiceBus. Estas credenciales se usarán para autorizar a la aplicación lógica para conectarse y tener acceso a los datos de su cuenta de ServiceBus: ![](./media/connectors-create-api-servicebus/servicebus-2.png)  
3. Observe que la conexión se ha creado y que puede continuar sin problemas con el resto de pasos en la aplicación lógica: ![](./media/connectors-create-api-servicebus/servicebus-3.png)   

<!---HONumber=AcomDC_0525_2016-->