<properties
   pageTitle="Conector de SAP"
   description="Uso del conector de SAP"
   services="app-service\logic"
   documentationCenter=".net,nodejs,java"
   authors="harishkragarwal"
   manager="dwrede"
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="07/02/2015"
   ms.author="sameerch"/>


# Conector de SAP #

Los conectores pueden utilizarse en aplicaciones lógicas para capturar, procesar o insertar datos como parte de un flujo. Hay escenarios donde puede que necesite trabajar con SAP, que se instala localmente y detrás del firewall. El uso del conector de SAP en su flujo de trabajo le permitir conseguir distintos resultados. Ejemplos:

1.	Exponer una parte de los datos que residen en SAP a través de un front-end de usuario móvil o web.
2.	Publicar datos en SAP después del correspondiente procesamiento.
3.	Extraer datos de SAP para su uso en un proceso empresarial.

Para estos escenarios, deben cumplirse las siguientes condiciones:

1. Creación de una instancia de la aplicación de API del conector de SAP
2. Establecimiento de conectividad híbrida para la comunicación de la aplicación de API con el SAP local
3. Utilización de la aplicación de API creada en una aplicación lógica para lograr el proceso empresarial deseado

## Creación de una instancia de la aplicación de API del conector de SAP ##

Para usar el conector de SAP, deberá crear primero una instancia de la aplicación de API del conector de SAP. Se puede realizar del modo siguiente:

1. Abra Azure Marketplace mediante la opción "+ Nuevo", situada en la parte inferior izquierda del Portal de Azure.
2. Vaya a "Web y móvil > Aplicaciones de API" y busque "Conector de SAP".
3. Configúrelo de la siguiente forma:
	1. Proporcione detalles genéricos, por ejemplo, el nombre, el plan de servicio de aplicación, etc., en la primera hoja.

	2. Como parte de la configuración del paquete, deberá proporcionar las credenciales de SAP. Proporcione también una cadena de conexión de Bus de servicio de Azure. Se usará para establecer conectividad híbrida con el SAP local.

	3. RFC, TRFC, BAPI e IDOC deben configurarse según la necesidad del escenario. Si hay que proporcionar varios valores, se pueden separar con comas.

![][1]

## Configuración la aplicación de API de conector de SAP recién creada ##

Acceda a la aplicación de API que acaba de crear mediante Examinar -> Aplicaciones de API -> <Name of the API App just created>. Verá el siguiente comportamiento. La instalación está incompleta, ya que todavía no se ha establecido la conexión híbrida.

![][2]

Para establecer la conectividad híbrida, haga lo siguiente:

1. Copie la cadena de conexión principal.
2. Haga clic en el vínculo 'Descargar y configurar’.
3. Siga el proceso de instalación que se inicia y especifique la cadena de conexión principal cuando se le pida.
4. Una vez que se completa el proceso de instalación, se muestra un cuadro de diálogo similar al siguiente.

![][3]

Ahora, cuando acceda de nuevo a la aplicación de API que ha creado, verá que el estado de la conexión híbrida se muestra como "conectado".

![][4]

Nota: si desea cambiar a la cadena de conexión secundaria, solo tiene que volver a realizar la configuración híbrida y proporcionar la cadena de conexión secundaria en lugar de la cadena de conexión principal.

## Uso en una aplicación lógica ##

El conector de SAP puede usarse como una acción/paso únicamente en una aplicación lógica.

Al crear o editar una aplicación lógica, elija la aplicación de API del conector de SAP que ha creado anteriormente. Se mostrarán todas las acciones permitidas, entre las que podrá elegir.

![][5]

Tras seleccionar una acción, se mostrará una lista de parámetros de entrada para esa acción. Proporcione los valores adecuados y haga clic en el icono de la marca.

![][6]

El paso/acción aparecerá ahora como configurado en la aplicación lógica. Se mostrarán las salidas de la operación y se podrán utilizar como entradas en un paso posterior.

![][7]

Complete la aplicación lógica para definir el proceso empresarial y, a continuación, ejecútelo para lograr el objetivo deseado.

<!--Image references-->
[1]: ./media/app-service-logic-connector-sap/Create.jpg
[2]: ./media/app-service-logic-connector-sap/BrowseSetupIncomplete.jpg
[3]: ./media/app-service-logic-connector-sap/HybridSetup.jpg
[4]: ./media/app-service-logic-connector-sap/BrowseSetupComplete.jpg
[5]: ./media/app-service-logic-connector-sap/LogicApp1.jpg
[6]: ./media/app-service-logic-connector-sap/LogicApp2.jpg
[7]: ./media/app-service-logic-connector-sap/LogicApp3.jpg

<!---HONumber=July15_HO4-->