<properties
   pageTitle="Azure AD Connect: historial de versiones | Microsoft Azure"
   description="En este tema se muestran todas las versiones de Azure AD Connect y Azure AD Sync"
   services="active-directory"
   documentationCenter=""
   authors="AndKjell"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="10/20/2015"
   ms.author="andkjell"/>

# Azure AD Connect: historial de versiones

El equipo de Azure Active Directory actualiza periódicamente Azure AD Connect con nuevas características y funciones. No todas las adiciones son aplicables a todas las audiencias.

Este artículo está diseñado para ayudarle a realizar un seguimiento de las versiones que se han publicado y comprender si necesita actualizar a la versión más reciente o no.

## 1\.0.8667.0
Fecha de publicación: agosto de 2015

**Nuevas características:**

- Ahora, el asistente para la instalación de Azure AD Connect se adapta a todos los idiomas de Windows Server.
- Se ha agregado compatibilidad con el desbloqueo de cuentas cuando se usa la administración de contraseñas de Azure AD.

**Problemas corregidos:**

- El asistente para la instalación de Azure AD Connect se bloquea si otro usuario continúa la instalación, y no la persona que la inició por primera vez.
- Si una desinstalación anterior de Azure AD Connect no desinstala limpiamente Azure AD Connect Sync, no es posible volver a instalarlo.
- No se puede instalar Azure AD Connect mediante la instalación rápida si el usuario no está en el dominio raíz del bosque, o si se usa una versión distinta del inglés de Active Directory.
- Si no se puede resolver el FQDN de la cuenta de usuario de Active Directory, se muestra un mensaje de error engañoso para indicar que no se pudo confirmar el esquema.
- Si se cambia la cuenta usada en el conector de Active Directory fuera del asistente, se producirá un error en el asistente en ejecuciones posteriores.
- Azure AD Connect no se puede instalar en ocasiones en un controlador de dominio.
- No se puede habilitar y deshabilitar el "Modo provisional" si se han agregado atributos de extensión.
- La escritura diferida de contraseñas produce un error en alguna configuración debido a una contraseña incorrecta en el conector de Active Directory.
- No se puede actualizar la sincronización de directorios si dn se usa en el filtrado de atributos.

**Características de versión la preliminar eliminadas:**

- La característica de la versión preliminar [Reescritura de usuarios](active-directory-aadconnect-feature-preview.md#user-writeback) se ha eliminado temporalmente a raíz de los comentarios de nuestros clientes de dicha versión. Se volverá a agregar más adelante después de examinar los comentarios proporcionados.

## 1\.0.8641.0
Fecha de publicación: junio de 2015

**Versión inicial de Azure AD Connect.**

Ha cambiado el nombre de Azure AD Sync a Azure AD Connect.

**Nuevas características:**

- Instalación de [configuración rápida](active-directory-aadconnect-get-started-express.md)
- Posibilidad de [configurar ADFS](active-directory-aadconnect-get-started-custom.md#configuring-federation-with-ad-fs)
- Posibilidad de [actualizar desde DirSync](active-directory-aadconnect-dirsync-upgrade-get-started.md)
- [Evitar eliminaciones accidentales](active-directory-aadconnectsync-feature-prevent-accidental-deletes.md)
- Se ha introducido el [modo de ensayo](active-directory-aadconnectsync-operations.md#staging-mode)

**Nuevas características de la versión preliminar:**

- [Reescritura de usuarios](active-directory-aadconnect-feature-preview.md#user-writeback)
- [Escritura diferida de grupos](active-directory-aadconnect-feature-preview.md#group-writeback)
- [Escritura diferida de dispositivos](active-directory-aadconnect-get-started-custom-device-writeback.md)
- [Extensiones de directorio](active-directory-aadconnect-feature-preview.md#directory-extensions)


## 1\.0.494.0501
Fecha de publicación: mayo de 2015

**Nuevo requisito**

- Ahora, Azure AD Sync requiere que se instale la versión 4.5.1 de .Net Framework.

**Problemas corregidos:**

- La escritura diferida de contraseñas de Azure AD produce un error de conectividad de bus de servicio.

## 1\.0.491.0413
Fecha de publicación: abril de 2015

**Problemas corregidos y mejoras:**

- El conector de Active Directory no procesa las eliminaciones correctamente si está habilitada la Papelera de reciclaje y hay varios dominios en el bosque.
- Se ha mejorado el rendimiento de las operaciones de importación para el conector de Azure Active Directory.
- Cuando un grupo ha superado el límite de pertenencia (de forma predeterminada, el límite se establece en 50 000 objetos), el grupo se elimina en Azure Active Directory. El nuevo comportamiento es que el grupo se mantiene, se produce un error y no se exportará ningún nuevo cambio de pertenencia.
- No se puede aprovisionar un nuevo objeto si una eliminación preconfigurada con el mismo DN ya está presente en el espacio del conector.
- Algunos objetos se marcan para que se sincronicen durante una sincronización delta, aunque no haya ningún cambio preconfigurado en el objeto.
- Forzar una sincronización de contraseñas también elimina la lista preferida de controladores de dominio.
- CSExportAnalyzer tiene problemas con algunos estados de objetos.

**Nuevas características:**

- Una combinación puede conectarse ahora a "CUALQUIER" tipo de objeto en la MV.

## 1\.0.485.0222
Fecha de publicación: febrero de 2015

**Mejoras:**

- Rendimiento de importación mejorada.

**Problemas corregidos:**

- La sincronización de contraseñas respeta el atributo cloudFiltered usado por el filtrado de atributos. Los objetos filtrados no estarán en el ámbito de la sincronización de contraseñas.
- En raras ocasiones donde la topología tenía muchos controladores de dominio, la sincronización de contraseñas no funciona.
- Se ha habilitado en Azure AD/Intune "Servidor detenido" al importar desde el conector de Azure AD después de la administración de dispositivos.
- La unión de entidades de seguridad externas (FSP) desde varios dominios del mismo bosque produce un error de unión ambigua.

## 1\.0.475.1202
Fecha de publicación: diciembre de 2014

**Nuevas características:**

- Ahora es posible realizar la sincronización de contraseñas con filtrado basado en atributos. Para obtener más detalles, consulte [Sincronización de contraseñas con filtrado](active-directory-aadconnectsync-configure-filtering.md).
- El atributo msDS-ExternalDirectoryObjectID se reescribe en AD. De esta forma se agrega compatibilidad con las aplicaciones de Office 365 mediante OAuth2 para tener acceso tanto a los buzones en línea como locales en una implementación híbrida de Exchange.

**Problemas de actualización corregidos:**

- Hay una versión más reciente del ayudante de inicio de sesión en el servidor.
- Se usó una ruta de acceso de instalación personalizada para instalar Azure AD Sync.
- Un criterio de combinación personalizado no válido bloquea la actualización.

**Otras correcciones:**

- Se han corregido las plantillas para Office Pro Plus.
- Se han corregido los problemas de instalación ocasionados por nombres de usuario que comienzan con un guión.
- Se ha corregido la pérdida de la configuración de sourceAnchor cuando se ejecuta al asistente para la instalación por una segunda vez.
- Se ha corregido el seguimiento ETW para la sincronización de contraseñas

## 1\.0.470.1023
Publicado: octubre de 2014

**Nuevas características:**

- Sincronización de contraseñas desde varios AD locales a Azure AD.
- Interfaz de usuario de instalación localizada para todos los idiomas de Windows Server.

**Actualización de AADSync 1.0 GA**

Si ya tiene instalado Azure AD Sync, hay un paso adicional que debe seguir en caso de que haya cambiado alguna de las reglas de sincronización inmediata. Después de haber actualizado a la versión 1.0.470.1023, las reglas de sincronización que ha modificado se duplican. Para cada regla de sincronización modificada, haga lo siguiente:

- Busque la regla de sincronización que ha modificado y anote los cambios.
- Elimine la regla de sincronización.
- Busque la nueva regla de sincronización creada por Azure AD Sync y vuelva a aplicar los cambios.

**Permisos para la cuenta de AD**

Se deben conceder permisos adicionales a la cuenta de AD para poder leer los hash de contraseña de Active Directory. Los permisos que se deben conceder se denominan "Replicar cambios de directorio" y "Replicando todos los cambios de directorio". Ambos permisos son necesarios para poder leer los hash de contraseña.

## 1\.0.419.0911
Fecha de publicación: septiembre de 2014

**Versión inicial de Azure AD Sync.**

## Pasos siguientes
Obtenga más información sobre la [Integración de las identidades locales con Azure Active Directory](active-directory-aadconnect.md).

<!---HONumber=Oct15_HO4-->