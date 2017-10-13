---
title: "Transformaciones de flujo de datos de ejemplo posibles con Preparación de datos de Azure Machine Learning | Microsoft Docs"
description: "En este documento se proporciona un conjunto de ejemplos de transformaciones de flujo de datos posibles con Preparación de datos de Azure ML"
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: 
ms.service: machine-learning
ms.workload: data-services
ms.custom: 
ms.devlang: 
ms.topic: article
ms.date: 09/11/2017
ms.openlocfilehash: f43f65ca89349fc790684e9bd7acd2f19e15abe5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="sample-of-custom-data-flow-transforms-python"></a>Ejemplo de transformaciones de flujo de datos personalizados (Python) 
El nombre de esta transformación en el menú es "Transformar flujo de datos (script)". Antes de leer este apéndice, vea la [introducción a la extensibilidad de Python](data-prep-python-extensibility-overview.md)

## <a name="transform-frame"></a>Transformación de marco
### <a name="create-a-new-column-dynamically"></a>Crear una columna de forma dinámica 
Crea una columna de forma dinámica (city2) y reconcilia distintas versiones de San Francisco a una de la columna City (Ciudad) existente.
```python
    df.loc[(df['city'] == 'San Francisco') | (df['city'] == 'SF') | (df['city'] == 'S.F.') | (df['city'] == 'SAN FRANCISCO'), 'city2'] = 'San Francisco'
```

### <a name="add-new-aggregates"></a>Agregar nuevos agregados
Crea un marco con el primer y último agregados calculados para la columna Score (Puntuación) agrupada por la columna risk_category (categoría_riesgo)
```python
    df = df.groupby(['risk_category'])['Score'].agg(['first','last'])
```
### <a name="winsorize-a-column"></a>Winsorizar una columna 
Reformula los datos para cumplir una fórmula para reducir los valores atípicos de una columna
```python
    import scipy.stats as stats
    df['Last Order'] = stats.mstats.winsorize(df['Last Order'].values, limits=0.4)
```

## <a name="transform-data-flow"></a>Transformación del flujo de datos
### <a name="fill-down"></a>Rellenar hacia abajo 
"Rellenar hacia abajo" requiere dos transformaciones.
Si tenemos unos datos como estos:


|Estado         |City       |
|--------------|-----------|
|Washington    |Redmond    |
|              |Bellevue   |
|              |Issaquah   |
|              |Seattle    |
|California    |Los Ángeles|
|              |San Diego  |
|              |San José   |
|Texas         |Dallas     |
|              |San Antonio|
|              |Houston    |

En primer lugar, cree una transformación "Agregar columna (script)" que contenga el siguiente código:
```python
    row['State'] if len(row['State']) > 0 else None
```
Luego, cree una transformación "Transformar flujo de datos (script)" que contenga el siguiente código:
```python
    df = df.fillna( method='pad')
```

Los datos ahora tienen un aspecto similar al siguiente:

|Estado         |newState         |City       |
|--------------|--------------|-----------|
|Washington    |Washington    |Redmond    |
|              |Washington    |Bellevue   |
|              |Washington    |Issaquah   |
|              |Washington    |Seattle    |
|California    |California    |Los Ángeles|
|              |California    |San Diego  |
|              |California    |San José   |
|Texas         |Texas         |Dallas     |
|              |Texas         |San Antonio|
|              |Texas         |Houston    |

