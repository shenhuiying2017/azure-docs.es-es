---
title: (obsoleto) Clasificador binario - Azure | Microsoft Docs
description: (obsoleto) Clasificador binario
services: machine-learning
documentationcenter: 
author: jaymathe
manager: jhubbard
editor: cgronlun
ms.assetid: 8045038a-9dcf-44b9-a6de-7f1f8e791575
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: deprecated
ms.date: 01/06/2017
ms.author: jaymathe
ROBOTS: NOINDEX, NOFOLLOW
translationtype: Human Translation
ms.sourcegitcommit: f6ad106e769c807d1c281c8d19127eabc2048f30
ms.openlocfilehash: d1d4f35a7e77a5fac1e8ecb1a82c14cca3406676


---
# <a name="deprecated-binary-classifier"></a>(obsoleto) Clasificador binario

> [!NOTE]
> Microsoft DataMarket está en proceso de retirada y esta API está en desuso. 
> 
> Puede encontrar muchos experimentos y API de ejemplo útiles en la [Galería de Cortana Intelligence](http://gallery.cortanaintelligence.com). Para más información sobre la Galería, consulte [Uso compartido y descubrimiento de soluciones en la Galería de Cortana Intelligence](machine-learning-gallery-how-to-use-contribute-publish.md).

Suponga que tiene un conjunto de datos y desea predecir una variable dependiente binaria en función de las variables independientes. La "regresión logística" es una técnica estadística popular utilizada para tales predicciones. En este caso, la variable dependiente es binaria o dicotómica, y p es la probabilidad de la presencia de la característica de interés. 

[!INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

Un escenario sencillo podría ser aquel en el que un investigador trata de predecir la probabilidad de que un posible estudiante acepte una oferta de admisión a una universidad en función de información determinada (nota media en educación superior, ingresos familiares, residencia y sexo). El resultado de la predicción es la probabilidad de que el posible estudiante acepte la oferta de admisión. Este [servicio web](https://datamarket.azure.com/dataset/aml_labs/log_regression) ajusta el modelo de regresión logística a los datos y calcula el valor de probabilidad (y) para cada una de las observaciones de los datos.  

> Este servicio web puede ser consumido por los usuarios; posiblemente a través de una aplicación móvil, a través de un sitio web o incluso en un equipo local, por ejemplo. Pero el objetivo del servicio web también es actuar como un ejemplo de cómo se puede usar Aprendizaje automático de Azure para crear servicios web encima del código R. Con tan solo unas líneas de código R y algunos clics en un botón en Estudio de aprendizaje automático de Microsoft Azure, puede crear un experimento con código R y publicarlo como servicio web. A continuación, el servicio web se puede publicar en Azure Marketplace para que lo puedan usar usuarios y dispositivos en todo el mundo sin necesidad de que el autor del servicio web configure la infraestructura.  
> 
> 

## <a name="consumption-of-web-service"></a>Uso del servicio web
Este servicio web proporciona los valores de predicción de la variable dependiente según las variables independientes para todas las observaciones. El servicio web espera que el usuario final escriba sus datos como una cadena, donde las filas están separadas por coma (,) y las columnas se separan mediante punto y coma (;). El servicio web espera 1 fila a la vez y que la primera columna sea la variable dependiente. Un conjunto de datos de ejemplo podría tener este aspecto:

![Datos de ejemplo][1]

Las observaciones sin una variable dependiente deben especificarse como "NA" para Y. Los datos de entrada para el conjunto de datos anterior serían los siguientes: “1;5;2,1;1;6,0;5.3;2.1,0;5;5,0;3;4,1;2;1,NA;3;4”. El resultado es el valor de predicción para cada una de las filas en función de las variables independientes. 

> Este servicio cuando está hospedado en Azure Marketplace es un servicio de OData, al que se puede llamar mediante los métodos POST o GET. 
> 
> 

Hay varias maneras de consumir el servicio de forma automática ( [aquí](http://microsoftazuremachinelearning.azurewebsites.net/BinaryClassifier.aspx)se puede ver una aplicación de ejemplo).

### <a name="starting-c-code-for-web-service-consumption"></a>Inicio del código C# para el uso del servicio web:
    public class Input
    {
           public string value;
    }

    public AuthenticationHeaderValue CreateBasicHeader(string username, string password)
    {
        byte[] byteArray = System.Text.Encoding.UTF8.GetBytes(username + ":" + password);
        return new AuthenticationHeaderValue("Basic", Convert.ToBase64String(byteArray));
    }

    void Main()
    {
        var input = new Input() { value = TextBox1.Text };
        var json = JsonConvert.SerializeObject(input);
        var acitionUri = "PutAPIURLHere,e.g.https://api.datamarket.azure.com/..../v1/Score";
        var httpClient = new HttpClient();

        httpClient.DefaultRequestHeaders.Authorization = CreateBasicHeader("PutEmailAddressHere", "ChangeToAPIKey");
        httpClient.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));

        var response = httpClient.PostAsync(acitionUri, new StringContent(json));
        var result = response.Result.Content;
        var scoreResult = result.ReadAsStringAsync().Result;
    }


## <a name="creation-of-web-service"></a>Creación del servicio web
> Este servicio web se ha creado con el Aprendizaje automático de Azure. Para obtener acceso a una evaluación gratuita y a vídeos introductorios sobre la creación de experimentos y la [publicación de servicios web](machine-learning-publish-a-machine-learning-web-service.md), consulte [azure.com/ml](http://azure.com/ml). A continuación se muestra una captura de pantalla del experimento que creó el código de ejemplo y el servicio web para cada uno de los módulos dentro del experimento.
> 
> 

En Machine Learning de Azure se creó un nuevo experimento en blanco y se extrajeron dos módulos [Ejecutar scripts R][execute-r-script] en el área de trabajo. Este servicio web ejecuta un experimento de Aprendizaje automático de Azure con un script de R subyacente. Hay dos partes para este experimento: definición de esquema y modelo de aprendizaje + puntuación. El primer módulo define la estructura esperada del conjunto de datos de entrada, donde la primera variable es la variable dependiente y las demás variables son independientes. El segundo módulo ajusta un modelo de regresión logística genérico para los datos de entrada.    

![Flujo de experimento][2]

#### <a name="module-1"></a>Módulo 1:
    #Schema definition  
    data <- data.frame(value = "1;2;3,1;5;6,0;8;9", stringsAsFactors=FALSE) 
    maml.mapOutputPort("data");  

#### <a name="module-2"></a>Módulo 2:
    #GLM modeling   
    data <- maml.mapInputPort(1) # class: data.frame  

    data.split <- strsplit(data[1,1], ",")[[1]] 
    data.split <- sapply(data.split, strsplit, ";", simplify = TRUE) 
    data.split <- sapply(data.split, strsplit, ";", simplify = TRUE) 
    data.split <- as.data.frame(t(data.split)) data.split <- 
    data.matrix(data.split) 
    data.split <- data.frame(data.split) 

    model <- glm(data.split$V1 ~., family='binomial', data=data.split)  
    out <- data.frame(predict(model,data.split, type="response")) 
    pred1 <- as.data.frame(out) 
    group <- array(1:nrow(pred1)) 
    for (i in 1:nrow(pred1))  
        {
        if(as.numeric(pred1[i,])>0.5) {group[i]=1} 
        else {group[i]=0}
        } 
    pred2 <- as.data.frame(group) 
    maml.mapOutputPort("pred2");  


## <a name="limitations"></a>Limitaciones
Se trata de un ejemplo muy sencillo de un servicio web de clasificación binaria. Como puede observarse en el código de ejemplo anterior, no se implementa ninguna detección de errores y el servicio asume que todo es una variable binaria o continua (ninguna característica categórica permitida), ya que el servicio solo recibe valores numéricos en el momento de la creación de este servicio web. Además, el servicio actualmente controla el tamaño de datos limitado, debido a la naturaleza de solicitud y respuesta de la llamada al servicio web y el hecho de que el modelo se ajusta cada vez que se llama al servicio web. 

## <a name="faq"></a>P+F
Para ver las preguntas más frecuentes sobre el uso del servicio web o la publicación en Azure Marketplace, haga clic [aquí](machine-learning-marketplace-faq.md).

[1]: ./media/machine-learning-r-csharp-binary-classifier/binary1.png
[2]: ./media/machine-learning-r-csharp-binary-classifier/binary2.png


<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/




<!--HONumber=Jan17_HO2-->


