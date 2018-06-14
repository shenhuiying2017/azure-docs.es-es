---
title: Mayor precisión de Computer Vision y modelos de clasificación en Azure Machine Learning
description: Obtenga información sobre cómo mejorar la precisión de los modelos de clasificación de imágenes, detección de objetos y similitud de imágenes de Computer Vision con Azure Machine Learning Package for Computer Vision.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: netahw
author: nhaiby
ms.date: 04/23/2018
ms.openlocfilehash: e134e1e544c51d6756d5021fef8c049fe7d8afb0
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/07/2018
ms.locfileid: "33783584"
---
# <a name="improve-the-accuracy-of-computer-vision-models"></a>Mejora de la precisión de los modelos de Computer Vision

Con **Azure Machine Learning Package for Computer Vision**, puede crear e implementar modelos de clasificación de imágenes, detección de objetos y similitud de imágenes. Obtenga más información sobre este paquete y cómo instalarlo.

En este artículo, aprenderá a ajustar estos modelos para aumentar su precisión. 

## <a name="accuracy-of-image-classification-models"></a>Precisión de los modelos de clasificación de imágenes

Computer Vision Package se muestra para brindar buenos resultados para una amplia variedad de conjuntos de datos. Sin embargo, como sucede en la mayoría de los proyectos de aprendizaje automático, para obtener los mejores resultados posibles en un conjunto de datos nuevo, es necesario ajustar los parámetros con mucho cuidado, así como sopesar detenidamente diversas decisiones de diseño. En las siguientes secciones se proporcionan instrucciones sobre cómo mejorar la precisión en un conjunto de datos determinado, es decir, cuáles son los parámetros más prometedores para optimizarlos en primer ligar, qué valores se deben intentar en estos parámetros y qué dificultades evitar.

En términos generales, entrenar los modelos de aprendizaje profundo implica encontrar el equilibrio entre el tiempo de entrenamiento y la precisión del modelo. Computer Vision Package tiene parámetros predeterminados preestablecidos (consulte la primera fila de la tabla a continuación) que se centran en la velocidad de un entrenamiento rápido a la vez que, por lo general, se generan modelos de alta precisión. A menudo, esta precisión se puede mejorar si se usa, por ejemplo, una mayor resolución de la imagen o modelos más profundos, pero al costo de aumentar el tiempo de entrenamiento por un factor de 10 veces o más.

Se recomienda trabajar primero con los parámetros predeterminados, entrenar un modelo, revisar los resultados, corregir las anotaciones después de la prueba real según sea necesario y solo entonces probar los parámetros que ralentizan el tiempo de entrenamiento (consulte la tabla a continuación de los valores de parámetros sugeridos). Se recomienda conocer estos parámetros aunque no es necesario desde el punto de vista técnico.


### <a name="best-practices-and-tips"></a>Procedimientos recomendados y sugerencias

* Calidad de los datos: la calidad de los conjuntos de entrenamiento y prueba debe ser alta. Es decir, procure que las imágenes estén correctamente anotadas, que se quitaron las imágenes ambiguas (por ejemplo, donde no es claro para el ojo humano si la imagen muestra una pelota de tenis o un limón) y que los atributos son excluyentes entre sí (es decir, que cada imagen pertenece a exactamente un atributo).

* Antes de perfeccionar la DNN, se debe entrenar un clasificador SVM con una DNN previamente entrenada y corregida como caracterizador. Esta acción es compatible con Computer Vision Package y el entrenamiento no requiere mucho tiempo, porque la DNN misma no se modifica. Incluso este enfoque sencillo a menudo alcanza precisiones buenas, por lo que representa una sólida línea de base. Entonces, el paso siguiente es perfeccionar la DNN que debería generar mayor precisión.

* Hay constancia de que, si el objeto de interés es pequeño en la imagen, los métodos de clasificación de imágenes no funcionan bien. En esos casos, considere la posibilidad de usar un enfoque de detección de objetos, como Faster R-CNN de Computer Vision Package basado en TensorFlow.

* Cuantos más datos de entrenamiento haya, mejor. Como regla general, un usuario debería tener al menos 100 ejemplos de cada clase, es decir, 100 imágenes para "perro", 100 imágenes para "gato", etc. Es posible entrenar un modelo con menos imágenes, pero podría no generar buenos resultados.

* Las imágenes de entrenamiento deben residir localmente en la máquina con la GPU y deben estar en una unidad SSD (no HDD). En caso contrario, la latencia de la lectura de imágenes pueden disminuir considerablemente la velocidad del entrenamiento (incluso por un factor de 100).


### <a name="parameters-to-optimize"></a>Parámetros para optimizar

Encontrar los valores óptimos de estos parámetros es importante y a menudo puede mejorar considerablemente la precisión:
* Velocidad de aprendizaje (`lr_per_mb`): seguramente, el parámetro más importante para realizar correctamente esta tarea. Si la precisión del conjunto de entrenamiento después del perfeccionamiento de la DNN es mayor al 5 %, es muy probable que la velocidad de aprendizaje también sea alta o el número de tandas de entrenamiento sea demasiado bajo. Particularmente con los conjuntos de datos pequeños, la DNN tiende a sobreajustarse a los datos de entrenamiento; sin embargo, en la práctica esto generará buenos modelos en el conjunto de pruebas. Por lo general, usamos 15 tandas en las que la velocidad de aprendizaje inicial se reduce dos veces. En algunos casos, el entrenamiento que usa más tandas puede mejorar el rendimiento.

* Resolución de entrada (`image_dims`): la resolución de imagen predeterminada es de 224 x 224 píxeles. Si usa una resolución de imagen mayor, por ejemplo de 500 x 500 píxeles o de 1000 x 1000 píxeles, puede mejorar considerablemente la precisión, pero disminuir el perfeccionamiento de la DNN. Computer Vision Package espera que la resolución de entrada sea una tupla de (color-channels, image-width, image-height), por ejemplo (3, 224, 224), en la que el número de canales de color debe estar establecido en 3 (las bandas rojo-verde-azul).

* Arquitectura del modelo (`base_model_name`): intente usar DNN más profundas como ResNet-34 o ResNet-50 en lugar del modelo ResNet-18 predeterminado. El modelo ResNet-50 no solo es más profundo, sino que, además, su salida de la penúltima capa tiene un tamaño de 2048 flotantes (frente a los 512 flotantes de los modelos ResNet-18 y ResNet 34). Esta mayor dimensionalidad puede resultar particularmente útil cuando la DNN se mantiene fija y se entrena en su lugar un clasificador SVM.

* Tamaño de minilote (`mb_size`): los tamaños de minilotes grandes generarán un tiempo de entrenamiento más rápido a costa de un mayor consumo de memoria de la DNN. Por lo tanto, al seleccionar modelos más profundos (por ejemplo, ResNet-50 en comparación con ResNet-18) o una resolución de imagen mayor (500\*500 píxeles en comparación con 224\*224 píxeles), por lo general se debe disminuir el tamaño de minilote para evitar los errores de memoria insuficiente. Cuando se cambia el tamaño de minilote, a menudo también es necesario ajustar la velocidad de aprendizaje, tal como se puede ver en la tabla a continuación.
* Tasa de omisión (`dropout_rate`) and regularizador L2 (`l2_reg_weight`): es posible disminuir el sobreajuste de la DNN con una tasa de omisión de 0,5 (el valor predeterminado es 0,5 en Computer Vision Package) o más y mediante el aumento de la ponderación del regularizador (el valor predeterminado es 0,0005 en Computer Vision Package). Pero observe que, especialmente con paquetes de datos pequeños, es difícil y a veces imposible evitar el sobreajuste de la DNN.


### <a name="parameter-definitions"></a>Definiciones de parámetros

- **Velocidad de aprendizaje**: tamaño de los pasos que se usan durante el aprendizaje de descenso de gradiente. Si el valor es demasiado bajo, el entrenamiento del modelo tardará muchas tandas. Si el valor es demasiado alto, el modelo no convergerá en una buena solución. Por lo general, se usa una programación cuando la velocidad de aprendizaje disminuye después de cierto número de tandas. Por ejemplo, la programación de velocidad de aprendizaje `[0.05]*7 + [0.005]*7 + [0.0005]` corresponde a usar una velocidad de aprendizaje inicial de 0,05 para las siete primeras tandas, seguido de una velocidad de aprendizaje 10 veces menor de 0,005 para otras siete tandas y, finalmente, ajustar el modelo para una sola tanda con una velocidad de aprendizaje 100 veces menor de 0,0005.

- **Tamaño de minilote**: las GPU pueden procesar varias imágenes en paralelo para acelerar el cálculo. Estas imágenes procesadas en paralelo también se conocen como minilotes. Los tamaños de minilote más grandes generarán un entrenamiento más rápido, pero a costa de un mayor consumo de memoria de la DNN.

### <a name="recommended-parameter-values"></a>Valores de parámetro recomendados

La tabla siguiente brinda distintos conjuntos de parámetros que se mostraron para generar modelos de alta precisión en una amplia variedad de tareas de clasificación de imágenes. Los parámetros óptimos dependen del conjunto de datos específico y de la GPU exacta que se usa, por lo que la tabla solo se debe considerar como una referencia. Después de probar estos parámetros, considere también las resoluciones de imágenes de más de 500 x 500 píxeles o modelos más profundos, como ResNet-101 o ResNet-152.

La primera fila de la tabla corresponde a los parámetros predeterminados establecidos en Computer Vision Package. El entrenamiento de todas las otras filas tarda más (lo que se indica en la primera columna), pero con la ventaja de una mayor precisión (consulte la segunda columna para ver la precisión promedio en tres conjuntos de datos internos). Por ejemplo, los parámetros de la última fila tardan entre 5 y 15 veces más en entrenar, lo que sin embargo genera una mayor precisión (promedio) en tres conjuntos de pruebas internos, de 82,6 % a 92,8 %.

Los modelos más profundos y una resolución de entrada ocupan más memoria de DNN y, por tanto, el tamaño de minilote debe ser menor con una mayor complejidad de modelo para evitar errores de memoria insuficiente. Como se ve en la tabla a continuación, resulta útil disminuir la velocidad de aprendizaje por un factor de dos cada vez que se disminuye el tamaño de minilote por el mismo multiplicador. Puede que sea necesario disminuir aún más el tamaño de minilote en las GPU con menores cantidades de memoria.

| Tiempo de entrenamiento (estimación) | Precisión de ejemplo | Tamaño de minilote (*mb_size*) | Velocidad de aprendizaje (*lr_per_mb*) | Resolución de imagen (*image_dims*) | Arquitectura de la DNN (*base_model_name*) |
|------------- |:-------------:|:-------------:|:-----:|:-----:|:---:|
| 1x (referencia) | 82,6 % | 32 | [0,05]\*7 + [0,005]\*7 + [0,0005]  | (3, 224, 224) | ResNet18_ImageNet_CNTK |
| 2-5x    | 90,2 % | 16 | [0,025]\*7 + [0,0025]\*7 + [0,00025] | (3, 500, 500) | ResNet18_ImageNet_CNTK |
| 2-5x    | 87,5 % | 16 | [0,025]\*7 + [0,0025]\*7 + [0,00025] | (3, 224, 224) | ResNet50_ImageNet_CNTK |
| 5-15x        | 92,8 % |  8 | [0,01]\*7 + [0,001]\*7 + [0,0001]  | (3, 500, 500) | ResNet50_ImageNet_CNTK |


## <a name="next-steps"></a>Pasos siguientes

Para información sobre Azure Machine Learning Package for Computer Vision:
+ Consulte la documentación de referencia

+ Obtenga información sobre [otros paquetes Python para Azure Machine Learning](reference-python-package-overview.md).