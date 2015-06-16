<properties 
	pageTitle="Conector de archivos"
	description="Introducción al conector de archivos"
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

# Uso del conector de archivos en la aplicación lógica #

Las aplicaciones lógicas se pueden desencadenar en función de una variedad de orígenes de datos y ofrecen conectores para obtener y procesar los datos como parte del flujo. 

El conector de archivos permite cargar, descargar y eliminar archivos del sistema de archivos en un equipo host.

## Creación de un conector de archivos para la aplicación lógica ##
Para usar el conector de archivos, deberá crear primero una instancia de la aplicación de API del conector de archivos. Se puede hacer de la forma siguiente:

1.	Abra Azure Marketplace mediante la opción +NUEVO en la parte inferior izquierda del Portal de Azure.
2.	Vaya a "Web y móviles > Azure Marketplace" y busque "Conector de archivos".
3.	Configure el conector de archivos de la siguiente forma:
 
	![][1]
 
	- **Nombre**: asigne un nombre al conector de archivos.
	- **Configuración del paquete**
		- **Carpeta raíz**: especifique la ruta de acceso de la carpeta raíz en el equipo host. P. ej. D:\FileConnectorTest
		- **Cadena de conexión del Bus de servicio**: proporcione una cadena de conexión del Bus de servicio. Asegúrese de que el espacio de nombres del Bus de servicio es de tipo estándar y no básico.
	- **Plan de servicio de la aplicación**: seleccione o cree un plan de Servicio de aplicaciones.
	- **Nivel de precios**: elija un nivel de precios para el conector.
	- **Grupo de recursos**: seleccione o cree un grupo de recursos en el que vaya a estar el conector.
	- **Suscripción**: elija una suscripción en la que desee crear este conector.
	- **Ubicación**: elija la ubicación geográfica en la que desea implementar el conector.

4. Haga clic en Crear. Se creará un nuevo conector de archivos.
5. Una vez creada la instancia de aplicación de la API, vaya a su panel. Para ello, haga clic en Examinar -> Api de aplicaciones -> seleccione la aplicación de API del conector de archivos.
6. Puede ver en el panel del conector de archivos que el modo "Conexión híbrida" muestra "Instalación local incompleta". Para completar la configuración híbrida, abra este panel desde el equipo host a cuyo sistema de archivos debe estar conectado. A continuación, haga clic en el modo "Conexión híbrida". En la hoja "Conexión híbrida" abierta, haga clic en el vínculo "Descargar y configurar" para instalar el Administrador de conexiones híbridas local

	![][2]

7. Se iniciará un instalador que le solicitará la "Cadena de conexión de escucha de relé". Copie y pegue la "Cadena de configuración principal" de la hoja "Conexión híbrida". Haga clic en Instalar.

	![][3]

8. Para comprobar que la instalación híbrida es correcta, vuelva a abrir el panel del conector de archivos. Debería ver el estado de la conexión híbrida como "Conectado".

	![][4]

## Uso del conector de archivos en la aplicación lógica ##
Una vez creada la aplicación de la API, ahora puede usar el conector de archivos como desencadenador/acción para la aplicación lógica. Para ello, necesita lo siguiente:

1.	Cree una nueva aplicación lógica y elija el mismo grupo de recursos que tiene el conector de archivos. Siga las instrucciones para [crear una nueva aplicación lógica].  	
	
2.	Abra "Desencadenadores y acciones" dentro de la aplicación lógica creada para abrir el Diseñador de aplicaciones lógicas y configure el flujo.  	
	
3.	El conector de archivos aparecerá en la sección "Aplicaciones de API en este grupo de recursos" en la galería, en el lado derecho.
 	
4.	Puede quitar la aplicación de la API del conector de archivos en el editor haciendo clic en "Conector de archivos". El conector de archivos muestra un desencadenador y cuatro acciones.
 
	![][5]

6.	Cada uno de ellos muestra algunas propiedades. La imagen siguiente enumera las propiedades para el desencadenador y para la acción Obtener archivo.
 
	![][6]

7. Cuando se han configurado, el desencadenador y la acción pueden utilizarse en el flujo. De forma similar, pueden configurarse también otras acciones.

8. Para usar el conector fuera de una lógica de aplicación, pueden aprovecharse las API de REST expuestas por el conector. Puede ver estas definiciones de API con Examinar -> Aplicación de API -> Conector de archivos. Ahora haga clic en el modo de definición de API en la sección de resumen para ver todas las API expuestas por este conector.

	![][7]

9. Los detalles de las API se pueden encontrar en [Definición de la API del conector de archivos].

<!-- Image reference -->
[1]: ./media/app-service-logic-connector-file/img1.PNG
[2]: ./media/app-service-logic-connector-file/img2.PNG
[3]: ./media/app-service-logic-connector-file/img3.PNG
[4]: ./media/app-service-logic-connector-file/img4.PNG
[5]: ./media/app-service-logic-connector-file/img5.PNG
[6]: ./media/app-service-logic-connector-file/img6.PNG
[7]: ./media/app-service-logic-connector-file/img7.PNG

<!-- Links -->
[Creación de una nueva aplicación lógica]: app-service-logic-create-a-logic-app.md
[Definición de la API del conector de archivos]: https://msdn.microsoft.com/en-US/library/dn936296.aspx


<!--HONumber=52--> 