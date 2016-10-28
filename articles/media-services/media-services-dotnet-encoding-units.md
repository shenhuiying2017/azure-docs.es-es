<properties 
	pageTitle="Cómo agregar unidades de codificación" 
	description="Información sobre cómo agregar unidades de codificación con .NET"  
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	manager="erikre" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
 	ms.date="09/01/2016"
	ms.author="juliako;milangada;gtrifonov"/>


#Escalado de codificación con el SDK de .NET

> [AZURE.SELECTOR]
- [Portal](media-services-portal-scale-media-processing.md)
- [.NET](media-services-dotnet-encoding-units.md)
- [REST](https://msdn.microsoft.com/library/azure/dn859236.aspx)
- [Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
- [PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)

##Información general

>[AZURE.IMPORTANT] Para más información sobre cómo escalar el procesamiento de medios, consulte este tema de [introducción](media-services-scale-media-processing-overview.md).
 
Para cambiar el tipo de unidad reservada y el número de unidades reservadas de codificación mediante el SDK de .NET, haga lo siguiente:

	IEncodingReservedUnit encodingS1ReservedUnit = _context.EncodingReservedUnits.FirstOrDefault();
	encodingS1ReservedUnit.ReservedUnitType = ReservedUnitType.Basic; // Corresponds to S1
	encodingS1ReservedUnit.Update();
	Console.WriteLine("Reserved Unit Type: {0}", encodingS1ReservedUnit.ReservedUnitType);
	
	encodingS1ReservedUnit.CurrentReservedUnits = 2;
	encodingS1ReservedUnit.Update();
	
	Console.WriteLine("Number of reserved units: {0}", encodingS1ReservedUnit.CurrentReservedUnits);

##Apertura de una incidencia de soporte técnico

De manera predeterminada las cuentas de Servicios multimedia pueden escalar hasta 25 unidades reservadas de codificación y 5 de streaming a petición. Si desea solicitar un límite mayor, abra una incidencia de soporte técnico.

###Abrir una incidencia de soporte técnico

Para abrir una incidencia de soporte técnico, haga lo siguiente:

1. Haga clic en [Obtener soporte técnico](https://manage.windowsazure.com/?getsupport=true). Si no ha iniciado sesión, se le solicitará que especifique sus credenciales.

1. Seleccione su suscripción.

1. En el tipo de soporte, seleccione "Técnico".

1. Haga clic en "Crear incidencia".

1. Seleccione "Servicios multimedia de Microsoft Azure" en la lista de productos que se muestran en la página siguiente.

1. Seleccione un "Tipo de problema" que sea pertinente en relación con su problema.

1. Haga clic en Continue.

1. Siga las instrucciones que aparecen en la página siguiente y, a continuación, escriba los detalles de su problema.

1. Haga clic en Submit para abrir la incidencia.



##Rutas de aprendizaje de Servicios multimedia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Envío de comentarios

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

<!---HONumber=AcomDC_0907_2016-->