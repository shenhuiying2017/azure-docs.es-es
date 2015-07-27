<properties
   pageTitle="Validador XML de BizTalk"
   description="Validador XML de BizTalk"
   services="app-service\logic"
   documentationCenter=".net,nodejs,java"
   authors="rajram"
   manager="dwrede"
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="07/01/2015"
   ms.author="rajram"/>

# Validador XML de BizTalk

El conector Validador XML de BizTalk ayuda a la aplicación a validar los datos XML con respecto a los esquemas XML predefinidos. Los usuarios pueden usar los esquemas existentes o generar esquemas basándose en una instancia de archivo sin formato, instancia de JSON o conectores existentes.

##Uso del Validador XML de BizTalk
1. Para usar el Validador XML de BizTalk, primero debe crear una instancia de la aplicación de API del Validador XML de BizTalk. Esto puede hacerse ya sea en línea, mediante la creación de una aplicación lógica o mediante la selección de la aplicación de API del Validador XML de BizTalk desde Azure Marketplace.

###Configuración del Validador XML de BizTalk
El Validador XML de BizTalk adopta esquemas como parte de su configuración. Los usuarios pueden iniciar la hoja de configuración de la aplicación de API, bien directamente desde el Portal de Azure, o haciendo doble clic en dicha aplicación en la superficie del diseñador.

![Configuración del Validador XML de BizTalk][1]

En la hoja Aplicación de API, los usuarios pueden configurar esquemas haciendo clic en el elemento *Esquemas*.

![Elemento de Esquemas del Validador XML de BizTalk][2]

Los usuarios pueden cargar esquemas desde un disco o generar uno a partir de una instancia de archivo sin formato o una instancia JSON.

![Esquemas del Validador XML de BizTalk][3]


###Uso del Codificador de archivos sin formato de BizTalk en la superficie de diseño
Una vez configurado, los usuarios pueden hacer clic en *->* y elegir una acción de una lista de acciones.

![Lista de acciones del Validador XML de BizTalk][4]

####Validar XML

La acción Validar XML valida un entrada XML dada con respecto a esquemas preconfigurados.

![Acción Validar XML del Validador XML de BizTalk][5]

<table>
	<tr>
		<th>Parámetro</th>
		<th>Tipo</th>
		<th>Descripción del parámetro</th>
	</tr>
	<tr>
		<td>XML de entrada</td>
		<td>cadena</td>
		<td>XML de entrada para validar</td>
	</tr>
</table>


La acción devuelve la salida como un objeto. La salida contiene el modelo que representa la respuesta del Validador XML. Consta de resultado, nombre de esquema, nodo raíz y descripción del error.

![6]

<!-- References -->
[1]: ./media/app-service-logic-xml-validator/XmlValidator.ClickToConfigure.PNG
[2]: ./media/app-service-logic-xml-validator/XmlValidator.SchemasPart.PNG
[3]: ./media/app-service-logic-xml-validator/XmlValidator.SchemaUpload.PNG
[4]: ./media/app-service-logic-xml-validator/XmlValidator.ListOfActions.PNG
[5]: ./media/app-service-logic-xml-validator/XmlValidator.ValidateXml.PNG
[6]: ./media/app-service-logic-xml-validator/img1.PNG
 

<!---HONumber=July15_HO3-->