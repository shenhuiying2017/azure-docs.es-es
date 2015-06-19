<properties 
	pageTitle="Conjunto de servicios web de distribución normal | Azure"
	description="Conjunto de servicios web de distribución normal" 
	services="machine-learning" 
	documentationCenter="" 
	authors="jaymathe" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/11/2015" 
	ms.author="jaymathe"/> 

#Conjunto de servicios web de distribución normal



El conjunto de distribución normal es una serie de servicios web de ejemplo ([Generador]( https://datamarket.azure.com/dataset/aml_labs/ndg7), [Calculadora de probabilidad]( https://datamarket.azure.com/dataset/aml_labs/ndq5), [Calculadora de cuantil]( https://datamarket.azure.com/dataset/aml_labs/ndp5)), en particular tres servicios que ayudan a generar y administrar distribuciones normales. Los servicios permiten generar una secuencia de distribución normal de cualquier longitud, calcular los cuantiles de una probabilidad determinada y calcular la probabilidad a partir de un cuantil específico.  Cada uno de los servicios genera salidas diferentes en función del servicio seleccionado (consulte la siguiente descripción). El conjunto de distribución normal se basa en funciones qnorm, rnorm y pnorm de R que se incluyen en el paquete de estadísticas de R.

>Aunque este servicio web lo pueden utilizar los usuarios, posiblemente a través de una aplicación móvil, un sitio web o incluso un equipo local, por ejemplo, el propósito del servicio web también es servir de ejemplo de cómo se puede usar el Aprendizaje automático de Microsoft Azure para crear servicios web sobre el código R. Con tan solo unas líneas de código R y algunos clics en un botón en el Estudio de aprendizaje automático de Microsoft Azure, puede crear un experimento con código R y publicarlo como servicio web. A continuación, el servicio web se puede publicar en Azure Marketplace para que lo puedan utilizar usuarios y dispositivos en todo el mundo sin necesidad de que el autor del servicio web configure la infraestructura.  
 

##Uso del servicio web
El conjunto de distribución normal incluye los tres servicios siguientes:

###Calculadora de cuantil para la distribución normal:
Este servicio acepta 4 argumentos de una distribución normal y calcula el cuantil asociado.

Los argumentos de entrada son:

* p: una única probabilidad de un evento con distribución normal 
* Media: la media de la distribución normal
* SD: la desviación estándar de la distribución normal 
* Lado: L para la parte inferior de la distribución y U para la parte superior de la distribución

La salida del servicio es el cuantil calculado asociado con la probabilidad dada.

###Calculadora de probabilidad para la distribución normal:
Este servicio acepta 4 argumentos de una distribución normal y calcula la probabilidad asociada.

Los argumentos de entrada son:

* q: un único cuantil de un evento con distribución normal 
* Media: la media de la distribución normal
* SD: la desviación estándar de la distribución normal 
* Lado: L para la parte inferior de la distribución y U para la parte superior de la distribución

La salida del servicio es la probabilidad calculada asociada con el cuantil dado.

###Generador de distribución normal
Este servicio acepta 3 argumentos de una distribución normal y genera una secuencia aleatoria de números que se distribuyen de manera normal. 
Se le deben proporcionar los siguientes argumentos en la solicitud:

* n: número de observaciones 
* Media: la media de la distribución normal
* sd: la desviación estándar de la distribución normal 

La salida del servicio es una secuencia de longitud n con una distribución normal basada en los argumentos media y desviación estándar.

>Este servicio cuando está hospedado en Microsoft Azure Marketplace es un servicio de OData, al que se puede llamar mediante los métodos POST o GET. 

Hay varias maneras de utilizar el servicio de forma automática (las aplicaciones de ejemplo son: [Generador](http://microsoftazuremachinelearning.azurewebsites.net/NormalDistributionGenerator.aspx),
[Calculadora de probabilidad](http://microsoftazuremachinelearning.azurewebsites.net/NormalDistributionProbabilityCalculator.aspx),
[Calculadora de cuantil](http://microsoftazuremachinelearning.azurewebsites.net/NormalDistributionQuantileCalculator.aspx)).

###Inicio del código C# para el uso del servicio web:

	public class Input{
	public double Recency;
	public double Frequency;
	public double Monetary;
	public double Time;
	public double Class;
	}

	public AuthenticationHeaderValue CreateBasicHeader(string username, string password)
    {
        byte[] byteArray = System.Text.Encoding.UTF8.GetBytes(username + ":" + password);
        System.Diagnostics.Debug.WriteLine("AuthenticationHeaderValue" + new AuthenticationHeaderValue("Basic", Convert.ToBase64String(byteArray)));
        return new AuthenticationHeaderValue("Basic", Convert.ToBase64String(byteArray));
    }
       
	void Main()
	{
  	var input = new Input(){Recency =1, Frequency=0,Monetary=0,Time=1, Class= 0};
	var json = JsonConvert.SerializeObject(input);
	var acitionUri =  "PutAPIURLHere,e.g.https://api.datamarket.azure.com/..../v1/Score";
       
  	var httpClient = new HttpClient();
   	httpClient.DefaultRequestHeaders.Authorization = CreateBasicHeader("PutEmailAddressHere","ChangeToAPIKey");
   	httpClient.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));
  	var query = httpClient.PostAsync(acitionUri,new StringContent(json));
  	var result = query.Result.Content;
  	var scoreResult = result.ReadAsStringAsync().Result;
  	scoreResult.Dump();
	}

##Creación del servicio web 
>Este servicio web se ha creado con el Aprendizaje automático de Microsoft Azure. Para obtener acceso a una prueba gratuita y a vídeos introductorios sobre la creación de experimentos y la [publicación de servicios web](http://azure.microsoft.com/documentation/articles/machine-learning-publish-web-service-to-azure-marketplace/), consulte [azure.com/ml](http://azure.com/ml). 

A continuación se muestra una captura de pantalla del experimento que creó el código de ejemplo y el servicio web para cada uno de los módulos dentro del experimento.

###Calculadora de cuantil para la distribución normal:
Flujo de experimento
![Experiment flow][2]
 
	#data schema with example data (replaced with data from web service)
	data.set=data.frame(p=0.1,mean=0,sd=1,side='L');
	maml.mapOutputPort("data.set"); #send data to output port
	# Map 1-based optional input ports to variables
	dataset1 <- maml.mapInputPort(1) # class: data.frame

	param = dataset1
	if (param$p < 0 ) {
	print('Bad input: p must be between 0 and 1')
	param$p = 0
	} else if (param$p > 1) {
	print('Bad input: p must be between 0 and 1')
	param$p = 1
	}
	q = qnorm(param$p,mean=param$mean,sd=param$sd)

	if (param$side == 'U'){
	q = 2* param$mean - q
	} else if (param$side =='L') {
	q = q
	} else {
	print("Invalid side choice")
	}

	output = as.data.frame(q)
	# Select data.frame to be sent to the output Dataset port
	maml.mapOutputPort("output");
	
###Calculadora de probabilidad para la distribución normal:
Flujo de experimento
![Experiment flow][3]
 
 	#data schema with example data (replaced with data from web service)
	data.set=data.frame(q=-1,mean=0,sd=1,side='L');
	maml.mapOutputPort("data.set"); #send data to output port
	# Map 1-based optional input ports to variables
	dataset1 <- maml.mapInputPort(1) # class: data.frame

	param = dataset1
	prob = pnorm(param$q,mean=param$mean,sd=param$sd)

	if (param$side == 'U'){
	prob = 1 - prob
	} else if (param$side =='B') {
	prob = ifelse(prob<=0.5,prob * 2, 1)
	} else if (param$side =='L') {
	prob = prob
	} else {
	print("Invalid side choice")
	}

	output = as.data.frame(prob)
	# Select data.frame to be sent to the output Dataset port
	maml.mapOutputPort("output");
	
###Generador de distribución normal:
Flujo de experimento
![Experiment flow][4]

	#data schema with example data (replaced with data from web service)
	data.set=data.frame(n=50,mean=0,sd=1);
	maml.mapOutputPort("data.set"); #send data to output port
	# Map 1-based optional input ports to variables
	dataset1 <- maml.mapInputPort(1) # class: data.frame

	param = dataset1
	dist = rnorm(param$n,mean=param$mean,sd=param$sd)

	output = as.data.frame(t(dist))

	# Select data.frame to be sent to the output Dataset port
	maml.mapOutputPort("output");

##Limitaciones 

Se trata de ejemplos muy sencillos relacionados con la distribución normal. Como puede observarse en el código de ejemplo anterior, se implementa una detección de errores mínima.

##P+F
Para ver las preguntas más frecuentes sobre el uso del servicio web o la publicación en Marketplace, haga clic [aquí](http://azure.microsoft.com/documentation/articles/machine-learning-marketplace-faq).

[1]: ./media/machine-learning-r-csharp-normal-distribution/normal-img1.png
[2]: ./media/machine-learning-r-csharp-normal-distribution/normal-img2.png
[3]: ./media/machine-learning-r-csharp-normal-distribution/normal-img3.png
[4]: ./media/machine-learning-r-csharp-normal-distribution/normal-img4.png

<!--HONumber=46--> 

<!--HONumber=46--> 
 