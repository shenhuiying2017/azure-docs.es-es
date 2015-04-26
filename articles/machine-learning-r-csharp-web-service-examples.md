<properties 
	pageTitle="Compilación de servicios web de muestra con R en el Aprendizaje automático de Azure y publicación en Marketplace | Azure" 
	description="Compilación de servicios web de muestra con R en el Aprendizaje automático de Azure y publicación en Marketplace" 
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


#Compilación de servicios web de muestra con R en el Aprendizaje automático de Azure y publicación en Marketplace




Con el Estudio de aprendizaje automático de Azure, los usuarios pueden escribir código de R y, con tan solo hacer algunos clics en un botón, publicarlo como un servicio web para que lo usen otros usuarios y dispositivos en todo el mundo. Desde la producción de calculadoras sencillas que proporcionan funcionalidad estadística hasta la creación de un previsor de análisis de opiniones para la minería de texto personalizado, por ejemplo, los usuarios noveles y profesionales de R pueden beneficiarse de la facilidad con que los usuarios del Aprendizaje automático de Azure pueden aplicar el código R. Si bien estos servicios web podrían haberlo utilizado los usuarios, posiblemente a través de una aplicación móvil o un sitio web, su propósito también es servir de ejemplo de cómo el Aprendizaje automático de Azure puede aplicar scripts de R para fines analíticos y usarlos para crear servicios web basados en código R.

En esta página encontrará un amplio conjunto de servicios web de ejemplo que se crearon con el Aprendizaje automático de Azure y se publicaron en Microsoft Azure Marketplace. Cada servicio web tiene un documento exhaustivo asociado, donde se integran conjuntos de datos de ejemplo para probar los servicios y se explica cómo el usuario puede crear un servicio similar. 

![Workflow][1]

Considere los siguientes escenarios:

####Escenario 1: modelo genérico 
Un usuario trabaja con un modelo genérico que se puede aplicar a los datos de un nuevo usuario, como una previsión básica de datos de serie temporal o un método de R personalizado e integrado con análisis avanzado. Este usuario publica el modelo como un servicio web para que otros usuarios puedan utilizar sus datos.

* [Clasificador binario](http://azure.microsoft.com/documentation/articles/machine-learning-r-csharp-binary-classifier/)
* [Modelo de clúster](http://azure.microsoft.com/documentation/articles/machine-learning-r-csharp-cluster-model/)
* [Regresión lineal multivariada](http://azure.microsoft.com/documentation/articles/machine-learning-r-csharp-multivariate-linear-regression/)
* [Previsión-Suavizado exponencial](http://azure.microsoft.com/documentation/articles/machine-learning-r-csharp-forecasting-exponential-smoothing/)
* [Previsión con ETS + STL](http://azure.microsoft.com/documentation/articles/machine-learning-r-csharp-retail-demand-forecasting/)
* [Previsión - Media móvil integrada autorregresiva (ARIMA)](http://azure.microsoft.com/documentation/articles/machine-learning-r-csharp-arima/)
* [Análisis de supervivencia](http://azure.microsoft.com/documentation/articles/machine-learning-r-csharp-survival-analysis/)

####Escenario 2: Modelo entrenado - datos específicos 
Un usuario tiene datos que proporcionan útiles predicciones a través del código R, como un gran ejemplo de cuestionarios de personalidad agrupados mediante un algoritmo k-means para predecir el tipo de personalidad del usuario o los datos de encuestas de salud que pueden utilizarse para predecir el riesgo de que un individuo padezca cáncer de pulmón mediante un paquete de análisis de supervivencia basado en R. El usuario publica los datos a través de un servicio web que predice el resultado del nuevo usuario.

####Escenario 3: Modelo entrenado - datos genéricos 
Un usuario tiene datos genéricos (por ejemplo, un conjunto de texto) que permiten crear un servicio web que pueda aplicarse de manera general en diferentes tipos de escenarios y casos de uso.

* [Análisis de opiniones basado en léxico](http://azure.microsoft.com/documentation/articles/machine-learning-r-csharp-lexicon-based-sentiment-analysis/)

####Escenario 4: Calculadora avanzada 
Un usuario proporciona cálculos avanzados o simulaciones, que no requieren ningún modelo entrenado o ajuste de un modelo a los datos del usuario.

* [Diferencia en la prueba de proporciones](http://azure.microsoft.com/documentation/articles/machine-learning-r-csharp-difference-in-two-proportions/)
* [Conjunto de distribución normal](http://azure.microsoft.com/documentation/articles/machine-learning-r-csharp-normal-distribution/)
* [Conjunto de distribución binomial](http://azure.microsoft.com/documentation/articles/machine-learning-r-csharp-binomial-distribution/)

##P+F
Para ver las preguntas más frecuentes sobre el uso del servicio web o la publicación en Marketplace, haga clic [aquí](http://azure.microsoft.com/documentation/articles/machine-learning-marketplace-faq).

[1]: ./media/machine-learning-r-csharp-web-service-examples/base1.png



<!--HONumber=46--> 

<!--HONumber=46--> 
