<properties 
	pageTitle="Escalación de un Servicio multimedia" 
	description="Aprenda a escalar Servicios multimedia mediante la especificación del número de unidades reservadas de streaming a petición y unidades reservadas de codificación con las que desea aprovisionar la cuenta." 
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


#Escalación de un Servicio multimedia  

Este artículo forma parte de la serie [Flujo de trabajo de vídeo bajo demanda de Servicios multimedia](../media-services-video-on-demand-workflow).

##Información general

Es posible escalar Servicios multimedia mediante la especificación del número de **unidades reservadas de streaming** y **unidades reservadas de codificación** con las que desea aprovisionar la cuenta. 

##Unidades reservadas de streaming

Para obtener más información, consulte [Escalación de unidades de streaming](../media-services-manage-origins#scale_streaming_endpoints).

##<a id="encoding_reserved_units"></a>Unidades reservadas de codificación

Una cuenta de Servicios multimedia está asociada con un tipo de unidad reservada que determina la rapidez con la que se procesan los trabajos de codificación. Puede elegir uno de los siguientes tipos de unidad reservada: Basic, Standard o Premium. Por ejemplo, el mismo trabajo de codificación se ejecuta más rápido cuando se usa el tipo de unidad reservada Standard en comparación con el tipo Basic. Para obtener más información, consulte el blog de "Codificación de tipos de unidad reservada" escrito por [Milan Gada](http://azure.microsoft.com/blog/author/milanga).

Además de especificar el tipo de unidad reservada, puede especificar el aprovisionamiento de su cuenta con unidades reservadas de codificación. El número de unidades reservadas de codificación aprovisionadas determina el número de tareas multimedia que se pueden procesar de forma simultánea en una cuenta determinada. Por ejemplo, si la cuenta tiene cinco unidades reservadas, se ejecutarán simultáneamente cinco tareas multimedia siempre que haya tareas para procesar. Las tareas restantes esperarán en la cola y se elegirán para el procesamiento secuencialmente tan pronto como finalice la tarea en ejecución. Si una cuenta no tiene ninguna unidad reservada aprovisionada, las tareas se elegirán de manera secuencial. En este caso, el tiempo de espera entre la finalización de una tarea y el inicio de la siguiente dependerá de la disponibilidad de los recursos del sistema.

Para cambiar el tipo de unidad reservada y el número de unidades reservadas de codificación, haga lo siguiente:

1. En el [Portal de administración](https://manage.windowsazure.com/), haga clic en **Servicios multimedia**. A continuación, haga clic en el nombre del servicio multimedia.

2. Seleccione la página **CODIFICACIÓN**. 

	Para cambiar el **TIPO DE UNIDAD RESERVADA**, presione BASIC, STANDARD o PREMIUM. 

	Para cambiar el número de unidades reservadas para el tipo de unidad reservada seleccionada, use el control deslizante **CODIFICACIÓN**. 
	
	
	![Processors page](./media/media-services-how-to-scale/media-services-encoding-scale.png)

	  
	>[Azure.Note] Los siguientes centros de datos no ofrecen el tipo de unidad reservada Premium: Singapur, Hong Kong, Osaka, Beijing, Shanghai.

3. Presione el botón SAVE para guardar los cambios.

	Las nuevas unidades reservadas de codificación se asignan en cuanto presiona GUARDAR.

	>[Azure.Note] Se utiliza el número más elevado de unidades especificadas durante el período de 24 horas al calcular el coste.

##Apertura de una incidencia de soporte técnico

De manera predeterminada las cuentas de Servicios multimedia pueden escalar hasta 25 unidades reservadas de codificación y 5 de streaming a petición. Si desea solicitar un límite mayor, abra una incidencia de soporte técnico.

Para abrir una incidencia de soporte técnico, haga lo siguiente: 

1. Inicie sesión en su cuenta de Azure en el [Portal de administración](http://manage.windowsazure.com).
2. Vaya a [Soporte técnico](http://azure.microsoft.com/support/contact/).
3. Haga clic en "Obtener soporte".
4. Seleccione su suscripción.
5. En el tipo de soporte, seleccione "Técnico".
6. Haga clic en "Crear incidencia".
7. Seleccione "Servicios multimedia de Microsoft Azure" en la lista de productos que se muestran en la página siguiente.
8. Seleccione "Media Processing" como "Problem type" y, a continuación, seleccione "Reservation Units" en categoría.
9. Haga clic en Continue.
10. Siga las instrucciones de la página siguiente y, a continuación, especifique los detalles sobre cuántas unidades reservadas de streaming a petición o de codificación necesita.
11. Haga clic en Submit para abrir la incidencia.





<!--HONumber=45--> 
