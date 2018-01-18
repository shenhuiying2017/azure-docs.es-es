---
title: "Usar la extensibilidad de Python con Preparación de datos de Azure Machine Learning | Microsoft Docs"
description: "En este documento se proporcionan información general y algunos ejemplos detallados sobre cómo usar el código de Python para ampliar la funcionalidad de la preparación de datos"
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: 
ms.devlang: 
ms.topic: article
ms.date: 09/07/2017
ms.openlocfilehash: 3c3864480d2fcba4f6d388d4e0d00b917cb62d2b
ms.sourcegitcommit: df4ddc55b42b593f165d56531f591fdb1e689686
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/04/2018
---
# <a name="data-preparations-python-extensions"></a>Extensiones de Python para la preparación de datos
Como método para completar vacíos de funcionalidad entre características integradas, Preparación de datos de Azure Machine Learning incluye una extensibilidad en varios niveles. En este documento se indica la extensibilidad mediante un script de Python. 

## <a name="custom-code-steps"></a>Pasos del código personalizado 
Preparación de datos tiene los siguientes pasos personalizados, en los que los usuarios pueden escribir código:

* Lector de archivos*
* Escritor*
* Agregar columna
* Filtro avanzado
* Transformar flujo de datos
* Transformar partición

*Estos pasos no se admiten actualmente en una ejecución de Spark.

## <a name="code-block-types"></a>Tipos de bloques de código 
Para cada uno de estos pasos se admiten dos tipos de bloques de código. En primer lugar, se admite una expresión básica de Python que se ejecuta tal cual. En segundo lugar, se admite un módulo de Python donde se llama a una determinada función con una firma conocida en el código proporcionado.

Por ejemplo, puede agregar una nueva columna que calcule el registro de otra columna de las dos maneras siguientes:

Expression 

```python    
    math.log(row["Score"])
```

Módulo 
    
```python
def newvalue(row): 
        return math.log(row["Score"])
```


La transformación Agregar columna en el modo del módulo espera encontrar una función denominada `newvalue` que acepte una variable de fila y devuelva el valor de la columna. Este módulo puede incluir cualquier código de Python con otras funciones, importaciones, etc.

Los detalles de cada punto de extensión se tratan en las secciones siguientes. 

## <a name="imports"></a>Importaciones 
Si usa el tipo de bloque Expresión, puede seguir agregando instrucciones **import** al código. Todas ellas se deben agrupar en las líneas superiores del código.

Correcto 

```python
import math 
import numpy 
math.log(row["Score"])
```
 

Error  

```python
import math  
math.log(row["Score"])  
import numpy
```
 
 
Si usa el tipo de bloque Módulo, puede seguir todas las reglas normales de Python para usar la instrucción **import**. 

## <a name="default-imports"></a>Importaciones predeterminadas
Las siguientes importaciones siempre se incluyen y se pueden usar en el código. No es necesario volver a importarlas. 

```python
import math  
import numbers  
import datetime  
import re  
import pandas as pd  
import numpy as np  
import scipy as sp
```
  

## <a name="install-new-packages"></a>Instalar nuevos paquetes
Para usar un paquete que no está instalado de forma predeterminada, primero debe instalarlo en los entornos usados por Preparación de datos. Esta instalación se debe llevar a cabo tanto en el equipo local como en cualquier destino de proceso en el que quiera ejecutarla.

Para instalar los paquetes en un destino de proceso, tendrá que modificar el archivo conda_dependencies.yml, ubicado en la carpeta aml_config, en la raíz del proyecto.

### <a name="windows"></a>Windows 
Para buscar la ubicación en Windows, busque la instalación específica de la aplicación de Python y su directorio de scripts. La ubicación predeterminada es:  

`C:\Users\<user>\AppData\Local\AmlWorkbench\Python\Scripts.` 

Luego, ejecute cualquiera de los siguientes comandos: 

`conda install <libraryname>` 

o 

`pip install <libraryname> `

### <a name="mac"></a>Mac 
Para buscar la ubicación en Mac, busque la instalación específica de la aplicación de Python y su directorio de scripts. La ubicación predeterminada es: 

`/Users/<user>/Library/Caches/AmlWorkbench>/Python/bin` 

Luego, ejecute cualquiera de los siguientes comandos: 

`./conda install <libraryname>`

o 

`./pip install <libraryname>`

## <a name="use-custom-modules"></a>Uso de módulos personalizados
En Transformar flujo de datos (script), escriba código Python como el siguiente:

```python
import sys
sys.path.append(*<absolute path to the directory containing UserModule.py>*)

from UserModule import ExtensionFunction1
df = ExtensionFunction1(df)
```

En Agregar columna (script), establezca Code Block Type = Module y escriba el código Python siguiente:

```python 
import sys
sys.path.append(*<absolute path to the directory containing UserModule.py>*)

from UserModule import ExtensionFunction2

def newvalue(row):
    return ExtensionFunction2(row)
```
En el caso de contextos de ejecución distintos (local, docker spark), dirija la ruta de acceso absoluta al lugar correcto. Es posible que desee usar "os.getcwd() + relativePath" para localizarlo.


## <a name="column-data"></a>Datos de columna 
Se puede obtener acceso a los datos de columna desde una fila usando la notación de puntos o la notación de clave-valor. No se puede obtener acceso a los nombres de las columnas que contienen espacios o caracteres especiales mediante la notación de puntos. La variable `row` siempre debe definirse en los dos modos de las extensiones de Python (Módulo y Expresión). 

Ejemplos 

```python
    row.ColumnA + row.ColumnB  
    row["ColumnA"] + row["ColumnB"]
```

## <a name="file-reader"></a>Lector de archivos 
### <a name="purpose"></a>Propósito 
El punto de extensión Lector de archivos permite controlar completamente el proceso de lectura de un archivo en un flujo de datos. El sistema llama al código y pasa la lista de archivos que debe procesar. Es necesario que el código cree y devuelva una trama de datos de Pandas. 

>[!NOTE]
>Este punto de extensión no funciona en Spark. 


### <a name="how-to-use"></a>Modo de uso 
A este punto de extensión se obtiene acceso desde el asistente **Abrir origen de datos**. Seleccione **File** (Archivo) en la primera página y, luego, elija la ubicación del archivo. En la página **Choose File Parameters** (Seleccionar parámetros de archivo), en la lista desplegable **File Type** (Tipo de archivo), seleccione **Custom File (Script)** (Archivo personalizado (script)). 

El código obtiene una trama de datos de Pandas denominada "df" que contiene información sobre los archivos que debe leer. Si decide abrir un directorio con varios archivos, la trama de datos contendrá más de una fila.  

Esta trama de datos tiene las columnas siguientes:

- Ruta de acceso: Archivo que se va a leer.
- PathHint: Le indica dónde se encuentra el archivo. Valores: Local, AzureBlobStorage y AzureDataLakeStorage.
- AuthenticationType: Tipo de autenticación usado para obtener acceso al archivo. Valores: None, SasToken y OAuthToken.
- AuthenticationValue: Contiene None (Ninguno) o el token que se va a usar.

### <a name="syntax"></a>Sintaxis 
Expression 

```python
    paths = df['Path'].tolist()  
    df = pd.read_csv(paths[0])
```


Módulo  
```python
PathHint = Local  
def read(df):  
    paths = df['Path'].tolist()  
    filedf = pd.read_csv(paths[0])  
    return filedf  
```
 

## <a name="writer"></a>Escritor 
### <a name="purpose"></a>Propósito 
El punto de extensión Escritor le permite controlar completamente el proceso de escritura de datos desde un flujo de datos. El sistema llama al código y pasa una trama de datos. El código puede usar la trama de datos para escribir datos como quiera. 

>[!NOTE]
>El punto de extensión Escritor no funciona en Spark.


### <a name="how-to-use"></a>Modo de uso 
Puede agregar este punto de extensión mediante el bloque Escribir flujo de datos (script). Está disponible en el menú **Transformations** (Transformaciones) de nivel superior.

### <a name="syntax"></a>Sintaxis 
Expression

```python
    df.to_csv('c:\\temp\\output.csv')
```

Módulo

```python
def write(df):  
    df.to_csv('c:\\temp\\output.csv')  
    return df
```
 
 
Este bloque de escritura personalizado puede existir en medio de una lista de pasos. Si usa un módulo, la función de escritura debe devolver la trama de datos que es la entrada al paso siguiente. 

## <a name="add-column"></a>Agregar columna 
### <a name="purpose"></a>Propósito
El punto de extensión Agregar columna permite escribir Python para calcular una columna nueva. El código que escriba tiene acceso a la fila completa. Debe devolver un nuevo valor de columna para cada fila. 

### <a name="how-to-use"></a>Modo de uso
Puede agregar este punto de extensión mediante el bloque Agregar columna (script). Está disponible en el menú **Transformations** (Transformaciones) de nivel superior, así como en el menú contextual de **columna**. 

### <a name="syntax"></a>Sintaxis
Expression

```python
    math.log(row["Score"])
```

Módulo 

```python
def newvalue(row):  
     return math.log(row["Score"])
```
 

## <a name="advanced-filter"></a>Filtro avanzado
### <a name="purpose"></a>Propósito 
El punto de extensión Filtro avanzado permite escribir un filtro personalizado. Tiene acceso a toda la fila y el código debe devolver True (incluir la fila) o False (excluir la fila). 

### <a name="how-to-use"></a>Modo de uso
Puede agregar este punto de extensión mediante el bloque Filtro avanzado (script). Está disponible en el menú **Transformations** (Transformaciones) de nivel superior. 

### <a name="syntax"></a>Sintaxis

Expression

```python
    row["Score"] > 95
```

Módulo  

```python
def includerow(row):  
    return row["Score"] > 95
```
 

## <a name="transform-dataflow"></a>Transformar flujo de datos
### <a name="purpose"></a>Propósito 
El punto de extensión Transformar flujo de datos permite transformar completamente el flujo de datos. Tiene acceso a una trama de datos de Pandas que contiene todas las columnas y filas que procesa. El código debe devolver una trama de datos de Pandas con los nuevos datos. 

>[!NOTE]
>En Python, se cargarán todos los datos en memoria en una trama de datos de Pandas si se usa esta extensión. 
>
>En Spark, todos los datos se recopilan en un solo nodo de trabajo. Si los datos son muy grandes, es posible que un trabajador no disponga de memoria suficiente. Úselo con precaución.

### <a name="how-to-use"></a>Modo de uso 
Puede agregar este punto de extensión mediante el bloque Transformar flujo de datos (script). Está disponible en el menú **Transformations** (Transformaciones) de nivel superior. 
### <a name="syntax"></a>Sintaxis 

Expression

```python
    df['index-column'] = range(1, len(df) + 1)  
    df = df.reset_index()
```
 

Módulo 

```python
def transform(df):  
    df['index-column'] = range(1, len(df) + 1)  
    df = df.reset_index()  
    return df
```
  

## <a name="transform-partition"></a>Transformar partición  
### <a name="purpose"></a>Propósito 
El punto de extensión Transformar partición permite transformar una partición del flujo de datos. Tiene acceso a una trama de datos de Pandas que contiene todas las columnas y filas de esa partición. El código debe devolver una trama de datos de Pandas con los nuevos datos. 

>[!NOTE]
>En Python puede acabar con una o varias particiones, según el tamaño de los datos. En Spark, trabaja con una trama de datos que contiene los datos de una partición en un nodo de trabajo determinado. En ambos casos no puede dar por hecho que tiene acceso a todo el conjunto de datos. 


### <a name="how-to-use"></a>Modo de uso
Puede agregar este punto de extensión mediante el bloque Transformar partición (script). Está disponible en el menú **Transformations** (Transformaciones) de nivel superior. 

### <a name="syntax"></a>Sintaxis 

Expression 

```python
    df['partition-id'] = index  
    df['index-column'] = range(1, len(df) + 1)  
    df = df.reset_index()
```
 

Módulo 

```python
def transform(df, index):
    df['partition-id'] = index
    df['index-column'] = range(1, len(df) + 1)
    df = df.reset_index()
    return df
```


## <a name="datapreperror"></a>DataPrepError  
### <a name="error-values"></a>Valores de error  
En Preparación de datos existe el concepto de valores de error. 

Se pueden detectar valores de error en el código personalizado de Python. Son instancias de una clase de Python denominada `DataPrepError`. Esta clase encapsula una excepción de Python y tiene un par de propiedades. Las propiedades contienen información sobre el error que se produjo al procesarse el valor original, así como sobre este último. 


### <a name="datapreperror-class-definition"></a>Definición de clase DataPrepError
```python 
class DataPrepError(Exception): 
    def __bool__(self): 
        return False 
``` 
La creación de una clase DataPrepError en el marco de Python de Preparación de datos suele tener este aspecto: 
```python 
DataPrepError({ 
   'message':'Cannot convert to numeric value', 
   'originalValue': value, 
   'exceptionMessage': e.args[0], 
   '__errorCode__':'Microsoft.DPrep.ErrorValues.InvalidNumericType' 
}) 
``` 
#### <a name="how-to-use"></a>Modo de uso 
Cuando Python se ejecuta en un punto de extensión, es posible generar clases DataPrepErrors como valores devueltos mediante el método de creación anterior. Es mucho más probable que los errores DataPrepErrors se detecten al procesarse los datos en un punto de extensión. En este momento, el código personalizado de Python necesita tratar DataPrepError como un tipo de datos válido.

#### <a name="syntax"></a>Sintaxis 
Expression 
```python 
    if (isinstance(row["Score"], DataPrepError)): 
        row["Score"].originalValue 
    else: 
        row["Score"] 
``` 
```python 
    if (hasattr(row["Score"], "originalValue")): 
        row["Score"].originalValue 
    else: 
        row["Score"] 
``` 
Módulo 
```python 
def newvalue(row): 
    if (isinstance(row["Score"], DataPrepError)): 
        return row["Score"].originalValue 
    else: 
        return row["Score"] 
``` 
```python 
def newvalue(row): 
    if (hasattr(row["Score"], "originalValue")): 
        return row["Score"].originalValue 
    else: 
        return row["Score"] 
```  
