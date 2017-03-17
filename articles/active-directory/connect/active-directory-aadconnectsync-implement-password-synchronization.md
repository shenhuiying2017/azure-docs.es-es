---
title: "Implementación de la sincronización de contraseña con la sincronización de Azure AD Connect | Microsoft Docs"
description: "Proporciona información sobre cómo funciona la sincronización de contraseña y cómo habilitarla."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 05f16c3e-9d23-45dc-afca-3d0fa9dbf501
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/13/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: 64b6447608ecdd9bdd2b307f4bff2cae43a4b13f
ms.openlocfilehash: cff066ff2943443749ee8eb2ef71c7ca93bb829c
ms.lasthandoff: 03/01/2017


---
# <a name="implementing-password-synchronization-with-azure-ad-connect-sync"></a>Implementación de la sincronización de contraseña mediante la sincronización de Azure AD Connect
Este tema le proporciona la información que necesita para sincronizar las contraseñas de usuario de una instancia de Active Directory (AD) local con otra de Azure Active Directory (Azure AD) basado en la nube.

## <a name="what-is-password-synchronization"></a>Qué es la sincronización de contraseñas
La probabilidad de que se quede bloqueado sin poder realizar su trabajo debido a una contraseña olvidada, está relacionada con el número de contraseñas diferentes que tenga que recordar. Cuantas más contraseñas tenga que recordar, más probabilidades tiene de olvidar alguna. Las preguntas y llamadas para el restablecimiento de contraseñas y otros problemas relacionados con las mismas, son los temas que más recursos del departamento de soporte técnico utilizan.

La sincronización de contraseñas es una característica para sincronizar contraseñas de usuario de una instancia de Active Directory local con otra de Azure Active Directory (Azure AD) basado en la nube.
Esta característica le permite iniciar sesión en los servicios de Azure Active Directory (como Office 365, Microsoft Intune y CRM Online y Servicios de dominio de Azure AD) con la misma contraseña que usa para iniciar sesión en la instancia local de Active Directory.

![Qué es Azure AD Connect](./media/active-directory-aadconnectsync-implement-password-synchronization/arch1.png)

Al reducir el número de contraseñas que los usuarios necesitan mantener a una sola, la sincronización de contraseña le ayuda a:

* Mejora de la productividad de los usuarios
* Reducción de los costos de soporte técnico  

Además, si elige usar [**Federación con AD FS**](https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Configuring-AD-FS-for-user-sign-in-with-Azure-AD-Connect), tiene la opción de habilitar la sincronización de contraseñas como una copia de seguridad en caso de error en la infraestructura de AD FS.

La sincronización de contraseñas es una extensión de la característica de sincronización de directorios implementada por la sincronización de Azure AD Connect. Para utilizar la sincronización de contraseña en su entorno, necesitará lo siguiente:

* Instalación de Azure AD Connect  
* Configure la sincronización de directorios entre su instancia de AD local y la de Azure Active Directory.
* Habilitación de la sincronización de contraseñas

Para más detalles, consulte [Integración de las identidades locales con Azure Active Directory](active-directory-aadconnect.md)

> [!NOTE]
> Para obtener más información acerca de los Servicios de dominio de Active Directory configurados para la sincronización de contraseñas y FIPS, consulte [Sincronización de contraseñas y FIPS](#password-synchronization-and-fips).
>
>

## <a name="how-password-synchronization-works"></a>Funcionamiento de la sincronización de contraseñas
El Servicio de dominio de Active Directory almacena contraseñas en forma de representación de valor hash de la contraseña de usuario real. Un valor hash es un resultado de una función matemática unidireccional (el "*algoritmo hash*"). No hay ningún método para volver del resultado de una función unidireccional a la versión de texto sin formato de una contraseña. El hash de contraseña no puede usarse para iniciar sesión en la red local.

Para sincronizar una contraseña, la sincronización de Azure AD Connect extrae el hash de contraseña de usuario de la instancia de Active Directory local. Se aplica un procesamiento de seguridad adicional al hash de contraseña antes de que se sincronice con el servicio de autenticación de Azure Active Directory. Las contraseñas se sincronizan usuario por usuario y en orden cronológico.

El flujo de datos reales del proceso de sincronización de contraseñas es similar al de sincronización de datos de usuario, como el nombre para mostrar o las direcciones de correo electrónico. De todas formas, las contraseñas se sincronizan con más frecuencia que la ventana de sincronización de directorios estándar para otros atributos. El proceso de sincronización de contraseñas se ejecuta cada 2 minutos. La frecuencia de este proceso no se puede modificar. Al sincronizar una contraseña, esta sobrescribe la contraseña existente en la nube.

La primera vez que se habilita la característica de sincronización de contraseñas, se realiza una sincronización inicial de las contraseñas de todos los usuarios dentro del ámbito. No puede definir explícitamente un subconjunto de contraseñas de usuario que desee sincronizar.

Al cambiar una contraseña local, la contraseña actualizada se sincroniza más a menudo en cuestión de minutos.
La característica de sincronización de contraseñas reintenta automáticamente los intentos fallidos de sincronización. Si se produce un error al intentar sincronizar una contraseña, se registra un error en el visor de eventos.

La sincronización de una contraseña no influye en el usuario con la sesión iniciada actualmente.
Si se sincroniza un cambio de contraseña mientras tiene iniciada sesión en un servicio en la nube, este cambio no afectará de inmediato a la sesión actual del servicio en la nube. Sin embargo, cuando el servicio en la nube requiera de nuevo su autenticación, deberá proporcionar la nueva contraseña.

> [!NOTE]
> Solo se admite la sincronización de la contraseña para el usuario del tipo de objeto de Active Directory. No se admite para el tipo de objeto iNetOrgPerson.
>
>

### <a name="how-password-synchronization-works-with-azure-ad-domain-services"></a>Funcionamiento de la sincronización de contraseñas con Servicios de dominio de Azure AD
También puede utilizar la característica de sincronización de contraseñas para sincronizar las contraseñas locales con los [Servicios de dominio de AD de Azure](../../active-directory-domain-services/active-directory-ds-overview.md). Este escenario permite a los Servicios de dominio de Azure AD autenticar a los usuarios en la nube con todos los métodos disponibles en la instancia de AD local. Esta experiencia es similar al uso de la Herramienta de migración Active Directory (ADMT) en un entorno local.

### <a name="security-considerations"></a>Consideraciones sobre la seguridad
Al sincronizar contraseñas, la versión de texto sin formato de su contraseña no se expone a la característica de sincronización de contraseñas ni a Azure AD ni a ninguno de los servicios asociados.

Además, la instancia de Active Directory local no requiere que se almacene la contraseña en un formato cifrado reversible. Se usa un resumen del hash de contraseña de Active Directory para la transmisión entre Azure Active Directory y AD local. El resumen del hash de contraseña no se puede usar para obtener acceso a recursos en su entorno local.

### <a name="password-policy-considerations"></a>Consideraciones de la directiva de contraseña
Existen dos tipos de directivas de contraseña que se ven afectados por la habilitación de la sincronización de contraseñas:

1. Directiva de complejidad de contraseñas
2. Directiva de expiración de contraseñas

**Password complexity policy**  
Cuando se habilita la sincronización de contraseñas, las directivas de complejidad de contraseñas en su instancia local de Active Directory anulan las directivas de complejidad en la nube para los usuarios sincronizados. Puede utilizar todas las contraseñas válidas de su instancia de Active Directory local para tener acceso a servicios de Azure AD.

> [!NOTE]
> Las contraseñas para los usuarios que se crean directamente en la nube siguen estando sujetas a las directivas de contraseñas tal como se definen en la nube.
>
>

**Password expiration policy**  
Si un usuario está en el ámbito de sincronización de contraseñas, la contraseña de cuenta en la nube se establece en "*No caduca nunca*".
Puede seguir iniciando sesión en servicios en la nube con una contraseña sincronizada que haya caducado en su entorno local. La contraseña en la nube se actualizará la próxima vez que cambie la contraseña en el entorno local.

### <a name="overwriting-synchronized-passwords"></a>Sobrescritura de las contraseñas sincronizadas
Un administrador puede restablecer manualmente la contraseña utilizando Windows PowerShell.

En este caso, la nueva contraseña sobrescribe la contraseña sincronizada y todas las directivas de contraseñas definidas en la nube se aplican a la nueva contraseña.

Si cambia la contraseña local de nuevo, la nueva contraseña se sincroniza con la nube y reemplaza a la contraseña actualizada manualmente.

## <a name="enabling-password-synchronization"></a>Habilitación de la sincronización de contraseñas
La sincronización de contraseñas está habilitada de forma automática cuando se instala Azure AD Connect utilizando la **Configuración rápida**. Para conocer más detalles, consulte [Introducción a Azure AD Connect mediante la configuración rápida](active-directory-aadconnect-get-started-express.md).

Si utiliza una configuración personalizada al instalar Azure AD Connect, podrá habilitar la sincronización de contraseña en la página de inicio de sesión del usuario. Para más información, consulte [Instalación personalizada de Azure AD Connect](active-directory-aadconnect-get-started-custom.md).

![Habilitación de la sincronización de contraseñas](./media/active-directory-aadconnectsync-implement-password-synchronization/usersignin.png)

### <a name="password-synchronization-and-fips"></a>Sincronización de contraseñas y FIPS
Si el servidor se bloquea de acuerdo con FIPS (Estándar federal de procesamiento de información), el MD5 se deshabilita.

**Para habilitar el MD5 para la sincronización de contraseñas, realice los pasos siguientes:**

1. Vaya a **%programfiles%\Azure AD Sync\Bin**.
2. Abra **miiserver.exe.config**.
3. Vaya al nodo **configuration/runtime** (al final del archivo).
4. Agregue el siguiente nodo: `<enforceFIPSPolicy enabled="false"/>`
5. Guarde los cambios.

Como referencia, este fragmento de código debe ser similar al siguiente:

```
    <configuration>
        <runtime>
            <enforceFIPSPolicy enabled="false"/>
        </runtime>
    </configuration>
```

Para obtener información sobre la seguridad y FIPS, consulte [AAD Password Sync, Encryption and FIPS compliance](https://blogs.technet.microsoft.com/enterprisemobility/2014/06/28/aad-password-sync-encryption-and-fips-compliance/)

## <a name="troubleshooting-password-synchronization"></a>Solución de problemas de sincronización de contraseña
Si tiene problemas con la sincronización de contraseña, consulte [Solución de problemas de sincronización de contraseñas](active-directory-aadconnectsync-troubleshoot-password-synchronization.md).

## <a name="next-steps"></a>Pasos siguientes
* [Sincronización de Azure AD Connect: personalización de las opciones de sincronización](active-directory-aadconnectsync-whatis.md)
* [Integración de las identidades locales con Azure Active Directory](active-directory-aadconnect.md)

