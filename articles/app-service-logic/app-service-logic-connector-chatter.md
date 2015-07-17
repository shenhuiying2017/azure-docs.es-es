<properties
   pageTitle="Aplicación de API del conector de Chatter"
   description="Uso del conector de Chatter"
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
   ms.date="03/31/2015"
   ms.author="adgoda"/>


# Uso del conector de Chatter en la aplicación lógica #

Las aplicaciones lógicas se pueden desencadenar en función de una variedad de orígenes de datos y ofrecen conectores para obtener y procesar los datos como parte del flujo.

El conector de Chatter le permite conectarse a Chatter y realizar diversas acciones como enviar mensajes, realizar búsquedas y un desencadenador para recuperar los mensajes nuevos.

## Creación de un conector de Chatter para la aplicación lógica ##
Para usar el conector de Chatter, deberá crear primero una instancia de la aplicación de API del conector de Chatter. Se puede hacer de la forma siguiente:

1.	Abra Azure Marketplace mediante la opción +NUEVO en la parte inferior izquierda del Portal de Azure.
2.	Vaya a "Web y móvil > Aplicaciones de API" y busque "Conector de Chatter".
3.	Configure el conector de Chatter de la siguiente forma:

	![][1]
	- **Ubicación**: elija la ubicación geográfica donde desea implementar el conector
	- **Suscripción**: elija una suscripción en la que desea crear este conector
	- **Grupo de recursos**: seleccione o cree un grupo de recursos donde debe residir el conector
	- **Plan de hospedaje web**: seleccione o cree un plan de hospedaje web
	- **Nivel de precios**: elija un nivel de precios para el conector
	- **Nombre**: signe un nombre al conector de Chatter.

4.	Haga clic en Crear. Se creará un nuevo conector de Chatter.
5.	Una vez creada la instancia de aplicación de la API, puede crear una aplicación lógica en el mismo grupo de recursos para usar el conector de Chatter.

## Uso del conector de Chatter en la aplicación lógica ##
Una vez creada la aplicación de la API, ahora puede usar el conector de Chatter como desencadenador/acción para la aplicación lógica. Para ello, necesita lo siguiente:

1.	Cree una nueva aplicación lógica y elija el mismo grupo de recursos que tiene el conector de Chatter.

	![][2]
2.	Abra "Desencadenadores y acciones" para abrir el Diseñador de aplicaciones lógicas y configure el flujo.

3.	El conector de Chatter aparecerá en la sección "Aplicaciones de API en este grupo de recursos" en la galería, en el lado derecho.

	![][4]
4. Puede quitar la aplicación de la API del conector de Chatter en el editor haciendo clic en "Conector de Chatter". Haga clic en el botón Autorizar. Proporcione sus credenciales. Haga clic en "Permitir".

	![][5]
	![][6]
	![][7]
5.	Ahora puede usar el conector de Chatter en el flujo. Puede usar el nuevo mensaje recuperado desde el desencadenador de Chatter ("Mensaje nuevo") en otras acciones del flujo. Configure las propiedades de entrada para el desencadenador de Chatter de la forma siguiente:
	- **Identificador de grupo**: especifique el identificador del grupo desde el que se va a recuperar el nuevo mensaje. Si no se proporciona el identificador de grupo, se recuperará el nuevo mensaje de la fuente del usuario.

  ![][8]
  ![][9]

6. En la forma similar puede usar la acción Chatter en el flujo para enviar un mensaje mediante la selección de la acción "Enviar mensaje". Configure las propiedades de entrada de la acción "Publicar mensaje" de la siguiente manera:
	- **Texto del mensaje**: contenido de texto del mensaje que se va a enviar.
	- **Id. de grupo**: especifique el identificador del grupo al que se va a enviar el nuevo mensaje. Si no se proporciona el identificador de grupo, el mensaje se publicará en la fuente del usuario.
	- 	**Nombre de archivo**: el nombre del archivo que se va a adjuntar al mensaje
	- 	**Datos de contenido**: datos de contenido de los datos adjuntos
	- 	**Tipo de contenido**: tipo de contenido de los datos adjuntos
	- 	**Codificación de transferencia de contenido**: codificación de transferencia del contenido de los datos adjuntos ("none"|"base64")
	- 	**Menciones**: la matriz de los nombres de usuario que se van a etiquetar en este mensaje
	- 	**Hashtags**: la matriz de los hashtags que se van a enviar junto con el mensaje

	![][10]
	![][11]

	<!--Image references-->
[1]: ./media/app-service-logic-connector-chatter/img1.PNG
[2]: ./media/app-service-logic-connector-chatter/img2.PNG
[3]: ./media/app-service-logic-connector-chatter/img3.png
[4]: ./media/app-service-logic-connector-chatter/img4.png
[5]: ./media/app-service-logic-connector-chatter/img5.PNG
[6]: ./media/app-service-logic-connector-chatter/img6.PNG
[7]: ./media/app-service-logic-connector-chatter/img7.PNG
[8]: ./media/app-service-logic-connector-chatter/img8.PNG
[9]: ./media/app-service-logic-connector-chatter/img9.PNG
[10]: ./media/app-service-logic-connector-chatter/img10.PNG
[11]: ./media/app-service-logic-connector-chatter/img11.PNG
 

<!----HONumber=62-->