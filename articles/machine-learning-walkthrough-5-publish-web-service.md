<properties title="Step 5: Publish the Azure Machine Learning web service" pageTitle="Paso 5: Publicación del servicio web de Aprendizaje automático | Azure" description="Step 5: Publish a scoring experiment in Azure Machine Learning Studio as an ML API web service" metaKeywords="" services="machine-learning" solutions="" documentationCenter="" authors="garye" manager="paulettm" editor="cgronlun" videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/06/2014" ms.author="garye" />


Este es el quinto paso del tutorial [Desarrollo de una solución predictiva con el Aprendizaje automático de Azure][develop]:

[develop]: ../machine-learning-walkthrough-develop-predictive-solution/


1.	[Creación de un área de trabajo de Aprendizaje automático][create-workspace]
2.	[Carga de los datos existentes][upload-data]
3.	[Creación de un nuevo experimento][create-new]
4.	[Entrenamiento y evaluación de los modelos][train-models]
5.	**Publicación del servicio web**
6.	[Acceso al servicio web][access-ws]

[create-workspace]: ../machine-learning-walkthrough-1-create-ml-workspace/
[upload-data]: ../machine-learning-walkthrough-2-upload-data/
[create-new]: ../machine-learning-walkthrough-3-create-new-experiment/
[train-models]: ../machine-learning-walkthrough-4-train-and-evaluate-models/
[publish]: ../machine-learning-walkthrough-5-publish-web-service/
[access-ws]: ../machine-learning-walkthrough-6-access-web-service/

----------

# Paso 5: Publicación del servicio web de Aprendizaje automático de Azure

Para que este modelo predictivo pueda resultar útil a otros usuarios, lo publicaremos como servicio web en Azure. Un usuario podrá enviar al servicio un conjunto de datos de una aplicación de crédito y el servicio devolverá la predicción del riesgo de crédito.  

Para ello, necesitamos lo siguiente:  

-	Preparar el experimento para que esté listo para su publicación
-	Publicarlo en un servidor de ensayo donde podamos probarlo
-	Promoverlo al servidor activo de acceso público cuando pensemos que está listo  

Antes de empezar, debería crear una copia de este experimento para su edición. De este modo, podrá volver a su experimento de entrenamiento siempre que necesite realizar alguna acción adicional sobre sus modelos.  

1.	Haga clic en **Guardar como** debajo del lienzo.
2.	Ponga un nombre que resulte útil a la copia del experimento. De manera predeterminada, se adjunta "- Copia" al nombre del experimento original. En este caso, vamos a ponerle el nombre "Predicción de riesgo de crédito: experimento de puntuación".
3.	Haga clic en **Aceptar**.  


Ahora podrá ver el experimento original y su copia en la lista EXPERIMENTOS de Estudio de aprendizaje automático.  

![Experiments list][1]
 
##Preparación del experimento de puntuación
Es necesario realizar dos acciones para que nuestro modelo esté listo para su publicación como servicio web.  

En primer lugar, hay que convertir el experimento de *experimento de entrenamiento* a *experimento de puntuación*. Hasta ahora hemos estado experimentando con el entrenamiento de nuestro modelo. Pero el servicio publicado ya no hará ningún tipo de entrenamiento, sino que puntuará las entradas del usuario. Por tanto, vamos a guardar una copia del modelo que hemos entrenado y, a continuación, eliminaremos todos los componentes de nuestro experimento dedicados al entrenamiento.  

En segundo lugar, el servicio web de Aprendizaje automático de Azure aceptará las entradas del usuario y devolverá un resultado; así pues, es necesario identificar esos puntos de entrada y salida en nuestro experimento.  

###Conversión de un experimento de entrenamiento a experimento de puntuación
Pongamos que hayamos decidido que el modelo de árbol ampliado es el mejor en este caso. De ser así, lo primero que tenemos que hacer es quitar los módulos de entrenamiento de SVM.  

1.	Elimine la **Máquina de vectores de soporte de dos clases**
2.	Elimine los módulos **Entrenar modelo** y **Puntuar modelo** conectados a ella.
3.	Elimine el módulo **Transformar datos mediante escalado**.  

Ahora guardaremos el modelo de árbol ampliado que hemos entrenado. A continuación, podemos quitar los módulos restantes en el experimento que utilizamos para el entrenamiento y sustituirlos por el modelo entrenado.  

1.	Haga clic con el botón secundario en el puerto de salida del módulo **Entrenar modelo** y seleccione **Guardar como modelo entrenado**.
2.	Escriba un nombre y una descripción para el modelo entrenado. En este ejemplo, lo llamaremos "Predicción de riesgo de crédito".

	>**Nota**: una vez que guardemos este modelo entrenado, aparecerá en la paleta de módulos y estará disponible para su uso en otros experimentos.

3.	Busque este modelo en la paleta de módulos escribiendo "riesgo de crédito" en el cuadro **Buscar** y, a continuación, arrastre el modelo entrenado **Predicción de riesgo de crédito** al lienzo del experimento.
4.	Elimine los módulos **Árbol de decisión ampliado de dos clases** y **Entrenar modelo**.
5.	Conecte la salida del modelo **Predicción de riesgo de crédito** a la entrada izquierda del módulo **Puntuar modelo**.   

Ahora tenemos la versión guardada y entrenada del modelo en nuestro experimento en lugar de los módulos de entrenamiento originales.  

Hay más componentes en el experimento que hemos agregado solo para el entrenamiento y la evaluación de nuestros dos algoritmos de modelo. También podemos quitarlos:  

-	**Dividir**
-	Ambos módulos **Ejecutar script R**
-	**Evaluar modelo**  

Una cosa más: los datos de la tarjeta de crédito original incluidos en la columna del riesgo de crédito. Hemos pasado esta columna a través del módulo **Entrenar modelo** para que pudiera entrenar el modelo para predecir estos valores. Pero ahora que el modelo se ha entrenado, no deseamos seguir pasando esa columna por él; el modelo entrenado predirá ese valor en nuestro lugar. Para quitar esa columna del flujo de datos, utilizamos el módulo **Columnas de proyecto**.  

1.	Busque el módulo **Columnas de proyecto** y colóquelo en el lienzo.
2.	Conecte este módulo a la salida del módulo **Editor de metadatos** module (ahora que se han quitado los módulos **Dividir** y **Ejecutar script R**).
3.	Seleccione el módulo **Columnas de proyecto** y haga clic en **Iniciar el selector de columnas**.
4.	Deje "All columns" en el desplegable.
5.	Haga clic en el signo más (+) para crear una nuevo fila en el desplegable.
6.	En este nuevo desplegable, seleccione "Exclude column names" y escriba "Credit risk" en el campo de texto (también puede especificar la columna por su número de columna, 21).
7.	Haga clic en **Aceptar**.

	>Sugerencia: el selector de columnas sigue la lógica de los desplegables en la secuencia en la que aparecen. En este caso, hemos dirigido el módulo **Columnas de proyecto** para "pasar por todas las columnas excepto la columna Credit risk". Si hubiéramos dejado el primer desplegable, el módulo no hubiera pasado por ninguna de las columnas.

8.	Conecte la salida del módulo **Columnas de proyecto** a la entrada derecha del módulo **Puntuar modelo**.  

Nuestro experimento debería tener ahora un aspecto similar al siguiente:  

![Scoring the trained model][2]  

###Selección de la entrada y la salida del servicio
En el modelo original, los datos para puntuar pasaron por el puerto de entrada derecho ("Conjunto de datos") del módulo **Puntuar modelo**, y el resultado puntuado apareció en el puerto de salida ("Conjunto de datos puntuado"). Cuando el servicio está en ejecución, deseamos que los datos del usuario y los resultados utilicen estos mismos puertos.  

1.	Haga clic con el botón secundario en el puerto de salida del módulo **Puntuar modelo** y seleccione **Establecer como entrada de publicación**. Los datos de usuario tendrán que incluir todos los datos del vector de características.

	>**Sugerencia**: si tiene que manipular de alguna manera los datos del usuario antes de pasarlos al módulo de puntuación (utilizándolo de manera similar que el módulo **Transformar datos mediante escalado** para preparar los datos para el modelo SVM), simplemente deje el módulo en el servicio web y configure la entrada del servicio en el puerto de entrada de dicho módulo.

2.	Haga clic con el botón secundario en el puerto de salida y seleccione **Establecer como salida de publicación**. El servicio devolverá la salida del módulo Puntuar modelo. Esto incluye el vector de características, además de la predicción del riesgo de crédito y el valor de probabilidad de puntuación.

	>**Sugerencia**: si desea que el servicio web devuelva solo parte de estos datos (por ejemplo, si desea que devuelva todo el vector de características), puede agregar un módulo **Columnas de proyecto** después del módulo **Puntuar modelo**, configurarlo para excluir las columnas que no quiera y, a continuación, establecer la salida del módulo **Columnas de proyecto** como salida del servicio web.  
  

>**Nota:** se estará preguntando por qué hemos dejado el conjunto de datos "Datos de tarjeta de crédito alemana de UCI" y sus módulos asociados conectados al módulo **Puntuar modelo**. El servicio va a utilizar los datos del usuario, no el conjunto de datos original; entonces, ¿por qué los dejamos conectados?

Es cierto que el servicio no necesita los datos originales de la tarjeta de crédito. Pero sí necesita el esquema para esos datos, que incluye información como la cantidad de columnas que hay y cuáles son numéricas. Esta información del esquema es necesaria a fin de interpretar los datos del usuario. Dejamos estos componentes conectados para que el módulo de puntuación tenga el esquema del conjunto de datos cuando el servicio se esté ejecutando. No se utilizan los datos, sino solamente el esquema.  

Ejecute el experimento por última vez (haga clic en **EJECUTAR**). Si desea comprobar que el modelo sigue funcionando, haga clic con el botón secundario en la salida del módulo **Puntuar modelo** y seleccione **Visualizar**. Verá que aparecen los datos originales, junto con el valor de riesgo de crédito ("Etiquetas puntuadas") y el valor de probabilidad de la puntuación ("Probabilidades puntuadas").  

##Publicación del servicio web
Para publicar un servicio web derivado de nuestro experimento, haga clic en **PUBLICAR SERVICIO WEB** bajo el lienzo y haga clic en **SÍ** cuando se le solicite. Estudio de aprendizaje automático publica el experimento como servicio web en el servidor de ensayo de aprendizaje automático y le dirige al panel de servicios.   

>**Sugerencia**: puede actualizar el servicio web después de haberlo publicado. Por ejemplo, si desea cambiar el modelo, simplemente modifique el experimento de entrenamiento guardado previamente, cambie los parámetros del modelo y guarde el modelo entrenado (sobrescribiendo el que guardó anteriormente). Cuando abra nuevamente el experimento de puntuación, verá un aviso que le indica que ha cambiado algo (que será su modelo entrenado) y podrá actualizar el experimento. Cuando publique el experimento de nuevo, sustituirá al servicio web, utilizando ahora el modelo actualizado.  

Puede configurar el servicio haciendo clic en la pestaña **CONFIGURACIÓN**. Aquí puede modificar el nombre del servicio (recibe de manera predeterminada el nombre del experimento) y proporcionarle una descripción. También puede poner etiquetas más descriptivas para las columnas de entrada y salida.  

Trataremos el cambio **¿LISTO PARA PRODUCCIÓN?** un poco más adelante.  

##Prueba del servicio web
En la página **PANEL**, haga clic en el vínculo **Probar** en **Servicios de ensayo**. Aparecerá un cuadro de diálogo que le pide los datos de entrada del servicio. Se trata de las mismas columnas que aparecieron en el conjunto de datos original de riesgo de crédito alemán.  

Escriba un conjunto de datos y, a continuación, haga clic en **Aceptar**.  

Los resultados generados por el servicio web se muestran en la parte inferior del panel. De este modo tendremos el servicio configurado; los resultados que vea están generados por el módulo de puntuación.   

##Promoción del servicio web al servidor activo de acceso público
Hasta este momento el servicio se ha estado ejecutando en el servidor de ensayo de aprendizaje automático. Cuando lo tenga listo, puede solicitar su promoción al servidor activo de acceso público.  

En la pestaña **CONFIGURACIÓN**, haga clic en "SÍ" junto a **¿LISTO PARA PRODUCCIÓN?** Esto envía una aviso a su administrador de TI que le notifica que el servicio web está listo para su promoción a acceso público. En ese momento el administrador podrá promoverlo al servidor activo de acceso público.

![Promoting the service to the live environment][3]  

----------

**A continuación: [Acceso al servicio web][access-ws]**

[1]: ./media/machine-learning-walkthrough-5-publish-web-service/publish1.png
[2]: ./media/machine-learning-walkthrough-5-publish-web-service/publish2.png
[3]: ./media/machine-learning-walkthrough-5-publish-web-service/publish3.png
