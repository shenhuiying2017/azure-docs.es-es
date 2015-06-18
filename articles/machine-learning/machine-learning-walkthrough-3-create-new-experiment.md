<properties 
	pageTitle="Paso 3: Creación de un nuevo experimento de Aprendizaje automático | Azure" 
	description="Paso 3 del tutorial de la solución: crear un nuevo experimento de aprendizaje en Estudio de aprendizaje automático de Azure" 
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
	ms.date="01/29/2015" 
	ms.author="garye"/>


	Este es el tercer paso del tutorial [Desarrollo de una solución predictiva con Aprendizaje automático de Azure][develop]:

[develop]: ../machine-learning-walkthrough-develop-predictive-solution/


1.	[Creación de un área de trabajo de Aprendizaje automático][create-workspace]
2.	[Carga de los datos existentes][upload-data]
3.	**Creación de un nuevo experimento**
4.	[Entrenamiento y evaluación de los modelos][train-models]
5.	[Publicación del servicio web][publish]
6.	[Acceso al servicio web][access-ws]

[create-workspace]: ../machine-learning-walkthrough-1-create-ml-workspace/
[upload-data]: ../machine-learning-walkthrough-2-upload-data/
[create-new]: ../machine-learning-walkthrough-3-create-new-experiment/
[train-models]: ../machine-learning-walkthrough-4-train-and-evaluate-models/
[publish]: ../machine-learning-walkthrough-5-publish-web-service/
[access-ws]: ../machine-learning-walkthrough-6-access-web-service/

----------

# Paso 3: Creación de un nuevo experimento de Aprendizaje automático de Azure

Tenemos que crear un nuevo experimento en Estudio de aprendizaje automático que utilice el conjunto de datos que acabamos de cargar.  

1.	En Estudio de aprendizaje automático, haga clic en **+NUEVO** en la parte inferior de la ventana.
2.	Seleccione **EXPERIMENTO**.
3.	En la paleta de módulos, a la izquierda del lienzo del experimento, expanda **Conjuntos de datos guardados**.
4.	Busque el conjunto de datos que había creado y arrástrelo al lienzo. También puede buscar el conjunto de datos escribiendo su nombre en el cuadro **Buscar** que hay encima de la paleta.  

##Preparación de los datos
Para ver las primeras 100 filas de datos y alguna información estadística de todo el conjunto de datos, haga clic con el botón secundario en el puerto de salida del conjunto de datos y seleccione **Visualizar**. Observe que Estudio de aprendizaje automático ya ha identificado el tipo de datos de cada columna. También ha puesto encabezados genéricos a las columnas porque el archivo de datos no contenía encabezados de columna.  

Los encabezados de columna no son esenciales, pero facilitarán el trabajo con los datos en el modelo. Además, si finalmente se publicara este modelo en un servicio web, los encabezados ayudarían al usuario del servicio a identificar las columnas.  

Podemos agregar encabezados de columna con el módulo **Editor de metadatos**.  

1.	En la paleta de módulos, escriba "metadatos" en el cuadro **Buscar**. Verá **Editor de metadatos** en la lista de módulos.
2.	Haga clic en el **Editor de metadatos**, arrástrelo al lienzo y colóquelo bajo el conjunto de datos.
3.	Conecte el conjunto de datos al **Editor de metadatos**: haga clic en el puerto de salida del conjunto de datos, arrastre el puerto de entrada del **Editor de metadatos** y, a continuación, suelte el botón del mouse. El conjunto de datos y el módulo permanecerán conectados incluso si se desplaza por el lienzo.
4.	En el panel **Propiedades** situado a la derecha del lienzo, haga clic en **Iniciar el selector de columnas**.
5.	Compruebe que está seleccionada la opción "Todas las columnas" en el campo **Empieza por**, quite las filas y haga clic en **Aceptar**. Esto hace que el **Editor de metadatos** opere sobre todas las columnas de datos.
6.	Para el parámetro **Nuevo nombre de columna** escriba la lista de nombres de las 21 columnas del conjunto de datos, separadas por comas y en el orden de las columnas. Puede obtener los nombres de las columnas en la documentación del conjunto de datos en el sitio web de UCI o, para mayor comodidad, puede copiar y pegar lo siguiente:  

	Status of checking account, Duration in months, Credit history, Purpose, Credit amount, Savings account/bond, Present employment since, Installment rate in percentage of disposable income, Personal status and sex, Other debtors, Present residence since, Property, Age in years, Other installment plans, Housing, Number of existing credits, Job, Number of people providing maintenance for, Telephone, Foreign worker, Credit risk  

El panel Propiedades tendrá un aspecto similar al siguiente:

![Properties for Metadata Editor][1] 

>Sugerencia: Si desea comprobar los encabezados de columna, ejecute el experimento (haga clic en **EJECUTAR** debajo del lienzo del experimento), haga clic con el botón secundario en el puerto de salida del módulo **Editor de metadatos** y seleccione **Visualizar**. Puede ver el resultado de cualquier módulo igual que visualiza el progreso de los datos a lo largo del experimento.

El experimento debería tener ahora un aspecto similar al siguiente:  

![Adding Metadata Editor][2]
 
##Creación de conjuntos de datos de entrenamiento y prueba
El siguiente paso del experimento es generar conjuntos de datos independientes que se utilizarán para entrenar y probar nuestro modelo. Para ello, utilizamos el módulo **Dividir**.  

1.	Busque el módulo **Dividir**, arrástrelo al lienzo y conéctelo al último módulo **Editor de metadatos**.
2.	De manera predeterminada, la proporción de división es de 0,5 y se establece el parámetro **División aleatoria**. Esto significa que una mitad aleatoria de los datos saldrá a través de un puerto del módulo **Dividir** y la otra mitad por el otro. Puede cambiar estos ajustes, así como el parámetro **Valor de inicialización aleatorio**, para cambiar la división entre datos de entrenamiento y de puntuación. Para este ejemplo, lo dejaremos como está.
	>Sugerencia: La proporción de división determina fundamentalmente qué cantidad de datos sale a través del puerto de salida de la izquierda. Por ejemplo, si establece la proporción en 0,7, el 70 % de los datos sale por el puerto de la izquierda y el 30 % por el puerto de la derecha.  
	
Podemos usar las salidas del módulo **Dividir** como deseemos, pero vamos a optar por utilizar la salida de la izquierda como datos de entrenamiento y la salida de la derecha como datos de puntuación.  

Como se menciona en el sitio web de UCI, el coste de clasificar erróneamente un riesgo de crédito alto como bajo es 5 veces más alto que el coste de clasificar erróneamente un riesgo de crédito bajo como alto. Para tener esto en cuenta, vamos a generar un nuevo conjunto de datos que refleje esta función de coste. En el nuevo conjunto de datos, cada ejemplo de alto riesgo se replica 5 veces, mientras que los ejemplos de bajo riesgo no se replican.   

Podemos conseguir esta replicación mediante el código R:  

1.	Busque y arrastre el módulo **Ejecutar script R** al lienzo de experimentos y conéctelo al puerto de salida izquierdo del módulo **Dividir**.
2.	En el panel **Propiedades**, elimine el texto predeterminado del parámetro **Script** y escriba este script: 

		dataset1 <- maml.mapInputPort(1)
		data.set<-dataset1[dataset1[,21]==1,]
		pos<-dataset1[dataset1[,21]==2,]
		for (i in 1:5) data.set<-rbind(data.set,pos)
		maml.mapOutputPort("data.set")


Tenemos que hacer esta misma operación de replicación para cada salida del módulo **Dividir** con el fin de que los datos de entrenamiento y puntuación tengan el mismo ajuste en relación al coste.

1.	Haga clic con el botón secundario en el módulo **Ejecutar script R** y seleccione **Copiar**.
2.	Haga clic con el botón secundario en el lienzo del experimento y seleccione **Pegar**.
3.	Conecte este módulo **Ejecutar script R** al puerto de salida derecho del módulo **Dividir**.  

>Sugerencia: La copia del módulo Ejecutar script R contiene el mismo script que el módulo original. Al copiar y pegar un módulo en el lienzo, la copia retiene todas las propiedades del original.  
>
Nuestro experimento tiene ahora un aspecto similar al siguiente:
 
![Adding Split module and R scripts][3]

**A continuación: [Entrenamiento y evaluación de los modelos][train-models]**


[1]: ./media/machine-learning-walkthrough-3-create-new-experiment/create1.png
[2]: ./media/machine-learning-walkthrough-3-create-new-experiment/create2.png
[3]: ./media/machine-learning-walkthrough-3-create-new-experiment/create3.png

<!--HONumber=46--> 

<!--HONumber=46--> 
 