<properties 
	pageTitle="Sondeo de operaciones de larga duración" 
	description="En este tema se muestra cómo sondear las operaciones de larga duración." 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/16/2015" 
	ms.author="juliako"/>


#Streaming en vivo con Servicios multimedia de Azure

##Información general

Servicios multimedia de Microsoft Azure ofrece determinadas API que envían solicitudes a los Servicios multimedia para iniciar operaciones (por ejemplo, crear, iniciar, detener o eliminar un canal). Estas son operaciones de larga duración.

El SDK .NET de Servicios multimedia proporciona las API que envían la solicitud y esperan a que la operación se complete (internamente, las API sondean el progreso de la operación a intervalos determinados). Por ejemplo, cuando se llama a channel.Start(), se devuelve el método después de que se inicie el canal. También puede usar la versión asincrónica: await channel.StartAsync() (para obtener información sobre el patrón asincrónico basado en tareas, consulte [TAP](https://msdn.microsoft.com/library/hh873175(v=vs.110).aspx)). Las API que envían una solicitud de operación y, después, sondean el estado hasta que se completa la operación se llaman "métodos de sondeo". Estos métodos (sobre todo la versión asincrónica) se recomiendan para las aplicaciones cliente enriquecidas y para servicios con estado.

En ciertos casos, una aplicación no puede esperar a una solicitud HTTP de ejecución prolongada y desea sondear el progreso de la operación manualmente. Un ejemplo típico sería un explorador que interactúa con un servicio web sin estado: cuando el explorador solicita crear un canal, el servicio web inicia una operación de ejecución prolongada y devuelve el identificador de la operación al explorador. A continuación, el explorador puede solicitar al servicio web que obtenga el estado de la operación en función del identificador. El SDK .NET de Servicios multimedia proporciona las API que son útiles para este escenario. Estas API se denominan "métodos sin sondeo".
Dichos métodos tienen el siguiente patrón de nomenclatura: Send*nombreOperación*Operation (por ejemplo, SendCreateOperation). Los métodos Send*nombreOperación*Operation devuelven el objeto **IOperation**. El objeto devuelto contiene información que puede usarse para realizar un seguimiento de la operación. Los métodos Send*nombreOperación*OperationAsync devuelven **Task<IOperation>**.

Las siguientes clases admiten métodos sin sondeo actualmente:  **Channel**, **StreamingEndpoint** y **Program**.

Para sondear el estado de la operación, use el método **GetOperation** en la clase **OperationBaseCollection**. Use los siguientes intervalos para comprobar el estado de la operación: para las operaciones **Channel** y **StreamingEndpoint**, use 30 segundos; para las operaciones **Program**, use 10 segundos.


##Ejemplo

En el ejemplo siguiente se define una clase llamada **ChannelOperations**. Esta definición de clase puede constituir un punto de partida para la definición de clase del servicio web. Para simplificar, en los siguientes ejemplos se usan las versiones no asincrónicas de los métodos.

También se muestra cómo el cliente puede usar esta clase.

###Definición de la clase ChannelOperations

	/// <summary> 
	/// La clase ChannelOperations solo implementa 
	/// la operación de creación del canal. 
	/// </summary> 
	public class ChannelOperations
	{
	    // Leer valores del archivo App.config.
	    private static readonly string _mediaServicesAccountName =
	        ConfigurationManager.AppSettings["MediaServicesAccountName"];
	    private static readonly string _mediaServicesAccountKey =
	        ConfigurationManager.AppSettings["MediaServicesAccountKey"];
	
	    // Campo para contexto de servicio.
	    private static CloudMediaContext _context = null;
	    private static MediaServicesCredentials _cachedCredentials = null;
	
	    public ChannelOperations()
	    {
	            _cachedCredentials = new MediaServicesCredentials(_mediaServicesAccountName,
	                _mediaServicesAccountKey);
	
	            _context = new CloudMediaContext(_cachedCredentials);    }
	
	    /// <summary>  
	    /// Inicia la creación de un nuevo canal.  
	    /// </summary>  
	    /// <param name="channelName">Nombre que se va a asignar al nuevo canal</param>  
	    /// <returns>  
	    /// Identificador de la operación de larga duración ejecutada por Servicios multimedia. 
	    /// Use este identificador de operación para sondear el estado de creación de canal. 
	    /// </returns> 
	    public string StartChannelCreation(string channelName)
	    {
	        var operation = _context.Channels.SendCreateOperation(
	            new ChannelCreationOptions
	            {
	                Name = channelName,
	                Input = CreateChannelInput(),
	                Preview = CreateChannelPreview(),
	                Output = CreateChannelOutput()
	            });
	
	        return operation.Id;
	    }
	
	    /// <summary> 
	    /// Comprueba si la operación se ha completado. 
	    /// Si la operación se ha realizado correctamente, se devuelve el identificador del canal creado en el parámetro de salida.
	    /// </summary> 
	    /// <param name="operationId">Identificador de la operación.</param> 
	    /// <param name="channel">
	    /// Si la operación se ha realizado correctamente, 
	    /// se devuelve el identificador del canal creado en el parámetro de salida.</param>
	    /// <returns>Devuelve false si la operación aún está curso; de lo contrario, devuelve true.</returns> 
	    public bool IsCompleted(string operationId, out string channelId)
	    {
	        IOperation operation = _context.Operations.GetOperation(operationId);
	        bool completed = false;
	
	        channelId = null;
	
	        switch (operation.State)
	        {
	            case OperationState.Failed:
	                // Controle el error. 
	                // Por ejemplo, inicie una excepción. 
					// Use la información siguiente en la excepción: operationId, operation.ErrorMessage.
	                break;
	            case OperationState.Succeeded:
	                completed = true;
	                channelId = operation.TargetEntityId;
	                break;
	            case OperationState.InProgress:
	                completed = false;
	                break;
	        }
	        return completed;
	    }
	
	
	    private static ChannelInput CreateChannelInput()
	    {
	        return new ChannelInput
	        {
	            StreamingProtocol = StreamingProtocol.RTMP,
	            AccessControl = new ChannelAccessControl
	            {
	                IPAllowList = new List<IPRange>
	                {
	                    new IPRange
	                    {
	                        Name = "TestChannelInput001",
	                        Address = IPAddress.Parse("0.0.0.0"),
	                        SubnetPrefixLength = 0
	                    }
	                }
	            }
	        };
	    }
	
	    private static ChannelPreview CreateChannelPreview()
	    {
	        return new ChannelPreview
	        {
	            AccessControl = new ChannelAccessControl
	            {
	                IPAllowList = new List<IPRange>
	                {
	                    new IPRange
	                    {
	                        Name = "TestChannelPreview001",
	                        Address = IPAddress.Parse("0.0.0.0"),
	                        SubnetPrefixLength = 0
	                    }
	                }
	            }
	        };
	    }
	
	    private static ChannelOutput CreateChannelOutput()
	    {
	        return new ChannelOutput
	        {
	            Hls = new ChannelOutputHls { FragmentsPerSegment = 1 }
	        };
	    }
	}

###El código de cliente

	ChannelOperations channelOperations = new ChannelOperations();
	string opId = channelOperations.StartChannelCreation("MyChannel001");
	
	string channelId = null;
	bool isCompleted = false;
	
	while (isCompleted == false)
	{
	    System.Threading.Thread.Sleep(TimeSpan.FromSeconds(30));
	    isCompleted = channelOperations.IsCompleted(opId, out channelId);
	}
	
	// Si hemos llegado hasta aquí, deberíamos tener el identificador del canal recién creado.
	Console.WriteLine(channelId);

<!--HONumber=52-->