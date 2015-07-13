<properties 
   pageTitle="Aplicación de API del conector de Bus de servicio de Azure" 
   description="Uso de conector de Bus de servicio de Azure" 
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


# Uso del conector de Bus de servicio de Azure en la aplicación lógica #

Las aplicaciones lógicas se pueden desencadenar en función de una variedad de orígenes de datos y ofrecen conectores para obtener y procesar los datos como parte del flujo.

El conector de Bus de servicio de Azure le permite recibir y enviar mensajes a una cola/tema/suscripción.

## Creación de un conector de Bus de servicio de Azure para la aplicación lógica. ##
Para usar el conector de Bus de servicio de Azure, deberá crear primero crear una instancia de la aplicación de API del conector de Bus de servicio de Azure. Se puede hacer de la forma siguiente:

1.	Abra Azure Marketplace mediante la opción +NUEVO en la parte inferior izquierda del Portal de Azure.
2.	Vaya a “Web y móvil > Aplicaciones de API” y busque “Conector de Bus de servicio de Azure”.
3.	Configure el conector de Bus de servicio de Azure de la forma siguiente:
 
	![][1]
	- **Ubicación**: elija la ubicación geográfica en la que desea implementar el conector
	- **Suscripción**: elija una suscripción en la que desea que se cree este conector  
	- **Grupo de recursos**: seleccione o cree un grupo de recursos donde debe residir el conector 
	- **Plan de hospedaje web**: seleccione o cree un plan de hospedaje web
	- **Nivel de precios**: elija un nivel de precios para el conector 
	- **Nombre**: proporcione un nombre para el conector de Bus de servicio de Azure 
	- **Configuración del paquete** 
		- **Cadena de conexión**: la cadena de conexión del Bus de servicio de Azure. Ejemplo: Endpoint=sb://[namespace].servicebus.windows.net;SharedAccessKeyName=[name];SharedAccessKey=[key] 
		- **Nombre de entidad**: el nombre de la cola o tema 
		- **Nombre de suscripción**: el nombre de la suscripción desde la que se recibirán mensajes.

4.	Haga clic en Crear. Se creará un nuevo conector de Bus de servicio de Azure.
5.	Una vez creada la instancia de aplicación de la API, puede crear una aplicación lógica en el mismo grupo de recursos para usar el conector de Bus de servicio de Azure. 

## Uso del conector de Bus de servicio de Azure en la aplicación lógica ##
Una vez creada la aplicación de la API, ahora puede usar el conector de Bus de servicio de Azure como desencadenador/acción para la aplicación lógica. Para ello, necesita lo siguiente:

1.	Cree una nueva aplicación lógica y elija el mismo grupo de recursos que tiene el conector de Bus de servicio de Azure.
 
	![][2]
2.	Abra "Desencadenadores y acciones" para abrir el Diseñador de aplicaciones lógicas y configure el flujo. 
 
	![][3]
3.	El conector de Bus de servicio de Azure aparecerá en la sección "Aplicaciones de API en este grupo de recursos" en la galería, en el lado derecho.
 
	![][4]
4. Puede quitar la aplicación de la API del conector de Bus de servicio de Azure en el editor haciendo clic en "Conector de Bus de servicio de Azure".
 
5.	Ahora puede usar el conector de Bus de servicio de Azure en el flujo. Puede utilizar el mensaje recuperado desde el desencadenador de Bus de servicio de Azure ("Mensaje disponible") en otras acciones en el flujo.
 
	![][5]
	![][6] 
6.	De forma similar puede usar la acción del Bus de servicio de Azure "Enviar mensaje" para enviar el mensaje

	![][7]
	![][8]


	<!--Image references-->
[1]: ./media/app-service-logic-connector-azureservicebus/img1.PNG
[2]: ./media/app-service-logic-connector-azureservicebus/img2.PNG
[3]: ./media/app-service-logic-connector-azureservicebus/img3.png
[4]: ./media/app-service-logic-connector-azureservicebus/img4.PNG
[5]: ./media/app-service-logic-connector-azureservicebus/img5.PNG
[6]: ./media/app-service-logic-connector-azureservicebus/img6.PNG
[7]: ./media/app-service-logic-connector-azureservicebus/img7.PNG
[8]: ./media/app-service-logic-connector-azureservicebus/img8.PNG
<!--HONumber=62-->