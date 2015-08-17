<properties
   pageTitle="Rendimiento y escala flexibles con Almacenamiento de datos SQL | Microsoft Azure"
   description="Comprender la flexibilidad del Almacenamiento de datos SQL mediante las unidades de almacenamiento de datos para escalar los recursos de proceso hacia niveles superiores o inferiores. Ejemplos de código proporcionados."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="TwoUnder"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/25/2015"
   ms.author="nicw;JRJ@BigBangData.co.uk;mausher"/>

# Rendimiento y escala flexibles con Almacenamiento de datos SQL
Para aumentar o reducir de manera elástica la potencia de proceso, solo hay que ajustar el número de unidades de almacenamiento de datos (DWU) asignado a su Almacenamiento de datos SQL. Las unidades de almacenamiento de datos son un concepto nuevo que proporciona el Almacenamiento de datos SQL para que la administración le resulte más sencilla y eficaz. Este tema sirve de introducción a las unidades de almacenamiento de datos; explica cómo puede escalar de manera elástica su potencia de proceso. El artículo también proporciona una orientación inicial sobre cómo establecer un valor de DWU razonable para su entorno.

## ¿Qué es una unidad de almacenamiento de datos?
Microsoft ejecuta en segundo plano una serie de pruebas comparativas de rendimiento para determinar qué cantidad de hardware y con qué configuración nos permitirá proporcionar una oferta competitiva a nuestros clientes. La capacidad de proceso se puede escalar para aumentarla o reducirla en bloques de 100 DWU, pero no se ofrecen todos los múltiplos de 100 DWU.

## ¿Cuántas DWU debería usar?
Hay muchas soluciones distintas que Almacenamiento de datos SQL puede desbloquear para los clientes. Por lo tanto, hay una gran variedad de tipos de consultas que pueden ejecutar los clientes y sobre cuántos datos operan, además de la arquitectura de los esquemas, cómo se distribuyen los datos, cuántos usuarios tendrán acceso a los datos, con qué frecuencia, etc.

En lugar de proporcionar puntos de partida prescriptivos de DWU que pueden ser adecuados para una categoría de clientes, vamos a dar a esta pregunta un enfoque práctico. El rendimiento en el Almacenamiento de datos SQL se amplía de manera lineal y el cambio de una escala de proceso a otra (por ejemplo, de 100 DWU a 2000 DWU) se produce en segundos. Esto da libertad para hacer pruebas y determinar qué se ajusta mejor a su escenario.


1. Para un almacenamiento de datos en desarrollo, comience con una cantidad pequeña de DWU.
2. Supervise el rendimiento de su aplicación para familiarizarse con las DWU asignadas y el rendimiento que observe.
3. Determine en qué grado debería ser el rendimiento más rápido o más lento para poder alcanzar el nivel óptimo de rendimiento para sus requerimientos empresariales suponiendo una escala lineal. 
4. Aumente o reduzca la cantidad de DWU usadas en función de sus necesidades. El servicio responderá rápidamente para ajustar los recursos de proceso para satisfacer los requisitos de DWU.
5. Realice ajustes hasta llegar a un nivel de rendimiento adecuado para sus requerimientos empresariales.

Si tiene una aplicación con una carga de trabajo que varía, puede subir o bajar los niveles de rendimiento para responder a los valores máximos y los puntos más bajos. Por ejemplo, si por lo general la carga de trabajo aumenta considerablemente a final de mes, podría planear agregar más DWU durante esos días de máxima actividad y luego reducir su número cuando termine este período.
 
## Escala de los recursos de proceso hacia un nivel superior o inferior
Independientemente del almacenamiento en la nube, la elasticidad del almacenamiento de datos SQL permite aumentar, reducir o pausar la capacidad de proceso utilizando una escala deslizante de unidades de almacenamiento de datos (DWU). Esto le ofrece la flexibilidad para optimizar la capacidad de proceso con un ajuste óptimo para su empresa.

La capacidad de proceso se puede aumentar mediante el [Portal de Azure][], usando T-SQL, con las API de REST o a través de PowerShell. La escala hacia niveles superiores o inferiores cancela todas las actividades que están en ejecución o en cola, pero la operación se completa en segundos, de manera que puede reanudar las actividades con más o menos capacidad de proceso.

El código de T-SQL siguiente muestra cómo ajustar la asignación de DWU para Almacenamiento de datos SQL:

```
ALTER DATABASE MySQLDW 
MODIFY (SERVICE_OBJECTIVE = 'DW1000')
;
```

También puede conseguir el mismo resultado mediante Powershell, utilizando el código siguiente:

```
Set-AzureSQLDatabase -DatabaseName "MySQLDW" -ServerName "MyServer.database.windows.net" -ServiceObjective "DW1000"
```

## Pausa de los recursos de proceso
El almacenamiento de datos SQL tiene la capacidad única de pausar y reanudar el proceso a petición. Si el equipo no va a utilizar la instancia de almacenamiento de datos durante un período de tiempo, como por la noche, durante los fines de semana, en determinados períodos de vacaciones o por cualquier otro motivo, puede pausar la instancia del almacenamiento de datos durante ese período de tiempo y reanudarla cuando vuelva donde la dejó.

La acción de pausar devuelve los recursos de proceso al grupo de recursos disponibles en el centro de datos, mientras que la de reanudar adquiere los recursos de proceso necesarios para las DWU establecidas y asigna estas a la instancia del almacenamiento de datos.

Las operaciones de pausar y reanudar la capacidad de proceso se pueden realizar mediante el [Portal de Azure][], a través de las API de REST o usando Powershell. Al pausar se cancela la ejecución de todas las actividades que están en ejecución o en cola, y cuando vuelva, puede reanudar el uso de los recursos de proceso en segundos.

El código siguiente muestra cómo realizar una pausa mediante PowerShell:

```
Suspend-AzureSqlDatabase –ResourceGroupName "ResourceGroup11" –ServerName
"Server01" –DatabaseName "Database02"
```

Reanudar el servicio también es muy sencillo con PowerShell:

```
Resume-AzureSqlDatabase –ResourceGroupName "ResourceGroup11" –ServerName "Server01" –DatabaseName "Database02"
```

Para obtener más detalles sobre cómo usar PowerShell, consulte el artículo [Introducción a los cmdlets de PowerShell][].

> [Azure.Note]Puesto que el almacenamiento es independiente de los procesos, el almacenamiento no se ve afectado por las pausas.

## Pasos siguientes
Para obtener información general sobre el rendimiento, vea [Introducción al rendimiento][].

<!--Image references-->

<!--Article references-->
[Introducción al rendimiento]: sql-data-warehouse-overview-performance.md
[Introducción a los cmdlets de PowerShell]: sql-data-warehouse-get-started-powershell-cmdlets.md

<!--MSDN references-->


<!--Other Web references-->

[Portal de Azure]: http://portal.azure.com/

<!---HONumber=August15_HO6-->