<properties
   pageTitle="Uso del conector de Facebook en la aplicación lógica en Servicio de aplicaciones de Azure"
   description="Uso del conector de Facebook en una aplicación lógica"
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
   ms.date="08/19/2015"
   ms.author="andalmia"/>


# Conector de Facebook

Las aplicaciones lógicas se pueden desencadenar en función de una variedad de orígenes de datos y ofrecen conectores para obtener y procesar los datos como parte del flujo.

- El desencadenador del conector de Facebook recuperará "Nuevo comentario en el muro del usuario" o "Nuevo comentario en la página". Cuando se recupera un nuevo tuit, desencadena una nueva instancia del flujo y pasa los datos recibidos en la solicitud al flujo para el procesamiento.
- Las acciones del conector de Facebook le permite realizar acciones como "Publicar comentario", "Publicar foto" y así sucesivamente. Estas acciones devuelven una respuesta y hacen que estén disponibles las acciones en el flujo para su consumo.

## Acciones y desencadenadores

Desencadenadores | Acciones
--- | ---
<ul><li>Nueva publicación en la escala de tiempo del usuario</li><li>Nueva publicación en la página</li></ul> | <ul><li>Publicar comentario</li><li>Publicar foto</li></ul>



## Creación del conector de Facebook para la aplicación lógica
Para usar el conector de Facebook, deberá crear primero crear una instancia de la aplicación de API del conector de Facebook. Se puede hacer de la forma siguiente:

1. En el panel de inicio de Azure, seleccione **Marketplace**.
2. Busque "Conector de Facebook", selecciónelo y seleccione **Crear**.
3. Escriba el nombre, el plan del Servicio de aplicaciones y otras propiedades: ![][1]
4.	Seleccione **Crear**.


## Uso del conector de Facebook en la aplicación lógica
Una vez creada la aplicación de la API, ahora puede usar el conector de Facebook como desencadenador/acción para la aplicación lógica. Para ello, necesita lo siguiente:

1.	En la aplicación lógica, abra **Desencadenadores y acciones** para abrir el diseñador de Aplicaciones lógicas y configure el flujo: ![][3]
2.	El conector de Facebook se muestra en la galería: ![][4]
3. Seleccione el conector de Facebook para agregar automáticamente en el diseñador. Seleccione **Autorizar**, escriba sus credenciales y seleccione **Permitir**: ![][5] ![][6] ![][7] ![][8]
4.	Seleccione un desencadenador: ![][9]

Ahora puede usar los comentarios recuperados del desencadenador de Facebook en otras acciones. En el siguiente flujo, cada vez que se publica un nuevo comentario en la línea de tiempo de Facebook del usuario, se tuiteará el mismo comentario en la línea de tiempo de Twitter del usuario: ![][10]

De forma parecida, puede crear flujos mediante acciones del conector de Facebook. El siguiente flujo recuperará un nuevo mensaje publicado en el grupo Yammer y publicará el mismo comentario en la página en Facebook administrada por el usuario: ![][11]

> [AZURE.TIP]Para obtener el identificador de página de Facebook o el de grupo de Yammer busque el código numérico en la dirección URL.

## Aplicaciones adicionales del conector
Una vez creado el conector, puede agregarlo a un flujo de trabajo empresarial mediante una aplicación lógica. Consulte [¿Qué son las aplicaciones lógicas?](app-service-logic-what-are-logic-apps.md)

Consulte la referencia de API de REST de Swagger en [Referencia de conectores y aplicaciones de API](http://go.microsoft.com/fwlink/p/?LinkId=529766).

También puede consultar las estadísticas de rendimiento y la seguridad de control para el conector. Consulte [Administración y supervisión de las aplicaciones de API y los conectores integrados](app-service-logic-monitor-your-connectors.md).

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

<!---HONumber=August15_HO8-->