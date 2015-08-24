<properties 
	pageTitle="Uso de AMQP 1.0 con la API del Bus de servicio de .NET | Microsoft Azure" 
	description="Obtenga información acerca de cómo usar Advanced Message Queuing Protocol (AMQP) 1.0 con la API del Service Bus de .NET de Azure." 
	services="service-bus" 
	documentationCenter=".net" 
	authors="sethmanheim" 
	manager="timlt" 
	editor="mattshel"/>

<tags 
	ms.service="service-bus" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="07/02/2015" 
	ms.author="sethm"/>

# Uso de AMQP 1.0 con la API .NET del bus de servicio

Advanced Message Queuing Protocol (AMQP) 1.0 es un protocolo de mensajes a nivel de red, confiable y eficaz que se puede utilizar para crear aplicaciones de mensajes robustas y compatibles con varias plataformas.

La compatibilidad con AMQP 1.0 del Bus de servicio implica que puede utilizar las funciones de colas y publicación/suscripción de mensajería asíncrona desde una amplia variedad de plataformas mediante un eficaz protocolo binario. Además, puede desarrollar aplicaciones formadas por componentes creados con una mezcla de lenguajes, marcos y sistemas operativos.

En esta guía de procedimientos se explica cómo utilizar las funciones de mensajería asíncrona del bus de servicio (colas y publicación/suscripción de temas) desde aplicaciones .NET utilizando la API .NET del bus de servicio. Existe otra guía de procedimientos complementaria en la que se explica cómo hacer lo mismo utilizando la API estándar de Java Message Service (JMS). Puede utilizar estas dos guías conjuntamente para obtener información acerca de la mensajería entre diferentes plataformas mediante AMQP 1.0.

## Introducción al Bus de servicio

En esta guía se asume que ya dispone de un espacio de nombres del Bus de servicio con una cola denominada “queue1”. Si no es así, puede crear el espacio de nombres y la cola con ayuda del [Portal de administración de Azure](http://manage.windowsazure.com). Para obtener más información acerca de cómo crear espacios de nombres y colas del Bus de servicio, consulte la guía de procedimientos titulada [Utilización de las colas del Bus de servicio](https://azure.microsoft.com/develop/net/how-to-guides/service-bus-queues/).

## Descarga del SDK del Bus de servicio

La compatibilidad con AMQP 1.0 está disponible en el SDK del bus de servicio 2.1 o posterior. Puede descargar el SDK más reciente de NuGet, en la dirección [http://nuget.org/packages/WindowsAzure.ServiceBus/](http://nuget.org/packages/WindowsAzure.ServiceBus/).

## Codificación de aplicaciones .NET

De manera predeterminada, la biblioteca de clientes .NET del bus de servicio se comunica con el servicio del bus de servicio utilizando un protocolo dedicado basado en SOAP. Para utilizar AMQP 1.0 en lugar del protocolo predeterminado, es necesario configurar de manera explícita la cadena de conexión del bus de servicio tal y como se describe en la sección siguiente. Aparte de este cambio, el código de la aplicación prácticamente permanece invariable al utilizar AMQP 1.0.

La versión actual incluye unas cuantas funciones de la API que no son compatibles con el uso de AMQP. Estas funciones incompatibles se enumeran más adelante en la sección Funciones incompatibles y restricciones. Algunos de los parámetros de configuración avanzados también adquieren un significado diferente cuando se usa AMQP. En esta breve guía de procedimientos no se utiliza ninguno de estos parámetros, pero puede encontrar más información en la [Guía para desarrolladores sobre AMQP 1.0 del Bus de servicio](http://msdn.microsoft.com/library/jj841071.aspx).

### Configuración mediante App.config

Es una práctica recomendada para que las aplicaciones usen el archivo de configuración App.config para almacenar la configuración. En el caso de las aplicaciones del Bus de servicio, puede utilizar App.config para almacenar la **ConnectionString** del Bus de servicio. Esta aplicación de ejemplo también utiliza App.config para almacenar el nombre de la entidad de mensajería de Service Bus que utiliza.

A continuación se muestra un archivo App.config de ejemplo:

	<?xml version="1.0" encoding="utf-8" ?>
	<configuration>
	  	<appSettings>
		    <add key="Microsoft.ServiceBus.ConnectionString"
        	     value="Endpoint=sb://[namespace].servicebus.windows.net;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[SAS key];TransportType=Amqp" />
	    	<add key="EntityName" value="queue1" />
  		</appSettings>
	</configuration>

### Configuración de la cadena de conexión del Bus de servicio

El valor del parámetro **Microsoft.ServiceBus.ConnectionString** es la cadena de conexión del Bus de servicio que se utiliza para configurar la conexión al Bus de servicio. El formato es como sigue:

	Endpoint=sb://[namespace].servicebus.windows.net;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[SAS key];TransportType=Amqp

Los valores [namespace] e [SAS key] se obtienen del Portal de administración de Azure. Para obtener más información, consulte [Utilización de las colas del Bus de servicio][].

Al utilizar AMQP, se agrega “;TransportType=Amqp” al final de la cadena de conexión para informar a la biblioteca de clientes de que debe recurrir a AMQP 1.0 para establecer la conexión con el bus de servicio.

### Configuración del nombre de la entidad

Esta aplicación de ejemplo utiliza el parámetro `EntityName` de la sección **appSettings** del archivo App.config para configurar el nombre de la cola con la que la aplicación intercambia mensajes.

### Una aplicación .NET sencilla que utiliza una cola del Bus de servicio

En el siguiente ejemplo se intercambian mensajes con una cola del bus de servicio.

	// SimpleSenderReceiver.cs
	
	using System;
	using System.Configuration;
	using System.Threading;
	using Microsoft.ServiceBus.Messaging;
	
	namespace SimpleSenderReceiver
	{
	    class SimpleSenderReceiver
	    {
	        private static string connectionString;
	        private static string entityName;
	        private static Boolean runReceiver = true;
	        private MessagingFactory factory;
	        private MessageSender sender;
	        private MessageReceiver receiver;
	        private MessageListener messageListener;
	        private Thread listenerThread;
	
	        static void Main(string[] args)
	        {
	            try
	            {
	                if ((args.Length > 0) && args[0].ToLower().Equals("sendonly"))
	                    runReceiver = false;
	                
	                string ConnectionStringKey = "Microsoft.ServiceBus.ConnectionString";
	                string entityNameKey = "EntityName";
	                entityName = ConfigurationManager.AppSettings[entityNameKey];
	                connectionString = ConfigurationManager.AppSettings[ConnectionStringKey];
	                SimpleSenderReceiver simpleSenderReceiver = new SimpleSenderReceiver();
	
	                Console.WriteLine("Press [enter] to send a message. " +
	                    "Type 'exit' + [enter] to quit.");
	
	                while (true)
	                {
	                    string s = Console.ReadLine();
	                    if (s.ToLower().Equals("exit"))
	                    {
	                        simpleSenderReceiver.Close();
	                        System.Environment.Exit(0);
	                    }
	                    else
	                        simpleSenderReceiver.SendMessage();
	                }
	            }
	            catch (Exception e)
	            {
	                Console.WriteLine("Caught exception: " + e.Message);
	            }
	        }
	
	        public SimpleSenderReceiver()
	        {
	            factory = MessagingFactory.CreateFromConnectionString(connectionString);
	            sender = factory.CreateMessageSender(entityName);
	
	            if (runReceiver)
	            {
	                receiver = factory.CreateMessageReceiver(entityName);
	                messageListener = new MessageListener(receiver);
	                listenerThread = new Thread(messageListener.Listen);
	                listenerThread.Start();
	            }
	        }
	
	        public void Close()
	        {
	            messageListener.RequestStop();
	            listenerThread.Join();
	            factory.Close();
	        }
	
	        private void SendMessage()
	        {
	            BrokeredMessage message = 
	                new BrokeredMessage("Test AMQP message from .NET");
	            sender.Send(message);
	            Console.WriteLine("Sent message with MessageID = " 
	                + message.MessageId);
	        }
	
	    }
	
	    public class MessageListener
	    {
	        private MessageReceiver messageReceiver;
	        public MessageListener(MessageReceiver receiver)
	        {
	            messageReceiver = receiver;
	        }
	
	        public void Listen()
	        {
	            while (!_shouldStop)
	            {
	                try
	                {
	                    BrokeredMessage message = 
	                        messageReceiver.Receive(new TimeSpan(0, 0, 10));
	                    if (message != null)
	                    {
	                        Console.WriteLine("Received message with MessageID = " + 
	                            message.MessageId);
	                        message.Complete();
	                    }
	                }
	                catch (Exception e)
	                {
	                    Console.WriteLine("Caught exception: " + e.Message);
	                    break;
	                }
	            }
	        }
	
	        public void RequestStop()
	        {
	            _shouldStop = true;
	        }
	
	        private volatile bool _shouldStop;
	    }
	}

### Ejecución de la aplicación

Al ejecutar la aplicación se obtienen resultados del tipo:

	> SimpleSenderReceiver.exe
	Press [enter] to send a message. Type 'exit' + [enter] to quit.
	
	Sent message with MessageID = fb7f5d3733704e4ba4bd55f759d9d7cf
	Received message with MessageID = fb7f5d3733704e4ba4bd55f759d9d7cf
	
	Sent message with MessageID = d00e2e088f06416da7956b58310f7a06
	Received message with MessageID = d00e2e088f06416da7956b58310f7a06
	
	Received message with MessageID = f27f79ec124548c196fd0db8544bca49
	Sent message with MessageID = f27f79ec124548c196fd0db8544bca49
	exit

## Mensajería de diferentes plataformas entre JMS y .NET

Hasta ahora hemos visto cómo enviar mensajes al bus de servicio utilizando .NET y cómo recibir mensajes utilizando .NET. Sin embargo, una de las ventajas principales de AMQP 1.0 es que permite que las aplicaciones estén formadas por componentes escritos en diferentes lenguajes, a la vez que garantiza que los mensajes se intercambien con total seguridad y fidelidad.

Utilizando la aplicación .NET de ejemplo descrita anteriormente y una aplicación Java similar tomada de la guía complementaria [Uso de la API de Java Message Service (JMS) con el Bus de servicio y AMQP 1.0](service-bus-java-how-to-use-jms-api-amqp.md), es posible intercambiar mensajes entre .NET y Java.

Para obtener más información acerca de la mensajería entre distintas plataformas mediante el Bus de servicio y AMQP 1.0, consulte la [Guía para desarrolladores sobre AMQP 1.0 del Bus de servicio](http://msdn.microsoft.com/library/azure/jj841071.aspx).

### De JMS a .NET

Para comprobar cómo funciona la mensajería de JMS a .NET:

* Inicie la aplicación de ejemplo .NET sin ningún argumento de línea de comandos.
* Inicie la aplicación de ejemplo Java con el argumento de línea de comandos “sendonly”. De esta manera, la aplicación no recibirá mensajes de la cola, sino que solo los enviará.
* Presione **Entrar** unas cuantas veces en la consola de la aplicación Java para enviar los mensajes.
* Estos mensajes los recibe la aplicación .NET.

### Resultados de la aplicación JMS

	> java SimpleSenderReceiver sendonly
	Press [enter] to send a message. Type 'exit' + [enter] to quit.
	Sent message with JMSMessageID = ID:4364096528752411591
	Sent message with JMSMessageID = ID:459252991689389983
	Sent message with JMSMessageID = ID:1565011046230456854
	exit

### Resultados de la aplicación .NET

	> SimpleSenderReceiver.exe	
	Press [enter] to send a message. Type 'exit' + [enter] to quit.
	Received message with MessageID = 4364096528752411591
	Received message with MessageID = 459252991689389983
	Received message with MessageID = 1565011046230456854
	exit

## De .NET a JMS

Para comprobar cómo funciona la mensajería de .NET a JMS:

* Inicie la aplicación de ejemplo .NET con el argumento de línea de comandos “sendonly”. De esta manera, la aplicación no recibirá mensajes de la cola, sino que solo los enviará.
* Inicie la aplicación de ejemplo Java sin ningún argumento de línea de comandos.
* Presione **Entrar** unas cuantas veces en la consola de la aplicación .NET para enviar los mensajes.
* Estos mensajes los recibe la aplicación Java.

#### Resultados de la aplicación .NET

	> SimpleSenderReceiver.exe sendonly
	Press [enter] to send a message. Type 'exit' + [enter] to quit.
	Sent message with MessageID = d64e681a310a48a1ae0ce7b017bf1cf3	
	Sent message with MessageID = 98a39664995b4f74b32e2a0ecccc46bb
	Sent message with MessageID = acbca67f03c346de9b7893026f97ddeb
	exit


#### Resultados de la aplicación JMS

	> java SimpleSenderReceiver	
	Press [enter] to send a message. Type 'exit' + [enter] to quit.
	Received message with JMSMessageID = ID:d64e681a310a48a1ae0ce7b017bf1cf3
	Received message with JMSMessageID = ID:98a39664995b4f74b32e2a0ecccc46bb
	Received message with JMSMessageID = ID:acbca67f03c346de9b7893026f97ddeb
	exit

## Características no admitidas y restricciones

Actualmente las siguientes funciones de la API .NET del bus de servicio no son compatibles con el uso de AMQP:

* Transacciones
* Envío a través de un destino de transferencia
* Recepción por número de secuencia de los mensajes
* Exploración de mensajes y sesiones
* Estado de la sesión
* API basadas en lotes
* Recepción escalada horizontalmente
* Manipulación de las reglas de suscripción por parte del tiempo de ejecución
* Renovación del bloqueo de la sesión
* Pequeñas diferencias de funcionamiento

Para obtener más información, consulte la [Guía para desarrolladores sobre AMQP 1.0 del Bus de servicio](http://msdn.microsoft.com/library/azure/jj841071.aspx). Aquí se incluye una lista detallada de las API incompatibles.

## Resumen

En esta guía de procedimientos mostramos cómo obtener acceso a las funciones de mensajería asíncrona del bus de servicio (colas y publicación/suscripción de temas) desde .NET utilizando AMQP 1.0 y la API .NET del bus de servicio.

También puede utilizar AMQP 1.0 del bus de servicio desde otros lenguajes, como Java, C, Python y PHP. Los componentes creados utilizando estos lenguajes pueden intercambiar mensajes con seguridad y fidelidad con AMQP 1.0 en el Bus de servicio. Para obtener más información, consulte la [Guía para desarrolladores sobre AMQP 1.0 del Bus de servicio](http://msdn.microsoft.com/library/azure/jj841071.aspx).

## Pasos siguientes

* [Compatibilidad de AMQP 1.0 en el Bus de servicio de Azure](service-bus-amqp-overview.md)
* [Uso de la API de Java Message Service (JMS) con el Bus de servicio y AMQP 1.0](service-bus-java-how-to-use-jms-api-amqp.md)
* [Guía para desarrolladores sobre AMQP 1.0 del Bus de servicio](http://msdn.microsoft.com/library/azure/jj841071.aspx)
* [Utilización de las colas del Bus de servicio](service-bus-dotnet-how-to-use-queues.md)
 

<!---HONumber=August15_HO7-->