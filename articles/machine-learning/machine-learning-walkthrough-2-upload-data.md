<properties
	pageTitle="Paso 2: Carga de datos en un experimento de Aprendizaje automático | Microsoft Azure"
	description="Paso 2 del tutorial Desarrollo de una solución predictiva: carga de datos públicos almacenados en Estudio de aprendizaje automático de Azure."
	services="machine-learning"
	documentationCenter=""
	authors="garyericson"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="machine-learning"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/08/2015" 
	ms.author="garye"/>


# Paso 2 del tutorial: Carga de los datos existentes en un experimento de Aprendizaje automático de Azure

Este es el segundo paso del tutorial [Desarrollo de una solución predictiva con el Aprendizaje automático de Azure](machine-learning-walkthrough-develop-predictive-solution.md)


1.	[Creación de un área de trabajo de Aprendizaje automático](machine-learning-walkthrough-1-create-ml-workspace.md)
2.	**Carga de los datos existentes**
3.	[Crear un experimento nuevo](machine-learning-walkthrough-3-create-new-experiment.md)
4.	[Entrenamiento y evaluación de los modelos](machine-learning-walkthrough-4-train-and-evaluate-models.md)
5.	[Implementación del servicio web](machine-learning-walkthrough-5-publish-web-service.md)
6.	[Acceso al servicio web](machine-learning-walkthrough-6-access-web-service.md)

----------

Para desarrollar un modelo predictivo para un riesgo de crédito, usaremos el conjunto de datos "UCI Statlog (German Credit Data)" del repositorio de Aprendizaje automático de UCI. Puede encontrarlo aquí: <a href="http://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data)">http://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data)</a>

Usaremos el archivo llamado **german.data**. Descargue este archivo en la unidad de disco duro local.

Este conjunto de datos contiene filas de 20 variables para 1000 solicitantes de crédito. Estas 20 variables representan el vector de características del conjunto de datos que proporciona características de identificación de cada solicitante de crédito. Una columna adicional en cada fila representa el riesgo de crédito del solicitante, donde 700 solicitantes se han identificado como de bajo riesgo y 300 como de alto riesgo.

El sitio web de UCI ofrece una descripción de los atributos del vector de características que incluyen información financiera, historial de crédito, estado de empleo e información personal. A cada solicitante se le ha dado una calificación binaria para indicar si son de riesgo de crédito alto o bajo.

Estos datos los usaremos para entrenar un modelo de análisis predictivo. Cuando hayamos acabado, nuestro modelo deberá poder aceptar información de nuevos individuos y predecir si representan un riesgo de crédito alto o bajo.

Aquí hay un giro interesante. La descripción del conjunto de datos explica que clasificar erróneamente una persona como de riesgo de crédito bajo cuando en realidad es de riesgo de crédito alto es cinco veces más costoso para la entidad financiera que clasificar erróneamente un riesgo de crédito bajo como alto. Una forma sencilla de tener esto en cuenta en nuestro experimento es duplicar (5 veces) esas entradas que representan a alguien con un riesgo de crédito alto. Luego, si el modelo clasifica erróneamente un riesgo de crédito alto como bajo, lo hará cinco veces, una por cada duplicado. Esto aumentará el coste de este error en los resultados del entrenamiento.

##Conversión del formato del conjunto de datos
El conjunto de datos original utiliza un formato separado por espacios en blanco. Estudio de aprendizaje automático funciona mejor con un archivo de valores delimitados por comas (CSV), así que vamos a convertir el conjunto de datos y reemplazar los espacios por comas.

Podemos hacer esto con el siguiente comando de Windows PowerShell:

	cat german.data | %{$_ -replace " ",","} | sc german.csv  

También lo podemos hacer con el comando sed de Unix:

	sed 's/ /,/g' german.data > german.csv  

##Carga del conjunto de datos en Estudio de aprendizaje automático

Una vez que los datos se han convertido al formato CSV, debemos cargarlos en el Estudio de aprendizaje automático.

1.	Inicie sesión en Estudio de aprendizaje automático ([https://studio.azureml.net](https://studio.azureml.net)) con la cuenta de Microsoft que especificó como propietaria del área de trabajo y, a continuación, haga clic en la pestaña **Estudio** que aparece en la parte superior.
2.	Haga clic en **+NUEVO** en la parte inferior de la página.
3.	Seleccione **CONJUNTO DE DATOS**.
4.	Seleccione **DE ARCHIVO LOCAL**.
5.	En el cuadro de diálogo **Cargar un nuevo conjunto de datos**, haga clic en **Examinar** y busque el archivo **german.csv** que creó.
6.	Escriba un nombre para el conjunto de datos. En este ejemplo, lo llamaremos "UCI German Credit Card Data".
7.	Para el tipo de datos, seleccione **Archivo CSV genérico sin encabezado (.nh.csv)**.
8.	Agregue una descripción si así lo desea.
9.	Haga clic en **Aceptar**.  

![Carga del conjunto de datos][1]


De esta manera los datos se cargan en un módulo de conjunto de datos que podemos usar en un experimento.

Para obtener más información acerca de la importación de diversos tipos de datos a un experimento, consulte [Importar datos de entrenamiento a Estudio de aprendizaje automático de Azure](machine-learning-import-data.md).

**Siguiente: [Crear un experimento nuevo](machine-learning-walkthrough-3-create-new-experiment.md)**

[1]: ./media/machine-learning-walkthrough-2-upload-data/upload1.png

<!---HONumber=Oct15_HO3-->