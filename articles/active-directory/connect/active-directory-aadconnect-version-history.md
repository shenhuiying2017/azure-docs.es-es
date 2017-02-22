---
title: 'Azure AD Connect: historial de versiones | Microsoft Docs'
description: "En este tema se muestran todas las versiones de Azure AD Connect y Sincronización de Azure AD"
services: active-directory
documentationcenter: 
author: billmath
manager: femila
editor: 
ms.assetid: ef2797d7-d440-4a9a-a648-db32ad137494
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/08/2017
ms.author: billmath
translationtype: Human Translation
ms.sourcegitcommit: 60f41890912eee3be364f5b3a47ded71991ee342
ms.openlocfilehash: 780fb6d69dab0b27e13f1e358be048f834dfc185


---
# <a name="azure-ad-connect-version-release-history"></a>Azure AD Connect: historial de versiones
El equipo de Azure Active Directory actualiza periódicamente Azure AD Connect con nuevas características y funciones. No todas las adiciones son aplicables a todas las audiencias.

Este artículo está diseñado para ayudarle a realizar un seguimiento de las versiones que se han publicado y comprender si necesita actualizar a la versión más reciente o no.

Esta es la lista de temas relacionados:


Tema. |  
--------- | --------- |
Pasos para actualizar desde Azure AD Connect | Diferentes métodos para [actualizar desde una versión anterior a la última](active-directory-aadconnect-upgrade-previous-version.md) versión de Azure AD Connect.
Permisos necesarios | Para más información sobre los permisos necesarios para aplicar una actualización, consulte el tema de [cuentas y permisos](./active-directory-aadconnect-accounts-permissions.md#upgrade)
Descargar| [Descarga de Azure AD Connect](http://go.microsoft.com/fwlink/?LinkId=615771)

## <a name="113800"></a>1.1.380.0
Fecha de publicación: diciembre de 2016

**Problema corregido:**

* Se ha corregido el problema por el cual la regla de notificación de issuerid para AD FS falta en esta compilación.

>[!NOTE]
>Esta compilación no estará disponible para los clientes a través de la característica de actualización automática de Azure AD Connect.

## <a name="113710"></a>1.1.371.0
Fecha de publicación: diciembre de 2016

**Problema conocido:**

* La regla de notificación de issuerid para AD FS falta en esta compilación. La regla de notificación de issuerid es necesaria si se está realizando una federación de varios dominios con Azure AD. Si utiliza Azure AD Connect para administrar la implementación de AD FS local, la actualización a esta compilación quitará la regla de notificación de issuerid existente de la configuración de AD FS. Puede solucionar el problema agregando la regla de notificación de issuerid después de la instalación o actualización. Para obtener más información sobre cómo agregar la regla de notificación de issuerid, consulte el artículo [Multiple Domain Support for Federating with Azure AD](active-directory-aadconnect-multiple-domains.md) (Compatibilidad con varios dominios para la federación con Azure AD).

**Problema corregido:**

* La instalación o actualización de Azure AD Connect generará un error si el puerto 9090 no está abierto para las conexiones salientes.

>[!NOTE]
>Esta compilación no estará disponible para los clientes a través de la característica de actualización automática de Azure AD Connect.

## <a name="113700"></a>1.1.370.0
Fecha de publicación: diciembre de 2016

**Problemas conocidos**:

* La regla de notificación de issuerid para AD FS falta en esta compilación. La regla de notificación de issuerid es necesaria si se está realizando una federación de varios dominios con Azure AD. Si utiliza Azure AD Connect para administrar la implementación de AD FS local, la actualización a esta compilación quitará la regla de notificación de issuerid existente de la configuración de AD FS. Puede solucionar el problema agregando la regla de notificación de issuerid después de la instalación o actualización. Para obtener más información sobre cómo agregar la regla de notificación de issuerid, consulte el artículo [Multiple Domain Support for Federating with Azure AD](active-directory-aadconnect-multiple-domains.md) (Compatibilidad con varios dominios para la federación con Azure AD).
* El puerto 9090 debe estar abierto para las conexiones salientes a fin de completar la instalación.

**Nuevas características:**

* Autenticación de paso a través (versión preliminar)

>[!NOTE]
>Esta compilación no estará disponible para los clientes a través de la característica de actualización automática de Azure AD Connect.

## <a name="113430"></a>1.1.343.0
Publicado: noviembre de 2016

**Problema conocido:**

* La regla de notificación de issuerid para AD FS falta en esta compilación. La regla de notificación de issuerid es necesaria si se está realizando una federación de varios dominios con Azure AD. Si utiliza Azure AD Connect para administrar la implementación de AD FS local, la actualización a esta compilación quitará la regla de notificación de issuerid existente de la configuración de AD FS. Puede solucionar el problema agregando la regla de notificación de issuerid después de la instalación o actualización. Para obtener más información sobre cómo agregar la regla de notificación de issuerid, consulte el artículo [Multiple Domain Support for Federating with Azure AD](active-directory-aadconnect-multiple-domains.md) (Compatibilidad con varios dominios para la federación con Azure AD).

**Problemas corregidos:**

* A veces, Azure AD Connect no se puede instalar porque no puede crear una cuenta de servicio local cuya contraseña cumpla el nivel de complejidad especificado por la directiva de contraseñas de la organización.
* Se corrigió un problema por el que las reglas de unión no se vuelven a evaluar si un objeto en el espacio del conector se convierte simultáneamente en un objeto fuera de ámbito para una regla de unión y dentro de ámbito para otra regla. Esto puede ocurrir si tiene dos o más reglas de unión cuyas condiciones de unión son mutuamente excluyentes.
* Se ha corregido un problema por el que no se procesan las reglas de sincronización entrantes (desde Azure AD) que no contienen reglas de unión si tienen valores de prioridad inferiores que aquellas que sí que las contienen.

**Mejoras:**

* Se agregó compatibilidad para la instalación de Azure AD Connect en Windows Server 2016 estándar o superior.
* Se agregó compatibilidad para utilizar SQL Server 2016 como la base de datos remota para Azure AD Connect.

## <a name="112810"></a>1.1.281.0
Fecha de publicación: agosto de 2016

**Problemas corregidos:**

* Los cambios en el intervalo de sincronización no se realizarán hasta que finalice el siguiente ciclo de sincronización.
* El Asistente de Azure AD Connect no acepta cuentas de Azure AD cuyo nombre de usuario comience con un carácter de subrayado (\_).
* Se producirá un error en el Asistente de Azure AD Connect al autenticar la cuenta de Azure AD proporcionada si la contraseña contiene demasiados caracteres especiales. Se mostrará el mensaje de error: No se pueden validar las credenciales. Se produjo un error inesperado" .
* Al desinstalar el servidor de ensayo, se deshabilita la sincronización de contraseñas del inquilino de Azure AD y se produce un error de sincronización de contraseñas en el servidor activo.
* Se produce un error de sincronización de contraseñas en casos raros cuando no hay ningún valor de hash de contraseña almacenado en el usuario.
* Cuando se habilita el servidor de Azure AD Connect para el modo provisional, la escritura diferida de contraseñas se deshabilita temporalmente.
* El Asistente de Azure AD Connect no muestra la sincronización de contraseñas y la configuración de escritura diferida de contraseñas reales cuando el servidor está en modo provisional. Siempre se muestran como deshabilitadas.
* Los cambios de configuración en la sincronización de contraseñas y la escritura diferida de contraseñas no se almacenan en el Asistente de Azure AD Connect cuando el servidor está en modo provisional.

**Mejoras:**

* Se ha actualizado el cmdlet Start-ADSyncSyncCycle para indicar si se puede iniciar correctamente un nuevo ciclo de sincronización.
* Se ha agregado el cmdlet Stop-ADSyncSyncCycle para finalizar el ciclo de sincronización y la operación que estén actualmente en curso.
* Se ha actualizado el cmdlet Stop-ADSyncScheduler para finalizar el ciclo de sincronización y la operación que estén actualmente en curso.
* Al configurar [extensiones de directorio](active-directory-aadconnectsync-feature-directory-extensions.md) en el Asistente de Azure AD Connect, ahora se podrá seleccionar el atributo de AD de tipo cadena Teletexto.

## <a name="111890"></a>1.1.189.0
Fecha de publicación: junio de 2016

**Problemas corregidos y mejoras:**

* Azure AD Connect ahora puede instalarse en un servidor conforme a FIPS.
  * Para la sincronización de contraseñas, consulte [Sincronización de contraseñas y FIPS](active-directory-aadconnectsync-implement-password-synchronization.md#password-synchronization-and-fips)
* Se ha corregido un problema por el cual un nombre NetBIOS no se pudo resolver en el FQDN en el conector de Active Directory.

## <a name="111800"></a>1.1.180.0
Fecha de publicación: mayo de 2016

**Nuevas características:**

* Le advierte y ayuda a comprobar los dominios, si no lo ha hecho, antes de ejecutar Azure AD Connect.
* Se ha agregado compatibilidad con [Microsoft Cloud Germany](active-directory-aadconnect-instances.md#microsoft-cloud-germany).
* Se ha agregado compatibilidad con la versión más reciente de la infraestructura de [Microsoft Azure Government Cloud](active-directory-aadconnect-instances.md#microsoft-azure-government-cloud) con los nuevos requisitos de dirección URL.

**Problemas corregidos y mejoras:**

* Agrega el filtrado en el Editor de reglas de sincronización para facilitar la búsqueda de reglas de sincronización.
* Mejora el rendimiento en la eliminación de un espacio de conector.
* Se corrigió un problemas cuando el mismo objeto se eliminaba y se agregaba en la misma ejecución (llamado eliminar o agregar).
* Una regla de sincronización deshabilitada dejará de volver a habilitar objetos y atributos incluidos durante la actualización o actualización del esquema de directorio.

## <a name="111300"></a>1.1.130.0
Fecha de publicación: abril de 2016

**Nuevas características:**

* Se ha agregado compatibilidad con atributos multivalor en las [extensiones de directorio](active-directory-aadconnectsync-feature-directory-extensions.md).
* Se ha agregado compatibilidad con más variaciones de configuración para que la [actualización automática](active-directory-aadconnect-feature-automatic-upgrade.md) se considere apta para la actualización.
* Se han agregado algunos cmdlets para el [programador personalizado](active-directory-aadconnectsync-feature-scheduler.md#custom-scheduler).

## <a name="111190"></a>1.1.119.0
Publicación: marzo de 2016

**Problemas corregidos:**

* Nos aseguramos de que la instalación rápida no se pueda usar en Windows Server 2008 (versión preliminar 2) porque la sincronización de contraseñas no se admite en este sistema operativo.
* La actualización desde DirSync con una configuración de filtro personalizada no funcionó como se esperaba.
* Cuando se actualiza a una versión más reciente y no hay ningún cambio en la configuración, no se debe programar una importación o sincronización completa.

## <a name="111100"></a>1.1.110.0
Fecha de publicación: febrero de 2016

**Problemas corregidos:**

* La actualización desde versiones anteriores no funciona si la instalación no está en la carpeta predeterminada **C:\Archivos de programa**.
* Si efectúa la instalación y anula la selección de **Inicie el proceso de sincronización...** al final del Asistente para instalación, el programador no se habilitará al volver a ejecutar dicho asistente.
* El programador no funcionará según lo previsto en los servidores en los que el formato de fecha y hora no sea US-en. Además, impedirá que `Get-ADSyncScheduler` devuelva las horas correctas.
* Si ha instalado una versión anterior de Azure AD Connect con AD FS como opción de inicio de sesión y actualización, no puede volver a ejecutar el asistente para la instalación.

## <a name="111050"></a>1.1.105.0
Fecha de publicación: febrero de 2016

**Nuevas características:**

* [Automatic upgrade](active-directory-aadconnect-feature-automatic-upgrade.md) para los clientes de configuración rápida.
* Soporte para el administrador global que utiliza MFA y PIM en el Asistente para instalación.
  * Debe permitir que el servidor proxy para permitir también el tráfico a https://secure.aadcdn.microsoftonline-p.com si utiliza MFA.
  * Debe agregar https://secure.aadcdn.microsoftonline-p.com a la lista de sitios de confianza para que MFA funcione correctamente.
* Permite cambiar el método de inicio de sesión del usuario después de la instalación inicial.
* Permita el [filtrado de dominios y unidades organizativas](active-directory-aadconnect-get-started-custom.md#domain-and-ou-filtering) en el Asistente para instalación. Esto también permite conectar con bosques donde no todos los dominios están disponibles.
* [programador](active-directory-aadconnectsync-feature-scheduler.md) está integrado en el motor de sincronización.

**Características promocionadas desde la vista previa a GA:**

* [Escritura diferida de dispositivos](active-directory-aadconnect-feature-device-writeback.md)
* [Extensiones de directorio](active-directory-aadconnectsync-feature-directory-extensions.md)

**Nuevas características de la versión preliminar:**

* El nuevo intervalo de ciclo de sincronización predeterminado es de 30 minutos. Solía ser 3 horas en todas las versiones anteriores. Agrega compatibilidad para cambiar el comportamiento del [programador](active-directory-aadconnectsync-feature-scheduler.md) .

**Problemas corregidos:**

* La página de comprobación de dominios DNS no siempre reconocía los dominios.
* Solicita las credenciales de administrador de dominio al configurar ADFS.
* El Asistente para instalación no reconoce las cuentas de AD locales si están ubicadas en un dominio con un árbol DNS diferente al dominio raíz.

## <a name="1091310"></a>1.0.9131.0
Fecha de publicación: diciembre de 2015

**Problemas corregidos:**

* La sincronización de contraseñas podría no funcionar al cambiar las contraseñas en AD DS, pero funciona al establecer la contraseña.
* Cuando se tiene un servidor proxy, la autenticación en Azure AD puede producir un error durante la instalación o la actualización en la página de configuración.
* La actualización desde una versión anterior de Azure AD Connect con una instalación completa de SQL Server producirá un error si no es una asociación de seguridad en SQL.
* La actualización desde una versión anterior de Azure AD Connect con una instalación remota de SQL Server mostrará el error "No se puede obtener acceso a la base de datos SQL de ADSync".

## <a name="1091250"></a>1.0.9125.0
Publicado: noviembre de 2015

**Nuevas características:**

* Puede volver a configurar ADFS para la confianza de Azure AD.
* Puede actualizar el esquema de Active Directory y volver a generar reglas de sincronización.
* Puede deshabilitar una regla de sincronización.
* Puede definir "AuthoritativeNull" como un nuevo literal en una regla de sincronización.

**Nuevas características de la versión preliminar:**

* [Azure AD Connect Health para sincronización](../connect-health/active-directory-aadconnect-health-sync.md)
* Compatibilidad para sincronización de contraseñas de [Servicios de dominio de Azure AD](../active-directory-passwords-getting-started.md#enable-users-to-reset-or-change-their-ad-passwords) .

**Nuevo escenarios admitido:**

* Admite varias organizaciones de Exchange locales. Consulte [Implementaciones híbridas con varios bosques de Active Directory](https://technet.microsoft.com/library/jj873754.aspx) para obtener más información.

**Problemas corregidos:**

* Problemas de sincronización de contraseñas:
  * Un objeto movido desde fuera de ámbito a dentro de ámbito no tendrá su contraseña sincronizada. Esto incluye tanto UO como el filtrado de atributos.
  * La selección de una nueva UO para incluir en sincronización no requiere una sincronización de contraseñas completa.
  * Cuando un usuario deshabilitado se habilita, la contraseña no se sincroniza.
  * La cola de reintentos de contraseña es infinita y el límite anterior de 5.000 objetos para retirar anterior se ha quitado.
  * [Solución de problemas mejorada](active-directory-aadconnectsync-implement-password-synchronization.md#troubleshooting-password-synchronization).
* No se puede conectar con Active Directory con el nivel funcional de bosque de Windows Server 2016.
* No se puede cambiar el grupo usado para el filtrado de grupo tras la instalación inicial.
* Ya no se creará un nuevo perfil de usuario en el servidor de Azure AD Connect para cada usuario haciendo un cambio de contraseña con la escritura diferida de contraseñas habilitada.
* No se pueden usar valores enteros largos en ámbitos de reglas de sincronización.
* La casilla de verificación "Reescritura de dispositivos" permanece deshabilitada si hay controladores de dominio inalcanzables.

## <a name="1086670"></a>1.0.8667.0
Fecha de publicación: agosto de 2015

**Nuevas características:**

* Ahora, el asistente para la instalación de Azure AD Connect se adapta a todos los idiomas de Windows Server.
* Se ha agregado compatibilidad con el desbloqueo de cuentas cuando se usa la administración de contraseñas de Azure AD.

**Problemas corregidos:**

* El asistente para la instalación de Azure AD Connect se bloquea si otro usuario continúa la instalación, y no la persona que la inició por primera vez.
* Si una desinstalación anterior de Azure AD Connect no desinstala limpiamente Azure AD Connect Sync, no es posible volver a instalarlo.
* No se puede instalar Azure AD Connect mediante la instalación rápida si el usuario no está en el dominio raíz del bosque, o si se usa una versión distinta del inglés de Active Directory.
* Si no se puede resolver el FQDN de la cuenta de usuario de Active Directory, se muestra un mensaje de error engañoso para indicar que no se pudo confirmar el esquema.
* Si se cambia la cuenta usada en el conector de Active Directory fuera del asistente, se producirá un error en el asistente en ejecuciones posteriores.
* Azure AD Connect no se puede instalar en ocasiones en un controlador de dominio.
* No se puede habilitar y deshabilitar el "Modo provisional" si se han agregado atributos de extensión.
* La escritura diferida de contraseñas produce un error en alguna configuración debido a una contraseña incorrecta en el conector de Active Directory.
* No se puede actualizar la sincronización de directorios si dn se usa en el filtrado de atributos.
* Uso excesivo de CPU al utilizar el restablecimiento de contraseña.

**Características de versión la preliminar eliminadas:**

* La característica en vista previa [Reescritura de usuarios](active-directory-aadconnect-feature-preview.md#user-writeback) se ha eliminado temporalmente a raíz de los comentarios de nuestros clientes de vista previa. Se volverá a agregar más adelante después de examinar los comentarios proporcionados.

## <a name="1086410"></a>1.0.8641.0
Fecha de publicación: junio de 2015

**Versión inicial de Azure AD Connect.**

Ha cambiado el nombre de Azure AD Sync a Azure AD Connect.

**Nuevas características:**

* Instalación de la [configuración rápida](active-directory-aadconnect-get-started-express.md)
* Posibilidad de [configurar ADFS](active-directory-aadconnect-get-started-custom.md#configuring-federation-with-ad-fs)
* Posibilidad de [actualizar desde DirSync](active-directory-aadconnect-dirsync-upgrade-get-started.md)
* [Evitar eliminaciones accidentales](active-directory-aadconnectsync-feature-prevent-accidental-deletes.md)
* Se ha introducido el [modo de ensayo](active-directory-aadconnectsync-operations.md#staging-mode)

**Nuevas características de la versión preliminar:**

* [Reescritura de usuarios](active-directory-aadconnect-feature-preview.md#user-writeback)
* [Escritura diferida de grupos](active-directory-aadconnect-feature-preview.md#group-writeback)
* [Escritura diferida de dispositivos](active-directory-aadconnect-feature-device-writeback.md)
* [Extensiones de directorio](active-directory-aadconnect-feature-preview.md)

## <a name="104940501"></a>1.0.494.0501
Fecha de publicación: mayo de 2015

**Nuevo requisito**

* Ahora, Azure AD Sync requiere que se instale la versión 4.5.1 de .Net Framework.

**Problemas corregidos:**

* La escritura diferida de contraseñas de Azure AD produce un error de conectividad de bus de servicio.

## <a name="104910413"></a>1.0.491.0413
Fecha de publicación: abril de 2015

**Problemas corregidos y mejoras:**

* El conector de Active Directory no procesa las eliminaciones correctamente si está habilitada la Papelera de reciclaje y hay varios dominios en el bosque.
* Se ha mejorado el rendimiento de las operaciones de importación para el conector de Azure Active Directory.
* Cuando un grupo ha superado el límite de pertenencia (de forma predeterminada, el límite se establece en 50 000 objetos), el grupo se elimina en Azure Active Directory. El nuevo comportamiento es que el grupo se mantiene, se produce un error y no se exportará ningún nuevo cambio de pertenencia.
* No se puede aprovisionar un nuevo objeto si una eliminación preconfigurada con el mismo DN ya está presente en el espacio del conector.
* Algunos objetos se marcan para que se sincronicen durante una sincronización delta, aunque no haya ningún cambio preconfigurado en el objeto.
* Forzar una sincronización de contraseñas también elimina la lista preferida de controladores de dominio.
* CSExportAnalyzer tiene problemas con algunos estados de objetos.

**Nuevas características:**

* Una combinación puede conectarse ahora a "CUALQUIER" tipo de objeto en la MV.

## <a name="104850222"></a>1.0.485.0222
Fecha de publicación: febrero de 2015

**Mejoras:**

* Rendimiento de importación mejorada.

**Problemas corregidos:**

* La sincronización de contraseñas respeta el atributo cloudFiltered usado por el filtrado de atributos. Los objetos filtrados no estarán en el ámbito de la sincronización de contraseñas.
* En raras ocasiones donde la topología tenía muchos controladores de dominio, la sincronización de contraseñas no funciona.
* Se ha habilitado en Azure AD/Intune "Servidor detenido" al importar desde el conector de Azure AD después de la administración de dispositivos.
* La unión de entidades de seguridad externas (FSP) desde varios dominios del mismo bosque produce un error de unión ambigua.

## <a name="104751202"></a>1.0.475.1202
Fecha de publicación: diciembre de 2014

**Nuevas características:**

* Ahora es posible realizar la sincronización de contraseñas con filtrado basado en atributos. Para obtener más detalles, consulte [Sincronización de contraseñas con filtrado](active-directory-aadconnectsync-configure-filtering.md).
* El atributo msDS-ExternalDirectoryObjectID se reescribe en AD. De esta forma se agrega compatibilidad con las aplicaciones de Office 365 mediante OAuth2 para tener acceso tanto a los buzones en línea como locales en una implementación híbrida de Exchange.

**Problemas de actualización corregidos:**

* Hay una versión más reciente del ayudante de inicio de sesión en el servidor.
* Se usó una ruta de acceso de instalación personalizada para instalar Azure AD Sync.
* Un criterio de combinación personalizado no válido bloquea la actualización.

**Otras correcciones:**

* Se han corregido las plantillas para Office Pro Plus.
* Se han corregido los problemas de instalación ocasionados por nombres de usuario que comienzan con un guión.
* Se ha corregido la pérdida de la configuración de sourceAnchor cuando se ejecuta al asistente para la instalación por una segunda vez.
* Se ha corregido el seguimiento ETW para la sincronización de contraseñas

## <a name="104701023"></a>1.0.470.1023
Publicado: octubre de 2014

**Nuevas características:**

* Sincronización de contraseñas desde varios AD locales a Azure AD.
* Interfaz de usuario de instalación localizada para todos los idiomas de Windows Server.

**Actualización de AADSync 1.0 GA**

Si ya tiene instalado Azure AD Sync, hay un paso adicional que debe seguir en caso de que haya cambiado alguna de las reglas de sincronización inmediata. Después de haber actualizado a la versión 1.0.470.1023, las reglas de sincronización que ha modificado se duplican. Para cada regla de sincronización modificada, haga lo siguiente:

* Busque la regla de sincronización que ha modificado y anote los cambios.
* Elimine la regla de sincronización.
* Busque la nueva regla de sincronización creada por Azure AD Sync y vuelva a aplicar los cambios.

**Permisos para la cuenta de AD**

Se deben conceder permisos adicionales a la cuenta de AD para poder leer los hash de contraseña de Active Directory. Los permisos que se deben conceder se denominan "Replicar cambios de directorio" y "Replicando todos los cambios de directorio". Ambos permisos son necesarios para poder leer los hash de contraseña.

## <a name="104190911"></a>1.0.419.0911
Fecha de publicación: septiembre de 2014

**Versión inicial de Azure AD Sync.**

## <a name="next-steps"></a>Pasos siguientes
Obtenga más información sobre la [Integración de las identidades locales con Azure Active Directory](active-directory-aadconnect.md).




<!--HONumber=Jan17_HO1-->


