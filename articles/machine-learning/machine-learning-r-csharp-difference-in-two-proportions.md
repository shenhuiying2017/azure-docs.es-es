---
title: Diferencia en la prueba de proporciones | Microsoft Docs
description: Diferencia en la prueba de proporciones
services: machine-learning
documentationcenter: 
author: aniedea
manager: jhubbard
editor: cgronlun
ms.assetid: 9356b821-5345-44f6-8e26-719f2dea5e6d
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/12/2016
ms.author: aniedea
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 27e1626e72f9740e73a19e078868a7c4392baf1e


---
# <a name="difference-in-proportions-test"></a>Diferencia en la prueba de proporciones
¿Las dos proporciones son diferentes desde el punto de vista estadístico? Supongamos que un usuario desea comparar dos películas para determinar si una película tiene una mayor proporción de "me gusta" en comparación con la otra. Con un ejemplo grande, podría haber una diferencia significativa estadísticamente entre las proporciones 0,50 y 0,51. Con un ejemplo pequeño, es posible que no haya suficientes datos para determinar si estas proporciones son realmente diferentes. 

[!INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

Este [servicio web](https://datamarket.azure.com/dataset/aml_labs/prop_test) realiza una prueba hipotética de la diferencia entre las dos proporciones basada en la entrada del usuario del número de éxitos y del número total de pruebas para los dos grupos objeto de comparación. En un posible escenario, este servicio web podría invocarse desde dentro de una aplicación de comparación de películas, que indica al usuario, si una de las películas realmente "gustó" con más frecuencia que la otra, basado en las clasificaciones de películas.

> Este servicio web puede ser consumido por los usuarios; posiblemente a través de una aplicación móvil, a través de un sitio web o incluso en un equipo local, por ejemplo. Pero el objetivo del servicio web también es actuar como un ejemplo de cómo se puede usar Aprendizaje automático de Azure para crear servicios web encima del código R. Con tan solo unas líneas de código R y algunos clics en un botón en Estudio de aprendizaje automático de Microsoft Azure, puede crear un experimento con código R y publicarlo como servicio web. A continuación, el servicio web se puede publicar en Azure Marketplace para que lo puedan usar usuarios y dispositivos en todo el mundo sin necesidad de que el autor del servicio web configure la infraestructura.
> 
> 

## <a name="consumption-of-web-service"></a>Uso del servicio web
Este servicio acepta 4 argumentos y realiza una prueba hipotética de las proporciones.

Los argumentos de entrada son:

* Éxitos1: número de eventos de éxito del ejemplo 1.
* Éxitos2: número de eventos de éxito del ejemplo 2.
* Total1: tamaño de la muestra 1.
* Total2: tamaño de la muestra 2.

La salida del servicio es el resultado de la prueba hipotética junto con la prueba estadística de Chi cuadrado, el valor df, el valor p y la proporción del ejemplo 1 y 2 y los límites del intervalo de confianza.

> Este servicio cuando está hospedado en Azure Marketplace es un servicio de OData, al que se puede llamar mediante los métodos POST o GET. 
> 
> 

Hay varias maneras de consumir el servicio de forma automática ( [aquí](http://microsoftazuremachinelearning.azurewebsites.net/DifferenceInProportionsTest.aspx)se puede ver una aplicación de ejemplo).

### <a name="starting-c-code-for-web-service-consumption"></a>Inicio del código C# para el uso del servicio web:
    public class Input
    {
            public string successes1;
            public string successes2;
            public string total1;
            public string total2;
    }

    public AuthenticationHeaderValue CreateBasicHeader(string username, string password)
    {
            byte[] byteArray = System.Text.Encoding.UTF8.GetBytes(username + ":" + password);
            return new AuthenticationHeaderValue("Basic", Convert.ToBase64String(byteArray));
    }

    void Main()
    {
            var input = new Input() { successes1 = TextBox1.Text, successes2 = TextBox2.Text, total1 = TextBox3.Text, total2 = TextBox4.Text };
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

En Azure Machine Learning, se creó un nuevo experimento en blanco con dos módulos [Ejecutar script R][execute-r-script]. En el primer módulo se define el esquema de datos, mientras que el segundo módulo utiliza el comando prop.test en R para realizar la prueba hipotética para dos proporciones. 

### <a name="experiment-flow"></a>Flujo de experimento:
![Flujo de experimento][2]

#### <a name="module-1"></a>Módulo 1:
    ####Schema definition  
    data.set=data.frame(successes1=50,successes2=60,total1=100,total2=100);
    maml.mapOutputPort("data.set"); #send data to output port
    dataset1 = maml.mapInputPort(1) #read data from input port


#### <a name="module-2"></a>Módulo 2:
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


## <a name="limitations"></a>Limitaciones
Se trata de un ejemplo muy sencillo para probar las diferencias entre dos proporciones. Como puede observarse en el código de ejemplo anterior, no se implementa ninguna detección de errores y el servicio supone que todas las variables son continuas.

## <a name="faq"></a>P+F
Para ver las preguntas más frecuentes sobre el uso del servicio web o la publicación en Azure Marketplace, haga clic [aquí](machine-learning-marketplace-faq.md).

[1]: ./media/machine-learning-r-csharp-difference-in-two-proportions/hyptest-img1.png
[2]: ./media/machine-learning-r-csharp-difference-in-two-proportions/hyptest-img2.png


<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/




<!--HONumber=Nov16_HO3-->


