<properties
	pageTitle="Conector de Dropbox"
	description="Introducción al conector de Dropbox"
	authors="anuragdalmia"
	manager="dwrede"
	editor=""
	services="app-service\logic"
	documentationCenter=""/>

<tags
	ms.service="app-service-logic"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/02/2015"
	ms.author="sameerch"/>

# Uso del conector de Dropbox en la aplicación lógica #

Las aplicaciones lógicas se pueden desencadenar en función de una variedad de orígenes de datos y ofrecen conectores para obtener y procesar los datos como parte del flujo.

El conector de Dropbox permite cargar o descargar archivos desde su cuenta de Dropbox.

## Creación de un conector de Dropbox para la aplicación lógica ##
Para usar el conector de Dropbox, deberá crear primero una instancia de la aplicación de API del conector de Dropbox. Se puede hacer de la forma siguiente:

1.	Abra Azure Marketplace mediante la opción +NUEVO en la parte inferior izquierda del Portal de Azure.
2.	Vaya a "Web y móviles --> Azure Marketplace" y busque "Conector de Dropbox".
3.	Configure el conector de Dropbox de la siguiente forma:

	![][1] - **Ubicación**: elija la ubicación geográfica en que desearía implementar el conector. - **Suscripción**: elija una suscripción en que desea crear este conector. - **Grupo de recursos**: seleccione o cree un grupo de recursos en que debe residir el conector. - **Plan del Servicio de aplicaciones**: seleccione o cree un plan del Servicio de aplicaciones. - **Nivel de precios**: elija un nivel de precios para el conector. - **Nombre**: asigne un nombre al conector de Dropbox.
4. Haga clic en Crear. Se creará un nuevo conector de Dropbox.
5. Una vez creada la instancia de aplicación de la API, puede crear una aplicación lógica en el mismo grupo de recursos para usar el conector de Dropbox.

## Uso del conector de Dropbox en la aplicación lógica ##
Una vez creada la aplicación de la API, ahora puede usar el conector de Dropbox como desencadenador/acción para la aplicación lógica. Para ello, necesita lo siguiente:

1.	Cree una nueva aplicación lógica y elija el mismo grupo de recursos que tiene el conector de Dropbox.

	![][2]
2.	Abra "Desencadenadores y acciones" para abrir el Diseñador de aplicaciones lógicas y configure el flujo.

	![][3]
3.	El conector de Dropbox aparecerá en la sección "Aplicaciones de API en este grupo de recursos" en la galería, en el lado derecho.

	![][4]
4.	Puede quitar la aplicación de la API del conector de Dropbox en el editor haciendo clic en "Conector de Dropbox". Haga clic en el botón Autorizar. Proporcione las credenciales de su Dropbox. Haga clic en "Permitir".

	![][5] ![][6] ![][7]
6.	Ahora puede usar el conector de Dropbox en el flujo. Puede usar la acción de Dropbox "Cargar archivo" para cargar un archivo en su cuenta de Dropbox.

	![][8] ![][9]

Configure las propiedades de entrada para la acción "Cargar archivo" de la siguiente manera:

- **Ruta de archivo**: especifique la ruta de acceso del archivo de Dropbox de destino que se va a cargar. Ejemplo: Fotos/imagen.png
- **Contenido**: especifique el contenido del archivo que se va a cargar. A menudo, esto deriva de un paso anterior en la aplicación lógica.
- **Codificación de la transferencia de contenido**: especifique ninguna o Base64.
- **Sobrescribir**: especifique "true" para sobrescribir el archivo si ya existe.


<!-- Image reference -->
[1]: ./media/app-service-logic-connector-dropbox/img1.PNG
[2]: ./media/app-service-logic-connector-dropbox/img2.PNG
[3]: ./media/app-service-logic-connector-dropbox/img3.png
[4]: ./media/app-service-logic-connector-dropbox/img4.png
[5]: ./media/app-service-logic-connector-dropbox/img5.PNG
[6]: ./media/app-service-logic-connector-dropbox/img6.PNG
[7]: ./media/app-service-logic-connector-dropbox/img7.PNG
[8]: ./media/app-service-logic-connector-dropbox/img8.PNG
[9]: ./media/app-service-logic-connector-dropbox/img9.PNG

<!---HONumber=July15_HO4-->