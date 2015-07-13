<properties 
	pageTitle="Paso 6: Acceso al servicio web de Aprendizaje automático | Microsoft Azure" 
	description="Paso 6 del tutorial Desarrollo de una solución predictiva: acceso a un servicio web activo de Estudio de aprendizaje automático de Azure." 
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
	ms.date="04/22/2015" 
	ms.author="garye"/>


# Paso 6 del tutorial: Acceso al servicio web de Aprendizaje automático de Azure

Este es el último paso del tutorial [Desarrollo de una solución predictiva con el Aprendizaje automático de Azure](machine-learning-walkthrough-develop-predictive-solution.md)


1.	[Creación de un área de trabajo de Aprendizaje automático](machine-learning-walkthrough-1-create-ml-workspace.md)
2.	[Carga de los datos existentes](machine-learning-walkthrough-2-upload-data.md)
3.	[Crear un experimento nuevo](machine-learning-walkthrough-3-create-new-experiment.md)
4.	[Entrenamiento y evaluación de los modelos](machine-learning-walkthrough-4-train-and-evaluate-models.md)
5.	[Publicación del servicio web](machine-learning-walkthrough-5-publish-web-service.md)
6.	**Acceso al servicio web**

----------

Para que resulte útil como servicio web, es necesario que los usuarios puedan enviar datos al servicio y recibir resultados. El servicio web es un servicio web de Azure que puede recibir y devolver datos de una de estas dos maneras:

-	**Solicitud/respuesta**: el usuario envía un conjunto sencillo de datos de crédito al servicio mediante un protocolo HTTP y el servicio responde con un conjunto sencillo de resultados.
-	**Ejecución de lotes**: el usuario envía al servicio la dirección URL de un blob de Azure que contiene una o varias filas de datos de crédito. El servicio almacena los resultados en otro blob y devuelve la dirección URL de ese contenedor.  

En la pestaña **PANEL** del servicio web, hay dos vínculos a la información que ayudará a un desarrollador a escribir código para tener acceso a este servicio. Haga clic en el vínculo **Página de ayuda de API** en la fila **SOLICITUD/RESPUESTA** y se abrirá una página que contiene código de muestra para utilizar el protocolo de solicitud/respuesta del servicio. De igual manera, el vínculo de la fila **EJECUCIÓN DE LOTES** proporciona código de ejemplo para realizar una solicitud de lotes al servicio.

La página de ayuda de API incluye ejemplos para los lenguajes de programación R, C# y Python.

<!-- Add link to Derrick's articles on web services -->

<!---HONumber=July15_HO1-->