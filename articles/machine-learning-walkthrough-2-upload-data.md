<properties title="Step 2: Upload existing data into an Azure Machine Learning experiment" pageTitle="Step 2: Upload data into a Machine Learning experiment | Azure" description="Step 2: Upload existing public data into Azure Machine Learning Studio" metaKeywords="" services="" solutions="" documentationCenter="" authors="garye" videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="garye"></tags>

Este es el segundo paso del tutorial [Desarrollo de una solución predictiva con el Aprendizaje automático de Azure][]:

1.  [Creación de un área de trabajo de Aprendizaje automático][]
2.  **Carga de los datos existentes**
3.  [Creación de un nuevo experimento][]
4.  [Entrenamiento y evaluación de los modelos][]
5.  [Publicación del servicio web][]
6.  [Acceso al servicio web][]

------------------------------------------------------------------------

# Paso 2: Carga de los datos existentes en un experimento de Aprendizaje automático de Azure

Para desarrollar un modelo predictivo para un riesgo de crédito, usaremos el conjunto de datos "UCI Statlog (German Credit Data)" del repositorio de Aprendizaje automático de UCI. Puede encontrarlo aquí:
<http://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data)>

Usaremos el archivo llamado **german.data**. Descargue este archivo en la unidad de disco duro local.

Este conjunto de datos contiene filas de 20 variables para 1000 solicitantes de crédito. Estas 20 variables representan el vector de características del conjunto de datos que proporciona características de identificación de cada solicitante de crédito. Una columna adicional en cada fila representa el riesgo de crédito del solicitante, donde 700 solicitantes se han identificado como de bajo riesgo y 300 como de alto riesgo.

El sitio web de UCI ofrece una descripción de los atributos del vector de características que incluyen información financiera, historial de crédito, estado de empleo e información personal. A cada solicitante se le ha dado una calificación binaria para indicar si son de riesgo de crédito alto o bajo.

Estos datos los usaremos para entrenar un modelo de análisis predictivo. Cuando hayamos acabado, nuestro modelo deberá poder aceptar información de nuevos individuos y predecir si representan un riesgo de crédito alto o bajo.

Aquí hay un giro interesante. La descripción del conjunto de datos explica que clasificar erróneamente una persona como de riesgo de crédito bajo cuando en realidad es de riesgo de crédito alto es cinco veces más costoso para la entidad financiera que clasificar erróneamente un riesgo de crédito bajo como alto. Una forma sencilla de tener esto en cuenta en nuestro experimento es duplicar (5 veces) esas entradas que representan a alguien con un riesgo de crédito alto. Luego, si el modelo clasifica erróneamente un riesgo de crédito alto como bajo, lo hará cinco veces, una por cada duplicado. Esto aumentará el coste de este error en los resultados del entrenamiento.

## Conversión del formato del conjunto de datos

El conjunto de datos original utiliza un formato separado por espacios en blanco. El Estudio de aprendizaje automático funciona mejor con un archivo separado por comas (CSV), así que vamos a convertir el conjunto de datos y sustituir los espacios por comas.

Podemos hacer esto con el siguiente comando de PowerShell:

    cat german.data | %{$_ -replace " ",","} | sc german.csv  

También lo podemos hacer con el comando sed de Unix:

    sed 's/ /,/g' german.data > german.csv  

## Carga del conjunto de datos en el Estudio de aprendizaje automático

Una vez que los datos se han convertido al formato CSV, debemos cargarlos en el Estudio de aprendizaje automático.

1.  En el Estudio de aprendizaje automático, haga clic en **+NUEVO** en la parte inferior de la ventana.
2.  Seleccione **CONJUNTO DE DATOS**.
3.  Seleccione **DE ARCHIVO LOCAL**.
4.  En el cuadro de diálogo **Cargar un nuevo conjunto de datos**, haga clic en **Examinar** y busque el archivo **german.csv** que ha creado.
5.  Escriba un nombre para el conjunto de datos, en este ejemplo lo llamaremos "Datos de tarjeta de crédito alemana de UCI".
6.  Para el tipo de dato, seleccione "Generic CSV File With no header (.nh.csv)"
7.  Agregue una descripción si así lo desea.
8.  Haga clic en**Aceptar**
    ![Upload the dataset][]

De esta manera los datos se cargan en un módulo de conjunto de datos que podemos usar en un experimento.

  [Desarrollo de una solución predictiva con el Aprendizaje automático de Azure]: ../machine-learning-walkthrough-develop-predictive-solution/
  [Creación de un área de trabajo de Aprendizaje automático]: ../machine-learning-walkthrough-1-create-ml-workspace/
  [Creación de un nuevo experimento]: ../machine-learning-walkthrough-3-create-new-experiment/
  [Entrenamiento y evaluación de los modelos]: ../machine-learning-walkthrough-4-train-and-evaluate-models/
  [Publicación del servicio web]: ../machine-learning-walkthrough-5-publish-web-service/
  [Acceso al servicio web]: ../machine-learning-walkthrough-6-access-web-service/
  [Upload the dataset]: ./media/machine-learning-walkthrough-2-upload-data/upload1.png
