<properties
	pageTitle="Paso 3: Creación de un nuevo experimento de Aprendizaje automático | Microsoft Azure"
	description="Paso 3 del tutorial Desarrollo de una solución predictiva: crear un nuevo experimento de aprendizaje en Estudio de aprendizaje automático de Azure."
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
	ms.date="09/08/2015" 
	ms.author="garye"/>


# Paso 3 del tutorial: Creación de un nuevo experimento de Aprendizaje automático de Azure

Este es el tercer paso del tutorial [Desarrollo de una solución predictiva con Aprendizaje automático de Azure](machine-learning-walkthrough-develop-predictive-solution.md)


1.	[Creación de un área de trabajo de Aprendizaje automático](machine-learning-walkthrough-1-create-ml-workspace.md)
2.	[Carga de los datos existentes](machine-learning-walkthrough-2-upload-data.md)
3.	**Crear un experimento nuevo**
4.	[Entrenamiento y evaluación de los modelos](machine-learning-walkthrough-4-train-and-evaluate-models.md)
5.	[Implementación del servicio web](machine-learning-walkthrough-5-publish-web-service.md)
6.	[Acceso al servicio web](machine-learning-walkthrough-6-access-web-service.md)

----------

Tenemos que crear un nuevo experimento en Estudio de aprendizaje automático que utilice el conjunto de datos que acabamos de cargar.

1.	En Estudio de aprendizaje automático, haga clic en **+NUEVO** en la parte inferior de la ventana.
2.	Seleccione **EXPERIMENTO** y luego "Experimento en blanco". Seleccione el nombre del experimento predeterminado en la parte superior del lienzo y cámbielo por uno significativo

	> [AZURE.TIP]Es una buena práctica para rellenar el **Resumen** y la **Descripción** para el experimento en el panel **Propiedades**. Estas propiedades ofrecen la oportunidad para documentar el experimento para que cualquier persona que lo vea posteriormente entienda sus objetivos y la metodología.

3.	En la paleta de módulos, a la izquierda del lienzo de experimentos, expanda **Conjuntos de datos guardados**.
4.	Busque el conjunto de datos que ha creado en **Mis conjuntos de datos** y arrástrelo al lienzo. También puede buscar el conjunto de datos escribiendo su nombre en el cuadro **Buscar** que está encima de la paleta.  

##Preparación de los datos
Para ver las primeras 100 filas de datos y alguna información estadística de todo el conjunto de datos, haga clic en el puerto de salida del conjunto de datos y seleccione **Ver resultados**. Observe que Estudio de aprendizaje automático ya ha identificado el tipo de datos de cada columna. También ha puesto encabezados genéricos a las columnas porque el archivo de datos no contenía encabezados de columna.

Los encabezados de columna no son esenciales, pero facilitarán el trabajo con los datos en el modelo. Además, si finalmente se publicara este modelo en un servicio web, los encabezados ayudarían al usuario del servicio a identificar las columnas.

Podemos agregar encabezados de columna mediante el módulo [Editor de metadatos][metadata-editor]. El módulo [Editor de metadatos][metadata-editor] se utiliza para cambiar los metadatos asociados a un conjunto de datos. En este caso, puede proporcionar nombres más descriptivos para los encabezados de columna. Para ello, indicaremos al [Editor de metadatos][metadata-editor] que actúe en todas las columnas y después proporcione una lista de nombres que se agregarán a las columnas.

1.	En la paleta de módulos, escriba "metadatos" en el cuadro **Buscar**. Verá [Editor de metadatos][metadata-editor] en la lista de módulos.
2.	Haga clic en el módulo [Editor de metadatos][metadata-editor], arrástrelo al lienzo y colóquelo bajo el conjunto de datos.
3.	Conecte el conjunto de datos al [Editor de metadatos][metadata-editor]: haga clic en el puerto de salida del conjunto de datos, arrastre el puerto de entrada del [Editor de metadatos][metadata-editor] y, a continuación, suelte el botón del mouse. El conjunto de datos y el módulo permanecerán conectados incluso si se desplaza por el lienzo.
4.	Con el [Editor de metadatos][metadata-editor] aún seleccionado, en el panel **Propiedades** situado a la derecha del lienzo, haga clic en **Iniciar el selector de columnas**.
5.	En el cuadro de diálogo **Seleccionar columnas**, establezca el campo **Empieza por** en "Todas las columnas".
6.	La fila bajo **Empieza por** permite incluir o excluir columnas específicas para que el [Editor de metadatos][metadata-editor] las modifique. Puesto que deseamos modificar todas las columnas, elimine esta fila haciendo clic en el signo menos ("-") a la derecha de la fila. El cuadro de diálogo debe ser similar al siguiente:  
    ![Selector de columnas con todas las columnas seleccionadas][4]
7.	Haga clic en la marca de verificación **Aceptar**. 
8.	En el panel **propiedades**, busque el parámetro **Nuevo nombre de columna**. En este campo, escriba la lista de nombres de las 21 columnas del conjunto de datos, separadas por comas y en el orden de las columnas. Puede obtener los nombres de las columnas en la documentación del conjunto de datos en el sitio web de UCI o, para mayor comodidad, puede copiar y pegar lo siguiente:  

<!-- try the same thing without upper-case
		Status of checking account, Duration in months, Credit history, Purpose, Credit amount, Savings account/bond, Present employment since, Installment rate in percentage of disposable income, Personal status and sex, Other debtors, Present residence since, Property, Age in years, Other installment plans, Housing, Number of existing credits, Job, Number of people providing maintenance for, Telephone, Foreign worker, Credit risk  
-->

	status of checking account, duration in months, credit history, purpose, credit amount, savings account/bond, present employment since, installment rate in percentage of disposable income, personal status and sex, other debtors, present residence since, property, age in years, other installment plans, housing, number of existing credits, job, number of people providing maintenance for, telephone, foreign worker, credit risk  

El panel Propiedades tendrá un aspecto similar al siguiente:

![Properties for Metadata Editor][1]

> [AZURE.TIP]Si desea comprobar los encabezados de columna, ejecute el experimento (haga clic en **EJECUTAR** debajo del lienzo del experimento), haga clic en el puerto de salida del módulo [Editor de metadatos][metadata-editor] y seleccione **Ver resultados**. Puede ver el resultado de cualquier módulo igual que visualiza el progreso de los datos a lo largo del experimento.

El experimento debería tener ahora un aspecto similar al siguiente:

![Adding Metadata Editor][2]

##Creación de conjuntos de datos de entrenamiento y prueba
El siguiente paso del experimento es generar conjuntos de datos independientes que se utilizarán para entrenar y probar nuestro modelo. Para ello, utilizamos el módulo [Dividir][split].

1.	Busque el módulo [Dividir][split], arrástrelo al lienzo y conéctelo al último módulo [Editor de metadatos][metadata-editor].
2.	De manera predeterminada, la proporción de división es 0,5 y se establece el parámetro **División aleatoria**. Esto significa que una mitad aleatoria de los datos saldrá a través de un puerto del módulo [Dividir][split] y la otra mitad por el otro. Puede cambiar estos ajustes, así como el parámetro **Valor de inicialización aleatorio**, para cambiar la división entre datos de entrenamiento y de puntuación. Para este ejemplo, lo dejaremos como está.
	> [AZURE.TIP]Sugerencia: la proporción de división determina fundamentalmente qué cantidad de datos sale a través del puerto de salida de la izquierda. Por ejemplo, si establece la proporción en 0,7, el 70 % de los datos sale por el puerto de la izquierda y el 30 % por el puerto de la derecha.  

Podemos utilizar las salidas del módulo [Dividir][split] como deseemos, pero vamos a optar por utilizar la salida de la izquierda como datos de entrenamiento y la salida de la derecha como datos de puntuación.

Como se menciona en el sitio web de UCI, el coste de clasificar erróneamente un riesgo de crédito alto como bajo es 5 veces más alto que el coste de clasificar erróneamente un riesgo de crédito bajo como alto. Para tener esto en cuenta, vamos a generar un nuevo conjunto de datos que refleje esta función de coste. En el nuevo conjunto de datos, cada ejemplo de alto riesgo se replica 5 veces, mientras que los ejemplos de bajo riesgo no se replican.

Podemos conseguir esta replicación mediante el código R:

1.	Busque y arrastre el módulo [Ejecutar script R][execute-r-script] al lienzo de experimentos y conecte el puerto de salida izquierdo del módulo [Dividir][split] al primer puerto de entrada ("Dataset1") del módulo [Ejecutar script R][execute-r-script].
2.	En el panel **Propiedades**, elimine el texto predeterminado del parámetro **Script R** y escriba este script:

		dataset1 <- maml.mapInputPort(1)
		data.set<-dataset1[dataset1[,21]==1,]
		pos<-dataset1[dataset1[,21]==2,]
		for (i in 1:5) data.set<-rbind(data.set,pos)
		maml.mapOutputPort("data.set")


Tenemos que hacer esta misma operación de replicación para cada salida del módulo [Dividir][split] para que los datos de entrenamiento y puntuación tengan el mismo ajuste en relación al coste.

1.	Haga clic en el módulo [Ejecutar script R][execute-r-script] y seleccione **Copiar**.
2.	Haga clic con el botón secundario en el lienzo del experimento y seleccione **Pegar**.
3.	Conecte el primer puerto de entrada de este módulo [Ejecutar script R][execute-r-script] al puerto de salida derecho del módulo [Dividir][split].  

> [AZURE.TIP]La copia del módulo Ejecutar script R contiene el mismo script que el módulo original. Al copiar y pegar un módulo en el lienzo, la copia retiene todas las propiedades del original.
>
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
[metadata-editor]: https://msdn.microsoft.com/library/azure/370b6676-c11c-486f-bf73-35349f842a66/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/

<!---HONumber=Oct15_HO3-->