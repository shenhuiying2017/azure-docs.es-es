---
title: "Rellenado dinámico de grupos en función de los atributos de objeto en Azure Active Directory | Microsoft Docs"
description: "Procedimientos para crear reglas avanzadas para la pertenencia a grupos, con operadores y parámetros de reglas de expresión admitidos."
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 04813a42-d40a-48d6-ae96-15b7e5025884
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/19/2017
ms.author: curtand
ms.reviewer: kairaz.contractor
ms.custom: oldportal
ms.translationtype: HT
ms.sourcegitcommit: f2ac16c2f514aaa7e3f90fdf0d0b6d2912ef8485
ms.openlocfilehash: b136d3841243ad7aa88786f76b2d31e5dfae9079
ms.contentlocale: es-es
ms.lasthandoff: 09/08/2017

---

# <a name="populate-groups-dynamically-based-on-object-attributes"></a>Rellenado dinámico de grupos en función de los atributos de objeto
El Portal de Azure clásico proporciona la capacidad de habilitar pertenencias dinámicas basadas en atributos más complejos para grupos de Azure Active Directory (Azure AD).  

Cuando cambia cualquier atributo de un usuario, el sistema evalúa todas las reglas de grupos dinámicos de un directorio para ver si la modificación desencadenaría adiciones o retiradas en el grupo. Si un usuario o dispositivo cumple una regla de un grupo, se agrega a este como miembro. Cuando ya no cumple la regla, se quita.

> [!NOTE]
> - Puede configurar una regla de pertenencia dinámica a grupos de seguridad o en grupos de Office 365.
>
> - Esta característica requiere una licencia de Azure AD Premium P1 para cada miembro de usuario agregado a al menos un grupo dinámico.
>
> - Puede crear un grupo dinámico para dispositivos o usuarios, pero no se puede crear una regla que contenga tanto objetos de usuario como de dispositivo.

> - En este momento no es posible crear un grupo de dispositivos basado en atributos de usuario propietario. Las reglas de pertenencia de dispositivo solo pueden hacer referencia a atributos inmediatos de objetos de dispositivo del directorio.

## <a name="to-create-an-advanced-rule"></a>Para crear una regla avanzada
1. En el [Portal de Azure clásico](https://manage.windowsazure.com), seleccione **Active Directory**y luego abra el directorio de su organización.
2. Seleccione la pestaña **Grupos** y abra el grupo que desea editar.
3. Seleccione la pestaña **Configurar**, seleccione la opción **Regla avanzada** y escriba la regla avanzada en el cuadro de texto.

## <a name="constructing-the-body-of-an-advanced-rule"></a>Construcción del cuerpo de una regla avanzada
La regla avanzada que se puede crear para las pertenencias dinámicas para grupos es esencialmente una expresión binaria que consta de tres partes y da como resultado true o false. Las tres partes son:

* Parámetro a la izquierda
* Operador binario
* Constante a la derecha

Una regla avanzada completa se parece a esto: (leftParameter binaryOperator "RightConstant"), donde los paréntesis de apertura y cierre son necesarios para toda la expresión binaria, las comillas dobles son necesarias para la constante de la derecha y la sintaxis para el parámetro izquierdo es user.property. Una regla avanzada puede constar de más de una expresión binaria separadas por los operadores lógicos -and, -or y -not.
Los siguientes son ejemplos de una regla avanzada construida correctamente:

* (user.department -eq "Sales") -or (user.department -eq "Marketing")
* (user.department -eq "Sales") -and -not (user.jobTitle -contains "SDE")

Para obtener una lista completa de los parámetros y los operadores de regla de expresión admitidos, vea las secciones siguientes.


Tenga en cuenta que la propiedad debe llevar un prefijo con el tipo de objeto correcto: usuario o dispositivo.
La regla que aparece a continuación producirá un error de validación: mail –ne null

La regla correcta sería:

user.mail –ne null

La longitud total del cuerpo de la regla avanzada no puede superar los 2048 caracteres.

> [!NOTE]
> Las operaciones de cadena y regex no distinguen mayúsculas de minúsculas.
> Las cadenas que contienen comillas " deben convertirse en escape con caracteres '; por ejemplo, user.department -eq \`"Sales".
> Utilice comillas solamente para los valores de tipo cadena y solo use comillas inglesas.
>
>

## <a name="supported-expression-rule-operators"></a>Operadores de regla de expresión admitidos
En la tabla siguiente se enumeran todos los operadores de regla de expresión admitidos y su sintaxis para su uso en el cuerpo de la regla avanzada:

| Operador | Sintaxis |
| --- | --- |
| Not Equals |-ne |
| Equals |-eq |
| Not Starts With |-notStartsWith |
| Starts With |-startsWith |
| Not Contains |-notContains |
| Contains |-contains |
| Not Match |-notMatch |
| Match |-match |
| En el | -in |
| No en el | -notIn |

## <a name="operator-precedence"></a>Precedencia de operadores

Todos los operadores se enumeran a continuación por orden de precedencia, de menor a mayor, los operadores en la misma línea tienen la misma prioridad -any -all -or -and -not -eq -ne -startsWith -notStartsWith -contains -notContains -match –notMatch -in -notIn

Todos los operadores se pueden utilizar con o sin prefijo de guión.

Tenga en cuenta que no siempre se necesitan paréntesis, solo necesita agregar paréntesis si la precedencia no cumple sus requisitos como, por ejemplo:

   user.department –eq "Marketing" –and user.country –eq "US"

equivale a:

   (user.department –eq "Marketing") –and (user.country –eq "US")

## <a name="using-the--in-and--notin-operators"></a>Uso de los operadores -In y -notIn

Si desea comparar el valor de un atributo de usuario con un número de valores diferentes, puede usar los operadores -In o -notIn. A continuación se muestra un ejemplo del operador -In:

    user.department -In [ "50001", "50002", "50003", “50005”, “50006”, “50007”, “50008”, “50016”, “50020”, “50024”, “50038”, “50039”, “51100” ]

Tenga en cuenta el uso de "[" y "]" al principio y al final de la lista de valores. Esta condición se evalúa como True si el valor de user.department es igual a uno de los valores en la lista.

## <a name="query-error-remediation"></a>Corrección de errores de consulta
En la tabla siguiente se enumeran los posibles errores y se indica cómo corregirlos si se producen.

| Error de análisis de consulta | Uso con error | Uso corregido |
| --- | --- | --- |
| Error: no se admite el atributo. |(user.invalidProperty -eq "Value") |(user.department -eq "value")<br/>La propiedad debe coincidir con una de la [lista de propiedades admitidas](#supported-properties). |
| Error: no se admite el operador en el atributo. |(user.accountEnabled -contains true) |(user.accountEnabled -eq true)<br/>La propiedad es de tipo booleano. Utilice los operadores admitidos (-eq o -ne) en un tipo booleano de la lista anterior. |
| Error: error de compilación de consulta. |(user.department -eq "Sales") -and (user.department -eq "Marketing")(user.userPrincipalName -match "*@domain.ext") |(user.department -eq "Sales") -and (user.department -eq "Marketing")<br/>El operador lógico debe coincidir con una de la lista de propiedades admitidas anterior.(user.userPrincipalName -match ".*@domain.ext")or(user.userPrincipalName -match "@domain.ext$")Error en la expresión regular. |
| Error: la expresión binaria no está en un formato adecuado. |(user.department –eq “Sales”) (user.department -eq "Sales")(user.department-eq"Sales") |(user.accountEnabled -eq true) -and (user.userPrincipalName -contains "alias@domain")<br/>La consulta tiene varios errores. El paréntesis no está en el lugar correcto. |
| Error: se ha producido un error desconocido durante la configuración de pertenencias dinámicas. |(user.accountEnabled -eq "True" AND user.userPrincipalName -contains "alias@domain") |(user.accountEnabled -eq true) -and (user.userPrincipalName -contains "alias@domain")<br/>La consulta tiene varios errores. El paréntesis no está en el lugar correcto. |

## <a name="supported-properties"></a>Propiedades admitidas
A continuación se muestran todas las propiedades de usuario que puede utilizar en la regla avanzada:

### <a name="properties-of-type-boolean"></a>Propiedades de tipo booleano
Operadores permitidos

* -eq
* -ne

| Propiedades | Valores permitidos | Uso |
| --- | --- | --- |
| accountEnabled |true false |user.accountEnabled -eq true |
| dirSyncEnabled |true false |user.dirSyncEnabled -eq true |

### <a name="properties-of-type-string"></a>Propiedades de tipo cadena
Operadores permitidos

* -eq
* -ne
* -notStartsWith
* -startsWith
* -contains
* -notContains
* -match
* -notMatch
* -in
* -notIn

| Propiedades | Valores permitidos | Uso |
| --- | --- | --- |
| city |Cualquier valor de cadena o $null |(user.city -eq "value") |
| country |Cualquier valor de cadena o $null |(user.country -eq "value") |
| companyName | Cualquier valor de cadena o $null | (user.companyName -eq "value") |
| department |Cualquier valor de cadena o $null |(user.department -eq "value") |
| DisplayName |Cualquier valor de cadena |(user.displayName -eq "value") |
| facsimileTelephoneNumber |Cualquier valor de cadena o $null |(user.facsimileTelephoneNumber -eq "value") |
| givenName |Cualquier valor de cadena o $null |(user.givenName -eq "value") |
| jobTitle |Cualquier valor de cadena o $null |(user.jobTitle -eq "value") |
| mail |Cualquier valor de cadena o $null (dirección SMTP del usuario) |(user.mail -eq "value") |
| mailNickName |Cualquier valor de cadena (alias de correo electrónico del usuario) |(user.mailNickName -eq "value") |
| mobile |Cualquier valor de cadena o $null |(user.mobile -eq "value") |
| objectId |GUID del objeto de usuario |(user.objectId -eq "1111111-1111-1111-1111-111111111111") |
| onPremisesSecurityIdentifier | Identificador de seguridad local (SID) para los usuarios que se han sincronizado desde local a la nube. |(user.onPremisesSecurityIdentifier -eq "S-1-1-11-1111111111-1111111111-1111111111-1111111") |
| passwordPolicies |None DisableStrongPassword DisablePasswordExpiration DisablePasswordExpiration, DisableStrongPassword |(user.passwordPolicies -eq "DisableStrongPassword") |
| physicalDeliveryOfficeName |Cualquier valor de cadena o $null |(user.physicalDeliveryOfficeName -eq "value") |
| postalCode |Cualquier valor de cadena o $null |(user.postalCode -eq "value") |
| preferredLanguage |Código ISO 639-1 |(user.preferredLanguage -eq "en-US") |
| sipProxyAddress |Cualquier valor de cadena o $null |(user.sipProxyAddress -eq "value") |
| state |Cualquier valor de cadena o $null |(user.state -eq "value") |
| streetAddress |Cualquier valor de cadena o $null |(user.streetAddress -eq "value") |
| surname |Cualquier valor de cadena o $null |(user.surname -eq "value") |
| telephoneNumber |Cualquier valor de cadena o $null |(user.telephoneNumber -eq "value") |
| usageLocation |Código de país con dos letras |(user.usageLocation -eq "US") |
| userPrincipalName |Cualquier valor de cadena |(user.userPrincipalName -eq "alias@domain") |
| userType |miembro invitado $null |(user.userType -eq "Member") |

### <a name="properties-of-type-string-collection"></a>Propiedades de colección de cadenas de tipo
Operadores permitidos

* -contains
* -notContains

| Propiedades | Valores permitidos | Uso |
| --- | --- | --- |
| otherMails |Cualquier valor de cadena |(user.otherMails -contains "alias@domain") |

| proxyAddresses |SMTP: alias@domain smtp: alias@domain |(user.proxyAddresses -contains "SMTP: alias@domain") |

## <a name="multi-value-properties"></a>Propiedades de varios valores
Operadores permitidos

* -any (se satisface cuando al menos un elemento de la colección coincide con la condición)
* -all (se satisface cuando todos los elementos de la colección coinciden con la condición)

| Propiedades | Valores | Uso |
| --- | --- | --- |
| assignedPlans |Cada objeto de la colección expone las siguientes propiedades de cadena: capabilityStatus, service, servicePlanId |user.assignedPlans -any (assignedPlan.servicePlanId -eq "efb87545-963c-4e0d-99df-69c6916d9eb0" -and assignedPlan.capabilityStatus -eq "Enabled") |

Las propiedades de varios valores son colecciones de objetos del mismo tipo. Puede usar los operadores -any y -all para aplicar una condición a uno o todos los elementos de la colección, respectivamente. Por ejemplo:

assignedPlans es una propiedad de varios valores que muestra todos los planes de servicio asignados al usuario. En la expresión siguiente se seleccionarán los usuarios que tienen el plan de servicio Exchange Online (Plan 2) que también está en estado Habilitado:

```
user.assignedPlans -any (assignedPlan.servicePlanId -eq "efb87545-963c-4e0d-99df-69c6916d9eb0" -and assignedPlan.capabilityStatus -eq "Enabled")
```

(El identificador GUID identifica el plan de servicio Exchange Online [Plan 2]).

> [!NOTE]
> Esto es útil si desea identificar a todos los usuarios para los que se habilitó una funcionalidad de Office 365 (u otro servicio en línea de Microsoft), por ejemplo, para establecerlos como destino de cierto conjunto de directivas.

En la expresión siguiente se seleccionarán todos los usuarios que tengan algún plan de servicio asociado con el servicio de Intune (identificado por el nombre de servicio "SCO"):
```
user.assignedPlans -any (assignedPlan.service -eq "SCO" -and assignedPlan.capabilityStatus -eq "Enabled")
```

## <a name="use-of-null-values"></a>Uso de valores nulos

Para especificar un valor nulo en una regla, puede usar "null" o $null. Ejemplo:

   user.mail –ne null equivale a user.mail –ne $null

## <a name="extension-attributes-and-custom-attributes"></a>Atributos de extensión y atributos personalizados
Se admiten los atributos de extensión y los atributos personalizados en las reglas de pertenencia dinámica.

Los atributos de extensión se sincronizan desde Windows Server AD local y tienen el formato "ExtensionAttributeX", donde X es igual a un número de 1 a 15.
Un ejemplo de una regla que utiliza un atributo de extensión sería

(user.extensionAttribute15 -eq "Marketing")

Los atributos personalizados se sincronizan desde Windows Server AD local o desde una aplicación SaaS conectada y el formato es "user.extension[GUID]\___[Attribute]", donde [GUID] es el identificador único de AAD para la aplicación que creó el atributo en AAD y [Attribute] es el nombre del atributo cuando se creó.
Un ejemplo de una regla que utiliza un atributo personalizado es

user.extension_c272a57b722d4eb29bfe327874ae79cb__OfficeNumber  

El nombre de atributo personalizado se puede encontrar en el directorio mediante la consulta de un atributo de usuario a través del explorador de Windows Azure AD Graph y la búsqueda del nombre en cuestión. Actualmente no se admiten atributos multivalor sincronizados desde Active Directory local. 

## <a name="direct-reports-rule"></a>Regla de "subordinados directos"
Puede crear un grupo con todos los subordinados directos de un administrador. Cuando los subordinados directos de un administrador cambien en el futuro, la pertenencia del grupo se ajustará automáticamente.

> [!NOTE]
> 1. Para que la regla funcione, asegúrese de que la propiedad de **identificador de administrador** esté establecida correctamente en los usuarios del inquilino. Puede comprobar el valor actual de un usuario en la **pestaña Perfil**.
> 2. Esta regla admite solo subordinados **directos**. Actualmente no es posible crear un grupo para una jerarquía anidada, por ejemplo, un grupo que incluya subordinados directos y sus subordinados.

**Para configurar el grupo**

1. Siga los pasos del 1 al 5 de la sección [Para crear la regla avanzada](#to-create-the-advanced-rule) y seleccione el **tipo de pertenencia** de **Usuario dinámico**.
2. En la hoja **Dynamic membership rules** (Reglas de pertenencia dinámica), escriba la regla con la siguiente sintaxis:

    *Direct Reports for "{obectID_of_manager}"*

    Ejemplo de una regla válida:
```
                    Direct Reports for "62e19b97-8b3d-4d4a-a106-4ce66896a863"
```
    where “62e19b97-8b3d-4d4a-a106-4ce66896a863” is the objectID of the manager. The object ID can be found on manager's **Profile tab**.
3. Después de guardar la regla, todos los usuarios con el valor de identificador de administrador especificado se agregarán al grupo.

## <a name="using-attributes-to-create-rules-for-device-objects"></a>Uso de atributos para crear reglas para los objetos de dispositivo
También puede crear una regla que selecciona objetos de dispositivo para la pertenencia de un grupo. Pueden utilizarse los siguientes atributos del dispositivo:

| Propiedades              | Valores permitidos                  | Uso                                                       |
|-------------------------|---------------------------------|-------------------------------------------------------------|
| accountEnabled          | true false                      | (device.accountEnabled -eq true)                            |
| DisplayName             | Cualquier valor de cadena                | (device.displayName -eq "Rob Iphone”)                       |
| deviceOSType            | Cualquier valor de cadena                | (device.deviceOSType -eq "IOS")                             |
| deviceOSVersion         | Cualquier valor de cadena                | (device.OSVersion -eq "9.1")                                |
| deviceCategory          | un nombre de la categoría de dispositivo válido    | (device.deviceCategory -eq "BYOD")                          |
| deviceManufacturer      | Cualquier valor de cadena                | (device.deviceManufacturer - eq "Microsoft")                 |
| deviceModel             | Cualquier valor de cadena                | (device.deviceModel -eq "IPhone 7+")                        |
| deviceOwnership         | Personal, empresa               | (device.deviceOwnership -eq "Company")                      |
| domainName              | Cualquier valor de cadena                | (device.domainName -eq "contoso.com")                       |
| enrollmentProfileName   | Cualquier valor de cadena                | (device.enrollmentProfileName -eq "")                       |
| isRooted                | true false                      | (device.deviceOSType -eq true)                              |
| managementType          | Cualquier valor de cadena                | (device.managementType -eq "")                              |
| organizationalUnit      | Cualquier valor de cadena                | (device.organizationalUnit -eq "")                          |
| deviceId                | un valor válido de deviceId                | (device.deviceId -eq "d4fe7726-5966-431c-b3b8-cddc8fdb717d") |
| objectId                | un valor de objectId de AAD válido            | (device.objectId -eq "76ad43c9-32c5-45e8-a272-7b58b58f596d") |

> [!NOTE]
> No se pueden crear estas reglas de dispositivo mediante la lista desplegable de "regla simple" en el Portal de Azure clásico.
>
>

## <a name="next-steps"></a>Pasos siguientes
Estos artículos proporcionan información adicional sobre Azure Active Directory.

* [Solución de problemas relacionados con las pertenencias dinámicas para grupos](active-directory-accessmanagement-troubleshooting.md)
* [Administración del acceso a los recursos con grupos de Azure Active Directory](active-directory-manage-groups.md)
* [Cmdlets de Azure Active Directory para configurar las opciones de grupo](active-directory-accessmanagement-groups-settings-cmdlets.md)
* [Índice de artículos sobre la administración de aplicaciones en Azure Active Directory](active-directory-apps-index.md)
* [Integración de las identidades locales con Azure Active Directory](active-directory-aadconnect.md)

