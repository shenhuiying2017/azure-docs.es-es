<properties title="Lexicon Based Sentiment Analysis" pageTitle="Paso 1: Análisis de opiniones basado en léxico | Azure" description="Lexicon Based Sentiment Analysis" metaKeywords="" services="machine-learning" solutions="" documentationCenter="" authors="jaymathe" manager="paulettm" editor="cgronlun" videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/08/2014" ms.author="jaymathe" /> 



#Análisis de opiniones basado en léxico 

 


¿Cómo puede medir la opinión y la actitud de un usuario con respecto a marcas o temas de redes sociales en línea, como publicaciones de Facebook, tweets, revisiones, etc.? El análisis de opiniones ofrece un método para analizar estas preguntas.

En concreto, hay dos métodos generales para analizar opiniones; uno usa el algoritmo de aprendizaje supervisado y otro puede tratarse como aprendizaje no supervisado. El algoritmo de aprendizaje supervisado normalmente genera un modelo de clasificación en grandes conjuntos anotados y su precisión se basa principalmente en la calidad de la anotación y normalmente el proceso de entrenamiento tardará mucho tiempo. Además de eso, cuando se aplica el algoritmo a otro dominio, el resultado normalmente no es bueno. En comparación con el aprendizaje supervisado, el aprendizaje no supervisado basado en léxico usa un diccionario de opinión, que no requiere almacenar grandes conjuntos de datos ni aprendizaje, lo que hace que todo el proceso sea mucho más rápido. 

Nuestro [servicio](https://datamarket.azure.com/dataset/aml_labs/lexicon_based_sentiment_analysis) se basa en el léxico MPQA (http://mpqa.cs.pitt.edu/lexicons/subj_lexicon/), que es uno de los lexicones de subjetividad más utilizados. Hay 5.097 palabras negativas y 2.533 palabras positivas en MPQA. Y todas estas palabras se anotan como polaridad fuerte o débil. El conjunto completo está sujeto a la licencia pública general GNU. El servicio web puede aplicarse a cualquier frase corta como tweets, publicaciones de Facebook, etc. 

>Aunque este servicio web lo pueden utilizar los usuarios, posiblemente a través de una aplicación móvil, un sitio web o incluso un equipo local, por ejemplo, el propósito del servicio web también es servir de ejemplo de cómo se puede usar el Aprendizaje automático de Microsoft Azure para crear servicios web sobre el código R. Con tan solo unas líneas de código R y algunos clics en un botón en el Estudio de aprendizaje automático de Microsoft Azure, puede crear un experimento con código R y publicarlo como servicio web. A continuación, el servicio web se puede publicar en Azure Marketplace para que lo puedan utilizar usuarios y dispositivos en todo el mundo sin necesidad de que el autor del servicio web configure la infraestructura.

##Uso del servicio web

Se puede utilizar cualquier texto como datos de entrada, pero el servicio web funciona mejor con frases cortas. El resultado es un valor numérico entre -1 y 1. Cualquier valor por debajo de 0 indica que la opinión sobre el texto es negativa, y positiva si el valor es mayor que 0. El valor absoluto del resultado indica la intensidad de la opinión asociada. 

>Este servicio cuando está hospedado en Microsoft Azure Marketplace es un servicio de OData, al que se puede llamar mediante los métodos POST o GET. 

Hay varias maneras de utilizar el servicio de forma automática ([aquí](http://microsoftazuremachinelearning.azurewebsites.net/) se puede ver una aplicación de ejemplo).

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


La entrada es "Hoy es un buen día.", y el resultado es "1", que indica la idea positiva asociada con la frase de entrada. 

##Creación del servicio web
>Este servicio web se ha creado con el Aprendizaje automático de Microsoft Azure. Para obtener acceso a una prueba gratuita y a vídeos introductorios sobre la creación de experimentos y la [publicación de servicios web](http://azure.microsoft.com/es-es/documentation/articles/machine-learning-publish-web-service-to-azure-marketplace/), consulte [azure.com/ml](http://azure.com/ml). A continuación se muestra una captura de pantalla del experimento que creó el código de ejemplo y el servicio web para cada uno de los módulos dentro del experimento.


Desde el Aprendizaje automático de Microsoft Azure, se creó un nuevo experimento en blanco. En la siguiente ilustración se muestra el flujo de experimento del análisis de opiniones basado en léxico. "sent_dict.csv" es el léxico de subjetividad de MPQA y está establecido como una de las entradas de "Ejecutar scripts R". Otra entrada es una revisión muestreada del conjunto de datos de revisión de Amazon para pruebas, donde se realizan la selección, la modificación del nombre de columna y las operaciones de separación.  Usamos el paquete de hash para almacenar el léxico de subjetividad en la memoria y acelerar el proceso de cálculo de la puntuación. Todo el texto se acortará con el paquete "tm" y se comparará con la palabra del diccionario de opiniones. Por último, se calculará una puntuación agregando la ponderación de cada palabra subjetiva del texto. 

###Flujo de experimento:

![experimenmt flow][2]


####Módulo 1:
	
	# Map 1-based optional input ports to variables
    sent_dict_data<- maml.mapInputPort(1) # class: data.frame
    dataset2 <- maml.mapInputPort(2) # class: data.frame
    # install hash package
    install.packages("src/hash_2.2.6.zip", lib = ".", repos = NULL, verbose = TRUE)
    success <- library("hash", lib.loc = ".", logical.return = TRUE, verbose = TRUE)
    library(tm)
    library(stringr)
    #create sentiment dictionary
    negation_word <- c("not","nor", "no")
    result <- c()
    sent_dict <- hash()
    sent_dict <- hash(sent_dict_data$word, sent_dict_data$polarity)
    #  compute sentiment score for each document
    for (m in 1:nrow(dataset2)){
	polarity_ratio <- 0
	polarity_total <- 0
	not <- 0
	sentence <- tolower(dataset2[m,1])
	if (nchar(sentence) > 0){
		token_array <- scan_tokenizer(sentence)
		for (j in 1:length(token_array)){
			word = str_replace_all(token_array[j], "[^[:alnum:]]", "")
		    for (k in 1:length(negation_word)){
		      if (word == negation_word[k]){
		        not <- (not+1) %% 2

			  }
		    }
			if (word != ""){
			    if (!is.null(sent_dict[[word]])){
			      polarity_ratio <- polarity_ratio + (-2*not+1)*sent_dict[[word]]
			      polarity_total <- polarity_total + abs(sent_dict[[word]])
			    }
			}
		  
		}
	}
	if (polarity_total > 0){
		result <- c(result, polarity_ratio/polarity_total)
	}else{
		result<- c(result,0)
	}
    }
    # Sample operation
    data.set <- data.frame(result)
    # Select data.frame to be sent to the output Dataset port
    maml.mapOutputPort("data.set")
	


##Limitaciones

Desde la perspectiva del algoritmo, el análisis de opiniones basado en léxico es una herramienta de análisis de opiniones generales, aunque es posible que no funcione mejor que el método de clasificación para campos específicos. No se aborda en gran detalle el problema de la negación. Codificamos varias palabras de negación en nuestro programa, pero una mejor manera es usar un diccionario de negación y crear algunas reglas. El servicio web funciona mejor con frases cortas y sencillas como tweets y publicaciones de Facebook que con frases largas y complejas como las de las revisiones de Amazon. 

##P+F
Para ver las preguntas más frecuentes sobre el uso del servicio web o la publicación en Marketplace, haga clic [aquí](http://azure.microsoft.com/es-es/documentation/articles/machine-learning-marketplace-faq).

[1]: ./media/machine-learning-r-csharp-lexicon-based-sentiment-analysis/sentiment_analysis_1.png
[2]: ./media/machine-learning-r-csharp-lexicon-based-sentiment-analysis/sentiment_analysis_2.png








