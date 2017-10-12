---
title: "Implementación de la sincronización de contraseña mediante la sincronización de Azure AD Connect | Microsoft Docs"
description: "Ofrece información sobre cómo funciona la sincronización de contraseña y cómo configurarla."
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
ms.date: 07/17/2017
ms.author: billmath
ms.openlocfilehash: db9b1578a235be9018fc1985cc75a0a05ee47b3a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="implement-password-synchronization-with-azure-ad-connect-sync"></a>Implementación de la sincronización de contraseña mediante la sincronización de Azure AD Connect
En este artículo se ofrece información que se necesita para sincronizar las contraseñas de usuario desde una instancia de Active Directory local con otra de Azure Active Directory (Azure AD) basado en la nube.

## <a name="what-is-password-synchronization"></a>Qué es la sincronización de contraseñas
Las probabilidades de que se quede bloqueado sin poder realizar su trabajo debido a una contraseña olvidada guarda relación con el número de contraseñas diferentes que tenga que recordar. Cuantas más contraseñas tenga que recordar, más probabilidades tiene de olvidar alguna. Las preguntas y llamadas para el restablecimiento de contraseñas y otros problemas relacionados con las mismas, son los temas que más recursos del departamento de soporte técnico utilizan.

La sincronización de contraseña es una característica que sirve para sincronizar contraseñas de usuario de una instancia de Active Directory local con otra de Azure Active Directory (Azure AD) basado en la nube.
Utilice esta característica para iniciar sesión en servicios de Azure AD como Office 365, Microsoft Intune, CRM Online y Azure Active Directory Domain Services (Azure AD DS). Inicie sesión en el servicio con la misma contraseña que usa para iniciar sesión en la instancia local de Active Directory.

![Qué es Azure AD Connect](./media/active-directory-aadconnectsync-implement-password-synchronization/arch1.png)

Al reducir el número de contraseñas, los usuarios necesitan mantener una sola. La sincronización de contraseña le ayuda a:

* Mejorar la productividad de los usuarios.
* Reducir los costos de soporte técnico.  

Además, si opta por usar [Federación con Servicios de federación de Active Directory (AD FS)](https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Configuring-AD-FS-for-user-sign-in-with-Azure-AD-Connect), tiene la posibilidad de configurar la sincronización de contraseña como copia de seguridad en caso de error en la infraestructura de AD FS.

La sincronización de contraseñas es una extensión de la característica de sincronización de directorios implementada por la sincronización de Azure AD Connect. Para utilizar la sincronización de contraseña en su entorno, necesitará lo siguiente:

* Instalar Azure AD Connect.  
* Configure la sincronización de directorios entre su instancia de Active Directory local y la de Azure Active Directory.
* Habilitar la sincronización de contraseña.

Para más información, consulte [Integración de las identidades locales con Azure Active Directory](active-directory-aadconnect.md).

> [!NOTE]
> Para obtener más información sobre Azure Active Directory Domain Services configurado para la sincronización de contraseña y FIPS, vea "Sincronización de contraseñas y FIPS" más adelante en este artículo.
>
>

## <a name="how-password-synchronization-works"></a>Funcionamiento de la sincronización de contraseñas
El servicio de dominio de Active Directory almacena contraseñas en forma de representación de valor hash de la contraseña de usuario real. Un valor hash es el resultado de una función matemática unidireccional (el *algoritmo hash*). No hay ningún método para volver del resultado de una función unidireccional a la versión de texto sin formato de una contraseña. El hash de contraseña no puede usarse para iniciar sesión en la red local.

Para sincronizar la contraseña, la sincronización de Azure AD Connect extrae el hash de la contraseña de la instancia de Active Directory local. Se aplica un procesamiento de seguridad adicional al hash de contraseña antes de que se sincronice con el servicio de autenticación de Azure Active Directory. Las contraseñas se sincronizan usuario por usuario y en orden cronológico.

El flujo de datos reales del proceso de sincronización de contraseñas es similar al de sincronización de datos de usuario, como el nombre para mostrar o las direcciones de correo electrónico. De todas formas, las contraseñas se sincronizan con más frecuencia que la ventana de sincronización de directorios estándar para otros atributos. El proceso de sincronización de contraseñas se ejecuta cada 2 minutos. La frecuencia de este proceso no se puede modificar. Al sincronizar una contraseña, esta sobrescribe la contraseña existente en la nube.

La primera vez que se habilita la característica de sincronización de contraseña, se realiza una sincronización inicial de las contraseñas de todos los usuarios dentro del ámbito. No puede definir explícitamente un subconjunto de contraseñas de usuario que quiera sincronizar.

Al cambiar una contraseña local, la contraseña actualizada se sincroniza más a menudo en cuestión de minutos.
La característica de sincronización de contraseñas reintenta automáticamente los intentos fallidos de sincronización. Si se produce un error al intentar sincronizar una contraseña, se registra un error en el visor de eventos.

La sincronización de una contraseña no influye en el usuario que actualmente ha iniciado sesión.
Si se sincroniza un cambio de contraseña mientras tiene iniciada sesión en un servicio en la nube, este cambio no afectará de inmediato a la sesión actual del servicio en la nube. Sin embargo, cuando el servicio en la nube requiera de nuevo su autenticación, deberá proporcionar la nueva contraseña.

Un usuario debe escribir sus credenciales corporativas una segunda vez para autenticarse en Azure AD, independientemente de que haya iniciado sesión en su red corporativa. Pero estos patrones puede reducirse si el usuario activa la casilla "Mantener la sesión iniciada" (KMSI) en el inicio de sesión. Con esta activación se establece una cookie de sesión que omite la autenticación durante un breve período. El comportamiento de KMSI lo puede habilitar o deshabilitar el administrador de Azure AD.

> [!NOTE]
> Solo se admite la sincronización de la contraseña para el usuario del tipo de objeto de Active Directory. No se admite para el tipo de objeto iNetOrgPerson.

### <a name="detailed-description-of-how-password-synchronization-works"></a>Descripción detallada de cómo funciona la sincronización de contraseña
El gráfico siguiente describe con detalle cómo funciona la sincronización de contraseña entre Active Directory y Azure AD.

![Flujo detallado de contraseñas](./media/active-directory-aadconnectsync-implement-password-synchronization/arch3.png)


1. Cada dos minutos, el agente de sincronización de contraseña en el servidor de AD Connect solicita hashes de contraseña almacenados (el atributo unicodePwd) desde un controlador de dominio a través protocolo de replicación [MS-DRSR](https://msdn.microsoft.com/library/cc228086.aspx) estándar que se utiliza para sincronizar datos entre controladores de dominio. La cuenta de servicio debe tener los permisos para replicar cambios de directorio y para replicar cambios de directorio en todos los AD (se conceden de manera predeterminada en la instalación) para obtener los hashes de contraseña.
2. Antes del envío, el controlador de dominio cifra el hash de contraseña MD4 mediante una clave que es un hash de [MD5](http://www.rfc-editor.org/rfc/rfc1321.txt) de la clave de sesión RPC y valor sal. Después envía el resultado al agente de sincronización de contraseña a través de RPC. El controlador de dominio también pasa el valor sal al agente de sincronización mediante el protocolo de replicación del controlador de dominio, por lo que el agente podrá descifrar el sobre.
3.  Cuando el agente de sincronización de contraseña tiene el sobre cifrado, usa [MD5CryptoServiceProvider](https://msdn.microsoft.com/library/System.Security.Cryptography.MD5CryptoServiceProvider.aspx) y el valor sal para generar una clave para descifrar los datos recibidos en su formato original de MD4. En ningún momento el agente de sincronización de contraseña tiene acceso a la contraseña de texto no cifrado. El uso del agente de sincronización de contraseña de MD5 es estrictamente para compatibilidad del protocolo de replicación con el controlador de dominio y solo se usa en local entre el controlador de dominio y el agente de sincronización de contraseña.
4.  El agente de sincronización de contraseña amplía el hash de contraseña binario de 16 bits a 64 bytes al convertir en primer lugar el hash con una cadena hexadecimal de 32 bytes y, después, convertir esta cadena de nuevo en binario con codificación UTF-16.
5.  El agente de sincronización de contraseña agrega un valor sal, que consta de un valor sal de longitud de 10 bytes, al archivo binario de 64 bits para proteger aún más el valor hash original.
6.  El agente de sincronización de contraseña combina el hash MD4 con el valor sal y los introduce en la función [PBKDF2](https://www.ietf.org/rfc/rfc2898.txt). Se usan 1000 iteraciones del algoritmo hash con clave [HMAC-SHA256](https://msdn.microsoft.com/library/system.security.cryptography.hmacsha256.aspx). 
7.  El agente de sincronización de contraseña toma el hash de 32 bits resultante, concatena el valor sal y el número de iteraciones de SHA256 con él (para su uso con Azure AD) y después transmite la cadena desde Azure AD Connect a Azure AD a través de SSL.</br> 
8.  Cuando un usuario intenta iniciar sesión en Azure AD y escribe su contraseña, esta se ejecuta a través del mismo proceso MD4 + sal + PBKDF2 + HMAC-SHA256. Si el hash resultante coincide con el valor hash almacenado en Azure AD, el usuario ha especificado la contraseña correcta y se autentica. 

>[!Note] 
>El hash MD4 original no se transmite a Azure AD. En su lugar, se transmite el hash SHA256 del algoritmo hash MD4 original. Por consiguiente, si se obtiene el hash almacenado en Azure AD, no se puede usar en un ataque pass-the-hash en local.

### <a name="how-password-synchronization-works-with-azure-active-directory-domain-services"></a>Funcionamiento de la sincronización de contraseñas con Azure Active Directory Domain Services
También puede usar la característica de sincronización de contraseña para sincronizar las contraseñas locales con [Azure Active Directory Domain Services](../../active-directory-domain-services/active-directory-ds-overview.md). En este escenario, la instancia de Azure Active Directory Domain Services autentica a los usuarios en la nube con todos los métodos disponibles en la instancia de Active Directory local. Esta experiencia es similar al uso de la Herramienta de migración Active Directory (ADMT) en un entorno local.

### <a name="security-considerations"></a>Consideraciones sobre la seguridad
Al sincronizar contraseñas, la versión de texto sin formato de su contraseña no se expone a la característica de sincronización de contraseñas ni a Azure AD ni a ninguno de los servicios asociados.

La autenticación del usuario tiene lugar en Azure AD, y no en la propia instancia de Active Directory de la organización. Si su organización se preocupa por los datos de la contraseña que salen de local de cualquier forma, tenga en cuenta el hecho de que los datos de la contraseña SHA256 que se almacenan en Azure AD (un hash del hash MD4 original) son mucho más seguros que lo que se almacena en Active Directory. Además, como no se puede descifrar este hash SHA256, no se vuelve al entorno de Active Directory de la organización y se presenta como una contraseña de usuario válida en un ataque pass-the-hash.





### <a name="password-policy-considerations"></a>Consideraciones de la directiva de contraseña
Existen dos tipos de directivas de contraseña que se ven afectados por la habilitación de la sincronización de contraseñas:

* Directiva de complejidad de contraseñas
* Directiva de expiración de contraseñas

#### <a name="password-complexity-policy"></a>Directiva de complejidad de contraseñas  
Cuando se habilita la sincronización de contraseñas, las directivas de complejidad de contraseñas en su instancia local de Active Directory reemplazan a las directivas de complejidad en la nube para los usuarios sincronizados. Puede usar todas las contraseñas válidas de su instancia de Active Directory local para acceder a servicios de Azure AD.

> [!NOTE]
> Las contraseñas para los usuarios que se crean directamente en la nube siguen estando sujetas a las directivas de contraseñas tal como se definen en la nube.

#### <a name="password-expiration-policy"></a>Directiva de expiración de contraseñas  
Si un usuario está en el ámbito de sincronización de contraseña, la contraseña de cuenta en la nube se establece en "*No caduca nunca*".

Puede seguir iniciando sesión en los servicios en la nube con una contraseña sincronizada que haya expirado en su entorno local. La contraseña en la nube se actualizará la próxima vez que cambie la contraseña en el entorno local.

#### <a name="account-expiration"></a>Expiración de la cuenta
Si su organización usa el atributo accountExpires como parte de la administración de cuentas de usuario, recuerde que este atributo no se sincroniza con Azure AD. Por consiguiente, una cuenta de Active Directory expirada en un entorno configurado para la sincronización de contraseña seguirá activa en Azure AD. Se recomienda que, si la cuenta ha expirado, una acción de flujo de trabajo debe desencadenar un script de PowerShell que deshabilite la cuenta de Azure AD del usuario. Por el contrario, cuando la cuenta está activada, la instancia de Azure AD debe estar activada.

### <a name="overwrite-synchronized-passwords"></a>Sobrescritura de las contraseñas sincronizadas
Un administrador puede restablecer manualmente la contraseña mediante Windows PowerShell.

En este caso, la nueva contraseña reemplaza a la contraseña sincronizada y todas las directivas de contraseñas definidas en la nube se aplican a la nueva contraseña.

Si vuelve a cambiar la contraseña local, la nueva contraseña se sincroniza con la nube y reemplaza a la contraseña actualizada manualmente.

La sincronización de una contraseña no influye en el usuario de Azure que ha iniciado sesión. Si se sincroniza un cambio de contraseña mientras tiene iniciada sesión en un servicio en la nube, este cambio no afectará de inmediato a la sesión actual del servicio en la nube. KMSI prolonga la duración de esta diferencia. Cuando el servicio en la nube requiera de nuevo su autenticación, deberá proporcionar la nueva contraseña.

### <a name="additional-advantages"></a>Ventajas adicionales

- Por lo general, la sincronización de contraseña es más fácil de implementar que un servicio de federación. No requiere ningún servidor adicional y elimina la dependencia en un servicio de federación de alta disponibilidad para autenticar a los usuarios. 
- También se puede habilitar la sincronización de contraseña además de la federación. Puede usarse como reserva en caso de que el servicio de federación experimente una interrupción del servicio.












## <a name="enable-password-synchronization"></a>Habilitación de la sincronización de contraseñas
Cuando se instala Azure AD Connect mediante la opción **Configuración rápida**, la sincronización de contraseñas se habilita automáticamente. Para conocer más detalles, consulte [Introducción a Azure AD Connect mediante la configuración rápida](active-directory-aadconnect-get-started-express.md).

Si usa una configuración personalizada al instalar Azure AD Connect, la sincronización de contraseña está disponible en la página de inicio de sesión del usuario. Para más información, consulte [Instalación personalizada de Azure AD Connect](active-directory-aadconnect-get-started-custom.md).

![Habilitación de la sincronización de contraseñas](./media/active-directory-aadconnectsync-implement-password-synchronization/usersignin.png)

### <a name="password-synchronization-and-fips"></a>Sincronización de contraseñas y FIPS
Si el servidor se bloquea según el Estándar Federal de Procesamiento de la Información (FIPS), se deshabilita el hash MD5.

**Para habilitar el MD5 para la sincronización de contraseñas, realice los pasos siguientes:**

1. Vaya a %programfiles%\Azure AD Sync\Bin.
2. Abra miiserver.exe.config.
3. Vaya al nodo configuration/runtime (al final del archivo).
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

Para obtener información sobre la seguridad y FIPS, vea [AAD Password Sync, Encryption and FIPS compliance](https://blogs.technet.microsoft.com/enterprisemobility/2014/06/28/aad-password-sync-encryption-and-fips-compliance/) (Cumplimiento de sincronización de contraseña, cifrado y FIPS AAD).

## <a name="troubleshoot-password-synchronization"></a>Solución de problemas de sincronización de contraseñas
Si tiene problemas con la sincronización de contraseña, vea [Solución de problemas de sincronización de contraseñas](active-directory-aadconnectsync-troubleshoot-password-synchronization.md).

## <a name="next-steps"></a>Pasos siguientes
* [Sincronización de Azure AD Connect: personalización de las opciones de sincronización](active-directory-aadconnectsync-whatis.md)
* [Integración de las identidades locales con Azure Active Directory](active-directory-aadconnect.md)
