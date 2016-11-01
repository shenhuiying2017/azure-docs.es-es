
<properties
	pageTitle="Uso de atributos para crear reglas avanzadas para la pertenencia a grupos en la versión preliminar de Azure Active Directory | Microsoft Azure"
	description="Procedimientos para crear reglas avanzadas para la pertenencia dinámica a grupos, con operadores y parámetros de reglas de expresión admitidos."
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
	ms.date="09/12/2016"
	ms.author="curtand"/>


# Uso de atributos para crear reglas avanzadas para la pertenencia a grupos en la versión preliminar de Azure Active Directory

El Portal de Azure clásico le ofrece la posibilidad de crear reglas avanzadas para permitir pertenencias dinámicas más complejas basadas en atributos para grupos de la versión preliminar de Azure Active Directory (Azure AD). [¿Qué hay en la versión preliminar?](active-directory-preview-explainer.md) En este artículo se describen los atributos y la sintaxis de reglas para crear estas reglas avanzadas.

## Para crear la regla avanzada

1.  Inicie sesión en [Azure Portal](https://portal.azure.com) con una cuenta que tenga el rol de administrador global en el directorio.

2.  Seleccione **Más servicios**, escriba **Usuarios y grupos** en el cuadro de texto y, después, presione **Entrar**.

  ![Apertura de Administración de usuarios](./media/active-directory-groups-dynamic-membership-azure-portal/search-user-management.png)

3.  En la hoja **Usuarios y grupos**, seleccione **Todos los grupos**.

  ![Apertura de la hoja Grupos](./media/active-directory-groups-dynamic-membership-azure-portal/view-groups-blade.png)

4. En la hoja **Usuarios y grupos - Todos los grupos**, seleccione el comando **Agregar**.

  ![Add new group](./media/active-directory-groups-dynamic-membership-azure-portal/add-group-type.png)

5. En la hoja **Grupo**, escriba un nombre y una descripción para el nuevo grupo. Seleccione en **Membership type** (Tipo de pertenencia a grupos): **Dynamic User** (Usuario dinámico) o **Dynamic Device** (Dispositivo dinámico), dependiendo de si quiere crear una regla para usuarios o dispositivos y, a continuación, seleccione **Add dynamic query** (Agregar consulta dinámica). En el caso de atributos usados para reglas de dispositivo, consulte [Uso de atributos para crear reglas para los objetos de dispositivo](#using-attributes-to-create-rules-for-device-objects).

  ![Adición de una regla de pertenencia dinámica](./media/active-directory-groups-dynamic-membership-azure-portal/add-dynamic-group-rule.png)

6. En la hoja **Dynamic membership rules** (Reglas de pertenencia dinámica), escriba su regla en el cuadro **Add dynamic membership advanced rule** (Agregar regla avanzada de pertenencia dinámica), presione Entrar y luego seleccione **Crear** en la parte inferior de la hoja.

7. Seleccione **Crear** en la hoja **Grupo** para crear el grupo.

## Construcción del cuerpo de una regla avanzada

La regla avanzada que se puede crear para las pertenencias dinámicas para grupos es esencialmente una expresión binaria que consta de tres partes y da como resultado true o false. Las tres partes son:

- Parámetro a la izquierda
- Operador binario
- Constante a la derecha

Una regla avanzada completa se parece a esto: (leftParameter binaryOperator "RightConstant"), donde los paréntesis de apertura y cierre son necesarios para toda la expresión binaria, las comillas dobles son necesarias para la constante de la derecha y la sintaxis para el parámetro izquierdo es user.property. Una regla avanzada puede constar de más de una expresión binaria separadas por los operadores lógicos -and, -or y -not.

Los siguientes son ejemplos de una regla avanzada construida correctamente:

- (user.department -eq "Sales") -or (user.department -eq "Marketing")
- (user.department -eq "Sales") -and -not (user.jobTitle -contains "SDE")

Para obtener una lista completa de los parámetros y los operadores de regla de expresión admitidos, vea las secciones siguientes. En el caso de atributos usados para reglas de dispositivo, consulte [Uso de atributos para crear reglas para los objetos de dispositivo](#using-attributes-to-create-rules-for-device-objects).

La longitud total del cuerpo de la regla avanzada no puede superar los 2048 caracteres.

> [AZURE.NOTE]
Las operaciones de cadena y regex no distinguen mayúsculas de minúsculas. También puede realizar comprobaciones Null, usando $null como constante; por ejemplo, user.department -eq $null. Las cadenas que contienen comillas " deben convertirse en escape con caracteres '; por ejemplo, user.department -eq `"Sales".

## Operadores de regla de expresión admitidos
En la tabla siguiente se enumeran todos los operadores de regla de expresión admitidos y su sintaxis para su uso en el cuerpo de la regla avanzada:

| Operador | Sintaxis |
|-----------------|----------------|
| Not Equals | -ne |
| Equals | -eq |
| Not Starts With | -notStartsWith |
| Starts With | -startsWith |
| Not Contains | -notContains |
| Contains | -contains |
| Not Match | -notMatch |
| Match | -match |


## Corrección de errores de consulta
En la tabla siguiente se enumeran los posibles errores y se indica cómo corregirlos si se producen.

| Error de análisis de consulta | Uso con error | Uso corregido |
|-----------------------|-------------------|-----------------------------|
| Error: no se admite el atributo. | (user.invalidProperty -eq "Value") | (user.department -eq "value")<br/>La propiedad debe coincidir con una de la [lista de propiedades admitidas](#supported-properties). |
| Error: no se admite el operador en el atributo. | (user.accountEnabled -contains true) | (user.accountEnabled -eq true)<br/>La propiedad es de tipo booleano. Utilice los operadores admitidos (-eq o -ne) en un tipo booleano de la lista anterior. |
| Error: error de compilación de consulta. | (user.department -eq "Sales") -and (user.department -eq "Marketing")(user.userPrincipalName -match "*@domain.ext") | (user.department -eq "Sales") -and (user.department -eq "Marketing")<br/>El operador lógico debe coincidir con una de las propiedades admitidas enumeradas anteriormente.(user.userPrincipalName -match ".*@domain.ext") o (user.userPrincipalName -match "@domain.ext$") Error en la expresión regular. |
| Error: la expresión binaria no está en un formato adecuado. | (user.department –eq “Sales”) (user.department -eq "Sales")(user.department-eq"Sales") | (user.accountEnabled -eq true) -and (user.userPrincipalName -contains "alias@domain")<br/>La consulta tiene varios errores. El paréntesis no está en el lugar correcto. |
| Error: se ha producido un error desconocido durante la configuración de pertenencias dinámicas. | (user.accountEnabled -eq "True" AND user.userPrincipalName -contains "alias@domain") | (user.accountEnabled -eq true) -and (user.userPrincipalName -contains "alias@domain")<br/>La consulta tiene varios errores. El paréntesis no está en el lugar correcto. |

## Propiedades admitidas
A continuación se muestran todas las propiedades de usuario que puede utilizar en la regla avanzada:

### Propiedades de tipo booleano

Operadores permitidos

* -eq


* -ne


| Propiedades | Valores permitidos | Uso |
|----------------|-----------------|--------------------------------|
| accountEnabled | true false | user.accountEnabled -eq true) |
| dirSyncEnabled | true false null | (user.dirSyncEnabled -eq true) |

### Propiedades de tipo cadena

Operadores permitidos

* -eq


* -ne


* -notStartsWith


* -StartsWith


* -contains


* -notContains


* -match


* -notMatch

| Propiedades | Valores permitidos | Uso |
|----------------------------|-------------------------------------------------------------------------------------------------------|-----------------------------------------------------------|
| city | Cualquier valor de cadena o $null | (user.city -eq "value") |
| country | Cualquier valor de cadena o $null | (user.country -eq "value") |
| department | Cualquier valor de cadena o $null | (user.department -eq "value") |
| DisplayName | Cualquier valor de cadena | (user.displayName -eq "value") |
| facsimileTelephoneNumber | Cualquier valor de cadena o $null | (user.facsimileTelephoneNumber -eq "value") |
| givenName | Cualquier valor de cadena o $null | (user.givenName -eq "value") |
| jobTitle | Cualquier valor de cadena o $null | (user.jobTitle -eq "value") |
| mail | Cualquier valor de cadena o $null (dirección SMTP del usuario) | (user.mail -eq "value") |
| mailNickName | Cualquier valor de cadena (alias de correo electrónico del usuario) | (user.mailNickName -eq "value") |
| mobile | Cualquier valor de cadena o $null | (user.mobile -eq "value") |
| objectId | GUID del objeto de usuario | (user.objectId -eq "1111111-1111-1111-1111-111111111111") |
| passwordPolicies | None DisableStrongPassword DisablePasswordExpiration DisablePasswordExpiration, DisableStrongPassword | (user.passwordPolicies -eq "DisableStrongPassword") |
| physicalDeliveryOfficeName | Cualquier valor de cadena o $null | (user.physicalDeliveryOfficeName -eq "value") |
| postalCode | Cualquier valor de cadena o $null | (user.postalCode -eq "value") |
| preferredLanguage | Código ISO 639-1 | (user.preferredLanguage -eq "es-ES") |
| sipProxyAddress | Cualquier valor de cadena o $null | (user.sipProxyAddress -eq "value") |
| state | Cualquier valor de cadena o $null | (user.state -eq "value") |
| streetAddress | Cualquier valor de cadena o $null | (user.streetAddress -eq "value") |
| surname | Cualquier valor de cadena o $null | (user.surname -eq "value") |
| telephoneNumber | Cualquier valor de cadena o $null | (user.telephoneNumber -eq "value") |
| usageLocation | Código de país con dos letras | (user.usageLocation -eq "US") |
| userPrincipalName | Cualquier valor de cadena | (user.userPrincipalName - eq "alias@domain") |
| userType | miembro invitado $null | (user.userType -eq "Member") |

### Propiedades de colección de cadenas de tipo

Operadores permitidos

* -contains


* -notContains

| Propiedades | Valores permitidos | Uso |
|----------------|---------------------------------------|------------------------------------------------------|
| otherMails | Cualquier valor de cadena | (user.otherMails -contains "alias@domain") |
| proxyAddresses | SMTP: alias@domain smtp: alias@domain | (user.proxyAddresses -contains "SMTP: alias@domain") |

## Atributos de extensión y atributos personalizados
Se admiten los atributos de extensión y los atributos personalizados en las reglas de pertenencia dinámica.

Los atributos de extensión se sincronizan desde Windows Server AD local y tienen el formato "ExtensionAttributeX", donde X es igual a un número de 1 a 15. Un ejemplo de una regla que utiliza un atributo de extensión sería

(user.extensionAttribute15 -eq "Marketing")

Los atributos personalizados se sincronizan desde Windows Server AD local o desde una aplicación de SaaS conectada y el formato es "user.extension_[GUID]\_\__[Attribute]", donde [GUID] es el identificador único de AAD para la aplicación que creó el atributo en AAD y [Attribute] es el nombre del atributo que se creó. Un ejemplo de una regla que utiliza un atributo personalizado es

user.extension\_c272a57b722d4eb29bfe327874ae79cb\_\_OfficeNumber

El nombre de atributo personalizado se puede encontrar en el directorio mediante la consulta de un atributo de usuario a través del explorador de Microsoft Azure AD Graph y la búsqueda del nombre en cuestión.

## Regla de informes directos
Ahora puede rellenar los miembros de un grupo en función del atributo de administrador de un usuario.

**Para configurar un grupo como un grupo de “Administrador”**

1. Siga los pasos del 1 al 5 de [Para crear la regla avanzada](#to-create-the-advanced-rule) y seleccione el **tipo de pertenencia** de **Usuario dinámico**.

2. En la hoja **Dynamic membership rules** (Reglas de pertenencia dinámica), escriba la regla con la siguiente sintaxis:

	Direct Reports for *Direct Reports for {obectID\_of\_manager}*. Un ejemplo de una regla válida para informes directos

					Direct Reports for "62e19b97-8b3d-4d4a-a106-4ce66896a863”

	donde "62e19b97-8b3d-4d4a-a106-4ce66896a863" es el identificador de objeto del administrador. Encontrará el identificador de objeto en Azure AD, en la pestaña **Perfil** de la página del usuario que es el administrador.

3. Al guardar esta regla, todos los usuarios que la cumplen se unirán como miembros del grupo. Pueden pasar unos minutos hasta que empiece a llenarse el grupo.


## Uso de atributos para crear reglas para los objetos de dispositivo

También puede crear una regla que selecciona objetos de dispositivo para la pertenencia de un grupo. Pueden utilizarse los siguientes atributos del dispositivo:

| Propiedades | Valores permitidos | Uso |
|----------------------|---------------------------------|------------------------------------------------------|
| DisplayName | cualquier valor de cadena | (device.displayName -eq "Rob Iphone”) |
| deviceOSType | cualquier valor de cadena | (device.deviceOSType -eq "IOS") |
| deviceOSVersion | cualquier valor de cadena | (device.OSVersion -eq "9.1") |
| isDirSynced | true false null | (device.isDirSynced -eq "true") |
| isManaged | true false null | (device.isManaged -eq "false") |
| isCompliant | true false null | (device.isCompliant -eq "true") |


## Información adicional
En estos artículos se proporciona información adicional sobre los grupos en Azure Active Directory.

* [Consulta de los grupos existentes](active-directory-groups-view-azure-portal.md)
* [Creación de un nuevo grupo y adición de miembros](active-directory-groups-create-azure-portal.md)
* [Administración de la configuración de un grupo](active-directory-groups-settings-azure-portal.md)
* [Administración de la pertenencia a un grupo](active-directory-groups-membership-azure-portal.md)
* [Administración de reglas dinámicas para los usuarios de un grupo](active-directory-groups-dynamic-membership-azure-portal.md)

<!---HONumber=AcomDC_0914_2016-->