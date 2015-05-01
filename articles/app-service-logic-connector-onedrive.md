<properties 
	pageTitle="Conector de OneDrive"
	description="Introducción al conector de OneDrive"
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
	ms.date="04/09/2015"
	ms.author="andalmia"/>

# Uso del conector de OneDrive en la aplicación lógica

Las aplicaciones lógicas se pueden desencadenar en función de una variedad de orígenes de datos y ofrecen conectores para obtener y procesar los datos como parte del flujo. 

El conector de OneDrive permite cargar, descargar o eliminar archivos desde su cuenta de OneDrive.

## Creación de un conector de OneDrive para la aplicación lógica ##
Para usar el conector de OneDrive, deberá crear primero una instancia de la aplicación de API del conector de OneDrive. Se puede hacer de la forma siguiente:

1.	Abra Azure Marketplace mediante la opción +NUEVO en la parte inferior izquierda del Portal de Azure.
2.	Vaya a "Web y móviles > Azure Marketplace" y busque "Conector de OneDrive".
3.	Configure el conector de OneDrive de la siguiente forma:
 
	![][1] 
	- **Nombre**: asigne un nombre al conector de OneDrive.
	- **Plan de servicio de la aplicación**: seleccione o cree un plan de Servicio de aplicaciones.
	- **Nivel de precios**: elija un nivel de precios para el conector.
	- **Grupo de recursos**: seleccione o cree un grupo de recursos en el que vaya a estar el conector.
	- **Suscripción**: elija una suscripción en la que desee crear este conector.
	- **Ubicación**: elija la ubicación geográfica en la que desea implementar el conector.

4. Haga clic en Crear. Se creará un nuevo conector de OneDrive.
5. Una vez creada la instancia de aplicación de la API, puede crear una aplicación lógica en el mismo grupo de recursos para usar el conector de OneDrive.

## Uso del conector de OneDrive en la aplicación lógica ##
Una vez creada la aplicación de la API, ahora puede usar el conector de OneDrive como desencadenador/acción para la aplicación lógica. Para ello, necesita lo siguiente:

1.	Cree una nueva aplicación lógica y elija el mismo grupo de recursos que tiene el conector de OneDrive. Siga las instrucciones para [crear una nueva aplicación lógica].  	
	
2.	Abra "Desencadenadores y acciones" dentro de la aplicación lógica creada para abrir el Diseñador de aplicaciones lógicas y configure el flujo.  	
	
3.	El conector de OneDrive aparecerá en la sección "Aplicaciones de API en este grupo de recursos" en la galería, en el lado derecho.
 
	![][2]
4.	Puede quitar la aplicación de la API del conector de OneDrive en el editor haciendo clic en "Conector de OneDrive". Haga clic en el botón Autorizar. Proporcione la credenciales de Microsoft (si no ha iniciado sesión automáticamente). Haga clic en "Sí" para permitir el acceso.
 
	![][3]
	![][4]
	
5.	Ahora puede usar el conector de OneDrive en el flujo. Actualmente, los desencadenadores no están disponibles en el conector de OneDrive. Las acciones disponibles son: Obtener archivo, Cargar un archivo, Eliminar archivo y Listar archivos.
 
	![][5]

6.	Veamos el escenario de "Cargar archivo". Puede usar la acción de OneDrive "Cargar archivo" para cargar un archivo en su cuenta de OneDrive.
 
	![][6]

	Configure las propiedades de entrada para la acción "Cargar archivo" de la siguiente manera:

 - **Ruta de archivo**: especifique la ruta de acceso del archivo que se va a cargar.
 - **Contenido**: especifique el contenido del archivo que se va a cargar.
 - **Codificación de la transferencia de contenido**: especifique ninguna o Base64.
 - **Sobrescribir**: especifique "true" para sobrescribir el archivo si ya existe. Se trata de una propiedad avanzada.

7. Cuando se han configurado, la acción "Cargar un archivo" se configura y se puede utilizar en el flujo. De forma similar, pueden configurarse también otras acciones.

8. Para usar el conector fuera de una lógica de aplicación, pueden aprovecharse las API de REST expuestas por el conector. Puede ver estas definiciones de API con Examinar -> Aplicación de API -> Conector de OneDrive. Ahora haga clic en el modo de definición de API en la sección de resumen para ver todas las API expuestas por este conector.

	![][7]

9. Los detalles de las API se pueden encontrar en [Definición de la API de OneDrive].

<!-- Image reference -->
[1]: ./media/app-service-logic-connector-onedrive/img1.PNG
[2]: ./media/app-service-logic-connector-onedrive/img2.PNG
[3]: ./media/app-service-logic-connector-onedrive/img3.PNG
[4]: ./media/app-service-logic-connector-onedrive/img4.PNG
[5]: ./media/app-service-logic-connector-onedrive/img5.PNG
[6]: ./media/app-service-logic-connector-onedrive/img6.PNG
[7]: ./media/app-service-logic-connector-onedrive/img7.PNG

<!-- Links -->
[crear una nueva aplicación lógica]: app-service-logic-create-a-logic-app.md
[Definición de la API de OneDrive]: https://msdn.microsoft.com/es-es/library/dn974227.aspx


<!--HONumber=52-->