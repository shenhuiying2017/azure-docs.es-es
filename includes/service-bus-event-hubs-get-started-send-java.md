## Envío de mensajes a Centros de eventos

La biblioteca de cliente de Java para Centros de eventos está disponible para su uso en proyectos de Maven en el [repositorio central de Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22), y se puede hacer referencia a ella mediante la siguiente declaración de dependencia en el archivo de proyecto de Maven:

``` XML
<dependency> 
    <groupId>com.microsoft.azure</groupId> 
    <artifactId>azure-eventhubs-clients</artifactId> 
    <version>0.6.0</version> 
</dependency>   
 ```
 
Para los diferentes tipos de entornos de compilación, puede obtener explícitamente los archivos JAR publicados más recientemente en el [repositorio central de Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22) o en el [punto de distribución de versiones en GitHub](https://github.com/Azure/azure-event-hubs/releases).

Para un editor de eventos simples, importe el paquete *com.microsoft.azure.eventhubs* para las clases de cliente de los Centros de eventos y el paquete *com.microsoft.azure.servicebus* para las clases de utilidad, como las excepciones comunes que se comparten con el cliente de mensajería de Bus de servicio de Azure.

Para el ejemplo siguiente, primero cree un nuevo proyecto de Maven para una aplicación de consola o shell en su entorno de desarrollo de Java favorito. La clase se llamará ```Send```.

``` Java

import java.io.IOException;
import java.nio.charset.*;
import java.util.*;
import java.util.concurrent.ExecutionException;

import com.microsoft.azure.eventhubs.*;
import com.microsoft.azure.servicebus.*;

public class Send
{
	public static void main(String[] args) 
			throws ServiceBusException, ExecutionException, InterruptedException, IOException
	{
```

Reemplace el nombre del espacio de nombres y del Centro de eventos por los valores que usó al crear el Centro de eventos. `sasKeyName` y `sasKey` se corresponden con el nombre y la clave de la regla Send que creó anteriormente. Con esa información, cree una cadena de conexión.

``` Java
	final String namespaceName = "----ServiceBusNamespaceName-----";
	final String eventHubName = "----EventHubName-----";
	final String sasKeyName = "-----SharedAccessSignatureKeyName-----";
	final String sasKey = "---SharedAccessSignatureKey----";
	ConnectionStringBuilder connStr = new ConnectionStringBuilder(namespaceName, eventHubName, sasKeyName, sasKey);
```

A continuación, cree un evento singular convirtiendo una cadena en su codificación de bytes UTF-8. Después creamos una nueva instancia de cliente de Centros de eventos a partir de la cadena de conexión y enviamos el mensaje.

``` Java 
				
	byte[] payloadBytes = "Test AMQP message from JMS".getBytes("UTF-8");
	EventData sendEvent = new EventData(payloadBytes);
	
	EventHubClient ehClient = EventHubClient.createFromConnectionStringSync(connStr.toString());
	ehClient.sendSync(sendEvent);
	}
}

``` 

<!---HONumber=AcomDC_0323_2016-->