---
title: "Ejecución de scripts de aprendizaje automático de Python | Microsoft Docs"
description: "Describe los principios de diseño subyacentes a la compatibilidad con scripts de Python en Azure Machine Learning y los escenarios de uso básico, las funcionalidades y las limitaciones."
keywords: "aprendizaje automático de Python, pandas, pandas de python, scripts de python, ejecutar scripts de python"
services: machine-learning
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: ee9eb764-0d3e-4104-a797-19fc29345d39
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/29/2017
ms.author: bradsev;garye
ms.openlocfilehash: c25f31ca72417672298657c4585184ad72db6c99
ms.sourcegitcommit: 5a6e943718a8d2bc5babea3cd624c0557ab67bd5
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/01/2017
---
# <a name="execute-python-machine-learning-scripts-in-azure-machine-learning-studio"></a>Ejecución de scripts de Python en Azure Machine Learning Studio

En este tema se describen los principios de diseño subyacentes a la compatibilidad actual con scripts de Python en Azure Machine Learning. También se esbozan las principales funcionalidades que proporciona, que incluyen:

- ejecución de escenarios de uso básicos
- puntuación de un experimento en un servicio web
- compatibilidad con la importación de código existente
- exportación de visualizaciones
- selección de características supervisada
- comprensión de algunas limitaciones

[Python](https://www.python.org/) es una herramienta indispensable de la caja de herramientas de muchos científicos de datos. Tiene:

* una sintaxis elegante y concisa, 
* asistencia multiplataforma, 
* una amplia colección de bibliotecas eficaces y 
* herramientas de desarrollo perfeccionadas. 

Python se usa en todas las fases de un flujo de trabajo que normalmente se emplea en el modelado de aprendizaje automático:

- ingesta y procesamiento de datos 
- construcción de características
- entrenamiento del modelo 
- validación del modelo
- implementación de los modelos

Azure Machine Learning Studio admite la incrustación de scripts de Python en varias partes de un experimento de aprendizaje automático y, además, su publicación sin problemas como servicios web en Microsoft Azure.

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]


## <a name="design-principles-of-python-scripts-in-machine-learning"></a>Principios de diseño de los scripts de Python en Machine Learning

La interfaz principal para Python en Azure Machine Learning Studio es mediante el módulo [Ejecutar script de Python][execute-python-script] que aparece en la figura 1.

![imagen1](./media/execute-python-scripts/execute-machine-learning-python-scripts-module.png)

![imagen2](./media/execute-python-scripts/embedded-machine-learning-python-script.png)

Figura 1. El módulo **Ejecutar script de Python** .

El módulo [Ejecutar script de Python][execute-python-script] de Azure ML Studio acepta hasta tres entradas y genera hasta dos salidas (que se tratan en la siguiente sección), al igual que su análogo de R, el módulo [Ejecutar script R][execute-r-script]. El código Python que se ejecutará se escribe en el cuadro de parámetro como una función de punto de entrada especialmente denominada llamada `azureml_main`. Los siguientes son los principios de diseño clave que se usan para implementar este módulo:

1. *Debe ser idiomático para los usuarios de Python.* La mayoría de los usuarios de Python incluye su código como funciones dentro de módulos. Así, la colocación de muchas instrucciones ejecutables en un módulo de nivel superior es relativamente rara. Como resultado, el cuadro del script también asume una función de Python especialmente denominada, en contraposición a solo una secuencia de instrucciones. Los objetos expuestos en la función son tipos de bibliotecas de Python estándar, como tramas de datos [Pandas](http://pandas.pydata.org/) y matrices [NumPy](http://www.numpy.org/).
2. *Debe contar con alta fidelidad entre las ejecuciones locales y las ejecuciones en la nube.* El back-end que se usa para ejecutar el código de Python se basa en [Anaconda](https://store.continuum.io/cshop/anaconda/), una distribución científica multiplataforma de Python ampliamente usada. Incluye cerca de 200 de los paquetes más comunes de Python. Por lo tanto, los científicos de datos pueden depurar y calificar su código en su entorno Anaconda compatible con la instancia de Azure Machine Learning local. Luego usan un entorno de desarrollo existente, como [IPython](http://ipython.org/) Notebook o las [Herramientas de Python para Visual Studio](http://aka.ms/ptvs), para ejecutarlo como parte de un experimento de Azure Machine Learning. El punto de entrada `azureml_main` es una función vainilla de Python y por tanto ****se puede crear sin código específico de Azure Machine Learning ni el SDK instalado.
3. *Debe admitir composición sin problemas con otros módulos de Azure Machine Learning.* El módulo [Ejecutar script de Python][execute-python-script] acepta, como entradas y salidas, conjuntos de datos estándar de Azure Machine Learning. El marco subyacente une los runtimes de Azure Machine Learning y Python de forma transparente y eficaz. Por tanto, Python se puede usar con flujos de trabajo existentes de Azure Machine Learning, incluidos los que llaman a R y SQLite. Como resultado, los científicos de datos pueden crear flujos de trabajo que:
   * usen Python y Pandas para el procesamiento previo y la limpieza de datos
   * transmitan los datos a una transformación de SQL que una varios conjuntos de datos para formar características
   * entrenen modelos mediante los algoritmos de Azure Machine Learning 
   * evalúen y procesen posteriormente los resultados con R.


## <a name="basic-usage-scenarios-in-ml-for-python-scripts"></a>Escenarios de uso básicos de Aprendizaje automático para scripts de Python

En esta sección, analizaremos algunos de los usos básicos del módulo [Ejecutar script de Python][execute-python-script]. Las entradas en el módulo Python se exponen como tramas de datos de Pandas. La función debe devolver una sola trama de datos de Pandas empaquetada dentro de una [secuencia](https://docs.python.org/2/c-api/sequence.html) de Python, como una tupla, una lista o una matriz de NumPy. Luego, el primer elemento de esta secuencia se devuelve en el primer puerto de salida del módulo. Este esquema aparece en la figura 2.

![imagen3](./media/execute-python-scripts/map-of-python-script-inputs-outputs.png)

Ilustración 2. Asignación de puertos de entrada a parámetros y valor de devolución a puerto de salida.

Puede ver una semántica más detallada de cómo se asignan los puertos de entrada a los parámetros de la `azureml_main` función en la tabla 1:

![imagen1T](./media/execute-python-scripts/python-script-inputs-mapped-to-parameters.png)

Tabla 1. Asignación de puertos de entrada a parámetros de función.

La asignación entre los puertos de entrada y los parámetros de función es posicional:

- El primer puerto de entrada conectado se asigna al primer parámetro de la función. 
- La segunda entrada (si está conectada) se asigna al segundo parámetro de la función.

Vea *Python for Data Analysis* (O'Reilly, 2012) de W. McKinney para más información sobre Python Pandas y sobre cómo se puede usar para manipular datos de manera eficaz y eficiente. 


## <a name="translation-of-input-and-output-types"></a>Traslación de tipos de entrada y salida 
Los conjuntos de datos de entrada de Azure Machine Learning se convierten en tramas de datos en Pandas. Las tramas de datos de salida se vuelven a convertir en conjuntos de datos de Azure Machine Learning. Se realizan las siguientes conversiones:

1. Las columnas numéricas y de cadena se convierten tal cual y los valores que faltan en un conjunto de datos se convierten en valores "NA" en Pandas. Se produce la misma conversión a la inversa (los valores NA de Pandas se convierten en valores que faltan en Azure Machine Learning).
2. Los vectores de índice de Pandas no se admiten en Azure Machine Learning. Todas las tramas de datos de entrada de la función de Python siempre tienen un índice numérico de 64 bits entre 0 y el número de filas menos 1. 
3. Los conjuntos de datos de Azure Machine Learning no pueden tener nombres de columnas duplicados ni nombres de columnas que no sean cadenas. Si una trama de datos de salida contiene columnas no numéricas, el marco llama a `str` en los nombres de columna. Del mismo modo, los nombres de columnas duplicados se alternan automáticamente para asegurarse de que los nombres sean únicos. El sufijo (2) se agrega al primer duplicado, (3) al segundo duplicado, etc.


## <a name="operationalizing-python-scripts"></a>Funcionamiento de los scripts de Python

Cuando un experimento de puntuación se publica como servicio web, se llama a todos los módulos [Ejecutar script de Python][execute-python-script] usados en él. Por ejemplo, la figura 3 muestra un experimento de puntuación que contiene el código para evaluar una única expresión de Python. 

![imagen4](./media/execute-python-scripts/figure3a.png)

![imagen5](./media/execute-python-scripts/python-script-with-python-pandas.png)

Figura 3. Servicio web para evaluar una expresión de Python.

Un servicio web creado a partir de este experimento:

- toma como entrada una expresión de Python (como una cadena)
- la envía al intérprete de Python 
- devuelve una tabla que contiene la expresión y el resultado evaluado.


## <a name="importing-existing-python-script-modules"></a>Importación de módulos existentes de scripts de Python

Un caso de uso común para muchos científicos de datos es incorporar scripts existentes de Python a experimentos de Azure Machine Learning. En lugar de exigir que todo el código se concatene y se pegue en un solo cuadro de script, el módulo [Ejecutar script de Python][execute-python-script] acepta un archivo ZIP que contiene los módulos de Python en el tercer puerto de entrada. El marco de ejecución descomprime el archivo en runtime y el contenido se agrega a la ruta de acceso de la biblioteca del intérprete de Python. La función de punto de entrada `azureml_main` luego puede importar directamente estos módulos.

Por ejemplo, considere el archivo Hello.py que contiene una función "Hello, World".

![imagen6](./media/execute-python-scripts/figure4.png)

Figura 4. Función definida por el usuario en el archivo Hello.py.

A continuación, creamos un archivo Hello.zip que contiene Hello.py:

![imagen7](./media/execute-python-scripts/figure5.png)

Figura 5. Archivo ZIP que contiene código Python definido por el usuario.

Cargue el archivo ZIP como un conjunto de datos en Azure Machine Learning Studio. Luego cree y ejecute un experimento que use el código de Python del archivo Hello.zip al asociarlo al tercer puerto de entrada del módulo **Ejecutar script de Python**, como se muestra en esta figura.

![imagen8](./media/execute-python-scripts/figure6a.png)

![imagen9](./media/execute-python-scripts/figure6b.png)

Figura 6. Experimento de ejemplo con código Python definido por el usuario cargado como archivo ZIP.

La salida del módulo muestra que el archivo ZIP se ha desempaquetado y que la función `print_hello` se ha ejecutado.
 
![image10](./media/execute-python-scripts/figure7.png)

Figura 7. Función definida por el usuario en uso dentro del módulo [Ejecutar script de Python][execute-python-script].


## <a name="working-with-visualizations"></a>Trabajo con visualizaciones

El módulo [Ejecutar script de Python][execute-python-script] puede devolver los gráficos creados con MatplotLib que se pueden visualizar en el explorador. Sin embargo, los gráficos no se redirigen automáticamente a imágenes como sucede cuando se usa R. Por lo tanto, el usuario debe guardar explícitamente todo gráfico como archivo PNG si se va a devolver a Azure Machine Learning. 

Para generar imágenes desde MatplotLib, debe realizar el siguiente procedimiento:

* cambie el backend a "AGG" desde el representador basado en Qt predeterminado, 
* cree un nuevo objeto de figura, 
* obtenga el eje y genere todos los gráficos en él, 
* guarde la figura en un archivo PNG. 

Este proceso se muestra en la figura 8 que aparece a continuación, donde se crea una matriz de gráfico de dispersión con la función scatter_matrix de Pandas.

![imagen1v](./media/execute-python-scripts/figure-v1-8.png)

Figura 8. Código para guardar figuras de MatplotLib en imágenes.

La figura 9 muestra un experimento que usa el script mostrado anteriormente para devolver gráficos a través del segundo puerto de salida.

![imagen2v](./media/execute-python-scripts/figure-v2-9a.png) 

![imagen2v](./media/execute-python-scripts/figure-v2-9b.png) 

Figura 9. Visualización de los gráficos generados a partir del código Python.

Es posible devolver varias figuras si se guardan en distintas imágenes: el tiempo de ejecución de Azure Machine Learning selecciona todas las imágenes y las concatena para visualización.


## <a name="advanced-examples"></a>Ejemplos avanzados

El entorno Anaconda instalado en Azure Machine Learning contiene paquetes comunes como NumPy, SciPy y Scikits-Learn. Estos paquetes se pueden usar de forma efectiva para distintas tareas de procesamiento de datos en una canalización de aprendizaje automático. Como ejemplo, el siguiente experimento y script muestra el uso de sistemas aprendices de conjunto de Scikits-Learn para calcular las puntuaciones de importancia de características de un conjunto de datos. Las puntuaciones se pueden usar para realizar una selección supervisada de características antes de transmitirlas a otro modelo de aprendizaje automático.

Aquí se muestra la función de Python usada para calcular las puntuaciones de importancia y ordenar las características según esas puntuaciones:

![imagen11](./media/execute-python-scripts/figure8.png)

Figura 10. Función para clasificar características por puntuaciones.
  El siguiente experimento calcula y devuelve las puntuaciones de importancia de características en el conjunto de datos "Diabetes en los indios Pima" en Azure Machine Learning:

![image12](./media/execute-python-scripts/figure9a.png)
![image13](./media/execute-python-scripts/figure9b.png)    

Figura 11. Experimento para clasificar las características del conjunto de datos Diabetes en los indios Pima.

## <a name="limitations"></a>Limitaciones
[Ejecutar script de Python][execute-python-script] actualmente tiene las siguientes limitaciones:

1. *Ejecución en espacio aislado.* El tiempo de ejecución de Python actualmente se realiza en espacio aislado y, como resultado, no permite el acceso a la red o al sistema de archivos local de manera persistente. Todos los archivos guardados localmente son aislados y eliminados una vez que se finaliza el módulo. El código Python no puede tener acceso a la mayoría de los directorios de la máquina en que se ejecuta, con la excepción del directorio actual y sus subdirectorios.
2. *Falta de compatibilidad con la depuración y desarrollo sofisticado.* El módulo Python actualmente no es compatible con características de IDE, como IntelliSense y depuración. Además, si se produce un error en el módulo en runtime, el seguimiento de la pila completo de Python está disponible. Pero debe verse en el registro de salida del módulo. De momento se recomienda que los usuarios desarrollen y depuren scripts de Python en un entorno como IPython y luego importen el código al módulo.
3. *Salida de una trama de datos.* El punto de entrada de Python solo tiene permitido devolver una trama de datos como salida. Actualmente no es posible devolver objetos arbitrarios de Python, como modelos entrenados, directamente de vuelta al tiempo de ejecución de Azure Machine Learning. Al igual que [Ejecutar script R][execute-r-script], que tiene la misma limitación, en muchos casos es posible incluir objetos en una matriz de bytes y luego devolverla dentro de una trama de datos.
4. *Incapacidad para personalizar la instalación de Python*. Actualmente, la única manera de agregar módulos personalizados de Python es a través del mecanismo de archivo ZIP descrito anteriormente. A pesar de que esto es viable cuando se trata de módulos pequeños, es complicado para módulos de gran tamaño (especialmente para los módulos con DLL nativas) o para un gran número de módulos. 

## <a name="conclusions"></a>Conclusiones
El módulo [Ejecutar script de Python][execute-python-script] permite que un científico de datos incorpore código Python existente en flujos de trabajo de aprendizaje automático hospedados en la nube en Azure Machine Learning y hacerlos operativos como parte de un servicio web. El módulo de script de Python interopera de forma natural con otros módulos de Azure Machine Learning. El módulo se puede usar para una variedad de tareas que abarcan desde la exploración de datos al procesamiento previo y la extracción de características y, luego, a la evaluación y el procesamiento posterior de los resultados. El tiempo de ejecución de backend que se usa para la ejecución se basa en Anaconda, una distribución de Python comprobada y ampliamente usada. Este back-end facilita que los usuarios incorporen recursos de código existentes a la nube.

Esperamos proporcionar una funcionalidad adicional al módulo [Ejecutar script de Python][execute-python-script], como la capacidad de entrenar y hacer operativos los modelos en Python y de agregar una mejor compatibilidad para el desarrollo y código de depuración en Azure Machine Learning Studio.

## <a name="next-steps"></a>Pasos siguientes
Para más información, vea el [Centro para desarrolladores de Python](https://azure.microsoft.com/develop/python/).

<!-- Module References -->
[execute-python-script]: https://msdn.microsoft.com/library/azure/cdb56f95-7f4c-404d-bde7-5bb972e6f232/
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
