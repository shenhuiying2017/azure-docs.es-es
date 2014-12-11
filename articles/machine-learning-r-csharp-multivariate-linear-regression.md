<properties title="Multivariate Linear Regression" pageTitle="Regresión lineal multivariada | Azure" description="Multivariate Linear Regression" metaKeywords="" services="machine-learning" solutions="" documentationCenter="" authors="jaymathe" manager="paulettm" editor="cgronlun" videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/08/2014" ms.author="jaymathe" /> 


#Regresión lineal multivariada   
 

 
Suponga que tiene un conjunto de datos y desea predecir rápidamente una variable dependiente y para cada individuo (i) en función de las demás variables independientes. 
La regresión lineal es una técnica estadística popular utilizada para tales predicciones. En este caso, se supone que la variable dependiente y es un valor continuo.    

Un escenario sencillo podría ser aquel en el que el investigador intenta predecir el peso de un individuo (y) en función de su altura (x). Un escenario más avanzado podría ser aquel en el que el investigador tiene información adicional sobre el individuo (por ejemplo, peso, sexo y raza) e intenta predecir el peso del individuo. Este [servicio web]( https://datamarket.azure.com/dataset/aml_labs/multivariate_regression) ajusta el modelo de regresión lineal a los datos y calcula el valor previsto (y) para cada una de las observaciones de los datos.

>Aunque este servicio web lo pueden utilizar los usuarios, posiblemente a través de una aplicación móvil, un sitio web o incluso un equipo local, por ejemplo, el propósito del servicio web también es servir de ejemplo de cómo se puede usar el Aprendizaje automático de Microsoft Azure para crear servicios web sobre el código R. Con tan solo unas líneas de código R y algunos clics en un botón en el Estudio de aprendizaje automático de Microsoft Azure, puede crear un experimento con código R y publicarlo como servicio web. A continuación, el servicio web se puede publicar en Azure Marketplace para que lo puedan utilizar usuarios y dispositivos en todo el mundo sin necesidad de que el autor del servicio web configure la infraestructura.  

#Uso del servicio web  
Este servicio web proporciona los valores de predicción de la variable dependiente según las variables independientes para todas las observaciones. El servicio web espera que el usuario final escriba sus datos como una cadena, donde las filas están separadas por comas (,) y las columnas se separan mediante punto y coma (;). El servicio web espera 1 fila a la vez y que la primera columna sea la variable dependiente. Un conjunto de datos de ejemplo podría tener este aspecto:

![Sample data][1]

Las observaciones sin una variable dependiente deben coincidir con una entrada como "NA" para y. Los datos de entrada para el conjunto de datos anterior se corresponderían con la cadena siguiente: "10;5;2,18;1;6,6;5.3;2.10,7;5;5,22;3;4,12;2;1,NA;3;4". El resultado es el valor de predicción para cada una de las filas en función de las variables independientes. 

>Este servicio cuando está hospedado en Microsoft Azure Marketplace es un servicio de OData, al que se puede llamar mediante los métodos POST o GET. 

Hay varias maneras de utilizar el servicio de forma automática ([aquí](http://microsoftazuremachinelearning.azurewebsites.net/MultipleLinearRegressionService.aspx ) se puede ver una aplicación de ejemplo).

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


#Creación del servicio web  
>Este servicio web se ha creado con el Aprendizaje automático de Microsoft Azure. Para obtener acceso a una prueba gratuita y a vídeos introductorios sobre la creación de experimentos y la [publicación de servicios web](http://azure.microsoft.com/es-es/documentation/articles/machine-learning-publish-web-service-to-azure-marketplace/), consulte [azure.com/ml](http://azure.com/ml). A continuación se muestra una captura de pantalla del experimento que creó el código de ejemplo y el servicio web para cada uno de los módulos dentro del experimento.


En el Aprendizaje automático de Azure se creó un nuevo experimento en blanco y se extrajeron dos comandos "Ejecutar scripts R" en el área de trabajo. Este servicio web ejecuta un experimento de Aprendizaje automático de Azure con el script de R subyacente.  Hay dos partes para este experimento, a saber: definición de esquema, modelo de entrenamiento + puntuación.  El primer módulo define la estructura esperada del conjunto de datos de entrada, donde la primera variable es la variable dependiente y las demás variables son independientes. El segundo módulo ajusta un modelo de regresión lineal genérico para los datos de entrada.  
  
![Experiment flow][3]

####Módulo 1:
 
####Definición de esquema  
	data <- data.frame(value = "1;2;3,4;5;6,7;8;9", stringsAsFactors=FALSE) maml.mapOutputPort("data");  

####Módulo 2:
####Modelado del Aprendizaje automático   
	data <- maml.mapInputPort(1) # class: data.frame  
  
	data.split <- strsplit(data[1,1], ",")[[1]]  
	data.split <- sapply(data.split, strsplit, ";", simplify = TRUE)  
	data.split <- sapply(data.split, strsplit, ";", simplify = TRUE)  
	data.split <- as.data.frame(t(data.split)) 
	data.split <- data.matrix(data.split) 
	data.split <- data.frame(data.split) 
	model <- lm(data.split)  

	out=data.frame(predict(model,data.split))  
	out <- data.frame(t(out))
	maml.mapOutputPort("out");  
 
##Limitaciones
Se trata de un ejemplo muy sencillo de un servicio web de regresión lineal múltiple. Como puede observarse en el código de ejemplo anterior, no se implementa ninguna detección de errores y el servicio asume que todo es una variable continua (ninguna característica categórica permitida), ya que el servicio solo recibe valores numéricos en el momento de la creación de este servicio web. Además, el servicio actualmente controla el tamaño de datos limitado, debido a la naturaleza de solicitud y respuesta de la llamada al servicio web y el hecho de que el modelo se ajusta cada vez que se llama al servicio web. 

##P+F
Para ver las preguntas más frecuentes sobre el uso del servicio web o la publicación en Marketplace, haga clic [aquí](http://azure.microsoft.com/es-es/documentation/articles/machine-learning-marketplace-faq).

[1]: ./media/machine-learning-r-csharp-multivariate-linear-regression/multireg-img1.png
[2]: ./media/machine-learning-r-csharp-multivariate-linear-regression/multireg-img2.png
[3]: ./media/machine-learning-r-csharp-multivariate-linear-regression/multireg-img3.png
