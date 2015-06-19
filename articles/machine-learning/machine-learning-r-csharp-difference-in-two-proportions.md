<properties 
	pageTitle="Diferencia en la prueba de proporciones | Azure" 
	description="Diferencia en la prueba de proporciones" 
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


#Diferencia en la prueba de proporciones




¿Las dos proporciones son diferentes desde el punto de vista estadístico? Supongamos que un usuario desea comparar dos películas para determinar si una película tiene una mayor proporción de "me gusta" en comparación con la otra. Con un gran ejemplo, podría haber una diferencia significativa estadísticamente entre las proporciones 0,50 y 0,51, aunque con una pequeña muestra puede no haber suficientes datos para determinar si estas proporciones son realmente diferentes. 

Este [servicio web]( https://datamarket.azure.com/dataset/aml_labs/prop_test) realiza una prueba hipotética de la diferencia entre las dos proporciones basada en la entrada del usuario del número de éxitos y del número total de pruebas para los dos grupos objeto de comparación. Un escenario sería que este servicio web podría invocarse desde dentro de una aplicación de comparación de películas, que indica al usuario, basándose en las clasificaciones de películas, si una de las películas realmente "gustó" con más frecuencia que la otra.

>Aunque este servicio web lo pueden utilizar los usuarios, posiblemente a través de una aplicación móvil, un sitio web o incluso un equipo local, por ejemplo, el propósito del servicio web también es servir de ejemplo de cómo se puede usar el Aprendizaje automático de Microsoft Azure para crear servicios web sobre el código R. Con tan solo unas líneas de código R y algunos clics en un botón en el Estudio de aprendizaje automático de Microsoft Azure, puede crear un experimento con código R y publicarlo como servicio web. A continuación, el servicio web se puede publicar en Azure Marketplace para que lo puedan utilizar usuarios y dispositivos en todo el mundo sin necesidad de que el autor del servicio web configure la infraestructura.


##Uso del servicio web

Este servicio acepta 4 argumentos y realiza una prueba hipotética de las proporciones.

Los argumentos de entrada son:

* Éxitos1: número de eventos de éxito del ejemplo 1
* Éxitos2: número de eventos de éxito del ejemplo 2
* Total1: tamaño de la muestra 1
* Total2: tamaño de la muestra 2

La salida del servicio es el resultado de la prueba hipotética junto con la prueba estadística de Chi cuadrado, el valor df, el valor p, la proporción del ejemplo 1 y 2 y los límites del intervalo de confianza.

>Este servicio cuando está hospedado en Microsoft Azure Marketplace es un servicio de OData, al que se puede llamar mediante los métodos POST o GET. 

Hay varias maneras de utilizar el servicio de forma automática ([aquí](http://microsoftazuremachinelearning.azurewebsites.net/DifferenceInProportionsTest.aspx ) se puede ver una aplicación de ejemplo).

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

En el Aprendizaje automático de Azure, se creó un nuevo experimento en blanco con dos comandos "Ejecutar scripts R". En el primer módulo se define el esquema de datos, mientras que el segundo módulo utiliza el comando prop.test en R para realizar la prueba hipotética para dos proporciones. 


###Flujo de experimento:

![Experiment flow][2]


####Módulo 1:
	####Schema definition  
	data.set=data.frame(successes1=50,successes2=60,total1=100,total2=100);
	maml.mapOutputPort("data.set"); #send data to output port
	dataset1 = maml.mapInputPort(1) #read data from input port
	

####Módulo 2:

	test=prop.test(c(dataset1$successes1[1],dataset1$successes2[1]),c(dataset1$total1[1],dataset1$total2[1])) #conduct hypothesis test

	result=data.frame(
	result=ifelse(test$p.value<0.05,"The proportions are different!",
	"The proportions aren't different statistically."),
	ChiSquarestatistic=round(test$statistic,2),df=test$parameter,
	pvalue=round(test$p.value,4),
	proportion1=round(test$estimate[1],4),
	proportion2=round(test$estimate[2],4),
	confintlow=round(test$conf.int[1],4),
	confinthigh=round(test$conf.int[2],4)) 

	maml.mapOutputPort("result"); #output port
	

##Limitaciones 

Se trata de un ejemplo muy sencillo para probar las diferencias entre dos proporciones. Como puede observarse en el código de ejemplo anterior, no se implementa ninguna detección de errores y el servicio supone que todas las variables son continuas.

##P+F
Para ver las preguntas más frecuentes sobre el uso del servicio web o la publicación en Marketplace, haga clic [aquí](http://azure.microsoft.com/documentation/articles/machine-learning-marketplace-faq).

[1]: ./media/machine-learning-r-csharp-difference-in-two-proportions/hyptest-img1.png
[2]: ./media/machine-learning-r-csharp-difference-in-two-proportions/hyptest-img2.png

<!--HONumber=46--> 

<!--HONumber=46--> 
 