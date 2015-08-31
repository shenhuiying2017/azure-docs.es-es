<properties
   pageTitle="Conector y agente de escucha de HTTP"
   description="Uso de la acción HTTP y del agente de escucha HTTP en la aplicación lógica"
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
   ms.author="prkumar"/>


#Uso de la acción HTTP y del agente de escucha HTTP en la aplicación lógica#

Las aplicaciones lógicas se pueden desencadenar en función de una variedad de orígenes de datos y ofrecen conectores para obtener y procesar los datos como parte del flujo. Sin embargo, hay algunos escenarios en los que puede que necesite trabajar con conexiones HTTP directas, entre los que se incluyen los siguientes:

1.	Desarrollar una aplicación lógica que admita un front-end interactivo de usuario web o móvil.
2.	Obtener y procesar los datos de un servicio web que no disponga de un conector disponible directamente.
3.	Realizar acciones ya expuestas como un servicio web, pero no disponibles como una aplicación de API.


Para estos escenarios, la galería proporciona dos opciones:

1. **Escucha HTTP**: este conector funciona como desencadenador y escucha las solicitudes HTTP en un extremo configurado. Cuando se recibe una llamada en el extremo configurado, desencadena una nueva instancia del flujo y pasa los datos recibidos en la solicitud al flujo para el procesamiento. También puede configurarse para responder automáticamente a la solicitud entrante cuando haya comenzado el flujo, o para construir una respuesta basada en la ejecución de flujo y enviar una respuesta al llamador.
2. **Acción HTTP**: le permite configurar una solicitud web a cualquier extremo disponible en Internet, recibir una respuesta y hacer que esté disponible para las acciones adicionales en el flujo que consumir.

## Creación de un agente de escucha de HTTP para la aplicación lógica
Un conector puede crearse dentro de una aplicación lógica o directamente desde Azure Marketplace. Para crear un conector desde Marketplace:

1. En el panel de inicio de Azure, seleccione **Marketplace**.
2. Busque "HTTP", seleccione Escucha HTTP y, después, seleccione **Crear**.
3.	Configure la escucha HTTP de la siguiente forma: ![][1]

4.	Al establecer la configuración del paquete, verá la siguiente opción sobre si el agente de escucha debe responder automáticamente o si es necesario que envíe una respuesta explícita. Establezca este valor en **False** para enviar su propia respuesta: ![][2]

5.	Haga clic en **Aceptar** para crearla.
6.	Una vez creada la instancia de aplicación de la API, abra la configuración para establecer la seguridad. La escucha HTTP actualmente admite la autenticación básica. Puede configurar esto con la opción de seguridad al abrir la escucha HTTP: ![][3]
  
	**Problema conocido** *La configuración de la seguridad muestra "Ninguno" como valor predeterminado, aunque no está definido. Debe cambiar la configuración a Básico y volver a Ninguno antes de guardarlo para asegurarse de que el agente de escucha de HTTP está configurado correctamente.*

7. Por último, establezca la configuración de seguridad de la aplicación de API en Público (Anónimo) para permitir que los clientes externos accedan al extremo. Esta configuración está disponible en "Todas las configuraciones > Configuración de la aplicación" de la aplicación de API de la escucha HTTP: ![][10]

Una vez realizado este paso, puede crear una aplicación lógica para usar la escucha HTTP.

## Uso del agente de escucha de HTTP en la aplicación lógica
Una vez creada la aplicación de la API, ahora puede usar el agente de escucha de HTTP como desencadenador para la aplicación lógica. Para ello, necesita lo siguiente:

4.	Cree una nueva aplicación lógica.
5.	Abra "Desencadenadores y acciones" para abrir el Diseñador de aplicaciones lógicas y configure el flujo. La escucha HTTP se muestra en la galería. Selecciónelo.
6.	Ahora puede establecer el método HTTP y la dirección URL relativa en la que necesita que la escucha desencadene el flujo: ![][4] ![][5]

7.	Para obtener el URI completo, haga doble clic en la escucha de HTTP para ver sus valores de configuración y copie la dirección URL para el "Host" de la aplicación de la API: ![][6]
8.	Ahora puede usar los datos recibidos en la solicitud HTTP en otras acciones del flujo de la siguiente forma: ![][7] ![][8]
9.	Por último, para enviar una respuesta, agregue otro agente de escucha de HTTP y seleccione la acción Enviar respuesta HTTP. Establezca el identificador de solicitud en el RequestID obtenido de la escucha HTTP y complete el cuerpo de respuesta y el estado HTTP que desee devolver: ![][9]

## Uso de la acción de HTTP
Las aplicaciones lógicas admiten de forma nativa la acción de HTTP y no es necesario crear una aplicación de API primero para poder usarla. Puede insertar una acción de HTTP en cualquier momento en la aplicación lógica y elegir el URI, los encabezados y el cuerpo de la llamada. La acción HTTP admite varias opciones para la seguridad del cliente. Para utilizar estas opciones, consulte el artículo [aquí](http://aka.ms/logicapphttpauth).

El resultado de la acción de HTTP son los encabezados y el cuerpo, que se pueden usar en el flujo similar al de cómo se consume el resultado de otras acciones y conectores.

## Aplicaciones adicionales del conector
Una vez creado el conector, puede agregarlo a un flujo de trabajo empresarial mediante una aplicación lógica. Consulte [¿Qué son las aplicaciones lógicas?](app-service-logic-what-are-logic-apps.md)

Consulte la referencia de API de REST de Swagger en [Referencia de conectores y aplicaciones de API](http://go.microsoft.com/fwlink/p/?LinkId=529766).

También puede consultar las estadísticas de rendimiento y la seguridad de control para el conector. Consulte [Administración y supervisión de las aplicaciones de API y los conectores integrados](app-service-logic-monitor-your-connectors.md).

<!--Image references-->
[1]: ./media/app-service-logic-connector-http/1.png
[2]: ./media/app-service-logic-connector-http/2.png
[3]: ./media/app-service-logic-connector-http/3.png
[4]: ./media/app-service-logic-connector-http/4.png
[5]: ./media/app-service-logic-connector-http/5.png
[6]: ./media/app-service-logic-connector-http/6.png
[7]: ./media/app-service-logic-connector-http/7.png
[8]: ./media/app-service-logic-connector-http/8.png
[9]: ./media/app-service-logic-connector-http/9.png
[10]: ./media/app-service-logic-connector-http/10.png

<!---HONumber=August15_HO8-->