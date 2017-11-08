---
title: "Destinos o salidas de ejemplo posibles con preparación de datos de Azure Machine Learning | Microsoft Docs"
description: "En este documento se proporciona un conjunto de ejemplos de destinos o salidas de datos personalizados con preparación de datos de Azure Machine Learning"
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
ms.openlocfilehash: ca9e7d8c318100ba498ee15be15e213905f1e8d2
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/31/2017
---
# <a name="sample-of-destination-connections-python"></a>Ejemplo de conexiones de destino (Python) 
Antes de leer este apéndice, vea la [introducción a la extensibilidad de Python](data-prep-python-extensibility-overview.md).


## <a name="write-to-excel"></a>Escribir en Excel 


Para escribir en Excel se requiere una biblioteca adicional. La forma de agregar nuevas bibliotecas se documenta en la introducción a la extensibilidad. `openpyxl` es la biblioteca que debe agregar.

Antes de escribir en Excel, es posible que se deban efectuar otros cambios. Algunos de los tipos de datos usados en la preparación de datos no se admiten en algunos formatos de destino. Por ejemplo, si existen objetos "Error", estos no se serializarán correctamente en Excel. Por lo tanto, antes de intentar escribir en Excel, se debe efectuar una transformación "Reemplazar valores de error", que quita los errores de todas las columnas.

Si se han llevado a cabo todas las tareas anteriores, la línea siguiente escribe la tabla de datos en una sola hoja de un documento de Excel. Agregue una transformación de escritura de flujo de datos (Script). A continuación, escriba el código siguiente en una sección de expresión.


### <a name="on-windows"></a>En Windows 
```python
df.to_excel('c:\dev\data\Output\Customer.xlsx', sheet_name='Sheet1')
```

### <a name="on-macosos-x"></a>En macOS/OS X ###
```python
df.to_excel('c:/dev/data/Output/Customer.xlsx', sheet_name='Sheet1')
```
