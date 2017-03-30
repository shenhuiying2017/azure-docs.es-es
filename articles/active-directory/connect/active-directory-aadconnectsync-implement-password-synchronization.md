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
ms.date: 03/21/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: 424d8654a047a28ef6e32b73952cf98d28547f4f
ms.openlocfilehash: 946f135e832667ad6e32743be2b07b4f86cd1cae
ms.lasthandoff: 03/22/2017


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

Hay que tener en cuenta que el usuario debe introducir sus credenciales corporativas una segunda vez para autenticarse en Azure AD, independientemente de si ha iniciado sesión en su red corporativa. Estos patrones puede reducirse, sin embargo, si el usuario activa la casilla "Mantener la sesión iniciada" en el inicio de sesión. Si se activa, establece una cookie de sesión que deriva la autenticación durante un breve período. El comportamiento de "Mantener la sesión iniciada" se puede habilitar o deshabilitar por el administrador de Azure Active Directory.

> [!NOTE]
> Solo se admite la sincronización de la contraseña para el usuario del tipo de objeto de Active Directory. No se admite para el tipo de objeto iNetOrgPerson.

### <a name="detailed-description-of-how-password-synchronization-works"></a>Descripción detallada de cómo funciona la sincronización de contraseña
A continuación se describe en profundidad cómo funciona la sincronización de contraseña entre Active Directory y Azure Active Directory.

![Flujo detallado de contraseñas](./media/active-directory-aadconnectsync-implement-password-synchronization/arch3.png)


1. Cada dos minutos, el agente de sincronización de contraseña en el servidor de AD Connect solicita hashes de contraseña almacenados (el atributo unicodePwd) desde un controlador de dominio a través protocolo de replicación [MS-DRSR](https://msdn.microsoft.com/library/cc228086.aspx) estándar que se utiliza para sincronizar datos entre controladores de dominio. La cuenta de servicio debe tener los permisos para replicar cambios de directorio y para replicar cambios de directorio en todos los AD (se conceden de manera predeterminada en la instalación) para obtener los hashes de contraseña.
2. Antes del envío, el controlador de dominio cifra el hash de contraseña MD4 mediante una clave que es un hash de [MD5](http://www.rfc-editor.org/rfc/rfc1321.txt) de la clave de sesión RPC y valor sal. Después envía el resultado al agente de sincronización de contraseña a través de RPC. El controlador de dominio también pasa el valor sal al agente de sincronización mediante el protocolo de replicación del controlador de dominio, por lo que el agente podrá descifrar el sobre.
3.    Cuando el agente de sincronización de contraseña tiene el sobre cifrado, usa [MD5CryptoServiceProvider](https://msdn.microsoft.com/library/System.Security.Cryptography.MD5CryptoServiceProvider.aspx) y el valor sal para generar una clave para descifrar los datos recibidos a su formato original de MD4. En ningún momento el agente de sincronización de contraseña tiene acceso a la contraseña de texto no cifrado. El uso del agente de sincronización de contraseña de MD5 es estrictamente para compatibilidad del protocolo de replicación con el controlador de dominio y solo se usa en local entre el controlador de dominio y el agente de sincronización de contraseña.
4.    El agente de sincronización de contraseña amplía el hash de contraseña binario de 16 bits a 64 bytes al convertir en primer lugar el hash con una cadena hexadecimal de 32 bytes y, después, convertir esta cadena de nuevo en binario con codificación UTF-16.
5.    El agente de sincronización de contraseña agrega un valor sal, que consta de un valor sal de longitud de 10 bytes, al archivo binario de 64 bits para proteger aún más el valor hash original.
6.    El agente de sincronización de contraseña combina el hash MD4 con el valor sal y los introduce en la función [PBKDF2](https://www.ietf.org/rfc/rfc2898.txt), con 1000 iteraciones del algoritmo hash [HMAC-SHA256](https://msdn.microsoft.com/library/system.security.cryptography.hmacsha256.aspx) algoritmo hash con clave.
Azure AD 
7.    El agente de sincronización de contraseña toma el hash de 32 bits resultante, concatena el valor sal y el número de iteraciones de SHA256 con él (para su uso con Azure AD) y después transmite la cadena desde AD Connect a Azure AD a través de SSL.</br> 
8.    Cuando un usuario intenta iniciar sesión en Azure AD y escribe su contraseña, esta se ejecuta a través del mismo proceso MD4 + sal + PBKDF2 + HMAC-SHA256. Si el hash resultante coincide con el valor hash almacenado en Azure AD, el usuario ha especificado la contraseña correcta y se autentica. 

>[!Note] 
>El hash MD4 original no se transmite a Azure AD; en su lugar, se transmite el hash SHA256 del hash MD4 original. Como resultado, si se obtiene el hash almacenado en Azure AD, no se puede usar en un ataque pass-the-hash en local.

### <a name="how-password-synchronization-works-with-azure-ad-domain-services"></a>Funcionamiento de la sincronización de contraseñas con Servicios de dominio de Azure AD
También puede utilizar la característica de sincronización de contraseñas para sincronizar las contraseñas locales con los [Servicios de dominio de AD de Azure](../../active-directory-domain-services/active-directory-ds-overview.md). Este escenario permite a los Servicios de dominio de Azure AD autenticar a los usuarios en la nube con todos los métodos disponibles en la instancia de AD local. Esta experiencia es similar al uso de la Herramienta de migración Active Directory (ADMT) en un entorno local.

### <a name="security-considerations"></a>Consideraciones sobre la seguridad
Al sincronizar contraseñas, la versión de texto sin formato de su contraseña no se expone a la característica de sincronización de contraseñas ni a Azure AD ni a ninguno de los servicios asociados.

La autenticación del usuario tiene lugar en Azure AD, en lugar de en la propia instancia de Active Directory de la organización. Si su organización se preocupa por los datos de la contraseña que salen de local de cualquier forma, tenga en cuenta el hecho de que los datos de la contraseña SHA256 almacenados en Azure AD (un hash del hash MD4 original) son mucho más seguros que el contenido almacenado en Active Directory. Además, como no se puede descifrar este hash SHA256, no se vuelve al entorno de Active Directory de la organización y se presenta como una contraseña de usuario válida en un ataque pass-the-hash.





### <a name="password-policy-considerations"></a>Consideraciones de la directiva de contraseña
Existen dos tipos de directivas de contraseña que se ven afectados por la habilitación de la sincronización de contraseñas:

1. Directiva de complejidad de contraseñas
2. Directiva de expiración de contraseñas

**Password complexity policy**  
Cuando se habilita la sincronización de contraseñas, las directivas de complejidad de contraseñas en su instancia local de Active Directory anulan las directivas de complejidad en la nube para los usuarios sincronizados. Puede utilizar todas las contraseñas válidas de su instancia de Active Directory local para tener acceso a servicios de Azure AD.

> [!NOTE]
> Las contraseñas para los usuarios que se crean directamente en la nube siguen estando sujetas a las directivas de contraseñas tal como se definen en la nube.

**Password expiration policy**  
Si un usuario está en el ámbito de sincronización de contraseñas, la contraseña de cuenta en la nube se establece en "*No caduca nunca*".

Puede seguir iniciando sesión en servicios en la nube con una contraseña sincronizada que haya caducado en su entorno local. La contraseña en la nube se actualizará la próxima vez que cambie la contraseña en el entorno local.

**Expiración de la cuenta**: si su organización usa el atributo accountExpires como parte de la administración de cuentas de usuario, tenga en cuenta que este atributo no está sincronizado con Azure AD. Como resultado, una cuenta de AD expirada en un entorno configurado para la sincronización de contraseña seguirá activa en Azure AD. Se recomienda que, si la cuenta ha expirado, una acción de flujo de trabajo debe desencadenar un script de PowerShell que deshabilite la cuenta de Azure AD del usuario. Por el contrario, cuando la cuenta está habilitada, debe habilitarse Azure AD.

### <a name="overwriting-synchronized-passwords"></a>Sobrescritura de las contraseñas sincronizadas
Un administrador puede restablecer manualmente la contraseña utilizando Windows PowerShell.

En este caso, la nueva contraseña sobrescribe la contraseña sincronizada y todas las directivas de contraseñas definidas en la nube se aplican a la nueva contraseña.

Si cambia la contraseña local de nuevo, la nueva contraseña se sincroniza con la nube y reemplaza a la contraseña actualizada manualmente.

La sincronización de una contraseña no influye en el usuario con la sesión iniciada actualmente en Azure. Si se sincroniza un cambio de contraseña mientras tiene iniciada sesión en un servicio en la nube, este cambio no afectará de inmediato a la sesión actual del servicio en la nube. KMSI ampliará la duración de esta diferencia. Cuando el servicio en la nube requiera de nuevo su autenticación, deberá proporcionar la nueva contraseña.

### <a name="additional-advantages"></a>Ventajas adicionales

- Por lo general, la sincronización de contraseña es más fácil de implementar que un servicio de federación. No requiere ningún servidor adicional y elimina la dependencia en un servicio de federación de alta disponibilidad para autenticar a los usuarios. 
- La sincronización de contraseña también puede habilitarse además de la federación, por lo que puede utilizarse como una acción de reserva si el servicio de federación produce una interrupción del servicio.












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

