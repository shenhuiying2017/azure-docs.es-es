---
title: "Expresiones de filtro de ejemplo posibles con la preparación de datos de Azure Machine Learning | Microsoft Docs"
description: "En este documento se proporciona un conjunto de ejemplos de expresiones de filtro posibles con la preparación de datos de Azure Machine Learning"
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
ms.date: 09/11/2017
ms.openlocfilehash: ae70f5981c7ca533740968e0eea1b0b83df414fa
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/18/2017
---
# <a name="sample-of-filter-expressions-python"></a>Ejemplo de expresiones de filtro (Python) 
Antes de leer este apéndice, vea la [Introducción a la extensibilidad de Python](data-prep-python-extensibility-overview.md).

## <a name="filter-with-equivalence-test"></a>Filtrar con prueba de equivalencia
Filtre solo en aquellas filas en las que el valor de Col2 (numérico) sea mayor que 4. 

```python
    row["Col2"] > 4
```

## <a name="filter-with-multiple-columns"></a>Filtrar con varias columnas 
Filtre solo en aquellas filas en las que Col1 contenga el valor **Good** y Col2 contiene el valor 1 (numérico). 
```python
    row["Col1"] == 'Good' and row["Col2"] == 1
```

## <a name="test-filter-against-null"></a>Filtro de prueba con valores null
Filtre solo en aquellas filas en las que Col1 tenga un valor null. 
```python
    pd.isnull(row["Col1"])
```
