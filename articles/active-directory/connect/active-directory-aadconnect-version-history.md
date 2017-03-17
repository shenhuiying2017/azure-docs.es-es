---
title: 'Azure AD Connect: historial de versiones | Microsoft Docs'
description: "En este artículo se muestran todas las versiones de Azure AD Connect y Sincronización de Azure AD"
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
ms.sourcegitcommit: d9dad6cff80c1f6ac206e7fa3184ce037900fc6b
ms.openlocfilehash: 7e16fa749389ab876ae413e2ffef7713ed22adac
ms.lasthandoff: 03/06/2017


---
# <a name="azure-ad-connect-version-release-history"></a>Azure AD Connect: historial de versiones
El equipo de Azure Active Directory (Azure AD) actualiza periódicamente Azure AD Connect con nuevas características y funcionalidades. No todas las adiciones son aplicables a todas las audiencias.

Este artículo está diseñado para ayudarle a realizar un seguimiento de las versiones que se han publicado y comprender si necesita actualizar a la versión más reciente o no.

Lista de temas relacionados:


Tema. |  Detalles
--------- | --------- |
Pasos para actualizar desde Azure AD Connect | Diferentes métodos para [actualizar desde una versión anterior a la última](active-directory-aadconnect-upgrade-previous-version.md) versión de Azure AD Connect.
Permisos necesarios | Para más información sobre los permisos necesarios para aplicar una actualización, consulte [cuentas y permisos](./active-directory-aadconnect-accounts-permissions.md#upgrade).
Descargar| [Descarga de Azure AD Connect](http://go.microsoft.com/fwlink/?LinkId=615771).

## <a name="114430"></a>1.1.443.0
Fecha de publicación: marzo de 2017

**Problemas corregidos:**

Sincronización de Azure AD Connect
* Se ha corregido un problema que hace el Asistente para Azure AD Connect deje de funcionar si el nombre para mostrar del conector de Azure AD no contiene el dominio inicial onmicrosoft.com que se asignan al inquilino de Azure AD.
* Se ha corregido un problema que hace que el Asistente para Azure AD Connect genere un error al realizar la conexión a la base de datos SQL cuando la contraseña de la cuenta de servicio de sincronización tenga caracteres especiales, como un apóstrofo, una coma y un espacio.
* Se corrigió un problema que hace que el error que indica que dimage tiene un delimitador que es diferente de la imagen se produzca en un servidor de Azure AD Connect en modo de almacenamiento provisional, una vez que ha excluido temporalmente un objeto AD de sincronización local de la sincronización y, luego, lo incluye de nuevo para realizar la sincronización.
* Se corrigió un problema que hace que el error que indica que el objeto que ha localizado DN es un fantasma se produzca en un servidor de Azure AD Connect en modo de almacenamiento provisional, una vez que ha excluido temporalmente un objeto AD de sincronización local de la sincronización y, luego, lo incluye de nuevo para realizar la sincronización.

Administración de AD FS
* Se ha corregido un problema donde el Asistente para Azure AD Connect no actualizar la configuración de AD FS y establece las notificaciones adecuadas en el usuario de confianza después de configurar el identificador de inicio de sesión alternativo.
* Se ha corregido un problema donde el Asistente para Azure AD Connect no puede administrar correctamente los servidores de AD FS cuyas cuentas de servicio se configuran mediante el formato userPrincipalName en lugar de sAMAccountName.

Autenticación de paso a través
* Se ha corregido un problema que hace que el Asistente para Azure AD Connect deje de funcionar si se selecciona la autenticación de paso a través, pero se produce un error de registro de su conector.
* Se ha corregido un problema que hace que el Asistente para Azure AD Connect pase por alto las comprobaciones de validación en el método de inicio de sesión seleccionado cuando se habilita la característica de SSO de escritorio.

**Nuevas características y mejoras:**

Sincronización de Azure AD Connect
* El cmdlet Get-ADSyncScheduler ahora devuelve una nueva propiedad booleana denominada "SyncCycleInProgress". Si el valor devuelto es True, significa que hay un ciclo de sincronización programada en curso.
* La carpeta de destino para almacenar la instalación de Azure AD Connect y los registros de instalación se han movido desde %localappdata%\AADConnect a %programdata%\AADConnect para mejorar la accesibilidad a los archivos de registro.

Administración de AD FS
* Se agregó compatibilidad para actualizar el certificado SSL de granja de servidores de AD FS.
* Se agregó compatibilidad para administrar AD FS 2016.
* Ahora puede especificar gMSA existente (cuenta de servicio administrada de grupo) durante la instalación de AD FS.
* Ahora puede configurar SHA-256 como el algoritmo de hash de firma para usuarios de confianza de Azure AD.

## <a name="113800"></a>1.1.380.0
Fecha de publicación: diciembre de 2016

**Problema corregido:**

* Se ha corregido el problema por el cual falta la regla de notificación de issuerid para Active Directory Federation Services (AD FS) en esta compilación.

>[!NOTE]
>Esta compilación no está disponible para los clientes a través de la característica de actualización automática de Azure AD Connect.

## <a name="113710"></a>1.1.371.0
Fecha de publicación: diciembre de 2016

**Problema conocido:**

* Falta la regla de notificación de issuerid para AD FS en esta compilación. La regla de notificación de issuerid es necesaria si se está realizando una federación de varios dominios con Azure Active Directory (Azure AD). Si utiliza Azure AD Connect para administrar la implementación de AD FS local, la actualización a esta compilación quitará la regla de notificación de issuerid existente de la configuración de AD FS. Puede solucionar el problema agregando la regla de notificación de issuerid después de la instalación o actualización. Para más información sobre cómo agregar la regla de notificación de issuerid, consulte el artículo [Compatibilidad con varios dominios para la federación con Azure AD](active-directory-aadconnect-multiple-domains.md).

**Problema corregido:**

* Si el puerto 9090 no está abierto para las conexiones salientes, la instalación o actualización de Azure AD Connect producirá un error.

>[!NOTE]
>Esta compilación no está disponible para los clientes a través de la característica de actualización automática de Azure AD Connect.

## <a name="113700"></a>1.1.370.0
Fecha de publicación: diciembre de 2016

**Problemas conocidos**:

* Falta la regla de notificación de issuerid para AD FS en esta compilación. La regla de notificación de issuerid es necesaria si se está realizando una federación de varios dominios con Azure AD. Si utiliza Azure AD Connect para administrar la implementación de AD FS local, la actualización a esta compilación quitará la regla de notificación de issuerid existente de la configuración de AD FS. Puede solucionar el problema agregando la regla de notificación de issuerid después de la instalación o actualización. Para más información sobre cómo agregar la regla de notificación de issuerid, consulte el artículo [Compatibilidad con varios dominios para la federación con Azure AD](active-directory-aadconnect-multiple-domains.md).
* El puerto 9090 debe estar abierto para las conexiones salientes a fin de completar la instalación.

**Nuevas características:**

* Autenticación de paso a través (versión preliminar)

>[!NOTE]
>Esta compilación no está disponible para los clientes a través de la característica de actualización automática de Azure AD Connect.

## <a name="113430"></a>1.1.343.0
Fecha de publicación: noviembre de 2016

**Problema conocido:**

* Falta la regla de notificación de issuerid para AD FS en esta compilación. La regla de notificación de issuerid es necesaria si se está realizando una federación de varios dominios con Azure AD. Si utiliza Azure AD Connect para administrar la implementación de AD FS local, la actualización a esta compilación quitará la regla de notificación de issuerid existente de la configuración de AD FS. Puede solucionar el problema agregando la regla de notificación de issuerid después de la instalación o actualización. Para más información sobre cómo agregar la regla de notificación de issuerid, consulte el artículo [Compatibilidad con varios dominios para la federación con Azure AD](active-directory-aadconnect-multiple-domains.md).

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
* El Asistente de Azure AD Connect no acepta cuentas de Azure AD cuyo nombre de usuario comience con un guion bajo (\_).
* Si la contraseña contiene demasiados caracteres especiales, se producirá un error en el Asistente de Azure AD Connect al autenticar la cuenta de Azure AD. Se mostrará el mensaje de error: No se pueden validar las credenciales. Se produjo un error inesperado" .
* Al desinstalar el servidor de ensayo, se deshabilita la sincronización de contraseñas del inquilino de Azure AD y se produce un error de sincronización de contraseñas en el servidor activo.
* Se produce un error de sincronización de contraseñas en casos raros cuando no hay ningún valor de hash de contraseña almacenado en el usuario.
* Cuando se habilita el servidor de Azure AD Connect para el modo provisional, la escritura diferida de contraseñas se deshabilita temporalmente.
* El Asistente de Azure AD Connect no muestra la sincronización de contraseñas y la configuración de escritura diferida de contraseñas reales cuando el servidor está en modo provisional. Siempre se muestran como deshabilitadas.
* Los cambios de configuración en la sincronización de contraseñas y la escritura diferida de contraseñas no se almacenan en el Asistente de Azure AD Connect cuando el servidor está en modo provisional.

**Mejoras:**

* Se ha actualizado el cmdlet Start-ADSyncSyncCycle para indicar si se puede iniciar correctamente un nuevo ciclo de sincronización.
* Se ha agregado el cmdlet Stop-ADSyncSyncCycle para finalizar el ciclo de sincronización y la operación actualmente en curso.
* Se ha actualizado el cmdlet Stop-ADSyncScheduler para finalizar el ciclo de sincronización y la operación actualmente en curso.
* Al configurar [extensiones de directorio](active-directory-aadconnectsync-feature-directory-extensions.md) en el Asistente de Azure AD Connect, ahora se podrá seleccionar el atributo de Azure AD de tipo cadena Teletexto.

## <a name="111890"></a>1.1.189.0
Fecha de publicación: junio de 2016

**Problemas corregidos y mejoras:**

* Azure AD Connect ahora puede instalarse en un servidor conforme a FIPS.
  * Para la sincronización de contraseñas, consulte [Sincronización de contraseñas y FIPS](active-directory-aadconnectsync-implement-password-synchronization.md#password-synchronization-and-fips).
* Se ha corregido un problema por el cual un nombre NetBIOS no se pudo resolver en el FQDN en el conector de Active Directory.

## <a name="111800"></a>1.1.180.0
Fecha de publicación: mayo de 2016

**Nuevas características:**

* Le advierte y ayuda a comprobar los dominios, si no lo hizo antes de ejecutar Azure AD Connect.
* Se ha agregado compatibilidad con [Microsoft Cloud Germany](active-directory-aadconnect-instances.md#microsoft-cloud-germany).
* Se ha agregado compatibilidad con la versión más reciente de la infraestructura de [Microsoft Azure Government Cloud](active-directory-aadconnect-instances.md#microsoft-azure-government-cloud) con los nuevos requisitos de dirección URL.

**Problemas corregidos y mejoras:**

* Agrega el filtrado en el Editor de reglas de sincronización para facilitar la búsqueda de reglas de sincronización.
* Mejora el rendimiento en la eliminación de un espacio de conector.
* Se corrigió un problema cuando el mismo objeto se eliminaba y se agregaba en la misma ejecución (llamado eliminar o agregar).
* Una regla de sincronización deshabilitada ya no vuelve a habilitar objetos y atributos incluidos durante la actualización del esquema de directorio o una actualización.

## <a name="111300"></a>1.1.130.0
Fecha de publicación: abril de 2016

**Nuevas características:**

* Se ha agregado compatibilidad con atributos multivalor en las [extensiones de directorio](active-directory-aadconnectsync-feature-directory-extensions.md).
* Se ha agregado compatibilidad con más variaciones de configuración para que la [actualización automática](active-directory-aadconnect-feature-automatic-upgrade.md) se considere apta para la actualización.
* Se han agregado algunos cmdlets para el [programador personalizado](active-directory-aadconnectsync-feature-scheduler.md#custom-scheduler).

## <a name="111190"></a>1.1.119.0
Fecha de publicación: marzo de 2016

**Problemas corregidos:**

* Nos aseguramos de que la instalación rápida no se pueda usar en Windows Server 2008 (versión preliminar 2) porque la sincronización de contraseñas no es compatible con este sistema operativo.
* La actualización desde DirSync con una configuración de filtro personalizada no funcionó como se esperaba.
* Cuando se actualiza a una versión más reciente y no hay ningún cambio en la configuración, no se debe programar una importación o sincronización completa.

## <a name="111100"></a>1.1.110.0
Fecha de publicación: febrero de 2016

**Problemas corregidos:**

* La actualización desde versiones anteriores no funciona si la instalación no está en la carpeta predeterminada C:\Archivos de programa.
* Si efectúa la instalación y anula la selección de **Inicie el proceso de sincronización** al final del asistente para instalación, el programador no se habilitará al volver a ejecutar el asistente.
* El programador no funciona según lo previsto en los servidores en los que no se use el formato de fecha y hora US-en. Además, impedirá que `Get-ADSyncScheduler` devuelva las horas correctas.
* Si ha instalado una versión anterior de Azure AD Connect con AD FS como opción de inicio de sesión y actualización, no puede volver a ejecutar el asistente para instalación.

## <a name="111050"></a>1.1.105.0
Fecha de publicación: febrero de 2016

**Nuevas características:**

* [Automatic upgrade](active-directory-aadconnect-feature-automatic-upgrade.md) para los clientes de configuración rápida.
* Compatibilidad con el administrador global mediante Azure Multi-Factor Authentication y Privileged Identity Management en el asistente para la instalación.
  * Si utiliza Multi-Factor Authentication, debe permitir que el servidor proxy también permita el tráfico a https://secure.aadcdn.microsoftonline-p.com.
  * Debe agregar https://secure.aadcdn.microsoftonline-p.com a la lista de sitios de confianza para que Multi-Factor Authentication funcione correctamente.
* Permite cambiar el método de inicio de sesión del usuario después de la instalación inicial.
* Permita el [filtrado de dominios y unidades organizativas](active-directory-aadconnect-get-started-custom.md#domain-and-ou-filtering) en el Asistente para instalación. Esto también permite conectar con bosques donde no todos los dominios están disponibles.
* [Scheduler](active-directory-aadconnectsync-feature-scheduler.md) está integrado en el motor de sincronización.

**Características promocionadas desde la vista previa a GA:**

* [Escritura diferida de dispositivos](active-directory-aadconnect-feature-device-writeback.md)
* [Extensiones de directorio](active-directory-aadconnectsync-feature-directory-extensions.md)

**Nuevas características de la versión preliminar:**

* El nuevo intervalo de ciclo de sincronización predeterminado es de 30 minutos. Solía ser tres horas en todas las versiones anteriores. Agrega compatibilidad para cambiar el comportamiento del [programador](active-directory-aadconnectsync-feature-scheduler.md) .

**Problemas corregidos:**

* La página de comprobación de dominios DNS no siempre reconocía los dominios.
* Solicita las credenciales de administrador de dominio al configurar AD FS.
* El Asistente para instalación no reconoce las cuentas de AD locales si están ubicadas en un dominio con un árbol DNS diferente al dominio raíz.

## <a name="1091310"></a>1.0.9131.0
Fecha de publicación: diciembre de 2015

**Problemas corregidos:**

* La sincronización de contraseñas podría no funcionar al cambiar las contraseñas en Active Directory Domain Services (AD DS), pero funciona al establecer una contraseña.
* Con un servidor proxy, la autenticación en Azure AD puede producir errores durante la instalación o si se cancela una actualización en la página de configuración.
* La actualización desde una versión anterior de Azure AD Connect con una instalación completa de una instancia de SQL Server produce errores si no es administrador del sistema de SQL Server (SA).
* La actualización desde una versión anterior de Azure AD Connect con una instalación remota de SQL Server muestra el error "Unable to access the ADSync SQL database" (No se puede acceder a la base de datos SQL de ADSync).

## <a name="1091250"></a>1.0.9125.0
Fecha de publicación: noviembre de 2015

**Nuevas características:**

* Puede volver a configurar AD FS para la confianza de Azure AD.
* Puede actualizar el esquema de Active Directory y volver a generar reglas de sincronización.
* Puede deshabilitar una regla de sincronización.
* Puede definir "AuthoritativeNull" como un nuevo literal en una regla de sincronización.

**Nuevas características de la versión preliminar:**

* [Azure AD Connect Health para sincronización](../connect-health/active-directory-aadconnect-health-sync.md)
* Compatibilidad para sincronización de contraseñas de [Servicios de dominio de Azure AD](../active-directory-passwords-getting-started.md#enable-users-to-reset-or-change-their-ad-passwords) .

**Nuevo escenarios admitido:**

* Admite varias organizaciones de Exchange locales. Consulte [Implementaciones híbridas con varios bosques de Active Directory](https://technet.microsoft.com/library/jj873754.aspx) para más información.

**Problemas corregidos:**

* Problemas de sincronización de contraseñas:
  * Un objeto movido desde fuera de ámbito a dentro de ámbito no tendrá su contraseña sincronizada. Esto incluye tanto UO como el filtrado de atributos.
  * La selección de una nueva UO para incluir en sincronización no requiere una sincronización de contraseñas completa.
  * Cuando un usuario deshabilitado se habilita, la contraseña no se sincroniza.
  * La cola de reintentos de contraseña es infinita y el límite anterior de 5.000 objetos para retirar anterior se ha quitado.
  * [Solución de problemas mejorada](active-directory-aadconnectsync-implement-password-synchronization.md#troubleshooting-password-synchronization).
* No se puede conectar con Active Directory con el nivel funcional de bosque de Windows Server 2016.
* No se puede cambiar el grupo usado para el filtrado de grupo tras la instalación inicial.
* Ya no se crea un nuevo perfil de usuario en el servidor de Azure AD Connect para cada usuario al hacer un cambio de contraseña con la escritura diferida de contraseñas habilitada.
* No se pueden usar valores enteros largos en ámbitos de reglas de sincronización.
* La casilla de verificación "Reescritura de dispositivos" permanece deshabilitada si hay controladores de dominio inalcanzables.

## <a name="1086670"></a>1.0.8667.0
Fecha de publicación: agosto de 2015

**Nuevas características:**

* Ahora, el asistente para la instalación de Azure AD Connect se adapta a todos los idiomas de Windows Server.
* Se ha agregado compatibilidad con el desbloqueo de cuentas cuando se usa la administración de contraseñas de Azure AD.

**Problemas corregidos:**

* El asistente para la instalación de Azure AD Connect se bloquea si otro usuario continúa la instalación, y no la persona que la inició por primera vez.
* Si una desinstalación anterior de Azure AD Connect no desinstala limpiamente la sincronización de Azure AD Connect, no es posible volver a instalarlo.
* No se puede instalar Azure AD Connect mediante la instalación rápida si el usuario no está en el dominio raíz del bosque o si se usa una versión distinta del inglés de Active Directory.
* Si no se puede resolver el FQDN de la cuenta de usuario de Active Directory, se muestra un mensaje de error engañoso para indicar que no se pudo confirmar el esquema.
* Si se cambia la cuenta usada en el conector de Active Directory fuera del asistente, este producirá errores en ejecuciones posteriores.
* Azure AD Connect no se puede instalar en ocasiones en un controlador de dominio.
* No se puede habilitar y deshabilitar el "Modo provisional" si se han agregado atributos de extensión.
* La escritura diferida de contraseñas produce errores en alguna configuración debido a una contraseña incorrecta en el conector de Active Directory.
* No se puede actualizar la sincronización de directorios si se usa en el filtrado de atributos el nombre distintivo (DN).
* Uso excesivo de CPU al utilizar el restablecimiento de contraseña.

**Características de versión la preliminar eliminadas:**

* La característica en vista previa [Reescritura de usuarios](active-directory-aadconnect-feature-preview.md#user-writeback) se ha eliminado temporalmente a raíz de los comentarios de nuestros clientes de vista previa. Se volverá a agregar después de que se hayan examinado los comentarios proporcionados.

## <a name="1086410"></a>1.0.8641.0
Fecha de publicación: junio de 2015

**Versión inicial de Azure AD Connect.**

Ha cambiado el nombre de Azure AD Sync a Azure AD Connect.

**Nuevas características:**

* Instalación de la [configuración rápida](active-directory-aadconnect-get-started-express.md)
* Posibilidad de [configurar AD FS](active-directory-aadconnect-get-started-custom.md#configuring-federation-with-ad-fs)
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

* Ahora, Azure AD Sync requiere que se instale la versión 4.5.1 de .NET Framework.

**Problemas corregidos:**

* La escritura diferida de contraseñas de Azure AD produce errores de conectividad de Service Bus.

## <a name="104910413"></a>1.0.491.0413
Fecha de publicación: abril de 2015

**Problemas corregidos y mejoras:**

* El conector de Active Directory no procesa las eliminaciones correctamente si está habilitada la Papelera de reciclaje y hay varios dominios en el bosque.
* Se ha mejorado el rendimiento de las operaciones de importación para el conector de Azure Active Directory.
* Cuando un grupo superaba el límite de pertenencia (de forma predeterminada, el límite se establece en 50 000 objetos), el grupo se eliminaba de Azure Active Directory. Con el nuevo comportamiento, no se elimina el grupo, se produce un error y no se exportan los cambios de pertenencia.
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

* La sincronización de contraseñas respeta el atributo cloudFiltered que usa el filtrado de atributos. Los objetos filtrados ya no pertenecen al ámbito de la sincronización de contraseñas.
* En las raras ocasiones donde la topología tenía muchos controladores de dominio, la sincronización de contraseñas no funcionaba.
* Se ha habilitado en Azure AD/Intune "Servidor detenido" al importar desde el conector de Azure AD después de la administración de dispositivos.
* La unión de entidades de seguridad externas (FSP) desde varios dominios del mismo bosque produce un error de unión ambigua.

## <a name="104751202"></a>1.0.475.1202
Fecha de publicación: diciembre de 2014

**Nuevas características:**

* Ahora es posible realizar la sincronización de contraseñas con filtrado basado en atributos. Para más información, consulte el artículo sobre la [sincronización de contraseñas con filtrado](active-directory-aadconnectsync-configure-filtering.md).
* El atributo msDS-ExternalDirectoryObjectID se reescribe en Active Directory. Esta característica agrega compatibilidad para las aplicaciones de Office 365. Utiliza OAuth2 para acceder a los buzones de correo en línea y locales en una implementación híbrida de Exchange.

**Problemas de actualización corregidos:**

* Hay una versión más reciente del ayudante de inicio de sesión en el servidor.
* Se usó una ruta de acceso de instalación personalizada para instalar Azure AD Sync.
* Un criterio de combinación personalizado no válido bloquea la actualización.

**Otras correcciones:**

* Se han corregido las plantillas para Office Pro Plus.
* Se han corregido los problemas de instalación ocasionados por nombres de usuario que comienzan con un guión.
* Se ha corregido la pérdida de la configuración de sourceAnchor cuando se ejecuta al asistente para la instalación por una segunda vez.
* Se ha corregido el seguimiento ETW para la sincronización de contraseñas.

## <a name="104701023"></a>1.0.470.1023
Fecha de publicación: octubre de 2014

**Nuevas características:**

* Sincronización de contraseñas desde varias instancias de Active Directory locales a Azure AD.
* Interfaz de usuario de instalación localizada para todos los idiomas de Windows Server.

**Actualización de AADSync 1.0 GA**

Si ya tiene instalado Azure AD Sync, hay un paso adicional que debe seguir en caso de que haya cambiado alguna de las reglas de sincronización inmediata. Después de haber actualizado a la versión 1.0.470.1023, las reglas de sincronización que ha modificado se duplican. Para cada regla de sincronización modificada, haga lo siguiente:

1.  Busque la regla de sincronización modificada y anote los cambios.
* Elimine la regla de sincronización.
* Busque la nueva regla de sincronización creada por Azure AD Sync y vuelva a aplicar los cambios.

**Permisos para la cuenta de Active Directory**

Se deben conceder permisos adicionales a la cuenta de Active Directory para poder leer los hash de contraseña de Active Directory. Los permisos que se deben conceder se denominan "Replicating Directory Changes" (Replicar cambios de directorio) y "Replicating Directory Changes All" (Replicar todos los cambios de directorio). Ambos permisos son necesarios para poder leer los hash de contraseña.

## <a name="104190911"></a>1.0.419.0911
Fecha de publicación: septiembre de 2014

**Versión inicial de Azure AD Sync.**

## <a name="next-steps"></a>Pasos siguientes
Obtenga más información sobre la [Integración de las identidades locales con Azure Active Directory](active-directory-aadconnect.md).

