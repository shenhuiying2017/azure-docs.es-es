En este ejemplo, le mostraremos cómo usar el desencadenador **Service Bus - When a message is received in a queue** (Bus de servicio: cuando se recibe un mensaje en una cola) para iniciar un flujo de trabajo de aplicación lógica cuando se reciba un nuevo elemento en una cola del Bus de servicio.

>[AZURE.NOTE]Si aún no ha creado una *conexión* al Bus de servicio, se le pedirá que inicie sesión con la cadena de conexión del Bus de servicio.

1. Escriba *bus de servicio* en el cuadro de búsqueda del diseñador de Logic Apps y luego seleccione el desencadenador **Service Bus - When a message is received in a queue** (Bus de servicio: cuando se recibe un mensaje en una cola).  
![Imagen 1 de desencadenador del Bus de servicio](./media/connectors-create-api-servicebus/trigger-1.png)   
- Se muestra el control **When a message is received in a queue** (Cuando se recibe un mensaje en una cola).  
![Imagen 2 de desencadenador del Bus de servicio](./media/connectors-create-api-servicebus/trigger-2.png)   
- Escriba el nombre de la cola del Bus de servicio que quiere que supervise el desencadenador.  
![Imagen 3 de desencadenador del Bus de servicio](./media/connectors-create-api-servicebus/trigger-3.png)   

En este punto, la aplicación lógica está configurada con un desencadenador que activará otros desencadenadores y acciones del flujo de trabajo cuando se reciba un nuevo elemento en la cola que ha seleccionado.

<!---HONumber=AcomDC_0727_2016-->
