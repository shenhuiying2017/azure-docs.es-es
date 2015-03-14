<properties 
	pageTitle="Paso 6: Acceso al servicio web de Aprendizaje automático | Azure" 
	description="Paso 6 del tutorial de la solución: acceso a un servicio web activo de Aprendizaje automático de Azure" 
	services="machine-learning" 
	documentationCenter="" 
	authors="garyericson" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/06/2014" 
	ms.author="garye"/>


Este es el último paso del tutorial [Desarrollo de una solución predictiva con el Aprendizaje automático de Azure][develop]:

[develop]: ../machine-learning-walkthrough-develop-predictive-solution/


1.	[Creación de un área de trabajo de Aprendizaje automático][create-workspace]
2.	[Carga de los datos existentes][upload-data]
3.	[Creación de un nuevo experimento][create-new]
4.	[Entrenamiento y evaluación de los modelos][train-models]
5.	[Publicación del servicio web][publish]
6.	**Acceso al servicio web**

[create-workspace]: ../machine-learning-walkthrough-1-create-ml-workspace/
[upload-data]: ../machine-learning-walkthrough-2-upload-data/
[create-new]: ../machine-learning-walkthrough-3-create-new-experiment/
[train-models]: ../machine-learning-walkthrough-4-train-and-evaluate-models/
[publish]: ../machine-learning-walkthrough-5-publish-web-service/
[access-ws]: ../machine-learning-walkthrough-6-access-web-service/

----------

# Paso 6: Acceso al servicio web de Aprendizaje automático de Azure

Para que resulte útil como servicio web, es necesario que los usuarios puedan enviar datos al servicio y recibir resultados. El servicio web es un servicio web de Azure que puede recibir y devolver datos de una de estas dos maneras:  

-	**Solicitud/Respuesta**: el usuario envía un conjunto sencillo de datos de crédito al servicio mediante un protocolo HTTP y el servicio responde con un conjunto sencillo de resultados.
-	**Ejecución de lotes**: el usuario envía al servicio la dirección URL de un BLOB de Azure que contiene una o varias filas de datos de crédito. El servicio almacena los resultados en otro BLOB y devuelve la dirección URL de ese contenedor.  

En la pestaña **PANEL** del servicio web, hay dos vínculos a la información que ayudará a un desarrollador a escribir código para tener acceso a este servicio. Haga clic en el vínculo **Página de ayuda de API** en la fila **SOLICITUD/RESPUESTA** y se abrirá una página que contiene código de muestra para utilizar el protocolo de solicitud/respuesta del servicio. De igual manera, el vínculo de la fila **EJECUCIÓN DE LOTES** proporciona código de ejemplo para realizar una solicitud de lotes al servicio.  

La página de ayuda de API incluye ejemplos para los lenguajes de programación R, C# y Python. Por ejemplo, en este caso es el código R el que podría utilizar para tener acceso al servicio web que hemos publicado (la URL del servicio real se mostraría en el código de ejemplo):  

	library("RCurl")
	library("RJSONIO")
	
	h = basicTextGatherer()
	req = list(Id="score00001",
	 Instance=list(FeatureVector=list(
	    "row.names"= "0",
	    "Status of checking account"= "0",
	    "Duration in months"= "0",
	    "Credit history"= "0",
	    "Purpose"= "0",
	    "Credit amount"= "0",
	    "Savings account/bond"= "0",
	    "Present employment since"= "0",
	    "Installment rate in percentage of disposable income"= "0",
	    "Personal status and sex"= "0",
	    "Other debtors"= "0",
	    "Present residence since"= "0",
	    "Property"= "0",
	    "Age in years"= "0",
	    "Other installment plans"= "0",
	    "Housing"= "0",
	    "Number of existing credits"= "0",
	    "Job"= "0",
	    "Number of people providing maintenance for"= "0",
	    "Telephone"= "0",
	    "Foreign worker"= "0",
	    "Credit risk"= "0"
	 ),GlobalParameters=fromJSON('{}')))
	
	body = toJSON(req)
	api_key = "abc123" # You can obtain the API key from the publisher of the web service
	
	h$reset()
	curlPerform(url = "http://xxx.cloudapp.net/workspaces/xxx/services/xxx/score",
	            httpheader=c('Content-Type' = "application/json", 'Authorization' = "Bearer " + api_key),
	            postfields=body,
	            writefunction = h$update,
	            verbose = TRUE
	            )
	
	result = h$value()



<!--HONumber=46--> 
