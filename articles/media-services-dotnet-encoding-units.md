<properties 
	pageTitle="Cómo agregar unidades de codificación" 
	description="Información sobre cómo agregar unidades de codificación con .NET" 
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
	ms.date="02/15/2015" 
	ms.author="juliako"/>




#Escalado de codificación con el SDK de .NET

Este artículo forma parte de la serie [Vídeo de Servicios multimedia sobre el flujo de trabajo a petición](media-services-video-on-demand-workflow.md).
  
##Información general

Una cuenta de Servicios multimedia está asociada con un tipo de unidad reservada que determina la rapidez con la que se procesan los trabajos de codificación. Puede elegir uno de los siguientes tipos de unidad reservada: Basic, Standard o Premium. Por ejemplo, el mismo trabajo de codificación se ejecuta más rápido cuando se usa el tipo de unidad reservada Standard en comparación con el tipo Basic. Para obtener más información, consulte el blog acerca de los tipos de unidades reservadas de codificación escrito por [Milan Gada](http://azure.microsoft.com/blog/author/milanga/).

Además de especificar el tipo de unidad reservada, puede especificar el aprovisionamiento de su cuenta con unidades reservadas de codificación. El número de unidades reservadas de codificación aprovisionadas determina el número de tareas multimedia que se pueden procesar de forma simultánea en una cuenta determinada. Por ejemplo, si la cuenta tiene cinco unidades reservadas, se ejecutarán simultáneamente cinco tareas multimedia siempre que haya tareas para procesar. Las tareas restantes esperarán en la cola y se elegirán para el procesamiento secuencialmente tan pronto como finalice la tarea en ejecución. Si una cuenta no tiene ninguna unidad reservada aprovisionada, las tareas se elegirán de manera secuencial. En este caso, el tiempo de espera entre la finalización de una tarea y el inicio de la siguiente dependerá de la disponibilidad de los recursos del sistema.

Para cambiar el tipo de unidad reservada y el número de unidades reservadas de codificación mediante el SDK de .NET, haga lo siguiente:

	IEncodingReservedUnit encodingBasicReservedUnit = _context.EncodingReservedUnits.FirstOrDefault();
	encodingBasicReservedUnit.ReservedUnitType = ReservedUnitType.Basic;
	encodingBasicReservedUnit.Update();
	Console.WriteLine("Reserved Unit Type: {0}", encodingBasicReservedUnit.ReservedUnitType);
	
	encodingBasicReservedUnit.CurrentReservedUnits = 2;
	encodingBasicReservedUnit.Update();
	
	Console.WriteLine("Number of reserved units: {0}", encodingBasicReservedUnit.CurrentReservedUnits);

##Apertura de una incidencia de soporte técnico

De manera predeterminada las cuentas de Servicios multimedia pueden escalar hasta 25 unidades reservadas de codificación y 5 de streaming a petición. Si desea solicitar un límite mayor, abra una incidencia de soporte técnico.

Para abrir una incidencia de soporte técnico, haga lo siguiente: 

1. Inicie sesión en su cuenta de Azure en [Portal de administración](http://manage.windowsazure.com).
2. Vaya a [Soporte técnico](http://www.windowsazure.com/support/contact/).
3. Haga clic en "Obtener soporte".
4. Seleccione su suscripción.
5. En el tipo de soporte, seleccione "Técnico".
6. Haga clic en "Crear incidencia".
7. Seleccione "Servicios multimedia de Microsoft Azure" en la lista de productos que se muestran en la página siguiente.
8. Seleccione "Media Processing" como "Problem type" y, a continuación, seleccione "Reservation Units" en categoría.
9. Haga clic en Continue.
10. Siga las instrucciones de la página siguiente y, a continuación, especifique los detalles sobre cuántas unidades reservadas de streaming a petición o de codificación necesita.
11. Haga clic en Submit para abrir la incidencia.



<!--HONumber=52-->