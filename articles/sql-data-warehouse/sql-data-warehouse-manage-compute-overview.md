<properties
   pageTitle="Administración de la potencia de proceso en Almacenamiento de datos SQL de Azure (información general) | Microsoft Azure"
   description="Funcionalidades de escalado horizontal del rendimiento en Almacenamiento de datos SQL de Azure. Realice el escalado horizontal ajustando las DWU o pause y reanude los recursos de proceso para ahorrar costos."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="barbkess"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/01/2016"
   ms.author="barbkess;sonyama"/>

# Administración de la potencia de proceso en Almacenamiento de datos SQL de Azure (información general)

> [AZURE.SELECTOR]
- [Información general](sql-data-warehouse-manage-compute-overview.md)
- [Portal](sql-data-warehouse-manage-compute-portal.md)
- [PowerShell](sql-data-warehouse-manage-compute-powershell.md)
- [REST](sql-data-warehouse-manage-compute-rest-api.md)
- [TSQL](sql-data-warehouse-manage-compute-tsql.md)

La arquitectura del Almacenamiento de datos SQL separa el proceso y el almacenamiento, lo que permite a cada uno escalar de manera independiente. Como resultado, puede realizar el escalado horizontal del rendimiento mientras ahorra costos al pagar solo el rendimiento cuando lo necesite.

Esta información general describe las siguientes capacidades de escalabilidad horizontal de rendimiento de Almacenamiento de datos SQL y proporciona recomendaciones sobre cómo y cuándo utilizarlas.

- Escalado de la potencia de proceso mediante el ajuste de unidades de almacenamiento de datos (DWU)
- Pausa o reanudación de recursos de proceso

<a name="scale-performance-bk"></a>

## Rendimiento a escala

En Almacenamiento de datos SQL, puede escalar horizontalmente o de nuevo de forma rápida el rendimiento mediante el aumento o disminución de los recursos de proceso de la CPU, la memoria y el ancho de banda de E/S. Para escalar el rendimiento, todo lo que necesita hacer es ajustar el número de unidades de almacenamiento de datos (DWU) que asigna Almacenamiento de datos SQL. Almacenamiento de datos SQL realiza el cambio y controla todos los cambios subyacentes al hardware o software rápidamente.

>[AZURE.NOTE] Lejos quedan los días en que había que investigar qué tipo de procesadores, qué cantidad de memoria o qué tipo de almacenamiento era necesario para obtener un rendimiento excelente en el almacenamiento de datos. Al colocar el Almacenamiento de datos en la nube, ya no es necesario preocuparse por los problemas de hardware de bajo nivel. En su lugar, Almacenamiento de datos SQL formula esta pregunta: ¿con qué rapidez se desean analizar los datos?

### ¿Qué es una DWU?

Una *unidad de almacenamiento de datos* (DWU) es una medida de capacidades de proceso subyacentes para la base de datos en un momento dado. A medida que aumenta el número de DWU, el Almacenamiento de datos SQL ejecuta operaciones en paralelo (por ejemplo, la carga o la consulta de datos) a través una CPU más distribuida y recursos de memoria. Esto reduce la latencia y mejora el rendimiento.

Las DWU se basan en la velocidad de digitalización y carga. A medida que aumenten las DWU, aumenta la velocidad de carga y digitalización.

- **Velocidad de carga**. El número de registros que Almacenamiento de datos SQL puede introducir por segundo. En concreto, este es el número de registros que Almacenamiento de datos SQL puede importar desde el Almacenamiento de blobs de Azure a un índice columnstore almacenado en clúster mediante PolyBase. 

- **Velocidad de digitalización.**. El número de registros que una consulta puede recuperar desde Almacenamiento de datos SQL por segundo. En concreto, este es el número de registros que Almacenamiento de datos SQL puede importar mediante la ejecución de una consulta de almacenamiento de datos en un índice columnstore almacenado en clúster. Para realizar una prueba de la velocidad de digitalización usamos una consulta de almacenamiento de datos estándar que examina un gran número de filas y realiza después una agregación compleja; Se trata de una operación con un gran consumo de E/S y CPU.

>[AZURE.NOTE] Se están midiendo y realizando algunas mejoras importantes del rendimiento y pronto se compartirán las velocidades esperadas. Durante la versión preliminar, realizaremos mejoras continuas (por ejemplo, aumentar la compresión y el almacenamiento en caché) para aumentar estas métricas de rendimiento y asegurar que se escalan de forma predecible.

Para obtener una lista de DWU, consulte los objetivos de nivel de servicio en el artículo [Límites de capacidad][].

### ¿Cómo se realiza el escalado del rendimiento?

Para ampliar aumentar o disminuir de manera elástica la capacidad de proceso, basta con cambiar la configuración de las unidades de almacenamiento de datos (DWU) para la base de datos. En segundo plano, Almacenamiento de datos SQL cambia las asignaciones de CPU y memoria utilizando las capacidades de implementación rápidas y simples de la Base de datos SQL.

Se asignan las DWU en bloques de 100, pero no todos los bloques están disponibles. A medida que los DWU aumentan, aumenta el rendimiento de manera lineal. En niveles superiores de DWU, debe agregar más de 100 DWU para observar una mejora considerable del rendimiento. Para ayudarle a seleccionar saltos significativos en las DWU, le ofrecemos los niveles DWU que proporcionarán los mejores resultados.
 
Para ajustar las DWU, puede utilizar cualquiera de estos métodos individuales.

- [Scale compute power with Azure portal (Escalado de la potencia de proceso con el Portal de Azure)][]
- [Scale compute power with PowerShell (Escalado de la potencia de proceso con PowerShell)][]
- [Scale compute power with REST APIs (Escalado de la potencia de proceso con API de REST)][]
- [Scale compute power with TSQL (Escalado de la potencia de proceso con TSQL)][]

### ¿Cuántas DWU debería usar?
 
El rendimiento en el Almacenamiento de datos SQL se amplía de manera lineal y el cambio de una escala de proceso a otra (por ejemplo, de 100 DWU a 2000 DWU) se produce en segundos. Esto le ofrece la flexibilidad necesaria para experimentar con distintas configuraciones de DWU hasta que determine el escenario que mejor se ajuste.

> [AZURE.NOTE] Puede que no vea el escalado del rendimiento esperada con volúmenes de datos inferiores. Se recomienda comenzar con volúmenes de datos a partir de 1 TB o más a fin de obtener unos resultados de pruebas de rendimiento exactos.

Recomendaciones para encontrar la mejor DWU para la carga de trabajo:

1. Para un almacenamiento de datos en desarrollo, comience seleccionando un número pequeño de DWU.
2. Supervise el rendimiento de su aplicación, observando el número de DWU seleccionados en comparación con el rendimiento que observe.
3. Determine en qué grado debería ser el rendimiento más rápido o más lento para poder alcanzar el nivel óptimo de rendimiento para sus requerimientos suponiendo una escala lineal.
4. Aumente o disminuya el número de DWU en proporción a la rapidez con la que quiere que funcione la carga de trabajo. El servicio responderá rápidamente y ajustará los recursos de proceso para satisfacer los nuevos requisitos de DWU.
5. Continúe realizando ajustes hasta llegar a un nivel de rendimiento adecuado para sus requerimientos empresariales.

### ¿Cuándo debo realizar un escalado de DWU?

En general, queremos que las DWU sean sencillas. Cuando necesite resultados más rápidos, aumente el número de DWU y pague por un mayor rendimiento. Cuando necesite menos capacidad de proceso, reduzca el número de DWU y pague solo por lo que necesita.

Recomendaciones de cuándo realizar el escalado de DWU:

1. Si su aplicación tiene una carga de trabajo que varía, realice un escalado vertical y una reducción vertical de los niveles de DWU para responder a los valores máximos y los puntos más bajos. Por ejemplo, si por lo general la carga de trabajo aumenta considerablemente a final de mes, planee agregar más DWU durante esos días de máxima actividad y luego reduzca su número verticalmente cuando termine este período.
1. Para realizar una operación de transformación o carga de una gran cantidad de datos, realice el escalado vertical de DWU para que los datos estén disponibles con mayor rapidez.

<a name="pause-compute-bk"></a>

## Pausa del proceso

[AZURE.INCLUDE [SQL Data Warehouse pause description (Descripción de pausa de Almacenamiento de datos SQL)](../../includes/sql-data-warehouse-pause-description.md)]

Para pausar una base de datos, use cualquiera de estos métodos individuales.

- [Pausa del proceso con el Portal de Azure][]
- [Pausa del proceso con PowerShell][]
- [Pausa del proceso con las API de REST][]

<a name="resume-compute-bk"></a>

## Reanudación del proceso

[AZURE.INCLUDE [SQL Data Warehouse resume description (Descripción de reanudación de Almacenamiento de datos SQL)](../../includes/sql-data-warehouse-resume-description.md)]

Para reanudar una base de datos, use cualquiera de estos métodos individuales.

- [Reanudación del proceso con el Portal de Azure][]
- [Reanudación del proceso con PowerShell][]
- [Reanudación del proceso con las API de REST][]

<a name="next-steps-bk"></a>

## Pasos siguientes
Consulte los artículos siguientes para comprender mejor algunos conceptos fundamentales adicionales sobre rendimiento y escala:

- [modelo de simultaneidad][]
- [diseño de tablas][]
- [selección de una clave de distribución hash para una tabla][]
- [estadísticas para mejorar el rendimiento][]

<!--Image reference-->

<!--Article references-->

[Scale compute power with Azure portal (Escalado de la potencia de proceso con el Portal de Azure)]: ./sql-data-warehouse-manage-compute-portal.md#scale-compute-bk
[Scale compute power with PowerShell (Escalado de la potencia de proceso con PowerShell)]: ./sql-data-warehouse-manage-compute-powershell.md#scale-compute-bk
[Scale compute power with REST APIs (Escalado de la potencia de proceso con API de REST)]: ./sql-data-warehouse-manage-compute-rest-api.md#scale-compute-bk
[Scale compute power with TSQL (Escalado de la potencia de proceso con TSQL)]: ./sql-data-warehouse-manage-compute-tsql.md#scale-compute-bk

[Límites de capacidad]: ./sql-data-warehouse-service-capacity-limits.md

[Pausa del proceso con el Portal de Azure]: ./sql-data-warehouse-manage-compute-portal.md#pause-compute-bk
[Pausa del proceso con PowerShell]: ./sql-data-warehouse-manage-compute-powershell.md#pause-compute-bk
[Pausa del proceso con las API de REST]: ./sql-data-warehouse-manage-compute-rest-api.md#pause-compute-bk

[Reanudación del proceso con el Portal de Azure]: ./sql-data-warehouse-manage-compute-portal.md#resume-compute-bk
[Reanudación del proceso con PowerShell]: ./sql-data-warehouse-manage-compute-powershell.md#resume-compute-bk
[Reanudación del proceso con las API de REST]: ./sql-data-warehouse-manage-compute-rest-api.md#resume-compute-bk

[modelo de simultaneidad]: sql-data-warehouse-develop-concurrency.md
[diseño de tablas]: sql-data-warehouse-develop-table-design.md
[selección de una clave de distribución hash para una tabla]: sql-data-warehouse-develop-hash-distribution-key.md
[estadísticas para mejorar el rendimiento]: sql-data-warehouse-develop-statistics.md
[development overview]: sql-data-warehouse-overview-develop.md



<!--MSDN references-->


<!--Other Web references-->

[Azure portal]: http://portal.azure.com/

<!---HONumber=AcomDC_0608_2016-->