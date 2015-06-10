<properties 
   pageTitle="Codificador de archivos sin formato de BizTalk" 
   description="Codificador de archivos sin formato de BizTalk" 
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
   ms.date="03/20/2015"
   ms.author="rajram"/>

# Codificador de archivos sin formato de BizTalk

El conector Codificador/decodificador de archivos sin formato de BizTalk contribuye a la interoperabilidad de aplicaciones entre los datos de archivos sin formato (por ejemplo, Excel, csv) y datos XML. En este sentido, puede convertir una instancia de archivo sin formato a XML y viceversa.

##Uso del Codificador de archivos sin formato de BizTalk
1. Para usar el Codificador de archivos sin formato de BizTalk, primero debe crear una instancia de la aplicación de API del Codificador de archivos sin formato de BizTalk. Esta operación puede realizarse en línea, mediante la creación de una aplicación lógica, o bien seleccionando la aplicación de API del Codificador de archivos sin formato de BizTalk en Azure Marketplace.

###Configuración del Codificador de archivos sin formato de BizTalk
El Codificador de archivos sin formato de BizTalk adopta esquemas como parte de su configuración. Para iniciar la hoja de configuración de la aplicación de API, los usuarios pueden iniciar la aplicación de API directamente desde el Portal de Azure, o bien pueden hacer clic en dicha aplicación en la superficie del diseñador.

![Configuración del Codificador de archivos sin formato de BizTalk][1]

En la hoja Aplicación de API, los usuarios pueden configurar esquemas haciendo clic en el elemento *Esquemas*.

![Elemento Esquemas del Codificador de archivos sin formato de BizTalk][2]

Los usuarios pueden cargar esquemas desde un disco o generar uno a partir de una instancia de archivo sin formato o una instancia JSON.

![Elemento Esquemas del Codificador de archivos sin formato de BizTalk][3]


###Uso del Codificador de archivos sin formato de BizTalk en la superficie de diseño
Una vez configurado, los usuarios pueden hacer clic en *->* y elegir una acción de una lista de acciones.

![Lista de acciones del Codificador de archivos sin formato de BizTalk][4]

####Archivo sin formato a XML

![Lista de acciones del Codificador de archivos sin formato de BizTalk][5]

<table>
	<tr>
		<th>Parámetro</th>
		<th>Tipo</th>
		<th>Descripción del parámetro</th>
	</tr>
	<tr>
		<td>Archivos planos</td>
		<td>cadena</td>
		<td>Contenido del archivo sin formato de entrada</td>
	</tr>
	<tr>
		<td>Nombre del esquema</td>
		<td>cadena</td>
		<td>Nombre del esquema que representa el archivo sin formato de entrada</td>
	</tr>
	<tr>
		<td>Nombre de raíz</td>
		<td>cadena</td>
		<td>Nombre del nodo raíz del esquema de archivo sin formato</td>
	</tr>
</table>


La acción devuelve la salida como una cadena: XML de salida. El XML de salida contiene la representación XML del contenido del archivo sin formato de entrada.

####XML a archivo sin formato

![Lista de acciones del Codificador de archivos sin formato de BizTalk][6]

<table>
	<tr>
		<th>Parámetro</th>
		<th>Tipo</th>
		<th>Descripción del parámetro</th>
	</tr>
	<tr>
		<td>XML de entrada</td>
		<td>cadena</td>
		<td>Contenido de XML de entrada</td>
	</tr>
</table>

La acción devuelve la salida como una cadena: archivo sin formato. La salida contiene la representación del archivo sin formato del contenido XML de entrada.

<!-- References -->
[1]: ./media/app-service-logic-flatfile-encoder/FlatFileEncoder.ClickToConfigure.PNG
[2]: ./media/app-service-logic-flatfile-encoder/FlatFileEncoder.SchemasPart.PNG
[3]: ./media/app-service-logic-flatfile-encoder/FlatFileEncoder.SchemaUpload.PNG
[4]: ./media/app-service-logic-flatfile-encoder/FlatFileEncoder.ListOfActions.PNG
[5]: ./media/app-service-logic-flatfile-encoder/FlatFileEncoder.FlatFileToXml.PNG
[6]: ./media/app-service-logic-flatfile-encoder/FlatFileEncoder.XmlToFlatFile.PNG

<!---HONumber=58-->