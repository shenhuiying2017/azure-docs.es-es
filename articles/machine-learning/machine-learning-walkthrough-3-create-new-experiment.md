<properties
    pageTitle="Paso 3: Creación de un nuevo experimento de Aprendizaje automático | Microsoft Azure"
    description="Paso 3 del tutorial Desarrollo de una solución predictiva: crear un nuevo experimento de aprendizaje en Estudio de aprendizaje automático de Azure."
    services="machine-learning"
    documentationCenter=""
    authors="garyericson"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/05/2016" 
    ms.author="garye"/>



# <a name="walkthrough-step-3:-create-a-new-azure-machine-learning-experiment"></a>Paso 3 del tutorial: Creación de un nuevo experimento de Aprendizaje automático de Azure

Este es el tercer paso del tutorial [Desarrollo de una solución de análisis predictiva para la evaluación del riesgo de crédito en Aprendizaje automático de Azure](machine-learning-walkthrough-develop-predictive-solution.md)


1.  [Creación de un área de trabajo de Aprendizaje automático](machine-learning-walkthrough-1-create-ml-workspace.md)
2.  [Carga de los datos existentes](machine-learning-walkthrough-2-upload-data.md)
3.  **Crear un experimento nuevo**
4.  [Entrenamiento y evaluación de los modelos](machine-learning-walkthrough-4-train-and-evaluate-models.md)
5.  [Implementación del servicio web](machine-learning-walkthrough-5-publish-web-service.md)
6.  [Acceso al servicio web](machine-learning-walkthrough-6-access-web-service.md)

----------

El siguiente paso de este tutorial es crear un experimento en Machine Learning Studio que utilice el conjunto de datos cargado.  

1.  En Estudio de aprendizaje automático, haga clic en **+NUEVO** en la parte inferior de la ventana.
2.  Seleccione **EXPERIMENTO**y luego "Experimento en blanco". Seleccione el nombre del experimento predeterminado en la parte superior del lienzo y cámbielo por uno significativo

    > [AZURE.TIP] Se recomienda rellenar los campos **Resumen** y **Descripción** del experimento en el panel **Propiedades**. Estas propiedades ofrecen la oportunidad para documentar el experimento para que cualquier persona que lo vea posteriormente entienda sus objetivos y la metodología.

3.  En la paleta de módulos, a la izquierda del lienzo de experimentos, expanda **Conjuntos de datos guardados**.
4.  Busque el conjunto de datos que ha creado en **Mis conjuntos de datos** y arrástrelo al lienzo. También puede buscar el conjunto de datos escribiendo su nombre en el cuadro **Buscar** que está encima de la paleta.  

##<a name="prepare-the-data"></a>Preparación de los datos
Para ver las primeras 100 filas de datos y alguna información estadística de todo el conjunto de datos, haga clic en el puerto de salida del conjunto de datos (el círculo pequeño de la parte inferior) y seleccione **Visualizar**.  

Dado que el archivo de datos no incluye encabezados de columna, Estudio de aprendizaje automático ha proporcionado encabezados genéricos (Col1, Col2, *etc.*). No es esencial que los encabezados sean perfectos para crear un modelo, pero facilitan el trabajo con los datos del experimento. Además, si finalmente se publicara este modelo en un servicio web, los encabezados ayudarían al usuario del servicio a identificar las columnas.  

Podemos agregar encabezados de columna mediante el módulo [Edit Metadata][edit-metadata] (Editar metadatos).
Use el módulo [Edit Metadata][edit-metadata] (Editar metadatos) para cambiar los metadatos asociados a un conjunto de datos. En este caso, puede proporcionar nombres más descriptivos para los encabezados de columna. 

Para usar [Edit Metadata][edit-metadata] (Editar metadatos), especifique primero las columnas que desea modificar (en este caso, todas). A continuación, especifique la acción que desea realizar en esas columnas (en este caso, cambiar los encabezados de las columnas).

1.  En la paleta de módulos, escriba "metadatos" en el cuadro **Buscar** . El módulo [Edit Metadata][edit-metadata] (Editar metadatos) aparecerá en la lista de módulos.
2.  Haga clic en el módulo [Edit Metadata][edit-metadata] (Editar metadatos), arrástrelo al lienzo y colóquelo bajo el conjunto de datos agregado anteriormente.
3.  Conecte el conjunto de datos al módulo [Edit Metadata][edit-metadata] (Editar metadatos): haga clic en el puerto de salida del conjunto de datos (el círculo pequeño de la parte inferior del conjunto de datos). A continuación, arrastre el puerto de entrada de [Edit Metadata][edit-metadata] (Editar metadatos) (el círculo pequeño de la parte superior del módulo) y luego suelte el botón del mouse. El conjunto de datos y el módulo permanecen conectados aunque se desplace por el lienzo.

    El experimento debería tener ahora un aspecto similar al siguiente:  

    ![Agregar metadatos de edición][2]
    
    El signo de exclamación rojo indica que no hemos configurado aún las propiedades de este módulo. Lo haremos a continuación.
    
    > [AZURE.TIP] Puede agregar un comentario a un módulo; para ello, haga doble clic en el módulo y escriba texto. Esto puede ayudarle a ver de un vistazo lo que el módulo hace en el experimento. En este caso, haga doble clic en el módulo [Edit Metadata][edit-metadata] (Editar metadatos) y escriba el comentario "Agregar encabezados de columna". Haga clic en cualquier lugar del lienzo para cerrar el cuadro de texto. Para mostrar el comentario, haga clic en la flecha abajo en el módulo.

4.  Seleccione [Edit Metadata][edit-metadata] (Editar metadatos) y luego, en el panel **Propiedades** a la derecha del lienzo, haga clic en **Launch column selector** (Iniciar el selector de columnas).
5.  En el cuadro de diálogo **Seleccionar columnas**, elija todas las filas de **Columnas disponibles** y haga clic en > para moverlas a **Columnas seleccionadas**.
El cuadro de diálogo debe ser similar al siguiente: ![Selector de columnas con todas las columnas seleccionadas][4]
7.  Haga clic en la marca de verificación **Aceptar** .
8.  En el panel **Propiedades**, busque el parámetro **Nuevo nombre de columna**. En este campo, escriba la lista de nombres de las 21 columnas del conjunto de datos, separadas por comas y en el orden de las columnas. Puede obtener los nombres de las columnas en la documentación del conjunto de datos en el sitio web de UCI o, para mayor comodidad, puede copiar y pegar la siguiente lista:  

          Status of checking account, Duration in months, Credit history, Purpose, Credit amount, Savings account/bond, Present employment since, Installment rate in percentage of disposable income, Personal status and sex, Other debtors, Present residence since, Property, Age in years, Other installment plans, Housing, Number of existing credits, Job, Number of people providing maintenance for, Telephone, Foreign worker, Credit risk  

    El panel Propiedades tendrá un aspecto similar al siguiente:

    ![Propiedades de los metadatos de edición][1]

> [AZURE.TIP] Si desea comprobar los encabezados de columna, ejecute el experimento (haga clic en **EJECUTAR** debajo del lienzo del experimento). Cuando termine de ejecutarse (aparecerá una marca de verificación verde en [Edit Metadata][edit-metadata] [Editar metadatos]), haga clic en el puerto de salida del módulo [Edit Metadata][edit-metadata] (Editar metadatos) y seleccione **Visualizar**. Puede ver el resultado de cualquier módulo igual que visualiza el progreso de los datos a lo largo del experimento.

##<a name="create-training-and-test-datasets"></a>Creación de conjuntos de datos de entrenamiento y prueba

El siguiente paso del experimento es generar conjuntos de datos independientes que se utilizarán para entrenar y probar nuestro modelo.

Para ello, utilizamos el módulo [Split Data][split] (Dividir datos).  

1.  Busque el módulo [Split Data][split] (Dividir datos), arrástrelo al lienzo y conéctelo al último módulo [Edit Metadata][edit-metadata] (Editar metadatos).
2.  De manera predeterminada, la proporción de división es 0,5 y se establece el parámetro **División aleatoria** . Esto significa que una mitad aleatoria de los datos saldrá a través de un puerto del módulo [Split Data][split] (Dividir datos) y la otra mitad, por el otro. Puede cambiar estos ajustes, así como el parámetro **Valor de inicialización aleatorio** , para cambiar la división entre datos de entrenamiento y de prueba. En este ejemplo, lo dejaremos tal cual.
    
    > [AZURE.TIP] La propiedad **Fracción de filas del primer conjunto de datos de salida** determina la cantidad de datos que salen a través del puerto de salida de la izquierda. Por ejemplo, si establece la proporción en 0,7, el 70 % de los datos sale por el puerto de la izquierda y el 30 % por el puerto de la derecha.  
    
3. Haga doble clic en el módulo [Split Data][split] (Dividir datos) y escriba el comentario "Datos de entrenamiento y pruebas divididos al 50 %". 

Podemos utilizar las salidas del módulo [Split Data][split] (Dividir datos) como deseemos, pero vamos a optar por utilizar la salida de la izquierda como datos de entrenamiento y la salida de la derecha como datos de pruebas.  

Como se menciona en el sitio web de UCI, el costo de clasificar erróneamente un riesgo de crédito alto como bajo es cinco veces más alto que el costo de clasificar erróneamente un riesgo de crédito bajo como alto. Para tener esto en cuenta, se debe generar un nuevo conjunto de datos que refleje esta función de costo. En el nuevo conjunto de datos, cada ejemplo de alto riesgo se replica cinco veces, mientras que el ejemplo de bajo riesgo no se replica.   

Podemos conseguir esta replicación mediante el código R:  

1.  Busque y arrastre el módulo [Execute R Script][execute-r-script] (Ejecutar script R) al lienzo de experimentos y conecte el puerto de salida izquierdo del módulo [Split Data][split] (Dividir datos) al primer puerto de entrada (Dataset1) del módulo [Execute R Script][execute-r-script] (Ejecutar script R).
2. Haga doble clic en el módulo [Execute R script][execute-r-script] y escriba el comentario "Establecer ajuste de costos".
2.  En el panel **Propiedades**, elimine el texto predeterminado del parámetro **Script R** y escriba este script:

          dataset1 <- maml.mapInputPort(1)
          data.set<-dataset1[dataset1[,21]==1,]
          pos<-dataset1[dataset1[,21]==2,]
          for (i in 1:5) data.set<-rbind(data.set,pos)
          maml.mapOutputPort("data.set")


Tenemos que hacer esta misma operación de replicación en cada salida del módulo [Split Data][split] (Dividir datos) para que los datos de entrenamiento y prueba tengan el mismo ajuste con relación al costo.

1.  Haga clic con el botón derecho en el módulo [Execute R Script][execute-r-script] (Ejecutar script R) y seleccione **Copiar**.
2.  Haga clic con el botón secundario en el lienzo del experimento y seleccione **Pegar**.
3.  Conecte el primer puerto de entrada del módulo [Execute R Script][execute-r-script] (Ejecutar script R) al puerto de salida derecho del módulo [Split Data][split] (Dividir datos). 
4.  En la parte inferior del lienzo, haga clic en **Ejecutar**. 

> [AZURE.TIP] La copia del módulo Ejecutar script R contiene el mismo script que el módulo original. Al copiar y pegar un módulo en el lienzo, la copia retiene todas las propiedades del original.  

Nuestro experimento tiene ahora un aspecto similar al siguiente:

![Adding Split module and R scripts][3]

Para obtener más información sobre cómo usar los scripts de R en sus experimentos, consulte [Extender el experimento con R](machine-learning-extend-your-experiment-with-r.md).

**A continuación: [Entrenamiento y evaluación de los modelos](machine-learning-walkthrough-4-train-and-evaluate-models.md)**


[1]: ./media/machine-learning-walkthrough-3-create-new-experiment/create1.png
[2]: ./media/machine-learning-walkthrough-3-create-new-experiment/create2.png
[3]: ./media/machine-learning-walkthrough-3-create-new-experiment/create3.png
[4]: ./media/machine-learning-walkthrough-3-create-new-experiment/columnselector.png


<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[edit-metadata]: https://msdn.microsoft.com/library/azure/370b6676-c11c-486f-bf73-35349f842a66/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/



<!--HONumber=Oct16_HO2-->


