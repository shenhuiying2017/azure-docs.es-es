<properties 
	pageTitle="Convertir un experimento de formación en Aprendizaje automático en un experimento puntuación | Azure" 
	description="Cómo convertir un experimento de formación en Aprendizaje automático, usado para formar el modelo de análisis predictivo, en un experimento de puntuación que se pueden publicar como un servicio web." 
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
	ms.date="02/10/2015" 
	ms.author="garye"/>

#Convertir un experimento de formación en Aprendizaje automático en un experimento puntuación

Aprendizaje automático de Azure permite compilar, probar e implementar soluciones de análisis predictivo. 

Una vez que haya creado e iterado en un *training experiment* para entrenar el modelo de análisis predictivo y esté listo para usarlo para puntuar nuevos datos, deberá preparar y simplificar el experimento para puntuar. A continuación, puede publicar este *scoring experiment* como servicio web de Azure para que los usuarios puedan enviar datos a su modelo y recibir las predicciones de su modelo.

Al efectuar la conversión a un experimento de puntuación, estará preparando el modelo formado para publicarlo como servicio web de puntuación. Los usuarios del servicio web enviarán datos de entrada a su modelo y el modelo devolverá los resultados de predicción. Por lo tanto, cuando efectúe la conversión a un experimento de puntuación, deberá tener en cuenta cómo espera que utilicen el modelo los demás.

El proceso de convertir un experimento de formación en un experimento puntuación implica tres pasos:

1.	Guardar el modelo de aprendizaje automático que ha formado y, a continuación, sustituir el algoritmo de aprendizaje automático y los módulos del **modelo de formación** con su modelo entrenado guardado.
2.	Recortar el experimento solo a los módulos que se necesitan para efectuar la puntuación. Un experimento de formación incluye una serie de módulos que son necesarios para la formación, pero que no son necesarios una vez que el modelo está formado y listo para usar para la puntuación.
3.	Defina en qué parte del aceptará datos del usuario del servicio web y qué datos se devolverán.

##Botón Crear experimento de puntuación

El botón **Crear experimento de puntuación** que se encuentra situado en la parte inferior del lienzo del experimento realizará automáticamente los tres pasos de convertir el experimento de formación en un experimento de puntuación:

1.	Guarda el modelo formado como módulo en la sección **Modelos entrenados** de la paleta del módulo (a la izquierda del lienzo del experimento), a continuación, reemplaza el algoritmo de aprendizaje automático y los módulos **Formar modelo** con el modelo formado guardado. 
2.	Elimina los módulos que claramente no se necesitan. En nuestro ejemplo, esto incluye los módulos **División**, 2º **modelo de puntuación** y **Modelo de evaluación**.
3.	Crea módulos de y salida del servicio web y los agrega en ubicaciones predeterminadas en el experimento.

Por ejemplo, en el siguiente experimento se entrena un modelo de árbol de decisión aumentada de dos clases con los datos del censo de muestra:

![Training experiment][figure1]

Los módulos de este experimento realizan básicamente cuatro funciones diferentes:

![Module functions][figure2]

Al convertir este experimento de formación en un experimento de puntuación, algunos de estos módulos ya no se necesitan o tienen un objetivo diferente:

- **Datos**: los datos de este conjunto de datos de ejemplo no se usan durante la puntuación. El usuario del servicio web proporcionará los datos a puntuar. Sin embargo, los metadatos de este conjunto de datos, como los tipos de datos, son utilizados por el modelo formado. Por ello, necesita mantener el conjunto de datos en el experimento de puntuación para que pueda ofrecer estos metadatos.

- **Prep**: en función de los datos que se van a enviar para puntuar, es posible que estos módulos sean o no necesarios para procesar los datos entrantes. 

	Por ejemplo, en este ejemplo, el conjunto de datos de ejemplo puede tener valores que faltan e incluye columnas que no son necesarias para formar el modelo. Por lo tanto, se ha incluido un módulo de **Limpiar los datos que faltan** para tratar los valores ausentes y se ha incluido un módulo de **Columnas de proyecto** para excluir esas columnas adicionales del flujo de datos. Si sabe que los datos que se van a enviar para puntuar a través del servicio web no tendrán valores ausentes, a continuación, puede quitar el módulo **Limpiar los datos que faltan**. Sin embargo, dado que el módulo **Columnas de proyecto** ayuda a definir el conjunto de funciones que se puntúan, ese módulo debe permanecer.

- **Formar**: una vez que el modelo se ha formado correctamente, debe guardarlo como un módulo único de modelo formado. A continuación, reemplace estos módulos individuales por los del modelo formado guardado.

- **Puntuación**: en este ejemplo, el módulo de división se utiliza para dividir el flujo de datos en un conjunto de datos de prueba y datos de formación. En el experimento de puntuación esto no es necesario y puede quitarse. De forma similar, el segundo módulo de **Modelo de puntuación** y el módulo **Modelo de evaluación** se usan para comparar los resultados de los datos de prueba, por lo que estos módulos tampoco son necesarios en el experimento de puntuación. No obstante, el módulo de **Modelo de puntuación** restante, es necesario para devolver un resultado de puntuación a través del servicio web.

Este es el aspecto de nuestro ejemplo después de hacer clic en **Crear experimento puntuación**:	

![Converted scoring experiment][figure3]

Esto puede ser suficiente para preparar el experimento para publicarlo como servicio web. Sin embargo, es aconsejable realizar algún trabajo adicional específico en su experimento.

###Ajustar los módulos de entrada y salida

En el experimento de formación, utilizó un conjunto de datos de entrenamiento y, a continuación, realizó algún procesamiento para obtener los datos en un formato necesitado por el algoritmo de aprendizaje automático. Si los datos que espera recibir a través del servicio web no necesitan este procesamiento, puede mover el **Módulo de entrada del servicio web** a un nodo diferente del experimento.

Por ejemplo, de forma predeterminada **Crear experimento de puntuación** coloca el módulo de **Entrada de servicio web** en la parte superior de su flujo de datos, como se muestra en la ilustración anterior. Sin embargo, si los datos de entrada no necesitan este procesamiento, a continuación, puede colocar manualmente la **Entrada del servicio web** más allá de los módulos de procesamiento de datos:

![Moving the web service input][figure4]

Los datos de entrada que se proporciona a través del servicio web pasarán ahora directamente al módulo del modelo de puntuación sin ningún procesamiento previo.

De forma similar, de manera predeterminada **Crear experimento de puntuación** coloca el módulo de salida del servicio web en la parte inferior de su flujo de datos. En este ejemplo, el servicio web devolverá al usuario el resultado del módulo **Modelo de puntuación**, que incluye el vector de los datos de entrada completo, además de los resultados de las puntuaciones.

Sin embargo, si prefiere devolver algo diferente, por ejemplo, solo los resultados de puntuación y no el vector completo de datos de entrada, a continuación, puede insertar un módulo de **Columnas de proyecto** para excluir todas las columnas excepto los resultados de la puntuación. A continuación, mueva el módulo **Resultado del servicio web** a la salida del módulo **Columnas del proyecto**:

![Moving the web service output][figure5]

###Agregar o quitar módulos de procesamiento de datos adicionales

Si hay más módulos en el experimento de los que sabe que se necesitarán durante la puntuación, se pueden eliminar. Por ejemplo, debido a que hemos movido el módulo **Entrada del servicio web** a un punto después de los módulos de procesamiento de datos, podemos eliminar el módulo **Limpiar los datos que faltan** del experimento de puntuación. 

Nuestro experimento de puntuación ahora tiene el siguiente aspecto:

![Removing additional module][figure6]

###Agregar parámetros de servicio web opcionales

En algunos casos, puede permitir al usuario del servicio web cambiar el comportamiento de los módulos al acceder al servicio. *Web Service Parameters* le permite hacer esto.

Un ejemplo común es la configuración del módulo **Lector** para que el usuario del servicio web publicado pueda especificar un origen de datos diferente al acceder al servicio web. También puede configurar el módulo **Escritor** para que se pueda especificar un destino diferente. 

Puede definir parámetros de servicio web y asociarlos con uno o más parámetros de módulo, y puede especificar si son obligatorios u opcionales. El usuario del servicio web puede proporcionar a continuación valores para estos parámetros cuando se tiene acceso al servicio y las acciones del módulo se modificarán en consecuencia.

Para obtener más información acerca de los parámetros del servicio web, consulte [Uso de los parámetros del servicio web de Aprendizaje automático de Azure
][webserviceparameters].

[webserviceparameters]: machine-learning-web-service-parameters.md


##Publicar el experimento de puntuación como un servicio web

Ahora que ha preparado el experimento de puntuación suficientemente, puede publicarlo como un servicio web de Azure. Mediante el servicio web, los usuarios pueden enviar datos a su modelo y el modelo devolverá las predicciones.

Para obtener más información sobre el proceso de publicación completo, consulte [Publicar un servicio web de Aprendizaje automático de Azure][publicar]

[publicar]: machine-learning-publish-a-machine-learning-web-service.md


<!-- Images -->
[figure1]:./media/machine-learning-convert-training-experiment-to-scoring-experiment/figure1.png
[figure2]:./media/machine-learning-convert-training-experiment-to-scoring-experiment/figure2.png
[figure3]:./media/machine-learning-convert-training-experiment-to-scoring-experiment/figure3.png
[figure4]:./media/machine-learning-convert-training-experiment-to-scoring-experiment/figure4.png
[figure5]:./media/machine-learning-convert-training-experiment-to-scoring-experiment/figure5.png
[figure6]:./media/machine-learning-convert-training-experiment-to-scoring-experiment/figure6.png


<!--HONumber=49-->