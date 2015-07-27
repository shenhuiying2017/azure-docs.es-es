<properties
   pageTitle="Codificador JSON de BizTalk"
   description="Codificador JSON de BizTalk"
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

#Codificador JSON de BizTalk
El conector de codificación y descodificación JSON de BizTalk ayuda a la interoperabilidad de las aplicaciones entre datos JSON y XML. Puede convertir una instancia JSON dada a XML y viceversa.

##Uso del codificador JSON de BizTalk
Para usar el codificador JSON de BizTalk, primero debe crear una instancia de la aplicación de API del codificador JSON de BizTalk. Esta operación puede realizarse en línea, mediante la creación de una aplicación lógica, o bien seleccionando la aplicación de API del codificador JSON de BizTalk en Azure Marketplace.

##Uso del codificador JSON de BizTalk en la superficie del diseñador de aplicaciones lógicas
Para crear una aplicación lógica, siga los pasos que se indican en [Creación de una aplicación lógica]. El codificador JSON de BizTalk puede usarse como una acción. No tiene ningún desencadenador.

###Acción
- Haga clic en el codificador JSON de BizTalk en el panel derecho

	![Configuración de la acción][3]
- Haga clic en ->

	![Lista de acciones][4]
- El codificador JSON de BizTalk admite dos acciones. Seleccione *Xml to JSON* (XML a JSON)

	![Entrada de XML a JSON][5]
- Proporcione las entradas para la acción y configúrela.

	![Codificar y enviar configurados][6]

<table>
	<tr>
		<th>Parámetro</th>
		<th>Tipo</th>
		<th>Descripción del parámetro</th>
	</tr>
	<tr>
		<td>XML de entrada</td>
		<td>objeto</td>
		<td>Contenido de XML de entrada</td>
	</tr>
	<tr>
		<td>Quitar sobre exterior</td>
		<td>cadena</td>
		<td>Marca establecida para quitar el nodo raíz del contenido XML</td>
	</tr>
</table>


La acción devuelve una representación json del contenido de entrada.

## Aplicaciones adicionales del conector
Una vez creado el conector, puede agregarlo a un flujo empresarial mediante una aplicación lógica. Consulte [¿Qué son las aplicaciones lógicas?](app-service-logic-what-are-logic-apps.md)

También puede consultar las estadísticas de rendimiento y la seguridad de control para el conector. Consulte [Administración y supervisión de conectores y aplicaciones de API](../app-service-api/app-service-api-manage-in-portal.md).

<!--References -->
[1]: app-service-logic-connector-tpm
[2]: app-service-logic-create-a-trading-partner-agreement
[3]: ./media/app-service-logic-json-encoder/ActionSettings.PNG
[4]: ./media/app-service-logic-json-encoder/ListOfActions.PNG
[5]: ./media/app-service-logic-json-encoder/EncodeInput.PNG
[6]: ./media/app-service-logic-json-encoder/EncodeConfigured.PNG

<!--Links -->
[Creación de una aplicación lógica]: app-service-logic-create-a-logic-app.md

<!---HONumber=July15_HO3-->