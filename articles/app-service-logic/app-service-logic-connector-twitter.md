<properties 
   pageTitle="Aplicación de API de conector Twitter" 
   description="Uso del conector Twitter" 
   services="app-service\logic" 
   documentationCenter=".net,nodejs,java" 
   authors="anuragdalmia" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="03/20/2015"
   ms.author="adgoda"/>


# Uso del conector Twitter en su aplicación lógica #

Las aplicaciones lógicas se pueden desencadenar en función de una variedad de orígenes de datos y ofrecen conectores para obtener y procesar los datos como parte del flujo.

El conector Twitter permite exponer tweets y obtener tweets de la escala de tiempo, amigos y seguidores de su cuenta de Twitter.

- El desencadenador del conector Twitter recuperará nuevos tweets asociados con la palabra clave dada. Cuando se recupera un nuevo tuit, desencadena una nueva instancia del flujo y pasa los datos recibidos en la solicitud al flujo para el procesamiento. 
- Las acciones del conector Twitter le permite "tweetear", "buscar tweets", "retweetear", "obtener la escala de tiempo del usuario", etc. Estas acciones devuelven una respuesta y hacen que estén disponibles las acciones en el flujo para su consumo.

## Creación de un conector Twitter para la lógica de aplicación ##
Para usar el conector Twitter, primero necesita crear una instancia de la aplicación de API del conector Twitter. Se puede hacer de la forma siguiente:

1. Abra Azure Marketplace mediante la opción +NUEVO en la parte inferior izquierda del Portal de Azure.
1. Vaya a “Aplicaciones de API” y busque “Conector Twitter”.
1. Configure el conector Twitter de la siguiente manera:

	![][1]
4.	Haga clic en Aceptar para crear.
5.	Una vez creada la instancia de aplicación de API, puede crear una aplicación lógica en el mismo grupo de recursos para usar el conector Twitter. 
	- La instancia de la aplicación de API del conector Twitter también pueden crearse desde la aplicación lógica. 
	- Abra el editor de la aplicación lógica y haga clic en el conector Twitter disponible en la galería en el lado derecho.
	- Esto creará una instancia de la aplicación de API del conector Twitter en el mismo grupo de recursos en el que se ha creado la aplicación lógica.


## Uso del conector Twitter en su aplicación lógica ##
Una vez creada la aplicación de API, ahora puede usar el conector Twitter como un desencadenador o una acción para la aplicación lógica. Para ello, necesita lo siguiente:

1.	Crear una nueva aplicación lógica y elegir el mismo grupo de recursos que tiene el conector Twitter.
 	
	![][2]
2.	Abra "Desencadenadores y acciones" para abrir el Diseñador de aplicaciones lógicas y configure el flujo. 
 	
	![][3]
3.	El conector Twitter debe aparecer en la sección "Usados recientemente" en la galería en el lado derecho. Selecciónelo.
 
	![][4]
4.	Puede colocar la aplicación de API del conector Twitter en el editor haciendo clic en "Conector Twitter" bajo "Usados recientemente" en el lado derecho de la galería. Haga clic en el botón Autorizar. Proporcione sus credenciales de Twitter. Haga clic en "Autorizar aplicación"
 
	![][5]
6.	Ahora puede usar el conector Twitter en el flujo. Ahora puede utilizar los tweets recuperados desde el desencadenador de Twitter en otras acciones en el flujo.
 
	![][6]
7.	De un modo similar, puede usar las acciones de Twitter en el flujo. Seleccione una acción de Twitter y configure las entradas para la acción correspondiente.

	![][7]
	![][8]

	<!--Image references-->
[1]: ./media/app-service-logic-connector-twitter/img1.png
[2]: ./media/app-service-logic-connector-twitter/img2.png
[3]: ./media/app-service-logic-connector-twitter/img3.png
[4]: ./media/app-service-logic-connector-twitter/img4.png
[5]: ./media/app-service-logic-connector-twitter/img5.png
[6]: ./media/app-service-logic-connector-twitter/img6.png
[7]: ./media/app-service-logic-connector-twitter/img7.png
[8]: ./media/app-service-logic-connector-twitter/img8.png
 

<!----HONumber=62-->