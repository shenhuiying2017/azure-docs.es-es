<properties
   pageTitle="Aplicación de API del conector de Facebook"
   description="Uso del conector de Facebook"
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
   ms.date="07/02/2015"
   ms.author="andalmia"/>


# Uso del conector de Facebook en la aplicación lógica #

Las aplicaciones lógicas se pueden desencadenar en función de una variedad de orígenes de datos y ofrecen conectores para obtener y procesar los datos como parte del flujo.

El conector de Facebook permite recuperar "Nuevo comentario en el muro del usuario", "Nuevo comentario en la página", "Publicar comentario", "Publicar foto" y así sucesivamente de su cuenta de Facebook.

- El desencadenador del conector de Facebook recuperará "Nuevo comentario en el muro del usuario" o "Nuevo comentario en la página". Cuando se recupera un nuevo tuit, desencadena una nueva instancia del flujo y pasa los datos recibidos en la solicitud al flujo para el procesamiento.
- Las acciones del conector de Facebook le permite realizar acciones como "Publicar comentario", "Publicar foto" y así sucesivamente. Estas acciones devuelven una respuesta y hacen que estén disponibles las acciones en el flujo para su consumo.

## Creación de un conector de Facebook para la aplicación lógica ##
Para usar el conector de Facebook, deberá crear primero crear una instancia de la aplicación de API del conector de Facebook. Se puede hacer de la forma siguiente:

1.	Abra Azure Marketplace mediante la opción +NUEVO en la parte inferior izquierda del Portal de Azure.
2.	Diríjase a "Internet y móvil > Aplicaciones API" y busque "Conector de Facebook".
3.	Configure el conector de Facebook de la siguiente forma:

	![][1]
4.	Haga clic en Aceptar para crear.
5.	Una vez creada la instancia de aplicación de la API, puede crear una aplicación lógica en el mismo grupo de recursos para usar el conector de Facebook.
	- La instancia de la aplicación de API de del conector de Facebook también puede crearse desde la aplicación lógica.
	- Abra el editor de aplicación lógica y haga clic en el conector de Facebook disponible en la galería en el lado derecho.
	- De esta forma, se creará una instancia de aplicación API del conector de Facebook en el mismo grupo de recursos en el que se ha creado la aplicación lógica.


## Uso del conector de Facebook en la aplicación lógica ##
Una vez creada la aplicación de la API, ahora puede usar el conector de Facebook como desencadenador/acción para la aplicación lógica. Para ello, necesita lo siguiente:

1.	Cree una nueva aplicación lógica y elija el mismo grupo de recursos que tenga el conector de Facebook.

	![][2]
2.	Abra "Desencadenadores y acciones" para abrir el Diseñador de aplicaciones lógicas y configure el flujo.

	![][3]
3.	El conector de Facebook aparecería en la sección "Usados recientemente" en la galería de la derecha. Selecciónelo.

	![][4]
4.	Puede soltar la aplicación de la API del conector de Facebook en el editor haciendo clic en "Conector de Facebook" en "Usados recientemente" en la galería de la derecha. Haga clic en el botón Autorizar. Proporcione las credenciales de Facebook.

	![][5]
5.	Permiso de "Aplicaciones lógicas de Servicios de aplicaciones de Azure"

	![][6] ![][7] ![][8]
6.	Seleccione un desencadenador.

	![][9]
7.	Ahora puede usar los comentarios recuperados del desencadenador de Facebook en otras acciones. En el siguiente flujo, cada vez que se publica un nuevo comentario en el muro de Facebook del usuario, se tuiteará el mismo comentario en el muro de Twitter del usuario.

	![][10]
8.	De forma parecida, puede crear flujos mediante acciones del conector de Facebook. El siguiente flujo recuperará un nuevo mensaje publicado en el grupo Yammer y publicará el mismo comentario en la página en Facebook administrada por el usuario.

	![][11]

**Sugerencia** : para obtener el identificador de página de Facebook o el de grupo de Yammer busque los códigos numéricos en la dirección URL.

<!--Image references-->
[1]: ./media/app-service-logic-connector-facebook/img1.png
[2]: ./media/app-service-logic-connector-facebook/img2.png
[3]: ./media/app-service-logic-connector-facebook/img3.png
[4]: ./media/app-service-logic-connector-facebook/img4.png
[5]: ./media/app-service-logic-connector-facebook/img5.png
[6]: ./media/app-service-logic-connector-facebook/img6.png
[7]: ./media/app-service-logic-connector-facebook/img7.png
[8]: ./media/app-service-logic-connector-facebook/img8.png
[9]: ./media/app-service-logic-connector-facebook/img9.png
[10]: ./media/app-service-logic-connector-facebook/img10.png
[11]: ./media/app-service-logic-connector-facebook/img11.png

<!---HONumber=July15_HO4-->