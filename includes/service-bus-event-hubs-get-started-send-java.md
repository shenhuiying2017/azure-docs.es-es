## Envío de mensajes a Centros de eventos
En esta sección se escribirá una aplicación de consola Java para enviar eventos al centro de eventos. Utilizaremos el proveedor de JMS AMQP del [proyecto Apache Qpid](http://qpid.apache.org/). Esto es parecido a usar temas y colas de Bus de servicio con AMQP a través de Java como se muestra [aquí](http://azure.microsoft.com/es-es/documentation/articles/service-bus-java-how-to-use-jms-api-amqp/). Para obtener más información consulte la [documentación de Qpid JMS](http://qpid.apache.org/releases/qpid-0.30/programming/book/QpidJMS.html) y el [servicio de mensajería Java](http://www.oracle.com/technetwork/java/jms/index.html).

1. En Eclipse, cree un nuevo proyecto de Java denominado **Remitente**.

2. Descargue la última versión de la biblioteca **Qpid JMS AMQP 1.0** desde [aquí](http://qpid.apache.org/components/qpid-jms/index.html).

3. Extraiga los archivos del archivo y copie los JAR siguientes desde el directorio de archivo "qpid-amqp-1-0-client-jms\<version>\lib" en el proyecto **Remitente** de Eclipse.

4. En el Explorador de paquetes de Eclipse, haga clic con el botón secundario en el proyecto **Remitente** y seleccione **Propiedades**. En el panel izquierdo del cuadro de diálogo, haga clic en **Ruta de compilación Java**, haga clic en **Bibliotecas** y, a continuación, en el botón **Agregar JAR**. Seleccione todos los JAR copiados anteriormente y, a continuación, haga clic en **Aceptar**.

	![][8]

5. Cree un archivo denominado **servicebus.properties** en la raíz del proyecto **Remitente**, con el siguiente contenido. No olvide sustituir el valor para el nombre del centro de eventos y el espacio de nombres (este último suele ser "{nombre del centro de eventos}-ns"). También debe sustituir una versión con codificación URL de la clave para la regla **SendRule** creada anteriormente. Puede codificar con URL [aquí](http://www.w3schools.com/tags/ref_urlencode.asp).

		# servicebus.properties - sample JNDI configuration

		# Register a ConnectionFactory in JNDI using the form:
		# connectionfactory.[jndi_name] = [ConnectionURL]
		connectionfactory.SBCF = amqps://SendRule:{Send Rule key}@{namespace name}.servicebus.windows.net/?sync-publish=false

		# Register some queues in JNDI using the form
		# queue.[jndi_name] = [physical_name]
		# topic.[jndi_name] = [physical_name]
		queue.EventHub = {event hub name}

5. Cree una clase nueva denominada **Remitente**. Agregue las siguientes instrucciones "import":

		import java.io.BufferedReader;
		import java.io.IOException;
		import java.io.InputStreamReader;
		import java.io.UnsupportedEncodingException;
		import java.util.Hashtable;
		
		import javax.jms.BytesMessage;
		import javax.jms.Connection;
		import javax.jms.ConnectionFactory;
		import javax.jms.Destination;
		import javax.jms.JMSException;
		import javax.jms.MessageProducer;
		import javax.jms.Session;
		import javax.naming.Context;
		import javax.naming.InitialContext;
		import javax.naming.NamingException; 

8. Después agregue el siguiente código:

		public static void main(String[] args) throws NamingException,
				JMSException, IOException, InterruptedException {
			// Configure JNDI environment
			Hashtable<String, String> env = new Hashtable<String, String>();
			env.put(Context.INITIAL_CONTEXT_FACTORY,
					"org.apache.qpid.amqp_1_0.jms.jndi.PropertiesFileInitialContextFactory");
			env.put(Context.PROVIDER_URL, "servicebus.properties");
			Context context = new InitialContext(env);
	
			ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCF");
	
			Destination queue = (Destination) context.lookup("EventHub");
	
			// Create Connection
			Connection connection = cf.createConnection();
	
			// Create sender-side Session and MessageProducer
			Session sendSession = connection.createSession(false,
					Session.AUTO_ACKNOWLEDGE);
			MessageProducer sender = sendSession.createProducer(queue);
	
			System.out.println("Press Ctrl-C to stop the sender process");
			System.out.println("Press Enter to start now");
			BufferedReader commandLine = new java.io.BufferedReader(
					new InputStreamReader(System.in));
			commandLine.readLine();
	
			while (true) {
				sendBytesMessage(sendSession, sender);
				Thread.sleep(200);
			}
		}
		
		private static void sendBytesMessage(Session sendSession, MessageProducer sender) throws JMSException, UnsupportedEncodingException {
	        BytesMessage message = sendSession.createBytesMessage();
	        message.writeBytes("Test AMQP message from JMS".getBytes("UTF-8"));
	        sender.send(message);
	        System.out.println("Sent message");
	    }



<!-- Links -->
[Portal de administración de Azure]: https://manage.windowsazure.com/


<!-- Images -->
[8]: ./media/service-bus-event-hubs-getstarted/create-sender-java1.png
