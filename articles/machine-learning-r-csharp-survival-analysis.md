<properties 
	pageTitle="Análisis de supervivencia | Azure" 
	description="Probabilidad de aparición de eventos de análisis de supervivencia" 
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


#Análisis de supervivencia 





En muchos escenarios, el resultado principal de la evaluación es el momento en que se producirá un evento de interés. En otras palabras, se plantea la pregunta "cuándo se producirá este evento". Como ejemplos, tenga en cuenta las situaciones donde los datos describen el tiempo transcurrido (días, años, kilometraje, etc.) hasta que el evento de interés (recaída de la enfermedad, grado de doctorado recibido o pastillas de freno estropeadas) se produce. Cada instancia de los datos representa un objeto específico (un paciente, una persona, un automóvil, etc.).

Este [servicio web]( https://datamarket.azure.com/dataset/aml_labs/survivalanalysis) responde a la pregunta "¿qué probabilidad hay de que el evento de interés se produzca en el momento n para el objeto x?". Al proporcionar un modelo de análisis de supervivencia, este servicio web permite a los usuarios proporcionar datos para entrenar el modelo y probarlo. El tema principal del experimento es modelar la duración del tiempo que transcurre hasta que se produzca el evento de interés. 

>Aunque este servicio web lo pueden utilizar los usuarios, posiblemente a través de una aplicación móvil, un sitio web o incluso un equipo local, por ejemplo, el propósito del servicio web también es servir de ejemplo de cómo se puede usar el Aprendizaje automático de Microsoft Azure para crear servicios web sobre el código R. Con tan solo unas líneas de código R y algunos clics en un botón en el Estudio de aprendizaje automático de Microsoft Azure, puede crear un experimento con código R y publicarlo como servicio web. A continuación, el servicio web se puede publicar en Azure Marketplace para que lo puedan utilizar usuarios y dispositivos en todo el mundo sin necesidad de que el autor del servicio web configure la infraestructura.  

##Uso del servicio web

El esquema de datos de entrada del servicio web se muestra en la tabla siguiente. Se necesitan seis elementos de información como entrada: los datos de aprendizaje, los datos de prueba, el tiempo de interés, el índice de la dimensión de "tiempo", el índice de la dimensión de "evento" y los tipos de variables (continua o factor). Los datos de aprendizaje se representan con una cadena, donde las filas están separadas por comas y las columnas están separadas por punto y coma. El número de características de los datos es flexible. Todos los elementos de la cadena de entrada deben ser numéricos. En los datos de aprendizaje, la dimensión de "tiempo" indica que el número de unidades de tiempo (días, años, kilometraje, etc.) transcurrido desde el inicio del estudio (un paciente recibe los programas de tratamiento contra el consumo de drogas, un estudiante que empieza el doctorado, un automóvil que empieza a conducirse, etc.) hasta que se produce el evento de interés (el paciente vuelve a consumir droga, el alumno obtiene el título de doctorado, el vehículo presenta desgaste de las pastillas de freno, etc.). La dimensión de "evento" indica si se produce el evento de interés al final del estudio. "evento = 1" significa que se produce el evento de interés en el momento indicado por la dimensión de "tiempo"; mientras que "evento = 0" significa que el evento de interés no se ha producido todavía en el momento indicado por la dimensión de "tiempo".

- trainingdata: una cadena de caracteres. Las filas están separadas por comas, y las columnas están separadas por punto y coma. Cada fila incluye la dimensión de "tiempo", la dimensión de "evento" y las variables del previsor.
- testingdata: una fila de datos que contiene variables del previsor para un objeto determinado.
- time_of_interest: el tiempo transcurrido de interés n
- index_time: el índice de columna de la dimensión de "tiempo" (a partir de 1)
- index_event: el índice de columna de la dimensión de "evento" (a partir de 1)
- variable_types: una cadena de caracteres con punto y coma como separadores. 0 representa variables continuas y 1 representa variables factor.


El resultado es la probabilidad de que un evento se produzca en un momento determinado. 

>Este servicio cuando está hospedado en Microsoft Azure Marketplace es un servicio de OData, al que se puede llamar mediante los métodos POST o GET. 

Hay varias maneras de utilizar el servicio de forma automática ([aquí](http://microsoftazuremachinelearning.azurewebsites.net/SurvivalAnalysis.aspx) se puede ver una aplicación de ejemplo). 

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


Esta prueba se interpreta de la siguiente forma. Supongamos que el objetivo de los datos es modelar el tiempo transcurrido hasta que los pacientes sometidos a algunos de los dos programas de tratamiento contra las drogas vuelven a consumir drogas. El resultado del servicio web es: para pacientes de 35 años, que se han sometido dos veces a un tratamiento anterior contra el consumo de drogas, con un largo programa de tratamiento residencial y que consumían heroína y cocaína, la probabilidad de volver a consumir drogas es del 95,64 % el día 500.

##Creación del servicio web

>Este servicio web se ha creado con el Aprendizaje automático de Microsoft Azure. Para obtener acceso a una prueba gratuita y a vídeos introductorios sobre la creación de experimentos y la [publicación de servicios web](http://azure.microsoft.com/documentation/articles/machine-learning-publish-web-service-to-azure-marketplace/), consulte [azure.com/ml](http://azure.com/ml). A continuación se muestra una captura de pantalla del experimento que creó el código de ejemplo y el servicio web para cada uno de los módulos dentro del experimento.

En el Aprendizaje automático de Azure se creó un nuevo experimento en blanco y se extrajeron dos comandos "Ejecutar scripts R" en el área de trabajo. El esquema de datos se creó con un comando sencillo "Ejecutar script R", que define el esquema de datos de entrada para el servicio web. Este módulo está vinculado al segundo módulo "Ejecutar script R", que hace la mayor parte del trabajo. Este módulo realiza el preprocesamiento de datos, la creación de modelos y las previsiones. En el paso del preprocesamiento de los datos, los datos de entrada representados por una cadena larga se transforman y convierten en una trama de datos. En el paso de creación del modelo, un paquete R externo "survival_2.37 7.zip" se instala en primer lugar para llevar a cabo el análisis de supervivencia. A continuación, se ejecuta la función "coxph" después de una serie de tareas de procesamiento de datos. Se pueden leer los detalles de la función "coxph" para el análisis de supervivencia en la documentación de R. En el paso de previsión, se proporciona una instancia de prueba en el modelo de entrenado con la función "surfit", y la curva de supervivencia para esta instancia de prueba se genera como variable "curva". Por último, se obtiene la probabilidad del tiempo de interés. 

###Flujo de experimento:

![experiment flow][1]

####Módulo 1:

    #data schema with example data (replaced with data from web service)
    trainingdata="53;1;29;0;0;3,79;1;34;0;1;2,45;1;27;0;1;1,37;1;24;0;1;1,122;1;30;0;1;1,655;0;41;0;0;1,166;1;30;0;0;3,227;1;29;0;0;3,805;0;30;0;0;1,104;1;24;0;0;1,90;1;32;0;0;1,373;1;26;0;0;1,70;1;36;0;0;1"
    testingdata="35;2;1;1"
    time_of_interest="500"
    index_time="1"
    index_event="2"
    sampleInput=data.frame(trainingdata,testingdata,time_of_interest,index_time,index_event,variable_types)
    maml.mapOutputPort("sampleInput"); #send data to output port
	
####Módulo 2:

    #read data from input port
    data <- maml.mapInputPort(1) 
    colnames(data) <- c("trainingdata","testingdata","time_of_interest","index_time","index_event","variable_types")
    # preprocessing trainingdata
    traindingdata=data$trainingdata
    y=strsplit(as.character(data$trainingdata),",")
    n_row=length(unlist(y))
    z=sapply(unlist(y), strsplit, ";", simplify = TRUE)
    mydata <- data.frame(matrix(unlist(z), nrow=n_row, byrow=T), stringsAsFactors=FALSE)
    n_col=ncol(mydata)
    # preprocessing testingdata
    testingdata=as.character(data$testingdata)
    testingdata=unlist(strsplit(testingdata,";"))
    # preprocessing other input parameters
    time_of_interest=data$time_of_interest
    time_of_interest=as.numeric(as.character(time_of_interest))
    index_time = data$index_time
    index_event = data$index_event
    variable_types = data$variable_types
    # necessary R packages
    install.packages("src/packages_survival/survival_2.37-7.zip",lib=".",repos=NULL,verbose=TRUE)
    library(survival)
    # prepare to build model
    attach(mydata)

    for (i in 1:n_col){ mydata[,i]=as.numeric(mydata[,i])} 
    d_time=paste("X",index_time,sep = "")
    d_event=paste("X",index_event,sep = "")
    v_time_event <- c(d_time,d_event)
    v_predictors = names(mydata)[!(names(mydata) %in% v_time_event)]

    variable_types = unlist(strsplit(as.character(variable_types),";"))

    len = length(v_predictors)
    c="" # construct the execution string
    for (i in 1:len){
    if(i==len){
    if(variable_types[i]!=0){ c=paste(c, "factor(",v_predictors[i],")",sep="")}
     else{ c=paste(c, v_predictors[i])}
    }else{
    if(variable_types[i]!=0){c=paste(c, "factor(",v_predictors[i],") + ",sep="")}
    else{c=paste(c, v_predictors[i],"+")}
    }
    }
    f=paste("coxph(Surv(",d_time,",",d_event,") ~")
    f=paste(f,c)
    f=paste(f,", data=mydata )")

    # fit a Cox proportional hazards model and get the predicted survival curve for a testing instance 
    fit=eval(parse(text=f))

    testingdata = as.data.frame(matrix(testingdata, ncol=len,byrow = TRUE),stringsAsFactors=FALSE)
    names(testingdata)=v_predictors
    for (i in 1:len){ testingdata[,i]=as.numeric(testingdata[,i])}

    curve=survfit(fit,testingdata)

    # based on user input, find the event occurance probablity
    position_closest=which.min(abs(prob_event$time - time_of_interest))

    if(prob_event[position_closest,"time"]==time_of_interest){# exact match
    output=prob_event[position_closest,"prob"]
    }else{# not exact match
    if(time_of_interest>max(prob_event$time)){
    output=prob_event[position_closest,"prob"]
    }else if(time_of_interest<min(prob_event$time)){
    output=prob_event[position_closest,"prob"]
    }else{output=(prob_event[position_closest,"prob"]+prob_event[position_closest+1,"prob"])/2}
    }
    #pull out results to send to web service
    output=paste(round(100*output, 2), "%") 
    maml.mapOutputPort("output"); #output port




##Limitaciones

Este servicio web solo puede aceptar valores numéricos como variables de característica (columnas). La columna "evento" solo puede aceptar el valor 0 ó 1. La columna "tiempo" debe ser un entero positivo.

##P+F
Para ver las preguntas más frecuentes sobre el uso del servicio web o la publicación en Marketplace, haga clic [aquí](http://azure.microsoft.com/documentation/articles/machine-learning-marketplace-faq).

[1]: ./media/machine-learning-r-csharp-survival-analysis/survive_img2.png

<!--HONumber=46--> 
