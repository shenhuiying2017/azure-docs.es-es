
<properties
    pageTitle="Uso de atributos para crear reglas avanzadas| Microsoft Azure"
    description="Procedimientos para crear reglas avanzadas para un grupo, entre los que se incluyen parámetros y operadores de reglas de expresiones."
    services="active-directory"
    documentationCenter=""
    authors="curtand"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="11/01/2016"
    ms.author="curtand"/>



# <a name="using-attributes-to-create-advanced-rules"></a>Uso de atributos para crear reglas avanzadas

El Portal de Azure clásico proporciona la capacidad necesaria para crear reglas avanzadas para habilitar pertenencias dinámicas basadas en atributos más complejas para grupos de Azure Active Directory (Azure AD).  

Cuando los atributos de un usuario cambian, el sistema evalúa todas las reglas de grupos dinámicos de un directorio para ver si la modificación de los atributos del usuario en cuestión desencadenaría adiciones o retiradas en el grupo. Si un usuario cumple una regla de un grupo, se agrega a este como miembro. Si, por el contrario, deja de cumplir la regla del grupo al que pertenece, se le quita como miembro de este.

## <a name="to-create-the-advanced-rule"></a>Para crear la regla avanzada

1. En el [Portal de Azure clásico](https://manage.windowsazure.com), seleccione **Active Directory**y luego abra el directorio de su organización.

2. Seleccione la pestaña **Grupos** y abra el grupo que desea editar.

3. Seleccione la pestaña **Configurar**, seleccione la opción **Regla avanzada** y escriba la regla avanzada en el cuadro de texto.

## <a name="constructing-the-body-of-an-advanced-rule"></a>Construcción del cuerpo de una regla avanzada

La regla avanzada que se puede crear para las pertenencias dinámicas para grupos es esencialmente una expresión binaria que consta de tres partes y da como resultado true o false. Las tres partes son:

- Parámetro a la izquierda
- Operador binario
- Constante a la derecha

Una regla avanzada completa se parece a esto: (leftParameter binaryOperator "RightConstant"), donde los paréntesis de apertura y cierre son necesarios para toda la expresión binaria, las comillas dobles son necesarias para la constante de la derecha y la sintaxis para el parámetro izquierdo es user.property. Una regla avanzada puede constar de más de una expresión binaria separadas por los operadores lógicos -and, -or y -not.
Los siguientes son ejemplos de una regla avanzada construida correctamente:

- (user.department -eq "Sales") -or (user.department -eq "Marketing")
- (user.department -eq "Sales") -and -not (user.jobTitle -contains "SDE")

Para obtener una lista completa de los parámetros y los operadores de regla de expresión admitidos, vea las secciones siguientes.

La longitud total del cuerpo de la regla avanzada no puede superar los 2048 caracteres.

> [AZURE.NOTE]
>Las operaciones de cadena y regex no distinguen mayúsculas de minúsculas. También puede realizar comprobaciones Null, usando $null como constante; por ejemplo, user.department -eq $null.
Las cadenas que contienen comillas " deben convertirse en escape con caracteres '; por ejemplo, user.department -eq \`"Sales".

## <a name="supported-expression-rule-operators"></a>Operadores de regla de expresión admitidos
En la tabla siguiente se enumeran todos los operadores de regla de expresión admitidos y su sintaxis para su uso en el cuerpo de la regla avanzada:

| Operador        | Sintaxis         |
|-----------------|----------------|
| Not Equals      | -ne            |
| Equals          | -eq            |
| Not Starts With | -notStartsWith |
| Starts With     | -startsWith    |
| Not Contains    | -notContains   |
| Contains        | -contains      |
| Not Match       | -notMatch      |
| Match           | -match         |


## <a name="query-error-remediation"></a>Corrección de errores de consulta
En la tabla siguiente se enumeran los posibles errores y se indica cómo corregirlos si se producen.

| Error de análisis de consulta     | Uso con error       | Uso corregido             |
|-----------------------|-------------------|-----------------------------|
| Error: no se admite el atributo.                                      | (user.invalidProperty -eq "Value")       | (user.department -eq "value")<br/>La propiedad debe coincidir con una de la [lista de propiedades admitidas](#supported-properties).                          |
| Error: no se admite el operador en el atributo.                       | (user.accountEnabled -contains true)                                                                               | (user.accountEnabled -eq true)<br/>La propiedad es de tipo booleano. Utilice los operadores admitidos (-eq o -ne) en un tipo booleano de la lista anterior.                                                                                                                                   |
| Error: error de compilación de consulta.                                      | (user.department -eq "Sales") -and (user.department -eq "Marketing")(user.userPrincipalName -match "*@domain.ext") | (user.department -eq "Sales") -and (user.department -eq "Marketing")<br/>El operador lógico debe coincidir con una de la lista de propiedades admitidas anterior. (user.userPrincipalName -match ".*@domain.ext")or(user.userPrincipalName -match "@domain.ext$")Error en la expresión regular. |
| Error: la expresión binaria no está en un formato adecuado.                     | (user.department –eq “Sales”) (user.department -eq "Sales")(user.department-eq"Sales")                             | (user.accountEnabled -eq true) -and (user.userPrincipalName -contains "alias@domain")<br/>La consulta tiene varios errores. El paréntesis no está en el lugar correcto.                                                                                                                            |
| Error: se ha producido un error desconocido durante la configuración de pertenencias dinámicas. | (user.accountEnabled -eq "True" AND user.userPrincipalName -contains "alias@domain")                               | (user.accountEnabled -eq true) -and (user.userPrincipalName -contains "alias@domain")<br/>La consulta tiene varios errores. El paréntesis no está en el lugar correcto.                                                                                                                            |

## <a name="supported-properties"></a>Propiedades admitidas
A continuación se muestran todas las propiedades de usuario que puede utilizar en la regla avanzada:

### <a name="properties-of-type-boolean"></a>Propiedades de tipo booleano

Operadores permitidos

* -eq


* -ne


| Propiedades     | Valores permitidos  | Uso                          |
|----------------|-----------------|--------------------------------|
| accountEnabled | true false      | user.accountEnabled -eq true)  |
| dirSyncEnabled | true false null | (user.dirSyncEnabled -eq true) |

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

| Propiedades                 | Valores permitidos                                                                                        | Uso                                                     |
|----------------------------|-------------------------------------------------------------------------------------------------------|-----------------------------------------------------------|
| city                       | Cualquier valor de cadena o $null                                                                           | (user.city -eq "value")                                   |
| country                    | Cualquier valor de cadena o $null                                                                            | (user.country -eq "value")                                |
| department                 | Cualquier valor de cadena o $null                                                                          | (user.department -eq "value")                             |
| DisplayName                | Cualquier valor de cadena                                                                                 | (user.displayName -eq "value")                            |
| facsimileTelephoneNumber   | Cualquier valor de cadena o $null                                                                           | (user.facsimileTelephoneNumber -eq "value")               |
| givenName                  | Cualquier valor de cadena o $null                                                                           | (user.givenName -eq "value")                              |
| jobTitle                   | Cualquier valor de cadena o $null                                                                           | (user.jobTitle -eq "value")                               |
| mail                       | Cualquier valor de cadena o $null (dirección SMTP del usuario)                                                  | (user.mail -eq "value")                                   |
| mailNickName               | Cualquier valor de cadena (alias de correo electrónico del usuario)                                                            | (user.mailNickName -eq "value")                           |
| mobile                     | Cualquier valor de cadena o $null                                                                           | (user.mobile -eq "value")                                 |
| objectId                   | GUID del objeto de usuario                                                                            | (user.objectId -eq "1111111-1111-1111-1111-111111111111") |
| passwordPolicies           | None DisableStrongPassword DisablePasswordExpiration DisablePasswordExpiration, DisableStrongPassword |   (user.passwordPolicies -eq "DisableStrongPassword")                                                      |
| physicalDeliveryOfficeName | Cualquier valor de cadena o $null                                                                            | (user.physicalDeliveryOfficeName -eq "value")             |
| postalCode                 | Cualquier valor de cadena o $null                                                                            | (user.postalCode -eq "value")                             |
| preferredLanguage          | Código ISO 639-1                                                                                        | (user.preferredLanguage -eq "en-US")                      |
| sipProxyAddress            | Cualquier valor de cadena o $null                                                                            | (user.sipProxyAddress -eq "value")                        |
| state                      | Cualquier valor de cadena o $null                                                                            | (user.state -eq "value")                                  |
| streetAddress              | Cualquier valor de cadena o $null                                                                            | (user.streetAddress -eq "value")                          |
| surname                    | Cualquier valor de cadena o $null                                                                            | (user.surname -eq "value")                                |
| telephoneNumber            | Cualquier valor de cadena o $null                                                                            | (user.telephoneNumber -eq "value")                        |
| usageLocation              | Código de país con dos letras                                                                           | (user.usageLocation -eq "US")                             |
| userPrincipalName          | Cualquier valor de cadena                                                                                     | (user.userPrincipalName -eq "alias@domain")               |
| userType                   | miembro invitado $null                                                                                    | (user.userType -eq "Member")                              |

### <a name="properties-of-type-string-collection"></a>Propiedades de colección de cadenas de tipo

Operadores permitidos

* -contains


* -notContains

| Propiedades      | Valores permitidos                        | Uso                                                |
|----------------|---------------------------------------|------------------------------------------------------|
| otherMails     | Cualquier valor de cadena                      | (user.otherMails -contains "alias@domain")           |
| proxyAddresses | SMTP: alias@domain smtp: alias@domain | (user.proxyAddresses -contains "SMTP: alias@domain") |

## <a name="extension-attributes-and-custom-attributes"></a>Atributos de extensión y atributos personalizados
Se admiten los atributos de extensión y los atributos personalizados en las reglas de pertenencia dinámica.

Los atributos de extensión se sincronizan desde Windows Server AD local y tienen el formato "ExtensionAttributeX", donde X es igual a un número de 1 a 15.
Un ejemplo de una regla que utiliza un atributo de extensión sería

(user.extensionAttribute15 -eq "Marketing")

Los atributos personalizados se sincronizan desde Windows Server AD local o desde una aplicación de SaaS conectada y el formato es "user.extension[GUID]\___[Attribute]", donde [GUID] es el identificador único de AAD para la aplicación que creó el atributo en AAD y [Attribute] es el nombre del atributo que se creó.
Un ejemplo de una regla que utiliza un atributo personalizado es

user.extension_c272a57b722d4eb29bfe327874ae79cb__OfficeNumber  

El nombre de atributo personalizado se puede encontrar en el directorio mediante la consulta de un atributo de usuario a través del explorador de Windows Azure AD Graph y la búsqueda del nombre en cuestión.

## <a name="direct-reports-rule"></a>Regla de informes directos
Ahora puede rellenar los miembros de un grupo en función del atributo de administrador de un usuario.

**Para configurar un grupo como un grupo de  “Administrador”**

1. En el Portal de Azure clásico, haga clic en **Active Directory**y luego en el nombre del directorio de su organización.

2. Seleccione la pestaña **Grupos** y abra el grupo que desea editar.

3. Seleccione la pestaña **Configurar** y luego seleccione **REGLA AVANZADA**.

4. Escriba la regla con la sintaxis siguiente:

    Direct Reports for *Direct Reports for {obectID_of_manager}*. Un ejemplo de una regla válida para informes directos

                    Direct Reports for "62e19b97-8b3d-4d4a-a106-4ce66896a863”

    donde "62e19b97-8b3d-4d4a-a106-4ce66896a863" es el identificador de objeto del administrador. Encontrará el identificador de objeto en Azure AD, en la pestaña **Perfil** de la página del usuario que es el administrador.

3. Al guardar esta regla, todos los usuarios que la cumplen se unirán como miembros del grupo. Pueden pasar unos minutos hasta que empiece a llenarse el grupo.


## <a name="using-attributes-to-create-rules-for-device-objects"></a>Uso de atributos para crear reglas para los objetos de dispositivo

También puede crear una regla que selecciona objetos de dispositivo para la pertenencia de un grupo. Pueden utilizarse los siguientes atributos del dispositivo:

| Propiedades              | Valores permitidos                  | Uso                                                       |
|-------------------------|---------------------------------|-------------------------------------------------------------|
| DisplayName             | Cualquier valor de cadena                | (device.displayName -eq "Rob Iphone”)                       |
| deviceOSType            | Cualquier valor de cadena                | (device.deviceOSType -eq "IOS")                             |
| deviceOSVersion         | Cualquier valor de cadena                | (device.OSVersion -eq "9.1")                                |
| isDirSynced             | true false null                 | (device.isDirSynced -eq "true")                             |
| isManaged               | true false null                 | (device.isManaged -eq "false")                              |
| isCompliant             | true false null                 | (device.isCompliant -eq "true")                             |
| deviceCategory          | Cualquier valor de cadena                | (device.deviceCategory -eq "")                              |
| deviceManufacturer      | Cualquier valor de cadena                | (device.deviceManufacturer - eq "Microsoft")                 |
| deviceModel             | Cualquier valor de cadena                | (device.deviceModel -eq "IPhone 7+")                        |
| deviceOwnership         | Cualquier valor de cadena                | (device.deviceOwnership -eq "")                             |
| domainName              | Cualquier valor de cadena                | (device.domainName -eq "contoso.com")                       |
| enrollmentProfileName   | Cualquier valor de cadena                | (device.enrollmentProfileName -eq "")                       |
| isRooted                | true false null                 | (device.deviceOSType -eq "true")                            |
| managementType          | Cualquier valor de cadena                | (device.managementType -eq "")                              |
| organizationalUnit      | Cualquier valor de cadena                | (device.organizationalUnit -eq "")                          |
| deviceId                | un valor válido de deviceId                | (device.deviceId -eq "d4fe7726-5966-431c-b3b8-cddc8fdb717d" |

> [AZURE.NOTE]
> No se pueden crear estas reglas de dispositivo mediante la lista desplegable de "regla simple" en el Portal de Azure clásico.


## <a name="additional-information"></a>Información adicional
Estos artículos proporcionan información adicional sobre Azure Active Directory.

* [Solución de problemas relacionados con las pertenencias dinámicas para grupos](active-directory-accessmanagement-troubleshooting.md)

* [Administración del acceso a los recursos con grupos de Azure Active Directory](active-directory-manage-groups.md)

* [Cmdlets de Azure Active Directory para configurar las opciones de grupo](active-directory-accessmanagement-groups-settings-cmdlets.md)

* [Índice de artículos sobre la administración de aplicaciones en Azure Active Directory](active-directory-apps-index.md)

* [Integración de las identidades locales con Azure Active Directory](active-directory-aadconnect.md)



<!--HONumber=Oct16_HO2-->


