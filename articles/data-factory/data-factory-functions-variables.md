---
title: 'Data Factory: funciones y variables del sistema | Microsoft Docs'
description: Proporciona una lista de funciones y variables del sistema de Data Factory de Azure
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: monicar
services: data-factory
ms.assetid: b6b3c2ae-b0e8-4e28-90d8-daf20421660d
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/25/2017
ms.author: shlo
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 370b2212be8d5f7a537b8fadbfa05355844dcb96


---
# <a name="azure-data-factory---functions-and-system-variables"></a>Data Factory de Azure: funciones y variables del sistema
Este artículo proporciona información sobre las funciones y las variables compatibles con Data Factory de Azure.

## <a name="data-factory-system-variables"></a>Variables del sistema de Data Factory
| Nombre de la variable | Descripción | Ámbito del objeto | Ámbito JSON y casos de uso |
| --- | --- | --- | --- |
| WindowStart |Inicio del intervalo de tiempo para la ventana de ejecución de la actividad actual |activity |<ol><li>Especifique las consultas de selección de datos. Consulte los artículos de conector indicados en el artículo [Actividades de movimiento de datos](data-factory-data-movement-activities.md) .</li><li>Pase parámetros al script de Hive (ejemplo mostrado anteriormente).</li> |
| WindowEnd |Final del intervalo de tiempo para la ventana de ejecución de actividad actual |activity |Igual que el anterior. |
| SliceStart |Inicio del intervalo de tiempo para el segmento de datos que se está generando |activity<br/>dataset |<ol><li>Especifique rutas de acceso dinámicas a carpetas y nombres de archivo mientras trabaja con [Azure Blob](data-factory-azure-blob-connector.md) y [conjuntos de archivos del sistema de archivos](data-factory-onprem-file-system-connector.md).</li><li>Especifique las dependencias de entrada con funciones de factoría de datos en la colección de entradas de la actividad.</li></ol> |
| SliceEnd |Fin del intervalo de tiempo para el segmento de datos que se está generando |activity<br/>dataset |Igual que el anterior. |

> [!NOTE]
> Actualmente Factoría de datos requiere que el programa especificado en la actividad coincida exactamente con el programa especificado en la disponibilidad del conjunto de datos de salida. Esto significa que WindowStart, WindowEnd, SliceStart y SliceEnd siempre se asignan al mismo período de tiempo y un segmento de salida única.
> 
> 

## <a name="data-factory-functions"></a>Funciones de Data Factory
Puede usar las funciones de Factoría de datos junto con las variables del sistema mencionadas anteriormente con los fines siguientes:

1. Especifique las consultas de selección de datos (consulte los artículos de conector mencionados en el artículo [Actividades de movimiento de datos](data-factory-data-movement-activities.md) .
   
   La sintaxis para invocar una función de Data Factory es: **$$<function>** para las consultas de selección de datos y otras propiedades de la actividad y conjuntos de datos.  
2. Especificar las dependencias de entrada con las funciones de Factoría de datos en la colección de entradas de la actividad (consulte el ejemplo anterior).
   
    $$ no es necesario para especificar expresiones de dependencia de entrada.     

En el ejemplo siguiente, la propiedad **sqlReaderQuery** de un archivo JSON se asigna a un valor devuelto por la función **Text.Format**. Este ejemplo también usa una variable de sistema denominada **WindowStart**, que representa la hora de inicio de la ventana de ejecución de la actividad.

```JSON
{
    "Type": "SqlSource",
    "sqlReaderQuery": "$$Text.Format('SELECT * FROM MyTable WHERE StartTime = \\'{0:yyyyMMdd-HH}\\'', WindowStart)"
}
```

### <a name="functions"></a>Funciones
Las tablas siguientes muestran todas las funciones de Factoría de datos de Azure:

| Categoría | Función | Parámetros | Descripción |
| --- | --- | --- | --- |
| Hora |AddHours(X,Y) |X: DateTime  <br/><br/>Y: int |Agrega Y horas a la hora X determinada. <br/><br/>Ejemplo: 9/5/2013 12:00:00 PM + 2 horas = 9/5/2013 2:00:00 PM |
| Hora |AddMinutes(X,Y) |X: DateTime  <br/><br/>Y: int |Agrega Y minutos a X.<br/><br/>Ejemplo: 15/9/2013 12:00:00 + 15 minutos = 15/9/2013 12:15:00. |
| Hora |StartOfHour(X) |X: DateTime  |Obtiene la hora de inicio de la hora representada por el componente de hora de X. <br/><br/>Ejemplo: StartOfHour de 9/15/2013 05: 10:23 PM es 9/15/2013 05: 00:00 PM |
| Date |AddDays(X,Y) |X: DateTime <br/><br/>Y: int |Agrega Y días a X.<br/><br/>Ejemplo: 9/15/2013 12:00:00 PM + 2 días = 9/17/2013 12:00:00 PM |
| Date |AddMonths(X,Y) |X: DateTime <br/><br/>Y: int |Agrega Y meses a X.<br/><br/>Ejemplo: 9/15/2013 12:00:00 PM + 1 mes = 10/15/2013 12:00:00 PM |
| Date |AddQuarters(X,Y) |X: DateTime  <br/><br/>Y: int |Agrega Y * 3 meses a X.<br/><br/>Ejemplo: 9/15/2013 12:00:00 PM + 1 trimestre = 12/15/2013 12:00:00 PM |
| Date |AddWeeks(X,Y) |X: DateTime <br/><br/>Y: int |Agrega Y * 7 días a X.<br/><br/>Ejemplo: 9/15/2013 12:00:00 PM + 1 semana = 9/22/2013 12:00:00 PM |
| Date |AddYears(X,Y) |X: DateTime <br/><br/>Y: int |Agrega Y años a X.<br/><br/>Ejemplo: 9/15/2013 12:00:00 PM + 1 año = 9/15/2014 12:00:00 PM |
| Date |Day(X) |X: DateTime  |Obtiene el componente de día de X.<br/><br/>Ejemplo: Día de 15/9/2013, 12:00:00 es 9. |
| Date |DayOfWeek(X) |X: DateTime  |Obtiene el día del componente de la semana de X.<br/><br/>Ejemplo: DayOfWeek de 15/9/2013, 12:00:00 es domingo. |
| Date |DayOfYear(X) |X: DateTime  |Obtiene el día del año representado por el componente de año de X.<br/><br/>Ejemplos:<br/>1/12/2015: día 335 de 2015<br/>31/12/2015: día 365 de 2015<br/>31/12/2016: día 366 de 2016 (año bisiesto) |
| Date |DaysInMonth(X) |X: DateTime  |Obtiene los días del mes representado por el componente de mes del parámetro X.<br/><br/>Ejemplo: DaysInMonth de 15/9/2013 son 30 debido a que hay 30 días del mes de septiembre. |
| Date |EndOfDay(X) |X: DateTime  |Obtiene la fecha y hora que representa el final del día (componente de días) de X.<br/><br/>Ejemplo: EndOfDay de 9/15/2013 05:10:23 PM es 9/15/2013 11:59:59 PM. |
| Date |EndOfMonth(X) |X: DateTime  |Obtiene el final del mes representado por el componente de mes del parámetro X. <br/><br/>Ejemplo: EndOfMonth de 15/9/2013 17:10:23 es 30/9/2013 23:59:59 (fecha y hora que representa el final del mes de septiembre). |
| Date |StartOfDay(X) |X: DateTime  |Obtiene el inicio del día representado por el componente de día del parámetro X.<br/><br/>Ejemplo: StartOfDay de 15/9/2013 17:10:23 es 15/9/2013 00:00:00. |
| DateTime |From(X) |X: String |Analiza la cadena X en una fecha y hora. |
| DateTime |Ticks(X) |X: DateTime  |Obtiene la propiedad ticks del parámetro X. Un ciclo es igual a 100 nanosegundos. El valor de esta propiedad representa el número de ciclos transcurridos desde la medianoche del 1 de enero de 0001. |
| Texto |Format(X) |X: String variable |Da formato al texto. |

#### <a name="textformat-example"></a>Ejemplo de Text.Format

```JSON
"defines": { 
    "Year" : "$$Text.Format('{0:yyyy}',WindowStart)",
    "Month" : "$$Text.Format('{0:MM}',WindowStart)",
    "Day" : "$$Text.Format('{0:dd}',WindowStart)",
    "Hour" : "$$Text.Format('{0:hh}',WindowStart)"
}
```

Consulte el tema [Cadenas con formato de fecha y hora personalizado](https://msdn.microsoft.com/library/8kb3ddd4.aspx) , en el que se describen las diferentes opciones de formato que puede usar (por ejemplo: aa frente a aaaa). 

> [!NOTE]
> Cuando se usa una función dentro de otra función, no es necesario usar el prefijo **$$** para la función interna. Por ejemplo: $$Text.Format('PartitionKey eq \\'my_pkey_filter_value\\' y RowKey ge \\'{0:yyyy-MM-dd HH:mm:ss}\\'', Time.AddHours(SliceStart, -6)). En este ejemplo, observe que el prefijo **$$** no se usa para la función **Time.AddHours**. 
> 
> 




<!--HONumber=Nov16_HO3-->


