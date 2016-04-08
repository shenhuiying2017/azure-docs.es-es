<properties 
	pageTitle="Sincronización de identidades y resistencia de atributos duplicados | Microsoft Azure" 
	description="Nuevo comportamiento a la hora de administrar objetos con conflictos de UPN o ProxyAddress durante la sincronización de directorios con Azure AD Connect." 
	services="active-directory" 
	documentationCenter="" 
	authors="markusvi" 
	manager="stevenpo" 
	editor=""/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/18/2016" 
	ms.author="markusvi"/>




# Sincronización de identidades y resistencia de atributos duplicados

La resistencia de atributos duplicados es una nueva característica que se ha agregado a Azure Active Directory con el fin de eliminar la fricción causada por los conflictos entre **UserPrincipalName** y **ProxyAddress** cuando se ejecuta una de las herramientas de sincronización de Microsoft. Esta característica está actualmente en su versión preliminar.

Por lo general, se necesita que estos dos atributos sean únicos entre los objetos **Usuario**, **Grupo** o **Contacto** de un inquilino determinado de Azure Active Directory.

> [AZURE.NOTE] Solo los usuarios pueden tener UPN.
 

El nuevo comportamiento que permite esta característica se produce en la parte de la nube de canalización de sincronización, por lo tanto es independiente del cliente y pertinente para cualquier producto de sincronización de Microsoft incluidos Azure AD Connect, DirSync y MIM + Connector. El término general “cliente de sincronización” se usará en este documento para representar cualquiera de estos productos.

## Comportamiento actual

Si hay un intento de aprovisionar un nuevo objeto con un valor de UPN o ProxyAddress que infrinja esta restricción de unicidad, Azure Active Directory bloqueará la creación de ese objeto. Igualmente, si un objeto se actualiza con un UPN o ProxyAddress que no sea único, se producirá un error en la actualización. El cliente de sincronización volverá a realizar el intento de aprovisionamiento o actualización tras cada ciclo de exportación y seguirá generando un error hasta que se resuelva el conflicto. Se genera un correo electrónico de informe de errores tras cada intento y el cliente de sincronización registra un error.

## Comportamiento con resistencia de atributos duplicados

En lugar de generar un error completo al aprovisionar o actualizar un objeto con un atributo duplicado, Azure Active Directory "pone en cuarentena" el atributo duplicado que infringe la restricción de unicidad. Si este atributo es necesario para el aprovisionamiento, como en el caso de UserPrincipalName, el servicio asignará un valor de marcador de posición. El formato de estos valores temporales es<br> “***<OriginalPrefix>+<4DigitNumber>@<InitialTenantDomain>.onmicrosoft.com***”.<br> Si el atributo no es necesario, como en el caso de **ProxyAddress**, Azure Active Directory simplemente pone en cuarentena el atributo en conflicto y continúa con la creación de objetos o la actualización.
 
Al poner en cuarentena el atributo, se envía información sobre el conflicto con el mismo correo electrónico de informe de errores utilizado en el comportamiento anterior. Sin embargo, esta información solo aparece en el informe de errores una vez, cuando se produce la cuarentena, y no se volverá a registrar en futuros correos electrónicos. Además, puesto que la exportación de este objeto se ha realizado correctamente, el cliente de sincronización no registrará ningún error y no volverá a intentar la operación de creación o actualización en ciclos de sincronización posteriores.

Para admitir este comportamiento se ha agregado un nuevo atributo a las clases de objeto de usuario, grupo y contacto: <br> **DirSyncProvisioningErrors**

Se trata de un atributo multivalor que se utiliza para almacenar los atributos en conflicto que infringirían la restricción de unicidad si se agregaran normalmente. Se ha habilitado una tarea de temporizador de segundo plano en Azure Active Directory que se ejecuta cada hora para buscar conflictos de atributos duplicados que se han resuelto y quitar automáticamente los atributos en cuestión de la cuarentena.



###Habilitación de resistencia de atributos duplicados

Como se mencionó anteriormente en este tema, este nuevo comportamiento está actualmente en vista previa. Una vez que tenga disponibilidad general, se habilitará para todos los inquilinos automáticamente. Mientras está en estado de vista previa, se puede habilitar descargando la versión más reciente del módulo de PowerShell de Azure Active Directory y ejecutando:

`Set-MsolDirSyncFeature -Feature DuplicateUPNResiliency -Enable $true`

`Set-MsolDirSyncFeature -Feature DuplicateProxyAddressResiliency -Enable $true`

Durante la vista previa, la resistencia de UPN y ProxyAddress puede activarse y deshabilitarse individualmente. Una vez que el comportamiento esté disponible de forma general, la resistencia de ambos se habilitará para todos los inquilinos y no podrá deshabilitarse.



## Identificación de objetos con el atributo DirSyncProvisioningErrors

Existen actualmente dos métodos para identificar los objetos que experimentan estos errores debido a conflictos de propiedad duplicada, Azure Active Directory PowerShell y el Portal de administración de Office 365. Existen planes de ampliación con informes adicionales basados en el portal para el futuro.

### Azure Active Directory PowerShell

Para los cmdlets de PowerShell en este tema, las siguientes afirmaciones son verdaderas:

- Todos los cmdlets siguientes distinguen mayúsculas de minúsculas. 
- Siempre se debe incluir **–ErrorCategoryPropertyConflict**. Actualmente no hay ningún otro tipo de **ErrorCategory**, pero esto se podría ampliar en el futuro.

En primer lugar, comience con la ejecución de **Connect-MsolService** y escriba las credenciales de un administrador de inquilinos.

A continuación, use los cmdlets y operadores siguientes para ver los errores de maneras diferentes:

1. [Ver todos](#see-all)

2. [Por tipo de propiedad](#by-property-type)

3. [Por valor en conflicto](#by-conflicting-value)

4. [Mediante una búsqueda de cadena](#using-a-string-search)

5. [Ordenados](#sorted)

6. [En una cantidad limitada o todos](#in-a-limited-quantity-or-all)

<br>

#### Ver todos 

Una vez conectado, para ver una lista general de errores de aprovisionamiento de atributos en el inquilino ejecute:

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict`

Esto generará un resultado similar al siguiente:

<br> ![Get-MsolDirSyncProvisioningError](./media/active-directory-aadconnectsync-duplicate-attribute-resiliency/1.png "Get-MsolDirSyncProvisioningError") <br>


 
#### Por tipo de propiedad

Para ver los errores ordenados por tipo de propiedad, agregue la marca **-PropertyName** con el argumento **UserPrincipalName** o **ProxyAddresses**:

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -PropertyName UserPrincipalName`

O

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -PropertyName ProxyAddresses`

#### Por valor en conflicto

Para ver los errores relativos a una propiedad específica agregue la marca **-PropertyValue** (**-PropertyName** se debe utilizar también al agregar esta marca):

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -PropertyValue User@domain.com -PropertyName UserPrincipalName`


#### Mediante una búsqueda de cadena

Para realizar una búsqueda de cadenas amplia use la marca **-SearchString**. Se puede utilizar independientemente de todas las marcas anteriores, con la excepción de **-ErrorCategory PropertyConflict**, que es obligatoria:

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -SearchString User`

#### Ordenados

Hay dos indicadores que se pueden usar para ordenar los resultados de una consulta determinada:

1.	**SortField**: los argumentos válidos incluyen DisplayName y UserPrincipalName
 
2.	**SortDirection**: los argumentos válidos incluyen ascendente y descendente

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -SortField UserPrincipalName -SortDirection Ascending`


#### En una cantidad limitada o todos

1. **MaxResults <Int>** se puede utilizar para limitar la consulta a un número específico de valores. 
 
2. **Todos** se puede utilizar para asegurarse de que todos los resultados se recuperan en caso de que exista un gran número de errores.

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -MaxResults 5`

## Portal de administración de Office 365

El informe del portal de Office 365 solo muestra los objetos de **usuario** que presentan estos errores. No se mostrará información acerca de los conflictos entre los objetos de **grupos**, **contactos** o **carpetas públicas**.

**Para ver estos errores en el Portal de administración de Office 365**:

1.	Inicie sesión en **portal.office.com** como administrador de inquilinos

2.	Haga clic en **Usuarios -> Usuarios activos** <br>


    ![Usuarios activos](./media/active-directory-aadconnectsync-duplicate-attribute-resiliency/2.png "Usuarios activos") <br>



 
3.	Se mostrará una advertencia en la parte superior de la página si hay errores de atributos duplicados en cualquier objeto del inquilino: <br> ![Usuarios activos](./media/active-directory-aadconnectsync-duplicate-attribute-resiliency/3.png "Usuarios activos") <br>
 
4.	Para ver los detalles específicos de objeto, elija “Usuarios con errores” en el menú desplegable “Seleccione una vista”: <br> ![Usuarios activos](./media/active-directory-aadconnectsync-duplicate-attribute-resiliency/4.png "Usuarios activos") <br>
 
5.	Haga clic en un objeto para ver más detalles sobre el conflicto, que se mostrarán en la esquina inferior derecha de la pantalla: <br> ![Usuarios activos](./media/active-directory-aadconnectsync-duplicate-attribute-resiliency/5.png "Usuarios activos") <br>
 
### Informe de errores de sincronización de identidades

Cuando se administra un objeto con un conflicto de atributo duplicado con este nuevo comportamiento se incluye una notificación en el correo electrónico estándar de informe de errores de sincronización de identidades que se envía al contacto de notificaciones técnicas del inquilino. Sin embargo, hay un cambio importante en este comportamiento. En el pasado, se incluía información sobre un conflicto de atributo duplicado en cada informe de errores posterior hasta que se resolvía el conflicto. Con este nuevo comportamiento, la notificación de error de un conflicto determinado solo aparece una vez en el momento en que se pone en cuarentena el atributo en conflicto.

Este es un ejemplo del aspecto de la notificación de correo electrónico si hay un conflicto de ProxyAddress:

<br> ![Usuarios activos](./media/active-directory-aadconnectsync-duplicate-attribute-resiliency/6.png "Usuarios activos") <br>
 
## Resolución de conflictos

La estrategia de solución de problemas y las tácticas de resolución no son diferentes de la forma en que se controlaban los errores de atributo duplicado en el pasado. La única diferencia es que la tarea de temporizador limpiará el inquilino en el servicio para agregar automáticamente el atributo en cuestión al objeto adecuado una vez que se resuelva el conflicto.

El siguiente artículo describe diversas estrategias de solución de problemas: [Duplicate or invalid attributes prevent directory synchronization in Office 365 (Atributos duplicados o no válidos que impiden la sincronización de directorios en Office 365)](https://support.microsoft.com/es-ES/kb/2647098).

## Problemas conocidos 

Ninguno de estos problemas conocidos provocará la degradación del servicio o la pérdida de datos. Varios de ellos son estéticos, otros producen errores estándar de atributos duplicados de “*resistencia previa*” que se generan en lugar de poner en cuarentena el archivo en conflicto, y otros que provocan ciertos errores que requieren una solución manual adicional.

**Comportamiento básico:**

1. Un usuario con una configuración de atributo específica continúa recibiendo errores de exportación en lugar de que los atributos se pongan en cuarentena. <br>Por ejemplo:

    a. Se crea un nuevo usuario en AD con un UPN de **Joe@contoso.com** y un valor de ProxyAddress **smtp:Joe@contoso.com**

    b. Las propiedades de este conflicto de objeto con un grupo existente, donde el valor de ProxyAddress es **SMTP:Joe@contoso.com**.

    c. Tras la exportación, se produce un error de **conflicto de ProxyAddress** en lugar de poner los atributos en conflicto en cuarentena. Se reintenta la operación tras cada ciclo de sincronización posterior, como sucedía antes de que se habilitara la característica de resistencia.

2. La tarea de temporizador que busca los conflictos de atributos duplicados resueltos solo compara conflictos de UPN con otros conflictos de UPN. Esto provoca el problema que se muestra en el paso 4 del siguiente escenario:

    a. **UserA@contoso.com** tiene un UPN no único debido a otro valor ProxyAddress de objeto que también tiene ese valor.

    b. A UserA se le proporciona un **UPN MOERA** temporal, **UserA1234@contoso.onmicrosoft.com** y se pone en cuarentena el verdadero valor UPN (como era de esperar).

    c. Se quitará más tarde el valor de ProxyAddress del otro objeto en conflicto.

    d. El UPN de UserA nunca se corrige automáticamente; debe actualizarse manualmente.

3. Si se crean dos grupos locales con la misma dirección SMTP, uno no se podrá aprovisionar en el primer intento con un error estándar de atributo **ProxyAddress** duplicado. Sin embargo, el valor duplicado se pondrá en cuarentena correctamente en el siguiente ciclo de sincronización.

**Cmdlets de PowerShell**:

1. **ImmutableId** / **LastDirSyncTime** no se muestran para la clase de objeto de usuario. 

2. Las marcas **SortField** y **SortDirection** no afectan a los resultados.

3. La utilización de la marca **PropertyValue** sin agregar la marca **PropertyName** produce un error ambiguo.

4. La marca **SearchString** devuelve resultados adicionales si se ejecuta sin las marcas **PropertyValue** y **PropertyName**.



**Informe del Portal de Office**:

1. El mensaje de error detallado para dos objetos en un conjunto de conflictos de UPN es el mismo. Esto indica que se ha modificado o puesto en cuarentena el UPN de ambos cuando, en realidad, solo se modificaron los datos de uno de ellos.

2. El mensaje de error detallado de un conflicto de UPN muestra la propiedad displayName incorrecta de un usuario cuyo UPN se ha modificado o puesto en cuarentena. Por ejemplo:

    a. **El usuario A** se sincroniza primero con **UPN = User@contoso.com**.

    b. A continuación, se intenta sincronizar **el usuario B** con **UPN = User@contoso.com**.

    c. El UPN del **usuario B** se cambia a **User1234@contoso.onmicrosoft.com** y **User@contoso.com** se agrega a **DirSyncProvisioningErrors**.

    d. El mensaje de error para el **usuario B** debería indicar que el **usuario A** ya tiene **User@contoso.com** como UPN, pero sin embargo muestra el displayName propio del **usuario B**.

3. El informe solo puede mostrar información de error detallada para los usuarios con conflictos de **UPN**, no para aquellos con errores de **ProxyAddress** (todavía se sigue investigando si esto es coherente o depende del entorno).




##Consulte también

- [Sincronización de Azure AD Connect](active-directory-aadconnectsync-whatis.md)

- [Integración de las identidades locales con Azure Active Directory](active-directory-aadconnect.md).

<!----HONumber=AcomDC_0323_2016-->