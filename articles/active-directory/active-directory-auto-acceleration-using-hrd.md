---
title: "Configurar la aceleración automática de inicio de sesión de una aplicación mediante la directiva de detección del dominio de inicio | Microsoft Docs"
description: "Explica qué es un inquilino de Azure AD y cómo administrar Azure mediante Azure Active Directory"
services: active-directory
documentationcenter: 
author: billmath
manager: femila
ms.service: active-directory
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: it-pro
ms.date: 11/09/2017
ms.author: billmath
ms.openlocfilehash: b1f0e5da09ef1d6acd234b72878cc71d66bb551e
ms.sourcegitcommit: 659cc0ace5d3b996e7e8608cfa4991dcac3ea129
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/13/2017
---
# <a name="configure-sign-in-auto-acceleration-for-an-application-using-home-realm-discovery-hrd-policy"></a>Configurar la aceleración automática de inicio de sesión de una aplicación mediante la directiva de detección del dominio de inicio (HRD)

## <a name="introduction-to-home-realm-discovery-and-auto-acceleration"></a>Introducción a la detección del dominio de inicio y la aceleración automática
En el siguiente documento se proporciona una introducción a la detección del dominio de inicio y la aceleración automática.

### <a name="home-realm-discovery"></a>Detección del dominio de inicio
La detección del dominio de inicio es el proceso que permite a Azure Active Directory determinar dónde debe autenticarse un usuario en el momento del inicio de sesión.  Al iniciar sesión en un inquilino de Azure AD para obtener acceso a un recurso o a la página de inicio de sesión común de Azure AD, el usuario debe escribir un nombre de usuario (UPN).  Azure AD usa esa información para detectar dónde debe iniciar sesión el usuario.   Es posible que el usuario sea dirigido a uno de estos lugares para poder realizar la autenticación:

- El inquilino principal del usuario (puede ser el mismo inquilino que el recurso al que el usuario está intentando obtener acceso). 
- Una cuenta Microsoft.   El usuario es un invitado en el inquilino de recursos.
- Otro proveedor de identidades federado con el inquilino de Azure AD.  Un proveedor de identidades local como, por ejemplo, AD FS.

### <a name="auto-acceleration"></a>Aceleración automática 
Algunas organizaciones configuran su inquilino de Azure Active Directory para federarlo con otro IdP (como AD FS) y así poder realizar la autenticación de usuario.  En estos casos, al iniciar sesión en una aplicación, lo primero que ve el usuario es una página de inicio de sesión de Azure AD y, una vez que haya escrito en ella su UPN, accederá a la página de inicio de sesión de IdP.  En casos donde sea necesario, es posible que los administradores quieran que los usuarios accedan directamente a la página de inicio de sesión al iniciar sesión en alguna aplicación específica; para ello, se omitirá la página inicial de Azure Active Directory. Esto se conoce como "aceleración automática de inicio de sesión".

Al habilitar la aceleración automática en aquellos casos donde el inquilino está federado a otro IdP para iniciar la sesión, se consigue que el usuario inicie sesión de forma más simple cuando se sabe que todos aquellos que inician sesión se pueden autenticar mediante ese IdP.  Puede configurar la aceleración automática en aplicaciones individuales.

>[!NOTE]
>Si configura la aceleración automática en una aplicación, los usuarios invitados no podrán iniciar sesión. Recuerde que llevar al usuario directamente a un IdP federado para que se autentique es un camino unidireccional, por lo que no se podrá volver a la página de inicio de sesión de Azure Active Directory.  Los usuarios invitados que deban autenticarse serán dirigidos a otros inquilinos o a un IdP externo, como una cuenta Microsoft, por lo que no podrán iniciar sesión en esa aplicación, ya que el paso de detección del dominio de inicio se omitirá.  

Hay dos formas de controlar la aceleración automática en un IdP federado.  Opciones:    

- Use una sugerencia de dominio en las solicitudes de autenticación de una aplicación. 
- Configure una directiva de HomeRealmDiscovery para habilitar la aceleración automática.

## <a name="domain-hints"></a>Sugerencias de dominio 
Las sugerencias de dominio son directivas que se incluyen en la solicitud de autenticación de una aplicación.  Se pueden usar para enviar al usuario a su página de inicio de sesión de IdP federado o puede usarlas una aplicación de varios inquilinos para enviar al usuario directamente a la página de inicio de sesión de Azure AD del inquilino.  Por ejemplo, una aplicación largeapp.com puede permitir que sus clientes obtengan acceso a la aplicación mediante la dirección URL personalizada contoso.largeapp.com y puede incluir una sugerencia de dominio de Contoso.com en la solicitud de autenticación. La sintaxis de la sugerencia de dominio varía en función del protocolo que se va a usar y se suele configurar en la aplicación.

**WS-Federation**: whr=contoso.com en la cadena de consulta.

**SAML**: una solicitud de autenticación de SAML que contiene una sugerencia de dominio o una cadena de consulta whr=contoso.com.

**Open ID Connect**: una cadena de consulta domain_hint=contoso.com. 

Si una sugerencia de dominio se incluye en la solicitud de autenticación de la aplicación y el inquilino está federado con ese dominio, Azure AD intentará redirigir el inicio de sesión al servicio IdP configurado para ese dominio.  Si la sugerencia de dominio no hace referencia a un dominio federado verificado, se omite y se invoca la detección del dominio de inicio normal.

Si quiere obtener más información acerca del uso de la aceleración automática con las sugerencias de dominio compatibles con Azure Active Directory, consulte [esta entrada de blog](https://cloudblogs.microsoft.com/enterprisemobility/2015/02/11/using-azure-ad-to-land-users-on-their-custom-login-page-from-within-your-app/).

>[!NOTE]
>Si una sugerencia de dominio se incluye en una solicitud de autenticación, su presencia invalida cualquier directiva de HRD que se establezca en la aplicación.

## <a name="home-realm-discovery-hrd-policy"></a>Directiva de detección del dominio de inicio (HRD)
Algunas aplicaciones no proporcionan una manera de configurar la solicitud de autenticación que emiten; en estos casos, no es posible utilizar sugerencias de dominio para controlar la aceleración automática.   Debido a ello, la aceleración automática se puede configurar mediante la directiva y así lograr el mismo comportamiento.  

### <a name="setting-hrd-policy"></a>Configurar la directiva de HRD
Debe seguir tres pasos para configurar la aceleración automática al iniciar sesión en una aplicación.


1. Crear una directiva de HRD para la aceleración automática.
2. Buscar la entidad de servicio que se va a asociar la directiva.
3. Asociar la directiva a la entidad de servicio.  Aunque las directivas se hayan creado en un inquilino, no tendrán ningún efecto hasta que no se adjunten a una entidad.  Se puede adjuntar una directiva de HRD a una entidad de servicio, pero solo una puede estar activa en una entidad determinada en cualquier momento.  

Puede usar la API Graph de Microsoft Azure Active Directory directamente o los cmdlets de PowerShell de Azure Active Directory para configurar la aceleración automática mediante la directiva de HRD.

[Aquí](https://msdn.microsoft.com/library/azure/ad/graph/api/policy-operations) se describe la API Graph que manipula la directiva.

Este es un ejemplo de la definición de la directiva de HRD:
    
 ```
   {  
    "HomeRealmDiscoveryPolicy":
    {  
    "AccelerateToFederatedDomain":true,
    "PreferredDomain":"federated.example.edu"
    }
   }
```

El tipo de directiva es "HomeRealmDiscoveryPolicy".

Si **AccelerateToFederatedDomain** es false, la directiva no tendrá ningún efecto, **PreferredDomain** deberá indicar un dominio al que realizar la aceleración y se podrá omitir en caso de que el inquilino tenga un solo dominio federado.  Si se omite y se verifica que hay más de un dominio federado, entonces la directiva no tendrá ningún efecto.

Si se especifica **PreferredDomain**, deberá coincidir con un dominio verificado y federado en el inquilino y todos los usuarios de la aplicación en cuestión deben poder iniciar sesión en ese dominio.

### <a name="priority-and-evaluation-of-hrd-policies"></a>Prioridad y evaluación de las directivas de HRD
Se pueden crear directivas de HRD y asignarlas a organizaciones y entidades de servicio específicas. Esto significa que se pueden aplicar varias directivas a una aplicación específica. La directiva de HRD que entra en vigor sigue estas reglas:


- Si una sugerencia de dominio se encuentra en la solicitud de autenticación, se pasa por alto cualquier directiva de HRD y se usa el comportamiento que especificó la sugerencia de dominio.
- Por otro lado, si una directiva se asigna explícitamente a la entidad de servicio, esta se aplicará. 
- Si no hay sugerencias de dominio y ninguna directiva se asigna explícitamente a la entidad de servicio, se aplicará una directiva asignada explícitamente a la organización principal de la entidad de servicio. 
- Si no hay ninguna sugerencia de dominio y no se asignó ninguna directiva a la entidad de servicio o a la organización, se usa el comportamiento de HRD predeterminado.

## <a name="tutorial-for-setting-sign-in-auto-acceleration-on-an-application-using-hrd-policy-with-azure-active-directory-powershell-cmdlets"></a>Tutorial para configurar la aceleración automática para iniciar sesión en una aplicación mediante la directiva de HRD con los cmdlets de PowerShell de Azure Active Directory
Examinaremos algunos escenarios, por ejemplo:


- Configurar la aceleración automática de una aplicación en un inquilino con un único dominio federado.
- Configurar la aceleración automática de una aplicación en uno de varios dominios que se verifican en el inquilino.
- Enumerar las aplicaciones para las que se configura una directiva.

### <a name="prerequisites"></a>Requisitos previos
En los ejemplos siguientes podrá crear, actualizar, vincular y eliminar directivas en las entidades de servicio de la aplicación en Azure AD.

1.  Para comenzar, descargue la versión preliminar más reciente de los cmdlets de PowerShell de Azure AD. 
2.  Cuando tenga los cmdlets de PowerShell de Azure AD, ejecute el comando Connect para iniciar sesión en la cuenta de administrador de Azure AD.

    ``` powershell
    Connect-AzureAD -Confirm
    ```
3.  Ejecute el siguiente comando para ver todas las directivas de la organización.

    ``` powershell
    Get-AzureADPolicy
    ```

Si no se devuelve nada, es que no tiene directivas creadas en el inquilino.

### <a name="example-setting-auto-acceleration-for-an-application"></a>Ejemplo: configurar la aceleración automática de una aplicación 
En este ejemplo, se crea una directiva que acelera automáticamente a los usuarios hacia una pantalla de inicio de sesión de AD FS cuando estos inician sesión en una aplicación.  Esta operación se realiza sin que el usuario tenga que especificar un nombre de usuario en la página de inicio de sesión de Azure AD. 

#### <a name="step-1-create-an-hrd-policy"></a>Paso 1: cree una directiva de HRD.
``` powershell
New-AzureADPoly -Definition @("{`"HomeRealmDiscoveryPolicy`":{`"AccelerateToFederatedDomain`":true}}") -DisplayName BasicAutoAccelerationPolicy -Type HomeRealmDiscoveryPolicy
```

Si tiene un dominio federado único que autentica a los usuarios de las aplicaciones, basta con crear una directiva de HRD.  
Para ver la nueva directiva y obtener su ObjectID, ejecute el siguiente comando.

``` powershell
Get-AzureADPolicy
```


Una vez que tenga una directiva de HRD, puede asignarla a varias entidades de servicio de aplicaciones para habilitar la aceleración automática.

#### <a name="step-2-locate-the-service-principal-to-assign-the-policy-to"></a>Paso 2: busque la entidad de servicio a la cual se asignará la directiva.  
Para ello, necesita el valor de ObjectId de las entidades de servicio a las que quiere asignar la directiva. Hay varias maneras de buscar el id. de objeto de las entidades de servicio.    

Puede usar el portal, puede realizar una consulta en [Microsoft Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity) o puede usar la [herramienta del explorador de Graph](https://graphexplorer.cloudapp.net/) e iniciar sesión en la cuenta de Azure AD para ver todas las entidades de servicio de la organización; asimismo, ya que está usando PowerShell, puede usar el cmdlet get-AzureADServicePrincipal para enumerar las entidades de servicio y sus identificadores.

#### <a name="step-3-assign-the-policy-to-your-service-principal"></a>Paso 3: asigne la directiva a la entidad de servicio.  
Una vez que tenga el valor de ObjectId de la entidad de servicio de la aplicación en la que quiera configurar la aceleración automática, ejecute el siguiente comando para asociar la directiva de HRD que creó en el paso 1 con la entidad de servicio que buscó en el paso 2.

``` powershell
Add-AzureADServicePrincipalPolicy -Id <ObjectID of the Service Principal> -RefObjectId <ObjectId of the Policy>
```

Puede repetir este comando en cada entidad de servicio a la que quiera agregar la directiva.

#### <a name="step-4-check-which-application-service-principals-your-auto-acceleration-policy-is-assigned-to"></a>Paso 4: comprobar qué entidades de servicio de aplicaciones tienen asignada la directiva de aceleración automática.
Para comprobar qué aplicaciones tienen configurada la directiva de aceleración automática, use el cmdlet Get-AzureADPolicyAppliedObject e indique el valor de objectId de la directiva que quiera comprobar.

``` powershell
Get-AzureADPolicyAppliedObject -ObjectId <ObjectId of the Policy>
```
#### <a name="step-5-youre-done"></a>Paso 5: ¡todo listo!
Pruebe la aplicación para comprobar que la nueva directiva funciona correctamente.

### <a name="example-listing-the-applications-for-which-an-auto-acceleration-policy-is-configured"></a>Ejemplo: enumerar las aplicaciones para las cuales se configuró la directiva de aceleración automática

#### <a name="step-1-list-all-policies-created-in-your-organization"></a>Paso 1: enumere todas las directivas creadas en la organización. 

``` powershell
Get-AzureADPolicy
```

Apunte el **id. de objeto** de la directiva de la cual quiere enumerar las asignaciones.

#### <a name="step-2-list-the-service-principals-the-policy-is-assigned-to"></a>Paso 2: enumere las entidades de servicio a las que se asignó la directiva.  

``` powershell
Get-AzureADPolicyAppliedObject -ObjectId <ObjectId of the Policy>
```

### <a name="example-removing-an-auto-acceleration-policy-for-an-application"></a>Ejemplo: quitar una directiva de aceleración automática de una aplicación
#### <a name="step-1-use-the-previous-example-to-get-the-objectid-of-the-policy-and-that-of-the-application-service-principal-you-wish-to-remove-it-from"></a>Paso 1: use el ejemplo anterior para obtener el valor de ObjectId de la directiva y el valor correspondiente a la entidad de servicio de la aplicación a la que quiera quitar la aceleración automática.
#### <a name="step-2-remove-the-policy-assignment-from-the-application-service-principal"></a>Paso 2: quite la asignación de directiva de la entidad de servicio de la aplicación.  

``` powershell
Remove-AzureADApplicationPolicy -ObjectId <ObjectId of the Service Principal>  -PolicyId <ObjectId of the policy>
```

#### <a name="step-3-check-removal-by-listing-the-service-principals-the-policy-is-assigned-to"></a>Paso 3: compruebe que se eliminó correctamente; para ello, enumere las entidades de servicio a las que se asignó la directiva. 

``` powershell
Get-AzureADPolicyAppliedObject -ObjectId <ObjectId of the Policy>
```
## <a name="next-steps"></a>Pasos siguientes
- Para más información sobre el funcionamiento de la autenticación en Azure AD, consulte [Escenarios de autenticación para Azure AD](develop/active-directory-authentication-scenarios.md).
- Para obtener más información sobre el inicio de sesión único del usuario, consulte [Application access and single sign-on with Azure Active Directory (Obtener acceso a la aplicación e inicio de sesión único con Azure Active Directory)](active-directory-enterprise-apps-manage-sso.md).
- Consulte la [Guía del desarrollador de Active Directory](develop/active-directory-developers-guide.md) para información general sobre todo el contenido de interés para los desarrolladores.
