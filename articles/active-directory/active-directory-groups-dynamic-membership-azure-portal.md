---

title: "Pertenencia a grupos dinámicos basada en atributos en Azure Active Directory | Microsoft Docs"
description: "Procedimientos para crear reglas avanzadas para la pertenencia dinámica a grupos, con operadores y parámetros de reglas de expresión admitidos."
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: fb434cc2-9a91-4ebf-9753-dd81e289787e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/04/2017
ms.author: curtand
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: ff2fb126905d2a68c5888514262212010e108a3d
ms.openlocfilehash: 2f1d68c7127324477cfc8b87df0fd82dee7cd1d6
ms.contentlocale: es-es
ms.lasthandoff: 06/17/2017


---
# <a name="create-attribute-based-rules-for-dynamic-group-membership-in-azure-active-directory"></a>Creación de reglas de pertenencia dinámica a grupos basadas en atributos en Azure Active Directory
En Azure Active Directory (Azure AD), puede crear reglas avanzadas para habilitar la pertenencia dinámica a grupos basada en atributos complejos. En este artículo se detallan los atributos y la sintaxis para crear reglas de pertenencia dinámica.

## <a name="to-create-the-advanced-rule"></a>Para crear la regla avanzada
1. Inicie sesión en [Azure Portal](https://portal.azure.com) con una cuenta que tenga el rol de administrador global en el directorio.
2. Seleccione **Más servicios**, escriba **Usuarios y grupos** en el cuadro de texto y presione **Entrar**.

   ![Apertura de Administración de usuarios](./media/active-directory-groups-dynamic-membership-azure-portal/search-user-management.png)
3. En la hoja **Usuarios y grupos**, seleccione **Todos los grupos**.

   ![Apertura de la hoja Grupos](./media/active-directory-groups-dynamic-membership-azure-portal/view-groups-blade.png)
4. En la hoja **Usuarios y grupos - Todos los grupos**, seleccione el comando **Agregar**.

   ![Add new group](./media/active-directory-groups-dynamic-membership-azure-portal/add-group-type.png)
5. En la hoja **Grupo** , escriba un nombre y una descripción para el nuevo grupo. En **Tipo de pertenencia**, seleccione **Usuario dinámico** o **Dispositivo dinámico**, dependiendo de si quiere crear una regla para usuarios o dispositivos y, a continuación, seleccione **Agregar una consulta dinámica**. En el caso de atributos usados para reglas de dispositivo, consulte [Uso de atributos para crear reglas para los objetos de dispositivo](#using-attributes-to-create-rules-for-device-objects).

   ![Adición de una regla de pertenencia dinámica](./media/active-directory-groups-dynamic-membership-azure-portal/add-dynamic-group-rule.png)
6. En la hoja **Reglas de pertenencia dinámica**, escriba su regla en el cuadro **Agregar una regla avanzada de pertenencia dinámica**, presione Entrar y luego seleccione **Crear** en la parte inferior de la hoja.
7. Seleccione **Crear** on the **Grupo** para crear el grupo.

## <a name="constructing-the-body-of-an-advanced-rule"></a>Construcción del cuerpo de una regla avanzada
La regla avanzada que se puede crear para las pertenencias dinámicas para grupos es esencialmente una expresión binaria que consta de tres partes y da como resultado true o false. Las tres partes son:

* Parámetro a la izquierda
* Operador binario
* Constante a la derecha

Una regla avanzada completa es similar a esto: (leftParameter binaryOperator "RightConstant"), donde los paréntesis de apertura y cierre son opcionales para toda la expresión binaria, las comillas dobles también son opcionales, pero solo se requieren para la constante de la derecha cuando es una cadena, y la sintaxis para el parámetro izquierdo es user.property. Una regla avanzada puede constar de más de una expresión binaria separadas por los operadores lógicos -and, -or y -not.

Los siguientes son ejemplos de una regla avanzada construida correctamente:
```
(user.department -eq "Sales") -or (user.department -eq "Marketing")
(user.department -eq "Sales") -and -not (user.jobTitle -contains "SDE")
```
Para obtener una lista completa de los parámetros y los operadores de regla de expresión admitidos, vea las secciones siguientes. En el caso de atributos usados para reglas de dispositivo, consulte [Uso de atributos para crear reglas para los objetos de dispositivo](#using-attributes-to-create-rules-for-device-objects).

La longitud total del cuerpo de la regla avanzada no puede superar los 2048 caracteres.

> [!NOTE]
> Las operaciones de cadena y regex no distinguen mayúsculas de minúsculas. También puede realizar comprobaciones Null, usando $null como constante; por ejemplo, user.department -eq $null.
> Las cadenas que contienen comillas " deben convertirse en escape con caracteres '; por ejemplo, user.department -eq \`"Sales".
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
```
   user.department –eq "Marketing" –and user.country –eq "US"
```
equivale a:
```
   (user.department –eq "Marketing") –and (user.country –eq "US")
```
## <a name="using-the--in-and--notin-operators"></a>Uso de los operadores -In y -notIn

Si desea comparar el valor de un atributo de usuario con un número de valores diferentes, puede usar los operadores -In o -notIn. A continuación se muestra un ejemplo del operador -In:
```
    user.department -In [ "50001", "50002", "50003", “50005”, “50006”, “50007”, “50008”, “50016”, “50020”, “50024”, “50038”, “50039”, “51100” ]
```
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
| CompanyName | Cualquier valor de cadena o $null | (user.CompanyName -eq "value") |
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
| assigendPlans |Cada objeto de la colección expone las siguientes propiedades de cadena: capabilityStatus, service, servicePlanId |user.assignedPlans -any (assignedPlan.servicePlanId -eq "efb87545-963c-4e0d-99df-69c6916d9eb0" -and assignedPlan.capabilityStatus -eq "Enabled") |

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
```
   user.mail –ne null
```
equivale a
```
   user.mail –ne $null
   ```

## <a name="extension-attributes-and-custom-attributes"></a>Atributos de extensión y atributos personalizados
Se admiten los atributos de extensión y los atributos personalizados en las reglas de pertenencia dinámica.

Los atributos de extensión se sincronizan desde Windows Server AD local y tienen el formato "ExtensionAttributeX", donde X es igual a un número de 1 a 15.
Un ejemplo de una regla que utiliza un atributo de extensión sería
```
(user.extensionAttribute15 -eq "Marketing")
```
Los atributos personalizados se sincronizan desde Windows Server AD local o desde una aplicación de SaaS conectada y el formato es "user.extension[GUID]\___[Attribute]", donde [GUID] es el identificador único de AAD para la aplicación que creó el atributo en AAD y [Attribute] es el nombre del atributo que se creó.
Un ejemplo de una regla que utiliza un atributo personalizado es
```
user.extension_c272a57b722d4eb29bfe327874ae79cb__OfficeNumber  
```
El nombre de atributo personalizado se puede encontrar en el directorio mediante la consulta de un atributo de usuario a través del explorador de Windows Azure AD Graph y la búsqueda del nombre en cuestión.

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
| deviceCategory          | Cualquier valor de cadena                | (device.deviceCategory -eq "")                              |
| deviceManufacturer      | Cualquier valor de cadena                | (device.deviceManufacturer - eq "Microsoft")                 |
| deviceModel             | Cualquier valor de cadena                | (device.deviceModel -eq "IPhone 7+")                        |
| deviceOwnership         | Cualquier valor de cadena                | (device.deviceOwnership -eq "")                             |
| domainName              | Cualquier valor de cadena                | (device.domainName -eq "contoso.com")                       |
| enrollmentProfileName   | Cualquier valor de cadena                | (device.enrollmentProfileName -eq "")                       |
| isRooted                | true false                      | (device.deviceOSType -eq true)                              |
| managementType          | Cualquier valor de cadena                | (device.managementType -eq "")                              |
| organizationalUnit      | Cualquier valor de cadena                | (device.organizationalUnit -eq "")                          |
| deviceId                | un valor válido de deviceId                | (device.deviceId -eq "d4fe7726-5966-431c-b3b8-cddc8fdb717d") |
| objectId                | un valor de objectId de AAD válido            | (device.objectId -eq "76ad43c9-32c5-45e8-a272-7b58b58f596d") |




## <a name="next-steps"></a>Pasos siguientes
En estos artículos se proporciona información adicional sobre los grupos en Azure Active Directory.

* [Consulta de los grupos existentes](active-directory-groups-view-azure-portal.md)
* [Crear un nuevo grupo y agregar miembros](active-directory-groups-create-azure-portal.md)
* [Administrar la configuración de un grupo](active-directory-groups-settings-azure-portal.md)
* [Administrar la pertenencia a grupos](active-directory-groups-membership-azure-portal.md)
* [Administrar reglas dinámicas de los usuarios de un grupo](active-directory-groups-dynamic-membership-azure-portal.md)

