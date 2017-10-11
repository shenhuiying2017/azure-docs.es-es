---
title: "Sincronización de identidades y resistencia de atributos duplicados | Microsoft Docs"
description: "Nuevo comportamiento a la hora de administrar objetos con conflictos de UPN o ProxyAddress durante la sincronización de directorios con Azure AD Connect."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 537a92b7-7a84-4c89-88b0-9bce0eacd931
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: markvi
ms.openlocfilehash: 7a8700e70f64851a0c5e5e8c6b31ec7a6884a96c
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/03/2017
---
# <a name="identity-synchronization-and-duplicate-attribute-resiliency"></a>Sincronización de identidades y resistencia de atributos duplicados
La resistencia de atributos duplicados es una característica de Azure Active Directory que eliminará la fricción causada por los conflictos entre **UserPrincipalName** y **ProxyAddress** al ejecutar una de las herramientas de sincronización de Microsoft.

Por lo general, se necesita que estos dos atributos sean únicos entre los objetos **User**, **Group** o **Contact** de un inquilino determinado de Azure Active Directory.

> [!NOTE]
> Solo los usuarios pueden tener UPN.
> 
> 

El nuevo comportamiento que permite esta característica se produce en la parte de la nube de canalización de sincronización, por lo tanto es independiente del cliente y pertinente para cualquier producto de sincronización de Microsoft incluidos Azure AD Connect, DirSync y MIM + Connector. El término general "cliente de sincronización" se usará en este documento para representar cualquiera de estos productos.

## <a name="current-behavior"></a>Comportamiento actual
Si hay un intento de aprovisionar un nuevo objeto con un valor de UPN o ProxyAddress que infrinja esta restricción de unicidad, Azure Active Directory bloqueará la creación de ese objeto. Igualmente, si un objeto se actualiza con un UPN o ProxyAddress que no sea único, se producirá un error en la actualización. El cliente de sincronización realiza el intento de aprovisionamiento o actualización tras cada ciclo de exportación y sigue generando un error hasta que se resuelva el conflicto. Se genera un correo electrónico de informe de errores tras cada intento y el cliente de sincronización registra un error.

## <a name="behavior-with-duplicate-attribute-resiliency"></a>Comportamiento con resistencia de atributos duplicados
En lugar de generar un error completo al aprovisionar o actualizar un objeto con un atributo duplicado, Azure Active Directory "pone en cuarentena" el atributo duplicado que infringe la restricción de unicidad. Si este atributo es necesario para el aprovisionamiento, como en el caso de UserPrincipalName, el servicio asigna un valor de marcador de posición. El formato de estos valores temporales es  
"***<OriginalPrefix>+<Número4Dígitos>@<InitialTenantDomain>.onmicrosoft.com***".  
Si el atributo no es necesario, como en el caso de **ProxyAddress**, Azure Active Directory simplemente pone en cuarentena el atributo en conflicto y continúa con la creación o actualización de objetos.

Al poner en cuarentena el atributo, se envía información sobre el conflicto con el mismo correo electrónico de informe de errores utilizado en el comportamiento anterior. Sin embargo, esta información solo aparece en el informe de errores una vez, cuando se produce la cuarentena; no se vuelve a registrar en futuros correos electrónicos. Además, puesto que la exportación de este objeto se ha realizado correctamente, el cliente de sincronización no registrará ningún error y no volverá a intentar la operación de creación o actualización en ciclos de sincronización posteriores.

Para admitir este comportamiento se ha agregado un nuevo atributo a las clases de objeto User, Group y Contact:   
**DirSyncProvisioningErrors**

Se trata de un atributo multivalor que se utiliza para almacenar los atributos en conflicto que infringirían la restricción de unicidad si se agregaran normalmente. Se ha habilitado una tarea de temporizador de segundo plano en Azure Active Directory que se ejecuta cada hora para buscar conflictos de atributos duplicados que se han resuelto y quitar automáticamente los atributos en cuestión de la cuarentena.

### <a name="enabling-duplicate-attribute-resiliency"></a>Habilitación de resistencia de atributos duplicados
Resistencia de atributo duplicados será el nuevo comportamiento predeterminado en todos los inquilinos de Azure Active Directory. Estarán activos de forma predeterminada para todos los inquilinos que habilitaron la sincronización por primera vez el 22 de agosto de 2016, o en cualquier fecha posterior. Los inquilinos que habilitaron la sincronización antes de esta fecha tendrán la característica habilitada en lotes. Este lanzamiento comenzará en septiembre de 2016 y se enviará una notificación por correo electrónico al contacto de notificación técnica de cada inquilino con la fecha específica en que se habilitará la característica.

> [!NOTE]
> Una vez que se ha activado la resistencia de atributo duplicados no se puede deshabilitar.

Para comprobar si la característica está habilitada para su inquilino, puede hacerlo descargando la versión más reciente del módulo de PowerShell de Azure Active Directory y ejecutando lo siguiente:

`Get-MsolDirSyncFeatures -Feature DuplicateUPNResiliency`

`Get-MsolDirSyncFeatures -Feature DuplicateProxyAddressResiliency`

> [!NOTE]
> Ya no puede usar el cmdlet Set-MsolDirSyncFeature para habilitar de forma proactiva la característica Resistencia de atributos duplicados antes de activarla en el inquilino. Para poder probar la característica, deberá crear un inquilino nuevo de Azure Active Directory.

## <a name="identifying-objects-with-dirsyncprovisioningerrors"></a>Identificación de objetos con el atributo DirSyncProvisioningErrors
Existen actualmente dos métodos para identificar los objetos que experimentan estos errores debido a conflictos de propiedad duplicada, Azure Active Directory PowerShell y el Portal de administración de Office 365. Existen planes de ampliación con informes adicionales basados en el portal para el futuro.

### <a name="azure-active-directory-powershell"></a>Azure Active Directory PowerShell
Para los cmdlets de PowerShell en este tema, las siguientes afirmaciones son verdaderas:

* Todos los cmdlets siguientes distinguen mayúsculas de minúsculas.
* Siempre se debe incluir **–ErrorCategoryPropertyConflict** . Actualmente no hay ningún otro tipo de **ErrorCategory**, pero esto se podría ampliar en el futuro.

En primer lugar, comience con la ejecución de **Connect-MsolService** y escriba las credenciales de un administrador de inquilinos.

A continuación, use los cmdlets y operadores siguientes para ver los errores de maneras diferentes:

1. [Ver todos](#see-all)
2. [Por tipo de propiedad](#by-property-type)
3. [Por valor en conflicto](#by-conflicting-value)
4. [Mediante una búsqueda de cadena](#using-a-string-search)
5. [Ordenados](#sorted)
6. [En una cantidad limitada o todos](#in-a-limited-quantity-or-all)

#### <a name="see-all"></a>Ver todos
Una vez conectado, para ver una lista general de errores de aprovisionamiento de atributos en el inquilino ejecute:

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict`

Esto genera un resultado similar al siguiente:   
 ![Get-MsolDirSyncProvisioningError](./media/active-directory-aadconnectsyncservice-duplicate-attribute-resiliency/1.png "MsolDirSyncProvisioningError Get")  

#### <a name="by-property-type"></a>Por tipo de propiedad
Para ver los errores ordenados por tipo de propiedad, agregue la marca **-PropertyName** con el argumento **UserPrincipalName** o **ProxyAddresses**:

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -PropertyName UserPrincipalName`

O

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -PropertyName ProxyAddresses`

#### <a name="by-conflicting-value"></a>Por valor en conflicto
Para ver los errores relativos a una propiedad específica, agregue la marca **-PropertyValue** (**-PropertyName** se debe utilizar también al agregar esta marca):

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -PropertyValue User@domain.com -PropertyName UserPrincipalName`

#### <a name="using-a-string-search"></a>Mediante una búsqueda de cadena
Para realizar una búsqueda de cadenas amplia, use la marca **-SearchString** . Se puede utilizar independientemente de todas las marcas anteriores, con la excepción de **-ErrorCategory PropertyConflict**, que es obligatoria:

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -SearchString User`

#### <a name="in-a-limited-quantity-or-all"></a>En una cantidad limitada o todos
1. **MaxResults <Int>** se puede utilizar para limitar la consulta a un número específico de valores.
2. **All** se puede utilizar para asegurarse de que todos los resultados se recuperan en caso de que exista un gran número de errores.

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -MaxResults 5`

## <a name="office-365-admin-portal"></a>Portal de administración de Office 365
Puede ver los errores de sincronización de directorios en el Centro de administración de Office 365. En el informe del portal de Office 365 solo aparecen los objetos **User** que presentan estos errores. No se muestran datos acerca de los conflictos entre los objetos **Groups**, **Contacts**.

![Usuarios activos](./media/active-directory-aadconnectsyncservice-duplicate-attribute-resiliency/1234.png "Usuarios activos")

Para obtener instrucciones acerca de cómo ver los errores de sincronización de directorios en el Centro de administración de Office 365, consulte [Identificar problemas de sincronización de directorios en Office 365](https://support.office.com/en-us/article/Identify-directory-synchronization-errors-in-Office-365-b4fc07a5-97ea-4ca6-9692-108acab74067).

### <a name="identity-synchronization-error-report"></a>Informe de errores de sincronización de identidades
Cuando se administra un objeto con un conflicto de atributo duplicado con este nuevo comportamiento se incluye una notificación en el correo electrónico estándar de informe de errores de sincronización de identidades que se envía al contacto de notificaciones técnicas del inquilino. Sin embargo, hay un cambio importante en este comportamiento. En el pasado, se incluía información sobre un conflicto de atributo duplicado en cada informe de errores posterior hasta que se resolvía el conflicto. Con este nuevo comportamiento, la notificación de error de un conflicto determinado solo aparece una vez en el momento en que se pone en cuarentena el atributo en conflicto.

Este es un ejemplo del aspecto de la notificación de correo electrónico si hay un conflicto de ProxyAddress:   
    ![Usuarios activos](./media/active-directory-aadconnectsyncservice-duplicate-attribute-resiliency/6.png "Usuarios activos")  

## <a name="resolving-conflicts"></a>Resolución de conflictos
La estrategia de solución de problemas y las tácticas de resolución no son diferentes de la forma en que se controlaban los errores de atributo duplicado en el pasado. La única diferencia es que la tarea de temporizador limpiará el inquilino en el servicio para agregar automáticamente el atributo en cuestión al objeto adecuado una vez que se resuelva el conflicto.

En el siguiente artículo se describen diversas estrategias de solución de problemas: [Atributos duplicados o no válidos evitar la sincronización de directorios en Office 365](https://support.microsoft.com/kb/2647098).

## <a name="known-issues"></a>Problemas conocidos
Ninguno de estos problemas conocidos provoca la degradación del servicio o la pérdida de datos. Varios de ellos son estéticos, otros producen errores estándar de atributos duplicados de “*resistencia previa*” que se generan en lugar de poner en cuarentena el archivo en conflicto, y otros que provocan ciertos errores que requieren una solución manual adicional.

**Comportamiento básico:**

1. Los objetos con configuraciones de atributos específicos continúan recibiendo errores de exportación, en lugar de que los atributos duplicados se pongan en cuarentena.  
   Por ejemplo:
   
    a. Se crea un nuevo usuario en AD con un UPN de **Joe@contoso.com** y ProxyAddress **smtp:Joe@contoso.com**
   
    b. Las propiedades de este objeto entran en conflicto con un grupo existente, donde el valor de ProxyAddress es **SMTP:Joe@contoso.com**.
   
    c. Tras la exportación, se produce un error de **conflicto de ProxyAddress** en lugar de poner los atributos en conflicto en cuarentena. Se reintenta la operación tras cada ciclo de sincronización posterior, como sucedía antes de que se habilitara la característica de resistencia.
2. Si se crean dos grupos locales con la misma dirección SMTP, uno no se podrá aprovisionar en el primer intento con un error estándar de atributo **ProxyAddress** duplicado. Sin embargo, el valor duplicado se pondrá en cuarentena correctamente en el siguiente ciclo de sincronización.

**Informe del Portal de Office**:

1. El mensaje de error detallado para dos objetos en un conjunto de conflictos de UPN es el mismo. Esto indica que se ha modificado o puesto en cuarentena el UPN de ambos cuando, en realidad, solo se modificaron los datos de uno de ellos.
2. El mensaje de error detallado de un conflicto de UPN muestra la propiedad displayName incorrecta de un usuario cuyo UPN se ha modificado o puesto en cuarentena. Por ejemplo:
   
    a. El **usuario A** se sincroniza primero con **UPN = User@contoso.com**.
   
    b. A continuación, se trata de sincronizar el **usuario B** con **UPN = User@contoso.com**.
   
    c. El UPN del **usuario B** se cambia a **User1234@contoso.onmicrosoft.com** y **User@contoso.com** se agrega a **DirSyncProvisioningErrors**.
   
    d. El mensaje de error para el **usuario B** debe indicar que el **usuario A** ya tiene **User@contoso.com** como UPN; no obstante, muestra el valor de nombreParaMostrar propio del **usuario B**.

**Informe de errores de sincronización de identidades**:

El vínculo de los *pasos necesarios para resolver este problema* no es correcto:  
    ![Usuarios activos](./media/active-directory-aadconnectsyncservice-duplicate-attribute-resiliency/6.png "Usuarios activos")  

Debe apuntar a [https://aka.ms/duplicateattributeresiliency](https://aka.ms/duplicateattributeresiliency).

## <a name="see-also"></a>Consulte también
* [Sincronización de Azure AD Connect](active-directory-aadconnectsync-whatis.md)
* [Integración de las identidades locales con Azure Active Directory](active-directory-aadconnect.md)
* [Identificar problemas de sincronización de directorios en Office 365](https://support.office.com/en-us/article/Identify-directory-synchronization-errors-in-Office-365-b4fc07a5-97ea-4ca6-9692-108acab74067)

