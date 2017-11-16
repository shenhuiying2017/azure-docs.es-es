---
title: Acceso a conjuntos de datos con la biblioteca de cliente de Python de Machine Learning | Microsoft Docs
description: Instale y use la biblioteca de cliente de Python para tener acceso y administrar datos de Azure Machine Learning de forma segura desde un entorno local de Python.
services: machine-learning
documentationcenter: python
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: 9ab42272-c30c-4b7e-8e66-d64eafef22d0
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2017
ms.author: huvalo;bradsev
ms.openlocfilehash: 3cffb90baadef570bbb7e4e2d96741cc0420b0a2
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/14/2017
---
# <a name="access-datasets-with-python-using-the-azure-machine-learning-python-client-library"></a>Acceso a conjuntos de datos con Python mediante la biblioteca de cliente de Python de Azure Machine Learning
La versión preliminar de la biblioteca de cliente de Python de Microsoft Azure Machine Learning puede permitir un acceso seguro a los conjuntos de datos de Azure Machine Learning desde un entorno local de Python, así como la creación y administración de conjuntos de datos en un área de trabajo.

Este tema proporciona instrucciones sobre cómo realizar las siguientes acciones:

* instalar la biblioteca de cliente de Python de Machine Learning; 
* obtener acceso y cargar conjuntos de datos, con instrucciones sobre cómo obtener autorización para el acceso a conjuntos de datos de Azure Machine Learning desde el entorno local de Python;
* obtener acceso a los conjuntos de datos intermedios de experimentos;
* usar la biblioteca de cliente de Python para enumerar conjuntos de datos, obtener acceso a los metadatos, leer el contenido de un conjunto de datos, crear nuevos conjuntos de datos y actualizar conjuntos de datos existentes.

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="prerequisites"></a>Requisitos previos
La biblioteca de cliente de Python se ha probado en los siguientes entornos:

* Windows, Mac y Linux
* Python 2.7, 3.3 y 3.4

Tiene una dependencia en los siguientes paquetes:

* Solicitudes
* Python-dateutil
* Pandas

Se recomienda utilizar una distribución de Python como [Anaconda](http://continuum.io/downloads#all) o [Canopy](https://store.enthought.com/downloads/), incluidas con Python, IPython y los tres paquetes instalados enumerados anteriormente. Aunque IPython no es estrictamente necesario, es un excelente entorno para manipular y visualizar datos de forma interactiva.

### <a name="installation"></a>Cómo instalar la biblioteca de cliente de Python de Azure Machine Learning
La biblioteca cliente de Python de Azure Machine Learning también debe instalarse para completar las tareas descritas en este tema. Está disponible desde el [Índice de paquetes de Python](https://pypi.python.org/pypi/azureml). Para instalarlo en su entorno de Python, ejecute el siguiente comando desde el entorno de Python local:

    pip install azureml

Como alternativa, puede descargar e instalar desde los orígenes de [github](https://github.com/Azure/Azure-MachineLearning-ClientLibrary-Python).

    python setup.py install

Si tiene git instalado en su equipo, puede usar pip para instalar directamente desde el repositorio de git:

    pip install git+https://github.com/Azure/Azure-MachineLearning-ClientLibrary-Python.git


## <a name="datasetAccess"></a>Utilizar fragmentos de código del Estudio para tener acceso a los conjuntos de datos
La biblioteca de cliente de Python proporciona acceso mediante programación a los conjuntos de datos existentes de los experimentos que se han ejecutado.

Desde la interfaz de web del Estudio, puede generar fragmentos de código que incluyen toda la información necesaria para descargar y deserializar los conjuntos de datos como objetos Pandas DataFrame en el equipo de la ubicación.

### <a name="security"></a>Seguridad de acceso a datos
Los fragmentos de código proporcionados por el Estudio para su uso con la biblioteca de cliente de Python incluyen el identificador de área de trabajo y el token de autorización. Estos proporcionan acceso completo a su área de trabajo y se deben proteger, como una contraseña.

Por motivos de seguridad, la funcionalidad de fragmento de código solo está disponible para los usuarios que tengan su rol definido como **Propietario** para el área de trabajo. Su rol se muestra en la página **USUARIOS** de Azure Machine Learning Studio, en **Configuración**.

![Seguridad][security]

Si su rol no está establecido como **Propietario**, puede solicitar que se le vuelva a invitar como propietario o pedir al propietario del área de trabajo que le proporcione el fragmento de código.

Para obtener el token de autorización, puede realizar una de las acciones siguientes:

* Solicite un token a un propietario. Los propietarios pueden tener acceso a sus tokens de autorización en la página Configuración de su área de trabajo en el Estudio. Seleccione **Configuración** en el panel izquierdo y haga clic en **TOKENS DE AUTORIZACIÓN** para ver los tokens primarios y secundarios.  Aunque se pueden utilizar los tokens de autorización principales y secundarios, se recomienda que los propietarios solo compartan los tokens de autorización secundarios.

![Tokens de autorización](./media/python-data-access/ml-python-access-settings-tokens.png)

* Pida que le amplíen al rol de propietario.  Para ello, un propietario actual del área de trabajo debe quitarle primero del área de trabajo y, a continuación, volver a invitarle como propietario.

Cuando los desarrolladores hayan obtenido el identificador de área de trabajo y el token de autorización, podrán acceder al área de trabajo usando el fragmento de código independientemente de su rol.

Los tokens de autorización se administran en la página **TOKENS DE AUTORIZACIÓN**, en **CONFIGURACIÓN**. Puede volver a generarlos, pero este procedimiento revoca el acceso al token anterior.

### <a name="accessingDatasets"></a>Obtener acceso a los conjuntos de datos desde una aplicación local de Python
1. En Machine Learning Studio, haga clic en la opción **CONJUNTOS DE DATOS** de la barra de navegación de la izquierda.
2. Seleccione el conjunto de datos al que le gustaría tener acceso. Puede seleccionar cualquiera de los conjuntos de datos de las listas **MIS CONJUNTOS DE DATOS** o **EJEMPLOS**.
3. En la barra de herramientas de la parte inferior, haga clic en **Generate Data Access Code**(Generar código de acceso a datos). Este botón se deshabilitará si los datos están en un formato no compatible con la biblioteca cliente de Python.
   
    ![CONJUNTOS DE DATOS][datasets]
4. Seleccione el fragmento de código de la ventana que aparece y cópielo al Portapapeles.
   
    ![Código de acceso][dataset-access-code]
5. Pegue el código en el Bloc de notas de su aplicación local de Python.
   
    ![Bloc de notas][ipython-dataset]

## <a name="accessingIntermediateDatasets"></a>Obtener acceso a los conjuntos de datos intermedios de experimentos de Machine Learning
Después de ejecutar un experimento en Machine Learning Studio, es posible tener acceso a los conjuntos de datos intermedios desde los nodos de salida de los módulos. Los conjuntos de datos intermedios son datos que se han creado y utilizado para pasos intermedios cuando se ha ejecutado una herramienta de modelo.

El acceso a los conjuntos de datos intermedios es posible siempre que el formato de los datos sea compatible con la biblioteca de cliente de Python.

Se admiten los siguientes formatos (sus constantes están en la clase `azureml.DataTypeIds` ):

* PlainText
* GenericCSV
* GenericTSV
* GenericCSVNoHeader
* GenericTSVNoHeader

Puede determinar el formato desplazando el mouse sobre un nodo de salida del módulo. Aparecerá junto al nombre del nodo, en la información sobre herramientas.

Algunos de los módulos, como el de [División][split], dan como resultado un formato denominado `Dataset`, que no es compatible con la biblioteca de cliente de Python.

![Formato de conjunto de datos][dataset-format]

Necesitará usar un módulo de conversión, como [Convertir en CSV][convert-to-csv], para obtener un resultado en un formato compatible.

![Formato GenericCSV][csv-format]

Los pasos siguientes muestran un ejemplo que crea un experimento, lo ejecuta y tiene acceso al conjunto de datos intermedio.

1. Cree un experimento nuevo.
2. Inserte un módulo **Conjunto de datos de clasificación binaria de ingresos en el censo de adultos** .
3. Inserte un módulo [División][split] y conecte su entrada a la salida del módulo del conjunto de datos.
4. Inserte un módulo [Convertir a CSV][convert-to-csv] y conecte su entrada a una de las salidas del módulo [División][split].
5. Guarde el experimento, ejecútelo y espere a que finalice su ejecución.
6. Haga clic en el nodo de salida del módulo [Convertir en CSV][convert-to-csv].
7. Cuando aparezca el menú contextual, seleccione **Generar código de acceso a datos**.
   
    ![Menú contextual][experiment]
8. Seleccione el fragmento de código y cópielo en la ventana que se muestra.
   
    ![Código de acceso][intermediate-dataset-access-code]
9. Pegue el código en el Bloc de notas.
   
    ![Bloc de notas][ipython-intermediate-dataset]
10. Puede visualizar los datos con matplotlib. Esto se muestra en un histograma de la columna de tiempo:
    
    ![Histograma][ipython-histogram]

## <a name="clientApis"></a>Use la biblioteca de cliente de Python de Machine Learning para obtener acceso, leer, crear y administrar conjuntos de datos.
### <a name="workspace"></a>Área de trabajo
El área de trabajo es el punto de entrada para la biblioteca de cliente de Python. Proporcione la clase `Workspace` con su id. de área de trabajo y token de autorización para crear una instancia:

    ws = Workspace(workspace_id='4c29e1adeba2e5a7cbeb0e4f4adfb4df',
                   authorization_token='f4f3ade2c6aefdb1afb043cd8bcf3daf')


### <a name="enumerate-datasets"></a>Enumerar los conjuntos de datos
Para enumerar todos los conjuntos de datos en un área determinado:

    for ds in ws.datasets:
        print(ds.name)

Para enumerar solo los conjuntos de datos creados por el usuario:

    for ds in ws.user_datasets:
        print(ds.name)

Para enumerar solo los conjuntos de datos de ejemplo:

    for ds in ws.example_datasets:
        print(ds.name)

Puede tener acceso a un conjunto de datos por nombre (que distingue mayúsculas de minúsculas):

    ds = ws.datasets['my dataset name']

O bien, puede tener acceso a él por su índice:

    ds = ws.datasets[0]


### <a name="metadata"></a>Metadatos
Los conjuntos de datos tienen metadatos, además de contenido. (Los conjuntos de datos intermedios son una excepción a esta regla y no tienen metadatos).

Algunos valores de metadatos son asignados por el usuario en tiempo de creación:

    print(ds.name)
    print(ds.description)
    print(ds.family_id)
    print(ds.data_type_id)

Los demás son valores asignados por el Aprendizaje automático de Azure:

    print(ds.id)
    print(ds.created_date)
    print(ds.size)

Vea la clase `SourceDataset` para obtener más información sobre los metadatos disponibles.

### <a name="read-contents"></a>Leer contenido
Los fragmentos de código que proporciona Machine Learning Studio descargan y deserializan automáticamente el conjunto de datos a un objeto Pandas DataFrame. Esto se hace en el método `to_dataframe` :

    frame = ds.to_dataframe()

Si prefiere descargar los datos sin procesar y realizar la deserialización usted mismo, tiene esa opción. En este momento, esta es la única opción para formatos como 'ARFF', que la biblioteca de cliente de Python no puede deserializar.

Para leer el contenido como texto:

    text_data = ds.read_as_text()

Para leer el contenido como binario:

    binary_data = ds.read_as_binary()

También puede abrir una secuencia para el contenido:

    with ds.open() as file:
        binary_data_chunk = file.read(1000)


### <a name="create-a-new-dataset"></a>Crear un conjunto de datos nuevo
La biblioteca cliente de Python permite cargar conjuntos de datos desde el programa de Python. Estos conjuntos de datos estarán disponibles para utilizarse en el área de trabajo.

Si tiene sus datos en un Pandas DataFrame, utilice el siguiente código:

    from azureml import DataTypeIds

    dataset = ws.datasets.add_from_dataframe(
        dataframe=frame,
        data_type_id=DataTypeIds.GenericCSV,
        name='my new dataset',
        description='my description'
    )

Si sus datos ya están serializados, puede utilizar:

    from azureml import DataTypeIds

    dataset = ws.datasets.add_from_raw_data(
        raw_data=raw_data,
        data_type_id=DataTypeIds.GenericCSV,
        name='my new dataset',
        description='my description'
    )

La biblioteca cliente de Python puede serializar una trama de datos de Pandas en los siguientes formatos (sus constantes se encuentran en la clase `azureml.DataTypeIds` ):

* PlainText
* GenericCSV
* GenericTSV
* GenericCSVNoHeader
* GenericTSVNoHeader

### <a name="update-an-existing-dataset"></a>Actualizar un registro existente
Si trata de cargar un nuevo conjunto de datos con un nombre que coincida con un conjunto de datos existente, debería recibir un error de conflicto.

Para actualizar un conjunto de datos existente, primero debe obtener una referencia al conjunto de datos existente:

    dataset = ws.datasets['existing dataset']

    print(dataset.data_type_id) # 'GenericCSV'
    print(dataset.name)         # 'existing dataset'
    print(dataset.description)  # 'data up to jan 2015'

A continuación, utilice `update_from_dataframe` para serializar y reemplazar el contenido del conjunto de datos en Azure:

    dataset = ws.datasets['existing dataset']

    dataset.update_from_dataframe(frame2)

    print(dataset.data_type_id) # 'GenericCSV'
    print(dataset.name)         # 'existing dataset'
    print(dataset.description)  # 'data up to jan 2015'

Si desea serializar los datos a un formato diferente, especifique un valor para el parámetro opcional `data_type_id` .

    from azureml import DataTypeIds

    dataset = ws.datasets['existing dataset']

    dataset.update_from_dataframe(
        dataframe=frame2,
        data_type_id=DataTypeIds.GenericTSV,
    )

    print(dataset.data_type_id) # 'GenericTSV'
    print(dataset.name)         # 'existing dataset'
    print(dataset.description)  # 'data up to jan 2015'

Opcionalmente, puede definir una nueva descripción especificando un valor para el parámetro `description` .

    dataset = ws.datasets['existing dataset']

    dataset.update_from_dataframe(
        dataframe=frame2,
        description='data up to feb 2015',
    )

    print(dataset.data_type_id) # 'GenericCSV'
    print(dataset.name)         # 'existing dataset'
    print(dataset.description)  # 'data up to feb 2015'

Opcionalmente, puede definir un nuevo nombre especificando un valor para el parámetro `name` . De ahora en adelante, solo podrá recuperar el conjunto de datos con el nuevo nombre. El siguiente código actualiza los datos, el nombre y la descripción.

    dataset = ws.datasets['existing dataset']

    dataset.update_from_dataframe(
        dataframe=frame2,
        name='existing dataset v2',
        description='data up to feb 2015',
    )

    print(dataset.data_type_id)                    # 'GenericCSV'
    print(dataset.name)                            # 'existing dataset v2'
    print(dataset.description)                     # 'data up to feb 2015'

    print(ws.datasets['existing dataset v2'].name) # 'existing dataset v2'
    print(ws.datasets['existing dataset'].name)    # IndexError

Los parámetros `data_type_id`, `name` y `description` son opcionales y tienen el valor anterior como predeterminado. El parámetro `dataframe` siempre es obligatorio.

Si sus datos ya están serializados, puede utilizar`update_from_raw_data` en lugar de `update_from_dataframe`. Funcionará de una forma similar si pasa `raw_data` en lugar de `dataframe`.

<!-- Images -->
[security]:./media/python-data-access/security.png
[dataset-format]:./media/python-data-access/dataset-format.png
[csv-format]:./media/python-data-access/csv-format.png
[datasets]:./media/python-data-access/datasets.png
[dataset-access-code]:./media/python-data-access/dataset-access-code.png
[ipython-dataset]:./media/python-data-access/ipython-dataset.png
[experiment]:./media/python-data-access/experiment.png
[intermediate-dataset-access-code]:./media/python-data-access/intermediate-dataset-access-code.png
[ipython-intermediate-dataset]:./media/python-data-access/ipython-intermediate-dataset.png
[ipython-histogram]:./media/python-data-access/ipython-histogram.png


<!-- Module References -->
[convert-to-csv]: https://msdn.microsoft.com/library/azure/faa6ba63-383c-4086-ba58-7abf26b85814/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/

