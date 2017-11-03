---
title: Referencia de la sintaxis de SQLFilter de Azure Service Bus | Microsoft Docs
description: "Obtenga más información sobre la gramática de SQLFilter."
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/16/2017
ms.author: sethm
ms.openlocfilehash: 159bb0500dd98a428d60d6aeb78767a3460b40dc
ms.sourcegitcommit: 9ae92168678610f97ed466206063ec658261b195
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/17/2017
---
# <a name="sqlfilter-syntax"></a>Sintaxis de SQLFilter

Un objeto *SqlFilter* es una instancia de la [clase SqlFilter](/dotnet/api/microsoft.servicebus.messaging.sqlfilter) y representa una expresión de filtro basada en lenguaje SQL que se evalúa con respecto a un objeto [BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage). SqlFilter admite un subconjunto del estándar SQL-92.  
  
 En este tema se ofrece información sobre la gramática de SqlFilter.  
  
```  
<predicate ::=  
      { NOT <predicate> }  
      | <predicate> AND <predicate>  
      | <predicate> OR <predicate>  
      | <expression> { = | <> | != | > | >= | < | <= } <expression>  
      | <property> IS [NOT] NULL  
      | <expression> [NOT] IN ( <expression> [, ...n] )  
      | <expression> [NOT] LIKE <pattern> [ESCAPE <escape_char>]  
      | EXISTS ( <property> )  
      | ( <predicate> )  
  
```  
  
```  
<expression> ::=  
      <constant>   
      | <function>  
      | <property>  
      | <expression> { + | - | * | / | % } <expression>  
      | { + | - } <expression>  
      | ( <expression> )  
  
```  
  
```  
<property> :=   
       [<scope> .] <property_name>  
  
```  
  
## <a name="arguments"></a>Argumentos  
  
-   `<scope>` es una cadena opcional que indica el ámbito de `<property_name>`. Los valores válidos son `sys` o `user`. El valor `sys` indica el ámbito del sistema, donde `<property_name>` es un nombre de propiedad pública de la [clase BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage). `user` indica el ámbito de usuario, donde `<property_name>` es una clave del diccionario de la [clase BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage). El ámbito de `user` es el predeterminado si no se especifica `<scope>`.  
  
## <a name="remarks"></a>Comentarios

Un intento de acceso a una propiedad de sistema que no existe es un error, mientras que uno a una propiedad de usuario inexistente, no lo es. En su lugar, una propiedad de usuario inexistente internamente se evalúa como un valor desconocido. Un valor desconocido se trata de una forma especial durante la evaluación de operador.  
  
## <a name="propertyname"></a>property_name  
  
```  
<property_name> ::=  
     <identifier>  
     | <delimited_identifier>  
  
<identifier> ::=  
     <regular_identifier> | <quoted_identifier> | <delimited_identifier>  
  
```  
  
### <a name="arguments"></a>Argumentos  

 `<regular_identifier>` es una cadena que se representa mediante la siguiente expresión regular:  
  
```  
[[:IsLetter:]][_[:IsLetter:][:IsDigit:]]*  
```  
  
Esta gramática significa cualquier cadena que empiece por una letra y vaya seguida de uno o varios dígitos, letras o guiones bajo.  
  
`[:IsLetter:]` significa cualquier carácter Unicode que se clasifica como una letra Unicode. `System.Char.IsLetter(c)` devuelve `true` si `c` es una letra Unicode.  
  
`[:IsDigit:]` significa cualquier carácter Unicode que se clasifica como un dígito Unicode. `System.Char.IsDigit(c)` devuelve `true` si `c` es un dígito Unicode.  
  
`<regular_identifier>` no puede ser una palabra clave reservada.  
  
`<delimited_identifier>` es cualquier cadena que se incluye con corchetes izquierdos y derechos ([]). Un corchete derecho se representan como dos corchetes derechos. A continuación, se muestran ejemplos de `<delimited_identifier>`:  
  
```  
[Property With Space]  
[HR-EmployeeID]  
  
```  
  
`<quoted_identifier>` es cualquier cadena que se incluye entre comillas dobles. Las comillas dobles en el identificador se representan como dos comillas dobles. No se recomienda utilizar identificadores entre comillas, ya que pueden confundirse fácilmente con una constante de cadena. Si es posible, utilice un identificador delimitado. A continuación, se muestra un ejemplo de `<quoted_identifier>`:  
  
```  
"Contoso & Northwind"  
```  
  
## <a name="pattern"></a>Patrón  
  
```  
<pattern> ::=  
      <expression>  
```  
  
### <a name="remarks"></a>Comentarios
  
`<pattern>` debe ser una expresión que se evalúa como una cadena. Se utiliza como patrón para el operador LIKE.      Puede contener los siguientes caracteres comodín:  
  
-   `%`: cualquier cadena de cero o más caracteres.  
  
-   `_`: cualquier carácter individual.  
  
## <a name="escapechar"></a>escape_char  
  
```  
<escape_char> ::=  
      <expression>  
```  
  
### <a name="remarks"></a>Comentarios  

`<escape_char>` debe ser una expresión que se evalúa como una cadena de longitud 1. Se utiliza como carácter de escape para el operador LIKE.  
  
 Por ejemplo, `property LIKE 'ABC\%' ESCAPE '\'` coincide con `ABC%`, en lugar de con una cadena que comienza con `ABC`.  
  
## <a name="constant"></a>constant  
  
```  
<constant> ::=  
      <integer_constant> | <decimal_constant> | <approximate_number_constant> | <boolean_constant> | NULL  
```  
  
### <a name="arguments"></a>Argumentos  
  
-   `<integer_constant>` es una cadena de números que no se incluyen entre comillas y no contienen decimales. Los valores se almacenan como `System.Int64` internamente y siguen el mismo intervalo.  
  
     A continuación, se muestran ejemplos de constantes largas:  
  
    ```  
    1894  
    2  
    ```  
  
-   `<decimal_constant>` es una cadena de números que no se incluyen entre comillas y contienen un separador decimal. Los valores se almacenan como `System.Double` internamente y siguen el mismo intervalo o la misma precisión.  
  
     En una versión futura, este número podría almacenarse en un tipo de datos diferente para admitir la semántica de número exacto. Por lo tanto, no debe confiar en el hecho de que el tipo de datos subyacente es `System.Double` en `<decimal_constant>`.  
  
     A continuación, se muestran ejemplos de constantes decimales:  
  
    ```  
    1894.1204  
    2.0  
    ```  
  
-   `<approximate_number_constant>` es un número escrito en la notación científica. Los valores se almacenan como `System.Double` internamente y siguen el mismo intervalo o la misma precisión. A continuación, se muestran ejemplos de constantes de número aproximado:  
  
    ```  
    101.5E5  
    0.5E-2  
    ```  
  
## <a name="booleanconstant"></a>boolean_constant  
  
```  
<boolean_constant> :=  
      TRUE | FALSE  
```  
  
### <a name="remarks"></a>Comentarios  

Las constantes booleanas se representan mediante las palabras clave **TRUE** o **FALSE**. Los valores se almacenan como `System.Boolean`.  
  
## <a name="stringconstant"></a>string_constant  
  
```  
<string_constant>  
```  
  
### <a name="remarks"></a>Comentarios  

Las constantes de cadena se incluyen entre comillas simples y contienen caracteres Unicode válidos. Una comilla simple incrustada en una constante de cadena se representan como dos comillas simples.  
  
## <a name="function"></a>function  
  
```  
<function> :=  
      newid() |  
      property(name) | p(name)  
```  
  
### <a name="remarks"></a>Comentarios
  
La función `newid()` devuelve un elemento **System.Guid** generado por el método `System.Guid.NewGuid()`.  
  
La función `property(name)` devuelve el valor de la propiedad a la que hace referencia `name`. El valor `name` puede ser cualquier expresión válida que devuelve un valor de cadena.  
  
## <a name="considerations"></a>Consideraciones
  
Tenga en cuenta la siguiente semántica de [SqlFilter](/dotnet/api/microsoft.servicebus.messaging.sqlfilter):  
  
-   Los nombres de propiedad distinguen entre mayúsculas y minúsculas.  
  
-   Los operadores siguen la semántica de conversión implícita de C# siempre que sea posible.  
  
-   Las propiedades del sistema son propiedades públicas expuestas en instancias de [BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage).  
  
    Tenga en cuenta la siguiente semántica de `IS [NOT] NULL`:  
  
    -   `property IS NULL` se evalúa como `true` si no existe la propiedad o valor de la propiedad es `null`.  
  
### <a name="property-evaluation-semantics"></a>Semántica de evaluación de propiedades  
  
-   Un intento de evaluar una propiedad no existente del sistema genera una excepción [FilterException](/dotnet/api/microsoft.servicebus.messaging.filterexception).  
  
-   Una propiedad que no existe se evalúa internamente como **valor desconocido**.  
  
 Evaluación desconocida en operadores aritméticos:  
  
-   Para los operadores binarios, si la parte izquierda o derecha de los operandos se evalúa como **valor desconocido**, el resultado será **desconocido**.  
  
-   Para los operadores unarios, si un operando se evalúa como **desconocido**, el resultado será **desconocido**.  
  
 Evaluación desconocida en los operadores de comparación binaria:  
  
-   Si la parte izquierda o derecha de los operandos se evalúa como **valor desconocido**, el resultado será **desconocido**.  
  
 Evaluación desconocida en `[NOT] LIKE`:  
  
-   Si cualquier operando se evalúa como **desconocido**, el resultado es **desconocido**.  
  
 Evaluación desconocida en `[NOT] IN`:  
  
-   Si el operando izquierdo se evalúa como **desconocido**, el resultado es **desconocido**.  
  
 Evaluación desconocida en el operador **AND**:  
  
```  
+---+---+---+---+  
|AND| T | F | U |  
+---+---+---+---+  
| T | T | F | U |  
+---+---+---+---+  
| F | F | F | F |  
+---+---+---+---+  
| U | U | F | U |  
+---+---+---+---+  
```  
  
 Evaluación desconocida en el operador **OR**:  
  
```  
+---+---+---+---+  
|OR | T | F | U |  
+---+---+---+---+  
| T | T | T | T |  
+---+---+---+---+  
| F | T | F | U |  
+---+---+---+---+  
| U | T | U | U |  
+---+---+---+---+  
```  
  
### <a name="operator-binding-semantics"></a>Semántica de enlace de operadores
  
-   Los operadores de comparación, como `>`, `>=`, `<`, `<=`, `!=` y `=`, siguen la misma semántica que el enlace de operadores de C# en promociones de tipo de datos y conversiones implícitas.  
  
-   Los operadores aritméticos, como `+`, `-`, `*`, `/` y `%`, siguen la misma semántica que el enlace de operadores de C# en promociones de tipo de datos y conversiones implícitas.

## <a name="next-steps"></a>Pasos siguientes

- [Clase SQLFilter (.NET Framework)](/dotnet/api/microsoft.servicebus.messaging.sqlfilter)
- [Clase SQLFilter (.NET Standard)](/dotnet/api/microsoft.azure.servicebus.filters.sqlfilter)
- [Clase SQLRuleAction](/dotnet/api/microsoft.servicebus.messaging.sqlruleaction)