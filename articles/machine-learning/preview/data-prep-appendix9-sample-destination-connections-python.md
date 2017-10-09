---
title: "Destinos/salidas de ejemplo posibles con Preparación de datos de Azure Machine Learning | Microsoft Docs"
description: "En este documento se proporciona un conjunto de ejemplos de destinos/salidas de datos personalizados con Preparación de datos de Azure ML"
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
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: e85ab5d52c2265536f72fa7301c0a25fa3a112cc
ms.contentlocale: es-es
ms.lasthandoff: 09/25/2017

---


# <a name="sample-of-destination-connections-python"></a>Ejemplo de conexiones de destino (Python) 
Antes de leer este apéndice, vea la [introducción a la extensibilidad de Python](data-prep-python-extensibility-overview.md).


### <a name="write-to-excel"></a>Escribir en Excel 


La escritura en Excel requiere una biblioteca adicional. La adición de nuevas bibliotecas se documenta en la introducción a la extensibilidad. `openpyxl` es la biblioteca que se debe agregar.

Antes de pasar a la escritura, es posible que se deban efectuar otros cambios. Algunos de los tipos de datos usados en Preparación de datos no son compatibles en algunos formatos de destino. Por ejemplo, si existen objetos "Error", estos no se serializarán correctamente en Excel. Por lo tanto, se debe efectuar una transformación "Reemplazar valores de error", que quita los errores de todas las columnas, antes de intentar escribir en Excel.

Suponiendo que se han llevado a cabo todas las tareas anteriores, la línea siguiente escribe la tabla de datos en una sola hoja de un documento de Excel. Agregue una transformación de escritura de flujo de datos (Script) y escriba el siguiente código en una sección de la expresión:


#### <a name="on-windows"></a>En Windows 
```python
df.to_excel('c:\dev\data\Output\Customer.xlsx', sheet_name='Sheet1')
```

#### <a name="on-macosos-x"></a>En macOS/OS X ###
```python
df.to_excel('c:/dev/data/Output/Customer.xlsx', sheet_name='Sheet1')
```

