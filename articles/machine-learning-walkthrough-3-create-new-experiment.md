<properties title="Step 3: Create a new Azure Machine Learning experiment" pageTitle="Step 3: Create a new Machine Learning experiment | Azure" description="Step 3: Create a new training experiment in Azure Machine Learning Studio" metaKeywords="" services="" solutions="" documentationCenter="" authors="garye" videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="garye"></tags>

Este es el tercer paso del tutorial [Desarrollo de una solución predictiva con el Aprendizaje automático de Azure][Desarrollo de una solución predictiva con el Aprendizaje automático de Azure]:

1.  [Creación de un área de trabajo de Aprendizaje automático][Creación de un área de trabajo de Aprendizaje automático]
2.  [Carga de los datos existentes][Carga de los datos existentes]
3.  **Creación de un nuevo experimento**
4.  [Entrenamiento y evaluación de los modelos][Entrenamiento y evaluación de los modelos]
5.  [Publicación del servicio web][Publicación del servicio web]
6.  [Acceso al servicio web][Acceso al servicio web]

------------------------------------------------------------------------

# Paso 3: Creación de un nuevo experimento de Aprendizaje automático de Azure

Tenemos que crear un nuevo experimento en Estudio de aprendizaje automático que utilice el conjunto de datos que acabamos de cargar.

1.  En el Estudio de aprendizaje automático, haga clic en **+NUEVO** en la parte inferior de la ventana.
2.  Seleccione **EXPERIMENTO**.
3.  En la paleta de módulos, a la izquierda del lienzo de experimentos, expanda **Conjuntos de datos guardados**.
4.  Busque el conjunto de datos que ha creado y arrástrelo al lienzo. También puede buscar el conjunto de datos escribiendo su nombre en el cuadro **Buscar** que está encima de la paleta.

## Preparación de los datos

Puede ver las primeras 100 filas de datos, y alguna información estadística de todo el conjunto de datos, haciendo clic con el botón secundario en el puerto de salida del conjunto de datos y seleccionando **Visualizar**. Observe que Estudio de aprendizaje automático ya ha identificado el tipo de datos de cada columna. También ha puesto encabezado genéricos a las columnas dado que el archivo de datos no contenía encabezados de columna.

Los encabezados de columna no son esenciales, pero facilitarán el trabajo con los datos en el modelo. Además, si eventualmente se publicara este modelo en un servicio web, los encabezados ayudarían a identificar las columnas al usuario del servicio.

Podemos agregar encabezados de columna mediante el módulo **Editor de metadatos**.

1.  En la paleta de módulos, escriba "metadatos" en el cuadro **Buscar**. Verá **Editor de metadatos** en la lista de módulos.
2.  Haga clic en el módulo **Editor de metadatos**, arrástrelo al lienzo y colóquelo bajo el conjunto de datos.
3.  Conecte el conjunto de datos al **Editor de metadatos**: haga clic en el puerto de salida del conjunto de datos, arrastre el puerto de entrada del **Editor de metadatos** y, a continuación, suelte el botón del mouse. El conjunto de datos y el módulo permanecerán conectados incluso si se desplaza por el lienzo.
4.  En el panel **Propiedades** a la derecha del lienzo, haga clic en **Iniciar selector de columnas**.
5.  Compruebe que está seleccionada la opción "Todas las columnas" en la lista desplegable y, a continuación, haga clic en **Aceptar**. Esto hace que el **Editor de metadatos** opere sobre todas las columnas de datos.
6.  En el parámetro **Nuevo nombre de columna**, escriba la lista de nombres de las 21 columnas del conjunto de datos, separadas por comas y en orden de columnas. Puede obtener los nombres de columnas de la documentación del conjunto de datos en el sitio web de UCI, o por comodidad puede copiar y pegar lo siguiente:

    Status of checking account, Duration in months, Credit history, Purpose, Credit amount, Savings account/bond, Present employment since, Installment rate in percentage of disposable income, Personal status and sex, Other debtors, Present residence since, Property, Age in years, Other installment plans, Housing, Number of existing credits, Job, Number of people providing maintenance for, Telephone, Foreign worker, Credit risk

El panel Propiedades tendrá un aspecto similar al siguiente:

![Properties for Metadata Editor][Properties for Metadata Editor]

> Sugerencia: si desea comprobar los encabezados de columna, ejecute el experimento (haga clic en **EJECUTAR** bajo el lienzo de experimentos), haga clic en el puerto de salida del módulo **Editor de metadatos** y seleccione **Visualizar**. Puede ver el resultado de cualquier módulo igual que visualiza el progreso de los datos a través del experimento.

El experimento debería tener ahora un aspecto similar al siguiente:

![Adding Metadata Editor][Adding Metadata Editor]

## Creación de conjuntos de datos de entrenamiento y prueba

El siguiente paso del experimento es generar conjuntos de datos independientes que se utilizarán para entrenar y probar nuestro modelo. Para ello, utilizamos el módulo **Dividir**.

1.  Busque el módulo **Dividir**, arrástrelo al lienzo y conéctelo al último módulo **Editor de metadatos**.
2.  De manera predeterminada, la proporción de división es 0,5 y se establece el parámetro **División aleatoria**. Esto significa que una mitad aleatoria de los datos saldrá a través de un puerto del módulo **Dividir** y la otra mitad por el otro. Puede cambiar estos ajustes, así como el parámetro **Valor de inicialización aleatorio**, para cambiar la división entre datos de entrenamiento y de puntuación. Para este ejemplo, lo dejaremos como está.

    > Sugerencia: la proporción de división determina fundamentalmente qué cantidad de datos sale a través del puerto de salida de la izquierda. Por ejemplo, si establece la proporción en 0,7, el 70 % de los datos sale por el puerto de la izquierda y el 30 % por el puerto de la derecha.

Podemos utilizar las salidas del módulo **Dividir** como deseemos, pero vamos a optar por utilizar la salida de la izquierda como datos de entrenamiento y la salida de la derecha como datos de puntuación.

Como se menciona en el sitio web de UCI, el coste de clasificar erróneamente un riesgo de crédito alto como bajo es 5 veces más alto que el coste de clasificar erróneamente un riesgo de crédito bajo como alto. Para tener esto en cuenta, vamos a generar un nuevo conjunto de datos que refleje esta función de coste. En el nuevo conjunto de datos, cada ejemplo de alto riesgo se replica 5 veces, mientras que los ejemplos de bajo riesgo no se replican.

Podemos conseguir esta replicación mediante el código R:

1.  Busque y arrastre el módulo **Ejecutar script R** al lienzo de experimentos y conéctelo al puerto de salida izquierdo del módulo **Dividir**.
2.  En el panel de **Propiedades**, elimine el texto predeterminado en el parámetro **Script** y escriba este script:

        dataset1 <- maml.mapInputPort(1)
        data.set<-dataset1[dataset1[,21]==1,]
        pos<-dataset1[dataset1[,21]==2,]
        for (i in 1:5) data.set<-rbind(data.set,pos)
        maml.mapOutputPort("data.set")

Tenemos que hacer esta misma operación de replicación para cada salida del módulo **Dividir** para que los datos de entrenamiento y puntuación tengan el mismo ajuste en relación al coste.

1.  Haga clic en el módulo **Ejecutar script R** y seleccione **Copiar**.
2.  Haga clic con el botón secundario en el lienzo del experimento y seleccione **Pegar**.
3.  Conecte este módulo **Ejecutar script R** al puerto de salida derecho del módulo **Dividir**.

> Sugerencia: la copia del módulo Ejecutar script R contiene el mismo script que el módulo original. Al copiar y pegar un módulo en el lienzo, la copia retiene todas las propiedades del original.
>
> Nuestro experimento tiene ahora un aspecto similar al siguiente:

![Adding Split module and R scripts][Adding Split module and R scripts]

  [Desarrollo de una solución predictiva con el Aprendizaje automático de Azure]: ../machine-learning-walkthrough-develop-predictive-solution/
  [Creación de un área de trabajo de Aprendizaje automático]: ../machine-learning-walkthrough-1-create-ml-workspace/
  [Carga de los datos existentes]: ../machine-learning-walkthrough-2-upload-data/
  [Entrenamiento y evaluación de los modelos]: ../machine-learning-walkthrough-4-train-and-evaluate-models/
  [Publicación del servicio web]: ../machine-learning-walkthrough-5-publish-web-service/
  [Acceso al servicio web]: ../machine-learning-walkthrough-6-access-web-service/
  [Properties for Metadata Editor]: ./media/machine-learning-walkthrough-3-create-new-experiment/create1.png
  [Adding Metadata Editor]: ./media/machine-learning-walkthrough-3-create-new-experiment/create2.png
  [Adding Split module and R scripts]: ./media/machine-learning-walkthrough-3-create-new-experiment/create3.png
