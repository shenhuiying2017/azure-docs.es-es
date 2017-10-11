---
title: 'Azure AD Connect: conocimiento del aprovisionamiento declarativo | Microsoft Docs'
description: "Explica el modelo de configuración de aprovisionamiento declarativo en Azure AD Connect."
services: active-directory
documentationcenter: 
author: andkjell
manager: femila
editor: 
ms.assetid: cfbb870d-be7d-47b3-ba01-9e78121f0067
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.author: billmath
ms.openlocfilehash: 7497ec2ca658c3790227c56ef1755d9a1cb74e0a
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/03/2017
---
# <a name="azure-ad-connect-sync-understanding-declarative-provisioning"></a>Sincronización de Azure AD Connect: conocimiento del aprovisionamiento declarativo
Este tema explica el modelo de configuración de Azure AD Connect. El modelo se denomina aprovisionamiento declarativo y permite cambiar una configuración con facilidad. Muchas cosas descritas en este tema son avanzadas y no son necesarias para la mayoría de los escenarios de los clientes.

## <a name="overview"></a>Información general
El aprovisionamiento declarativo consiste en procesar objetos procedentes de un directorio de origen conectado y determina cómo el objeto y los atributos deben transformarse desde un origen a un destino. Un objeto se procesa en una canalización de sincronización, que es la misma para las reglas de entrada y salidas. Una regla de entrada es de un espacio conector al metaverso y una regla de salida del metaverso a un espacio conector.

![Canalización de sincronización](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/sync1.png)  

La canalización consta de varios módulos diferentes. Cada uno de ellos es responsable de un concepto de sincronización de objetos.

![Canalización de sincronización](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/pipeline.png)  

* Origen: el objeto de origen.
* [Ámbito](#scope): busca todas las reglas de sincronización que están en ámbito.
* [Unión](#join): determina la relación entre el espacio conector y el metaverso.
* [Transformación](#transform): calcula cómo deben transformarse los atributos y el flujo.
* [Prioridad](#precedence): resuelve las contribuciones de atributo en conflicto.
* Destino: el objeto de destino.

## <a name="scope"></a>Ámbito
El módulo de ámbito consiste en evaluar un objeto, y determina las reglas que están en el ámbito y deben incluirse en el procesamiento. En función de los valores de los atributos en el objeto, se evalúan diferentes reglas de sincronización para que estén en el ámbito. Por ejemplo, un usuario deshabilitado sin ningún buzón de Exchange tiene reglas diferentes a las de un usuario con un buzón habilitado.  
![Ámbito](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/scope1.png)  

El ámbito se define como cláusulas y grupos. Las cláusulas están dentro de un grupo. Se usa un operador lógico AND entre todas las cláusulas de un grupo. Por ejemplo, (departamento = IT AND país = Dinamarca). Se usa un operador lógico OR entre los grupos.

![Ámbito](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/scope2.png)  
El ámbito de esta imagen se debe leer como (departamento = IT AND país = Dinamarca) OR (país = Suecia). Si el grupo 1 o el grupo 2 se evalúa como verdadero, la regla está en el ámbito.

El módulo de ámbito admite las siguientes operaciones:

| Operación | Description |
| --- | --- |
| EQUAL, NOTEQUAL |Una comparación de cadenas que evalúa si el valor es igual al valor del atributo. Para los atributos con varios valores, consulte ISIN e ISNOTIN. |
| LESSTHAN, LESSTHAN_OR_EQUAL |Una comparación de cadenas que evalúa si el valor es menor que el valor en el atributo. |
| CONTAINS, NOTCONTAINS |Una comparación de cadenas que evalúa si el valor puede encontrarse en cualquier lugar dentro del valor del atributo. |
| STARTSWITH, NOTSTARTSWITH |Una comparación de cadenas que evalúa si el valor está al principio del valor del atributo. |
| ENDSWITH, NOTENDSWITH |Una comparación de cadenas que evalúa si el valor está al final del valor del atributo. |
| GREATERTHAN, GREATERTHAN_OR_EQUAL |Una comparación de cadenas que evalúa si el valor es mayor que el valor en el atributo. |
| ISNULL, ISNOTNULL |Evalúa si el atributo no está presente en el objeto. Si el atributo no está presente y, por tanto, es nulo, la regla está en el ámbito. |
| ISIN, ISNOTIN |Evalúa si el valor está presente en el atributo definido. Esta operación es la variación con múltiples valores de EQUAL y NOTEQUAL. Se supone que el atributo es tiene múltiples valores y si el valor puede encontrarse en cualquiera de los valores de atributo; en ese caso, la regla está en el ámbito. |
| ISBITSET, ISNOTBITSET |Evalúa si hay un determinado bit establecido. Por ejemplo, se puede usar para evaluar los bits de userAccountControl para ver si un usuario está habilitado o deshabilitado. |
| ISMEMBEROF, ISNOTMEMBEROF |El valor debe contener un DN para un grupo en el espacio conector. Si el objeto es un miembro del grupo especificado, la regla está en el ámbito. |

## <a name="join"></a>Unión
El módulo de unión en la canalización de sincronización es responsable de encontrar la relación entre el objeto en el origen y un objeto en el destino. En una regla de entrada, esta relación sería un objeto en un espacio conector que busca una relación con un objeto en el metaverso.  
![Unión entre cs y mv](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/join1.png)  
El objetivo es ver si ya hay un objeto en el metaverso, creado por otro conector, al que debe estar asociado. Por ejemplo, en un bosque de cuenta-recurso, el usuario del bosque de cuentas debe unirse al usuario del bosque de recursos.

Las uniones se utilizan principalmente en las reglas de entrada para unir objetos del espacio conector para el mismo objeto de metaverso.

Las uniones se definen como uno o varios grupos. Dentro de un grupo hay cláusulas. Se usa un operador lógico AND entre todas las cláusulas de un grupo. Se usa un operador lógico OR entre los grupos. El orden de procesamiento de los grupos es de arriba a abajo. Cuando un grupo encuentra exactamente una coincidencia con un objeto en el destino, no se evalúa ninguna otra regla de unión. Si se encuentran cero o más de un objeto, el procesamiento continúa con el siguiente grupo de reglas. Por este motivo, las reglas deben crearse en el orden de primero la más explícita y la más aproximada al final.  
![Definición de unión](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/join2.png)  
Las uniones de esta imagen se procesan de arriba a abajo. En primer lugar, la canalización de sincronización ve si hay una coincidencia en employeeID. Si no la hay, la segunda regla ve si el nombre de cuenta puede utilizarse para unir los objetos. Si tampoco hay ninguna coincidencia, la tercera y última regla es una coincidencia más aproximada que utiliza el nombre de usuario.

Si se han evaluado todas las reglas de unión y no hay ninguna coincidencia exacta, se usa el valor de **Tipo de vínculo** en la página **Descripción**. Si este valor se establece en **Aprovisionar**, se crea un objeto en el destino.  
![Aprovisionar o unir](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/join3.png)  

Un objeto solo debe tener una regla de sincronización con reglas de unión en el ámbito. Si hay varias reglas de sincronización donde se define la unión, se produce un error. No se utiliza la prioridad para resolver conflictos de unión. Un objeto debe tener una regla de unión en el ámbito para que los atributos fluyan con la misma dirección entrante y saliente. Si necesita que fluyan atributos entrantes y salientes al mismo objeto, debe tener una regla de sincronización de entrada y de salida con unión.

La unión de salida tiene un comportamiento especial cuando intenta aprovisionar un objeto en un espacio conector de destino. Se utiliza el atributo DN para probar primero una inversión-unión. Si ya hay un objeto en el espacio conector de destino con el mismo DN, los objetos se unen.

El módulo de unión solo se evalúa una vez cuando una nueva regla de sincronización entra en el ámbito. Cuando se ha unido un objeto, no se anula la unión ni siquiera si ya no se cumplen los criterios de unión. Si desea anular la unión de un objeto, se debe sacar del ámbito la regla de sincronización que unió los objetos.

### <a name="metaverse-delete"></a>Eliminación del metaverso
Un objeto de metaverso permanece mientras hay una regla de sincronización en el ámbito con **Tipo de vínculo** establecido en **Aprovisionar** o **StickyJoin** (Unión permanente). Una unión permanente se utiliza cuando un conector no puede aprovisionar un nuevo objeto en el metaverso, pero cuando está unido, debe eliminarse en el origen antes de eliminar el objeto de metaverso.

Cuando se elimina un objeto de metaverso, todos los objetos asociados a una regla de sincronización de salida marcada para **aprovisionar** están marcados para su eliminación.

## <a name="transformations"></a>Transformaciones
Las transformaciones se usan para definir cómo deben fluir los atributos del origen al destino. Los flujos pueden tener uno de los siguientes **tipos de flujo**: directo, constante o expresión. En un flujo directo, un valor de atributo fluye tal cual, sin transformaciones adicionales. Un valor constante establece el valor especificado. Una expresión utiliza el lenguaje de expresiones de aprovisionamiento declarativo para expresar cómo debe ser la transformación. Los detalles para el lenguaje de expresiones se pueden encontrar en el tema sobre el [conocimiento de expresiones de aprovisionamiento declarativo](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md) .

![Aprovisionar o unir](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/transformations1.png)  

La casilla **Aplicar una vez** define que el atributo solo se debe establecer cuando el objeto se crea inicialmente. Por ejemplo, esta configuración se puede utilizar para establecer una contraseña inicial para un nuevo objeto de usuario.

### <a name="merging-attribute-values"></a>Combinación de valores de atributo
En los flujos de atributos hay un valor que determina si se deben combinar atributos con varios valores de distintos conectores. El valor predeterminado es **Update**, que indica que debe prevalecer la regla de sincronización con prioridad más alta.

![Mezcla de tipos](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/mergetype.png)  

También se pueden seleccionar los valores **Merge** y **MergeCaseInsensitive**. Estas opciones permiten combinar valores de distintos orígenes. Por ejemplo, se pueden utilizar para combinar el miembro o el atributo proxyAddresses de varios bosques. Si utiliza esta opción, todas las reglas de sincronización del ámbito de un objeto deben utilizar el mismo tipo de combinación. No se puede definir **Update** de un conector y **Merge** de otro. Si lo intenta, recibirá un error.

La diferencia entre **Merge** y **MergeCaseInsensitive** es la forma en que se procesan los valores duplicados del atributo. El motor de sincronización garantiza que no se insertan valores duplicados en el atributo de destino. Con **MergeCaseInsensitive**, los valores duplicados que solo se diferencien por el uso de mayúscula y minúscula no estarán presentes. Por ejemplo, no debería ver "SMTP:bob@contoso.com" y "smtp:bob@contoso.com" en el atributo de destino. **Mezcla** solo examina los valores exactos y pueden estar presentes varios valores donde solo hay una diferencia en el uso de mayúscula y minúscula.

La opción **Replace** es igual que **Update**, pero no se usa.

### <a name="control-the-attribute-flow-process"></a>Control del proceso de flujo de atributo
Cuando se configuran varias reglas de sincronización de entrada para contribuir al mismo atributo de metaverso, se utiliza la prioridad para determinar al ganador. La regla de sincronización con prioridad más alta (cuyo valor numérico es el más bajo) va a aportar el valor. Lo mismo sucede para las reglas de salida. La regla de sincronización de prioridad más alta es la que gana y aporta el valor al directorio conectado.

En algunos casos, en lugar de aportar un valor, la regla de sincronización determina cómo deben comportarse las otras reglas. Hay algunos literales especiales que se utilizan para este caso.

Para las reglas de sincronización de entrada, se puede usar el literal **NULL** para indicar que el flujo no tiene ningún valor para aportar. Otra regla con una prioridad más baja puede aportar un valor. Si no hay ninguna regla que aporte un valor, se quita el atributo metaverse. Para una regla de salida, si **NULL** es el valor final después de que se han procesado todas las reglas de sincronización, se quita el valor en el directorio conectado.

El literal **AuthoritativeNull** es similar a **NULL** pero con la diferencia de que ninguna regla de prioridad inferior puede aportar un valor.

Un flujo de atributo también puede usar el atributo **IgnoreThisFlow**. Es similar a NULL en el sentido de que indica que no hay nada que aportar. La diferencia es que no se quita ningún valor existente en el destino. Es como si el flujo de atributo nunca hubiera estado allí.

Aquí tiene un ejemplo:

En la regla *Out to AD - User Exchange hybrid* se puede encontrar el siguiente flujo:  
`IIF([cloudSOAExchMailbox] = True,[cloudMSExchSafeSendersHash],IgnoreThisFlow)`  
Esta expresión se debe leer como: si el buzón del usuario se encuentra en Azure AD, el flujo de atributo va de Azure AD a AD. Si no es así, no vuelve nada a Active Directory. En este caso, se mantiene el valor existente en AD.

### <a name="importedvalue"></a>ImportedValue
La función ImportedValue es diferente de todas las demás funciones, ya que el nombre del atributo debe incluirse entre comillas, en lugar de corchetes:   
`ImportedValue("proxyAddresses")`.

Normalmente, un atributo usa el valor esperado durante la sincronización, incluso si no se ha exportado todavía o se recibió un error durante la exportación ("parte superior de la torre"). Una sincronización entrante supone que un atributo que todavía no ha llegado a un directorio conectado lo alcanzará en algún momento. En algunos casos, es importante sincronizar únicamente un valor confirmado por el directorio conectado ("torre de importación delta y holograma").

Un ejemplo de esta función se puede encontrar en la regla de sincronización de serie *In from AD – User Common from Exchange*. En Exchange híbrido, el valor agregado por Exchange Online solo se debe sincronizar cuando se ha confirmado que el valor se exportó correctamente:   
`proxyAddresses` <- `RemoveDuplicates(Trim(ImportedValue("proxyAddresses")))`

## <a name="precedence"></a>Prioridad
Cuando varias reglas de sincronización intentan contribuir con el mismo valor de atributo al destino, el valor de prioridad se utiliza para determinar el ganador. En un conflicto, la regla con prioridad más alta y valor numérico más bajo contribuirá con el atributo.

![Mezcla de tipos](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/precedence1.png)  

Este orden puede utilizarse para definir flujos de atributo más precisos para un pequeño subconjunto de objetos. Por ejemplo, las reglas de configuración rápida garantizan que los atributos de una cuenta habilitada (**User AccountEnabled**) tengan prioridad sobre otras cuentas.

Se puede definir la prioridad entre conectores. Esto permite que los conectores con mejores datos sean los primeros en aportar los valores.

### <a name="multiple-objects-from-the-same-connector-space"></a>Varios objetos desde el mismo espacio conector
Si tiene varios objetos en el mismo espacio conector unido al mismo objeto de metaverso, se debe ajustar la prioridad. Si varios objetos están en el ámbito de la misma regla de sincronización, el motor de sincronización no es capaz de determinar la prioridad. Es ambiguo qué objeto de origen debe contribuir al valor en el metaverso. Esta configuración se notifica como ambigua incluso si los atributos en el origen tienen el mismo valor.  
![Varios objetos unidos al mismo objeto de mv](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/multiple1.png)  

En este escenario, debe cambiar el ámbito de las reglas de sincronización para que los objetos de origen tengan reglas de sincronización diferentes en el ámbito. Esto permite definir una prioridad diferente.  
![Varios objetos unidos al mismo objeto de mv](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/multiple2.png)  

## <a name="next-steps"></a>Pasos siguientes
* Consulte más detalles sobre el lenguaje de expresiones en el artículo [Descripción de las expresiones de aprovisionamiento declarativo](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md).
* Vea cómo se utiliza aprovisionamiento declarativo integrado en el artículo sobre la [configuración predeterminada](active-directory-aadconnectsync-understanding-default-configuration.md).
* Descubra cómo hacer un cambio práctico utilizando el aprovisionamiento declarativo en [Sincronización de Azure AD Connect: cómo realizar un cambio en la configuración predeterminada](active-directory-aadconnectsync-change-the-configuration.md).
* Siga leyendo sobre cómo los usuarios y contactos se utilizan juntos en el artículo de [descripción de usuarios y contactos](active-directory-aadconnectsync-understanding-users-and-contacts.md).

**Temas de introducción**

* [Sincronización de Azure AD Connect: comprender y personalizar la sincronización](active-directory-aadconnectsync-whatis.md)
* [Integración de las identidades locales con Azure Active Directory](active-directory-aadconnect.md)

**Temas de referencia**

* [Azure AD Connect Sync: referencia de funciones](active-directory-aadconnectsync-functions-reference.md)
