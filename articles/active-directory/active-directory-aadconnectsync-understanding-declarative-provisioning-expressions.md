<properties
	pageTitle="Sincronización de Azure AD Connect: conocimiento de expresiones de aprovisionamiento declarativo"
	description="Explica las expresiones declarativas de aprovisionamiento."
	services="active-directory"
	documentationCenter=""
	authors="markusvi"
	manager="swadhwa"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/27/2015"
	ms.author="markusvi"/>


# Sincronización de Azure AD Connect: conocimiento de expresiones de aprovisionamiento declarativo

Servicio de sincronización de Azure Active Directory Connect (Sincronización de Azure AD Connect) se basa en el aprovisionamiento declarativo incluido por primera vez en Forefront Identity Manager 2010 para permitirle implementar la lógica de negocios de integración de identidades completa sin necesidad de escribir código.

Una parte esencial del aprovisionamiento declarativo es el lenguaje de expresiones que se usa en flujos de atributos. El lenguaje usado es un subconjunto de Microsoft® Visual Basic® para Aplicaciones. Este lenguaje se usa en Microsoft Office, y los usuarios con experiencia en VBScript también lo reconocerán. El lenguaje de expresiones de aprovisionamiento declarativo solo utiliza funciones y no es un lenguaje estructurado; no hay métodos ni instrucciones. Las funciones se anidarán en su lugar en el flujo de programa rápido.

Para obtener más información, vea [Bienvenido a la referencia de idioma de Visual Basic para Aplicaciones para Office 2013] (https://msdn.microsoft.com/library/gg264383(v=office.15).aspx).

Los atributos están fuertemente tipados. Una función que espera un atributo de cadena de valor único no aceptará varios valores o atributos para un tipo diferente. También distingue mayúsculas de minúsculas. Los nombres de función y los nombres de atributo deben tener la grafía correcta o se producirá un error





## Identificadores y definiciones de idioma

- Las funciones tienen un nombre seguido de argumentos entre paréntesis: FunctionName(<<argument 1>>,<<argument N>>).
- Los atributos se especifican entre corchetes: [attributeName]
- Los parámetros se identifican por signos de porcentaje: %ParameterName%
- Las constantes de cadenas están entre comillas: por ejemplo, “Contoso”
- Los valores numéricos se expresan sin comillas y se espera que sean decimales. Los valores hexadecimales van precedidos de &H. Por ejemplo, 98052, &HFF
- Los valores booleanos se expresan con constantes: True, False.
- Las constantes integradas se expresan solo con su nombre: NULL, CRLF, IgnoreThisFlow


## Operadores

Pueden utilizarse los siguientes operadores:

- **Comparación**: <, <=, <>, =, >, >=
- **Matemáticos**: +, -, *, -
- **Cadena**: & (concatenar)
- **Lógicos**: && (and), || (or)
- **Orden de evaluación**: ( )



Los operadores se evalúan de izquierda a derecha. 2*(5+3) no es igual a 2*5+3.<br> Los paréntesis () se usan para cambiar el orden de evaluación.





## Parámetros

Un parámetro se define por un conector o por un administrador mediante PowerShell. Los parámetros contendrán normalmente valores que serán diferentes entre sistemas, por ejemplo, el nombre del dominio en el que está ubicado el usuario. Pueden usarse en flujos de atributos.

El conector de Active Directory proporcionó los siguientes parámetros para las reglas de sincronización entrantes:

 
| Domain.Netbios | Domain.FQDN | Domain.LDAP | | Forest.Netbios | Forest.FQDN | Forest.LDAP |
 

El sistema proporciona el parámetro siguiente:

Connector.ID

Un ejemplo que llenará el dominio del atributo de metaverso con el nombre netbios del dominio en el que se encuentra el usuario.

domain <- %Domain.Netbios%

## Escenarios comunes

### Longitud de los atributos

Los atributos de cadena se establecen de forma predeterminada para que puedan indexarse y tengan una longitud máxima de 448 caracteres. Si está trabajando con atributos de cadena que podrían contener más, asegúrese de incluir lo siguiente en el flujo de atributos:

`attributeName <- Left([attributeName],448)`

### Cambio de userPrincipalSuffix

Los usuarios no siempre conocen el atributo userPrincipalName en Active Directory y puede no ser igual de adecuado que el identificador de inicio de sesión. La guía de instalación de Sincronización de ADD permite seleccionar un atributo diferente, por ejemplo, mail. Sin embargo, en algunos casos, debe calcularse el atributo. Por ejemplo, la empresa Contoso tiene dos directorios AAD, uno para producción y otro para pruebas. Quieren que los usuarios del inquilino de prueba cambien el sufijo en el inicio de sesión ID.userPrincipalName <- Word([userPrincipalName],1,"@") & "@contosotest.com"

En esta expresión tomamos todo de la izquierda en el primer @-sign (Word) y se concatena con una cadena fija.





### Conversión de varios valores en un valor único

Algunos atributos de Active Directory tienen varios valores en el esquema aunque parezca que tienen un valor en Usuarios y equipos de Active Directory. Un ejemplo es el atributo description.

En esta expresión, en caso de que el atributo tenga un valor, tomamos el primer elemento (Elemento) en el atributo, quitamos los espacios iniciales y finales (Recorte) y, a continuación, conservamos los primeros 448 caracteres (Izquierda) en la cadena.



## Conceptos avanzados

### NULL frente a IgnoreThisFlow

Para las reglas de sincronización entrantes, debe usarse siempre la constante **NULL**. Esto indica que el flujo no tiene ningún valor para contribuir y otra regla puede aportar un valor. Si no hay ninguna regla que aporte un valor, se quita el atributo metaverse.

Existen dos constantes distintas que utilizar par alas reglas de sincronización salientes: NULL e IgnoreThisFlow. Ambas indican que el flujo de atributos no tiene nada a lo que contribuir, pero la diferencia es lo que ocurre cuando ninguna otra regla tiene nada que aportar. Si hay un valor existente en el directorio conectado, se llevará a cabo una eliminación del valor NULL en el atributo quitándolo, mientras que IgnoreThisFlow conservará el valor existente.



#### ImportedValue

La función ImportedValues es diferente de todas las demás funciones, ya que el nombre del atributo debe incluirse entre comillas, en lugar de corchetes: ImportedValue(“proxyAddresses”).

Normalmente, un atributo usará el valor esperado durante la sincronización, incluso si no se ha exportado todavía o se recibió un error durante la exportación ("parte superior de la torre"). Una sincronización entrante supondrá que un atributo que todavía no haya llegado a un directorio conectado lo alcanzará en algún momento. En algunos casos es importante sincronizar únicamente un valor confirmado por el directorio conectado y, en este caso, se usa la función ImportedValue ("torre de importación delta y holograma").

Encontrará un ejemplo de esto en la regla de sincronización lista para su aplicación en AD – User Common desde Exchange, donde en Hybrid Exchange el valor agregado por Exchange Online solo debe sincronizarse si se ha confirmado que el valor se ha exportado correctamente:


`proxyAddresses <- RemoveDuplicates(Trim(ImportedValues(“proxyAddresses”)))`

Para obtener una lista completa de funciones, vea [Sincronización de Azure AD Connect: referencia de funciones](active-directory-aadconnectsync-functions-reference.md)


## Recursos adicionales

* [Sincronización de Azure AD Connect: personalización de las opciones de sincronización](active-directory-aadconnectsync-whatis.md)
* [Integración de las identidades locales con Azure Active Directory](active-directory-aadconnect.md)
 
<!--Image references-->

<!---HONumber=August15_HO7-->