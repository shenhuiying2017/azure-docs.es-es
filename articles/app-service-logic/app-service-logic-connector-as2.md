<properties 
   pageTitle="Conector AS2" 
   description="Conector AS2" 
   services="app-service\logic" 
   documentationCenter=".net,nodejs,java" 
   authors="rajeshramabathiran" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="06/14/2015"
   ms.author="rajram"/>

# Conector AS2
El conector AS2 de Microsoft Azure le permite recibir y enviar mensajes mediante el protocolo de transporte AS2 en comunicaciones negocio a negocio. AS2 significa Applicability Statement 2. Los datos se transportan de forma segura y confiable a través de Internet. La seguridad se consigue mediante el uso de cifrado y certificados digitales.

## Requisitos previos
- Aplicación de API TPM: antes de crear un conector AS2, debe crear un [conector de administración de socios comerciales de BizTalk][1]
- Base de datos SQL de Azure: cada una de las aplicaciones de API B2B requiere su propia base de datos SQL de Azure.
- Contenedor de almacenamiento de blobs de Azure: almacena las propiedades de los mensajes cuando está habilitado el archivado AS2. Si no necesita archivar los mensajes AS2, tampoco necesita un contenedor de almacenamiento. 

## Uso del conector AS2
Para usar el conector AS2, deberá crear primero una instancia de la aplicación de API del conector de AS2. Esta tarea puede realizarse en línea mediante la creación de una aplicación lógica, o bien seleccionando la aplicación de API del conector AS2 en Azure Marketplace.

## Configuración del conector AS2
Los socios comerciales son las entidades implicadas en comunicaciones B2B (negocio a negocio). Cuando dos asociados establecen una relación, esto se conoce como contrato. El contrato definido se basa en la comunicación que los dos socios desean lograr y es específico del protocolo o el transporte.

Los pasos necesarios para crear un contrato de socio comercial se documentan [aquí][2]

## Uso del conector AS2 en la superficie del diseñador de aplicaciones lógicas
El conector AS2 puede usarse como un desencadenador o como una acción.

### Desencadenador
- Inicie el diseñador de flujo de aplicaciones lógicas de Azure.
- Haga clic en el conector AS2 en el panel derecho.

	![Configuración del desencadenador][3]
- Haga clic en ->

	![Opciones del desencadenador][4]
- El conector AS2 expone un solo desencadenador. Seleccione *Recibir y descodificar*

	![Entrada de Recibir y descodificar][5]
- Este desencadenador no tiene ninguna entrada. Haga clic en ->

	![Configuración de Recibir y descodificar][6]
- Como parte del resultado, el conector devuelve la carga de AS2, así como los metadatos específicos de AS2.

### Acción
- Haga clic en el conector AS2 en el panel derecho.

	![Configuración de la acción][7]
- Haga clic en ->

	![Lista de acciones][8]
- El conector AS2 solo admite una acción. Seleccione *Codificar y enviar*

	![Entrada de Codificar y enviar][9]
- Proporcione las entradas para la acción y configúrela.

	![Codificar y enviar configurados][10]

<table>
	<tr>
		<th>Parámetro</th>
		<th>Tipo</th>
		<th>Descripción del parámetro</th>
	</tr>
	<tr>
		<td>Carga</td>
		<td>objeto</td>
		<td>Carga</td>
	</tr>
	<tr>
		<td>AS2 desde</td>
		<td>cadena</td>
		<td>AS2 desde</td>
	</tr>
	<tr>
		<td>AS2 hasta</td>
		<td>cadena</td>
		<td>AS2 hasta</td>
	</tr>
	<tr>
		<td>URL de asociado</td>
		<td>cadena</td>
		<td>URL de asociado</td>
	</tr>
	<tr>
		<td>Habilitar archivado</td>
		<td>boolean</td>
		<td>Habilitar archivado</td>
	</tr>
</table>

La acción devuelve un código de respuesta HTTP 200 de finalización satisfactoria.

## Aplicaciones adicionales del conector
Una vez creado el conector, puede agregarlo a un flujo empresarial mediante una aplicación lógica. Consulte [¿Qué son las aplicaciones lógicas?](app-service-logic-what-are-logic-apps.md)

También puede consultar las estadísticas de rendimiento y la seguridad de control para el conector. Consulte [Administración y supervisión de conectores y aplicaciones de API](../app-service-api/app-service-api-manage-in-portal.md).

<!--References -->
[1]: app-service-logic-connector-tpm.md
[2]: app-service-logic-create-a-trading-partner-agreement.md
[3]: ./media/app-service-logic-connector-as2/TriggerSettings.PNG
[4]: ./media/app-service-logic-connector-as2/TriggerOptions.PNG
[5]: ./media/app-service-logic-connector-as2/ReceiveAndDecodeInput.PNG
[6]: ./media/app-service-logic-connector-as2/ReceiveAndDecodeConfigured.PNG
[7]: ./media/app-service-logic-connector-as2/ActionSettings.PNG
[8]: ./media/app-service-logic-connector-as2/ListOfActions.PNG
[9]: ./media/app-service-logic-connector-as2/EncodeAndSendInput.PNG
[10]: ./media/app-service-logic-connector-as2/EncodeAndSendConfigured.PNG
<!--HONumber=62-->