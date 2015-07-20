<properties 
	pageTitle="Conectarse a un servicio web de Aprendizaje automático | Microsoft Azure" 
	description="Mediante C# o Python, conéctese a un servicio web de Aprendizaje automático de Azure mediante una clave de autorización." 
	services="machine-learning" 
	documentationCenter="" 
	authors="garyericson" 
	manager="paulettm" 
	editor="cgronlun" />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/07/2015" 
	ms.author="derrickv" />


# Conectarse a un servicio web de Aprendizaje automático de Azure 
La experiencia del desarrollador de Aprendizaje automático de Azure es una API de servicio web para realizar predicciones a partir de datos de entrada en tiempo real o en modo por lotes. Utilice Estudio de aprendizaje automático de Azure para crear predicciones y publicar un servicio web de Aprendizaje automático de Azure.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

Para obtener información acerca de cómo crear y publicar un servicio web de Aprendizaje automático de Azure con Estudio:

- [Publicar un servicio web de Aprendizaje automático de Azure](machine-learning-publish-a-machine-learning-web-service.md)
- [Introducción a Estudio de aprendizaje automático](http://azure.microsoft.com/documentation/videos/getting-started-with-ml-studio/)
- [Vista previa de Aprendizaje automático de Azure](https://studio.azureml.net/)
- [Centro de documentación de Aprendizaje automático](http://azure.microsoft.com/documentation/services/machine-learning/)

## Servicio web de Aprendizaje automático de Azure ##

Con el servicio web de Aprendizaje automático de Azure (ML), una aplicación externa se comunica con un modelo de puntuación de flujo de trabajo ML en tiempo real. Una llamada al servicio web ML devuelve resultados de predicción a una aplicación externa. Para llamar a un servicio web de ML, se pasa una clave de API que se crea cuando se publica una predicción. El servicio web de ML se basa en REST, una opción popular de arquitectura para proyectos de programación web.

Aprendizaje automático de Azure tiene dos tipos de servicios:

- Servicio de solicitud y respuesta (RRS): servicio de latencia baja altamente escalable que proporciona una interfaz para los modelos sin estado creados y publicados desde ML Studio.
- Servicio de ejecución por lotes (BES): servicio asincrónico que puntúa un lote de registros de datos.

Para obtener más información sobre los servicios web de Aprendizaje automático de Azure, consulte [Publicar un servicio web de Aprendizaje automático de Azure](machine-learning-publish-a-machine-learning-web-service.md).

## Obtener una clave de autorización de Aprendizaje automático de Azure ##
Obtenga una clave de API de servicio web de un servicio web de ML. Puede obtenerla de Microsoft Azure Machine Learning Studio o del portal de administración de Azure.
### Microsoft Azure Machine Learning Studio ###
1. En Estudio de aprendizaje automático de Microsoft Azure, haga clic en **SERVICIOS WEB** a la izquierda.
2. Haga clic en un servicio web. La "clave de API" está en la pestaña **PANEL**.

### Portal de administración de Azure ###

1. Haga clic en **APRENDIZAJE AUTOMÁTICO** a la izquierda.
2. Haga clic en un área de trabajo.
3. Haga clic en **SERVICIOS WEB**.
4. Haga clic en un servicio web.
5. Haga clic en un extremo. La "CLAVE DE API" está inactiva en la parte inferior derecha.

## <a id="connect"></a>Conexión a un servicio web de Aprendizaje automático de Azure

Puede conectarse a un servicio web de Aprendizaje automático de Azure mediante cualquier lenguaje de programación que admita la respuesta y solicitud HTTP. Puede ver ejemplos en C#, Python y R desde una página de ayuda de servicio web de Azure ML.

### Para ver una página de ayuda de la API de servicios web de Azure ML ###
Se crea una página de ayuda de la API de Azure ML al publicar un servicio web. Consulte [Tutorial de Aprendizaje automático de Azure: publicar servicio web](machine-learning-walkthrough-5-publish-web-service.md).


**Para ver una página de ayuda de la API de Aprendizaje automático de Azure**, en Estudio de aprendizaje automático de Microsoft Azure:

1. Elija **SERVICIOS WEB**.
2. Elija un servicio web.
3. Elija **Página de ayuda de la API** - **SOLICITUD-RESPUESTA** o **EJECUCIÓN POR LOTES**.


**Página de ayuda de la API de Aprendizaje automático de Azure**. La página de ayuda de la API de Aprendizaje automático de Azure contiene detalles acerca de un servicio web de predicción, entre los que se incluyen


<table>
	<tr>
		<td>&#160;</td>
		<td>Ejemplo </td>
	</tr>
	<tr>
		<td>URI de solicitud POST </td>

		<td>https://ussouthcentral.services.azureml.net/workspaces/{WorkspaceId}/services/{ServiceId}/score
		</td>
	</tr>
	<tr>
		<td>Solicitud de ejemplo </td>
		<td>{ <br/> 
			&#160;&#160; "Id": "score00001",   <br/>
			&#160;&#160; "Instancia": <br/>
			&#160;&#160;&#160;&#160; {  <br/>  
 			&#160;&#160;&#160;&#160; &#160;&#160; "FeatureVector": { <br/>
			&#160;&#160;&#160;&#160; &#160;&#160;  "Col1": "0", <br/>      
			&#160;&#160;&#160;&#160; &#160;&#160;  "Col2": "0", <br/>      
			&#160;&#160;&#160;&#160; &#160;&#160;  "Col3": "0", <br/>  
			&#160;&#160;&#160;&#160; &#160;&#160;  ... },   <br/>
			&#160;&#160;&#160;&#160;   "GlobalParameters": {}   <br/>
			&#160;&#160;&#160;&#160; } <br/>
		}</td>
	</tr>
	<tr>
		<td>Cuerpo de respuesta </td>
		<td>
		<table style="width: 100%">

			<tr>
				<td><B>Name</B></td>
				<td><B>Tipo de datos</B></td>
			</tr>
	
			<tr>
				<td>Característica</td>
				<td>String</td>
			</tr>
			<tr>
				<td>Recuento</td>
				<td>Numeric</td>
			</tr>
			<tr>
				<td>Recuento de valor único </td>
				<td>Numeric </td>
			</tr>
			<tr>
				<td>... </td>
				<td>... </td>
			</tr>
		</table>
		</td>
	</tr>
	<tr>
		<td>Respuesta de ejemplo </td>
		<td>["Col1","1","1",…] </td>
	</tr>
	<tr>
		<td>Código de ejemplo </td>
		<td>(Código de ejemplo en C#, Python y R) </td>
	</tr>
</table>

**NOTA** Los ejemplos corresponden al ejemplo 1: descargar el conjunto de datos de UCI: conjunto de datos de clase de contenido para adultos 2, que forma parte de la colección de ejemplos de Aprendizaje automático de Azure.

### Ejemplo de C# ###

Para conectarse a un servicio web de Aprendizaje automático de Azure, use un **HttpClient** que pase ScoreData. ScoreData contiene un FeatureVector, un vector de n dimensiones de características numéricos que representa el ScoreData. Autentíquese en el servicio de Azure ML con una clave de API.

Para conectarse a un servicio web de Aprendizaje automático, se debe instalar el paquete NuGet **Microsoft.AspNet.WebApi.Client**.

**Instalar NuGet Microsoft.AspNet.WebApi.Client Nuget en Visual Studio**

1. Publique el conjunto de datos de descarga de UCI: Servicio web de conjunto de datos de clase de contenido para adultos 2.
2. Haga clic en **Herramientas** > **Administrador de paquetes de Nuget** > **Consola del Administrador de paquetes**.
2. Elija **Install-Package Microsoft.AspNet.WebApi.Client**.

**Para ejecutar el ejemplo de código**

1. Publique el experimento "Ejemplo 1: descargar el conjunto de datos de UCI: conjunto de datos de clase de contenido para adultos 2", que forma parte de la colección de ejemplos de Aprendizaje automático de Azure.
2. Asigne una clave de API con la clave de un servicio web. Consulte cómo obtener una clave de autorización de Aprendizaje automático de Azure.
3. Asigne la URI de servicio a la URI de solicitud. Consulte cómo obtener un URI de solicitud.

		using System;
		using System.Collections.Generic;
		using System.IO;
		using System.Net.Http;
		using System.Net.Http.Formatting;
		using System.Net.Http.Headers;
		using System.Text;
		using System.Threading.Tasks;

		namespace CallRequestResponseService
		{
	    public class ScoreData
	    {
	        public Dictionary<string, string> FeatureVector { get; set; }
	        public Dictionary<string, string> GlobalParameters { get; set; }
	    }
	
	    public class ScoreRequest
	    {
	        public string Id { get; set; }
	        public ScoreData Instance { get; set; }
	    }
	
	    class Program
	    {
	        static void Main(string[] args)
	        {
	            InvokeRequestResponseService().Wait();
	
	            Console.ReadLine();
	        }
	
	        static async Task InvokeRequestResponseService()
	        {
	            //Assign apiKey with the key from a web service.
	            const string apiKey = "{ApiKey}";
	
	            //Assign serviceUri with the Request URI. See How to get a Request URI.
	            const string serviceUri = "{ServiceUri}";
	            
	            using (var client = new HttpClient())
	            {
	                ScoreData scoreData = new ScoreData()
	                {
	                    //Input data
	                    FeatureVector = new Dictionary<string, string>() 
	                    {
	                        { "Col1", "0" },
	                        { "Col2", "0" },
	                        { "Col3", "0" },
	                        { "Col4", "0" },
	                        { "Col5", "0" },
	                        { "Col6", "0" },
	                        { "Col7", "0" },
	                        { "Col8", "0" },
	                        { "Col9", "0" },
	                        { "Col10", "0" },
	                        { "Col11", "0" },
	                        { "Col12", "0" },
	                        { "Col13", "0" },
	                        { "Col14", "0" },
	                        { "Col15", "0" },
	                    },
	                    GlobalParameters = 
	                        new Dictionary<string, string>() {}
	                };
	
	                ScoreRequest scoreRequest = new ScoreRequest()
	                {
	                    Id = "score00001",
	                    Instance = scoreData
	                };
	
	                //Set authorization header
	                client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue( "Bearer", apiKey);
	             
	                client.BaseAddress = new Uri(serviceUrl);
	
	                //Post HTTP response message
	                HttpResponseMessage response = await client.PostAsJsonAsync("", scoreRequest);
	
	                if (response.IsSuccessStatusCode)
	                {
	                    //Read result string
	                    string result = await response.Content.ReadAsStringAsync();
	                    Console.WriteLine("Result: {0}", result);
	                }
	                else
	                {
	                    Console.WriteLine("Failed with status code: {0}", response.StatusCode);
	                }
	            }
	        }
	    }
		}


### Ejemplo de Python ###

Para conectarse a un servicio web de Aprendizaje automático de Azure, use la biblioteca **urllib2** pasando ScoreData. ScoreData contiene un FeatureVector, un vector de n dimensiones de características numéricos que representa el ScoreData. Autentíquese en el servicio de Azure ML con una clave de API.


**Para ejecutar el ejemplo de código**

1. Publique el experimento "Ejemplo 1: descargar el conjunto de datos de UCI: conjunto de datos de clase de contenido para adultos 2", que forma parte de la colección de ejemplos de Aprendizaje automático de Azure.
2. Asigne una clave de API con la clave de un servicio web. Consulte cómo obtener una clave de autorización de Aprendizaje automático de Azure.
3. Asigne la URI de servicio a la URI de solicitud. Consulte cómo obtener un URI de solicitud.

		import urllib2
		# If you are using Python 3+, import urllib instead of urllib2
	
		import json 
	
		data =  {
	            "Id": "score00001",
	            "Instance": {
	                "FeatureVector": {
	                    "Col1": "0",
	                    "Col2": "0",
	                    "Col3": "0",
	                    "Col4": "0",
	                    "Col5": "0",
	                    "Col6": "0",
	                    "Col7": "0",
	                    "Col8": "0",
	                    "Col9": "0",
	                    "Col10": "0",
	                    "Col11": "0",
	                    "Col12": "0",
	                    "Col13": "0",
	                    "Col14": "0",
	                    "Col15": "0",
	                },
	                "GlobalParameters": { }
	            }
	        }
	
		body = str.encode(json.dumps(data))
	
		#Assign serviceUrl with the Request URI. See How to get a Request URI.
		uri = '{ServiceUri}'
	
		#Assign apiKey with the key from a web service.
		api_key = '{ApiKey}'
		headers = {'Content-Type':'application/json', 'Authorization':('Bearer '+ api_key)}
	
		req = urllib2.Request(uri, body, headers) 
		response = urllib2.urlopen(req)
	
		#If you are using Python 3+, replace urllib2 with urllib.request in the above code:
		#req = urllib.request.Request(uri, body, headers) 
		#response = urllib.request.urlopen(req)
	
		result = response.read()
		print(result) 
 

<!---HONumber=July15_HO2-->