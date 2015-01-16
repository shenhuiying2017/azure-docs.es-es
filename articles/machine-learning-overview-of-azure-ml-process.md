<properties title="Azure Machine Learning API service operations" pageTitle="Operaciones de servicio de la API de Aprendizaje automático | Azure" description="Creación y administración de servicios web de aprendizaje automático de Azure" metaKeywords="" services="machine-learning" solutions="" documentationCenter="" authors="garye" manager="paulettm" editor="cgronlun" videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/06/2014" ms.author="garye" />


# Operaciones de servicio de la API de Aprendizaje automático de Azure 
Un proyecto de Aprendizaje automático de Microsoft Azure suele incluir los siguientes pasos de alto nivel:  

1.	Obtención, análisis y preparación de datos
2.	Creación de experimentos de aprendizaje automático aprovechando diversos algoritmos de aprendizaje automático
3.	Aprendizaje, prueba y generación de un modelo entrenado
4.	Creación de un flujo de trabajo operativo utilizando el modelo entrenado e implementación del flujo de trabajo en producción
5.	Supervisión del rendimiento del modelo y las posteriores actualizaciones  

>El término "experimento" se utiliza para describir un flujo de trabajo interactivo que puede incluir introducción y manipulación de datos, entrenadores y puntuadores en forma de un grafo acíclico dirigido (DAG). Una vez que se publique el flujo de trabajo como Servicio web de Azure, deja de estar interactivo, lo que significa que, para cambiarlo, es necesario actualizar el modelo y, a continuación, volver a publicarlo a fin de actualizar el Servicio web y su comportamiento.  

Los pasos de 1 a 3 suelen realizarse por un científico de datos a través de varias iteraciones al final de las cuales se entrega un modelo de aprendizaje automático a los equipos de ingeniería y operaciones para su integración en sistemas de producción (con el fin de que el modelo de aprendizaje automático se pueda utilizar en producción).  

Los procesos tradicionales de integración e implementación del modelo de aprendizaje automático en un sistema de producción puede tomar semanas, e incluso meses, en función del código utilizado para crear los modelos (como R, Python, C# o Java), las consideraciones sobre infraestructura e integración de plataforma y la planificación de la implementación.  

El aprendizaje automático de Azure simplifica este proceso convirtiendo en primer lugar la creación y la evaluación del modelo en una experiencia intuitiva y proporcionando a continuación un proceso sencillo para implementar el experimento como Servicio web en Azure, reduciendo así significativamente el tiempo total desde la experimentación del modelo hasta su ejecución en producción como Servicio web.  

Este documento describe los conceptos y los pasos necesarios para configurar un Servicio web de Aprendizaje automático de Azure desde un experimento de aprendizaje automático.  

# Información general sobre el proceso Aprendizaje automático de Azure  #
Aprendizaje automático de Azure permite la creación de Servicios web desde experimentos de aprendizaje automático definidos en el Estudio de aprendizaje automático de Microsoft Azure. Un Servicio web de Aprendizaje automático de Azure se puede utilizar para realizar predicciones basadas en datos de entrada reales en tiempo real o en modo por lotes.  
 
El diagrama siguiente muestra los pasos a un alto nivel en dos partes: primero la creación de un modelo y, a continuación, su publicación como Servicio web. Este documento se centra en el contenido a la derecha del diagrama de la ilustración 1 (Publicación de un Servicio web de puntuación) y explica los conceptos implicados en ese proceso. 

![][1]  

Ilustración 1: provisión, creación y publicación de un Servicio web de puntuación  

# Servicios web de Aprendizaje automático de Azure #
Un Servicio web, en el contexto del aprendizaje automático, es una interfaz de software que permite la comunicación entre aplicaciones externas y un flujo de trabajo de aprendizaje automático. Ofrece una forma de comunicarse con un modelo de puntuación en tiempo real para recibir los resultados de la predicción e incorporar dichos resultados en aplicaciones cliente externas. Aprendizaje automático de Azure aprovecha Microsoft Azure para la implementación, el hospedaje y la administración de Servicios web de Aprendizaje automático de Azure. Se pueden crear dos tipos de servicio mediante el Aprendizaje automático de Azure.  

## Servicio de solicitud-respuesta (RRS) ##
El servicio de solicitud-respuesta (RRS) es un servicio web de alta escala y baja latencia que se usa para proporcionar una interfaz para los modelos sin estado creados y publicados desde el entorno de experimentación.   

- API de REST: RRS es un Servicio web RESTFul. 
-	Interfaz de servicios: la interfaz de Servicio web de RRS se define como parte de la configuración del experimento que utiliza puertos de entrada/salida en el experimento de Estudio de aprendizaje automático de Azure.
-	Etapas de desarrollo: como parte del flujo de trabajo del Aprendizaje automático de Azure, el servicio RRS se genera primero en el entorno de ensayo (y ahí se puede probar). Cuando se considera que está completo y listo para la producción, se implementa en el entorno de producción. 
-	Implementado en Azure: el resultado de implementar RRS en el extremo de un Servicio web de Azure.
-	Parámetros de interfaz: la solicitud a un servicio RRS representa los datos que hay que puntuar utilizando el experimento definido en el Estudio. La respuesta es el resultado de la predicción del modelo. 
-	Valores de respuesta: RRS acepta una sola fila de parámetros de entrada y genera una sola fila como resultado. La fila de resultados puede contener varias columnas.   

## Servicio de ejecución de lotes (BES) ##
El servicio de ejecución de lotes (BES) es un servicio para la puntuación asincrónica de lotes de registros de datos. La entrada para BES es parecida a la entrada de datos usada en RRS; la diferencia principal radica en que BES lee un lote de registros de varios orígenes como blobs, tablas de Azure, SQL Azure, HD Insight (Hive Query) y fuentes HTTP.  Los resultados de la puntuación se transfieren a un archivo del almacenamiento de blobs de Azure y en respuesta se devuelven los datos del extremo de almacenamiento.  

BES ofrece también interfaces para obtener el estado de los procesos de puntuación en ejecución y la cancelación de la solicitud. BES puede ejecutar paquetes de modelos sobre cantidades de datos muy grandes.  

-	API de REST: BES es un Servicio web RESTFul.
-	Interfaz de servicios: igual que RRS, la interfaz de Servicio web de BES se define como parte de la configuración de un modelo que utiliza puertos de entrada/salida en el experimento de Estudio de aprendizaje automático de Azure.
-	Etapas de desarrollo: como parte del flujo de trabajo de creación de BES, se genera primero en el entorno de ensayo (y ahí se puede probar). Una vez que está completo y listo para la producción, se implementa en el entorno de producción. 
-	Implementado en Azure: el resultado de implementar BES en el extremo de un Servicio web de Azure.
-	Parámetros de interfaz: la solicitud a un servicio BES es la URL de un archivo en un blob de Azure o una entrada de SAS de los registros que se van a puntuar. La respuesta se escribe en un blob de Azure y se devuelve la URL del extremo de almacenamiento de respuesta.  

# Publicación de un Servicio web de Aprendizaje automático de Azure #
Estudio de aprendizaje automático de Azure ofrece una aplicación basada en explorador para crear y ejecutar con facilidad experimentos de aprendizaje automático en una interfaz gráfica de usuario mediante varios orígenes de datos, modelos de manipulación y validación de datos y algoritmos de aprendizaje automático. Un experimento en el Estudio de aprendizaje automático se construye como un grafo acíclico dirigido (DAG) de módulos de procesamiento de datos.  

Una vez configurado el experimento y ejecutado correctamente para entrenarse en los datos, se puede guardar como modelo entrenado y utilizarse para puntuación. El modelo entrenado se utiliza entonces en un experimento de puntuación o un flujo de trabajo y se publica como un Servicio web de Azure.  

## Experimento de entrenamiento ##
Un experimento puede incluir varios módulos para la carga y la manipulación de datos, la aplicación de algoritmos de aprendizaje automático y la evaluación de resultados. Entrenar modelo utiliza el conjunto de datos de entrenamiento y un algoritmo de aprendizaje para predecir una respuesta.  

Una vez que el modelo ha completado correctamente su ejecución, Entrenar modelo se puede guardar como componente reutilizable para puntuar conjuntos de datos y consultas de prueba.

![][2]  

Ilustración 2: ejemplo que muestra cómo guardar un modelo entrenado en el experimento

El modelo entrenado guardado estará entonces disponible en la sección Modelos entrenados de la aplicación.

![][3]  

Ilustración 3: sección Modelos entrenados en la que aparece la lista de modelos

## Experimento de puntuación ##
Un experimento de puntuación genera predicciones utilizando el modelo entrenado y los datos de muestra.  

La ilustración 1 anterior muestra el uso de Puntuar modelo en el experimento. En el Estudio, forma parte de los módulos de Aprendizaje automático.

![][4]  

Ilustración 4: puntuar modelo 

### Servicio de solicitud-respuesta (RRS) frente a Servicio de ejecución de lotes ###
Al crear experimentos de puntuación que se publicarán como Servicios web, es posible seleccionar cualquiera de las dos opciones (dependerá del escenario de puntuación). Si la solicitud de puntuación implica la puntuación de un solo registro, como una solicitud para determinar si el cliente A va a cambiar de operador (predicción de rotación de clientes), es posible puntuar en tiempo real y, por tanto, la opción sería un Servicio web RRS. El servicio devolverá el resultado del modelo de predicción en tiempo real (en el caso del ejemplo anterior sobre rotación de clientes, la respuesta podría ser "sí" o "no").  

En el caso de las operaciones de puntuación es la que es necesario puntuar muchos registros en una solicitud; por ejemplo, cuando se envía al servicio un lote de registros que contiene datos de los clientes para su puntuación, BES es el servicio apropiado. En este caso, la solicitud será asincrónica, donde todos los registros se procesan y se guardan en un blob de Azure antes de devolver una respuesta una vez que todo el procesado se ha completado.  
  
### Uso del modelo entrenado ###
Para configurar el experimento de puntuación, el modelo entrenado ("Predictor de ingresos de adultos", en la ilustración 3) se agregará al experimento. Ya se pueden quitar los demás módulos utilizados para entrenar al modelo entrenado. El flujo de trabajo final tendrá un aspecto similar al de la ilustración 5 que aparece más abajo. 

### Puertos de entrada y salida ###
Después de configurar el experimento con el modelo entrenado (vea la sección anterior para conocer más detalles) y puntuar el experimento actualizado, es necesario establecer los puertos de entrada y salida que especifican los puntos de entrada y salida de los datos en el modelo de predicción y el resultado de la misma, y actuarán como definiciones de interactuación para el Servicio web publicado. El puerto de entrada de Puntuar modelo se puede configurar como se especifica a continuación haciendo clic en con el botón secundario en el punto de entrada.

![][5]  

Ilustración 5: configuración del puerto de entrada de puntuación 

De igual manera, el puerto de salida de Puntuar modelo se puede configurar como se muestra a continuación.

![][6]  

Ilustración 6: configuración del puerto de salida  

## Publicación del servicio ##
Una vez que estén configurados los puertos y el experimento esté en ejecución, el modelo se puede publicar como Servicio web. El primer paso es publicar el servicio en el entorno de ensayo y probarlo ahí para asegurarse de que devuelve los resultados esperados antes de marcarlo como listo para su implementación en producción.  

### Publicación en ensayo ###
Al hacer clic en el icono Publicación del servicio web, se implementará el Servicio web en el entorno de ensayo. 

![][7]  

Ilustración 7: botón Publicación del Servicio web  

Después de publicar el modelo como Servicio web en el entorno de ensayo, se puede probar utilizando los parámetros de entrada y marcarlo para su implementación en producción. El Panel muestra el vínculo para las pruebas. 

![][8]  

Ilustración 8: panel de Servicio web

Al hacer clic en el vínculo Probar y proporcionar los parámetros de puntuación, el Servicio web se puede probar en el entorno de ensayo. La solicitud de prueba se puntúa utilizando el modelo y de acuerdo con los datos proporcionados, y se devuelve el resultado de la puntuación. 

### Publicación en producción ###
La publicación de un Servicio web en producción lo pondrá a disposición de otras aplicaciones, que podrán utilizarlo para predicción y puntuación. Una vez que se haya completado la implementación en el entorno de ensayo y esta se haya probado correctamente, el Servicio web se marca para su implementación en el entorno de producción. 

![][9]  

Ilustración 9: marcado del servicio web como listo para la implementación en producción

Con esto en realidad no se realiza la implementación, sino que se crea una notificación al usuario con los permisos de implementación apropiados para que sea él quien implemente el servicio en producción.

![][10]  

Ilustración 10: notificación de implementación y opción para implementar en producción

## Llamamiento al Servicio web ##
#### RRS ####
El Servicio web RRS es un extremo REST al que se puede llamar desde las aplicaciones cliente utilizando diversos lenguajes de programación. La página de ayuda de API incluye un vínculo al código de ejemplo para llamar al nuevo Servicio web con ejemplos en C#, R y Python.

![][11]  

Ilustración 11: código de ejemplo para llamar a RRS  

## Experimentos que no son de puntuación ##
Además de crear Servicios web de puntuación, es posible crear experimentos para realizar otras tareas como la extracción y la transformación de datos. En este caso, el Servicio web no realizaría operaciones de aprendizaje automático. Utiliza las funciones de manipulación de datos de Estudio de aprendizaje automático de Azure para leer desde diversos orígenes de datos, convertir tipos de datos o filtrar y aplicar datos y manipulaciones matemáticas.   

### Publicación de un Servicio web que no es de puntuación ###
Los pasos para publicar un Servicio web que no es de puntuación son similares a los del servicio de puntuación descrito anteriormente. La diferencia principal es que el puerto de salida no se define en Puntuar modelo.

# Actualización de un servicio publicado #
Puede ser necesario actualizar un Servicio web publicado por diversos motivos, como la actualización de datos de entrenamiento, la realización de cambios en el esquema de datos utilizado para el entrenamiento y la puntuación, la necesidad de mejorar el algoritmo o la aplicación de otros cambios en el modelo de aprendizaje automático original. Estos cambios afectarán al modelo entrenado y los resultados de puntuación, y requerirán la publicación de un Servicio web actualizado.

![][12]  

Ilustración 12: edición del modelo y publicación del Servicio web de puntuación actualizado  

## Actualización del modelo entrenado ##
Los cambios en el experimento de entrenamiento requieren un nuevo entrenamiento del modelo entrenado. Para ello, es necesario editar el modelo publicado. El ejemplo siguiente muestra el flujo de trabajo de puntuación de la ilustración 5 anterior después de quitar el modelo entrenado existente.

![][13]  

Ilustración 13: modelo entrenado quitado del flujo de trabajo  

El paso siguiente sería agregar los módulos necesarios para dividir los datos en segmentos de entrenamiento y prueba, aplicar el algoritmo de aprendizaje, entrenar el modelo, puntuar los datos de entrenamiento y valorar los resultados. Tenga en cuenta que esto puede variar en función del resto de cambios requeridos para el experimento, como la necesidad de aplicar un algoritmo de aprendizaje diferente como ejemplo. (Ilustración 14)  

Una vez agregados los módulos nuevos, es necesario configurarlos del modo oportuno antes de volver a ejecutar el experimento. Como ejemplo, el círculo rojo del ejemplo siguiente indica que no se ha configurado la columna de la etiqueta para Entrenar modelo.

![][14]  

Ilustración 14: módulos agregados para volver a entrenar el modelo.  

Al hacer clic en Entrenar modelo y configurar el nombre de la columna Etiqueta se resolverá el problema. 

![][15]  

Ilustración 15: selección de la columna de entrada como etiqueta  

## Guardado del modelo entrenado actualizado ##
Una vez que todos los nuevos módulos estén correctamente configurados, el experimento debería guardarse y volver a ejecutarse correctamente. Entrenar modelo podrá guardarse entonces (como se muestra en la Ilustración 2 anterior). La diferencia es que en este caso es necesario marcar la casilla para actualizar el modelo entrenado existente. 

![][16]  

Ilustración 16: actualizar el modelo entrenado existente

## Publicación del servicio actualizado ##
Una vez actualizado el modelo entrenado, se repetirán los pasos descritos en la sección anterior sobre Publicación de un **Servicio web de Aprendizaje automático de Azure**:  

-	Uso del modelo entrenado (recién actualizado) en el experimento de puntuación
-	Configuración de los puertos de entrada/salida
-	Publicación en ensayo
-	Publicación en producción  

Una vez que se ha actualizado el experimento y se ha creado y puntuado el nuevo modelo entrenado, al hacer clic en Publicar Servicio web se publicará el servicio. El servicio recién creado sobrescribirá al existente.


<!--Image references-->
[1]: ./media/machine-learning-overview-of-azure-ml-process/oamlp1.png
[2]: ./media/machine-learning-overview-of-azure-ml-process/oamlp2.png
[3]: ./media/machine-learning-overview-of-azure-ml-process/oamlp3.png
[4]: ./media/machine-learning-overview-of-azure-ml-process/oamlp4.png
[5]: ./media/machine-learning-overview-of-azure-ml-process/oamlp5.png
[6]: ./media/machine-learning-overview-of-azure-ml-process/oamlp6.png
[7]: ./media/machine-learning-overview-of-azure-ml-process/oamlp7.png
[8]: ./media/machine-learning-overview-of-azure-ml-process/oamlp8.png
[9]: ./media/machine-learning-overview-of-azure-ml-process/oamlp9.png
[10]: ./media/machine-learning-overview-of-azure-ml-process/oamlp10.png
[11]: ./media/machine-learning-overview-of-azure-ml-process/oamlp11.png
[12]: ./media/machine-learning-overview-of-azure-ml-process/oamlp12.png
[13]: ./media/machine-learning-overview-of-azure-ml-process/oamlp13.png
[14]: ./media/machine-learning-overview-of-azure-ml-process/oamlp14.png
[15]: ./media/machine-learning-overview-of-azure-ml-process/oamlp15.png
[16]: ./media/machine-learning-overview-of-azure-ml-process/oamlp16.png

<!--Link references-->
