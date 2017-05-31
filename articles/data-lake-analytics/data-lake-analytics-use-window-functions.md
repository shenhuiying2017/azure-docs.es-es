---
title: Uso de funciones de ventana de U-SQL para trabajos de Azure Data Lake Analytics | Microsoft Docs
description: 'Aprenda a usar las funciones de ventana de U-SQL. '
services: data-lake-analytics
documentationcenter: 
author: saveenr
manager: saveenr
editor: cgronlun
ms.assetid: a5e14b32-d5eb-4f4b-9258-e257359f9988
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data`
ms.date: 12/05/2016
ms.author: edmaca
ms.translationtype: Human Translation
ms.sourcegitcommit: e72275ffc91559a30720a2b125fbd3d7703484f0
ms.openlocfilehash: 55d19a00198f1943a8196d31399c617397b4e5d2
ms.contentlocale: es-es
ms.lasthandoff: 05/05/2017


---
# <a name="using-u-sql-window-functions-for-azure-data-lake-analytics-jobs"></a>Uso de funciones de ventana de U-SQL para trabajos de Análisis de Azure Data Lake
Las funciones de ventana se introdujeron en el estándar SQL de ISO/ANSI en 2003. U-SQL adopta un subconjunto de las funciones de ventana según lo definido en el estándar SQL de ANSI.

Las funciones de ventana se usan para realizar cálculos dentro de conjuntos de filas denominados *ventanas*. Las ventanas se definen mediante la cláusula OVER. Las funciones de ventana resuelven algunos escenarios clave de manera muy eficaz.

Las funciones de ventana se dividen en las siguientes categorías: 

* [Funciones de agregación de informes](#reporting-aggregation-functions), como SUM o AVG
* [Funciones de categoría](#ranking-functions), por ejemplo, DENSE_RANK, ROW_NUMBER, NTILE y RANK
* [Funciones analíticas](#analytic-functions), como distribución acumulativa o percentiles, acceso a los datos de una fila anterior en el mismo conjunto de resultados sin usar una autocombinación

## <a name="sample-datasets"></a>Conjuntos de datos de ejemplo
En este tutorial, se usan dos conjuntos de datos:

### <a name="the-querylog-sample-dataset"></a>El conjunto de datos de ejemplo QueryLog.
  
QueryLog representa una lista de lo que se buscó en el motor de búsqueda. Cada registro de consultas incluye:
  
* Consulta: lo que el usuario buscaba.
* Latencia: la rapidez con que la consulta volvió al usuario en milisegundos.
* Vertical: el tipo de contenido en el que está interesado el usuario (vínculos web, imágenes y vídeos).  
 
```
@querylog = 
    SELECT * FROM ( VALUES
        ("Banana"  , 300, "Image" ),
        ("Cherry"  , 300, "Image" ),
        ("Durian"  , 500, "Image" ),
        ("Apple"   , 100, "Web"   ),
        ("Fig"     , 200, "Web"   ),
        ("Papaya"  , 200, "Web"   ),
        ("Avocado" , 300, "Web"   ),
        ("Cherry"  , 400, "Web"   ),
        ("Durian"  , 500, "Web"   ) )
    AS T(Query,Latency,Vertical);
```

## <a name="the-employees-sample-dataset"></a>El conjunto de datos de ejemplo Employees
  
El conjunto de datos Employees incluye los campos siguientes:
  
* EmpID: el id. del empleado
* EmpName: el nombre del empleado
* DeptName: el nombre del departamento 
* DeptID: el id. del departamento
* Salary: el sueldo del empleado

```
@employees = 
    SELECT * FROM ( VALUES
        (1, "Noah",   "Engineering", 100, 10000),
        (2, "Sophia", "Engineering", 100, 20000),
        (3, "Liam",   "Engineering", 100, 30000),
        (4, "Emma",   "HR",          200, 10000),
        (5, "Jacob",  "HR",          200, 10000),
        (6, "Olivia", "HR",          200, 10000),
        (7, "Mason",  "Executive",   300, 50000),
        (8, "Ava",    "Marketing",   400, 15000),
        (9, "Ethan",  "Marketing",   400, 10000) )
    AS T(EmpID, EmpName, DeptName, DeptID, Salary);
```  

## <a name="compare-window-functions-to-grouping"></a>Comparación de las funciones de ventana con la agrupación
Las funciones de ventana y agrupación están relacionadas desde un punto de vista conceptual. Resulta útil comprender esta relación.

### <a name="use-aggregation-and-grouping"></a>Uso de agregación y agrupación
En la consulta siguiente, se usa una agregación para calcular el salario total de todos los empleados:

    @result = 
        SELECT 
            SUM(Salary) AS TotalSalary
        FROM @employees;

El resultado es una sola fila y una sola columna. 165 000 USD es la suma del valor de Salary de toda la tabla. 

| TotalSalary |
| --- |
| 165000 |


La instrucción siguiente usa la cláusula GROUP BY para calcular el salario total para cada departamento:

    @result=
        SELECT DeptName, SUM(Salary) AS SalaryByDept
        FROM @employees
        GROUP BY DeptName;

Los resultados son:

| DeptName | SalaryByDept |
| --- | --- |
| Engineering |60000 |
| HR |30000 |
| Ejecutivo |50000 |
| Marketing |25000 |

La suma de la columna SalaryByDept es 165000 USD, que coincide con la cifra en el último script.

En ambos casos, hay menos filas de salida que de entrada:

* Sin GROUP BY, la agregación contrae todas las filas en una sola. 
* Con GROUP BY, hay N filas de salida, donde N es el número de valores distintos que aparecen en los datos.  En este caso, obtendrá 4 filas en la salida.

### <a name="use-a-window-function"></a>Uso de una función de ventana
La cláusula OVER en el ejemplo siguiente está vacía, por lo que la ventana incluye todas las filas. La suma (SUM) en este ejemplo se aplica a la cláusula OVER a la que precede.

Se podría interpretar esta consulta como la suma del salario en una ventana de todas las filas.

    @result=
        SELECT
            EmpName,
            SUM(Salary) OVER( ) AS SalaryAllDepts
        FROM @employees;

A diferencia de GROUP BY, hay tantas filas de salida como de entrada: 

| EmpName | TotalAllDepts |
| --- | --- |
| Noah |165000 |
| Sophia |165000 |
| Liam |165000 |
| Emma |165000 |
| Jacob |165000 |
| Olivia |165000 |
| Mason |165000 |
| Ava |165000 |
| Ethan |165000 |

El valor 165000 (el total de todos los salarios) se coloca en cada fila de salida. Ese total proviene de la "ventana" de todas las filas, por lo que incluye todos los salarios. 

En el siguiente ejemplo, se muestra cómo refinar la "ventana" para obtener una lista de todos los empleados, el departamento y el salario total para el departamento. Se agrega PARTITION BY a la cláusula OVER.

    @result=
    SELECT
        EmpName, DeptName,
        SUM(Salary) OVER( PARTITION BY DeptName ) AS SalaryByDept
    FROM @employees;

Los resultados son:

| EmpName | DeptName | SalaryByDep |
| --- | --- | --- |
| Noah |Engineering |60000 |
| Sophia |Engineering |60000 |
| Liam |Engineering |60000 |
| Mason |Ejecutivo |50000 |
| Emma |HR |30000 |
| Jacob |HR |30000 |
| Olivia |HR |30000 |
| Ava |Marketing |25000 |
| Ethan |Marketing |25000 |

Una vez más, hay el mismo número de filas de entrada que de salida. Sin embargo, cada fila tiene un salario total para el departamento correspondiente.

## <a name="reporting-aggregation-functions"></a>Funciones de agregación de informes
Las funciones de ventana también admiten los agregados siguientes:

* COUNT
* SUM
* MÍN
* MÁX
* MEDIA
* STDEV
* VAR

La sintaxis es:

    <AggregateFunction>( [DISTINCT] <expression>) [<OVER_clause>]

Nota: 

* De forma predeterminada, las funciones de agregación, excepto COUNT, pasan por alto los valores nulos.
* Cuando se especifican funciones de agregado junto con la cláusula OVER, no se permite la cláusula ORDER BY en la cláusula OVER.

### <a name="use-sum"></a>Uso de SUM
En el ejemplo siguiente se agrega un salario total por departamento a cada fila de entrada:

    @result=
        SELECT 
            *,
            SUM(Salary) OVER( PARTITION BY DeptName ) AS TotalByDept
        FROM @employees;

Este es el resultado:

| EmpID | EmpName | DeptName | DeptID | Salary | TotalByDept |
| --- | --- | --- | --- | --- | --- |
| 1 |Noah |Engineering |100 |10000 |60000 |
| 2 |Sophia |Engineering |100 |20000 |60000 |
| 3 |Liam |Engineering |100 |30000 |60000 |
| 7 |Mason |Ejecutivo |300 |50000 |50000 |
| 4 |Emma |HR |200 |10000 |30000 |
| 5 |Jacob |HR |200 |10000 |30000 |
| 6 |Olivia |HR |200 |10000 |30000 |
| 8 |Ava |Marketing |400 |15000 |25000 |
| 9 |Ethan |Marketing |400 |10000 |25000 |

### <a name="use-count"></a>Uso de COUNT
En el ejemplo siguiente se agrega un campo adicional a cada fila para mostrar el número total de empleados de cada departamento.

    @result =
        SELECT *, 
            COUNT(*) OVER(PARTITION BY DeptName) AS CountByDept 
        FROM @employees;

El resultado es:

| EmpID | EmpName | DeptName | DeptID | Salary | CountByDept |
| --- | --- | --- | --- | --- | --- |
| 1 |Noah |Engineering |100 |10000 |3 |
| 2 |Sophia |Engineering |100 |20000 |3 |
| 3 |Liam |Engineering |100 |30000 |3 |
| 7 |Mason |Ejecutivo |300 |50000 |1 |
| 4 |Emma |HR |200 |10000 |3 |
| 5 |Jacob |HR |200 |10000 |3 |
| 6 |Olivia |HR |200 |10000 |3 |
| 8 |Ava |Marketing |400 |15000 |2 |
| 9 |Ethan |Marketing |400 |10000 |2 |

### <a name="use-min-and-max"></a>Uso de MIN y MAX
En el ejemplo siguiente se agrega un campo adicional a cada fila para mostrar el salario más bajo de cada departamento.

    @result =
        SELECT 
            *,
            MIN(Salary) OVER( PARTITION BY DeptName ) AS MinSalary
        FROM @employees;

Los resultados son:

| EmpID | EmpName | DeptName | DeptID | Salary | MinSalary |
| --- | --- | --- | --- | --- | --- |
| 1 |Noah |Engineering |100 |10000 |10000 |
| 2 |Sophia |Engineering |100 |20000 |10000 |
| 3 |Liam |Engineering |100 |30000 |10000 |
| 7 |Mason |Ejecutivo |300 |50000 |50000 |
| 4 |Emma |HR |200 |10000 |10000 |
| 5 |Jacob |HR |200 |10000 |10000 |
| 6 |Olivia |HR |200 |10000 |10000 |
| 8 |Ava |Marketing |400 |15000 |10000 |
| 9 |Ethan |Marketing |400 |10000 |10000 |

## <a name="ranking-functions"></a>Funciones de categoría
Las funciones de categoría devuelven un valor de categoría (LONG) para cada fila de cada partición definida mediante las cláusulas PARTITION BY y OVER. El orden de categorías se controla con la cláusula ORDER BY en la cláusula OVER.

Se admiten estas funciones de categoría:

* RANK
* DENSE_RANK 
* NTILE
* ROW_NUMBER

**Sintaxis:**

    [ RANK() | DENSE_RANK() | ROW_NUMBER() | NTILE(<numgroups>) ]
        OVER (
            [PARTITION BY <identifier, > …[n]]
            [ORDER BY <identifier, > …[n] [ASC|DESC]] 
    ) AS <alias>

* La cláusula ORDER BY es opcional para las funciones de categoría. Si no se especifica ORDER BY, U-SQL asigna valores basados en el orden en que lee el registro, lo que produce valores no deterministas para ROW_NUMBER, RANK o DENSE_RANK.
* NTILE requiere una expresión que se evalúe como entero positivo. Este número especifica el número de grupos en que se debe dividir cada partición. Este identificador se usa solo con la función de categoría NTILE. 

Para obtener más información sobre la cláusula OVER, consulte la [referencia sobre el lenguaje U-SQL](http://go.microsoft.com/fwlink/p/?LinkId=691348).

ROW_NUMBER, RANK y DENSE_RANK asignan números a las filas de una ventana. En lugar de tratarlas por separado, es más intuitivo ver cómo responden a la misma entrada.

    @result =
    SELECT 
        *,
        ROW_NUMBER() OVER (PARTITION BY Vertical ORDER BY Latency) AS RowNumber,
        RANK() OVER (PARTITION BY Vertical ORDER BY Latency) AS Rank, 
        DENSE_RANK() OVER (PARTITION BY Vertical ORDER BY Latency) AS DenseRank 
    FROM @querylog;

Tenga en cuenta que las cláusulas OVER son idénticas. El resultado es:

| Consultar | Latency: INT | Vertical | RowNumber | RANK | DenseRank |
| --- | --- | --- | --- | --- | --- |
| Banana |300 |Imagen |1 |1 |1 |
| Cherry |300 |Imagen |2 |1 |1 |
| Durian |500 |Imagen |3 |3 |2 |
| Apple |100 |Web |1 |1 |1 |
| Fig |200 |Web |2 |2 |2 |
| Papaya |200 |Web |3 |2 |2 |
| Fig |300 |Web |4 |4 |3 |
| Cherry |400 |Web |5 |5 |4 |
| Durian |500 |Web |6 |6 |5 |

### <a name="rownumber"></a>ROW_NUMBER
Dentro de cada ventana (Vertical, Image o Web), el número de fila aumenta en 1 según el orden de latencia.  

![Función de ventana ROW_NUMBER de U-SQL](./media/data-lake-analytics-use-windowing-functions/u-sql-windowing-function-row-number-result.png)

### <a name="rank"></a>RANK
A diferencia de ROW_NUMBER (), RANK() usa el valor de Latency que se especifica en la cláusula ORDER BY para la ventana.

RANK comienza con (1, 1, 3) porque los dos primeros valores de Latency son iguales. A continuación, el siguiente valor es 3, porque el valor de Latency cambió a 500. El aspecto clave es que, aunque se proporcionen valores duplicados en la misma categoría, el número de RANK "saltará" al siguiente valor de ROW_NUMBER. Puede ver la repetición de este patrón con la secuencia (2, 2, 4) en la vertical Web.

![Función de ventana RANK de U-SQL](./media/data-lake-analytics-use-windowing-functions/u-sql-windowing-function-rank-result.png)

### <a name="denserank"></a>DENSE_RANK
DENSE_RANK es igual que el RANK, salvo que no vaya al siguiente ROW_NUMBER. DENSE_RANK va al siguiente número en la secuencia. Observe las secuencias (1, 1, 2) y (2, 2, 3) en el ejemplo.

![Función de ventana DENSE_RANK de U-SQL](./media/data-lake-analytics-use-windowing-functions/u-sql-windowing-function-dense-rank-result.png)

### <a name="remarks"></a>Comentarios
* Si no se especifica ORDER BY, la función de clasificación se aplica al conjunto de filas sin ningún orden, lo que provoca un comportamiento no determinista.
* Deben cumplirse las siguientes condiciones para garantizar se ordena lo mismo a las filas devueltas por una consulta con ROW_NUMBER con cada ejecución.
  
  * Los valores de la columna particionada son únicos.
  * Los valores de las columnas ORDER BY son únicos.
  * Las combinaciones de valores de la columna particionada y las columnas ORDER BY son únicas.

### <a name="ntile"></a>NTILE
NTILE distribuye las filas de una partición ordenada entre un número especificado de grupos. Los grupos se numeran comenzando por uno. 

En el ejemplo siguiente se divide el conjunto de filas de cada partición (vertical) en cuatro grupos, en orden de latencia, y se devuelve el número de grupo para cada fila. 

El segmento vertical Imagen tiene tres filas, por lo que tiene tres grupos. 

El segmento vertical Web tiene seis filas.  Las dos filas adicionales se distribuyen a los primeros dos grupos. Esta es la razón de que haya dos filas en los grupos 1 y 2, y solo una fila en los grupos 3 y 4.  

    @result =
        SELECT 
            *,
            NTILE(4) OVER(PARTITION BY Vertical ORDER BY Latency) AS Quartile   
        FROM @querylog;

Los resultados son:

| Consultar | Latency | Vertical | Quartile |
| --- | --- | --- | --- |
| Banana |300 |Imagen |1 |
| Cherry |300 |Imagen |2 |
| Durian |500 |Imagen |3 |
| Apple |100 |Web |1 |
| Fig |200 |Web |1 |
| Papaya |200 |Web |2 |
| Fig |300 |Web |2 |
| Cherry |400 |Web |3 |
| Durian |500 |Web |4 |

NTILE toma un parámetro ("numgroups"). Numgroups es una expresión constante int o long positiva que especifica el número de grupos en los que se debe dividir cada partición. 

* Si el número de filas de la partición es divisible por numgroups, los grupos tendrán el mismo tamaño. 
* Si el número de filas de una partición no es divisible por numgroups, los grupos tendrán tamaños ligeramente diferentes. Los grupos de mayor tamaño preceden a los más pequeños en el orden especificado por la cláusula OVER. 

Por ejemplo:

    100 rows divided into 4 groups: 
    [ 25, 25, 25, 25 ]

    102 rows divided into 4 groups: 
    [ 26, 26, 25, 25 ]

### <a name="top-n-records-per-partition-via-rank-denserank-or-rownumber"></a>N registros principales por partición mediante RANK, DENSE_RANK o ROW_NUMBER
Muchos usuarios desean seleccionar solo n filas SUPERIORES por grupo, algo que no se puede hacer con la cláusula GROUP BY tradicional. 

Vio el ejemplo siguiente al principio de la sección de funciones de categoría. No muestra los N registros principales para cada partición:

    @result =
    SELECT 
        *,
        ROW_NUMBER() OVER (PARTITION BY Vertical ORDER BY Latency) AS RowNumber,
        RANK() OVER (PARTITION BY Vertical ORDER BY Latency) AS Rank,
        DENSE_RANK() OVER (PARTITION BY Vertical ORDER BY Latency) AS DenseRank
    FROM @querylog;

Los resultados son:

| Consultar | Latency | Vertical | RANK | DenseRank | RowNumber |
| --- | --- | --- | --- | --- | --- |
| Banana |300 |Imagen |1 |1 |1 |
| Cherry |300 |Imagen |1 |1 |2 |
| Durian |500 |Imagen |3 |2 |3 |
| Apple |100 |Web |1 |1 |1 |
| Fig |200 |Web |2 |2 |2 |
| Papaya |200 |Web |2 |2 |3 |
| Fig |300 |Web |4 |3 |4 |
| Cherry |400 |Web |5 |4 |5 |
| Durian |500 |Web |6 |5 |6 |

### <a name="top-n-with-dense-rank"></a>N principales con DENSE RANK
El ejemplo siguiente devuelve los tres registros principales de cada grupo sin interrupciones en la numeración secuencial de categoría de las filas de cada partición.

    @result =
    SELECT 
        *,
        DENSE_RANK() OVER (PARTITION BY Vertical ORDER BY Latency) AS DenseRank
    FROM @querylog;

    @result = 
        SELECT *
        FROM @result
        WHERE DenseRank <= 3;

Los resultados son:

| Consultar | Latency | Vertical | DenseRank |
| --- | --- | --- | --- |
| Banana |300 |Imagen |1 |
| Cherry |300 |Imagen |1 |
| Durian |500 |Imagen |2 |
| Apple |100 |Web |1 |
| Fig |200 |Web |2 |
| Papaya |200 |Web |2 |
| Fig |300 |Web |3 |

### <a name="top-n-with-rank"></a>N principales con RANK
    @result =
        SELECT 
            *,
            RANK() OVER (PARTITION BY Vertical ORDER BY Latency) AS Rank
        FROM @querylog;

    @result = 
        SELECT *
        FROM @result
        WHERE Rank <= 3;

Los resultados son:    

| Consultar | Latency | Vertical | RANK |
| --- | --- | --- | --- |
| Banana |300 |Imagen |1 |
| Cherry |300 |Imagen |1 |
| Durian |500 |Imagen |3 |
| Apple |100 |Web |1 |
| Fig |200 |Web |2 |
| Papaya |200 |Web |2 |

### <a name="top-n-with-rownumber"></a>N principales con ROW_NUMBER
    @result =
        SELECT 
            *,
            ROW_NUMBER() OVER (PARTITION BY Vertical ORDER BY Latency) AS RowNumber
        FROM @querylog;

    @result = 
        SELECT *
        FROM @result
        WHERE RowNumber <= 3;

Los resultados son:   

| Consultar | Latency | Vertical | RowNumber |
| --- | --- | --- | --- |
| Banana |300 |Imagen |1 |
| Cherry |300 |Imagen |2 |
| Durian |500 |Imagen |3 |
| Apple |100 |Web |1 |
| Fig |200 |Web |2 |
| Papaya |200 |Web |3 |

### <a name="assign-globally-unique-row-number"></a>Asignación de un número de fila único global
Suele resultar útil asignar un número único global a cada fila. Las Funciones de categoría son más fáciles y más eficaces que el uso de un reductor.

    @result =
        SELECT 
            *,
            ROW_NUMBER() OVER () AS RowNumber
        FROM @querylog;

<!-- ################################################### -->
## <a name="analytic-functions"></a>Funciones analíticas
Las funciones analíticas se usan para comprender las distribuciones de valores en ventanas. El escenario más común en el que se usan funciones analíticas es el cálculo de percentiles.

**Funciones de ventana analíticas admitidas**

* CUME_DIST 
* PERCENT_RANK
* PERCENTILE_CONT
* PERCENTILE_DISC

### <a name="cumedist"></a>CUME_DIST

CUME_DIST calcula la posición relativa de un valor especificado en un grupo de valores. Calcula el porcentaje de consultas que tienen una latencia inferior o igual a la latencia de la consulta actual en la misma vertical. 

CUME_DIST para una fila R, suponiendo que el orden sea ascendente, es el número de filas con valores inferiores o iguales al valor de R, dividido por el número de filas que se evalúan en la partición. 

CUME_DIST devuelve números en el intervalo 0 < x < = 1.

**Sintaxis:**

    CUME_DIST() 
        OVER (
            [PARTITION BY <identifier, > …[n]]
            ORDER BY <identifier, > …[n] [ASC|DESC] 
    ) AS <alias>

En el ejemplo siguiente se usa la función CUME_DIST para calcular el percentil de latencia para cada consulta en una vertical. 

    @result=
        SELECT 
            *,
            CUME_DIST() OVER(PARTITION BY Vertical ORDER BY Latency) AS CumeDist
        FROM @querylog;

Los resultados son:

| Consultar | Latency | Vertical | CumeDist |
| --- | --- | --- | --- |
| Durian |500 |Imagen |1 |
| Banana |300 |Imagen |0.666666666666667 |
| Cherry |300 |Imagen |0.666666666666667 |
| Durian |500 |Web |1 |
| Cherry |400 |Web |0.833333333333333 |
| Fig |300 |Web |0.666666666666667 |
| Fig |200 |Web |0,5 |
| Papaya |200 |Web |0,5 |
| Apple |100 |Web |0.166666666666667 |

Hay seis filas en la partición donde la clave de partición es "Web"

* Hay seis filas con un valor inferior o igual a 500, por lo que la CUME_DIST es igual a 6/6=1
* Hay cinco filas con un valor inferior o igual a 400, por lo que la CUME_DIST es igual a 5/6=0,83
* Hay cuatro filas con un valor inferior o igual a 300, por lo que la CUME_DIST es igual a 4/6=0,66
* Hay tres filas con un valor inferior o igual a 200, por lo que la CUME_DIST es igual a 3/6=0,5. Hay dos filas con el mismo valor de latencia.
* Hay una fila con un valor inferior o igual a 100, por lo que la CUME_DIST es igual a 1/6=0,16. 

**Notas de uso:**

* Los valores empatados siempre se evalúan como el mismo valor de la distribución acumulativa.
* Los valores NULL se tratan como los valores más bajos posibles.
* Es necesaria una cláusula ORDER BY para calcular CUME_DIST.
* CUME_DIST es similar a la función PERCENT_RANK.

Nota: no se permite la cláusula ORDER BY si la instrucción SELECT no va seguida de OUTPUT.

### <a name="percentrank"></a>PERCENT_RANK
PERCENT_RANK calcula el orden relativo de una fila dentro de un grupo de filas. PERCENT_RANK se usa para evaluar la importancia relativa de un valor dentro de un conjunto de filas o una partición. El intervalo de valores devueltos por PERCENT_RANK es mayor que 0 y menor o igual que 1. A diferencia de CUME_DIST, PERCENT_RANK es siempre 0 para la primera fila.

** Sintaxis:**

    PERCENT_RANK() 
        OVER (
            [PARTITION BY <identifier, > …[n]]
            ORDER BY <identifier, > …[n] [ASC|DESC] 
        ) AS <alias>

**Notas**

* La primera fila de cualquier conjunto tiene un PERCENT_RANK 0.
* Los valores NULL se tratan como los valores más bajos posibles.
* PERCENT_RANK requiere una cláusula ORDER BY.
* CUME_DIST es similar a la función PERCENT_RANK. 

En el ejemplo siguiente se usa la función PERCENT_RANK para calcular el percentil de latencia para cada consulta en una vertical. 

La cláusula PARTITION BY se especifica para particionar las filas en el conjunto de resultados por la vertical. La cláusula ORDER BY en la cláusula OVER ordena las filas de cada partición. 

El valor devuelto por la función PERCENT_RANK representa el orden de latencia de las consultas en una vertical como porcentaje. 

    @result=
        SELECT 
            *,
            PERCENT_RANK() OVER(PARTITION BY Vertical ORDER BY Latency) AS PercentRank
        FROM @querylog;

Los resultados son:

| Consultar | Latency: INT | Vertical | PercentRank |
| --- | --- | --- | --- |
| Banana |300 |Imagen |0 |
| Cherry |300 |Imagen |0 |
| Durian |500 |Imagen |1 |
| Apple |100 |Web |0 |
| Fig |200 |Web |0,2 |
| Papaya |200 |Web |0,2 |
| Fig |300 |Web |0.6 |
| Cherry |400 |Web |0.8 |
| Durian |500 |Web |1 |

### <a name="percentilecont--percentiledisc"></a>PERCENTILE_CONT y PERCENTILE_DISC
Estas dos funciones calculan un percentil basándose en una distribución continua o discreta de los valores de columna.

**Sintaxis:**

    [PERCENTILE_CONT | PERCENTILE_DISC] ( numeric_literal ) 
        WITHIN GROUP ( ORDER BY <identifier> [ ASC | DESC ] )
        OVER ( [ PARTITION BY <identifier,>…[n] ] ) AS <alias>

**numeric_literal**: el percentil que se va a calcular. El valor debe estar entre 0.0 y 1.0.

    WITHIN GROUP (ORDER BY <identifier> [ ASC | DESC ])

Especifica una lista de valores numéricos para ordenar y calcular el percentil. Solamente se permite un identificador de columna. La expresión debe evaluarse como un tipo numérico. No se permiten otros tipos de datos. El criterio de ordenación predeterminado es ascendente.

    OVER ([ PARTITION BY <identifier,>…[n] ] )

Divide el conjunto de filas de entrada en particiones según la clave de partición a la que se aplica la función percentil. Para obtener más información, consulte la sección de este documento sobre funciones de categoría.
Nota: se pasan por alto los valores NULL del conjunto de datos.

**PERCENTILE_CONT**: calcula un percentil basándose en una distribución continua del valor de columna. El resultado se interpola y es posible que no coincida con ninguno de los valores específicos de la columna. 

**PERCENTILE_DISC**: calcula el percentil basándose en una distribución discreta de los valores de columna. El resultado es igual a un valor específico de la columna. Es decir, PERCENTILE_DISC, en contraste con PERCENTILE_CONT, siempre devuelve un valor real (entrada original).

Puede ver cómo funcionan ambas en el ejemplo siguiente, que intenta encontrar la mediana (percentil = 0,50) para Latency en cada vertical.

    @result = 
        SELECT 
            Vertical, 
            Query,
            PERCENTILE_CONT(0.5) 
                WITHIN GROUP (ORDER BY Latency)
                OVER ( PARTITION BY Vertical ) AS PercentileCont50,
            PERCENTILE_DISC(0.5) 
                WITHIN GROUP (ORDER BY Latency) 
                OVER ( PARTITION BY Vertical ) AS PercentileDisc50 

        FROM @querylog;

Los resultados son:

| Consultar | Latency: INT | Vertical | PercentileCont50 | PercentilDisc50 |
| --- | --- | --- | --- | --- |
| Banana |300 |Imagen |300 |300 |
| Cherry |300 |Imagen |300 |300 |
| Durian |500 |Imagen |300 |300 |
| Apple |100 |Web |250 |200 |
| Fig |200 |Web |250 |200 |
| Papaya |200 |Web |250 |200 |
| Fig |300 |Web |250 |200 |
| Cherry |400 |Web |250 |200 |
| Durian |500 |Web |250 |200 |

Para PERCENTILE_CONT, dado que los valores se pueden interpolar, la mediana para Web es 250 aunque no haya ninguna consulta en la vertical Web con una latencia de 250. 

PERCENTILE_DISC no interpola valores, por lo que la mediana de Web es 200, que es un valor que se encuentra en las filas de entrada.

## <a name="see-also"></a>Consulte también
* [Desarrollo de scripts U-SQL mediante Data Lake Tools for Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
* [Uso de tutoriales interactivos de Análisis de Azure Data Lake](data-lake-analytics-use-interactive-tutorials.md)
* [Introducción al lenguaje U-SQL de Análisis de Azure Data Lake](data-lake-analytics-u-sql-get-started.md)



