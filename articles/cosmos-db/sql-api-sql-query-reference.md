---
title: Referencia de la sintaxis en las consultas SQL de Azure Cosmos DB | Microsoft Docs
description: "Documentación de referencia para el lenguaje de consulta SQL de Azure Cosmos DB."
services: cosmos-db
author: LalithaMV
manager: jhubbard
editor: mimig
documentationcenter: 
ms.assetid: 
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 10/18/2017
ms.author: laviswa
ms.openlocfilehash: 968f9e4d643228e02a1d09aae1b6ce82d6775f25
ms.sourcegitcommit: 6fb44d6fbce161b26328f863479ef09c5303090f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/10/2018
---
# <a name="azure-cosmos-db-sql-syntax-reference"></a>Referencia de la sintaxis SQL de Azure Cosmos DB

[!INCLUDE [cosmos-db-sql-api](../../includes/cosmos-db-sql-api.md)]

Azure Cosmos DB admite consultar documentos mediante una instancia de SQL (lenguaje de consulta estructurado) familiar, como gramática, en documentos JSON jerárquicos sin necesidad de esquemas explícitos ni de índices secundarios. En este tema se proporciona documentación de referencia para el lenguaje de consulta SQL, que es compatible con las cuentas de API de SQL.

Para ver un tutorial del lenguaje de consulta SQL, consulte [SQL queries for Azure Cosmos DB](sql-api-sql-query.md) (Consultas SQL para Azure Cosmos DB).  
  
Le invitamos a visitar también [Query Playground](http://www.documentdb.com/sql/demo), donde puede probar Azure Cosmos DB y ejecutar consultas SQL en nuestro conjunto de datos.  
  
## <a name="select-query"></a>Consulta SELECT  
Recupera documentos JSON de la base de datos. Es compatible con la evaluación de expresiones, las proyecciones, el filtrado y las combinaciones.  Las convenciones que se usan para describir las instrucciones SELECT se tabulan en la sección de convenciones de sintaxis.  
  
**Sintaxis**  
  
```
<select_query> ::=  
SELECT <select_specification>   
    [ FROM <from_specification>]   
    [ WHERE <filter_condition> ]  
    [ ORDER BY <sort_specification> ]  
```  
  
 **Comentarios:**  
  
 Consulte las siguientes secciones para más información sobre las cláusulas:  
  
-   [Cláusula SELECT](#bk_select_query)  
  
-   [Cláusula FROM](#bk_from_clause)  
  
-   [Cláusula WHERE](#bk_where_clause)  
  
-   [Cláusula ORDER BY](#bk_orderby_clause)  
  
Las cláusulas de la instrucción SELECT se deben ordenar como se indicó anteriormente. Se puede omitir cualquiera de las cláusulas opcionales. Pero cuando se usen, deben aparecer en el orden correcto.  
  
**Orden de procesamiento lógico de la instrucción SELECT**  
  
El orden en que se procesan las cláusulas es el siguiente:  

1.  [Cláusula FROM](#bk_from_clause)  
2.  [Cláusula WHERE](#bk_where_clause)  
3.  [Cláusula ORDER BY](#bk_orderby_clause)  
4.  [Cláusula SELECT](#bk_select_query)  

Tenga en cuenta que difiere del orden en que aparecen en la sintaxis. La ordenación es tal que todos los símbolos nuevos introducidos por una cláusula procesada son visibles y pueden utilizarse en las cláusulas que se procesen después. Por ejemplo, las cláusulas WHERE y SELECT pueden acceder a los alias declarados en una cláusula FROM.  

**Comentarios y caracteres de espacio en blanco**  

Todos los caracteres de espacio en blanco que no formen parte de una cadena o identificador entrecomillados no forman parte de la gramática del lenguaje y se omiten durante el análisis.  

El lenguaje de consulta admite comentarios del estilo de T-SQL como  

-   Instrucción SQL `-- comment text [newline]`.  

Aunque los comentarios y los caracteres de espacio en blanco no tienen significado en la gramática, deben utilizarse para separar tokens. Por ejemplo: `-1e5` es un token de un solo número, mientras que `: – 1 e5` es un token de menos seguido por el número 1 y el identificador e5.  

##  <a name="bk_select_query"></a> Cláusula SELECT  
Las cláusulas de la instrucción SELECT se deben ordenar como se indicó anteriormente. Se puede omitir cualquiera de las cláusulas opcionales. Pero cuando se usen, deben aparecer en el orden correcto.  

**Sintaxis**  
```  
SELECT <select_specification>  

<select_specification> ::=   
      '*'   
      | <object_property_list>   
      | VALUE <scalar_expression> [[ AS ] value_alias]  
  
<object_property_list> ::=   
{ <scalar_expression> [ [ AS ] property_alias ] } [ ,...n ]  
  
```  
  
 **Argumentos**  
  
 `<select_specification>`  
  
 Propiedades o valor que se van a seleccionar para los resultados.  
  
 `'*'`  
  
Especifica que se debe recuperar el valor sin cambios. En particular, si el valor procesado es un objeto, se recuperarán todas las propiedades.  
  
 `<object_property_list>`  
  
Especifica la lista de propiedades que se recuperan. Los valores devueltos serán objetos con las propiedades especificadas.  
  
`VALUE`  
  
Especifica que se debe recuperar el valor JSON en lugar del objeto JSON completo. Esto, a diferencia de `<property_list>`, no encapsula el valor proyectado en un objeto.  
  
`<scalar_expression>`  
  
Expresión que representa el valor que hay que calcular. Consulte la sección [Expresiones escalares](#bk_scalar_expressions) para más información.  
  
**Comentarios:**  
  
La sintaxis `SELECT *` solo es válida si la cláusula FROM ha declarado exactamente un alias. `SELECT *` proporciona una proyección de identidad, que puede resultar útil si no se necesitan proyecciones. SELECT * solo es válida si se especifica cláusula FROM y se introdujo solo un origen de entrada.  
  
Tenga en cuenta que `SELECT <select_list>` y `SELECT *` son código sintáctico y pueden expresarse también mediante instrucciones SELECT sencillas, como se muestra a continuación.  
  
1.  `SELECT * FROM ... AS from_alias ...`  
  
     equivale a:  
  
     `SELECT from_alias FROM ... AS from_alias ...`  
  
2.  `SELECT <expr1> AS p1, <expr2> AS p2,..., <exprN> AS pN [other clauses...]`  
  
     equivale a:  
  
     `SELECT VALUE { p1: <expr1>, p2: <expr2>, ..., pN: <exprN> }[other clauses...]`  
  
**Consulte también**  
  
[Expresiones escalares](#bk_scalar_expressions)  
[Cláusula SELECT](#bk_select_query)  
  
##  <a name="bk_from_clause"></a> Cláusula FROM  
Especifica el origen o la combinación de orígenes. La cláusula FROM es opcional. Si no se especifica, las demás cláusulas se continuarán ejecutando como si la cláusula FROM proporcionara un documento único.  
  
**Sintaxis**  
  
```  
FROM <from_specification>  
  
<from_specification> ::=   
        <from_source> {[ JOIN <from_source>][,...n]}  
  
<from_source> ::=   
          <collection_expression> [[AS] input_alias]  
        | input_alias IN <collection_expression>  
  
<collection_expression> ::=   
        ROOT   
     | collection_name  
     | input_alias  
     | <collection_expression> '.' property_name  
     | <collection_expression> '[' "property_name" | array_index ']'  
```  
  
**Argumentos**  
  
`<from_source>`  
  
Especifica un origen de datos, con o sin alias. Si no se especifica, se deducirá de `<collection_expression>` con las siguientes reglas:  
  
-   Si la expresión es un nombre de colección, se utilizará collection_name como alias.  
  
-   Si la expresión es `<collection_expression>`, se utilizará property_name como alias. Si la expresión es un nombre de colección, se utilizará collection_name como alias.  
  
AS `input_alias`.  
  
Especifica que `input_alias` es un conjunto de valores que devuelve la expresión de la colección subyacente.  
 
`input_alias` IN  
  
Especifica que `input_alias` debe representar el conjunto de valores obtenidos al recorrer en iteración todos los elementos de las matrices que devuelve la expresión de la colección subyacente. Se omite cualquier valor que la expresión de colección subyacente devuelva que no sea una matriz.  
  
`<collection_expression>`  
  
Especifica la expresión de colección que se usará para recuperar los documentos.  
  
`ROOT`  
  
Especifica que debe recuperarse ese documento predeterminado de la colección actualmente conectada.  
  
`collection_name`  
  
Especifica que debe recuperarse ese documento de la colección indicada. El nombre de la colección debe coincidir con el nombre de la colección conectada actualmente.  
  
`input_alias`  
  
Especifica que debe recuperarse ese documento desde otro origen que define el alias proporcionado.  
  
`<collection_expression> '.' property_`  
  
Especifica que debe recuperarse ese documento mediante el acceso a la propiedad `property_name` o el elemento de matriz array_index para todos los documentos que recupera una expresión de colección específica.  
  
`<collection_expression> '[' "property_name" | array_index ']'`  
  
Especifica que debe recuperarse ese documento mediante el acceso a la propiedad `property_name` o el elemento de matriz array_index para todos los documentos que recupera una expresión de colección específica.  
  
**Comentarios:**  
  
Todos los alias proporcionados o deducidos en `<from_source>(` deben ser únicos. La sintaxis de property_name de `<collection_expression>.` es la misma que la de `<collection_expression>' ['"property_name"']'`. Sin embargo, esta última puede usarse si un nombre de propiedad contiene caracteres que no sean identificadores.  
  
**Manejo de situaciones en las que faltan propiedades o elementos de matriz o en las que hay valores sin definir**  
  
Si una expresión de colección accede a propiedades o elementos de matriz y el valor no existe, ese valor se omitirá y no se seguirá procesando.  
  
**Ámbito de contexto de la expresión de la colección**  
  
El ámbito de las expresiones de colección puede ser de colección o de documento:  
  
-   Una expresión es de ámbito de colección si el origen subyacente de la expresión de colección es ROOT o `collection_name`. Este tipo de expresión representa un conjunto de documentos que se recuperan directamente de la colección y no dependen del procesamiento de otras expresiones de colección.  
  
-   Una expresión es de ámbito de documento si el origen subyacente de la expresión de colección es el valor `input_alias` introducido anteriormente en la consulta. Esta expresión representa un conjunto de documentos obtenidos al evaluar la expresión de colección en el ámbito de cada documento perteneciente al conjunto asociado a la colección del alias.  El conjunto resultante será una unión de los conjuntos obtenidos al evaluar la expresión de colección para cada uno de los documentos del conjunto subyacente.  
  
**Combinaciones**  
  
En la versión actual, Azure Cosmos DB admite combinaciones internas. Las funcionalidades de combinación adicionales estarán disponibles próximamente.

Las combinaciones internas derivan en un producto cruzado completo con los conjuntos participantes en la combinación. El resultado de una combinación de N es un conjunto de tuplas de N elementos, donde cada valor de la tupla se asocia con el conjunto del alias participante en la combinación, accesible mediante una referencia a ese alias en otras cláusulas.  
  
La evaluación de la combinación depende del ámbito del contexto de los conjuntos participantes:  
  
-  La combinación entre un conjunto de ámbito de colección A y otro B da como resultado un producto cruzado de todos los elementos de los conjuntos A y B.
  
-   La combinación entre un conjunto A y un conjunto de ámbito de documento B da como resultado una unión de todos los conjuntos obtenidos de la evaluación de B para cada documento del conjunto A.  
  
 En la versión actual, el procesador de consultas admite un máximo de una expresión de ámbito de colección.  
  
**Ejemplos de combinaciones:**  
  
Observemos la siguiente cláusula FROM: `<from_source1> JOIN <from_source2> JOIN ... JOIN <from_sourceN>`  
  
 Los orígenes definirán `input_alias1, input_alias2, …, input_aliasN`. Esta cláusula FROM devuelve un conjunto de N tuplas (tupla con N valores). Cada tupla tiene valores generados por sus respectivos conjuntos en iteración de todos los alias de colección.  
  
*Ejemplo de COMBINACIÓN 1, con 2 orígenes:*  
  
- `<from_source1>` tendrá ámbito de colección y representa el conjunto {A, B, C}.  
  
- `<from_source2>` tendrá ámbito de documento, hará referencia a input_alias1 y representará los conjuntos:  
  
    {1, 2} para `input_alias1 = A,`  
  
    {3} para `input_alias1 = B,`  
  
    {4, 5} para `input_alias1 = C,`  
  
- La cláusula FROM `<from_source1> JOIN <from_source2>` dará como resultado las siguientes tuplas:  
  
    (`input_alias1, input_alias2`):  
  
    `(A, 1), (A, 2), (B, 3), (C, 4), (C, 5)`  
  
*Ejemplo de COMBINACIÓN 2, con 3 orígenes:*  
  
- `<from_source1>` tendrá ámbito de colección y representa el conjunto {A, B, C}.  
  
- `<from_source2>` tendrá ámbito de documento, hará referencia a `input_alias1` y representará los conjuntos:  
  
    {1, 2} para `input_alias1 = A,`  
  
    {3} para `input_alias1 = B,`  
  
    {4, 5} para `input_alias1 = C,`  
  
- `<from_source3>` tendrá ámbito de documento, hará referencia a `input_alias2` y representará los conjuntos:  
  
    {100, 200} para `input_alias2 = 1,`  
  
    {300} para `input_alias2 = 3,`  
  
- La cláusula FROM `<from_source1> JOIN <from_source2> JOIN <from_source3>` dará como resultado las siguientes tuplas:  
  
    (input_alias1, input_alias2, input_alias3):  
  
    (A, 1, 100), (A, 1, 200), (B, 3, 300)  
  
> [!NOTE]
> Faltan tuplas para otros valores de `input_alias1` y `input_alias2`, para los cuales `<from_source3>` no devolvió ningún valor.  
  
*Ejemplo de COMBINACIÓN 3, con 3 orígenes:*  
  
- <from_source1> tendrá ámbito de colección y representa el conjunto {A, B, C}.  
  
- `<from_source1>` tendrá ámbito de colección y representa el conjunto {A, B, C}.  
  
- <from_source2> tendrá ámbito de documento, hará referencia a input_alias1 y representará los conjuntos:  
  
    {1, 2} para `input_alias1 = A,`  
  
    {3} para `input_alias1 = B,`  
  
    {4, 5} para `input_alias1 = C,`  
  
- `<from_source3>` tendrá el ámbito `input_alias1` y representará los conjuntos:  
  
    {100, 200} para `input_alias2 = A,`  
  
    {300} para `input_alias2 = C,`  
  
- La cláusula FROM `<from_source1> JOIN <from_source2> JOIN <from_source3>` dará como resultado las siguientes tuplas:  
  
    (`input_alias1, input_alias2, input_alias3`):  
  
    (A, 1, 100), (A, 1, 200), (A, 2, 100), (A, 2, 200), (C, 4, 300), (C, 5, 300)  
  
> [!NOTE]
> Esto dio como resultado un producto cruzado entre `<from_source2>` y `<from_source3>`, ya que ambos tienen como ámbito el mismo `<from_source1>`.  Esto dio como resultado 4 (2 x 2) tuplas con valor A, 0 tuplas con valor B (1 x 0) y 2 (2 x 1) tuplas con valor C.  
  
**Consulte también**  
  
 [Cláusula SELECT](#bk_select_query)  
  
##  <a name="bk_where_clause"></a> Cláusula WHERE  
 Especifica la condición de búsqueda para los documentos que devuelve la consulta.  
  
 **Sintaxis**  
  
```  
WHERE <filter_condition>  
<filter_condition> ::= <scalar_expression>  
  
```  
  
 **Argumentos**  
  
-   `<filter_condition>`  
  
     Especifica la condición que debe cumplirse para que se devuelvan los documentos.  
  
-   `<scalar_expression>`  
  
     Expresión que representa el valor que hay que calcular. Consulte la sección [Expresiones escalares](#bk_scalar_expressions) para más información.  
  
 **Comentarios:**  
  
 Para que el documento se devuelva, debe establecerse en true una expresión especificada como condición de filtro. Solo un valor booleano true cumplirá la condición, los demás valores: undefined, null, false, número, matriz u objeto, no cumplirán la condición.  
  
##  <a name="bk_orderby_clause"></a> Cláusula ORDER BY  
 Especifica el criterio de ordenación para los resultados que devuelve la consulta.  
  
 **Sintaxis**  
  
```  
ORDER BY <sort_specification>  
<sort_specification> ::= <sort_expression> [, <sort_expression>]  
<sort_expression> ::= <scalar_expression> [ASC | DESC]  
  
```  
  
 **Argumentos**  
  
-   `<sort_specification>`  
  
     Especifica una propiedad o una expresión para ordenar el conjunto de resultados de la consulta. Se puede especificar una columna de ordenación como alias de nombre o de columna.  
  
     Se pueden especificar varias columnas de ordenación. Los nombres de columna deben ser únicos. La secuencia de las columnas de ordenación en la cláusula ORDER BY define la organización del conjunto de resultados ordenado. Es decir, el conjunto de resultados se ordena en función de la primera propiedad, esa lista ordenada se ordena en función de la segunda propiedad y así sucesivamente.  
  
     Los nombres de columna indicados en la cláusula ORDER BY deben corresponderse con una columna de la lista seleccionada o con una que se defina en una tabla que se especifique en la cláusula FROM sin ambigüedades.  
  
-   `<sort_expression>`  
  
     Especifica una propiedad o expresión únicas para ordenar el conjunto de resultados de la consulta.  
  
-   `<scalar_expression>`  
  
     Consulte la sección [Expresiones escalares](#bk_scalar_expressions) para más información.  
  
-   `ASC | DESC`  
  
     Especifica que los valores de la columna especificada se deben ordenar en orden ascendente o descendente. ASC ordena los valores de menor a mayor. DESC ordena los valores de mayor a menos. ASC es el criterio de ordenación predeterminado. Los valores null se tratan como valores mínimos.  
  
 **Comentarios:**  
  
 Aunque la gramática de consulta admite varias propiedades para order by, el tiempo de ejecución de consulta de Azure Cosmos DB admite solo la ordenación por una propiedad y únicamente según los nombres de propiedad, es decir, no según las propiedades calculadas. La ordenación también requiere que la directiva de indexación incluya un índice de intervalo para la propiedad y el tipo especificado, con máxima precisión. Consulte la documentación de la directiva de indexación para más detalles.  
  
##  <a name="bk_scalar_expressions"></a> Expresiones escalares  
 Una expresión escalar es una combinación de símbolos y operadores que se puede evaluar para obtener un solo valor. Las expresiones simples pueden ser constantes, referencias de propiedad, referencias de elementos de matriz, referencias de alias o llamadas de función. Las expresiones simples se pueden combinar en expresiones complejas con operadores.  
  
 Para más información sobre los valores que puede tener una expresión escalar, consulte la sección [Constantes](#bk_constants).  
  
 **Sintaxis**  
  
```  
<scalar_expression> ::=  
       <constant>   
     | input_alias   
     | parameter_name  
     | <scalar_expression>.property_name  
     | <scalar_expression>'['"property_name"|array_index']'  
     | unary_operator <scalar_expression>  
     | <scalar_expression> binary_operator <scalar_expression>    
     | <scalar_expression> ? <scalar_expression> : <scalar_expression>  
     | <scalar_function_expression>  
     | <create_object_expression>   
     | <create_array_expression>  
     | (<scalar_expression>)   
  
<scalar_function_expression> ::=  
        'udf.' Udf_scalar_function([<scalar_expression>][,…n])  
        | builtin_scalar_function([<scalar_expression>][,…n])  
  
<create_object_expression> ::=  
   '{' [{property_name | "property_name"} : <scalar_expression>][,…n] '}'  
  
<create_array_expression> ::=  
   '[' [<scalar_expression>][,…n] ']'  
  
```  
  
 **Argumentos**  
  
-   `<constant>`  
  
     Representa un valor constante. Consulte la sección [Constantes](#bk_constants) para más información.  
  
-   `input_alias`  
  
     Representa un valor definido por `input_alias`, introducido en la cláusula `FROM`.  
    Este valor nunca será **undefined**, los valores **undefined** se omiten de la entrada.  
  
-   `<scalar_expression>.property_name`  
  
     Representa un valor de propiedad de un objeto. Si la propiedad no existe o se hace referencia a ella con un valor que no es un objeto, la expresión se evalúa como valor **undefined**.  
  
-   `<scalar_expression>'['"property_name"|array_index']'`  
  
     Representa un valor de propiedad con nombre `property_name` o de elemento de matriz con el índice `array_index` de un objeto o matriz. Si la propiedad no existe o se hace referencia a ella con un valor que no es un objeto, la expresión se evalúa como valor undefined.  
  
-   `unary_operator <scalar_expression>`  
  
     Representa un operador que se aplica a un único valor. Consulte la sección [Operadores](#bk_operators) para más información.  
  
-   `<scalar_expression> binary_operator <scalar_expression>`  
  
     Representa un operador que se aplica a dos valores. Consulte la sección [Operadores](#bk_operators) para más información.  
  
-   `<scalar_function_expression>`  
  
     Representa un valor definido por el resultado de una llamada de función.  
  
-   `udf_scalar_function`  
  
     Función escalar definida por el nombre del usuario.  
  
-   `builtin_scalar_function`  
  
     Nombre de la función escalar incorporada.  
  
-   `<create_object_expression>`  
  
     Representa un valor obtenido al crear un objeto con propiedades especificadas y sus valores.  
  
-   `<create_array_expression>`  
  
     Representa un valor obtenido al crear una matriz con valores especificados como elementos.  
  
-   `parameter_name`  
  
     Representa un valor del nombre de parámetro especificado. Los nombres de parámetro deben tener un único @ como primer carácter.  
  
 **Comentarios:**  
  
 Todos los argumentos deben estar definidos al llamar a una función escalar incorporada o definida por el usuario. Si alguno de los argumentos no está definido, no se llamará a la función y el resultado será undefined.  
  
 Al crear un objeto, se omitirá cualquier propiedad a la que se le haya asignado un valor undefined y no se incluirá en el objeto creado.  
  
 Al crear una matriz, se omitirá cualquier elemento al que se le haya asignado un valor **undefined** y no se incluirá en el objeto creado. Esto hará que el siguiente elemento definido ocupe su lugar de manera que la matriz creada no tenga índices omitidos.  
  
##  <a name="bk_operators"></a> Operadores  
 En esta sección se describen los operadores compatibles. Cada operador puede asignarse exactamente a una categoría.  
  
 Consulte la siguiente tabla, **Categorías de operadores**, para más información acerca del control de los valores **undefined**, los requisitos de tipo de valores de entrada y el control de los valores con tipos no coincidentes.  
  
 **Categorías de operadores:**  
  
|**Categoría**|**Detalles**|  
|-|-|  
|**aritméticos**|El operador espera que las entradas sean números. La salida también es un número. Si alguna de las entradas es **undefined** o de un tipo que no sea un número, el resultado es **undefined**.|  
|**bit a bit**|El operador espera que las entradas sean números enteros con signo de 32 bits. La salida también es un número entero con signo de 32 bits.<br /><br /> Los valores que no sean enteros se redondean. Los valores positivos se redondean a la baja y los negativos, a la alta.<br /><br /> Se convertirá cualquier valor que esté fuera del intervalo de enteros de 32 bits, aprovechando los últimos 32 bits de su notación de complemento a dos.<br /><br /> Si alguna de las entradas es **undefined** o de un tipo que no sea un número, el resultado es **undefined**.<br /><br /> **Nota:** El comportamiento anterior es compatible con el comportamiento del operador bit a bit de JavaScript.|  
|**lógicos**|El operador espera que las entradas sean booleanos. La salida también es un booleano.<br />Si alguna de las entradas es **undefined** o de un tipo que no sea un booleano, el resultado es **undefined**.|  
|**de comparación**|El operador espera que las entradas sean del mismo tipo y sin valores undefined. La salida es un booleano.<br /><br /> Si alguna de las entradas es **undefined** o tienen distintos tipos, el resultado es **undefined**.<br /><br /> Consulte la tabla **Ordenación de los valores para la comparación** para conocer los detalles de ordenación de los valores.|  
|**cadena**|El operador espera que las entradas sean cadenas. La salida también es una cadena.<br />Si alguna de las entradas es **undefined** o de un tipo que no sea una cadena, el resultado es **undefined**.|  
  
 **Operadores unarios:**  
  
|**Name**|**Operador**|**Detalles**|  
|-|-|-|  
|**aritméticos**|+<br /><br /> -|Devuelve el valor numérico.<br /><br /> Negativos bit a bit. Devuelve el valor numérico negativo.|  
|**bit a bit**|~|Complemento de uno. Devuelve un complemento de un valor numérico.|  
|**lógicos**|**NOT**|Negación. Devuelve el valor booleano negativo.|  
  
 **Operadores binarios:**  
  
|**Name**|**Operador**|**Detalles**|  
|-|-|-|  
|**aritméticos**|+<br /><br /> -<br /><br /> *<br /><br /> /<br /><br /> %|Suma.<br /><br /> Resta.<br /><br /> Multiplicación.<br /><br /> División.<br /><br /> Modulación.|  
|**bit a bit**|&#124;<br /><br /> &<br /><br /> ^<br /><br /> <<<br /><br /> >><br /><br /> >>>|OR bit a bit.<br /><br /> AND bit a bit.<br /><br /> XOR bit a bit.<br /><br /> Desplazamiento a la izquierda.<br /><br /> Desplazamiento a la derecha.<br /><br /> Desplazamiento a la derecha con relleno de ceros.|  
|**lógicos**|**AND**<br /><br /> **O bien**|Conjunción lógica. Devuelve **true** si ambos argumentos son **true**, devuelve **false** en caso contrario.<br /><br /> Conjunción lógica. Devuelve **true** si ambos argumentos son **true**, devuelve **false** en caso contrario.|  
|**de comparación**|**=**<br /><br /> **!=, &lt;&gt;**<br /><br /> **>**<br /><br /> **>=**<br /><br /> **<**<br /><br /> **<=**<br /><br /> **??**|Igual a. Devuelve **true** si ambos argumentos son iguales, en caso contrario, devuelve **false**.<br /><br /> Diferente de. Devuelve **true** si ambos argumentos no son iguales, en caso contrario, devuelve **false**.<br /><br /> Mayor que. Devuelve **true** si el primer argumento es mayor que el segundo, en caso contrario, devuelve **false**.<br /><br /> Mayor o igual que. Devuelve **true** si el primer argumento es mayor o igual que el segundo, en caso contrario, devuelve **false**.<br /><br /> Menor que. Devuelve **true** si el primer argumento es menor que el segundo, en caso contrario, devuelve **false**.<br /><br /> Menor o igual que. Devuelve **true** si el primer argumento es menor o igual que el segundo, en caso contrario, devuelve **false**.<br /><br /> Fusionar. Devuelve el segundo argumento si el primero es un valor **undefined**.|  
|**String**|**&amp;#124;&amp;#124;**|Concatenación. Devuelve una concatenación de ambos argumentos.|  
  
 **Operadores ternarios:**  
  
|Operador ternario|?|Devuelve el segundo argumento si el primero se evalúa como **true**, en caso contrario, devuelve el tercer argumento.|  
|-|-|-|  
  
 **Ordenación de los valores para la comparación**  
  
|**Tipo**|**Orden de los valores**|  
|-|-|  
|**Undefined**|No comparables.|  
|**Null**|Valor único: **null**|  
|**Number**|Número real natural.<br /><br /> El valor de infinito negativo es menor que cualquier otro valor numérico.<br /><br /> El valor de infinito positivo es mayor que cualquier otro valor numérico. El valor **NaN** no es comparable. La comparación con **NaN** dará como resultado el valor **undefined**.|  
|**String**|Orden lexicográfico.|  
|**Array**|Ningún orden, pero equitativo.|  
|**Object**|Ningún orden, pero equitativo.|  
  
 **Comentarios:**  
  
 En Azure Cosmos DB, a menudo no se conocen los tipos de los valores hasta que se recuperan de la base de datos. Para admitir la ejecución de consultas de forma eficaz, la mayoría de los operadores tienen requisitos de tipo estrictos. Además, los operadores por sí mismos no realizan conversiones implícitas.  
  
 Esto significa que una consulta como: SELECT * FROM ROOT r WHERE r.Age = 21 solo devolverá documentos con la propiedad Age igual al número 21. Los documentos con la propiedad Age igual a la cadena "21" o a la cadena "0021" no coincidirán, ya que la expresión "21" = 21 se cuenta como undefined. Esto permite un mejor uso de los índices, ya que la búsqueda de un valor específico (es decir, el número 21) es más rápida que la búsqueda de un número indefinido de coincidencias posibles (es decir, el número 21 o las cadenas "21", "021", "21,0", etc.). Esto difiere de la evaluación de los distintos tipos de valores de los operadores en JavaScript.  
  
 **Comparación e igualdad de objetos y matrices**  
  
 La comparación de valores de Array u Object mediante los operadores de intervalo (>, > =, <, < =) dará como resultado undefined, ya que no hay orden definido en los valores de Object o Array. Sin embargo, se admite con operadores de igualdad y desigualdad (=,! =, <>) y los valores se compararán estructuralmente.  
  
 Las matrices son iguales si ambas tienen el mismo número de elementos y los elementos de las posiciones coincidentes también son iguales. Si la comparación de cualquier par de resultados de elementos es undefined, el resultado de la comparación de matrices es undefined.  
  
 Los objetos son iguales si tienen las mismas propiedades definidas y si los valores de las propiedades coincidentes también son iguales. Si la comparación de cualquier par de valores de propiedad es undefined, el resultado de la comparación de objetos es undefined.  
  
##  <a name="bk_constants"></a> Constantes  
 Una constante, también conocida como valor literal o escalar, es un símbolo que representa un valor de datos específicos. El formato de una constante depende del tipo de datos del valor que representa.  
  
 **Tipos de datos escalares admitidos:**  
  
|**Tipo**|**Orden de los valores**|  
|-|-|  
|**Undefined**|Valor único: **undefined**|  
|**Null**|Valor único: **null**|  
|**Boolean**|Valores: **false**, **true**.|  
|**Number**|Número de punto flotante de precisión doble, estándar IEEE 754.|  
|**String**|Secuencia de cero o más caracteres Unicode. Las cadenas deben ir entre comillas sencillas o dobles.|  
|**Array**|Secuencia de cero o más elementos. Cada elemento puede ser un valor de cualquier tipo de datos escalar, excepto Undefined.|  
|**Object**|Conjunto desordenado de cero o más pares nombre/valor. Nombre es una cadena Unicode, el valor puede ser de cualquier tipo de datos escalar, excepto **Undefined**.|  
  
 **Sintaxis**  
  
```  
<constant> ::=  
   <undefined_constant>  
     | <null_constant>   
     | <boolean_constant>   
     | <number_constant>   
     | <string_constant>   
     | <array_constant>   
     | <object_constant>   
  
<undefined_constant> ::= undefined  
  
<null_constant> ::= null  
  
<boolean_constant> ::= false | true  
  
<number_constant> ::= decimal_literal | hexadecimal_literal  
  
<string_constant> ::= string_literal  
  
<array_constant> ::=  
    '[' [<constant>][,...n] ']'  
  
<object_constant> ::=   
   '{' [{property_name | "property_name"} : <constant>][,...n] '}'  
  
```  
  
 **Argumentos**  
  
1.  `<undefined_constant>; undefined`  
  
     Representa un valor sin definir de tipo Undefined.  
  
2.  `<null_constant>; null`  
  
     Representa el valor **null** de tipo **Null**.  
  
3.  `<boolean_constant>`  
  
     Representa la constante de tipo booleano.  
  
4.  `false`  
  
     Representa el valor **false** de tipo booleano.  
  
5.  `true`  
  
     Representa el valor **true** de tipo booleano.  
  
6.  `<number_constant>`  
  
     Representa una constante.  
  
7.  `decimal_literal`  
  
     Los literales decimales son números representados mediante notación decimal o científica.  
  
8.  `hexadecimal_literal`  
  
     Los literales hexadecimales son números representados mediante el prefijo "0x-" seguido de uno o más dígitos hexadecimales.  
  
9. `<string_constant>`  
  
     Representa una constante de tipo cadena.  
  
10. `string _literal`  
  
     Los literales de cadena son cadenas Unicode representadas por una secuencia de cero o varios caracteres Unicode o secuencias de escape. Los literales de cadena se encierran entre comillas simples (apóstrofo: ') o comillas dobles (comillas: ").  
  
 Se permiten las secuencias de escape siguientes:  
  
|**Secuencia de escape**|**Descripción**|**Carácter Unicode**|  
|-|-|-|  
|\\'|apóstrofo (')|U + 0027|  
|\\"|comillas dobles (")|U + 0022|  
|\\\|barra inclinada inversa (\\)|U + 005C|  
|\\/|barra inclinada (/)|U + 002F|  
|\b|retroceso|U + 0008|  
|\f|avance de página|U + 000C|  
|\n|avance de línea|U + 000A|  
|\r|retorno de carro|U + 000D|  
|\t|tabulador|U + 0009|  
|\uXXXX|Carácter Unicode definidos por 4 dígitos hexadecimales.|U + XXXX|  
  
##  <a name="bk_query_perf_guidelines"></a> Guías de rendimiento de las consultas  
 Para que una consulta se ejecute de forma eficaz con una colección extensa, debe utilizar filtros que puedan obtenerse a través de uno o más índices.  
  
 Para la búsqueda de índices, se considerarán los filtros siguientes:  
  
-   Utilice el operador de igualdad (=) con expresiones de ruta de acceso de documentos y constantes.  
  
-   Utilice los operadores de intervalo (<, \<=, >, >=) con expresiones de ruta de acceso de documentos y constantes numéricas.  
  
-   Una expresión de ruta de acceso de documento es cualquier expresión que identifica una ruta de acceso constante en los documentos de la colección de la base de datos a la que se hace referencia.  
  
 **Expresión de ruta de acceso de documento**  
  
 Las expresiones de ruta de acceso de documento son expresiones que evalúan la ruta de una propiedad o un indexador de matrices en un documento procedente de la colección de bases de datos. Esta ruta de acceso sirve para identificar directamente la ubicación de los valores a los que se hace referencia en un filtro en los documentos de la colección de la base de datos.  
  
 Para que una expresión se considere una expresión de ruta de acceso de documento, debe:  
  
1.  Hacer referencia directamente a la raíz de la colección.  
  
2.  Hacer referencia al indexador de matrices de propiedad o constantes de alguna expresión de ruta de acceso de documento.  
  
3.  Hacer referencia a un alias, que represente alguna expresión de ruta de acceso de documento.  
  
     **Convenciones de sintaxis**  
  
     En la tabla siguiente se indican las convenciones utilizadas para describir la sintaxis en la siguiente referencia de SQL.  
  
    |**Convención**|**Se usa para**|  
    |-|-|    
    |MAYÚSCULAS|Palabras clave sin distinción entre mayúsculas y minúsculas.|  
    |minúsculas|Palabras clave con distinción entre mayúsculas y minúsculas.|  
    |\<noterminal&gt;|Elemento no terminal, se define por separado.|  
    |\<noterminal&gt; ::=|Definición de la sintaxis del elemento no terminal.|  
    |otro_terminal|Terminal (token), se describe detalladamente en palabras.|  
    |identificador|Identificador. Solo admite los caracteres siguientes:, a-z, A-Z, 0-9 _El primer carácter no puede ser un dígito.|  
    |"cadena"|Cadena entrecomillada. Permite cualquier cadena válida. Vea la descripción de string_literal.|  
    |'símbolo'|Símbolo literal que forma parte de la sintaxis.|  
    |&#124; (barra vertical)|Alternativas para los elementos de la sintaxis. Puede usar solo uno de los elementos especificados.|  
    |[] /(corchetes)|Los corchetes contienen uno o varios elementos opcionales.|  
    |[, ...n]|Indica que el elemento anterior se puede repetir un número n de veces. Los elementos se separan por comas.|  
    |[ ...n]|Indica que el elemento anterior se puede repetir un número n de veces. Los elementos se separan por espacios en blanco.|  
  
##  <a name="bk_built_in_functions"></a> Funciones integradas  
 Azure Cosmos DB proporciona muchas funciones de SQL integradas. Las categorías de las funciones integradas aparecen a continuación.  
  
|Función|DESCRIPCIÓN|  
|--------------|-----------------|  
|[Funciones matemáticas](#bk_mathematical_functions)|Las funciones matemáticas realizan un cálculo, basado normalmente en valores de entrada proporcionados como argumentos, y devuelven un valor numérico.|  
|[Funciones de comprobación de tipos](#bk_type_checking_functions)|Las funciones de comprobación de tipos permiten comprobar el tipo de una expresión dentro de consultas SQL.|  
|[Funciones de cadena](#bk_string_functions)|Las siguientes funciones de cadena realizan una operación sobre un valor de entrada de cadena y devuelven una cadena, un valor numérico o un booleano.|  
|[Funciones de matriz](#bk_array_functions)|Las siguientes funciones de matriz realizan una operación en un valor de entrada de matriz y devolver un valor numérico, booleano o de matriz.|  
|[Funciones espaciales](#bk_spatial_functions)|Las siguientes funciones espaciales realizan una operación en un valor de entrada de objeto espacial y devuelven un valor numérico o un booleano.|  
  
###  <a name="bk_mathematical_functions"></a> Funciones matemáticas  
 Las siguientes funciones realizan un cálculo, basado normalmente en valores de entrada que se proporcionan como argumentos, y devuelven un valor numérico.  
  
||||  
|-|-|-|  
|[ABS](#bk_abs)|[ACOS](#bk_acos)|[ASIN](#bk_asin)|  
|[ATAN](#bk_atan)|[ATN2](#bk_atn2)|[CEILING](#bk_ceiling)|  
|[COS](#bk_cos)|[COT](#bk_cot)|[DEGREES](#bk_degrees)|  
|[EXP](#bk_exp)|[FLOOR](#bk_floor)|[LOG](#bk_log)|  
|[LOG10](#bk_log10)|[PI](#bk_pi)|[POWER](#bk_power)|  
|[RADIANS](#bk_radians)|[ROUND](#bk_round)|[SIN](#bk_sin)|  
|[SQRT](#bk_sqrt)|[SQUARE](#bk_square)|[SIGN](#bk_sign)|  
|[TAN](#bk_tan)|[TRUNC](#bk_trunc)||  
  
####  <a name="bk_abs"></a> ABS  
 Devuelve el valor absoluto (positivo) de la expresión numérica especificada.  
  
 **Sintaxis**  
  
```  
ABS (<numeric_expression>)  
```  
  
 **Argumentos**  
  
-   `numeric_expression`  
  
     Es una expresión numérica.  
  
 **Tipos de valor devuelto**  
  
 Devuelve una expresión numérica.  
  
 **Ejemplos**  
  
 En el ejemplo siguiente se muestran los resultados de usar la función ABS en tres números distintos.  
  
```  
SELECT ABS(-1), ABS(0), ABS(1)  
```  
  
 El conjunto de resultados es el siguiente:  
  
```  
[{$1: 1, $2: 0, $3: 1}]  
```  
  
####  <a name="bk_acos"></a> ACOS  
 Devuelve el ángulo, en radianes, cuyo coseno es la expresión numérica especificada; también se denomina arcocoseno.  
  
 **Sintaxis**  
  
```  
ACOS(<numeric_expression>)  
```  
  
 **Argumentos**  
  
-   `numeric_expression`  
  
     Es una expresión numérica.  
  
 **Tipos de valor devuelto**  
  
 Devuelve una expresión numérica.  
  
 **Ejemplos**  
  
 El siguiente ejemplo devuelve el arcocoseno de -1:  
  
```  
SELECT ACOS(-1)  
```  
  
 El conjunto de resultados es el siguiente:  
  
```  
[{"$1": 3.1415926535897931}]  
```  
  
####  <a name="bk_asin"></a> ASIN  
 Devuelve el ángulo, en radianes, cuyo seno es la expresión numérica especificada. También se denomina arcoseno.  
  
 **Sintaxis**  
  
```  
ASIN(<numeric_expression>)  
```  
  
 **Argumentos**  
  
-   `numeric_expression`  
  
     Es una expresión numérica.  
  
 **Tipos de valor devuelto**  
  
 Devuelve una expresión numérica.  
  
 **Ejemplos**  
  
 El siguiente ejemplo devuelve el arcoseno de -1:  
  
```  
SELECT ASIN(-1)  
```  
  
 El conjunto de resultados es el siguiente:  
  
```  
[{"$1": -1.5707963267948966}]  
```  
  
####  <a name="bk_atan"></a> ATAN  
 Devuelve el ángulo, en radianes, cuya tangente es la expresión numérica especificada. También se denomina arcotangente.  
  
 **Sintaxis**  
  
```  
ATAN(<numeric_expression>)  
```  
  
 **Argumentos**  
  
-   `numeric_expression`  
  
     Es una expresión numérica.  
  
 **Tipos de valor devuelto**  
  
 Devuelve una expresión numérica.  
  
 **Ejemplos**  
  
 El siguiente ejemplo devuelve la arcotangente del valor especificado.  
  
```  
SELECT ATAN(-45.01)  
```  
  
 El conjunto de resultados es el siguiente:  
  
```  
[{"$1": -1.5485826962062663}]  
```  
  
####  <a name="bk_atn2"></a> ATN2  
 Devuelve el valor principal de la arcotangente de y/x, expresado en radianes.  
  
 **Sintaxis**  
  
```  
ATN2(<numeric_expression>, <numeric_expression>)  
```  
  
 **Argumentos**  
  
-   `numeric_expression`  
  
     Es una expresión numérica.  
  
 **Tipos de valor devuelto**  
  
 Devuelve una expresión numérica.  
  
 **Ejemplos**  
  
 En el siguiente ejemplo se calcula ATN2 para los componentes x e y especificados.  
  
```  
SELECT ATN2(35.175643, 129.44)  
```  
  
 El conjunto de resultados es el siguiente:  
  
```  
[{"$1": 1.3054517947300646}]  
```  
  
####  <a name="bk_ceiling"></a> CEILING  
 Devuelve el valor entero más pequeño mayor o igual que la expresión numérica especificada.  
  
 **Sintaxis**  
  
```  
CEILING (<numeric_expression>)  
```  
  
 **Argumentos**  
  
-   `numeric_expression`  
  
     Es una expresión numérica.  
  
 **Tipos de valor devuelto**  
  
 Devuelve una expresión numérica.  
  
 **Ejemplos**  
  
 En el ejemplo siguiente se muestran valores numéricos positivos, negativos y cero con la función CEILING.  
  
```  
SELECT CEILING(123.45), CEILING(-123.45), CEILING(0.0)  
```  
  
 El conjunto de resultados es el siguiente:  
  
```  
[{$1: 124, $2: -123, $3: 0}]  
```  
  
####  <a name="bk_cos"></a> COS  
 Devuelve el coseno trigonométrico del ángulo especificado, en radianes, en la expresión especificada.  
  
 **Sintaxis**  
  
```  
COS(<numeric_expression>)  
```  
  
 **Argumentos**  
  
-   `numeric_expression`  
  
     Es una expresión numérica.  
  
 **Tipos de valor devuelto**  
  
 Devuelve una expresión numérica.  
  
 **Ejemplos**  
  
 En el ejemplo siguiente se calcula el coseno del ángulo especificado.  
  
```  
SELECT COS(14.78)  
```  
  
 El conjunto de resultados es el siguiente:  
  
```  
[{"$1": -0.59946542619465426}]  
```  
  
####  <a name="bk_cot"></a> COT  
 Devuelve la cotangente trigonométrica del ángulo especificado, en radianes, en la expresión numérica especificada.  
  
 **Sintaxis**  
  
```  
COT(<numeric_expression>)  
```  
  
 **Argumentos**  
  
-   `numeric_expression`  
  
     Es una expresión numérica.  
  
 **Tipos de valor devuelto**  
  
 Devuelve una expresión numérica.  
  
 **Ejemplos**  
  
 En el ejemplo siguiente se calcula la cotangente del ángulo especificado.  
  
```  
SELECT COT(124.1332)  
```  
  
 El conjunto de resultados es el siguiente:  
  
```  
[{"$1": -0.040311998371148884}]  
```  
  
####  <a name="bk_degrees"></a> DEGREES  
 Devuelve el ángulo correspondiente en grados de un ángulo especificado en radianes.  
  
 **Sintaxis**  
  
```  
DEGREES (<numeric_expression>)  
```  
  
 **Argumentos**  
  
-   `numeric_expression`  
  
     Es una expresión numérica.  
  
 **Tipos de valor devuelto**  
  
 Devuelve una expresión numérica.  
  
 **Ejemplos**  
  
 En el ejemplo siguiente se devuelven los grados de un ángulo de pi/2 radianes.  
  
```  
SELECT DEGREES(PI()/2)  
```  
  
 El conjunto de resultados es el siguiente:  
  
```  
[{"$1": 90}]  
```  
  
####  <a name="bk_floor"></a> FLOOR  
 Devuelve el valor entero más grande menor o igual que la expresión numérica especificada.  
  
 **Sintaxis**  
  
```  
FLOOR (<numeric_expression>)  
```  
  
 **Argumentos**  
  
-   `numeric_expression`  
  
     Es una expresión numérica.  
  
 **Tipos de valor devuelto**  
  
 Devuelve una expresión numérica.  
  
 **Ejemplos**  
  
 En el ejemplo siguiente se muestran valores numéricos positivos, negativos y cero con la función FLOOP.  
  
```  
SELECT FLOOR(123.45), FLOOR(-123.45), FLOOR(0.0)  
```  
  
 El conjunto de resultados es el siguiente:  
  
```  
[{$1: 123, $2: -124, $3: 0}]  
```  
  
####  <a name="bk_exp"></a> EXP  
 Devuelve el valor exponencial de la expresión numérica especificada.  
  
 **Sintaxis**  
  
```  
EXP (<numeric_expression>)  
```  
  
 **Argumentos**  
  
-   `numeric_expression`  
  
     Es una expresión numérica.  
  
 **Tipos de valor devuelto**  
  
 Devuelve una expresión numérica.  
  
 **Comentarios:**  
  
 La constante **e** (2,718281 …) es la base de los logaritmos naturales.  
  
 El exponente de un número es la constante **e** elevada a la potencia del número. Por ejemplo EXP(1,0) = e^1,0 = 2,718 281 828 459 05 y EXP(10) = e^10 = 22 026,465 794 806 7.  
  
 El valor exponencial del logaritmo natural de un número es el mismo número: EXP (LOG [n]) = n. Y el logaritmo natural del valor exponencial de un número es el mismo número: LOG (EXP [n]) = n.  
  
 **Ejemplos**  
  
 En el ejemplo siguiente se declara una variable y se devuelve el valor exponencial de la variable especificada (10).  
  
```  
SELECT EXP(10)  
```  
  
 El conjunto de resultados es el siguiente:  
  
```  
[{$1: 22026.465794806718}]  
```  
  
 En el ejemplo siguiente se devuelve el valor exponencial del logaritmo natural de 20 y el logaritmo natural del valor exponencial de 20. Dado que estas funciones son inversas entre sí, el valor devuelto con redondeo para las operaciones matemáticas de punto flotante en ambos casos es 20.  
  
```  
SELECT EXP(LOG(20)), LOG(EXP(20))  
```  
  
 El conjunto de resultados es el siguiente:  
  
```  
[{$1: 19.999999999999996, $2: 20}]  
```  
  
####  <a name="bk_log"></a> LOG  
 Devuelve el algoritmo natural de la expresión numérica especificada.  
  
 **Sintaxis**  
  
```  
LOG (<numeric_expression> [, <base>])  
```  
  
 **Argumentos**  
  
-   `numeric_expression`  
  
     Es una expresión numérica.  
  
-   `base`  
  
     Argumento numérico opcional que establece la base del logaritmo.  
  
 **Tipos de valor devuelto**  
  
 Devuelve una expresión numérica.  
  
 **Comentarios**  
  
 De forma predeterminada, LOG() devuelve el logaritmo natural. Puede cambiar la base del logaritmo a otro valor mediante el parámetro base opcional.  
  
 El logaritmo natural es el logaritmo en base **e**, donde **e** es una constante irracional aproximadamente igual a 2,718 281 828.  
  
 El logaritmo natural del valor exponencial de un número es el mismo número: LOG (EXP [n]) = n. Y el valor exponencial del logaritmo natural de un número es el mismo número: EXP (LOG [n]) = n.  
  
 **Ejemplos**  
  
 En el ejemplo siguiente se declara una variable y se devuelve el valor logarítmico de la variable especificada (10).  
  
```  
SELECT LOG(10)  
```  
  
 El conjunto de resultados es el siguiente:  
  
```  
[{$1: 2.3025850929940459}]  
```  
  
 En el ejemplo siguiente se calcula el logaritmo del exponente de un número.  
  
```  
SELECT EXP(LOG(10))  
```  
  
 El conjunto de resultados es el siguiente:  
  
```  
[{$1: 10.000000000000002}]  
```  
  
####  <a name="bk_log10"></a> LOG10  
 Devuelve el logaritmo de base 10 de la expresión numérica especificada.  
  
 **Sintaxis**  
  
```  
LOG10 (<numeric_expression>)  
```  
  
 **Argumentos**  
  
-   `numeric_expression`  
  
     Es una expresión numérica.  
  
 **Tipos de valor devuelto**  
  
 Devuelve una expresión numérica.  
  
 **Comentarios:**  
  
 Las funciones LOG10 y POWER tienen una relación inversa. Por ejemplo, 10 ^ LOG10 (n) = n.  
  
 **Ejemplos**  
  
 En el ejemplo siguiente se declara una variable y se devuelve el valor de LOG10 de la variable especificada (100).  
  
```  
SELECT LOG10(100)  
```  
  
 El conjunto de resultados es el siguiente:  
  
```  
[{$1: 2}]  
```  
  
####  <a name="bk_pi"></a> PI  
 Devuelve el valor constante de PI.  
  
 **Sintaxis**  
  
```  
PI ()  
```  
  
 **Argumentos**  
  
-   `numeric_expression`  
  
     Es una expresión numérica.  
  
 **Tipos de valor devuelto**  
  
 Devuelve una expresión numérica.  
  
 **Ejemplos**  
  
 El siguiente ejemplo devuelve el valor de pi.  
  
```  
SELECT PI()  
```  
  
 El conjunto de resultados es el siguiente:  
  
```  
[{"$1": 3.1415926535897931}]  
```  
  
####  <a name="bk_power"></a> POWER  
 Devuelve el valor de la expresión especificada a la potencia especificada.  
  
 **Sintaxis**  
  
```  
POWER (<numeric_expression>, <y>)  
```  
  
 **Argumentos**  
  
-   `numeric_expression`  
  
     Es una expresión numérica.  
  
-   `y`  
  
     Es la potencia a la que se eleva `numeric_expression`.  
  
 **Tipos de valor devuelto**  
  
 Devuelve una expresión numérica.  
  
 **Ejemplos**  
  
 En el ejemplo siguiente se muestra cómo elevar un número a la potencia de 3 (el cubo del número).  
  
```  
SELECT POWER(2, 3), POWER(2.5, 3)  
```  
  
 El conjunto de resultados es el siguiente:  
  
```  
[{$1: 8, $2: 15.625}]  
```  
  
####  <a name="bk_radians"></a> RADIANS  
 Devuelve radianes cuando se especifica una expresión numérica en grados.  
  
 **Sintaxis**  
  
```  
RADIANS (<numeric_expression>)  
```  
  
 **Argumentos**  
  
-   `numeric_expression`  
  
     Es una expresión numérica.  
  
 **Tipos de valor devuelto**  
  
 Devuelve una expresión numérica.  
  
 **Ejemplos**  
  
 En el ejemplo siguiente se toman unos ángulos como entrada y se devuelven sus correspondientes valores en radianes.  
  
```  
SELECT RADIANS(-45.01), RADIANS(-181.01), RADIANS(0), RADIANS(0.1472738), RADIANS(197.1099392)  
```  
  
 El conjunto de resultados es el siguiente:  
  
```  
[{  
       "$1": -0.7855726963226477,  
       "$2": -3.1592204790349356,  
       "$3": 0,  
       "$4": 0.0025704127119236249,  
       "$5": 3.4402174274458375  
   }]  
```  
  
####  <a name="bk_round"></a> ROUND  
 Devuelve un valor numérico, redondeado al valor entero más cercano.  
  
 **Sintaxis**  
  
```  
ROUND(<numeric_expression>)  
```  
  
 **Argumentos**  
  
-   `numeric_expression`  
  
     Es una expresión numérica.  
  
 **Tipos de valor devuelto**  
  
 Devuelve una expresión numérica.  
  
 **Ejemplos**  
  
 En el ejemplo siguiente se redondean los siguientes números positivos y negativos al entero más próximo.  
  
```  
SELECT ROUND(2.4), ROUND(2.6), ROUND(2.5), ROUND(-2.4), ROUND(-2.6)  
```  
  
 El conjunto de resultados es el siguiente:  
  
```  
[{$1: 2, $2: 3, $3: 3, $4: -2, $5: -3}]  
```  
  
####  <a name="bk_sign"></a> SIGN  
 Devuelve el signo positivo (+1), cero (0) o negativo (-1) de la expresión numérica especificada.  
  
 **Sintaxis**  
  
```  
SIGN(<numeric_expression>)  
```  
  
 **Argumentos**  
  
-   `numeric_expression`  
  
     Es una expresión numérica.  
  
 **Tipos de valor devuelto**  
  
 Devuelve una expresión numérica.  
  
 **Ejemplos**  
  
 En el ejemplo siguiente se devuelven los valores del signo de números entre -2 y 2.  
  
```  
SELECT SIGN(-2), SIGN(-1), SIGN(0), SIGN(1), SIGN(2)  
```  
  
 El conjunto de resultados es el siguiente:  
  
```  
[{$1: -1, $2: -1, $3: 0, $4: 1, $5: 1}]  
```  
  
####  <a name="bk_sin"></a> SIN  
 Devuelve el seno trigonométrico del ángulo especificado, en radianes, en la expresión especificada.  
  
 **Sintaxis**  
  
```  
SIN(<numeric_expression>)  
```  
  
 **Argumentos**  
  
-   `numeric_expression`  
  
     Es una expresión numérica.  
  
 **Tipos de valor devuelto**  
  
 Devuelve una expresión numérica.  
  
 **Ejemplos**  
  
 En el ejemplo siguiente se calcula el seno del ángulo especificado.  
  
```  
SELECT SIN(45.175643)  
```  
  
 El conjunto de resultados es el siguiente:  
  
```  
[{"$1": 0.929607286611012}]  
```  
  
####  <a name="bk_sqrt"></a> SQRT  
 Devuelve la raíz cuadrada del valor numérico especificado.  
  
 **Sintaxis**  
  
```  
SQRT(<numeric_expression>)  
```  
  
 **Argumentos**  
  
-   `numeric_expression`  
  
     Es una expresión numérica.  
  
 **Tipos de valor devuelto**  
  
 Devuelve una expresión numérica.  
  
 **Ejemplos**  
  
 En el ejemplo siguiente se devuelven las raíces cuadradas de los números 1-3.  
  
```  
SELECT SQRT(1), SQRT(2.0), SQRT(3)  
```  
  
 El conjunto de resultados es el siguiente:  
  
```  
[{$1: 1, $2: 1.4142135623730952, $3: 1.7320508075688772}]  
```  
  
####  <a name="bk_square"></a> SQUARE  
 Devuelve el cuadrado del valor numérico especificado.  
  
 **Sintaxis**  
  
```  
SQUARE(<numeric_expression>)  
```  
  
 **Argumentos**  
  
-   `numeric_expression`  
  
     Es una expresión numérica.  
  
 **Tipos de valor devuelto**  
  
 Devuelve una expresión numérica.  
  
 **Ejemplos**  
  
 En el ejemplo siguiente se devuelven los cuadrados de los números 1-3.  
  
```  
SELECT SQUARE(1), SQUARE(2.0), SQUARE(3)  
```  
  
 El conjunto de resultados es el siguiente:  
  
```  
[{$1: 1, $2: 4, $3: 9}]  
```  
  
####  <a name="bk_tan"></a> TAN  
 Devuelve la tangente del ángulo especificado, en radianes, en la expresión especificada.  
  
 **Sintaxis**  
  
```  
TAN (<numeric_expression>)  
```  
  
 **Argumentos**  
  
-   `numeric_expression`  
  
     Es una expresión numérica.  
  
 **Tipos de valor devuelto**  
  
 Devuelve una expresión numérica.  
  
 **Ejemplos**  
  
 En el ejemplo siguiente se calcula la tangente de pi()/2.  
  
```  
SELECT TAN(PI()/2);  
```  
  
 El conjunto de resultados es el siguiente:  
  
```  
[{"$1": 16331239353195370 }]  
```  
  
####  <a name="bk_trunc"></a> TRUNC  
 Devuelve un valor numérico, truncado al valor entero más cercano.  
  
 **Sintaxis**  
  
```  
TRUNC(<numeric_expression>)  
```  
  
 **Argumentos**  
  
-   `numeric_expression`  
  
     Es una expresión numérica.  
  
 **Tipos de valor devuelto**  
  
 Devuelve una expresión numérica.  
  
 **Ejemplos**  
  
 En el ejemplo siguiente se truncan los siguientes números positivos y negativos al valor entero más próximo.  
  
```  
SELECT TRUNC(2.4), TRUNC(2.6), TRUNC(2.5), TRUNC(-2.4), TRUNC(-2.6)  
```  
  
 El conjunto de resultados es el siguiente:  
  
```  
[{$1: 2, $2: 2, $3: 2, $4: -2, $5: -2}]  
```  
  
###  <a name="bk_type_checking_functions"></a> Funciones de comprobación de tipos  
 Las siguientes funciones admiten la comprobación de tipos de valores de entrada y devuelven un valor booleano.  
  
||||  
|-|-|-|  
|[IS_ARRAY](#bk_is_array)|[IS_BOOL](#bk_is_bool)|[IS_DEFINED](#bk_is_defined)|  
|[IS_NULL](#bk_is_null)|[IS_NUMBER](#bk_is_number)|[IS_OBJECT](#bk_is_object)|  
|[IS_PRIMITIVE](#bk_is_primitive)|[IS_STRING](#bk_is_string)||  
  
####  <a name="bk_is_array"></a> IS_ARRAY  
 Devuelve un valor booleano que indica si el tipo de la expresión especificada es una matriz.  
  
 **Sintaxis**  
  
```  
IS_ARRAY(<expression>)  
```  
  
 **Argumentos**  
  
-   `expression`  
  
     Es cualquier expresión válida.  
  
 **Tipos de valor devuelto**  
  
 Devuelve una expresión condicional.  
  
 **Ejemplos**  
  
 En el ejemplo siguiente se comprueban objetos de los tipos JSON Boolean, number, string, null, object, array y undefined, mediante la función IS_ARRAY.  
  
```  
SELECT   
 IS_ARRAY(true),   
 IS_ARRAY(1),  
 IS_ARRAY("value"),  
 IS_ARRAY(null),  
 IS_ARRAY({prop: "value"}),   
 IS_ARRAY([1, 2, 3]),  
 IS_ARRAY({prop: "value"}.prop2)  
```  
  
 El conjunto de resultados es el siguiente:  
  
```  
[{$1: false, $2: false, $3: false, $4: false, $5: false, $6: true}]  
```  
  
####  <a name="bk_is_bool"></a> IS_BOOL  
 Devuelve un valor booleano que indica si el tipo de la expresión especificada es un valor booleano.  
  
 **Sintaxis**  
  
```  
IS_BOOL(<expression>)  
```  
  
 **Argumentos**  
  
-   `expression`  
  
     Es cualquier expresión válida.  
  
 **Tipos de valor devuelto**  
  
 Devuelve una expresión condicional.  
  
 **Ejemplos**  
  
 En el ejemplo siguiente se comprueban objetos de los tipos JSON Boolean, number, string, null, object, array y undefined, mediante la función IS_BOOL.  
  
```  
SELECT   
    IS_BOOL(true),   
    IS_BOOL(1),  
    IS_BOOL("value"),   
    IS_BOOL(null),  
    IS_BOOL({prop: "value"}),   
    IS_BOOL([1, 2, 3]),  
    IS_BOOL({prop: "value"}.prop2)  
```  
  
 El conjunto de resultados es el siguiente:  
  
```  
[{$1: true, $2: false, $3: false, $4: false, $5: false, $6: false}]  
```  
  
####  <a name="bk_is_defined"></a> IS_DEFINED  
 Devuelve un valor booleano que indica si se ha asignado un valor a la propiedad.  
  
 **Sintaxis**  
  
```  
IS_DEFINED(<expression>)  
```  
  
 **Argumentos**  
  
-   `expression`  
  
     Es cualquier expresión válida.  
  
 **Tipos de valor devuelto**  
  
 Devuelve una expresión condicional.  
  
 **Ejemplos**  
  
 En el ejemplo siguiente se comprueba la presencia de una propiedad en el documento JSON especificado. En el primer caso se devuelve true, ya que "a" está presente, pero en el segundo, false, ya que "b" no lo está.  
  
```  
SELECT IS_DEFINED({ "a" : 5 }.a), IS_DEFINED({ "a" : 5 }.b)  
```  
  
 El conjunto de resultados es el siguiente:  
  
```  
[{  
       "$1": true,    
       "$2": false   
   }]  
```  
  
####  <a name="bk_is_null"></a> IS_NULL  
 Devuelve un valor booleano que indica si el tipo de la expresión especificada es nulo.  
  
 **Sintaxis**  
  
```  
IS_NULL(<expression>)  
```  
  
 **Argumentos**  
  
-   `expression`  
  
     Es cualquier expresión válida.  
  
 **Tipos de valor devuelto**  
  
 Devuelve una expresión condicional.  
  
 **Ejemplos**  
  
 En el ejemplo siguiente se comprueban objetos de los tipos JSON Boolean, number, string, null, object, array y undefined, mediante la función IS_NULL.  
  
```  
SELECT   
    IS_NULL(true),   
    IS_NULL(1),  
    IS_NULL("value"),   
    IS_NULL(null),  
    IS_NULL({prop: "value"}),   
    IS_NULL([1, 2, 3]),  
    IS_NULL({prop: "value"}.prop2)  
```  
  
 El conjunto de resultados es el siguiente:  
  
```  
[{$1: false, $2: false, $3: false, $4: true, $5: false, $6: false}]  
```  
  
####  <a name="bk_is_number"></a> IS_NUMBER  
 Devuelve un valor booleano que indica si el tipo de la expresión especificada es un número.  
  
 **Sintaxis**  
  
```  
IS_NUMBER(<expression>)  
```  
  
 **Argumentos**  
  
-   `expression`  
  
     Es cualquier expresión válida.  
  
 **Tipos de valor devuelto**  
  
 Devuelve una expresión condicional.  
  
 **Ejemplos**  
  
 En el ejemplo siguiente se comprueban objetos de los tipos JSON Boolean, number, string, null, object, array y undefined, mediante la función IS_NULL.  
  
```  
SELECT   
    IS_NUMBER(true),   
    IS_NUMBER(1),  
    IS_NUMBER("value"),   
    IS_NUMBER(null),  
    IS_NUMBER({prop: "value"}),   
    IS_NUMBER([1, 2, 3]),  
    IS_NUMBER({prop: "value"}.prop2)  
```  
  
 El conjunto de resultados es el siguiente:  
  
```  
[{$1: false, $2: true, $3: false, $4: false, $5: false, $6: false}]  
```  
  
####  <a name="bk_is_object"></a> IS_OBJECT  
 Devuelve un valor booleano que indica si el tipo de la expresión especificada es un objeto JSON.  
  
 **Sintaxis**  
  
```  
IS_OBJECT(<expression>)  
```  
  
 **Argumentos**  
  
-   `expression`  
  
     Es cualquier expresión válida.  
  
 **Tipos de valor devuelto**  
  
 Devuelve una expresión condicional.  
  
 **Ejemplos**  
  
 En el ejemplo siguiente se comprueban objetos de los tipos JSON Boolean, number, string, null, object, array y undefined, mediante la función IS_OBJECT.  
  
```  
SELECT   
    IS_OBJECT(true),   
    IS_OBJECT(1),  
    IS_OBJECT("value"),   
    IS_OBJECT(null),  
    IS_OBJECT({prop: "value"}),   
    IS_OBJECT([1, 2, 3]),  
    IS_OBJECT({prop: "value"}.prop2)  
```  
  
 El conjunto de resultados es el siguiente:  
  
```  
[{$1: false, $2: false, $3: false, $4: false, $5: true, $6: false}]  
```  
  
####  <a name="bk_is_primitive"></a> IS_PRIMITIVE  
 Devuelve un valor booleano que indica si el tipo de la expresión especificada es primitivo (cadena, booleano, numérico o nulo).  
  
 **Sintaxis**  
  
```  
IS_PRIMITIVE(<expression>)  
```  
  
 **Argumentos**  
  
-   `expression`  
  
     Es cualquier expresión válida.  
  
 **Tipos de valor devuelto**  
  
 Devuelve una expresión condicional.  
  
 **Ejemplos**  
  
 En el ejemplo siguiente se comprueban los objetos de los tipos JSON Boolean, number, string, null, object, array y undefined, mediante la función IS_PRIMITIVE.  
  
```  
SELECT   
           IS_PRIMITIVE(true),   
           IS_PRIMITIVE(1),  
           IS_PRIMITIVE("value"),   
           IS_PRIMITIVE(null),  
           IS_PRIMITIVE({prop: "value"}),   
           IS_PRIMITIVE([1, 2, 3]),  
           IS_PRIMITIVE({prop: "value"}.prop2)  
```  
  
 El conjunto de resultados es el siguiente:  
  
```  
[{"$1": true, "$2": true, "$3": true, "$4": true, "$5": false, "$6": false, "$7": false}]  
```  
  
####  <a name="bk_is_string"></a> IS_STRING  
 Devuelve un valor booleano que indica si el tipo de la expresión especificada es una cadena.  
  
 **Sintaxis**  
  
```  
IS_STRING(<expression>)  
```  
  
 **Argumentos**  
  
-   `expression`  
  
     Es cualquier expresión válida.  
  
 **Tipos de valor devuelto**  
  
 Devuelve una expresión condicional.  
  
 **Ejemplos**  
  
 En el ejemplo siguiente se comprueban objetos de los tipos JSON Boolean, number, string, null, object, array y undefined, mediante la función IS_STRING.  
  
```  
SELECT   
       IS_STRING(true),   
       IS_STRING(1),  
       IS_STRING("value"),   
       IS_STRING(null),  
       IS_STRING({prop: "value"}),   
       IS_STRING([1, 2, 3]),  
       IS_STRING({prop: "value"}.prop2)  
```  
  
 El conjunto de resultados es el siguiente:  
  
```  
[{$1: false, $2: false, $3: true, $4: false, $5: false, $6: false}]  
```  
  
###  <a name="bk_string_functions"></a> Funciones de cadena  
 Las siguientes funciones escalares realizan una operación sobre un valor de entrada de cadena y devuelven una cadena, un valor numérico o un booleano.  
  
||||  
|-|-|-|  
|[CONCAT](#bk_concat)|[CONTAINS](#bk_contains)|[ENDSWITH](#bk_endswith)|  
|[INDEX_OF](#bk_index_of)|[LEFT](#bk_left)|[LENGTH](#bk_length)|  
|[LOWER](#bk_lower)|[LTRIM](#bk_ltrim)|[REPLACE](#bk_replace)|  
|[REPLICATE](#bk_replicate)|[REVERSE](#bk_reverse)|[RIGHT](#bk_right)|  
|[RTRIM](#bk_rtrim)|[STARTSWITH](#bk_startswith)|[SUBSTRING](#bk_substring)|  
|[UPPER](#bk_upper)|||  
  
####  <a name="bk_concat"></a> CONCAT  
 Devuelve una cadena que es el resultado de concatenar dos o más valores de cadena.  
  
 **Sintaxis**  
  
```  
CONCAT(<str_expr>, <str_expr> [, <str_expr>])  
```  
  
 **Argumentos**  
  
-   `str_expr`  
  
     Es cualquier expresión de cadena válida.  
  
 **Tipos de valor devuelto**  
  
 Devuelve una expresión de cadena.  
  
 **Ejemplos**  
  
 El siguiente ejemplo devuelve la cadena concatenada de los valores especificados.  
  
```  
SELECT CONCAT("abc", "def")  
```  
  
 El conjunto de resultados es el siguiente:  
  
```  
[{"$1": "abcdef"}  
```  
  
####  <a name="bk_contains"></a> CONTAINS  
 Devuelve un valor booleano que indica si la primera expresión de cadena contiene la segunda.  
  
 **Sintaxis**  
  
```  
CONTAINS(<str_expr>, <str_expr>)  
```  
  
 **Argumentos**  
  
-   `str_expr`  
  
     Es cualquier expresión de cadena válida.  
  
 **Tipos de valor devuelto**  
  
 Devuelve una expresión condicional.  
  
 **Ejemplos**  
  
 En el ejemplo siguiente se comprueba si "abc" contiene "ab" y contiene "d".  
  
```  
SELECT CONTAINS("abc", "ab"), CONTAINS("abc", "d")  
```  
  
 El conjunto de resultados es el siguiente:  
  
```  
[{"$1": true, "$2": false}]  
```  
  
####  <a name="bk_endswith"></a> ENDSWITH  
 Devuelve un valor booleano que indica si la primera expresión de cadena finaliza con la segunda.  
  
 **Sintaxis**  
  
```  
ENDSWITH(<str_expr>, <str_expr>)  
```  
  
 **Argumentos**  
  
-   `str_expr`  
  
     Es cualquier expresión de cadena válida.  
  
 **Tipos de valor devuelto**  
  
 Devuelve una expresión condicional.  
  
 **Ejemplos**  
  
 El siguiente ejemplo devuelve las terminaciones "b" y "bc"de "abc".  
  
```  
SELECT ENDSWITH("abc", "b"), ENDSWITH("abc", "bc")  
```  
  
 El conjunto de resultados es el siguiente:  
  
```  
[{"$1": false, "$2": true}]  
```  
  
####  <a name="bk_index_of"></a> INDEX_OF  
 Devuelve la posición inicial de la primera aparición de la expresión de la segunda cadena dentro de la primera expresión de cadena especificada, o -1 si no se encuentra la cadena.  
  
 **Sintaxis**  
  
```  
INDEX_OF(<str_expr>, <str_expr>)  
```  
  
 **Argumentos**  
  
-   `str_expr`  
  
     Es cualquier expresión de cadena válida.  
  
 **Tipos de valor devuelto**  
  
 Devuelve una expresión numérica.  
  
 **Ejemplos**  
  
 En el ejemplo siguiente se devuelve el índice de varias subcadenas dentro de "abc".  
  
```  
SELECT INDEX_OF("abc", "ab"), INDEX_OF("abc", "b"), INDEX_OF("abc", "c")  
```  
  
 El conjunto de resultados es el siguiente:  
  
```  
[{"$1": 0, "$2": 1, "$3": -1}]  
```  
  
####  <a name="bk_left"></a> LEFT  
 Devuelve la parte izquierda de una cadena con el número especificado de caracteres.  
  
 **Sintaxis**  
  
```  
LEFT(<str_expr>, <num_expr>)  
```  
  
 **Argumentos**  
  
-   `str_expr`  
  
     Es cualquier expresión de cadena válida.  
  
-   `num_expr`  
  
     Es cualquier expresión numérica válida.  
  
 **Tipos de valor devuelto**  
  
 Devuelve una expresión de cadena.  
  
 **Ejemplos**  
  
 En el ejemplo siguiente se devuelve la parte izquierda de "abc" para distintos valores de longitud.  
  
```  
SELECT LEFT("abc", 1), LEFT("abc", 2)  
```  
  
 El conjunto de resultados es el siguiente:  
  
```  
[{"$1": "a", "$2": "ab"}]  
```  
  
####  <a name="bk_length"></a> LENGTH  
 Devuelve el número de caracteres de la expresión de cadena especificada.  
  
 **Sintaxis**  
  
```  
LENGTH(<str_expr>)  
```  
  
 **Argumentos**  
  
-   `str_expr`  
  
     Es cualquier expresión de cadena válida.  
  
 **Tipos de valor devuelto**  
  
 Devuelve una expresión de cadena.  
  
 **Ejemplos**  
  
 En el ejemplo siguiente se devuelve la longitud de una cadena.  
  
```  
SELECT LENGTH("abc")  
```  
  
 El conjunto de resultados es el siguiente:  
  
```  
[{"$1": 3}]  
```  
  
####  <a name="bk_lower"></a> LOWER  
 Devuelve una expresión de cadena después de convertir los datos de caracteres en mayúsculas a minúsculas.  
  
 **Sintaxis**  
  
```  
LOWER(<str_expr>)  
```  
  
 **Argumentos**  
  
-   `str_expr`  
  
     Es cualquier expresión de cadena válida.  
  
 **Tipos de valor devuelto**  
  
 Devuelve una expresión de cadena.  
  
 **Ejemplos**  
  
 En el ejemplo siguiente se muestra cómo utilizar LOWER en una consulta.  
  
```  
SELECT LOWER("Abc")  
```  
  
 El conjunto de resultados es el siguiente:  
  
```  
[{"$1": "abc"}]  
  
```  
  
####  <a name="bk_ltrim"></a> LTRIM  
 Devuelve una expresión de cadena después de quitar los espacios en blanco iniciales.  
  
 **Sintaxis**  
  
```  
LTRIM(<str_expr>)  
```  
  
 **Argumentos**  
  
-   `str_expr`  
  
     Es cualquier expresión de cadena válida.  
  
 **Tipos de valor devuelto**  
  
 Devuelve una expresión de cadena.  
  
 **Ejemplos**  
  
 En el ejemplo siguiente se muestra cómo utilizar LTRIM en una consulta.  
  
```  
SELECT LTRIM("  abc"), LTRIM("abc"), LTRIM("abc   ")  
```  
  
 El conjunto de resultados es el siguiente:  
  
```  
[{"$1": "abc", "$2": "abc", "$3": "abc   "}]  
```  
  
####  <a name="bk_replace"></a> REPLACE  
 Reemplaza todas las apariciones de un valor de cadena especificado por otro valor de cadena.  
  
 **Sintaxis**  
  
```  
REPLACE(<str_expr>, <str_expr>, <str_expr>)  
```  
  
 **Argumentos**  
  
-   `str_expr`  
  
     Es cualquier expresión de cadena válida.  
  
 **Tipos de valor devuelto**  
  
 Devuelve una expresión de cadena.  
  
 **Ejemplos**  
  
 En el ejemplo siguiente se muestra cómo utilizar REPLACE en una consulta.  
  
```  
SELECT REPLACE("This is a Test", "Test", "desk")  
```  
  
 El conjunto de resultados es el siguiente:  
  
```  
[{"$1": "This is a desk"}]  
```  
  
####  <a name="bk_replicate"></a> REPLICATE  
 Repite un valor de cadena un número especificado de veces.  
  
 **Sintaxis**  
  
```  
REPLICATE(<str_expr>, <num_expr>)  
```  
  
 **Argumentos**  
  
-   `str_expr`  
  
     Es cualquier expresión de cadena válida.  
  
-   `num_expr`  
  
     Es cualquier expresión numérica válida.  
  
 **Tipos de valor devuelto**  
  
 Devuelve una expresión de cadena.  
  
 **Ejemplos**  
  
 En el ejemplo siguiente se muestra cómo utilizar REPLICATE en una consulta.  
  
```  
SELECT REPLICATE("a", 3)  
```  
  
 El conjunto de resultados es el siguiente:  
  
```  
[{"$1": "aaa"}]  
```  
  
####  <a name="bk_reverse"></a> REVERSE  
 Devuelve el orden inverso de un valor de cadena.  
  
 **Sintaxis**  
  
```  
REVERSE(<str_expr>)  
```  
  
 **Argumentos**  
  
-   `str_expr`  
  
     Es cualquier expresión de cadena válida.  
  
 **Tipos de valor devuelto**  
  
 Devuelve una expresión de cadena.  
  
 **Ejemplos**  
  
 En el ejemplo siguiente se muestra cómo utilizar REVERSE en una consulta.  
  
```  
SELECT REVERSE("Abc")  
```  
  
 El conjunto de resultados es el siguiente:  
  
```  
[{"$1": "cbA"}]  
```  
  
####  <a name="bk_right"></a> RIGHT  
 Devuelve la parte derecha de una cadena con el número especificado de caracteres.  
  
 **Sintaxis**  
  
```  
RIGHT(<str_expr>, <num_expr>)  
```  
  
 **Argumentos**  
  
-   `str_expr`  
  
     Es cualquier expresión de cadena válida.  
  
-   `num_expr`  
  
     Es cualquier expresión numérica válida.  
  
 **Tipos de valor devuelto**  
  
 Devuelve una expresión de cadena.  
  
 **Ejemplos**  
  
 En el ejemplo siguiente se devuelve la parte derecha de "abc" para distintos valores de longitud.  
  
```  
SELECT RIGHT("abc", 1), RIGHT("abc", 2)  
```  
  
 El conjunto de resultados es el siguiente:  
  
```  
[{"$1": "c", "$2": "bc"}]  
```  
  
####  <a name="bk_rtrim"></a> RTRIM  
 Devuelve una expresión de cadena después de quitar los espacios en blanco finales.  
  
 **Sintaxis**  
  
```  
RTRIM(<str_expr>)  
```  
  
 **Argumentos**  
  
-   `str_expr`  
  
     Es cualquier expresión de cadena válida.  
  
 **Tipos de valor devuelto**  
  
 Devuelve una expresión de cadena.  
  
 **Ejemplos**  
  
 En el ejemplo siguiente se muestra cómo utilizar RTRIM en una consulta.  
  
```  
SELECT RTRIM("  abc"), RTRIM("abc"), RTRIM("abc   ")  
```  
  
 El conjunto de resultados es el siguiente:  
  
```  
[{"$1": "   abc", "$2": "abc", "$3": "abc"}]  
```  
  
####  <a name="bk_startswith"></a> STARTSWITH  
 Devuelve un valor booleano que indica si la primera expresión de cadena empieza con la segunda.  
  
 **Sintaxis**  
  
```  
STARTSWITH(<str_expr>, <str_expr>)  
```  
  
 **Argumentos**  
  
-   `str_expr`  
  
     Es cualquier expresión de cadena válida.  
  
 **Tipos de valor devuelto**  
  
 Devuelve una expresión condicional.  
  
 **Ejemplos**  
  
 En el ejemplo siguiente se comprueba si la cadena "abc" empieza por "b" y "a".  
  
```  
SELECT STARTSWITH("abc", "b"), STARTSWITH("abc", "a")  
```  
  
 El conjunto de resultados es el siguiente:  
  
```  
[{"$1": false, "$2": true}]  
```  
  
####  <a name="bk_substring"></a> SUBSTRING  
 Devuelve parte de una expresión de cadena a partir de la posición de base cero del carácter especificado y continúa hasta la longitud especificada, o hasta el final de la cadena.  
  
 **Sintaxis**  
  
```  
SUBSTRING(<str_expr>, <num_expr> [, <num_expr>])  
```  
  
 **Argumentos**  
  
-   `str_expr`  
  
     Es cualquier expresión de cadena válida.  
  
-   `num_expr`  
  
     Es cualquier expresión numérica válida.  
  
 **Tipos de valor devuelto**  
  
 Devuelve una expresión de cadena.  
  
 **Ejemplos**  
  
 En el ejemplo siguiente se devuelve la subcadena de "abc" que empieza por 1 y tiene una longitud de 1 carácter.  
  
```  
SELECT SUBSTRING("abc", 1, 1)  
```  
  
 El conjunto de resultados es el siguiente:  
  
```  
[{"$1": "b"}]  
```  
  
####  <a name="bk_upper"></a> UPPER  
 Devuelve una expresión de cadena después de convertir datos de caracteres en minúsculas a mayúsculas.  
  
 **Sintaxis**  
  
```  
UPPER(<str_expr>)  
```  
  
 **Argumentos**  
  
-   `str_expr`  
  
     Es cualquier expresión de cadena válida.  
  
 **Tipos de valor devuelto**  
  
 Devuelve una expresión de cadena.  
  
 **Ejemplos**  
  
 En el ejemplo siguiente se muestra cómo utilizar UPPER en una consulta.  
  
```  
SELECT UPPER("Abc")  
```  
  
 El conjunto de resultados es el siguiente:  
  
```  
[{"$1": "ABC"}]  
```  
  
###  <a name="bk_array_functions"></a> Funciones de matriz  
 Las siguientes funciones escalares realizan una operación en un valor de entrada de matriz y devuelven un valor numérico, booleano o de matriz.  
  
||||  
|-|-|-|  
|[ARRAY_CONCAT](#bk_array_concat)|[ARRAY_CONTAINS](#bk_array_contains)|[ARRAY_LENGTH](#bk_array_length)|  
|[ARRAY_SLICE](#bk_array_slice)|||  
  
####  <a name="bk_array_concat"></a> ARRAY_CONCAT  
 Devuelve una matriz que es el resultado de concatenar dos o más valores de la matriz.  
  
 **Sintaxis**  
  
```  
ARRAY_CONCAT (<arr_expr>, <arr_expr> [, <arr_expr>])  
```  
  
 **Argumentos**  
  
-   `arr_expr`  
  
     Es cualquier expresión de matriz válida.  
  
 **Tipos de valor devuelto**  
  
 Devuelve una expresión de matriz.  
  
 **Ejemplos**  
  
 En el ejemplo siguiente se muestra cómo concatenar dos matrices.  
  
```  
SELECT ARRAY_CONCAT(["apples", "strawberries"], ["bananas"])  
```  
  
 El conjunto de resultados es el siguiente:  
  
```  
[{"$1": ["apples", "strawberries", "bananas"]}]  
```  
  
####  <a name="bk_array_contains"></a> ARRAY_CONTAINS  
Devuelve un valor booleano que indica si la matriz contiene el valor especificado. Puede especificar si la coincidencia es completa o parcial. 

 **Sintaxis**  
  
```  
ARRAY_CONTAINS (<arr_expr>, <expr> [, bool_expr])  
```  
  
 **Argumentos**  
  
-   `arr_expr`  
  
     Es cualquier expresión de matriz válida.  
  
-   `expr`  
  
     Es cualquier expresión válida.  

-   `bool_expr`  
  
     Es cualquier expresión booleana.       
  
 **Tipos de valor devuelto**  
  
 Devuelve un valor booleano.  
  
 **Ejemplos**  
  
 En el siguiente ejemplo se muestra cómo comprobar la pertenencia a una matriz con ARRAY_CONTAINS.  
  
```  
SELECT   
           ARRAY_CONTAINS(["apples", "strawberries", "bananas"], "apples"),  
           ARRAY_CONTAINS(["apples", "strawberries", "bananas"], "mangoes")  
```  
  
 El conjunto de resultados es el siguiente:  
  
```  
[{"$1": true, "$2": false}]  
```  

 En el siguiente ejemplo se indica cómo buscar una coincidencia parcial de JSON en una matriz mediante ARRAY_CONTAINS.  
  
```  
SELECT  
    ARRAY_CONTAINS([{"name": "apples", "fresh": true}, {"name": "strawberries", "fresh": true}], {"name": "apples"}, true), 
    ARRAY_CONTAINS([{"name": "apples", "fresh": true}, {"name": "strawberries", "fresh": true}], {"name": "apples"}),
    ARRAY_CONTAINS([{"name": "apples", "fresh": true}, {"name": "strawberries", "fresh": true}], {"name": "mangoes"}, true) 
```  
  
 El conjunto de resultados es el siguiente:  
  
```  
[{
  "$1": true,
  "$2": false,
  "$3": false
}] 
```  
  
####  <a name="bk_array_length"></a> ARRAY_LENGTH  
 Devuelve el número de elementos de la expresión de matriz especificada.  
  
 **Sintaxis**  
  
```  
ARRAY_LENGTH(<arr_expr>)  
```  
  
 **Argumentos**  
  
-   `arr_expr`  
  
     Es cualquier expresión de matriz válida.  
  
 **Tipos de valor devuelto**  
  
 Devuelve una expresión numérica.  
  
 **Ejemplos**  
  
 En el siguiente ejemplo se muestra cómo obtener la longitud de una matriz con ARRAY_LENGTH.  
  
```  
SELECT ARRAY_LENGTH(["apples", "strawberries", "bananas"])  
```  
  
 El conjunto de resultados es el siguiente:  
  
```  
[{"$1": 3}]  
```  
  
####  <a name="bk_array_slice"></a> ARRAY_SLICE  
 Devuelve parte de una expresión de matriz.
  
 **Sintaxis**  
  
```  
ARRAY_SLICE (<arr_expr>, <num_expr> [, <num_expr>])  
```  
  
 **Argumentos**  
  
-   `arr_expr`  
  
     Es cualquier expresión de matriz válida.  
  
-   `num_expr`  
  
     Es cualquier expresión numérica válida.  
  
 **Tipos de valor devuelto**  
  
 Devuelve un valor booleano.  
  
 **Ejemplos**  
  
 En el siguiente ejemplo se muestra cómo obtener una parte de una matriz con ARRAY_SLICE.  
  
```  
SELECT   
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1),  
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1, 1)  
```  
  
 El conjunto de resultados es el siguiente:  
  
```  
[{  
           "$1": ["strawberries", "bananas"],   
           "$2": ["strawberries"]  
       }]  
```  
  
###  <a name="bk_spatial_functions"></a> Funciones espaciales  
 Las siguientes funciones escalares realizan una operación en un valor de entrada de objeto espacial y devuelven un valor numérico o un booleano.  
  
||||  
|-|-|-|  
|[ST_DISTANCE](#bk_st_distance)|[ST_WITHIN](#bk_st_within)|[ST_INTERSECTS](#bk_st_intersects)|[ST_ISVALID](#bk_st_isvalid)|  
|[ST_ISVALIDDETAILED](#bk_st_isvaliddetailed)|||  
  
####  <a name="bk_st_distance"></a> ST_DISTANCE  
 Devuelve la distancia entre dos expresiones Point, Polygon o LineString de GeoJSON.  
  
 **Sintaxis**  
  
```  
ST_DISTANCE (<spatial_expr>, <spatial_expr>)  
```  
  
 **Argumentos**  
  
-   `spatial_expr`  
  
     Es cualquier expresión válida de objeto de tipo Point, Polygon o LineString de GeoJSON.  
  
 **Tipos de valor devuelto**  
  
 Devuelve una expresión numérica con la distancia, que se expresa en metros para el sistema de referencia predeterminado.  
  
 **Ejemplos**  
  
 En el siguiente ejemplo se muestra cómo se devuelven todos los documentos de la familia en un radio de 30 km a partir de la ubicación especificada mediante la función integrada ST_DISTANCE. .  
  
```  
SELECT f.id   
FROM Families f   
WHERE ST_DISTANCE(f.location, {'type': 'Point', 'coordinates':[31.9, -4.8]}) < 30000  
```  
  
 El conjunto de resultados es el siguiente:  
  
```  
[{  
  "id": "WakefieldFamily"  
}]  
```  
  
####  <a name="bk_st_within"></a> ST_WITHIN  
 Devuelve una expresión condicional que indica si el objeto de GeoJSON (Point, Polygon o LineString) especificado en el primer argumento se encuentra en el objeto de GeoJSON (Point, Polygon o LineString) del segundo.  
  
 **Sintaxis**  
  
```  
ST_WITHIN (<spatial_expr>, <spatial_expr>)  
```  
  
 **Argumentos**  
  
-   `spatial_expr`  
  
     Es cualquier expresión válida de objeto de tipo Point, Polygon o LineString de GeoJSON.  
 
-   `spatial_expr`  
  
     Es cualquier expresión válida de objeto de tipo Point, Polygon o LineString de GeoJSON.  
  
 **Tipos de valor devuelto**  
  
 Devuelve un valor booleano.  
  
 **Ejemplos**  
  
 En el ejemplo siguiente se muestra cómo buscar todos los documentos de la familia en un polígono con ST_WITHIN.  
  
```  
SELECT f.id   
FROM Families f   
WHERE ST_WITHIN(f.location, {  
    'type':'Polygon',   
    'coordinates': [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]  
})  
```  
  
 El conjunto de resultados es el siguiente:  
  
```  
[{ "id": "WakefieldFamily" }]  
```  

####  <a name="bk_st_intersects"></a> ST_INTERSECTS  
 Devuelve una expresión condicional que indica si el objeto de GeoJSON (Point, Polygon o LineString) especificado en el primer argumento forma intersección con el objeto de GeoJSON (Point, Polygon o LineString) del segundo.  
  
 **Sintaxis**  
  
```  
ST_INTERSECTS (<spatial_expr>, <spatial_expr>)  
```  
  
 **Argumentos**  
  
-   `spatial_expr`  
  
     Es cualquier expresión válida de objeto de tipo Point, Polygon o LineString de GeoJSON.  
 
-   `spatial_expr`  
  
     Es cualquier expresión válida de objeto de tipo Point, Polygon o LineString de GeoJSON.  
  
 **Tipos de valor devuelto**  
  
 Devuelve un valor booleano.  
  
 **Ejemplos**  
  
 En el siguiente ejemplo se muestra cómo buscar todas las áreas de intersección con el polígono indicado.  
  
```  
SELECT a.id   
FROM Areas a   
WHERE ST_INTERSECTS(a.location, {  
    'type':'Polygon',   
    'coordinates': [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]  
})  
```  
  
 El conjunto de resultados es el siguiente:  
  
```  
[{ "id": "IntersectingPolygon" }]  
```  
  
####  <a name="bk_st_isvalid"></a> ST_ISVALID  
 Devuelve un valor booleano que indica si la expresión de Point, Polygon o LineString de GeoJSON especificada es válida.  
  
 **Sintaxis**  
  
```  
ST_ISVALID(<spatial_expr>)  
```  
  
 **Argumentos**  
  
-   `spatial_expr`  
  
     Es cualquier expresión válida de objeto de tipo Point, Polygon o LineString de GeoJSON.  
  
 **Tipos de valor devuelto**  
  
 Devuelve una expresión condicional.  
  
 **Ejemplos**  
  
 En el ejemplo siguiente se muestra cómo comprobar si un punto es válido con ST_VALID.  
  
 Por ejemplo, este punto tiene un valor de latitud que no está en el intervalo válido de valores [-90, 90], por lo que la consulta devuelve false.  
  
 Para los polígonos, la especificación de GeoJSON requiere que el último par de coordenadas indicado sea igual que el primero, para crear una forma cerrada. El orden de los puntos dentro de un polígono debe especificarse siguiendo el sentido contrario al de las agujas del reloj. Un polígono cuyos puntos se hayan especificado en el sentido de las agujas del reloj representa el inverso de la región dentro de él.  
  
```  
SELECT ST_ISVALID({ "type": "Point", "coordinates": [31.9, -132.8] })  
```  
  
 El conjunto de resultados es el siguiente:  
  
```  
[{ "$1": false }]  
```  
  
####  <a name="bk_st_isvaliddetailed"></a> ST_ISVALIDDETAILED  
 Devuelve un valor JSON que contiene un valor booleano si la expresión de Point, Polygon o LineString de GeoJSON especificada es válida; si no es válida, devuelve también el motivo como un valor de cadena.  
  
 **Sintaxis**  
  
```  
ST_ISVALID(<spatial_expr>)  
```  
  
 **Argumentos**  
  
-   `spatial_expr`  
  
     Es cualquier expresión válida de objeto Point o Polygon de GeoJSON.  
  
 **Tipos de valor devuelto**  
  
 Devuelve un valor JSON que contiene un valor booleano si la expresión de punto o polígono GeoJSON especificada es válida; si no es válida, devuelve también el motivo en forma de valor de cadena.  
  
 **Ejemplos**  
  
 En el siguiente ejemplo se muestra cómo comprobar la validez (con detalles) mediante ST_ISVALIDDETAILED.  
  
```  
SELECT ST_ISVALIDDETAILED({   
  "type": "Polygon",   
  "coordinates": [[ [ 31.8, -5 ], [ 31.8, -4.7 ], [ 32, -4.7 ], [ 32, -5 ] ]]  
})  
```  
  
 El conjunto de resultados es el siguiente:  
  
```  
[{  
  "$1": {   
    "valid": false,   
    "reason": "The Polygon input is not valid because the start and end points of the ring number 1 are not the same. Each ring of a polygon must have the same start and end points."   
  }  
}]  
```  
  
## <a name="next-steps"></a>pasos siguientes  
 [Sintaxis SQL y consulta SQL para Azure Cosmos DB](sql-api-sql-query.md)   
 [Documentación sobre Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/)  
  
  
