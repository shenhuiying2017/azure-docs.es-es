---
title: Vigencia de tokens configurable en Azure Active Directory | Microsoft Docs
description: "Esta característica la usan los administradores y desarrolladores para especificar las vigencias de los tokens emitidos por Azure AD."
services: active-directory
documentationcenter: 
author: billmath
manager: femila
editor: 
ms.assetid: 06f5b317-053e-44c3-aaaa-cf07d8692735
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/17/2016
ms.author: billmath
translationtype: Human Translation
ms.sourcegitcommit: d7e635f7e84ac53399309bf4ec8a7fa9e70e3728
ms.openlocfilehash: aa18efb0c622ae38eea0de28f25c72788e6d0f20
ms.lasthandoff: 03/01/2017


---
# <a name="configurable-token-lifetimes-in-azure-active-directory-public-preview"></a>Vigencia de tokens configurables de Azure Active Directory (versión preliminar pública)
> [!NOTE]
> Esta funcionalidad se encuentra actualmente en versión preliminar.  Debe estar preparado para deshacer o eliminar los cambios.  Estamos ofreciendo esta característica para que los usuarios la prueben durante la versión preliminar; sin embargo, hay ciertos aspectos que requieren una [suscripción a Azure AD Premium](active-directory-get-started-premium.md) una vez que esté disponible con carácter general.
> 
> 

## <a name="introduction"></a>Introducción
Esta característica la usan los administradores y desarrolladores para especificar las vigencias de los tokens emitidos por Azure AD. La vigencia de los tokens se puede configurar para todas las aplicaciones de una organización, para una aplicación multiinquilino (multiorganización) o para una entidad de servicio específica de una organización.

En Azure AD, un objeto de directiva representa un conjunto de reglas que se exigen en algunas o todas las aplicaciones de una organización.  Cada tipo de directiva tiene una estructura única con un conjunto de propiedades que luego se aplican a los objetos a los que están asignadas.

Una directiva se puede designar como el valor predeterminado para una organización. Esta directiva se aplica a cualquier aplicación que resida dentro de esa organización, siempre y cuando no se haya reemplazado por una directiva con una prioridad más alta. Las directivas pueden asignarse también a aplicaciones específicas. El orden de prioridad varía según el tipo de directiva.

Las directivas de vigencia del token se pueden configurar para tokens de actualización, tokens de acceso, tokens de sesión y tokens de id.

### <a name="access-tokens"></a>Tokens de acceso
Un cliente utiliza un token de acceso para acceder a un recurso protegido. Solo se puede utilizar un token de acceso para una combinación específica de usuario, cliente y recursos. Los tokens de acceso no se pueden revocar y son válidos hasta que caducan. Un individuo maltintencionado que haya obtenido un token de acceso puede usarlo durante toda su vigencia.  El ajuste de la vigencia del token de acceso es un balance entre la mejora del rendimiento del sistema y el aumento de la cantidad de tiempo que el cliente conserva el acceso después de que la cuenta de usuario está deshabilitada.  Se consigue un rendimiento mejorado del sistema al reducir el número de veces que un cliente necesita adquirir un token de acceso nuevo. 

### <a name="refresh-tokens"></a>Tokens de actualización
Cuando un cliente adquiere un token de acceso para acceder a un recurso protegido, recibe un token de actualización y un token de acceso. El token de actualización se usa para obtener nuevos pares de tokens de acceso/actualización cuando el token de acceso actual expira. Los tokens de actualización están vinculados a combinaciones de usuario y cliente. Se pueden revocar y su validez se comprueba cada vez que se usan.

Es importante diferenciar entre clientes públicos y confidenciales. Para más información sobre los diferentes tipos de clientes, consulte [RFC 6749](https://tools.ietf.org/html/rfc6749#section-2.1).

#### <a name="token-lifetimes-with-confidential-client-refresh-tokens"></a>Vigencia de los tokens de actualización de cliente confidencial
Los clientes confidenciales son aplicaciones que pueden almacenar de forma segura una contraseña de cliente (secreto), lo que les permite comprobar que las solicitudes proceden de la aplicación cliente y no de un individuo malintencionado. Por ejemplo, una aplicación web es un cliente confidencial ya que puede almacenar un secreto de cliente en el servidor web y, por tanto, este secreto no se expone. Como estos flujos son más seguros, las vigencias predeterminadas de los tokens de actualización emitidos a estos flujos son más altas y no se pueden cambiar mediante directiva.

#### <a name="token-lifetimes-with-public-client-refresh-tokens"></a>Vigencia de los tokens de actualización de cliente público 

Los clientes públicos no pueden almacenar de forma segura una contraseña (secreto) de cliente. Por ejemplo, una aplicación de iOS o Android no puede ofuscar un secreto del propietario del recurso y, como tal, se considera a la aplicación un cliente público.  Se pueden establecer directivas sobre los recursos para evitar que los tokens de actualización de clientes públicos anteriores a un período especificado obtengan un nuevo par de tokens de acceso/actualización (tiempo máximo de inactividad del token de actualización).  Además, se pueden usar directivas para establecer un período de tiempo más allá del cual los tokens de actualización ya no se aceptan (antigüedad máxima del token de actualización).  El ajuste de la vigencia del token de actualización le permite controlar cuándo y con qué frecuencia es necesario que el usuario vuelva a escribir las credenciales en lugar de volver a autenticarse de forma silenciosa al usar una aplicación cliente pública.

### <a name="id-tokens"></a>Tokens de identificador
Los tokens de identificador se pasan a clientes nativos y sitios web y contienen información del perfil de un usuario. Un token de identificador se enlaza a una combinación específica de usuario y cliente. Los tokens de identificador se consideran válidos hasta que expiran.  Normalmente, una aplicación web relaciona la vigencia de la sesión de un usuario en la aplicación con la vigencia del token de identificador emitido para el usuario.  El ajuste de la vigencia del token de identificador le permite controlar la frecuencia con la que la aplicación web expirará la sesión de la aplicación y requerirá que el usuario se vuelva a autenticar en Azure AD (de forma silenciosa o interactiva).

### <a name="single-sign-on-session-token"></a>Token de sesión de inicio de sesión único
Cuando un usuario se autentica en Azure AD y marca la casilla "Mantener la sesión iniciada", se establece una sesión de inicio de sesión único con Azure AD y el explorador del usuario.  El token de sesión de inicio de sesión único, en forma de cookie, representa esta sesión. Es importante tener en cuenta que el token de sesión SSO no está enlazado a una aplicación específica cliente o de recursos. Los tokens de sesión SSO se pueden revocar y su validez se activa cada vez que se utilizan.

Hay dos tipos de tokens de sesión SSO. Los tokens de sesión persistentes se almacenan como cookies persistentes mediante el explorador y los tokens de sesión no persistentes se almacenan como cookies de sesión (estos se destruyen cuando se cierra el explorador).

Los tokens de sesión no persistentes tienen una duración de 24 horas, mientras que los tokens persistentes tienen una duración de 180 días. Cada vez que se utilice el token de sesión SSO dentro de su período de validez, este se amplía otras 24 horas o 180 días. Si el token de sesión SSO no se usa dentro de su periodo de validez, se considerará caducado y ya no se aceptará. 

Se pueden usar directivas para establecer un período de tiempo después que se emitiera el token de la primera sesión, transcurrido el cual los tokens de sesión ya no se aceptan (antigüedad máxima del token de sesión).  El ajuste de la vigencia del token de sesión le permite controlar cuándo y con qué frecuencia el usuario debe volver a escribir las credenciales en lugar de autenticarse de forma silenciosa cuando se usa una aplicación web.

### <a name="token-lifetime-policy-properties"></a>Propiedades de la directiva de vigencia del token
Una directiva de vigencia del token es un tipo de objeto de directiva que contiene reglas de vigencia del token.  Las propiedades de la directiva se usan para controlar las vigencias de tokens especificados.  Si no se establece ninguna directiva, el sistema aplica el valor de vigencia predeterminado.

### <a name="configurable-token-lifetime-properties"></a>Propiedades de vigencia de tokens configurables
| Propiedad | Cadena de propiedad de directiva | Afecta a | Valor predeterminado | Mínima | Máxima |
| --- | --- | --- | --- | --- | --- |
| Vigencia del token de acceso |AccessTokenLifetime |Tokens de acceso, tokens de identificador, tokens de SAML2 |1 hora |10 minutos |1 día |
| Tiempo máximo de inactividad del token de actualización |MaxInactiveTime |Tokens de actualización |14 días |10 minutos |90 días |
| Antigüedad máxima del token de actualización (un solo factor) |MaxAgeSingleFactor |Tokens de actualización (para los usuarios) |90 días |10 minutos |Hasta que se revoca* |
| Antigüedad máxima del token de actualización (varios factores) |MaxAgeMultiFactor |Tokens de actualización (para los usuarios) |90 días |10 minutos |Hasta que se revoca* |
| Antigüedad máxima del token de sesión (un solo factor) |MaxAgeSessionSingleFactor** |Tokens de sesión (persistentes y no persistentes) |Hasta que se revoca |10 minutos |Hasta que se revoca* |
| Antigüedad máxima del token de sesión (varios factores) |MaxAgeSessionMultiFactor*** |Tokens de sesión (persistentes y no persistentes) |Hasta que se revoca |10 minutos |Hasta que se revoca* |

* *365 días es la longitud explícita máxima que se puede establecer para estos atributos.
* **Si no se establece MaxAgeSessionSingleFactor, este valor adquiere el valor de MaxAgeSingleFactor. Si no se establece ningún parámetro, la propiedad adquiere el valor predeterminado (Hasta que se revoca).
* Si MaxAgeSessionMultiFactor no se establece, este valor adquiere entonces el valor de MaxAgeMultiFactor. Si no se establece ningún parámetro, la propiedad adquiere el valor predeterminado (Hasta que se revoca).

### <a name="exceptions"></a>Excepciones
| Propiedad | Afecta a | Valor predeterminado |
| --- | --- | --- |
| Tiempo máximo de inactividad del token de actualización (usuarios federados con información de revocación insuficiente) |Tokens de actualización (emitidos para usuarios federados con información de revocación insuficiente) |12 horas |
| Tiempo máximo de inactividad del token de actualización (clientes confidenciales) |Tokens de actualización (emitidos para clientes confidenciales) |90 días |
| Antigüedad máxima del token de actualización (emitido para clientes confidenciales) |Tokens de actualización (emitidos para clientes confidenciales) |Hasta que se revoca |

### <a name="priority-and-evaluation-of-policies"></a>Prioridad y evaluación de las directivas
Se pueden crear directivas de vigencia del token y asignarse a aplicaciones, organizaciones y entidades de servicio específicas. Esto significa que se pueden aplicar varias directivas a una aplicación específica. La directiva de vigencia del token que entra en vigor sigue estas reglas:

* Si una directiva se asigna explícitamente a la entidad de servicio, se aplicará. 
* Si no hay ninguna directiva que se asigne explícitamente a la entidad de servicio, se aplicará una directiva asignada explícitamente a la organización primaria de la entidad de servicio. 
* Si no hay ninguna directiva que se asigne explícitamente a la entidad de servicio o a la organización, se aplicará la directiva asignada a la aplicación. 
* Si no se ha asignado ninguna directiva a la entidad de servicio, la organización o el objeto de aplicación, se aplicarán los valores predeterminados (consulte la tabla anterior).

Para más información sobre la relación entre objetos de aplicación y objetos de entidad de servicio en Azure AD, consulte [Objetos de aplicación y de entidad de servicio de Azure Active Directory](active-directory-application-objects.md).

La validez del token se evalúa en el momento en el que se usa. La directiva con la prioridad más alta en la aplicación a la que se accede es la que se aplica.

> [!NOTE]
> Ejemplo
> 
> Un usuario desea acceder a dos aplicaciones web, A y B. 
> 
> * Ambas aplicaciones están en la misma organización primaria. 
> * La directiva 1 de vigencia del token con una antigüedad máxima del token de sesión de ocho horas se establece como valor predeterminado de la organización primaria.
> * La aplicación web A es una aplicación web de uso habitual que no está vinculada a ninguna directiva. 
> * La aplicación web B se usa para procesos confidenciales y su entidad de servicio está vinculada a la directiva 2 de vigencia del token con una antigüedad máxima del token de sesión de 30 minutos.
> 
> A las 12:00 p.m. el usuario abre una nueva sesión en el explorador e intenta acceder a la aplicación web A. El usuario es redirigido a Azure AD y se le pide que inicie sesión. Esta acción coloca una cookie con un token de sesión en el explorador. El usuario es redirigido de nuevo a la aplicación web A con un token de identificador que le permite acceder a la aplicación.
> 
> A continuación, a las 12:15 p.m., el usuario intenta acceder a la aplicación web B. El explorador redirige a Azure AD, que detecta la cookie de sesión. La entidad de servicio de la aplicación web B está vinculada a la directiva 2, pero también forma parte de la organización primaria con la directiva 1 predeterminada. La directiva 2 se aplica porque las directivas vinculadas a entidades de servicio tienen una prioridad más alta que las directivas predeterminadas de la organización. El token de sesión se emitió originalmente en los últimos 30 minutos, por lo que se considera válido. El usuario es redirigido de nuevo a la aplicación web B con un token de identificador que le concede acceso.
> 
> A la 1:00 p.m., el usuario intenta navegar a. la aplicación web A y se le redirige a Azure AD. La aplicación web A no está vinculada a ninguna directiva, pero como está en una organización con la directiva predeterminada 1, se aplica esta directiva. Se detecta la cookie de sesión que se emitió originalmente en las últimas 8 horas y se redirige al usuario de forma silenciosa de nuevo a la aplicación web A con un nuevo token de identificador sin necesidad de autenticarse.
> 
> El usuario intenta acceder inmediatamente a la aplicación web B y se le redirige a Azure AD. Como antes, se aplica la directiva 2. Como el token se emitió hace más de 30 minutos, se le pide al usuario que vuelva a escribir sus credenciales y se emiten un token de sesión y un token de identificador completamente nuevos. El usuario pueden acceder entonces a la aplicación web B.
> 
> 

## <a name="configurable-policy-properties-in-depth"></a>Propiedades de directiva configurables: modo detallado
### <a name="access-token-lifetime"></a>Vigencia del token de acceso
**Cadena:** AccessTokenLifetime

**Afecta a:** tokens de acceso, tokens de identificador

**Resumen:** esta directiva controla cuánto tiempo se consideran válidos los token de acceso y de identificador para este recurso. Reducir la vigencia de los tokens de acceso disminuye el riesgo de que un individuo malintencionado use un token de acceso o de identificador durante un período de tiempo prolongado (ya que no se pueden revocar), pero también afecta negativamente al rendimiento, ya que los tokens tendrán que reemplazarse con más frecuencia.

### <a name="refresh-token-max-inactive-time"></a>Tiempo máximo de inactividad del token de actualización
**Cadena:** MaxInactiveTime

**Afecta a:** tokens de actualización

**Resumen:** esta directiva controla la antigüedad máxima que puede tener un token de actualización antes de que un cliente ya no pueda usarlo para recuperar un nuevo par de tokens de acceso/actualización al intentar acceder a este recurso. Dado que un nuevo token de actualización normalmente se devuelve cuando se usa un token de actualización, el cliente no tiene que haber alcanzado ningún recurso con el token de actualización actual durante el período de tiempo especificado antes de que esta directiva impida el acceso. 

Esta directiva obligará a los usuarios que no hayan estado activos en su cliente a volver a autenticarse para recuperar un nuevo token de actualización. 

Es importante tener en cuenta que el tiempo máximo de inactividad del token de actualización debe establecerse en un valor inferior a la antigüedad máxima del token de un solo factor y la antigüedad máxima del token de actualización de varios factores.

### <a name="single-factor-refresh-token-max-age"></a>Antigüedad máxima del token de actualización (un solo factor)
**Cadena:** MaxAgeSingleFactor

**Afecta a:** tokens de actualización

**Resumen:** esta directiva controla cuánto tiempo un usuario puede seguir usando tokens de actualización para obtener nuevos pares de tokens de acceso/actualización desde la última vez que se autenticara correctamente con un solo factor. Una vez que un usuario se autentique y reciba un nuevo token de actualización, podrá usar el flujo del token de actualización (siempre que el token de actualización actual no esté revocado y no se quede sin usar más tiempo que el período de inactividad) durante el período de tiempo especificado. En ese momento, los usuarios se verán obligados a volver a autenticarse para recibir un nuevo token de actualización. 

Reducir la antigüedad máxima obliga a los usuarios a autenticarse con más frecuencia. Puesto que la autenticación de un solo factor se considera menos segura que la autenticación multifactor, se recomienda establecer esta directiva en un valor igual o inferior al de la directiva de antigüedad máxima del token de actualización de varios factores.

### <a name="multi-factor-refresh-token-max-age"></a>Antigüedad máxima del token de actualización (varios factores)
**Cadena:** MaxAgeMultiFactor

**Afecta a:** tokens de actualización

**Resumen:** esta directiva controla cuánto tiempo un usuario puede seguir usando tokens de actualización para obtener nuevos pares de tokens de acceso/actualización desde la última vez que se autenticara correctamente con varios factores. Una vez que un usuario se autentique y reciba un nuevo token de actualización, podrá usar el flujo del token de actualización (siempre que el token de actualización actual no esté revocado y no se quede sin usar más tiempo que el período de inactividad) durante el período de tiempo especificado. En ese momento, los usuarios se verán obligados a volver a autenticarse para recibir un nuevo token de actualización. 

Reducir la antigüedad máxima obliga a los usuarios a autenticarse con más frecuencia. Puesto que la autenticación de un solo factor se considera menos segura que la autenticación multifactor, se recomienda establecer esta directiva en un valor igual o superior al de la directiva de antigüedad máxima del token de actualización de un solo factor.

### <a name="single-factor-session-token-max-age"></a>Antigüedad máxima del token de sesión (un solo factor)
**Cadena:** MaxAgeSessionSingleFactor

**Afecta a:** tokens de sesión (persistentes y no persistente)

**Resumen:** esta directiva controla cuánto tiempo un usuario puede seguir usando tokens de sesión para obtener nuevos tokens de identificador y de sesión desde la última vez que se autenticara correctamente con un solo factor. Una vez que un usuario se autentica y recibe un nuevo token de sesión, podrá usar el flujo del token de sesión (siempre que el token de sesión actual no esté revocado o haya caducado) durante el período de tiempo especificado. En ese momento, los usuarios se verán obligados a volver a autenticarse para recibir un nuevo token de sesión. 

Reducir la antigüedad máxima obliga a los usuarios a autenticarse con más frecuencia. Puesto que la autenticación de un solo factor se considera menos segura que una autenticación multifactor, se recomienda establecer esta directiva en un valor igual o inferior al de la directiva de antigüedad máxima del token de sesión de varios factores.

### <a name="multi-factor-session-token-max-age"></a>Antigüedad máxima del token de sesión (varios factores)
**Cadena:** MaxAgeSessionMultiFactor

**Afecta a:** tokens de sesión (persistentes y no persistente)

**Resumen:** esta directiva controla cuánto tiempo un usuario puede seguir usando tokens de sesión para obtener nuevos tokens de identificador y de sesión desde la última vez que se autenticara correctamente con varios factores. Una vez que un usuario se autentica y recibe un nuevo token de sesión, podrá usar el flujo del token de sesión (siempre que el token de sesión actual no esté revocado o haya caducado) durante el período de tiempo especificado. En ese momento, los usuarios se verán obligados a volver a autenticarse para recibir un nuevo token de sesión. 

Reducir la antigüedad máxima obliga a los usuarios a autenticarse con más frecuencia. Puesto que la autenticación de un solo factor se considera menos segura que la autenticación multifactor, se recomienda establecer esta directiva en un valor igual o superior al de la directiva de antigüedad máxima del token de sesión de un solo factor.

## <a name="sample-token-lifetime-policies"></a>Ejemplo de directivas de vigencia del token
La posibilidad de crear y administrar la vigencia de los tokens para aplicaciones, entidades de servicio y la organización en general expone toda clase de nuevos escenarios en Azure AD.  Vamos a recorrer algunos escenarios de directiva comunes que le ayudarán a imponer nuevas reglas para:

* Vigencia de los tokens
* Tiempos de inactividad máximos del token
* Antigüedad máxima del token

Examinaremos algunos escenarios, por ejemplo:

* Administración de la directiva predeterminada de una organización
* Creación de una directiva para inicio de sesión web
* Creación de una directiva para aplicaciones nativas mediante la llamada a una API web
* Administración de una directiva avanzada 

### <a name="prerequisites"></a>Requisitos previos
En los escenarios de ejemplo, crearemos, actualizaremos, vincularemos y eliminaremos directivas de aplicaciones, entidades de servicio y la organización en general.  Si no está familiarizado con Azure AD, consulte [este artículo](active-directory-howto-tenant.md) para ayudarle a empezar antes de continuar con estos ejemplos.  

1. Para empezar, descargue la [versión preliminar más reciente de los cmdlets de Azure PowerShell](https://www.powershellgallery.com/packages/AzureADPreview). 
2. Cuando tenga los cmdlets de Azure AD PowerShell, ejecute el comando Conectar para iniciar sesión en la cuenta de administrador de Azure AD. Debe hacer esto cada vez que inicie una nueva sesión.

    ```PowerShell
    Connect-AzureAD -Confirm
    ```

3. Ejecute el siguiente comando para ver todas las directivas que se han creado en la organización.  Este comando debe usarse después de la mayoría de las operaciones en los escenarios siguientes.  También le ayudará a obtener el valor de **ObjectId** de sus directivas. 
   
    ```PowerShell
    Get-AzureADPolicy
    ```

### <a name="sample-managing-a-organizations-default-policy"></a>Ejemplo: administración de la directiva predeterminada de una organización
En este ejemplo, crearemos una directiva que permita a sus usuarios iniciar sesión con menos frecuencia en toda su organización. 

Para ello, creamos una directiva de vigencia del token para tokens de actualización de un solo factor que se aplica en toda la organización. Esta directiva se aplicará a todas las aplicaciones de su organización y a todas las entidades de servicio que aún no tengan una directiva establecida en ella. 

#### <a name="1-create-a-token-lifetime-policy"></a>1. Cree una directiva de vigencia del token.

Establezca el token de actualización de un solo factor en "hasta que se revoque", lo que significa que no expirará hasta que se revoque el acceso.  La definición de la directiva que se indica a continuación es lo que crearemos:

```PowerShell
@('{
    "TokenLifetimePolicy":
    {
        "Version":1, 
        "MaxAgeSingleFactor":"until-revoked"
    }
}')
```

Ahora, ejecute el siguiente comando para crear esta directiva. 

```PowerShell
New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1, "MaxAgeSingleFactor":"until-revoked"}}') -DisplayName "OrganizationDefaultPolicyScenario" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"
```

Para ver la nueva directiva y obtener el valor de ObjectID, ejecute el siguiente comando.

```PowerShell
Get-AzureADPolicy
```

#### <a name="2-update-the-policy"></a>2. Actualice la directiva.

Ha decidido que la primera directiva no es tan estricta como requiere su servicio y que sus tokens de actualización de un solo factor expiren en 2 días. Ejecute el siguiente comando. 

```PowerShell
Set-AzureADPolicy -ObjectId <ObjectId FROM GET COMMAND> -DisplayName "OrganizationDefaultPolicyUpdatedScenario" -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxAgeSingleFactor":"2.00:00:00"}}')
```

#### <a name="3-youre-done"></a>3. ¡Y ya está! 

### <a name="sample-creating-a-policy-for-web-sign-in"></a>Ejemplo: Creación de una directiva para el inicio de sesión web

En este ejemplo, crearemos una directiva que requerirá que los usuarios se autentiquen con más frecuencia en la aplicación web. Esta directiva establecerá la vigencia de los tokens de acceso y de identificador y la antigüedad máxima de un token de sesión de varios factores en la entidad de servicio de su aplicación web.

#### <a name="1-create-a-token-lifetime-policy"></a>1. Cree una directiva de vigencia del token.

Para el inicio de sesión web esta directiva establecerá la vigencia del token de acceso y de identificador y la antigüedad máxima del token de sesión de un solo factor en 2 horas.

```PowerShell
New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"AccessTokenLifetime":"02:00:00","MaxAgeSessionSingleFactor":"02:00:00"}}') -DisplayName "WebPolicyScenario" -IsOrganizationDefault $false -Type "TokenLifetimePolicy"
```

Para ver la nueva directiva y obtener el valor de ObjectID, ejecute el siguiente comando.

```PowerShell
Get-AzureADPolicy
```

#### <a name="2-assign-the-policy-to-your-service-principal"></a>2. Asigne la directiva a su entidad de servicio.

Vamos a vincular esta nueva directiva con una entidad de servicio.  También necesitará una forma de acceder al valor de **ObjectId** de su entidad de servicio. Puede consultar [Microsoft Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity) o ir a nuestra [herramienta del Explorador de gráficos](https://graphexplorer.cloudapp.net/) e iniciar sesión en su cuenta de Azure AD para ver las entidades de servicio de todas sus organizaciones. 

Una vez que tenga el valor de **ObjectId**, ejecute el siguiente comando.

```PowerShell
Add-AzureADServicePrincipalPolicy -ObjectId <ObjectId of the ServicePrincipal> -RefObjectId <ObjectId of the Policy>
```

#### <a name="3-youre-done"></a>3. ¡Y ya está! 

### <a name="sample-creating-a-policy-for-native-apps-calling-a-web-api"></a>Ejemplo: Creación de una directiva para aplicaciones nativas mediante la llamada a una API web
En este ejemplo, crearemos una directiva que requiera que los usuarios se autentiquen menos y alargaremos la cantidad de tiempo que pueden estar inactivos sin tener que volver a autenticarse. La directiva se aplicará a la API web, así cuando la aplicación nativa la solicite como recurso, se aplicará esta directiva.

#### <a name="1-create-a-token-lifetime-policy"></a>1. Cree una directiva de vigencia del token. 

Este comando creará una directiva estricta para una API web. 

```PowerShell
New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxInactiveTime":"30.00:00:00","MaxAgeMultiFactor":"until-revoked","MaxAgeSingleFactor":"180.00:00:00"}}') -DisplayName "WebApiDefaultPolicyScenario" -IsOrganizationDefault $false -Type "TokenLifetimePolicy"
```

Para ver la nueva directiva y obtener el valor de ObjectID, ejecute el siguiente comando.

```PowerShell
Get-AzureADPolicy
```

#### <a name="2-assign-the-policy-to-your-web-api"></a>2. Asigne la directiva a la API web.

Vamos a vincular esta nueva directiva a una aplicación.  También necesitará una manera de acceder al valor de **ObjectId** de su aplicación. La mejor manera de encontrar el valor de **ObjectId** de la aplicación es usar [Azure Portal](https://portal.azure.com/). 

Una vez que tenga el valor de **ObjectId**, ejecute el siguiente comando.

```PowerShell
Add-AzureADApplicationPolicy -ObjectId <ObjectId of the Application> -RefObjectId <ObjectId of the Policy>
```

#### <a name="3-youre-done"></a>3. ¡Y ya está! 

### <a name="sample-managing-an-advanced-policy"></a>Ejemplo: Administración de una directiva avanzada
En este ejemplo, crearemos algunas directivas para demostrar cómo funciona el sistema de prioridad y cómo puede administrar varias directivas aplicadas a varios objetos. Esto le dará una idea de la prioridad de las directivas que se han explicado anteriormente y le ayudará a tratar con las situaciones más complicadas. 

#### <a name="1-create-a-token-lifetime-policy"></a>1. Cree una directiva de vigencia del token.

Hasta ahora ha sido bastante simple. Hemos creado una directiva predeterminada de organización que establece la vigencia del token de actualización de un solo factor en 30 días. 

```PowerShell
New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxAgeSingleFactor":"30.00:00:00"}}') -DisplayName "ComplexPolicyScenario" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"
```

Para ver su nueva directiva y obtener su ObjectID, ejecute el siguiente comando.

```PowerShell
Get-AzureADPolicy
```

#### <a name="2-assign-the-policy-to-a-service-principal"></a>2. Asigne la directiva a una entidad de servicio.

Ahora tenemos una directiva en toda la organización.  Supongamos que queremos conservar esta directiva 30 días para una entidad de servicio específica, pero cambiar la directiva predeterminada de organización para que sea el límite superior de "hasta que se revoque". 

En primer lugar, vamos a vincular esta nueva directiva a nuestra entidad de servicio.  También necesitará una forma de acceder al valor de **ObjectId** de su entidad de servicio. Puede consultar [Microsoft Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity) o ir a nuestra [herramienta del Explorador de gráficos](https://graphexplorer.cloudapp.net/) e iniciar sesión en su cuenta de Azure AD para ver las entidades de servicio de todas sus organizaciones. 

Una vez que tenga el valor de **ObjectId**, ejecute el siguiente comando.

```PowerShell
Add-AzureADServicePrincipalPolicy -ObjectId <ObjectId of the ServicePrincipal> -RefObjectId <ObjectId of the Policy>
```

#### <a name="3-set-the-isorganizationdefault-flag-to-false"></a>3. Establezca la marca de IsOrganizationDefault en false.

```PowerShell
Set-AzureADPolicy -ObjectId <ObjectId of Policy> -DisplayName "ComplexPolicyScenario" -IsOrganizationDefault $false
```

#### <a name="4-create-a-new-organization-default-policy"></a>4. Cree una nueva directiva predeterminada de organización.

```PowerShell
New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxAgeSingleFactor":"until-revoked"}}') -DisplayName "ComplexPolicyScenarioTwo" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"
```

#### <a name="5-youre-done"></a>5. ¡Y ya está! 

Ahora tiene la directiva original vinculada a la entidad de servicio y la nueva directiva establecida como su directiva predeterminada de organización.  Es importante recordar que las directivas aplicadas a las entidades de servicio tienen prioridad sobre las directivas predeterminadas de organización. 

## <a name="cmdlet-reference"></a>Referencia de cmdlets

### <a name="manage-policies"></a>Administración de directivas

Los cmdlets siguientes se pueden usar para administrar directivas.

#### <a name="new-azureadpolicy"></a>New-AzureADPolicy

Crea una nueva directiva.

```PowerShell
New-AzureADPolicy -Definition <Array of Rules> -DisplayName <Name of Policy> -IsOrganizationDefault <boolean> -Type <Policy Type> 
```

| parameters | Descripción | Ejemplo |
| --- | --- | --- |
| <code>&#8209;Definition</code> |La matriz de cadenas JSON que contiene todas las reglas de la directiva. | `-Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxInactiveTime":"20:00:00"}}')` |
| <code>&#8209;DisplayName</code> |Cadena del nombre de la directiva |`-DisplayName "MyTokenPolicy"` |
| <code>&#8209;IsOrganizationDefault</code> |Si es true establece la directiva como directiva predeterminada de la organización, si es false no hace nada. |`-IsOrganizationDefault $true` |
| <code>&#8209;Type</code> |El tipo de directiva para la vigencia de los tokens siempre usa "TokenLifetimePolicy". | `-Type "TokenLifetimePolicy"` |
| <code>&#8209;AlternativeIdentifier</code> [Opcional] |Establece un id alternativo para la directiva. |`-AlternativeIdentifier "myAltId"` |

</br></br>

#### <a name="get-azureadpolicy"></a>Get-AzureADPolicy
Obtiene todas las directivas de AzureAD o la directiva especificada. 

```PowerShell
Get-AzureADPolicy 
```

| parameters | Descripción | Ejemplo |
| --- | --- | --- |
| <code>&#8209;ObjectId</code> [Opcional] |El valor de ObjectId de la directiva que le gustaría obtener. |`-ObjectId <ObjectId of Policy>` |

</br></br>

#### <a name="get-azureadpolicyappliedobject"></a>Get-AzureADPolicyAppliedObject
Obtiene todas las aplicaciones y entidades de servicio vinculadas a una directiva.

```PowerShell
Get-AzureADPolicyAppliedObject -ObjectId <ObjectId of Policy> 
```

| parameters | Descripción | Ejemplo |
| --- | --- | --- |
| <code>&#8209;ObjectId</code> |El valor de ObjectId de la directiva que le gustaría obtener. |`-ObjectId <ObjectId of Policy>` |

</br></br>

#### <a name="set-azureadpolicy"></a>Set-AzureADPolicy
Actualiza una directiva existente.

```PowerShell
Set-AzureADPolicy -ObjectId <ObjectId of Policy> -DisplayName <string> 
```

| parameters | Descripción | Ejemplo |
| --- | --- | --- |
| <code>&#8209;ObjectId</code> |El valor de ObjectId de la directiva que le gustaría obtener. |`-ObjectId <ObjectId of Policy>` |
| <code>&#8209;DisplayName</code> |Cadena del nombre de la directiva |`-DisplayName "MyTokenPolicy"` |
| <code>&#8209;Definition</code> [Opcional] |La matriz de cadenas JSON que contiene todas las reglas de la directiva. |`-Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxInactiveTime":"20:00:00"}}')` |
| <code>&#8209;IsOrganizationDefault</code> [Opcional] |Si es true establece la directiva como directiva predeterminada de la organización, si es false no hace nada. |`-IsOrganizationDefault $true` |
| <code>&#8209;Type</code> [Opcional] |El tipo de directiva para la vigencia de los tokens siempre usa "TokenLifetimePolicy". |`-Type "TokenLifetimePolicy"` |
| <code>&#8209;AlternativeIdentifier</code> [Opcional] |Establece un id alternativo para la directiva. |`-AlternativeIdentifier "myAltId"` |

</br></br>

#### <a name="remove-azureadpolicy"></a>Remove-AzureADPolicy
Elimina la directiva especificada.

```PowerShell
 Remove-AzureADPolicy -ObjectId <ObjectId of Policy>
```

| parameters | Descripción | Ejemplo |
| --- | --- | --- |
| <code>&#8209;ObjectId</code> |El valor de ObjectId de la directiva que le gustaría obtener. | `-ObjectId <ObjectId of Policy>` |

</br></br>

### <a name="application-policies"></a>Directivas de aplicación
Los cmdlets siguientes se pueden usar para directivas de aplicación.</br></br>

#### <a name="add-azureadapplicationpolicy"></a>Add-AzureADApplicationPolicy
Vincula la directiva especificada a una aplicación.

```PowerShell
Add-AzureADApplicationPolicy -ObjectId <ObjectId of Application> -RefObjectId <ObjectId of Policy>
```

| parameters | Descripción | Ejemplo |
| --- | --- | --- |
| <code>&#8209;ObjectId</code> |El valor de ObjectId de la aplicación. | `-ObjectId <ObjectId of Application>` |
| <code>&#8209;RefObjectId</code> |El valor de ObjectId de la directiva. | `-RefObjectId <ObjectId of Policy>` |

</br></br>

#### <a name="get-azureadapplicationpolicy"></a>Get-AzureADApplicationPolicy
Obtiene la directiva asignada a una aplicación.

```PowerShell
Get-AzureADApplicationPolicy -ObjectId <ObjectId of Application>
```

| parameters | Descripción | Ejemplo |
| --- | --- | --- |
| <code>&#8209;ObjectId</code> |El valor de ObjectId de la aplicación. | `-ObjectId <ObjectId of Application>` |

</br></br>

#### <a name="remove-azureadapplicationpolicy"></a>Remove-AzureADApplicationPolicy
Quita una directiva de una aplicación.

```PowerShell
Remove-AzureADApplicationPolicy -ObjectId <ObjectId of Application> -PolicyId <ObjectId of Policy>
```

| parameters | Descripción | Ejemplo |
| --- | --- | --- |
| <code>&#8209;ObjectId</code> |El valor de ObjectId de la aplicación. | `-ObjectId <ObjectId of Application>` |
| <code>&#8209;PolicyId</code> |El valor de ObjectId de la directiva. | `-PolicyId <ObjectId of Policy>` |

</br></br>

### <a name="service-principal-policies"></a>Directivas de la entidad de servicio
Los cmdlets siguientes se pueden usar para las directivas de entidad de servicio.</br></br>

#### <a name="add-azureadserviceprincipalpolicy"></a>Add-AzureADServicePrincipalPolicy
Vincula la directiva especificada a una entidad de servicio.

```PowerShell
Add-AzureADServicePrincipalPolicy -ObjectId <ObjectId of ServicePrincipal> -RefObjectId <ObjectId of Policy>
```

| parameters | Descripción | Ejemplo |
| --- | --- | --- |
| <code>&#8209;ObjectId</code> |El valor de ObjectId de la aplicación. | `-ObjectId <ObjectId of Application>` |
| <code>&#8209;RefObjectId</code> |El valor de ObjectId de la directiva. | `-RefObjectId <ObjectId of Policy>` |

</br></br>

#### <a name="get-azureadserviceprincipalpolicy"></a>Get-AzureADServicePrincipalPolicy
Obtiene cualquier directiva vinculada a la entidad de servicio especificada.

```PowerShell
Get-AzureADServicePrincipalPolicy -ObjectId <ObjectId of ServicePrincipal>
```

| parameters | Descripción | Ejemplo |
| --- | --- | --- |
| <code>&#8209;ObjectId</code> |El valor de ObjectId de la aplicación. | `-ObjectId <ObjectId of Application>` |

</br></br>

#### <a name="remove-azureadserviceprincipalpolicy"></a>Remove-AzureADServicePrincipalPolicy
Quita la directiva de la entidad de servicio especificada.

```PowerShell
Remove-AzureADServicePrincipalPolicy -ObjectId <ObjectId of ServicePrincipal>  -PolicyId <ObjectId of Policy>
```

| parameters | Descripción | Ejemplo |
| --- | --- | --- |
| <code>&#8209;ObjectId</code> |El valor de ObjectId de la aplicación. | `-ObjectId <ObjectId of Application>` |
| <code>&#8209;PolicyId</code> |El valor de ObjectId de la directiva. | `-PolicyId <ObjectId of Policy>` |


