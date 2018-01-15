---
title: "Guía de ajuste del rendimiento para usar PowerShell con Data Lake Store | Microsoft Docs"
description: Sugerencias para mejorar el rendimiento al usar Azure PowerShell con Data Lake Store
services: data-lake-store
documentationcenter: 
author: stewu
manager: jhubbard
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/09/2018
ms.author: stewu
ms.openlocfilehash: 63e1114d49b7bcb8910e8cd8205f10d1e8587f61
ms.sourcegitcommit: 9292e15fc80cc9df3e62731bafdcb0bb98c256e1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/10/2018
---
# <a name="performance-tuning-guidance-for-using-powershell-with-azure-data-lake-store"></a>Guía de ajuste del rendimiento para usar PowerShell con Azure Data Lake Store

En este artículo se enumeran las propiedades que se pueden ajustar para obtener un mejor rendimiento al usar PowerShell para trabajar con Data Lake Store:

## <a name="performance-related-properties"></a>Propiedades relacionadas con el rendimiento

| Propiedad            | Valor predeterminado | DESCRIPCIÓN |
|---------------------|---------|-------------|
| PerFileThreadCount  | 10      | Este parámetro permite elegir el número de subprocesos paralelos para cargar o descargar cada archivo. Este número representa el número máximo de subprocesos que se pueden asignar por archivo, pero, según el escenario, es posible que obtenga menos (por ejemplo, si carga un archivo de 1 KB, obtiene un subproceso, aunque solicite veinte).  |
| ConcurrentFileCount | 10      | Este parámetro es específico para cargar o descargar carpetas. Este parámetro determina el número de archivos simultáneos que se pueden cargar o descargar. Este número representa el número máximo de archivos simultáneos que se pueden cargar o descargar al mismo tiempo, pero, según el escenario, es posible que obtenga una menor simultaneidad (por ejemplo, si carga dos archivos, obtiene dos cargas de archivos simultáneos, aunque solicite quince). |

**Ejemplo**

Este comando descarga los archivos de Azure Data Lake Store en la unidad local del usuario con 20 subprocesos por archivo y 100 archivos simultáneos.

    Export-AzureRmDataLakeStoreItem -AccountName <Data Lake Store account name> -PerFileThreadCount 20-ConcurrentFileCount 100 -Path /Powershell/100GB/ -Destination C:\Performance\ -Force -Recurse

## <a name="how-do-i-determine-the-value-for-these-properties"></a>¿Cómo se determina el valor de estas propiedades?

La siguiente pregunta que puede tener es cómo se determina el valor que se proporciona para las propiedades relacionadas con el rendimiento. A continuación hay algunas instrucciones que puede usar.

* **Paso 1: Determinación del número total de subprocesos**: debe empezar por calcular el recuento total de subprocesos que se usará. Como norma general, debe usar seis subprocesos por cada núcleo físico.

        Total thread count = total physical cores * 6

    **Ejemplo**

    Suponga que está ejecutando los comandos de PowerShell desde una máquina virtual D14 que tiene 16 núcleos.

        Total thread count = 16 cores * 6 = 96 threads


* **Paso 2: Cálculo de PerFileThreadCount**: calculamos nuestro PerFileThreadCount en función del tamaño de los archivos. En los archivos menores de 2,5 GB, no hay necesidad de cambiar este parámetro, ya que el valor predeterminado (10) es suficiente. En los archivos mayores de 2,5 GB, debe usar diez subprocesos como base para los primeros 2,5 GB y agregar uno por cada aumento de 256 MB en el tamaño del archivo. Si está copiando una carpeta con muchos tamaños de archivo, considere la posibilidad de agruparlos por tamaños de archivo similares. Tener tamaños de archivo diferentes puede provocar un rendimiento no óptimo. Si no es posible agrupar los archivos con un tamaño similar, debe establecer PerFileThreadCount en función del tamaño de archivo más grande.

        PerFileThreadCount = 10 threads for the first 2.5 GB + 1 thread for each additional 256 MB increase in file size

    **Ejemplo**

    Suponiendo que tenga 100 archivos que oscilan entre 1 GB y 10 GB, usaremos los 10 GB como el tamaño de archivo mayor para la ecuación, que sería como la siguiente.

        PerFileThreadCount = 10 + ((10 GB - 2.5 GB) / 256 MB) = 40 threads

* **Paso 3: Cálculo de ConcurrentFilecount**: use el número total de subprocesos y PerFileThreadCount para calcular ConcurrentFileCount basado en la siguiente ecuación:

        Total thread count = PerFileThreadCount * ConcurrentFileCount

    **Ejemplo**

    En función de los valores del ejemplo que hemos usado

        96 = 40 * ConcurrentFileCount

    Por tanto, **ConcurrentFileCount** es **2,4**, que se puede redondear a **2**.

## <a name="further-tuning"></a>Ajuste adicional

Es posible que necesite un ajuste adicional porque se va a trabajar con diversos tamaños de archivo. El cálculo anterior funciona bien si todos los archivos, o la mayoría de ellos, son más grandes y están más cercanos a los 10 GB. Por el contrario, si hay muchos tamaños de archivo diferentes, con muchos archivos pequeños, podría reducir PerFileThreadCount. Al reducir PerFileThreadCount, podemos aumentar ConcurrentFileCount. Por tanto, si suponemos que la mayoría de los archivos son más pequeños, en el intervalo de los 5 GB, podemos rehacer el cálculo:

    PerFileThreadCount = 10 + ((5 GB - 2.5 GB) / 256 MB) = 20

Por consiguiente, **ConcurrentFileCount** pasa a ser 96/20, que es 4,8, lo que se redondea a **4**.

Puede continuar ajustando esta configuración aumentando o reduciendo **PerFileThreadCount** en función de la distribución de los tamaños de archivo.

### <a name="limitation"></a>Limitación

* **El número de archivos es menor que ConcurrentFileCount**: si el número de archivos que va a cargar es menor que el valor de **ConcurrentFileCount** que ha calculado, debería reducir **ConcurrentFileCount** para que sea igual al número de archivos. También puede utilizar los subprocesos restantes para aumentar **PerFileThreadCount**.

* **Demasiados subprocesos**: si aumenta demasiado el número de subprocesos sin aumentar el tamaño del clúster, corre el riesgo de degradar el rendimiento. Puede haber problemas de contención al cambiar de contexto en la CPU.

* **Simultaneidad insuficiente**: si la simultaneidad no es suficiente, el clúster puede ser demasiado pequeño. Puede aumentar el número de nodos en el clúster, lo que proporciona más simultaneidad.

* **Errores de limitación**: es posible que vea errores de limitación si la simultaneidad es demasiado alta. Si ve errores de limitación, reduzca la simultaneidad o póngase en contacto con nosotros.

## <a name="next-steps"></a>pasos siguientes
* [Uso de Azure Data Lake Store para requisitos de macrodatos](data-lake-store-data-scenarios.md) 
* [Protección de los datos en Data Lake Store](data-lake-store-secure-data.md)
* [Uso de Análisis de Azure Data Lake con el Almacén de Data Lake](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Uso de HDInsight de Azure con el Almacén de Data Lake](data-lake-store-hdinsight-hadoop-use-portal.md)

