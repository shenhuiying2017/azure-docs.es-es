<properties 
	pageTitle="Conjunto de distribución binomial | Azure" 
	description="Conjunto de distribución binomial" 
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


#Conjunto de distribución binomial




El conjunto de distribución binomial es una serie de servicios web de ejemplo ([Generador binomial](https://datamarket.azure.com/dataset/aml_labs/bdg5), [Calculadora de probabilidad]( https://datamarket.azure.com/dataset/aml_labs/bdp4), [Calculadora de cuantil]( https://datamarket.azure.com/dataset/aml_labs/bdq5)), en particular tres servicios que ayudan a generar y administrar distribuciones binomiales. Los servicios permiten generar una secuencia de distribución binomial de cualquier longitud, calcular los cuantiles de una probabilidad determinada y calcular la probabilidad a partir de un cuantil específico.  Cada uno de los servicios genera salidas diferentes en función del servicio seleccionado (consulte la siguiente descripción). El conjunto de distribución binomial se basa en funciones qbinom, rbinom y pbinom de R que se incluyen en el paquete de estadísticas de R. 

>Aunque estos servicios web lo pueden utilizar los usuarios, posiblemente en Marketplace directamente, a través de una aplicación móvil, un sitio web o incluso un equipo local, por ejemplo, el propósito del servicio web también es servir de ejemplo de cómo se puede usar el Aprendizaje automático de Microsoft Azure para crear servicios web sobre el código R. Con tan solo unas líneas de código R y algunos clics en un botón en el Estudio de aprendizaje automático de Microsoft Azure, puede crear un experimento con código R y publicarlo como servicio web. A continuación, el servicio web se puede publicar en Azure Marketplace para que lo puedan utilizar usuarios y dispositivos en todo el mundo sin necesidad de que el autor del servicio web configure la infraestructura.

##Uso del servicio web
El conjunto de distribución binomial incluye los tres servicios siguientes:

###Calculadora de cuantil para la distribución binomial
Este servicio acepta 4 argumentos de una distribución normal y calcula el cuantil asociado.
Los argumentos de entrada son:

- p: una única probabilidad agregada de varias versiones de prueba  
- tamaño: el número de versiones de prueba
- probabilidad: la probabilidad de éxito en una versión de prueba
- Lado: L para la parte inferior de la distribución, U para la parte superior de la distribución 

La salida del servicio es el cuantil calculado asociado con la probabilidad dada.

###Calculadora de probabilidad para la distribución binomial
Este servicio acepta 4 argumentos de una distribución binomial y calcula la probabilidad asociada.
Los argumentos de entrada son:

- q: un único cuantil de un evento con distribución binomial 
- tamaño: el número de versiones de prueba
- probabilidad: la probabilidad de éxito en una versión de prueba
- Lado: L para la parte inferior de la distribución, U para la parte superior de la distribución o E que es igual a un único número de éxitos.

La salida del servicio es la probabilidad calculada asociada con el cuantil dado.

###Generador de distribución binomial
Este servicio acepta 3 argumentos de una distribución binomial y genera una secuencia aleatoria de números que se distribuyen de manera binomial. 
Se le deben proporcionar los siguientes argumentos en la solicitud:

- n: número de observaciones 
- tamaño: número de versiones de prueba
- probabilidad: probabilidad de éxito

La salida del servicio es una secuencia de longitud n con una distribución binomial basada en los argumentos de tamaño y probabilidad.

>Este servicio cuando está hospedado en Microsoft Azure Marketplace es un servicio de OData, al que se puede llamar mediante los métodos POST o GET. 

Hay varias maneras de utilizar el servicio de forma automática (las aplicaciones de ejemplo son: [Generador](http://microsoftazuremachinelearning.azurewebsites.net/BinomialDistributionGenerator.aspx),
[Calculadora de probabilidad](http://microsoftazuremachinelearning.azurewebsites.net/BinomialDistributionProbabilityCalculator.aspx),
[Calculadora de cuantil](http://microsoftazuremachinelearning.azurewebsites.net/BinomialDistributionQuantileCalculator)). 

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

>Este servicio web se ha creado con el Aprendizaje automático de Microsoft Azure. Para obtener acceso a una prueba gratuita y a vídeos introductorios sobre la creación de experimentos y la [publicación de servicios web](http://azure.microsoft.com/documentation/articles/machine-learning-publish-web-service-to-azure-marketplace/), consulte [azure.com/ml](http://azure.com/ml). A continuación se muestra una captura de pantalla del experimento que creó el código de ejemplo y el servicio web para cada uno de los módulos dentro del experimento.

###Calculadora de cuantil para la distribución binomial

![Create workspace][4]

####Módulo 1:
    #data schema with example data (replaced with data from web service)
    data.set=data.frame(p=0.1,size=10,prob=.5,side='L');
    maml.mapOutputPort("data.set"); #send data to output port
####Módulo 2:

    dataset1 <- maml.mapInputPort(1) # class: data.frame
    param = dataset1
    if (param$p < 0 ) {
	print('Bad input: p must be between 0 and 1')
	param$p = 0
    } else if (param$p > 1) {
	print('Bad input: p must be between 0 and 1')
	param$p = 1
    }

    if (param$prob < 0 ) {
	print('Bad input: prob must be between 0 and 1')
	param$prob = 0
    } else if (param$prob > 1) {
	print('Bad input: prob must be between 0 and 1')
	param$prob = 1
    }

    quantile = qbinom(param$p,size=param$size,prob=param$prob)
    df = data.frame(x=1:param$size, prob=dbinom(1:param$size, param$size, prob=param$prob))
    quantile

    if (param$side == 'U'){
	quantile = qbinom(param$p,size=param$size,prob=param$prob,lower.tail = F)
	band=subset(df,x>quantile)
    } else if (param$side =='L') {
	quantile = qbinom(param$p,size=param$size,prob=param$prob,lower.tail = T)
	band=subset(df,x<=quantile)
    } else {
	print("Invalid side choice")
    }

    output = as.data.frame(quantile)
    # Select data.frame to be sent to the output Dataset port
    maml.mapOutputPort("output");


###Calculadora de probabilidad para la distribución binomial

![Create workspace][5]

####Módulo 1:

    #data schema with example data (replaced with data from web service)
    data.set=data.frame(q=5,size=10,prob=.5,side='L');
    maml.mapOutputPort("data.set"); #send data to output port


####Módulo 2:
    dataset1 <- maml.mapInputPort(1) # class: data.frame
    param = dataset1
    prob = pbinom(param$q,size=param$size,prob=param$prob)
    prob.eq = dbinom(param$q,size=param$size,prob=param$prob)
    df = data.frame(x=1:param$size, prob=dbinom(1:param$size, param$size, prob=param$prob))
    prob

    if (param$side == 'U'){
	prob = 1 - prob
	band=subset(df,x>param$q)
    } else if (param$side =='E') {
	prob = prob.eq
	band=subset(df,x==param$q)
    } else if (param$side =='L') {
	prob = prob
	band=subset(df,x<=param$q)
    } else {
	print("Invalid side choice")
    }

    output = as.data.frame(prob)
    # Select data.frame to be sent to the output Dataset port
    maml.mapOutputPort("output");

###Generador de distribución binomial

![Create workspace][6]

####Módulo 1:

    #data schema with example data (replaced with data from web service)
    data.set=data.frame(n=50,size=10,p=.5);
    maml.mapOutputPort("data.set"); #send data to output port

####Módulo 2:
    dataset1 <- maml.mapInputPort(1) # class: data.frame
    param = dataset1
    dist = rbinom(param$n,param$size,param$p)

    output = as.data.frame(t(dist))
    # Select data.frame to be sent to the output Dataset port
    maml.mapOutputPort("output");

##Limitaciones 
Se trata de ejemplos muy sencillos relacionados con la distribución binomial. Como puede observarse en el código de ejemplo anterior, se implementa una detección de errores mínima.

##P+F
Para ver las preguntas más frecuentes sobre el uso del servicio web o la publicación en Marketplace, haga clic [aquí](http://azure.microsoft.com/documentation/articles/machine-learning-marketplace-faq).


[1]: ./media/machine-learning-r-csharp-binomial-distribution/binomial_1.png

[2]: ./media/machine-learning-r-csharp-binomial-distribution/binomial_2.png

[3]: ./media/machine-learning-r-csharp-binomial-distribution/binomial_3.png

[4]: ./media/machine-learning-r-csharp-binomial-distribution/binomial_4.png

[5]: ./media/machine-learning-r-csharp-binomial-distribution/binomial_5.png

[6]: ./media/machine-learning-r-csharp-binomial-distribution/binomial_6.png

<!--HONumber=46--> 
