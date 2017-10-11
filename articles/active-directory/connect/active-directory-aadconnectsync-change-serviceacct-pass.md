---
title: "Sincronización de Azure AD Connect: cambio de la cuenta del servicio de sincronización de Azure AD Connect | Microsoft Docs"
description: "En este documento del tema se describe la clave de cifrado y cómo abandonarla una vez cambiada la contraseña."
services: active-directory
keywords: "Cuenta del servicio Sincronización de Azure AD, contraseña"
documentationcenter: 
author: cychua
manager: femila
editor: 
ms.assetid: 76b19162-8b16-4960-9e22-bd64e6675ecc
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: billmath
ms.openlocfilehash: bf6234d0810f870909957ee1c1e33c225a4922b9
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/03/2017
---
# <a name="changing-the-azure-ad-connect-sync-service-account-password"></a>Cambio de la contraseña de la cuenta del servicio de sincronización de Azure AD Connect
Si cambia la contraseña de la cuenta del servicio de sincronización de Azure AD Connect, el servicio de sincronización no podrá iniciarse correctamente hasta que haya abandonado la clave de cifrado y reinicializado la contraseña de la cuenta del servicio de sincronización de Azure AD Connect. 

Azure AD Connect, como parte de los servicios de sincronización usa una clave de cifrado para almacenar las contraseñas de las cuentas de los servicios AD DS y Azure AD.  Estas cuentas se cifran antes de almacenarse en la base de datos. 

La clave de cifrado usada se protege mediante la [API de protección de datos de Windows (DPAPI)](https://msdn.microsoft.com/library/ms995355.aspx). Para proteger la clave de cifrado, DPAPI usa la **contraseña de la cuenta del servicio de sincronización de Azure AD Connect**. 

Si tiene que cambiar la contraseña de la cuenta del servicio, puede seguir para ello los procedimientos descritos en [Abandonar la clave de cifrado de sincronización de Azure AD Connect](#abandoning-the-azure-ad-connect-sync-encryption-key).  También debe seguir estos procedimientos si por algún motivo tiene que abandonar la clave de cifrado.

##<a name="issues-that-arise-from-changing-the-password"></a>Problemas que surgen al cambiar la contraseña
Es preciso hacer dos cosas al cambiar la contraseña de la cuenta del servicio.

En primer lugar, tiene que cambiar la contraseña en el Administrador de control de servicios de Windows.  Hasta que se solucione el problema, verá los siguientes errores:


- Si intenta iniciar el servicio de sincronización en el Administrador de control de servicios de Windows, recibirá el error "**Windows no pudo iniciar el servicio Sincronización de Microsoft Azure AD en el equipo local**". **Error 1069: No se puede iniciar el servicio debido a un error en el inicio de sesión.**"
- En el Visor de eventos de Windows, el registro de eventos del sistema contiene un error con **Id. de evento 7038** y el mensaje "**The ADSync service was unable to log on as with the currently configured password due to the following error (El servicio ADSync no puede iniciar sesión con la contraseña configurada actualmente debido al siguiente error): El nombre de usuario o la contraseña es incorrecto.**"

En segundo lugar, en determinadas condiciones, si la contraseña se actualiza, el servicio de sincronización ya no podrá recuperar la clave de cifrado a través de DPAPI. Sin la clave de cifrado, el servicio de sincronización no puede descifrar la contraseña necesaria para sincronizar con o desde AD y Azure AD locales.
Verá errores como los siguientes:

- En el Administrador de control de servicios de Windows, si intenta iniciar el servicio de sincronización y este no puede recuperar la clave de cifrado, se produce el error “**Windows no pudo iniciar el servicio Sincronización de Microsoft Azure AD en el equipo local. Para más información, revise el registro de eventos del sistema. Si este no es un servicio de Microsoft, póngase en contacto con el proveedor del servicio y haga referencia al código de error específico del servicio **-21451857952****.”
- En el Visor de eventos de Windows, el registro de eventos de la aplicación contiene un error con **Id. de evento 6028** y el mensaje de error *“**The server encryption key cannot be accessed.* *(No se puede acceder a la clave de cifrado del servidor.)”*

Para asegurarse de que no recibe estos errores, siga los procedimientos descritos en [Abandonar la clave de cifrado de sincronización de Azure AD Connect](#abandoning-the-azure-ad-connect-sync-encryption-key) al cambiar la contraseña.
 
## <a name="abandoning-the-azure-ad-connect-sync-encryption-key"></a>Abandonar la clave de cifrado de sincronización de Azure AD Connect
>[!IMPORTANT]
>Los procedimientos siguientes solo se aplican a Azure AD Connect compilación 1.1.443.0 o anterior.

Use los procedimientos siguientes para abandonar la clave de cifrado.

### <a name="what-to-do-if-you-need-to-abandon-the-encryption-key"></a>Qué hacer si tiene que abandonar la clave de cifrado

Si tiene que abandonar la clave de cifrado, use para ello los procedimientos siguientes.

1. [Abandonar la clave de cifrado existente](#abandon-the-existing-encryption-key)

2. [Especificar la contraseña de la cuenta de AD DS](#provide-the-password-of-the-ad-ds-account)

3. [Reinicializar la contraseña de la cuenta de Sincronización de Azure AD](#reinitialize-the-password-of-the-azure-ad-sync-account)

4. [Iniciar el servicio de sincronización](#start-the-synchronization-service)

#### <a name="abandon-the-existing-encryption-key"></a>Abandonar la clave de cifrado existente
Abandone la clave de cifrado existente para poder crear otra clave de cifrado:

1. Inicie sesión como administrador en el servidor de Azure AD Connect.

2. Inicie una nueva sesión de PowerShell.

3. Acceda a la carpeta: `$env:Program Files\Microsoft Azure AD Sync\bin\`

4. Ejecute el comando: `./miiskmu.exe /a`

![Utilidad de clave de cifrado de sincronización de Azure AD Connect](media/active-directory-aadconnectsync-encryption-key/key5.png)

#### <a name="provide-the-password-of-the-ad-ds-account"></a>Especificar la contraseña de la cuenta de AD DS
Cuando las contraseñas existentes almacenadas en la base de datos ya no se pueden descifrar, tiene que proporcionar el servicio de sincronización con la contraseña de la cuenta de AD DS. El servicio de sincronización cifra las contraseñas con la nueva clave de cifrado:

1. Inicie el Synchronization Service Manager (INICIO → Servicio de sincronización).
</br>![Sync Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/startmenu.png)  
2. Vaya a la pestaña **Conectores**.
3. Seleccione el **AD Connector** (conector de AD) que corresponda a su AD local. Si tiene más de un conector de AD, repita los pasos siguientes para cada uno de ellos.
4. En **Acciones**, seleccione **Propiedades**.
5. En el cuadro de diálogo emergente, seleccione **Connect to Active Directory Forest** (Conectarse al bosque de Active Directory):
6. Escriba la contraseña de la cuenta de AD DS en el cuadro de texto **Contraseña**. Si no conoce la contraseña, debe establecerla en un valor conocido antes de realizar este paso.
7. Haga en **Aceptar** para guardar la nueva contraseña y cerrar el cuadro de diálogo emergente.
![Utilidad de clave de cifrado de sincronización de Azure AD Connect](media/active-directory-aadconnectsync-encryption-key/key6.png)

#### <a name="reinitialize-the-password-of-the-azure-ad-sync-account"></a>Reinicializar la contraseña de la cuenta de Sincronización de Azure AD
No se puede especificar directamente la contraseña de la cuenta del servicio Azure AD en el servicio de sincronización. En su lugar, tendrá que usar el cmdlet **Add-ADSyncAADServiceAccount** para reinicializar la cuenta del servicio Azure AD. El cmdlet restablece la contraseña de la cuenta y la pone a disposición de servicio de sincronización:

1. Inicie una nueva sesión de PowerShell en el servidor de Azure AD Connect.
2. Ejecute el cmdlet `Add-ADSyncAADServiceAccount`.
3. En el cuadro de diálogo emergente, especifique las credenciales de administrador global de Azure AD para el inquilino de Azure AD.
![Utilidad de clave de cifrado de sincronización de Azure AD Connect](media/active-directory-aadconnectsync-encryption-key/key7.png)
4. Si se realiza correctamente, verá el símbolo del sistema de PowerShell.

#### <a name="start-the-synchronization-service"></a>Iniciar el servicio de sincronización
Ahora que el servicio de sincronización tiene acceso a la clave de cifrado y a todas las contraseñas que necesita, puede reiniciar el servicio en el Administrador de control de servicios de Windows:


1. Vaya a Administrador de control de servicios de Windows (INICIO → Servicios).
2. Seleccione **Sincronización de Microsoft Azure AD** y haga clic en Reiniciar.

## <a name="next-steps"></a>Pasos siguientes
**Temas de introducción**

* [Sincronización de Azure AD Connect: comprender y personalizar la sincronización](active-directory-aadconnectsync-whatis.md)

* [Integración de las identidades locales con Azure Active Directory](active-directory-aadconnect.md)
