---
title: "Pertenencia a grupos dinámicos basada en atributos en Azure Active Directory | Microsoft Docs"
description: "Procedimientos para crear reglas avanzadas para la pertenencia dinámica a grupos, con operadores y parámetros de reglas de expresión admitidos."
services: active-directory
documentationcenter: 
author: curtand
manager: mtillman
editor: 
ms.assetid: fb434cc2-9a91-4ebf-9753-dd81e289787e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: 
ms.devlang: 
ms.topic: article
ms.date: 12/06/2017
ms.author: curtand
ms.reviewer: piotrci
ms.custom: H1Hack27Feb2017;it-pro
ms.openlocfilehash: 3ece2326a19e32666f46e8b737d15a48e335de6a
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="create-attribute-based-rules-for-dynamic-group-membership-in-azure-active-directory"></a>Creación de reglas de pertenencia dinámica a grupos basadas en atributos en Azure Active Directory
En Azure Active Directory (Azure AD), puede crear reglas avanzadas para habilitar la pertenencia dinámica a grupos basada en atributos complejos. En este artículo se detallan los atributos y la sintaxis para crear reglas de pertenencia dinámica para usuarios o dispositivos.

Cuando cambia cualquier atributo de un usuario, el sistema evalúa todas las reglas de grupos dinámicos de un directorio para ver si la modificación desencadenaría adiciones o retiradas en el grupo. Si un usuario o dispositivo cumple una regla de un grupo, se agrega a este como miembro. Cuando ya no cumple la regla, se quita.

> [!NOTE]
> Puede configurar una regla de pertenencia dinámica a grupos de seguridad o en grupos de Office 365.
>
> Esta característica requiere una licencia de Azure AD Premium P1 para cada miembro de usuario agregado a al menos un grupo dinámico. No es obligatorio asignar licencias a los usuarios para que sean miembros de grupos dinámicos, pero es preciso tener en el inquilino el número mínimo de licencias para cubrirlos a todos. Por ejemplo: si tiene 1000 usuarios únicos en todos los grupos dinámicos del inquilino, debe tener al menos 1000 licencias para Azure AD Premium P1 (o cualquier versión posterior) para cumplir el requisito de licencia.
>
> Puede crear un grupo dinámico para dispositivos o usuarios, pero no se puede crear una regla que contenga tanto objetos de usuario como de dispositivo.
> 
> En este momento, no es posible crear un grupo de dispositivos basado en atributos del usuario propietario. Las reglas de pertenencia de dispositivo solo pueden hacer referencia a atributos inmediatos de objetos de dispositivo del directorio.

## <a name="to-create-an-advanced-rule"></a>Para crear una regla avanzada
1. Inicie sesión en el [Centro de administración de Azure AD](https://aad.portal.azure.com) con una cuenta que sea administrador global o administrador de cuentas de usuario.
2. Seleccione **Usuarios y grupos**.
3. Seleccione **Todos los grupos** y, luego, **Nuevo grupo**.

   ![Add new group](./media/active-directory-groups-dynamic-membership-azure-portal/new-group-creation.png)

4. En la hoja **Grupo** , escriba un nombre y una descripción para el nuevo grupo. En **Tipo de pertenencia**, seleccione **Usuario dinámico** o **Dispositivo dinámico**, dependiendo de si quiere crear una regla para usuarios o dispositivos y, a continuación, seleccione **Agregar una consulta dinámica**. Puede usar el generador de reglas para crear una regla sencilla o escriba usted mismo una regla avanzada. Este artículo contiene más información sobre los atributos de usuario y dispositivo disponibles, así como ejemplos de las reglas avanzadas.

   ![Adición de una regla de pertenencia dinámica](./media/active-directory-groups-dynamic-membership-azure-portal/add-dynamic-group-rule.png)

5. Después de crear la regla, seleccione **Agregar consulta** en la parte superior de la hoja.
6. Seleccione **Crear** on the **Grupo** para crear el grupo.

> [!TIP]
> Es posible que la creación del grupo presente un error si la regla avanzada que escribió no era correcta. Aparecerá una notificación en la esquina superior derecha del portal, con una explicación de por qué el sistema no pudo aceptar la regla. Léala con cuidado para saber cómo debe ajustar la regla para que sea válida.

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
> Las operaciones de cadena y regex no distinguen mayúsculas de minúsculas. También puede realizar comprobaciones Null, usando *null* como constante; por ejemplo, user.department -eq *null*.
> Las cadenas que contienen comillas " deben convertirse en escape con caracteres '; por ejemplo, user.department -eq \`"Sales".

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

Todos los operadores se enumeran a continuación por orden de precedencia, de menor a mayor. Los operadores en la misma línea tienen la misma prioridad:
````
-any -all
-or
-and
-not
-eq -ne -startsWith -notStartsWith -contains -notContains -match –notMatch -in -notIn
````
Todos los operadores se pueden utilizar con o sin el prefijo de guion. Los paréntesis son necesarios solo si la precedencia no cumple sus requisitos.
Por ejemplo:
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
En la tabla siguiente se enumeran los errores comunes y se indica cómo corregirlos:

| Error de análisis de consulta | Uso con error | Uso corregido |
| --- | --- | --- |
| Error: no se admite el atributo. |(user.invalidProperty -eq "Value") |(user.department -eq "value")<br/><br/>Asegúrese de que el atributo se encuentra en la [lista de propiedades admitidas](#supported-properties). |
| Error: no se admite el operador en el atributo. |(user.accountEnabled -contains true) |(user.accountEnabled -eq true)<br/><br/>El operador usado no se admite para el tipo de propiedad (en este ejemplo, -contains no se puede usar con el tipo boolean). Utilice los operadores correctos para el tipo de propiedad. |
| Error: error de compilación de consulta. |1. (user.department -eq "Sales") (user.department -eq "Marketing")<br/><br/>2. (user.userPrincipalName -match "*@domain.ext") |1. Falta el operador. Use -and u -or para predicados de combinación<br/><br/>(user.department -eq "Sales") -or (user.department -eq "Marketing")<br/><br/>2. Error en la expresión regular usada con -match<br/><br/>(user.userPrincipalName -match ".*@domain.ext"), o bien: (user.userPrincipalName -match "@domain.ext$")|

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
| city |Cualquier valor de cadena o *null* |(user.city -eq "value") |
| country |Cualquier valor de cadena o *null* |(user.country -eq "value") |
| companyName | Cualquier valor de cadena o *null* | (user.companyName -eq "value") |
| department |Cualquier valor de cadena o *null* |(user.department -eq "value") |
| DisplayName |Cualquier valor de cadena |(user.displayName -eq "value") |
| employeeId |Cualquier valor de cadena |(user.employeeId -eq "value")<br>(user.employeeId -ne *null*) |
| facsimileTelephoneNumber |Cualquier valor de cadena o *null* |(user.facsimileTelephoneNumber -eq "value") |
| givenName |Cualquier valor de cadena o *null* |(user.givenName -eq "value") |
| jobTitle |Cualquier valor de cadena o *null* |(user.jobTitle -eq "value") |
| mail |Cualquier valor de cadena o *null* (dirección SMTP del usuario) |(user.mail -eq "value") |
| mailNickName |Cualquier valor de cadena (alias de correo electrónico del usuario) |(user.mailNickName -eq "value") |
| mobile |Cualquier valor de cadena o *null* |(user.mobile -eq "value") |
| objectId |GUID del objeto de usuario |(user.objectId -eq "1111111-1111-1111-1111-111111111111") |
| onPremisesSecurityIdentifier | Identificador de seguridad local (SID) para los usuarios que se han sincronizado desde local a la nube. |(user.onPremisesSecurityIdentifier -eq "S-1-1-11-1111111111-1111111111-1111111111-1111111") |
| passwordPolicies |None DisableStrongPassword DisablePasswordExpiration DisablePasswordExpiration, DisableStrongPassword |(user.passwordPolicies -eq "DisableStrongPassword") |
| physicalDeliveryOfficeName |Cualquier valor de cadena o *null* |(user.physicalDeliveryOfficeName -eq "value") |
| postalCode |Cualquier valor de cadena o *null* |(user.postalCode -eq "value") |
| preferredLanguage |Código ISO 639-1 |(user.preferredLanguage -eq "en-US") |
| sipProxyAddress |Cualquier valor de cadena o *null* |(user.sipProxyAddress -eq "value") |
| state |Cualquier valor de cadena o *null* |(user.state -eq "value") |
| streetAddress |Cualquier valor de cadena o *null* |(user.streetAddress -eq "value") |
| surname |Cualquier valor de cadena o *null* |(user.surname -eq "value") |
| telephoneNumber |Cualquier valor de cadena o *null* |(user.telephoneNumber -eq "value") |
| usageLocation |Código de país con dos letras |(user.usageLocation -eq "US") |
| userPrincipalName |Cualquier valor de cadena |(user.userPrincipalName -eq "alias@domain") |
| userType |miembro invitado *null* |(user.userType -eq "Member") |

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

Para especificar un valor nulo en una regla, puede usar el valor *null*. Tenga cuidado de no usar comillas alrededor de la palabra *null*. Si lo hace, se interpretará como un valor de cadena literal. La manera correcta de hacer referencia al valor null es como sigue:
```
   user.mail –ne null
```

## <a name="extension-attributes-and-custom-attributes"></a>Atributos de extensión y atributos personalizados
Se admiten los atributos de extensión y los atributos personalizados en las reglas de pertenencia dinámica.

Los atributos de extensión se sincronizan desde Windows Server AD local y tienen el formato "ExtensionAttributeX", donde X es igual a un número de 1 a 15.
Un ejemplo de una regla que utiliza un atributo de extensión sería
```
(user.extensionAttribute15 -eq "Marketing")
```
Los atributos personalizados se sincronizan desde Windows Server AD local o desde una aplicación SaaS conectada y el formato es "user.extension[GUID]\___[Attribute]", donde [GUID] es el identificador único de AAD para la aplicación que creó el atributo en AAD y [Attribute] es el nombre del atributo cuando se creó.
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
También puede crear una regla que selecciona objetos de dispositivo para la pertenencia de un grupo. Pueden utilizarse los siguientes atributos del dispositivo.

 Atributo de dispositivo  | Valores | Ejemplo
 ----- | ----- | ----------------
 accountEnabled | true false | (device.accountEnabled -eq true)
 DisplayName | Cualquier valor de cadena |(device.displayName -eq "Rob Iphone”)
 deviceOSType | Cualquier valor de cadena | (device.deviceOSType -eq "iPad") -or (device.deviceOSType -eq "iPhone")
 deviceOSVersion | Cualquier valor de cadena | (device.OSVersion -eq "9.1")
 deviceCategory | un nombre de la categoría de dispositivo válido | (device.deviceCategory -eq "BYOD")
 deviceManufacturer | Cualquier valor de cadena | (device.deviceManufacturer -eq "Samsung")
 deviceModel | Cualquier valor de cadena | (device.deviceModel -eq "iPad Air")
 deviceOwnership | Personal, empresa, desconocido | (device.deviceOwnership -eq "Company")
 domainName | Cualquier valor de cadena | (device.domainName -eq "contoso.com")
 enrollmentProfileName | Nombre de perfil de inscripción de dispositivo de Apple | (device.enrollmentProfileName -eq "DEP iPhones")
 isRooted | true false | (device.isRooted -eq true)
 managementType | MDM (para dispositivos móviles)<br>PC (para equipos administrados por el agente de PC de Intune) | (device.managementType -eq "MDM")
 organizationalUnit | cualquier valor de cadena que coincida con el nombre de la unidad organizativa establecido por Active Directory local | (device.organizationalUnit -eq "US PCs")
 deviceId | un id. de dispositivo de Azure AD válido | (device.deviceId -eq "d4fe7726-5966-431c-b3b8-cddc8fdb717d")
 objectId | un id. de objeto de Azure AD válido |  (device.objectId -eq 76ad43c9-32c5-45e8-a272-7b58b58f596d")



## <a name="changing-dynamic-membership-to-static-and-vice-versa"></a>Cambio de la pertenencia dinámica a estática, y viceversa
Es posible cambiar cómo se administra la pertenencia a un grupo. Esto es útil cuando desea mantener el mismo nombre de grupo y el identificador en el sistema, por lo que cualquier referencia existente al grupo sigue siendo válida; crear un nuevo grupo requeriría actualizar esas referencias.

Hay un proceso de actualización en curso en Azure Portal para admitir esta funcionalidad. Mientras tanto, puede usar cmdlets de PowerShell tal y como se muestra a continuación.

> [!WARNING]
> Al cambiar un grupo estático existente a uno dinámico, se eliminarán todos sus miembros y, después, se procesará la regla de pertenencia para agregar nuevos miembros. Si el grupo se usa para controlar el acceso a las aplicaciones o los recursos, los miembros originales podrían perder el acceso hasta que se procese por completo la regla de pertenencia.
>
> Es una práctica recomendada para probar la nueva regla de pertenencia con antelación para asegurarse de que la nueva pertenencia al grupo es la que se preveía.

**Uso de PowerShell para cambiar la administración de la pertenencia a un grupo**

> [!NOTE]
> Para cambiar las propiedades de un grupo dinámico, es preciso usar los cmdlets de **la versión preliminar de** [Azure AD PowerShell, versión 2](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0). Dicha versión preliminar se puede instalar desde [aquí](https://www.powershellgallery.com/packages/AzureADPreview).

Este es un ejemplo e las funciones que cambian la administración de la pertenencia a un grupo existente. Tenga en cuenta que se actúa con precaución para manipular correctamente la propiedad GroupTypes y preservar todos los valores que puedan existir ahí, sin relación con la pertenencia a un grupo dinámico.

```
#The moniker for dynamic groups as used in the GroupTypes property of a group object
$dynamicGroupTypeString = "DynamicMembership"

function ConvertDynamicGroupToStatic
{
    Param([string]$groupId)

    #existing group types
    [System.Collections.ArrayList]$groupTypes = (Get-AzureAdMsGroup -Id $groupId).GroupTypes

    if($groupTypes -eq $null -or !$groupTypes.Contains($dynamicGroupTypeString))
    {
        throw "This group is already a static group. Aborting conversion.";
    }


    #remove the type for dynamic groups, but keep the other type values
    $groupTypes.Remove($dynamicGroupTypeString)

    #modify the group properties to make it a static group: i) change GroupTypes to remove the dynamic type, ii) pause execution of the current rule
    Set-AzureAdMsGroup -Id $groupId -GroupTypes $groupTypes.ToArray() -MembershipRuleProcessingState "Paused"
}

function ConvertStaticGroupToDynamic
{
    Param([string]$groupId, [string]$dynamicMembershipRule)

    #existing group types
    [System.Collections.ArrayList]$groupTypes = (Get-AzureAdMsGroup -Id $groupId).GroupTypes

    if($groupTypes -ne $null -and $groupTypes.Contains($dynamicGroupTypeString))
    {
        throw "This group is already a dynamic group. Aborting conversion.";
    }
    #add the dynamic group type to existing types
    $groupTypes.Add($dynamicGroupTypeString)

    #modify the group properties to make it a static group: i) change GroupTypes to add the dynamic type, ii) start execution of the rule, iii) set the rule
    Set-AzureAdMsGroup -Id $groupId -GroupTypes $groupTypes.ToArray() -MembershipRuleProcessingState "On" -MembershipRule $dynamicMembershipRule
}
```
Para convertir un grupo en estático:
```
ConvertDynamicGroupToStatic "a58913b2-eee4-44f9-beb2-e381c375058f"
```
Para convertir un grupo en dinámico:
```
ConvertStaticGroupToDynamic "a58913b2-eee4-44f9-beb2-e381c375058f" "user.displayName -startsWith ""Peter"""
```
## <a name="next-steps"></a>Pasos siguientes
En estos artículos se proporciona información adicional sobre los grupos en Azure Active Directory.

* [Consulta de los grupos existentes](active-directory-groups-view-azure-portal.md)
* [Crear un nuevo grupo y agregar miembros](active-directory-groups-create-azure-portal.md)
* [Administrar la configuración de un grupo](active-directory-groups-settings-azure-portal.md)
* [Administrar la pertenencia a grupos](active-directory-groups-membership-azure-portal.md)
* [Administrar reglas dinámicas de los usuarios de un grupo](active-directory-groups-dynamic-membership-azure-portal.md)
