---
title: Ciencia de los datos con Data Science Virtual Machine de Linux en Azure | Microsoft Docs
description: "Procedimiento para realizar varias tareas comunes de ciencia de los datos con la máquina virtual de Linux Data Science."
services: machine-learning
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: 34ef0b10-9270-474f-8800-eecb183bbce4
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/10/2017
ms.author: bradsev;paulsh
ms.openlocfilehash: 650b11d66f3ca32266b9842af77c909e125b4e4d
ms.sourcegitcommit: d03907a25fb7f22bec6a33c9c91b877897e96197
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/12/2017
---
# <a name="data-science-with-a-linux-data-science-virtual-machine-on-azure"></a>Ciencia de datos con una instancia de Data Science Virtual Machine de Linux en Azure
En este tutorial se muestra cómo realizar varias tareas comunes de ciencia de los datos con la máquina virtual de Linux Data Science. Linux Data Science Virtual Machine (DSVM) es una imagen de máquina virtual disponible en Azure que viene preinstalada con una colección de herramientas usadas normalmente en el análisis de los datos y el aprendizaje automático. Los componentes de software principales se detallan en el tema [Aprovisionamiento de Linux Data Science Virtual Machine](linux-dsvm-intro.md) . La imagen de máquina virtual permite comenzar a trabajar fácilmente con la ciencia de los datos en cuestión de minutos, sin tener que instalar ni configurar cada una de las herramientas de forma individual. Puede escalar la máquina virtual verticalmente de manera fácil, si es necesario, y detenerla cuando no la use. Así que este recurso es tanto elástico como rentable.

En las tareas de ciencias de los datos que se demuestran en este tutorial se siguen los pasos descritos en el [proceso de ciencia de los datos en equipos](https://azure.microsoft.com/documentation/learning-paths/data-science-process/). Este proceso proporciona un enfoque sistemático sobre la ciencia de los datos que permite a los equipos de científicos de los datos colaborar de manera efectiva durante el ciclo de vida de creación de aplicaciones inteligentes. El proceso de ciencia de los datos también proporciona un marco iterativo para la ciencia de los datos que pueden seguir los individuos.

En este tutorial analizamos el conjunto de datos [spambase](https://archive.ics.uci.edu/ml/datasets/spambase) . Se trata de un conjunto de correos electrónicos macados como es correo no deseado o no es correo no deseado, y también contiene algunas estadísticas sobre el contenido de los correos electrónicos. Las estadísticas incluidas se describen en la siguiente sección.

## <a name="prerequisites"></a>Requisitos previos
Antes de poder usar una máquina virtual de Linux Data Science, debe tener lo siguiente:

* Una **suscripción de Azure**. Si ya tiene una, consulte [Cree su cuenta gratuita de Azure hoy mismo](https://azure.microsoft.com/free/).
* Una [**máquina virtual de ciencia de los datos de Linux**](https://azure.microsoft.com/marketplace/partners/microsoft-ads/linux-data-science-vm). Para más información sobre el aprovisionamiento de esta máquina virtual, consulte [Aprovisionamiento de Linux Data Science Virtual Machine](linux-dsvm-intro.md).
* [X2Go](http://wiki.x2go.org/doku.php) instalado en su equipo y abierta una sesión de XFCE. Para más información sobre la instalación y la configuración de un **cliente X2Go**, consulte [Instalación y configuración del cliente X2Go](linux-dsvm-intro.md#installing-and-configuring-x2go-client).
* Para una experiencia más fluida de desplazamiento, active o desactive la marca gfx.xrender.enabled en about:config en el explorador FireFox de las máquinas virtuales. [Consulte más información aquí.](https://www.reddit.com/r/firefox/comments/4nfmvp/ff_47_unbearable_slow_over_remote_x11/). Considere también la posibilidad de alternar *mousewheel.enable_pixel_scrolling* en False. [Consulte las instrucciones aquí.](https://support.mozilla.org/en-US/questions/981140)
* Una **cuenta de AzureML**. Si aún no tiene una, suscríbase en la [página principal de AzureML](https://studio.azureml.net/). Hay un nivel de uso gratuito para ayudarle a comenzar.

## <a name="download-the-spambase-dataset"></a>Descarga del conjunto de datos spambase
El conjunto de datos [spambase](https://archive.ics.uci.edu/ml/datasets/spambase) es un conjunto de datos relativamente pequeño que contiene únicamente 4601 ejemplos. Este tamaño es adecuado para demostrar algunas de las características principales de la máquina virtual de Data Sciencie ya que mantiene los requisitos de recursos en un nivel modesto.

> [!NOTE]
> Este tutorial se creó en una máquina virtual Linux Data Science de tamaño D2 v2. Esta DSVM tiene la capacidad para controlar los procedimientos de este tutorial.
>
>

Si necesita más espacio de almacenamiento, puede crear discos adicionales y conectarlos a la máquina virtual. Estos discos usan almacenamiento de Azure persistente, por lo que sus datos se conservan incluso cuando el servidor se reaprovisiona debido a un cambio de tamaño o se apaga. Para agregar un disco y conectarlo a la máquina virtual, siga las instrucciones que se describen en [Adición de un disco a una máquina virtual Linux](../../virtual-machines/linux/add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). En estos pasos se usa la interfaz de la línea de comandos de Azure (CLI de Azure), que ya está instalada en la DSVM. De modo que estos procedimientos se pueden realizar por completo en la propia máquina virtual. Otra opción para aumentar el almacenamiento es usar [archivos de Azure](../../storage/files/storage-how-to-use-files-linux.md).

Para descargar los datos, abra una ventana de terminal y ejecute este comando:

    wget http://archive.ics.uci.edu/ml/machine-learning-databases/spambase/spambase.data

El archivo descargado no tiene una fila de encabezado, así que vamos a crear otro archivo que la tenga. Ejecute este comando para crear un archivo con los encabezados adecuados:

    echo 'word_freq_make, word_freq_address, word_freq_all, word_freq_3d,word_freq_our, word_freq_over, word_freq_remove, word_freq_internet,word_freq_order, word_freq_mail, word_freq_receive, word_freq_will,word_freq_people, word_freq_report, word_freq_addresses, word_freq_free,word_freq_business, word_freq_email, word_freq_you, word_freq_credit,word_freq_your, word_freq_font, word_freq_000, word_freq_money,word_freq_hp, word_freq_hpl, word_freq_george, word_freq_650, word_freq_lab,word_freq_labs, word_freq_telnet, word_freq_857, word_freq_data,word_freq_415, word_freq_85, word_freq_technology, word_freq_1999,word_freq_parts, word_freq_pm, word_freq_direct, word_freq_cs, word_freq_meeting,word_freq_original, word_freq_project, word_freq_re, word_freq_edu,word_freq_table, word_freq_conference, char_freq_semicolon, char_freq_leftParen,char_freq_leftBracket, char_freq_exclamation, char_freq_dollar, char_freq_pound, capital_run_length_average,capital_run_length_longest, capital_run_length_total, spam' > headers

A continuación, concatene los dos archivos junto con el comando:

    cat spambase.data >> headers
    mv headers spambaseHeaders.data

El conjunto de datos tiene varios tipos de estadísticas sobre cada correo electrónico:

* Las columnas como ***word\_freq\_WORD*** indican el porcentaje de palabras en el correo electrónico que coinciden con *WORD*. Por ejemplo, si *word\_freq\_make* es 1, el 1% de todas las palabras en el correo electrónico son *make*.
* Las columnas como ***char\_freq\_CHAR*** indican el porcentaje de todos los caracteres en el correo electrónico que son *CHAR*.
* ***capital\_run\_length\_longest*** es la mayor longitud de una secuencia de letras mayúsculas.
* ***capital\_run\_length\_average*** es la longitud media de todas las secuencias de letras mayúsculas.
* ***capital\_run\_length\_total*** es la longitud media de todas las secuencias de letras mayúsculas.
* ***spam*** indica si el correo electrónico se considera correo no deseado o no (1 = es correo no deseado, 0 = no es correo no deseado).

## <a name="explore-the-dataset-with-microsoft-r-open"></a>Exploración del conjunto de datos con Microsoft R Open
Vamos a examinar los datos y a realizar algunas tareas básicas de aprendizaje automático con R. La máquina virtual de Data Science viene preinstalada con [Microsoft R Open](https://mran.revolutionanalytics.com/open/). Las bibliotecas matemáticas multiproceso de esta versión de R ofrecen un rendimiento mejor que diversas versiones de un único subproceso. Microsoft R Open proporciona también reproducibilidad mediante una instantánea del repositorio de paquetes CRAN.

Para obtener copias de los ejemplos de código usados en este tutorial, clone el repositorio **Azure-Machine-Learning-Data-Science** con GIT, que viene preinstalado en la máquina virtual. Desde la línea de comandos de GIT, ejecute:

    git clone https://github.com/Azure/Azure-MachineLearning-DataScience.git

Abra una ventana de terminal e inicie una nueva sesión de R con la consola interactiva de R.

> [!NOTE]
> También puede usar RStudio para los siguientes procedimientos. Para instalar RStudio, ejecute este comando en un terminal: `./Desktop/DSVM\ tools/installRStudio.sh`
>
>

Para importar los datos y configurar el entorno, ejecute:

    data <- read.csv("spambaseHeaders.data")
    set.seed(123)

Para ver estadísticas de resumen sobre cada columna:

    summary(data)

Para tener una vista diferente de los datos:

    str(data)

Esto muestra el tipo de cada variable y algunos de los primeros valores del conjunto de datos.

La columna *spam* se leyó como un entero, pero es en realidad una variable (o factor) categórica. Para establecer su tipo:

    data$spam <- as.factor(data$spam)

Para realizar un análisis de exploración, use el paquete [ggplot2](http://ggplot2.org/) , una conocida biblioteca de gráficos para R que ya está instalada en la máquina virtual. Observe, por los datos de resumen que se mostraron anteriormente, que tenemos estadísticas de resumen sobre la frecuencia del carácter de signo de exclamación. Vamos a trazar aquí esas frecuencias con los siguientes comandos:

    library(ggplot2)
    ggplot(data) + geom_histogram(aes(x=char_freq_exclamation), binwidth=0.25)

Puesto que la barra de cero está sesgando el trazado, vamos a deshacernos de ella:

    email_with_exclamation = data[data$char_freq_exclamation > 0, ]
    ggplot(email_with_exclamation) + geom_histogram(aes(x=char_freq_exclamation), binwidth=0.25)

Hay una densidad no trivial por encima de 1 que parece interesante. Echemos un vistazo solo a esos datos:

    ggplot(data[data$char_freq_exclamation > 1, ]) + geom_histogram(aes(x=char_freq_exclamation), binwidth=0.25)

A continuación, los vamos a dividir en: es correo no deseado y no es correo no deseado:

    ggplot(data[data$char_freq_exclamation > 1, ], aes(x=char_freq_exclamation)) +
    geom_density(lty=3) +
    geom_density(aes(fill=spam, colour=spam), alpha=0.55) +
    xlab("spam") +
    ggtitle("Distribution of spam \nby frequency of !") +
    labs(fill="spam", y="Density")

Estos ejemplos deben permitirle realizar trazados similares de las demás columnas para explorar los datos contenidos en ellas.

## <a name="train-and-test-an-ml-model"></a>Entrenamiento y prueba de un modelo de Machine Learning
Ahora vamos a entrenar un par de modelos de aprendizaje automático para clasificar los correos electrónicos del conjunto de datos según si son correo no deseado o no es correo no deseado. Hemos entrenado un modelo de árbol de decisiones y un modelo de bosque aleatorio en esta sección y después probaremos su precisión en las predicciones.

> [!NOTE]
> El paquete rpart (árboles de regresión y particionamiento recursivo) usado en el siguiente código ya está instalado en la máquina virtual de Data Science.
>
>

En primer lugar, vamos a dividir el conjunto de datos en conjuntos de entrenamiento y conjuntos de prueba:

    rnd <- runif(dim(data)[1])
    trainSet = subset(data, rnd <= 0.7)
    testSet = subset(data, rnd > 0.7)

Luego, crearemos un árbol de decisiones para clasificar los correos electrónicos.

    require(rpart)
    model.rpart <- rpart(spam ~ ., method = "class", data = trainSet)
    plot(model.rpart)
    text(model.rpart)

El resultado es este:

![1](./media/linux-dsvm-walkthrough/decision-tree.png)

Para determinar su rendimiento en el conjunto de entrenamiento, use el siguiente código:

    trainSetPred <- predict(model.rpart, newdata = trainSet, type = "class")
    t <- table(`Actual Class` = trainSet$spam, `Predicted Class` = trainSetPred)
    accuracy <- sum(diag(t))/sum(t)
    accuracy

Para determinar su rendimiento en el conjunto de prueba:

    testSetPred <- predict(model.rpart, newdata = testSet, type = "class")
    t <- table(`Actual Class` = testSet$spam, `Predicted Class` = testSetPred)
    accuracy <- sum(diag(t))/sum(t)
    accuracy

Vamos a probar un modelo de bosque aleatorio. Los bosques aleatorios entrenan multitud de árboles de decisiones y generan una clase que es el modo de las clasificaciones de todos los árboles de decisiones individuales. Proporcionan un enfoque de aprendizaje automático más poderoso ya que corrigen la tendencia de un modelo de árbol de decisiones a saturar en exceso un conjunto de datos de entrenamiento.

    require(randomForest)
    trainVars <- setdiff(colnames(data), 'spam')
    model.rf <- randomForest(x=trainSet[, trainVars], y=trainSet$spam)

    trainSetPred <- predict(model.rf, newdata = trainSet[, trainVars], type = "class")
    table(`Actual Class` = trainSet$spam, `Predicted Class` = trainSetPred)

    testSetPred <- predict(model.rf, newdata = testSet[, trainVars], type = "class")
    t <- table(`Actual Class` = testSet$spam, `Predicted Class` = testSetPred)
    accuracy <- sum(diag(t))/sum(t)
    accuracy


## <a name="deploy-a-model-to-azure-ml"></a>Implementación de un modelo en Azure ML
[Azure Machine Learning Studio](https://studio.azureml.net/) (AzureML) es un servicio en la nube que permite la creación e implementación de forma fácil de modelos de análisis predictivo. Una de las interesantes características de AzureML es la posibilidad de publicar cualquier función de R como un servicio web. El paquete de R de AzureML permite realizar la implementación de forma fácil, justo desde nuestra sesión en la DSVM.

Para implementar el código del árbol de decisiones de la sección anterior, debe iniciar sesión en Azure Machine Learning Studio. Necesita el identificador del área de trabajo y un token de autorización para iniciar sesión. Para encontrar estos valores e inicializar las variables de AzureML con ellos:

Seleccione **Configuración** en el menú izquierdo. Anote su **WORKSPACE ID**(ID DE ÁREA DE TRABAJO). ![2](./media/linux-dsvm-walkthrough/workspace-id.png)

Seleccione **Authorization Tokens** (Tokens de autorización) en el menú principal y anote su **Primary Authorization Token** (Token de autorización principal).![3](./media/linux-dsvm-walkthrough/workspace-token.png)

Cargue el paquete de **AzureML** y luego establezca los valores de las variables con su token y su id. de área de trabajo en la sesión de R de la DSVM:

    require(AzureML)
    wsAuth = "<authorization-token>"
    wsID = "<workspace-id>"


Vamos a simplificar el modelo para que esta demostración sea más fácil de implementar. Seleccione las tres variables del árbol de decisiones más cercanas a la raíz y cree un nuevo árbol solo con esas tres variables:

    colNames <- c("char_freq_dollar", "word_freq_remove", "word_freq_hp", "spam")
    smallTrainSet <- trainSet[, colNames]
    smallTestSet <- testSet[, colNames]
    model.rpart <- rpart(spam ~ ., method = "class", data = smallTrainSet)

Necesitamos una función de predicción que tome las características como una entrada y devuelva los valores previstos:

    predictSpam <- function(char_freq_dollar, word_freq_remove, word_freq_hp) {
        predictDF <- predict(model.rpart, data.frame("char_freq_dollar" = char_freq_dollar,
        "word_freq_remove" = word_freq_remove, "word_freq_hp" = word_freq_hp))
        return(colnames(predictDF)[apply(predictDF, 1, which.max)])
    }

Publique la función predictSpam en AzureML mediante la función **publishWebService** :

    spamWebService <- publishWebService("predictSpam",
        "spamWebService",
        list("char_freq_dollar"="float", "word_freq_remove"="float","word_freq_hp"="float"),
        list("spam"="int"),
        wsID, wsAuth)

Esta función toma la función **predictSpam**, crea un servicio web llamado **spamWebService** con entradas y salidas definidas y devuelve información sobre el nuevo punto de conexión.

Vea los detalles del servicio web publicado, junto con su punto de conexión de API y las claves de acceso con el comando:

    spamWebService[[2]]

Para probarlo en las 10 primeras filas del conjunto de prueba:

    consumeDataframe(spamWebService$endpoints[[1]]$PrimaryKey, spamWebService$endpoints[[1]]$ApiLocation, smallTestSet[1:10, 1:3])


## <a name="use-other-tools-available"></a>Uso de otras herramientas disponibles
En las secciones restantes se muestra cómo usar algunas de las herramientas instaladas en la máquina virtual de Linux Data Science. Esta es la lista de herramientas que se describe:

* XGBoost
* Python
* Jupyterhub
* Rattle
* PostgreSQL & Squirrel SQL
* SQL Server Data Warehouse

## <a name="xgboost"></a>XGBoost
[XGBoost](https://xgboost.readthedocs.org/en/latest/) es una herramienta que proporciona una implementación de árbol ampliada rápida y precisa.

    require(xgboost)
    data <- read.csv("spambaseHeaders.data")
    set.seed(123)

    rnd <- runif(dim(data)[1])
    trainSet = subset(data, rnd <= 0.7)
    testSet = subset(data, rnd > 0.7)

    bst <- xgboost(data = data.matrix(trainSet[,0:57]), label = trainSet$spam, nthread = 2, nrounds = 2, objective = "binary:logistic")

    pred <- predict(bst, data.matrix(testSet[, 0:57]))
    accuracy <- 1.0 - mean(as.numeric(pred > 0.5) != testSet$spam)
    print(paste("test accuracy = ", accuracy))

XGBoost también se puede llamar desde Python o una línea de comandos.

## <a name="python"></a>Python
Para el desarrollo con Python, se han instalado las distribuciones 2.7 y 3.5 de Anaconda Python en la DSVM.

> [!NOTE]
> La distribución Anaconda incluye [Condas](http://conda.pydata.org/docs/index.html), que se puede usar para crear entornos personalizados para Python que tengan instalados diferentes versiones o paquetes.
>
>

Vamos a leer algunos de los conjuntos de datos spambase y a clasificar los correos electrónicos con máquinas vectoriales de apoyo en scikit-learn:

    import pandas
    from sklearn import svm    
    data = pandas.read_csv("spambaseHeaders.data", sep = ',\s*')
    X = data.ix[:, 0:57]
    y = data.ix[:, 57]
    clf = svm.SVC()
    clf.fit(X, y)

Para realizar predicciones:

    clf.predict(X.ix[0:20, :])

Para mostrar cómo publicar un punto de conexión de AzureML, vamos a crear un modelo más simple con tres variables como hicimos cuando publicamos el modelo de R anteriormente.

    X = data.ix[["char_freq_dollar", "word_freq_remove", "word_freq_hp"]]
    y = data.ix[:, 57]
    clf = svm.SVC()
    clf.fit(X, y)

Para publicar el modelo en AzureML:

    # Publish the model.
    workspace_id = "<workspace-id>"
    workspace_token = "<workspace-token>"
    from azureml import services
    @services.publish(workspace_id, workspace_token)
    @services.types(char_freq_dollar = float, word_freq_remove = float, word_freq_hp = float)
    @services.returns(int) # 0 or 1
    def predictSpam(char_freq_dollar, word_freq_remove, word_freq_hp):
        inputArray = [char_freq_dollar, word_freq_remove, word_freq_hp]
        return clf.predict(inputArray)

    # Get some info about the resulting model.
    predictSpam.service.url
    predictSpam.service.api_key

    # Call the model
    predictSpam.service(1, 1, 1)

> [!NOTE]
> Esto solo está disponible para Python 2.7 y todavía no se admite en la versión 3.5. Realice la ejecución con **/anaconda/bin/python2.7**.
>
>

## <a name="jupyterhub"></a>Jupyterhub
La distribución Anaconda en la DSVM viene con Jupyter Notebook, un entorno multiplataforma para compartir código y análisis de Python, R o Julia. Se accede a Jupyter Notebook mediante JupyterHub. Para iniciar sesión se usa el nombre de usuario local y la contraseña de Linux en ***https://\<nombre DNS o dirección IP de la máquina virtual\>:8000/***. Todos los archivos de configuración de JupyterHub se encuentran en el directorio **/etc/jupyterhub**.

Hay varios cuadernos de muestra ya instalados en la máquina virtual:

* Consulte [IntroToJupyterPython.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Data-Science-Virtual-Machine/Samples/Notebooks/IntroToJupyterPython.ipynb) para ver un cuaderno de ejemplo de Python.
* Consulte [IntroTutorialinR](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Data-Science-Virtual-Machine/Samples/Notebooks/IntroTutorialinR.ipynb) para ver un cuaderno de **R** de ejemplo.
* Consulte [IrisClassifierPyMLWebService](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Data-Science-Virtual-Machine/Samples/Notebooks/IrisClassifierPyMLWebService.ipynb) para ver otro cuaderno de ejemplo de **Python** .

> [!NOTE]
> El lenguaje Julia también está disponible desde la línea de comandos en la máquina virtual de Linux Data Science.
>
>

## <a name="rattle"></a>Rattle
[Rattle](https://cran.r-project.org/web/packages/rattle/index.html) (R Analytical Tool To Learn Easily) es una herramienta gráfica de R para la minería de datos. Presenta una interfaz intuitiva que permite cargar, explorar y transformar los datos y crear y evaluar modelos de forma fácil.  El artículo [Rattle: A Data Mining GUI for R](https://journal.r-project.org/archive/2009-2/RJournal_2009-2_Williams.pdf) (Rattle: una GUI de minería de datos para R) proporciona un tutorial que demuestra sus características.

Instale e inicie Rattle con los siguientes comandos:

    if(!require("rattle")) install.packages("rattle")
    require(rattle)
    rattle()

> [!NOTE]
> No es necesario instalarlo en la DSVM. Sin embargo, Rattle puede pedirle que instale paquetes adicionales cuando se carga.
>
>

Rattle usa una interfaz de usuario basada en pestañas. La mayoría de las pestañas corresponden a pasos del [proceso de ciencia de los datos](https://azure.microsoft.com/documentation/learning-paths/data-science-process/), como cargar los datos o explorarlos. El proceso de ciencia de los datos fluye de izquierda a derecha por las pestañas. Pero la última pestaña contiene un registro de los comandos de R ejecutados por Rattle.

Para cargar y configurar el conjunto de datos:

* Para cargar el archivo, seleccione la pestaña **Data** (Datos).
* Elija el selector junto a **Filename** (Nombre de archivo) y elija **spambaseHeaders.data**.
* Para cargar el archivo seleccione **Execute** (Ejecutar) en la fila superior de botones. Verá un resumen de cada columna, junto con su tipo de datos identificado, si es una entrada, un destino u otro tipo de variable, y el número de valores únicos.
* Rattle ha identificado correctamente la columna **correo no deseado** como el destino. Seleccione la columna de correo no deseado y luego establezca el **tipo de datos de destino** en **Categoric** (Categórico).

Para explorar los datos:

* Seleccione la pestaña **Explore** (Explorar).
* Haga clic en **Summary** (Resumen) y luego en **Execute** (Ejecutar) para ver alguna información sobre los tipos de variables y algunas estadísticas de resumen.
* Para ver otros tipos de estadísticas sobre cada variable, seleccione otras opciones como **Describe** (Describir) o **Basics** (Fundamentos).

La pestaña **Explore** (Explorar) le permite generar muchos trazados detallados. Para trazar un histograma de los datos:

* Seleccione **Distributions**(Distribuciones).
* Busque en **Histogram** (Histograma) **word_freq_remove** y **word_freq_you**.
* Seleccione **Execute**(Ejecutar). Verá ambos trazados de densidad en una sola ventana gráfica, donde está claro que la palabra "you" aparece con mucha más frecuencia que "remove".

Los trazados de correlación también son interesantes. Para crear uno:

* Elija **Correlation** (Correlación) como el **tipo**.
* Seleccione **Execute**(Ejecutar).
* Rattle le avisa de que recomienda 40 variables como máximo. Seleccione **Yes** (Sí) para ver la trazado.

Surgen algunas correlaciones interesantes: por ejemplo, "technology" está estrechamente relacionado con "HP" y "labs". También está estrechamente correlacionado con "650", porque el código de área de los donantes del conjunto de datos es 650.

Los valores numéricos de las correlaciones entre palabras están disponibles en la ventana de exploración. Es interesante advertir, por ejemplo, que "technology" está correlacionada negativamente con "your" y "money".

Rattle puede transformar el conjunto de datos para tratar algunos problemas comunes. Por ejemplo, le permite volver a escalar características, imputar valores que faltan, administrar los valores atípicos y quitar variables u observaciones con datos que faltan. Rattle también puede identificar reglas de asociación entre observaciones o variables. Estas pestañas escapan del ámbito de este tutorial de introducción.

Rattle también puede realizar análisis del clúster. Vamos a excluir algunas características para que la salida sea más fácil de leer. En la pestaña **Data** (Datos), elija **Ignore** (Ignorar) junto a cada una de las variables excepto estos diez términos:

* word_freq_hp
* word_freq_technology
* word_freq_george
* word_freq_remove
* word_freq_your
* word_freq_dollar
* word_freq_money
* capital_run_length_longest
* word_freq_business
* spam

A continuación, vuelva a la pestaña **Cluster** (Clúster), elija **KMeans** y establezca *Number of clusters* (Número de clústeres) en 4. A continuación, elija **Execute**(Ejecutar). Los resultados se muestran en la ventana de salida. Un clúster tiene alta frecuencia de "george" y "hp" y es probablemente un correo electrónico comercial legítimo.

Para crear un modelo de aprendizaje automático de árbol de decisiones sencillo:

* Seleccione la pestaña **Model** (Modelo).
* Elija **Tree** (Árbol) en **Type** (Tipo).
* Seleccione **Execute** (Ejecutar) para mostrar el árbol en forma de texto en la ventana de salida.
* Seleccione el botón **Draw** (Dibujar) para ver una versión gráfica. Se parece bastante al árbol que obtuvimos anteriormente mediante *rpart*.

Una de las características interesantes de Rattle es la posibilidad de ejecutar varios métodos de aprendizaje automático y evaluarlos rápidamente. A continuación se muestra el procedimiento:

* Elija **All** (Todo) como **Type**.
* Seleccione **Execute**(Ejecutar).
* Cuando finalice, puede hacer clic en cualquier **tipo**, como **SVM** y ver los resultados.
* También puede comparar el rendimiento de los modelos en el conjunto de validación mediante la pestaña **Evaluate** (Evaluar). Por ejemplo, la selección **Error Matrix** (Matriz de errores) muestra la matriz de confusiones, los errores generales y la media de errores de clase de cada modelo en el conjunto de validación.
* También puede trazar curvas ROC, realizar análisis de sensibilidad y llevar a cabo otros tipos de evaluaciones de modelos.

Cuando haya terminado de crear modelos, seleccione la pestaña **Log** (Registrar) para ver el código R ejecutado por Rattle durante la sesión. Puede seleccionar el botón **Export** (Exportar) para guardarlo.

> [!NOTE]
> Hay un error en la versión actual de Rattle. Para modificar el script o usarlo para repetir los pasos más adelante, debe insertar un carácter # delante de *Export this log ... * (Exportar este registro) en el texto del registro.
>
>

## <a name="postgresql--squirrel-sql"></a>PostgreSQL & Squirrel SQL
La DSVM viene con PostgreSQL instalado. PostgreSQL es una base de datos relacional sofisticada de código abierto. En esta sección se muestra cómo cargar nuestro conjunto de datos de correo no deseado en PostgreSQL y luego consultarlo.

Antes de cargar los datos, debe permitir la autenticación de contraseña desde el host local. En un símbolo del sistema:

    sudo gedit /var/lib/pgsql/data/pg_hba.conf

Cerca de la parte inferior del archivo de configuración, hay varias líneas que detallan las conexiones permitidas:

    # "local" is for Unix domain socket connections only
    local   all             all                                     trust
    # IPv4 local connections:
    host    all             all             127.0.0.1/32            ident
    # IPv6 local connections:
    host    all             all             ::1/128                 ident

Cambie la línea "IPv4 local connections" para usar md5 en lugar de ident, así podremos iniciar sesión con un nombre de usuario y una contraseña:

    # IPv4 local connections:
    host    all             all             127.0.0.1/32            md5

Reinicie el servicio de postgres:

    sudo systemctl restart postgresql

Para iniciar psql, un terminal interactivo para PostgreSQL, con el usuario de postgres integrado, ejecuta el siguiente comando desde un símbolo del sistema:

    sudo -u postgres psql

Cree una nueva cuenta de usuario, con el mismo nombre de usuario que el de la cuenta de Linux con el que ha iniciado la sesión, y proporcione una contraseña:

    CREATE USER <username> WITH CREATEDB;
    CREATE DATABASE <username>;
    ALTER USER <username> password '<password>';
    \quit

A continuación, inicie sesión en psql con su usuario:

    psql

Importe los datos en una base de datos:

    CREATE DATABASE spam;
    \c spam
    CREATE TABLE data (word_freq_make real, word_freq_address real, word_freq_all real, word_freq_3d real,word_freq_our real, word_freq_over real, word_freq_remove real, word_freq_internet real,word_freq_order real, word_freq_mail real, word_freq_receive real, word_freq_will real,word_freq_people real, word_freq_report real, word_freq_addresses real, word_freq_free real,word_freq_business real, word_freq_email real, word_freq_you real, word_freq_credit real,word_freq_your real, word_freq_font real, word_freq_000 real, word_freq_money real,word_freq_hp real, word_freq_hpl real, word_freq_george real, word_freq_650 real, word_freq_lab real,word_freq_labs real, word_freq_telnet real, word_freq_857 real, word_freq_data real,word_freq_415 real, word_freq_85 real, word_freq_technology real, word_freq_1999 real,word_freq_parts real, word_freq_pm real, word_freq_direct real, word_freq_cs real, word_freq_meeting real,word_freq_original real, word_freq_project real, word_freq_re real, word_freq_edu real,word_freq_table real, word_freq_conference real, char_freq_semicolon real, char_freq_leftParen real,char_freq_leftBracket real, char_freq_exclamation real, char_freq_dollar real, char_freq_pound real, capital_run_length_average real, capital_run_length_longest real, capital_run_length_total real, spam integer);
    \copy data FROM /home/<username>/spambase.data DELIMITER ',' CSV;
    \quit

Ahora, vamos a explorar los datos y a ejecutar algunas consultas mediante **Squirrel SQL**, una herramienta gráfica que le permite interactuar con bases de datos mediante un controlador JDBC.

Para comenzar, inicie SQL Squirrel desde el menú de aplicaciones. Para configurar el controlador:

* Seleccione **Windows** y luego **View Drivers** (Ver controladores).
* Haga clic con el botón derecho en **PostgreSQL** y seleccione **Modify Driver** (Modificar controlador).
* Seleccione **Extra Class Path** (Ruta de clase adicional) y luego **Add** (Agregar).
* Escriba ***/usr/share/java/jdbcdrivers/postgresql-9.4.1208.jre6.jar*** para **File Name** (Nombre de archivo) y
* seleccione **Open**(Abrir).
* Elija List Drivers (Mostrar controladores) y seleccione **org.postgresql.Driver** en **Class Name** (Nombre de clase), a continuación, seleccione **OK** (Aceptar).

Para configurar la conexión al servidor local:

* Seleccione **Windows** y luego **View Aliases** (Ver alias).
* Elija el botón **+** para crear un nuevo alias.
* Asígnele el nombre *Spam database* (Base de datos de correo no deseado) y elija **PostgreSQL** en la lista desplegable **Driver** (Controlador).
* Establezca la dirección URL en *jdbc:postgresql://localhost/spam*.
* Escriba su *nombre de usuario* y *contraseña*.
* Haga clic en **Aceptar**.
* Para abrir la ventana **Connection** (Conexión), haga doble clic en el alias ***Spam database*** (Base de datos de correo no deseado).
* Seleccione **Conectar**.

Para ejecutar algunas consultas:

* Seleccione la pestaña **SQL** .
* Escriba una consulta sencilla, por ejemplo `SELECT * from data;` en el cuadro de texto de consulta en la parte superior de la pestaña SQL.
* Presione **Ctrl + Entrar** para ejecutarla. Squirrel SQL devuelve de forma predeterminada las 100 primeras filas de la consulta.

Hay muchas más consultas que se podrían ejecutar para explorar estos datos. Por ejemplo, ¿de qué modo la frecuencia de la palabra *make* es diferente en es correo no deseado y no es correo no deseado?

    SELECT avg(word_freq_make), spam from data group by spam;

O, ¿cuáles son las características del correo electrónico que con frecuencia contiene *3d*?

    SELECT * from data order by word_freq_3d desc;

La mayoría de los correos electrónicos en los que aparece *3d* con frecuencia es aparentemente correo no deseado, así que podría ser una característica útil para crear un modelo predictivo para clasificar los correos electrónicos.

Si quisiera realizar aprendizaje automático con datos almacenados en una base de datos de PostgreSQL, podría usar [MADlib](http://madlib.incubator.apache.org/).

## <a name="sql-server-data-warehouse"></a>SQL Server Data Warehouse
Almacenamiento de datos SQL de Azure es una base de datos de escalado horizontal y basada en la nube capaz de procesar volúmenes masivos de datos (tanto relacionales como no relacionales). Para más información, consulte [¿Qué es Azure SQL Data Warehouse?](../../sql-data-warehouse/sql-data-warehouse-overview-what-is.md)

Para conectarse al almacén de datos y crear la tabla, ejecute el siguiente comando desde un símbolo del sistema:

    sqlcmd -S <server-name>.database.windows.net -d <database-name> -U <username> -P <password> -I

A continuación, en el símbolo del sistema de sqlcmd:

    CREATE TABLE spam (word_freq_make real, word_freq_address real, word_freq_all real, word_freq_3d real,word_freq_our real, word_freq_over real, word_freq_remove real, word_freq_internet real,word_freq_order real, word_freq_mail real, word_freq_receive real, word_freq_will real,word_freq_people real, word_freq_report real, word_freq_addresses real, word_freq_free real,word_freq_business real, word_freq_email real, word_freq_you real, word_freq_credit real,word_freq_your real, word_freq_font real, word_freq_000 real, word_freq_money real,word_freq_hp real, word_freq_hpl real, word_freq_george real, word_freq_650 real, word_freq_lab real,word_freq_labs real, word_freq_telnet real, word_freq_857 real, word_freq_data real,word_freq_415 real, word_freq_85 real, word_freq_technology real, word_freq_1999 real,word_freq_parts real, word_freq_pm real, word_freq_direct real, word_freq_cs real, word_freq_meeting real,word_freq_original real, word_freq_project real, word_freq_re real, word_freq_edu real,word_freq_table real, word_freq_conference real, char_freq_semicolon real, char_freq_leftParen real,char_freq_leftBracket real, char_freq_exclamation real, char_freq_dollar real, char_freq_pound real, capital_run_length_average real, capital_run_length_longest real, capital_run_length_total real, spam integer) WITH (CLUSTERED COLUMNSTORE INDEX, DISTRIBUTION = ROUND_ROBIN);
    GO

Copie los datos con bcp:

    bcp spam in spambaseHeaders.data -q -c -t  ',' -S <server-name>.database.windows.net -d <database-name> -U <username> -P <password> -F 1 -r "\r\n"

> [!NOTE]
> Los extremos de las líneas en el archivo descargable son de estilo Windows, pero bcp espera estilo UNIX, por lo que debemos indicar a bcp que con la marca -r.
>
>

Y la consulta con sqlcmd:

    select top 10 spam, char_freq_dollar from spam;
    GO

También puede consultar con Squirrel SQL. Siga pasos similares para PostgreSQL, mediante el controlador JDBC de Microsoft MSSQL Server, que se puede encontrar en ***/usr/share/java/jdbcdrivers/sqljdbc42.jar***.

## <a name="next-steps"></a>Pasos siguientes
Para ver una introducción de los temas que lo guiarán por las tareas que componen el proceso de ciencia de datos en Azure, consulte [Proceso de ciencia de los datos en equipos (TDSP)](http://aka.ms/datascienceprocess).

Para ver una descripción de otros tutoriales completos que demuestren los pasos del proceso de ciencia de los datos en equipo en escenarios concretos, consulte [Tutoriales del proceso de ciencia de datos en equipos](../team-data-science-process/walkthroughs.md). En los tutoriales también se muestra cómo combinar servicios y herramientas en la nube y locales en un flujo de trabajo o una canalización con el fin de crear una aplicación inteligente.
