---
title: Referencia de la sintaxis de SQLRuleAction en Azure | Microsoft Docs
description: "Obtenga más información sobre la gramática de SQLRuleAction."
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
ms.date: 11/07/2017
ms.author: sethm
ms.openlocfilehash: 83b4f76a171cd8a860e7ab43462c976bf4df941a
ms.sourcegitcommit: 0930aabc3ede63240f60c2c61baa88ac6576c508
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/07/2017
---
# <a name="sqlruleaction-syntax"></a>Sintaxis de SQLRuleAction

*SqlRuleAction* es una instancia de la clase [SqlRuleAction](/dotnet/api/microsoft.servicebus.messaging.sqlruleaction) y representa un conjunto de acciones escritos en una sintaxis basada en el lenguaje SQL que se realizan en un objeto [BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage).   
  
Este artículo describe los detalles de la gramática de acción de reglas SQL.  
  
```  
<statements> ::=
    <statement> [, ...n]  
  
```  
  
```  
<statement> ::=
    <action> [;]
    Remarks
    -------
    Semicolon is optional.  
  
```  
  
```  
<action> ::=
    SET <property> = <expression>
    REMOVE <property>  
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
  
-   `<scope>` es una cadena opcional que indica el ámbito de `<property_name>`. Los valores válidos son `sys` o `user`. El valor `sys` indica el ámbito del sistema, donde `<property_name>` es un nombre de propiedad pública de [Clase BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage). `user` indica el ámbito de usuario, donde `<property_name>` es una clave del diccionario [Clase BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage). El ámbito de `user` es el predeterminado si no se especifica `<scope>`.  
  
### <a name="remarks"></a>Comentarios  

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
  
 Es decir, cualquier cadena que empieza por una letra y va seguida de uno o varios dígitos, letras o guiones bajo.  
  
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
  
Las constantes booleanas se representan mediante las palabras clave `TRUE` o `FALSE`. Los valores se almacenan como `System.Boolean`.  
  
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

- SET se usa para crear una nueva propiedad o actualizar el valor de una propiedad existente.
- REMOVE se utiliza para quitar una propiedad.
- SET realiza la conversión implícita si es posible cuando el tipo de expresión y el tipo de propiedad existentes son diferentes.
- La acción no se realiza correctamente si se hace referencia a propiedades del sistema inexistentes.
- La acción no se realiza correctamente si se hace referencia a propiedades del usuario inexistentes.
- Una propiedad de usuario inexistente se evalúa como desconocida internamente siguiendo la misma semántica que [SQLFilter](/dotnet/api/microsoft.servicebus.messaging.sqlfilter) al evaluar los operadores.

## <a name="next-steps"></a>Pasos siguientes

- [Clase SQLRuleAction](/dotnet/api/microsoft.servicebus.messaging.sqlruleaction)
- [Clase SQLFilter](/dotnet/api/microsoft.servicebus.messaging.sqlfilter)
