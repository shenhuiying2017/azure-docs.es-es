<properties
	pageTitle="Uso del conector de Dropbox en la aplicación lógica en Servicio de aplicaciones de Azure"
	description="cómo comenzar con el conector de Dropbox en una aplicación lógica en el Servicio de aplicaciones"
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
	ms.date="08/19/2015"
	ms.author="sameerch"/>

# Conector de Dropbox

El conector de Dropbox permite cargar o descargar archivos desde su cuenta de Dropbox.

Las aplicaciones lógicas se pueden desencadenar en función de una variedad de orígenes de datos y ofrecen conectores para obtener y procesar los datos como parte del flujo.

## Acciones y desencadenadores

Un desencadenador inicia una nueva instancia en función de un evento específico, como la llegada de un nuevo mensaje. Una acción es el resultado, por ejemplo después de recibir un nuevo mensaje cargar el archivo en Dropbox.

El conector de Dropbox puede usarse como acción en una aplicación lógica y es compatible con datos en formato JSON y XML. El conector de Chatter dispone de los siguientes desencadenadores y acciones:

Desencadenadores | Acciones
--- | ---
None | <ul><li>Eliminar archivo</li><li>Obtener archivo</li><li>Cargar archivo</li><li>Enumerar archivos</li></ul>


## Creación de un conector de Dropbox para la aplicación lógica
Un conector puede crearse dentro de una aplicación lógica o directamente desde Azure Marketplace. Para crear un conector desde Marketplace:

1. En el panel de inicio de Azure, seleccione **Marketplace**.
2. Busque "Conector de Dropbox", selecciónelo y seleccione **Crear**.
3. Escriba el nombre, el plan del Servicio de aplicaciones y otras propiedades: 
	![][1]
	- **Ubicación**: elija la ubicación geográfica en la que desea implementar el conector.
	- **Suscripción**: elija una suscripción en la que desee crear este conector.
	- **Grupo de recursos**: seleccione o cree un grupo de recursos en el que vaya a estar el conector.
	- **Plan del Servicio de aplicaciones**: seleccione o cree un plan de hospedaje web
	- **Nivel de precios**: elija un nivel de precios para el conector.
	- **Nombre**: asigne un nombre al conector de Dropbox  
4. Seleccione **Crear**.


## Uso del conector de Dropbox en la aplicación lógica
Una vez creada la aplicación de la API, ahora puede usar el conector de Dropbox como desencadenador/acción para la aplicación lógica. Para ello, siga estos pasos:

1.	En la aplicación lógica, abra **Desencadenadores y acciones** para abrir el diseñador de Aplicaciones lógicas y configure el flujo: 
	![][3]
2.	El conector de Dropbox se muestra en la galería: 
	![][4]
3.	Seleccione el conector de Dropbox para agregar automáticamente en el diseñador. Seleccione **Autorizar**, escriba sus credenciales y seleccione **Permitir**: 
	![][5] 
	![][6] 
	![][7]

Ahora puede usar el conector de Dropbox en el flujo. Puede usar la acción de Dropbox "Cargar archivo" para cargar un archivo en su cuenta de Dropbox: 
	![][8] 
	![][9]

Configure las propiedades de entrada para la acción "Cargar archivo" de la siguiente manera:

- **Ruta de archivo**: especifique la ruta de acceso del archivo de Dropbox de destino que se va a cargar. Ejemplo: Fotos/imagen.png
- **Contenido**: especifique el contenido del archivo que se va a cargar. A menudo, esto deriva de un paso anterior en la aplicación lógica.
- **Codificación de la transferencia de contenido**: especifique ninguna o Base64.
- **Sobrescribir**: especifique "true" para sobrescribir el archivo si ya existe.

## Aplicaciones adicionales del conector
Una vez creado el conector, puede agregarlo a un flujo de trabajo empresarial mediante una aplicación lógica. Consulte [¿Qué son las aplicaciones lógicas?](app-service-logic-what-are-logic-apps.md)

Consulte la referencia de API de REST de Swagger en [Referencia de conectores y aplicaciones de API](http://go.microsoft.com/fwlink/p/?LinkId=529766).

También puede consultar las estadísticas de rendimiento y la seguridad de control para el conector. Consulte [Administración y supervisión de las aplicaciones de API y los conectores integrados](app-service-logic-monitor-your-connectors.md).

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

<!---HONumber=August15_HO8-->