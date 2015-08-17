<properties
   pageTitle="Uso del conector de Bus de servicio de Azure en el Servicio de aplicaciones de Azure"
   description="Uso del conector de Bus de servicio de Azure"
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
   ms.date="06/29/2015"
   ms.author="andalmia"/>


# Conector de Bus de servicio de Azure

El conector de Bus de servicio de Azure le permite enviar mensajes desde entidades de Bus de servicio, como colas y temas, y recibir mensajes de entidades de Bus de servicio, como colas y suscripciones.

## Acciones y desencadenadores
Los desencadenadores son eventos que se producen. Por ejemplo, cuando se actualiza un pedido o cuando se agrega un cliente nuevo. Un acción es el resultado del desencadenador. Por ejemplo, cuando se coloca un pedido o un mensaje nuevo en una cola o cuando se envía una alerta o un mensaje.

El conector de Bus de servicio de Azure puede usarse como un desencadenador o una acción en una aplicación lógica y es compatible con los datos en formato JSON y XML.

El conector de Bus de servicio de Azure tiene disponibles los desencadenadores y las acciones siguientes:

Desencadenadores | Acciones
--- | ---
Mensaje disponible | Enviar mensaje

## Creación del conector de Bus de servicio de Azure
Un conector puede crearse dentro de una aplicación lógica o directamente desde Azure Marketplace. Para crear un conector desde Marketplace:

1. En el panel de inicio de Azure, seleccione **Marketplace**.
2. Seleccione **Aplicaciones de API** y busque "Conector de Bus de servicio de Azure".
3. Escriba el nombre, el plan del Servicio de aplicaciones y otras propiedades: 
<br/> 
![][1]

4. Escriba la siguiente configuración del paquete:

	Nombre | Descripción
--- | ---
Cadena de conexión | La cadena de conexión para Bus de servicio de Azure. Por ejemplo, escriba: *Endpoint=sb://[namespace].servicebus.windows.net;SharedAccessKeyName=[name];SharedAccessKey=[key]*.
Nombre de entidad | Escriba el nombre de la cola o del tema.
Subscription Name | Escriba el nombre de la suscripción desde la que se van a recibir los mensajes.

5. Haga clic en **Crear**.

Una vez creado el conector, puede agregarlo a una aplicación lógica en el mismo grupo de recursos.

## Uso del conector de Bus de servicio en la aplicación lógica
Una vez creado el conector, ya puede usar el conector de Bus de servicio de Azure como desencadenador o acción para la aplicación lógica. Para ello, siga estos pasos:

1.	Cree una nueva aplicación lógica y elija el mismo grupo de recursos que tiene el conector de Bus de servicio de Azure: 
<br/> 
![][2]

2.	Abra “Desencadenadores y acciones” para abrir el diseñador de aplicaciones lógicas y configurar el flujo de trabajo: 
<br/> 
![][3]

3. El conector de Bus de servicio de Azure aparecerá en la sección "Aplicaciones de API en este grupo de recursos" en la galería, en el lado derecho: 
<br/> 
![][4]

4. Puede quitar el conector de Bus de servicio de Azure en el editor haciendo clic en "Conector de Bus de servicio de Azure".

5.	Ahora puede usar el conector de Bus de servicio de Azure en el flujo de trabajo. Puede utilizar el mensaje recuperado desde el desencadenador de Bus de servicio de Azure ("Mensaje disponible") en otras acciones del flujo: 
<br/> 
![][5] 
<br/> 
![][6]

También puede usar la acción “Enviar mensaje” del Bus de servicio de Azure: 
<br/> 
![][7] 
<br/> 
![][8]

## Aplicaciones adicionales del conector
Una vez creado el conector, puede agregarlo a un flujo de trabajo empresarial mediante una aplicación lógica. Consulte [¿Qué son las aplicaciones lógicas?](app-service-logic-what-are-logic-apps.md)

Cree las aplicaciones de API mediante las API de REST. Consulte [Referencia sobre conectores y aplicaciones de API](http://go.microsoft.com/fwlink/p/?LinkId=529766).

También puede consultar las estadísticas de rendimiento y la seguridad de control para el conector. Consulte [Administración y supervisión de las aplicaciones de API y los conectores integrados](app-service-logic-monitor-your-connectors.md).


	<!--Image references-->
[1]: ./media/app-service-logic-connector-azureservicebus/img1.PNG
[2]: ./media/app-service-logic-connector-azureservicebus/img2.PNG
[3]: ./media/app-service-logic-connector-azureservicebus/img3.png
[4]: ./media/app-service-logic-connector-azureservicebus/img4.PNG
[5]: ./media/app-service-logic-connector-azureservicebus/img5.PNG
[6]: ./media/app-service-logic-connector-azureservicebus/img6.PNG
[7]: ./media/app-service-logic-connector-azureservicebus/img7.PNG
[8]: ./media/app-service-logic-connector-azureservicebus/img8.PNG

<!---HONumber=August15_HO6-->