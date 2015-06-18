<properties 
	pageTitle="Modelo de clúster | Azure" 
	description="Modelo de clúster" 
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
	ms.date="02/12/2015" 
	ms.author="jaymathe"/> 


#Modelo de clúster    



¿Cómo podemos predecir grupos de comportamientos de los titulares de tarjetas de crédito para reducir el riesgo de impago al que están expuestos los emisores de tales tarjetas?  ¿Cómo podemos definir grupos de rasgos de personalidad de empleados con el fin de mejorar su rendimiento en el trabajo? ¿Cómo pueden clasificar los médicos a los pacientes en grupos en función de las características de sus enfermedades? En principio, todas estas preguntas pueden responderse mediante el análisis del clúster.    
   
En la práctica, el análisis del clúster clasifica un conjunto de observaciones en dos o más grupos desconocidos mutuamente excluyentes conforme a combinaciones de variables. El propósito del análisis del clúster es detectar un sistema para organizar en grupos las observaciones, normalmente las personas o sus características, donde los miembros de los grupos tienen propiedades en común. Este [servicio](https://datamarket.azure.com/dataset/aml_labs/k_cluster_model) utiliza la metodología de K-Means, una técnica de agrupación en clústeres de uso habitual, para agrupar datos arbitrarios en grupos. Este servicio web recibe los datos y el número de clústeres k como entradas y genera las predicciones del grupo k al que pertenece cada observación. 

>Aunque este servicio web lo pueden utilizar los usuarios, posiblemente a través de una aplicación móvil, un sitio web o incluso un equipo local, por ejemplo, el propósito del servicio web también es servir de ejemplo de cómo se puede usar el Aprendizaje automático de Microsoft Azure para crear servicios web sobre el código R. Con tan solo unas líneas de código R y algunos clics en un botón en el Estudio de aprendizaje automático de Microsoft Azure, puede crear un experimento con código R y publicarlo como servicio web. A continuación, el servicio web se puede publicar en Azure Marketplace para que lo puedan utilizar usuarios y dispositivos en todo el mundo sin necesidad de que el autor del servicio web configure la infraestructura.  

#Uso del servicio web   
Este servicio web agrupa los datos en un conjunto de grupos k y genera la asignación de grupo para cada fila. El servicio web espera que el usuario final escriba sus datos como una cadena, donde las filas están separadas por comas (,) y las columnas se separan mediante punto y coma (;). El servicio web espera 1 fila a la vez. Un conjunto de datos de ejemplo podría tener este aspecto:

![Sample data][1]

Supongamos que el usuario desea separar estos datos en tres grupos mutuamente excluyentes. Los datos de entrada para el conjunto de datos anterior serían los siguientes: valor = "10;5;2,18;1;6,7;5;5,22;3;4,12;2;1,10;3;4"; k="3". El resultado es la pertenencia prevista a un grupo para cada una de las filas.

>Este servicio cuando está hospedado en Microsoft Azure Marketplace es un servicio de OData, al que se puede llamar mediante los métodos POST o GET. 

Hay varias maneras de utilizar el servicio de forma automática ([aquí](http://microsoftazuremachinelearning.azurewebsites.net/ClusterModel.aspx ) se puede ver una aplicación de ejemplo).

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
>Este servicio web se ha creado con el Aprendizaje automático de Microsoft Azure. Para obtener acceso a una prueba gratuita y a vídeos introductorios sobre la creación de experimentos y la [publicación de servicios web](http://azure.microsoft.com/documentation/articles/machine-learning-publish-web-service-to-azure-marketplace/), consulte [azure.com/ml](http://azure.com/ml). A continuación se muestra una captura de pantalla del experimento que creó el código de ejemplo y el servicio web para cada uno de los módulos dentro del experimento.

En el Aprendizaje automático de Azure se creó un nuevo experimento en blanco y se extrajeron dos comandos "Ejecutar scripts R" en el área de trabajo. El esquema de datos se creó con un sencillo comando "Ejecutar scripts R"; a continuación, el esquema de datos se vinculó a la sección del modelo de clúster, que también se creó con un comando "Ejecutar script R". En el comando "Ejecutar script R" utilizado para el modelo de clúster, el servicio web utiliza la función "k-means", que está preintegrada en el comando "Ejecutar script R" del Aprendizaje automático de Azure.    
   

     
![Experiment flow][3]

####Módulo 1: 
	#Enter the input data as a string 
	mydata <- data.frame(value = "1; 3; 5; 6; 7; 7, 5; 5; 6; 7; 2; 1, 3; 7; 2; 9; 56; 6, 1; 4; 5; 26; 4; 23, 15; 35; 6; 7; 12; 1, 32; 51; 62; 7; 21; 1", k=5, stringsAsFactors=FALSE)
	maml.mapOutputPort("mydata");     
	

####Módulo 2:
	# Map 1-based optional input ports to variables
	mydata <- maml.mapInputPort(1) # class: data.frame

	data.split <- strsplit(mydata[1,1], ",")[[1]]
	data.split <- sapply(data.split, strsplit, ";", simplify = TRUE)
	data.split <- sapply(data.split, strsplit, ";", simplify = TRUE)
	data.split <- as.data.frame(t(data.split))

	data.split <- data.matrix(data.split)
	data.split <- data.frame(data.split)

	# K-Means Cluster Analysis
	fit <- kmeans(data.split, mydata$k) # k-cluster solution

	# get cluster means 
	aggregate(data.split,by=list(fit$cluster),FUN=mean)
	# append cluster assignment
	mydatafinal <- data.frame(t(fit$cluster))
	n_col=ncol(mydatafinal)
	colnames(mydatafinal) <- paste("V",1:n_col,sep="")

	# Select data.frame to be sent to the output Dataset port
	maml.mapOutputPort("mydatafinal");
   
 
#Limitaciones
Se trata de un ejemplo muy sencillo de un servicio web de agrupación en clústeres. Como puede observarse en el código de ejemplo anterior, no se implementa ninguna detección de errores y el servicio asume que todo es una variable continua (ninguna característica categórica permitida), ya que el servicio solo recibe valores numéricos en el momento de la creación de este servicio web. Además, el servicio actualmente controla el tamaño de datos limitado, debido a la naturaleza de solicitud y respuesta de la llamada al servicio web y el hecho de que el modelo se ajusta cada vez que se llama al servicio web. 

##P+F
Para ver las preguntas más frecuentes sobre el uso del servicio web o la publicación en Marketplace, haga clic [aquí](http://azure.microsoft.com/documentation/articles/machine-learning-marketplace-faq).

[1]: ./media/machine-learning-r-csharp-cluster-model/cluster-img1.png
[2]: ./media/machine-learning-r-csharp-cluster-model/cluster-img2.png
[3]: ./media/machine-learning-r-csharp-cluster-model/cluster-img3.png

<!--HONumber=46--> 

<!--HONumber=46--> 
 