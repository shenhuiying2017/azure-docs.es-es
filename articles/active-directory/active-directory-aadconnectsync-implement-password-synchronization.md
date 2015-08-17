<properties
	pageTitle="Sincronización de Azure AD Connect: implementación de la sincronización de contraseñas"
	description="Le proporciona la información que necesita para comprender cómo funciona la sincronización de contraseñas y cómo habilitarla en su entorno."
	services="active-directory"
	documentationCenter=""
	authors="markusvi"
	manager="swadhwa"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/27/2015"
	ms.author="markusvi"/>


# Sincronización de Azure AD Connect: implementación de la sincronización de contraseñas 

Con la sincronización de contraseñas permitirá a los usuarios usar la misma contraseña que utilizan para iniciar sesión en Active Directory local para iniciar sesión en Azure Active Directory.

El objetivo de este tema es proporcionarle la información que necesita para comprender cómo funciona la sincronización de contraseñas y cómo habilitarla en su entorno.

## Qué es la sincronización de contraseñas

La sincronización de contraseñas es una característica de Servicios de sincronización de Azure Active Directory Connect (Sincronización de Azure AD Connect) que sincroniza las contraseñas de usuario de Active Directory local a Azure Active Directory (Azure AD). Esta característica permite que los usuarios inicien sesión en sus servicios de Azure Active Directory (como Office 365, Microsoft Intune, CRM Online, etc.) con la misma contraseña que usan para iniciar sesión en la red local. Es importante tener en cuenta que esta característica no proporciona una solución de inicio de sesión único (SSO) porque no hay ningún intercambio/uso compartido de tokens en el proceso basado en la sincronización de contraseñas.

> [AZURE.NOTE]Para obtener más información acerca de los Servicios de dominio de Active Directory configurados para la sincronización de contraseñas y FIPS, vea Errores de sincronización de contraseñas en sistemas compatibles con FIPS.
 
## Disponibilidad de sincronización de contraseñas

Cualquier cliente de Azure Active Directory es apto para ejecutar la sincronización de contraseñas. A continuación se ofrece información sobre la compatibilidad de la sincronización de contraseñas y otras características como la autenticación federada.

## Funcionamiento de la sincronización de contraseñas

La sincronización de contraseñas es una extensión de la característica de sincronización de directorios implementada por Sincronización de Azure AD Connect. Por lo tanto, esta característica requiere la sincronización de directorios entre su local y Azure Active Directory para realizar la configuración.

El Servicio de dominio de Active Directory almacena contraseñas en forma de representación de valor hash de la contraseña de usuario real. El hash de contraseña no puede usarse para iniciar sesión en la red local. También se ha diseñado para que no se pueda deshacer para obtener acceso a la contraseña de texto sin formato del usuario. Para sincronizar una contraseña, Sincronización de Azure AD Connect extrae el hash de contraseña de usuario de Active Directory local. El procesamiento de seguridad adicional se aplica al hash de contraseña antes de que se sincronice con el servicio de Azure Active Directory Authentication. El flujo de datos reales del proceso de sincronización de contraseñas es similar al de sincronización de datos de usuario, como el nombre para mostrar o las direcciones de correo electrónico.

Las contraseñas se sincronizan con más frecuencia que la ventana de sincronización de directorios estándar para otros atributos. Las contraseñas se sincronizan según el usuario y, por lo general, en orden cronológico. Cuando se sincroniza una contraseña de usuario desde AD local a la nube, se sobrescribirá la contraseña existente en la nube.

Al habilitar la característica de sincronización de contraseñas por primera vez, se realizará una sincronización inicial de las contraseñas de todos los usuarios en el ámbito de Active Directory local en Azure Active Directory. No puede definir explícitamente el conjunto de usuarios cuyas contraseñas se sincronizarán con la nube. Posteriormente, cuando un usuario local cambie una contraseña, la característica de sincronización de contraseñas detectará y sincronizará la contraseña cambiada, normalmente en cuestión de minutos. La característica de sincronización de contraseñas reintenta automáticamente las sincronizaciones de contraseñas de usuario erróneas. Si se produce un error al intentar sincronizar una contraseña, el error se registra en el visor de eventos.

La sincronización de una contraseña no influye en los usuarios con la sesión iniciada actualmente. Si un usuario que ha iniciado sesión en un servicio en la nube también cambia la contraseña local, la sesión del servicio en la nube continuará sin interrupciones. Sin embargo, si el servicio en la nube requiere que el usuario vuelva a realizar la autenticación, debe proporcionarse la nueva contraseña. En este punto, el usuario debe proporcionar la nueva contraseña: la contraseña que se ha sincronizado recientemente desde Active Directory local a la nube.

## Consideraciones sobre la seguridad

Al sincronizar contraseñas, la versión de texto sin formato de una contraseña de usuario no se expone a la característica de sincronización de contraseñas ni a Azure AD ni a ninguno de los servicios asociados.

Además, no hay ningún requisito en Active Directory local para almacenar la contraseña en un formato cifrado reversible. Se usa un resumen del hash de contraseña de Windows Active Directory para la transmisión entre Azure Active Directory y AD local. El resumen del hash de contraseña no se puede usar para obtener acceso a recursos en el entorno local del cliente.

## Consideraciones de la directiva de contraseña

Existen dos tipos de directivas de contraseña que se ven afectados por la habilitación de la sincronización de contraseñas:

1. Directiva de complejidad de contraseñas
2. Directiva de expiración de contraseñas

### Directiva de complejidad de contraseñas

Cuando se habilita la sincronización de contraseñas, las directivas de complejidad de contraseñas configuradas en Active Directory local reemplazan a las directivas de complejidad que pueden definirse en la nube para los usuarios sincronizados. Esto significa que cualquier contraseña válida en el entorno del Active Directory local del usuario puede usarse para obtener acceso a servicios de Azure AD.


> [AZURE.NOTE]Las contraseñas para los usuarios que se crean directamente en la nube siguen estando sujetas a las directivas de contraseñas tal como se definen en la nube.
 
### Directiva de expiración de contraseñas

Si un usuario está en el ámbito de sincronización de contraseñas, la contraseña de cuenta en la nube se establece en "*No caduca nunca*". Esto significa que es posible que la contraseña de un usuario expire en el entorno local, pero los usuarios pueden seguir iniciando sesión en los servicios en la nube con la contraseña expirada.

La contraseña de la nube se actualizará la próxima vez que el usuario cambie la contraseña en el entorno local.


## Sobrescritura de las contraseñas sincronizadas

Un administrador puede restablecer manualmente la contraseña de un usuario con Azure Active Directory PowerShell.

En este caso, la nueva contraseña sobrescribirá la contraseña sincronizada del usuario y todas las directivas de contraseñas definidas en la nube se aplicarán a la nueva contraseña.

Si el usuario cambia la contraseña local de nuevo, la nueva contraseña se sincronizará con la nube y reemplazará a la contraseña actualizada manualmente.


## Preparación para la sincronización de contraseñas

El inquilino de Azure Active Directory debe habilitarse para la sincronización de directorios para que el inquilino pueda habilitar la sincronización de contraseñas.


## Habilitación de la sincronización de contraseñas

Habilite la sincronización de contraseñas cuando ejecute el Asistente de configuración de Azure AD Connect.

En la página del cuadro de diálogo **Características opcionales**, seleccione “**Sincronización de contraseñas**”.
 
![Características opcionales][1]


> [AZURE.NOTE]Este proceso activa una sincronización completa. Los ciclos de sincronización completa suelen tardan más en completarse que otros ciclos de sincronización.
 

## Administración de la sincronización de contraseñas

Puede supervisar el progreso de la sincronización de contraseñas mediante el registro de eventos de la máquina que está ejecutando Azure AD Connect.


### Determinación del estado de la sincronización de contraseñas

Puede determinar qué usuarios han sincronizado las contraseñas correctamente revisando los eventos que coinciden con los siguientes criterios:

| Origen| Id. de evento |
| --- | --- |
| Sincronización de directorios| 656|
| Sincronización de directorios| 657|
 
Los eventos con el Id. de evento 656 proporcionan un informe de solicitudes de cambio de contraseña procesadas:

![Id. de evento 656][2]

Los eventos correspondientes con el Id. 657 proporcionan el resultado de estas solicitudes:

![Id. de evento 657][3]

En los eventos, los objetos afectados se identifican por su delimitador y el valor DN. El valor del delimitador corresponde al valor **ImmutableId** que ha devuelto el cmdlet Get-MsoUser para un usuario.

Además de los identificadores de objeto, el **Id. de evento 656** proporciona la fecha en la que se cambió la contraseña del usuario en Active Directory local:

![Solicitud de cambio de contraseña][4]

El Id. de evento 657 tiene un campo de resultado además de los identificadores de objeto de origen para indicar el estado de sincronización para ese objeto de usuario.

Una contraseña sincronizada correctamente se indica en un evento con el Id. de evento 657 mediante el valor Success para el atributo Result. Cuando se produce un error al intentar realizar la sincronización de contraseñas, el valor del atributo Result es Failure:

![Resultado de cambio de contraseña][5]

 
## Deshabilitación de la sincronización de contraseñas

Deshabilite la sincronización de contraseñas volviendo a ejecutar el Asistente de configuración de Azure AD Connect. Cuando se lo solicite el asistente, anule la selección de la casilla "Sincronización de contraseñas".


> [AZURE.NOTE]Este proceso activa una sincronización completa. Los ciclos de sincronización completa suelen tardan más en completarse que otros ciclos de sincronización.
 
Después de ejecutar el Asistente para configuración, el inquilino dejará de sincronizar contraseñas. Los cambios de contraseña nueva no se sincronizarán con la nube. Los usuarios que anteriormente sincronizaron sus contraseñas podrán seguir iniciando sesión con esas contraseñas hasta que cambien manualmente las contraseñas en la nube.



## Recursos adicionales

* [Sincronización de Azure AD Connect: personalización de las opciones de sincronización](active-directory-aadconnectsync-whatis.md)
* [Integración de las identidades locales con Azure Active Directory](active-directory-aadconnect.md)
 
<!--Image references-->
[1]: ./media/active-directory-aadsync-implement-password-synchronization/IC759788.png
[2]: ./media/active-directory-aadsync-implement-password-synchronization/IC662504.png
[3]: ./media/active-directory-aadsync-implement-password-synchronization/IC662505.png
[4]: ./media/active-directory-aadsync-implement-password-synchronization/IC662506.png
[5]: ./media/active-directory-aadsync-implement-password-synchronization/IC662507.png

<!---HONumber=August15_HO6-->