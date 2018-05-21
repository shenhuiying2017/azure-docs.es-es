---
title: Sincronización de cuentas de usuario invitado que usan el correo electrónico para iniciar sesión en Azure | Microsoft Docs
description: En este artículo se explica cómo sincronizar las cuentas de usuario invitado que usan un identificador alternativo para iniciar sesión en las aplicaciones.
services: active-directory
author: billmath
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 04/19/2018
ms.author: billmath
ms.openlocfilehash: 4e6cf3bb4a691380a5fe22f5afdf749b40f15ef3
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/17/2018
---
# <a name="synchronizing-guest-user-accounts-that-use-email-for-sign-in-preview"></a>Sincronización de cuentas de usuario invitado que usan el correo electrónico para el inicio de sesión (versión preliminar)

>[!NOTE]
> Esta característica actualmente está en su versión preliminar pública.

En el siguiente escenario se aborda la situación en la que puede tener usuarios externos en el entorno local de AD, como son los asociados, que usan un método de inicio de sesión alternativo.

En el ejemplo anterior, Nina Morin trabaja para Fabrikam y tiene la siguiente dirección de correo electrónico: nmorin@fabrikam.com. Nina es asociada de Contoso y necesita acceso a determinadas aplicaciones que tiene Contoso. Contoso ha creado una cuenta para Nina y le ha indicado que use su dirección de correo electrónico para iniciar sesión en las aplicaciones.

Con las aplicaciones locales, este escenario funcionaba muy bien. Pero ahora, Contoso va a mover estas aplicaciones a la nube y quiere que sus asociados tengan la misma experiencia.

En este escenario se aborda esta situación.


## <a name="pre-requisites-and-assumptions"></a>Requisitos previos y supuestos
Esta sección contiene una lista de requisitos previos y supuestos que debe tener en cuenta antes de intentar configurar este escenario.

### <a name="pre-requisites"></a>Requisitos previos
- Credenciales de administrador global para configurar Azure AD Connect, verificar los dominios y configurar la federación de dominios.
- Azure AD Connect, versión 1.1.524.0 o superior.
- Dominio verificado para establecer el UPN de nube de los usuarios externos (ejemplo: bmcontoso.com).
- Servicio de federación para autenticar a los usuarios externos. Si usa AD FS, debe ser 2012 R2 o posterior.
- MSOL PowerShell v1.1 está instalado en una máquina para verificar la configuración de federación. Para más información, consulte [Azure Active Directory (MSOnline)](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-1.0).


### <a name="assumptions"></a>Supuestos 
- Azure AD Connect ya se ha configurado e instalado correctamente. Para información sobre cómo instalar Azure AD Connect, consulte [Azure AD Connect y federación](active-directory-aadconnectfed-whatis.md).
En este documento se realizan los siguientes supuestos:
- Dispone de un servicio de federación configurado que autentica a los usuarios correctamente.
- Los usuarios externos pueden autenticarse con su dirección de correo electrónico externa.
- - Se ha configurado un identificador alternativo para el inicio de sesión. Los usuarios pueden autenticarse con su identificador alternativo. Para más información sobre la configuración de un identificador alternativo con AD FS, consulte [Configurar un identificador de inicio de sesión alternativo](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configuring-alternate-login-id).

## <a name="task-1--prepare-the-environment"></a>Tarea 1:  Preparación del entorno
La siguiente tarea proporciona información que sirve de preparación para comenzar a sincronizar sus cuentas externas de forma que puedan iniciar sesión mediante un identificador alternativo, como el atributo de correo.

Defina los elementos en la tabla siguiente antes de pasar a la siguiente tarea.

![Architecture](media/active-directory-aadconnect-guest-sync/guest2.png)

|Aspecto del entorno|¿Para qué se usa?|Implementación en su entorno|
|-----|-----|-----|
|Atributo de UPN de nube|El atributo que rellena el UPN de los objetos de usuario externo en la nube. El sufijo UPN de las cuentas externas debe ser el definido en los requisitos previos. Este es el dominio verificado.|*Ejemplo: UserPrincipalName (nmorin@bmcontoso.com)|
|Dirección de inicio de sesión|El atributo que escribirán los usuarios externos al iniciar sesión. Este atributo debe tener un formato de dirección de correo electrónico y, en la mayoría de los casos, coincide con la dirección de correo electrónico real del usuario externo.|*Ejemplo: mail (nmorin@fabrikam.com)|
|Filtro con ámbito de Azure AD Connect|El filtro que permite establecer el destino de las identidades externas para definir el ámbito de las reglas de sincronización definidas posteriormente en esta guía. Las formas más habituales de definir el ámbito son: una unidad organizativa predefinida en la organización, una determinada convención de nomenclatura, un dominio específico, etc.|*Ejemplo: La unidad organizativa contiene Externos|
|Inquilino de Azure AD|El nombre del inquilino de Azure AD tal y como aparece para Azure AD Connect.|Ejemplo: contoso.onmicrosoft.com.|

La captura de pantalla siguiente tiene tres cuadros resumidos.
- La unidad organizativa **Externos**, que se usa en el filtro con ámbito de Azure AD Connect y es la ubicación de los usuarios externos.
- El atributo **mail**, que usan los usuarios externos para el inicio de sesión.
- El atributo **userPrincipalName**, que es el dominio verificado con el que está federado el entorno local.

![Usuario](media/active-directory-aadconnect-guest-sync/guest1.png)

## <a name="task-2--configure-azure-ad-connect"></a>Tarea 2: Configuración de Azure AD Connect
Una vez que tenga definida la información anterior, el siguiente paso es configurar las reglas de sincronización de Azure AD Connect. Para configurar las reglas, use el editor de reglas de sincronización de Azure AD Connect. Para más información sobre el editor, consulte [Aprovisionamiento declarativo](active-directory-aadconnectsync-understanding-declarative-provisioning.md).

### <a name="how-to-configure-the-synchronization-rule"></a>Configuración de la regla de sincronización
Use el procedimiento siguiente para configurar Azure AD Connect.

1. Abra el editor de reglas de sincronización de Azure AD Connect; para ello, vaya a **Inicio - Azure AD Connect - Synchronization rules editor (Editor de reglas de sincronización)**.
2. En la pantalla **Synchronization Rules Editor** (Editor de reglas de sincronización), asegúrese de que la dirección sea **inbound** (entrante) y, a la derecha, haga clic en **Add new rule** (Agregar nueva regla).
3. En la página **Description** (Descripción), configure los siguientes valores y haga clic en **Next** (Siguiente).
    - **Name** (Nombre): escriba un nombre para la regla. 
    - **Connected System** (Sistema conectado): el entorno local de AD.
    - **Connected System Object Type**  (Tipo de objeto del sistema conectado): user (usuario).
    - **Metaverse Object Type** (Tipo de objeto de metaverso): person (persona)
    - **Link Type** (Tipo de vínculo): Join (Combinación)
    - **Precedence:** (Precedencia) 90
    - 
![](media/active-directory-aadconnect-guest-sync/guest3.png)

4. En la pantalla **Scoping Filter** (Filtro de ámbito), haga clic en **Agregar grupo**.
5. Use las listas desplegables para configurar el filtro. Escriba lo siguiente y haga clic en **Next** (Siguiente). Esta acción crea un filtro que solo se aplica a objetos que se encuentran en la unidad organizativa externa.
    - **Attribute** (Atributo): dn
    - **Operator** (Operador): CONTAINS (CONTIENE)
    - **Value** (Value): Externos
 
 ![Filtrar](media/active-directory-aadconnect-guest-sync/guest4.png)

6. En la pantalla **Join Rules** (Reglas de combinación), haga clic en **Next** (Siguiente).
7. En la pantalla **Transformations** (Transformaciones), haga clic en **Add Transformation** (Agregar transformación). Escriba la siguiente información:
    - **FlowType**: Constante
    - **Target Attribute** (Atributo de destino): userType
    - **Source** (Origen): Guest (Invitado)
8. En la pantalla **Transformations** (Transformaciones), haga clic en **Add Transformation** (Agregar transformación). Escriba la siguiente información:
    - **FlowType**: Direct (Directo)
    - **Target Attribute** (Atributo de destino): onPremisesUserPrincipalName
    - **Source** (Origen): mail
9. En la pantalla **Transformations** (Transformaciones), haga clic en **Add Transformation** (Agregar transformación). Escriba la siguiente información:
    - **FlowType**: Direct (Directo)
    - **Target Attribute** (Atributo de destino): userPrincipalName
    - **Source** (Origen): userPrincipalName ![Transformación](media/active-directory-aadconnect-guest-sync/guest5.png)
10. Haga clic en **Agregar**. 
11. En la pantalla **Synchronization Rules Editor** (Editor de reglas de sincronización), asegúrese de que la dirección sea **outbound** (saliente) y, en la derecha, haga clic en **Add new rule** (Agregar nueva regla).
12. En la página **Description** (Descripción), configure los siguientes valores y haga clic en **Next** (Siguiente).
    - **Name** (Nombre): escriba un nombre para la regla. 
    - **Connected System** (Sistema conectado) :el inquilino de Azure AD
    - **Connected System Object Type**  (Tipo de objeto del sistema conectado): user (usuario).
    - **Metaverse Object Type** (Tipo de objeto de metaverso): person (persona)
    - **Link Type** (Tipo de vínculo): Join (Combinación)
    - **Precedence:** (Precedencia) 90
    - 
![Regla](media/active-directory-aadconnect-guest-sync/guest6.png)

13. En la pantalla **Scoping Filter** (Filtro de ámbito), haga clic en **Next** (Siguiente).
14. En la pantalla **Join Rules** (Reglas de combinación), haga clic en **Next** (Siguiente).
15. En la pantalla **Transformations** (Transformaciones), haga clic en **Add Transformation** (Agregar transformación).  Escriba la siguiente información:
    - **FlowType**: Direct (Directo)
    - **Target Attribute** (Atributo de destino): userType
    - **Source** (Origen): userType
9. En la pantalla **Transformations** (Transformaciones), haga clic en **Add Transformation** (Agregar transformación). Escriba la siguiente información:
    - **FlowType**: Direct (Directo)
    - **Target Attribute** (Atributo de destino): onPremisesUserPrincipalName
    - **Source** (Origen): onPremisesUserPrincipalName ![Transformación](media/active-directory-aadconnect-guest-sync/guest7.png)
10. Haga clic en **Agregar**. 
11. Una vez que haya configurado estas reglas, deberá ejecutar una sincronización completa. Use PowerShell para iniciar una sincronización completa. Una vez finalizada la sincronización, puede continuar con el paso siguiente.

``` powershell
    Start-ADSyncSyncCycle -PolicyType Initial
```

## <a name="task-3--federation"></a>Tarea 3: Federación
La siguiente tarea le proporciona información sobre algunas cosas que es necesario tener para que el escenario funcione.

Puede comprobar la configuración de federación con Azure mediante Azure AD PowerShell. En este documento se usa la versión 1.1 de MSOL PowerShell. Puede instalar esta versión [aquí](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-1.0).

### <a name="verify-the-federation-settings"></a>Verificación de la configuración de federación
Use el procedimiento siguiente para verificar la configuración de federación.
1. Abra Windows PowerShell y conéctese a Azure AD con el comando siguiente:
``` powershell
      Connect-MSOLservice
```
2.  Escriba las credenciales de administrador global.
3.  Ahora, escriba el comando siguiente:
  ``` powershell
      Get-MSOLDomainFederationSettings
  ```
4.   Observe que se debe devolver la información de federación. Tenga en cuenta que **ActiveLogonUri** es la dirección URL del servidor de federación.

  ![Federación](media/active-directory-aadconnect-guest-sync/guest8.png)

### <a name="verify-alternate-login-id"></a>Verificación del identificador de inicio de sesión alternativo
En este documento se usa AD FS como proveedor de identidades (Idp). Estos pasos pueden variar si se usa un Idp diferente.

1. Abra Windows PowerShell y escriba el siguiente comando:
   ```powershell
    Get-ADFSClaimsProviderTrust
   ```
2. Verá la información de AD FS.  Anote los valores de **AlternateLoginID** y **LookupForests**.

![ADFS](media/active-directory-aadconnect-guest-sync/guest9.png)

## <a name="task-4--testing"></a>Tarea 4: Prueba
Para comprobar que funciona correctamente, debe iniciar sesión en un punto de conexión que se haya configurado para autenticarse con el Idp. Para comprobar esto, se implementa un sitio web en Azure y se usa la dirección URL siguiente: contososampapp.azurewebsites.net.

### <a name="verify-that-you-can-sign-in-with-the-alternate-id"></a>Verificación de que puede iniciar sesión con el identificador alternativo
1. Inicie sesión en el punto de conexión.</br>
![Punto de conexión](media/active-directory-aadconnect-guest-sync/guest10.png)
1. Escriba el nombre de usuario; se le redirigirá a la página de inicio de sesión de federación.
![Federación](media/active-directory-aadconnect-guest-sync/guest11.png)
1. Escriba sus credenciales.
2. Ahora, habrá iniciado sesión correctamente.
![Success](media/active-directory-aadconnect-guest-sync/guest12.png)

## <a name="next-steps"></a>Pasos siguientes
- [Propiedades de un usuario de colaboración B2B de Azure Active Directory](../../active-directory/b2b/user-properties.md#key-properties-of-the-azure-ad-b2b-collaboration-user)
- [Configurar un identificador de inicio de sesión alternativo](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configuring-alternate-login-id)
- [Azure AD Connect: historial de versiones](active-directory-aadconnect-version-history.md)
