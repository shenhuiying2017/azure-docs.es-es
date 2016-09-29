<properties
   pageTitle="Realización de preguntas que pueden responderse con datos - formular preguntas | Microsoft Azure"
   description="Aprenda a formular una pregunta de ciencia de datos en Ciencia de datos para principiantes, vídeo 3. Incluye una comparación de preguntas de clasificación y regresión."
   keywords="elegir una pregunta,preguntas de ciencias de datos,formular pregunta,formular preguntas,preguntas de regresión,preguntas de clasificación,pregunta directa"
   services="machine-learning"
   documentationCenter="na"
   authors="brohrer-ms"
   manager="jhubbard"
   editor="cjgronlund"/>

<tags
   ms.service="machine-learning"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/15/2016"
   ms.author="cgronlun;brohrer;garye"/>

# Realización de preguntas que pueden responderse con datos

## Vídeo 3: Ciencia de datos para principiantes

Aprenda a formular una pregunta de ciencia de datos en Ciencia de datos para principiantes, vídeo 3. Este vídeo incluye una comparación de preguntas para algoritmos de clasificación y regresión.

Para obtener el máximo partido de la serie, véalos en orden. [Ir a la lista de vídeos](#other-videos-in-this-series)

> [AZURE.VIDEO data-science-for-beginners-ask-a-question-you-can-answer-with-data]

## Otros vídeos de la serie

*Ciencia de datos para principiantes* es una introducción rápida a la ciencia de datos en cinco vídeos de corta duración.

  * Vídeo 1: [Las cinco preguntas a las que responde la ciencia de datos](machine-learning-data-science-for-beginners-the-5-questions-data-science-answers.md) *(5 minutos y 14 segundos)*
  * Vídeo 2: [¿Están sus datos preparados para la ciencia de datos?](machine-learning-data-science-for-beginners-is-your-data-ready-for-data-science.md) *(4 minutos y 56 segundos)*
  * Vídeo 3: Realización de preguntas que pueden responderse con datos
  * Vídeo 4: [Predicción de respuestas con un modelo sencillo](machine-learning-data-science-for-beginners-predict-an-answer-with-a-simple-model.md) *(7 minutos y 42 segundos)*
  * Vídeo 5: [Copia del trabajo de otras personas para realizar ciencia de datos](machine-learning-data-science-for-beginners-copy-other-peoples-work-to-do-data-science.md) *(3 minutos y 18 segundos)*

## Transcripción: Realización de preguntas que pueden responderse con datos

Bienvenido al tercer vídeo de la serie "Ciencia de datos para principiantes".

En este vídeo, recibirá algunas sugerencias para formular una pregunta que se pueda responder con datos.

Para aprovechar mejor este vídeo, vea antes los dos primeros de esta serie: "Las cinco preguntas a las que responde la ciencia de datos"y "Is your data is ready for data science?" (¿Están sus datos preparados para la ciencia de datos?)

## Formulación de una pregunta directa

Hemos hablado de que la ciencia de datos es el proceso de usar nombres (llamados también categorías o etiquetas) y números para predecir una respuesta a una pregunta. Pero no sirve cualquier pregunta; debe ser una *pregunta directa.*

Una pregunta vaga no se tiene que responder con un nombre o un número. Una pregunta directa sí.

Imagine que encuentra una lámpara mágica con un genio que responde de forma veraz a cualquier pregunta que formule. Pero este genio es travieso, e intentará responder de forma tan vaga y confusa mientras pueda salirse con la suya. Quiere precisar una pregunta de forma tan irrefutable que a la fuerza le diga lo que quiere saber.

Si formulara una pregunta vaga como "¿Qué va a pasar con mis acciones?", el genio podría responder: "El precio cambiará". Es una respuesta veraz, pero no sirve de mucha ayuda.

Pero si tuviera que formular una pregunta directa, como "¿Cuál será el precio de venta de mis acciones la próxima semana?", el genio no podrá evitar darle una respuesta específica y predecir un precio de venta.

## Ejemplos de respuesta: datos de destino

Después de formular la pregunta, compruebe si tiene ejemplos de la respuesta en sus datos.

Si nuestra pregunta es "¿Cuál será el precio de venta de mis acciones la próxima semana?", deberemos asegurarnos de que nuestros datos incluyan el historial de precios de cotización.

Si nuestra pregunta es "¿Qué coche de mi flota se averiará primero?", deberemos asegurarnos de que nuestros datos incluyan información sobre averías anteriores.

![Datos de destino: ejemplos de la respuesta. Formular una pregunta de ciencia de datos.](./media/machine-learning-data-science-for-beginners-ask-a-question-you-can-answer-with-data/machine-learning-data-science-target-data.png)

Estos ejemplos de respuestas se denominan destino. Un destino es lo que intentamos predecir sobre los puntos de datos futuros, ya sea una categoría o un número.

Si no tiene los datos de destino, deberá obtener algunos. Sin ellos, no podrá responder a su pregunta.

## Reformulación de la pregunta

A veces, puede formular la pregunta con otras palabras a fin de obtener una respuesta más útil.

La pregunta "¿Este punto de datos es A o B?" predice la categoría (el nombre o la etiqueta) de algo. Para responderla, usamos un *algoritmo de clasificación*.

La pregunta "¿Cuánto?" o "¿Cuántos?" predice una cantidad. Para responderla, usamos un *algoritmo de regresión*.

Para ver cómo podemos transformarlas, examinemos la pregunta: "¿Qué reportaje de noticias es el más interesante para este lector?" Se pregunta por una predicción de una sola opción entre muchas posibilidades, es decir: ¿Es A, B, C o D?, y se usaría un algoritmo de clasificación.

Pero esta pregunta puede ser más fácil de responder si la formula de esta forma: "¿Cuánto interés tiene cada reportaje de la lista para este lector?" Ahora puede dar a cada artículo una puntuación numérica, de esta forma será fácil identificar el artículo con la puntuación más alta. Se trata de reformular la pregunta de clasificación como una de regresión o ¿cuánto?

![Reformular la pregunta. Pregunta de clasificación frente a pregunta de regresión.](./media/machine-learning-data-science-for-beginners-ask-a-question-you-can-answer-with-data/machine-learning-data-science-classification-question-vs-regression-question.png)

El modo de formular una pregunta es una pista de qué algoritmo puede darle una respuesta.

Encontrará que ciertas familias de algoritmos, como los de nuestro ejemplo de reportajes de noticias, están estrechamente relacionadas. Puede reformular la pregunta para usar el algoritmo que le proporcione la respuesta más útil.

Y más importante aún, formule esa pregunta directa, la pregunta que se pueda responder con datos. Y asegúrese de tener los datos adecuados para responderla.

Hemos hablado de algunos principios básicos para formular una pregunta que se pueda responder con datos.

Asegúrese de ver los otros cuatro vídeos de "Ciencia de datos para principiantes" en Aprendizaje automático de Microsoft Azure.


## Pasos siguientes

  * [Prueba de su primer experimento de ciencia de datos con Aprendizaje automático de Azure](machine-learning-create-experiment.md)
  * [Introducción a Aprendizaje automático en Microsoft Azure](machine-learning-what-is-machine-learning.md)

<!---HONumber=AcomDC_0914_2016-->