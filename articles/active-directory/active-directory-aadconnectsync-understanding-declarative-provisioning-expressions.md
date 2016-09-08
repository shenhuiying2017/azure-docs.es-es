<properties
	pageTitle="Sincronización de Azure AD Connect: conocimiento de expresiones de aprovisionamiento declarativo | Microsoft Azure"
	description="Explica las expresiones declarativas de aprovisionamiento."
	services="active-directory"
	documentationCenter=""
	authors="andkjell"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/23/2016"
	ms.author="markusvi;andkjell"/>


# Sincronización de Azure AD Connect: conocimiento de expresiones de aprovisionamiento declarativo
La sincronización de Azure AD Connect se basa en el aprovisionamiento declarativo, que se introdujo en Forefront Identity Manager 2010. Permite implementar la lógica de negocios de integración de identidades completa sin necesidad de escribir código compilado.

Una parte esencial del aprovisionamiento declarativo es el lenguaje de expresiones que se usa en flujos de atributos. El lenguaje usado es un subconjunto de Microsoft® Visual Basic® para Aplicaciones. Este lenguaje se usa en Microsoft Office, y los usuarios con experiencia en VBScript también lo reconocerán. El lenguaje de expresiones de aprovisionamiento declarativo solo utiliza funciones y no es un lenguaje estructurado. No hay métodos ni instrucciones. En su lugar, las funciones se anidan al flujo de programa rápido.

Para más información, vea [Referencia del lenguaje VBA para Office 2013](https://msdn.microsoft.com/library/gg264383.aspx).

Los atributos están fuertemente tipados. Las funciones solo aceptan atributos del tipo correcto. También distinguen mayúsculas de minúsculas. Es preciso usar correctamente las mayúsculas y minúsculas tanto en los nombres de función como en los nombres de atributo deben tener o se producirá un error.

## Identificadores y definiciones de idioma

- Las funciones tienen un nombre seguido de argumentos entre paréntesis: FunctionName(argumento 1, argumento N).
- Los atributos se especifican entre corchetes: [attributeName]
- Los parámetros se identifican por signos de porcentaje: %ParameterName%
- Las constantes de cadena aparecen entre comillas: por ejemplo, "Contoso" (Nota: se deben usar comillas rectas "", no tipográficas "")
- Los valores numéricos se expresan sin comillas y se espera que sean decimales. Los valores hexadecimales van precedidos de &H. Por ejemplo, 98052, &HFF
- Los valores booleanos se expresan con constantes: True, False.
- Las constantes y los literales integrados se expresan solo con su nombre: NULL, CRLF o IgnoreThisFlow

### Funciones
El aprovisionamiento declarativo usa muchas funciones que ofrecen la posibilidad de transformar los valores de atributo. Dichas funciones se pueden anidar de manera que el resultado de una función se pase a otra función.

`Function1(Function2(Function3()))`

La lista completa de funciones se puede encontrar en la [referencia de funciones](active-directory-aadconnectsync-functions-reference.md).

### Parámetros
Un parámetro se define por un conector o por un administrador mediante PowerShell. Los parámetros suelen contener valores que son diferentes de un sistema a otro, por ejemplo, el nombre del dominio en el que está ubicado el usuario. Dichos parámetros se pueden usar en flujos de atributos.

El conector de Active Directory proporcionó los siguientes parámetros para las reglas de sincronización entrantes:

| Nombre de parámetro | Comentario |
| --- | --- |
| Domain.Netbios | El formato Netbios del dominio importado actualmente, por ejemplo, FABRIKAMSALES |
| Domain.FQDN | El formato FQDN del dominio importado actualmente, por ejemplo, sales.fabrikam.com |
| Domain.LDAP | El formato LDAP del dominio importado actualmente, por ejemplo, DC=sales,DC=fabrikam,DC=com |
| Forest.Netbios | El formato Netbios del nombre de bosque importado actualmente, por ejemplo, FABRIKAMCORP |
| Forest.FQDN | El formato FQDN del nombre de bosque importado actualmente, por ejemplo, fabrikam.com |
| Forest.LDAP | El formato LDAP del nombre de bosque importado actualmente, por ejemplo, DC=fabrikam,DC=com |

El sistema proporciona el parámetro siguiente, que se usa para obtener el identificador del conector que se ejecuta actualmente: `Connector.ID`

Este es un ejemplo en el que se llena el dominio del atributo de metaverso con el nombre netbios del dominio en el que se encuentra el usuario: `domain` <- `%Domain.Netbios%`

### Operadores
Pueden utilizarse los siguientes operadores:

- **Comparación**: <, <=, <>, =, >, >=
- **Matemáticos**: +, -, *, -
- **Cadena**: & (concatenar)
- **Lógico**: && (and), || (or)
- **Orden de evaluación**: ( )

Los operadores se evalúan de izquierda a derecha y tienen la misma prioridad de evaluación. Es decir, la multiplicación (*) no se evalúa antes que la resta (-). 2*(5+3) no es lo mismo que 2*5+3. Los corchetes () se usan para cambiar el orden de evaluación cuando la evaluación de izquierda a derecha no es adecuada.

## Atributos con varios valores

### Flujos de atributos para atributos con varios valores
Las funciones pueden operar tanto atributos con un solo valor como con varios valores. Para los atributos con varios valores, la función opera sobre cada valor y aplica la misma función a cada valor.

Por ejemplo `Trim([proxyAddresses])` Use la función Trim en todos los valores del atributo proxyAddress. `Word([proxyAddresses],1,"@") & "@contoso.com"` En el caso de cada valor con un signo @, reemplace el dominio por @contoso.com. `IIF(InStr([proxyAddresses],"SIP:")=1,NULL,[proxyAddresses])` Busque la dirección SIP y quítela de los valores.

### Combinación de valores de atributo
En los flujos de atributos hay un valor que determina si se deben combinar atributos con varios valores de distintos conectores. El valor predeterminado es **Update**, que indica que debe prevalecer la regla de sincronización con prioridad más alta.

![Mezcla de tipos](./media/active-directory-aadconnectsync-understanding-declarative-provisioning-expressions/mergetype.png)

También se pueden seleccionar los valores **Merge** y **MergeCaseInsensitive**. Estas opciones permiten combinar valores de distintos orígenes. Por ejemplo, se pueden utilizar para combinar el miembro o el atributo proxyAddresses de varios bosques. Si utiliza esta opción, todas las reglas de sincronización del ámbito de un objeto deben utilizar el mismo tipo de combinación. No se puede definir **Update** de un conector y **Merge** de otro. Si lo intenta, recibirá un error.

La diferencia entre **Merge** y **MergeCaseInsensitive** es la forma en que se procesan los valores duplicados del atributo. El motor de sincronización garantiza que no se insertan valores duplicados en el atributo de destino. Con **MergeCaseInsensitive**, los valores duplicados que solo se diferencien por el uso de mayúscula y minúscula no estarán presentes. Por ejemplo, no verá "SMTP:bob@contoso.com" y "smtp:bob@contoso.com" en el atributo de destino. **Mezcla** solo examina los valores exactos y pueden estar presentes varios valores donde solo hay una diferencia en el uso de mayúscula y minúscula.

La opción **Replace** es igual que **Update**, pero no se usa.

## Recursos adicionales

[Azure AD Connect Sync: referencia de funciones](active-directory-aadconnectsync-functions-reference.md) [Sincronización de Azure AD Connect: comprender y personalizar la sincronización](active-directory-aadconnectsync-whatis.md) [Integración de las identidades locales con Azure Active Directory](active-directory-aadconnect.md)

<!---HONumber=AcomDC_0824_2016-->