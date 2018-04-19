---
title: "Ejemplo de Python para derivar nuevas columnas en la preparación de datos de Azure Machine Learning | Microsoft Docs"
description: "En este documento se proporcionan ejemplos de código de Python para crear nuevas columnas en la preparación de datos de Azure Machine Learning."
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: 
ms.devlang: 
ms.topic: article
ms.date: 02/01/2018
ms.openlocfilehash: 02670fa4506237ac391e467e5c7a1187b1131ece
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/12/2018
---
# <a name="sample-of-custom-column-transforms-python"></a>Ejemplo de transformaciones de columna personalizada (Python) 
El nombre de esta transformación en el menú es **Add Column (Script)** (Agregar columna [Script]).

Antes de leer este apéndice, vea la [Introducción a la extensibilidad de Python](data-prep-python-extensibility-overview.md).

## <a name="test-equivalence-and-replace-values"></a>Probar la equivalencia y reemplazar valores 
Si el valor de Col1 es inferior a 4, la nueva columna debe tener un valor de 1. Si el valor de Col1 es superior a 4, la nueva columna debe tener un valor de 2. 

```python
    1 if row["Col1"] < 4 else 2
```
## <a name="current-date-and-time"></a>Fecha y hora actual 

```python
    datetime.datetime.now()
```
## <a name="typecasting"></a>Conversión de tipo 
```python
    float(row["Col1"]) / float(row["Col2"] - 1)
```
## <a name="evaluate-for-nullness"></a>Evaluar los valores null 
Si Col1 contiene un valor null, marque la nueva columna como **Bad** (No válida). En caso contrario, márquela como **Good** (Válida). 

```python
    'Bad' if pd.isnull(row["Col1"]) else 'Good'
```
## <a name="new-computed-column"></a>Nueva columna calculada 
```python
    np.log(row["Col1"])
```
## <a name="epoch-computation"></a>Cálculo de tiempo 
Número de segundos transcurridos desde el tiempo Unix (suponiendo que Col1 ya sea una fecha): 
```python
    row["Col1"] - datetime.datetime.utcfromtimestamp(0)).total_seconds()
```

## <a name="hash-a-column-value-into-a-new-column"></a>Aplicación de un algoritmo hash de un valor de columna en una columna nueva
```python
    import hashlib
    hash(row["MyColumnToHashCol1"])

```




