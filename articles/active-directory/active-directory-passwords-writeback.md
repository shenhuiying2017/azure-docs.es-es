---
title: "SSPR de Azure AD con escritura diferida de contraseñas | Microsoft Docs"
description: "Use Azure AD y Azure AD Connect para realizar la escritura diferida de contraseñas en un directorio local."
services: active-directory
keywords: "Administración de contraseñas de Active Directory, administración de contraseñas, autoservicio de restablecimiento de contraseña de Azure AD"
documentationcenter: 
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: 
ms.devlang: 
ms.topic: article
ms.date: 01/11/2018
ms.author: joflore
ms.custom: it-pro
ms.openlocfilehash: b4a14d3c79f93988eeac1525da09cf70dc2de634
ms.sourcegitcommit: 562a537ed9b96c9116c504738414e5d8c0fd53b1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/12/2018
---
# <a name="password-writeback-overview"></a>Información general sobre la escritura diferida de contraseñas

Gracias a la escritura diferida de contraseñas, podrá configurar Azure Active Directory (Azure AD) para que escriba contraseñas en diferido en la instancia local de Active Directory. Esta opción elimina la necesidad de configurar y administrar una complicada solución local de autoservicio para el restablecimiento de contraseñas (SSPR) y le ofrece una cómoda manera basada en la nube para que los usuarios restablezcan sus contraseñas locales dondequiera que estén. La escritura diferida de contraseñas es un componente de [Azure Active Directory Connect](./connect/active-directory-aadconnect.md) que los suscriptores actuales de [Azure Active Directory Premium](active-directory-whatis.md) pueden habilitar y utilizar.

La escritura diferida de contraseñas ofrece las siguientes características:

* **Proporciona comentarios con retraso cero**: la escritura diferida de contraseñas es una operación sincrónica. Si la contraseña de un usuario no cumple la directiva o no se puede restablecer o modificar por algún motivo, a dicho usuario se le envía una notificación inmediatamente.
* **Admite el restablecimiento de contraseñas de usuarios que usan Servicios de federación de Active Directory (AD FS) u otras tecnologías de federación**: con la escritura diferida de contraseñas, siempre que las cuentas de usuario federadas estén sincronizadas en el inquilino de Azure AD, se podrán administrar las contraseñas de instancias locales de Active Directory desde la nube.
* **Admite el restablecimiento de contraseñas de usuarios que usan la**  [sincronización de hash de contraseñas](./connect/active-directory-aadconnectsync-implement-password-synchronization.md): cuando el servicio de restablecimiento de contraseñas detecta que una cuenta de usuario sincronizada está habilitada para la sincronización de hash de contraseñas, se restablece la contraseña local y de la nube de la cuenta al mismo tiempo.
* **Admite la modificación de contraseñas desde el panel de acceso y Office 365**: si los usuarios con sincronización de contraseñas o federados modifican las contraseñas expiradas o no expiradas, se escriben tales contraseñas en diferido en el entorno local de Active Directory.
* [Admite la escritura diferida de contraseñas si un administrador las restablece desde Azure Portal](https://portal.azure.com): cada vez que un administrador restablece una contraseña de usuario en **Azure Portal**, si ese usuario se federa o se sincroniza con contraseña, también se establece la contraseña que el administrador seleccione en el entorno local de Active Directory. Esta funcionalidad no se admite en el portal de administración de Office.
* **Aplica las directivas de contraseña de la instancia local de Active Directory**: cuando un usuario restablece su contraseña, se garantiza que cumpla la directiva de la instancia local de Active Directory antes de enviarla a ese directorio. En esta revisión se incluye la comprobación del historial, la complejidad, la antigüedad, los filtros de contraseñas y otras restricciones de contraseña que ha definido en el entorno local de Active Directory.
* **No requiere ninguna regla de firewall entrante**: la escritura diferida de contraseñas usa Azure Service Bus Relay como canal de comunicación subyacente. No tendrá que abrir ningún puerto de entrada en el firewall para que esta característica funcione.
* **No se admite en cuentas de usuario que existan en grupos protegidos en una instancia local de Active Directory**: para obtener más información sobre grupos protegidos, consulte [Protected accounts and groups in Active Directory](https://technet.microsoft.com/library/dn535499.aspx) (Cuentas protegidas y grupos de Active Directory).

## <a name="how-password-writeback-works"></a>Funcionamiento de la escritura diferida de contraseñas

Si un usuario federado o con sincronización de hash de contraseña cambia o restablece su contraseña en la nube, ocurre lo siguiente:

1. Comprobamos qué tipo de contraseña tiene el usuario. Si se observa que la contraseña se administra de forma local:
   * Se comprueba si el servicio de escritura diferida está en funcionamiento. De ser así, se deja que el usuario continúe.
   * Si el servicio de escritura diferida no está activo, se informa al usuario de que la contraseña no se puede restablecer ahora.
2. A continuación, el usuario realiza los pasos de autenticación adecuados y llega a la página **Restablecer contraseña**.
3. El usuario selecciona una nueva contraseña y la confirma.
4. Al hacer clic en **Enviar**, se cifra la contraseña de texto no cifrado con una clave simétrica que se creó durante el proceso de configuración de la escritura diferida.
5. Después de cifrar la contraseña, se incluye en una carga que se envía a través de un canal HTTPS a Service Bus Relay específico del inquilino (que también se establece automáticamente durante el proceso de configuración de la escritura diferida). Esta retransmisión está protegida por una contraseña generada de forma aleatoria que sólo conoce la instalación local.
6. Una vez que el mensaje llega a Service Bus, el punto de conexión de restablecimiento de contraseña se activa automáticamente y comprueba que tiene una solicitud de restablecimiento pendiente.
7. A continuación, el servicio busca al usuario mediante el atributo delimitador de la nube. Para que esta búsqueda se realice correctamente:

    * El objeto de usuario debe existir en el espacio del conector de Active Directory.
    * El objeto de usuario debe estar vinculado al objeto de metaverso (MV) correspondiente.
    * El objeto de usuario debe estar vinculado al objeto de conector de Azure Active Directory correspondiente.
    * El vínculo del objeto de conector de Active Directory que lleva al objeto de MV debe tener la regla de sincronización `Microsoft.InfromADUserAccountEnabled.xxx` en ese vínculo. <br> <br>
    Cuando se recibe la llamada de la nube, el motor de sincronización usa el atributo **cloudAnchor** para buscar el objeto de espacio de conector de Azure Active Directory. A continuación, sigue el vínculo de vuelta al objeto de MV para volver a seguir el vínculo al objeto de Active Directory. Dado que podría haber varios objetos de Active Directory (bosque múltiple) para el mismo usuario, el motor de sincronización se basa en el vínculo `Microsoft.InfromADUserAccountEnabled.xxx` para seleccionar el objeto correcto.

    > [!Note]
    > Como resultado de esta lógica, para que la escritura diferida de contraseñas funcione en Azure AD Connect, debe poder comunicarse con el emulador del controlador de dominio principal (PDC). Si necesita habilitar esta opción manualmente, puede conectar Azure AD Connect al emulador de PDC. Haga clic con el botón derecho en las **propiedades** del conector de sincronización de Active Directory y, a continuación, seleccione **configure directory partitions** (configurar particiones del directorio). Desde allí, busque la sección **domain controller connection settings** (configuración de conexión del controlador de dominio) y active la casilla titulada **only use preferred domain controllers** (solo usar los controladores de dominio preferidos). Incluso si el controlador de dominio preferido no es un emulador de PDC, Azure AD Connect intentará conectarse al PDC para realizar la escritura diferida de contraseñas.

8. Una vez que se encuentra la cuenta de usuario, se intenta restablecer la contraseña directamente en el bosque de Active Directory correspondiente.
9. Si la operación de establecimiento de la contraseña se realiza correctamente, se notifica al usuario que la contraseña se ha modificado.
    > [!NOTE]
    > Si la contraseña del usuario se sincroniza con Azure AD mediante la sincronización de contraseñas, existe la posibilidad de que la directiva de contraseñas local sea menos segura que la directiva de contraseñas en la nube. En este caso, se recomienda que, sea cual sea la directiva local, se use la sincronización de hash de contraseñas para sincronizar el hash de dicha contraseña. Esto garantiza que la directiva local se aplique en la nube, sin importar si usa la sincronización de contraseñas o la federación para proporcionar el inicio de sesión único.

10. Si se produce un error en la operación de establecimiento de la contraseña, se devolverá el error al usuario y se permitirá que vuelva a intentarlo. La operación puede producir un error debido a lo siguiente:
    * El servicio estaba inactivo.
    * La contraseña seleccionada no cumple las directivas de la organización.
    * Es posible que no se encuentre al usuario en la instancia local de Active Directory.

    Disponemos de un mensaje específico para muchos de estos casos, a fin de indicar al usuario qué puede hacer para resolver el problema.

## <a name="configure-password-writeback"></a>Configurar la escritura diferida de contraseñas

Se recomienda que use la característica de actualización automática de [Azure AD Connect](./connect/active-directory-aadconnect-get-started-express.md) si desea utilizar la escritura diferida de contraseñas.

Ya no se admiten DirSync y Azure AD Sync como métodos para habilitar la escritura diferida de contraseñas. Para obtener más información acerca de cómo realizar esta transición, consulte [Actualización de la sincronización de Microsoft Azure Active Directory ("DirSync") y la sincronización de Azure Active Directory ("Azure AD Sync")](connect/active-directory-aadconnect-dirsync-deprecated.md).

En los pasos siguientes se da por supuesto que ya ha configurado Azure AD Connect en su entorno mediante la configuración [Rápida](./connect/active-directory-aadconnect-get-started-express.md) o [Personalizada](./connect/active-directory-aadconnect-get-started-custom.md).

1. Para configurar y habilitar la escritura diferida de contraseñas, inicie sesión en su servidor de Azure AD Connect e inicie el asistente de configuración de **Azure AD Connect**.
2. En la página **principal**, seleccione **Configurar**.
3. En la página **Tareas adicionales**, seleccione **Personalizar las opciones de sincronización** y haga clic en **Siguiente**.
4. En la página **Conectar con Azure AD**, escriba una credencial de administrador global y elija **Siguiente**.
5. En las páginas de filtrado **Conectar directorios** y **Dominio/Unidad organizativa**, seleccione **Siguiente**.
6. En la página **Características opcionales**, active la casilla junto a **Escritura diferida de contraseñas** y haga clic en **Siguiente**.
   ![Habilitar la escritura diferida de contraseñas en Azure AD Connect][Writeback]
7. En la página **Listo para configurar**, haga clic en **Configurar** y espere a que se complete el proceso.
8. Cuando finalice la configuración, seleccione **Salir**.

Para tareas de solución de problemas comunes relacionadas con la escritura diferida de contraseñas, consulte la sección [Solución de problemas con la escritura diferida de contraseñas](active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback) en nuestro artículo de solución de problemas.

## <a name="active-directory-permissions"></a>Permisos de Active Directory

La cuenta especificada en la utilidad de Azure AD Connect debe tener establecidos los siguientes elementos si quiere estar en el ámbito de SSPR:

* **Restablecimiento de contraseñas** 
* Haga clic en **Cambiar contraseña**. 
* **Permisos de escritura** en `lockoutTime`  
* **Permisos de escritura** en `pwdLastSet`
* **Permisos extendidos** en:
   * El objeto raíz de *cada dominio* de ese bosque.
   * Las unidades organizativas (OU) de usuario que quiera que estén en el ámbito de SSPR.

Si no está seguro de cuál es la cuenta a la que se hace referencia en la descripción, abra la interfaz de usuario de la configuración de Azure Active Directory Connect y seleccione la opción **Ver la configuración actual**. La cuenta a la que necesita agregar permisos se enumera en **Directorios sincronizados**.

Si establece estos permisos, la cuenta de servicio de agente de administración de cada bosque podrá administrar las contraseñas en nombre de las cuentas de usuario de dicho bosque. 

> [!IMPORTANT]
> Si no asigna estos permisos, aunque la escritura diferida parezca estar configurada correctamente, los usuarios encontrarán errores al intentar administrar sus contraseñas locales desde la nube.
>

> [!NOTE]
> Estos permisos pueden tardar una hora (o más) en replicarse en todos los objetos del directorio.
>

Para configurar los permisos adecuados para que se realice la escritura diferida de contraseñas, complete los pasos siguientes:

1. Abra "Usuarios y equipos de Active Directory" con una cuenta que tenga los permisos de administración de dominio adecuados.
2. En el menú **Ver**, asegúrese de que la opción **Características avanzadas** está activada.
3. En el panel izquierdo, haga clic con el botón derecho en el objeto que representa la raíz del dominio y elija **Propiedades** > **Seguridad** > **Avanzado**.
4. En la pestaña **Permisos**, haga clic en **Agregar**.
5. Elija la cuenta a la que se van a aplicar los permisos (en el programa de instalación de Azure AD Connect).
6. En la lista desplegable **Se aplica a**, seleccione **Descendent User objects** (Objetos de usuario descendiente).
7. En **Permisos**, active las casillas para los siguientes elementos:
    * **Restablecimiento de contraseñas**
    * Haga clic en **Cambiar contraseña**.
    * **Escribir lockoutTime**
    * **Escribir pwdLastSet**
8. Haga clic en **Aplicar o Aceptar** para aplicar los cambios y salir de los cuadros de diálogo abiertos.

## <a name="licensing-requirements-for-password-writeback"></a>Requisitos de licencia para la escritura diferida de contraseñas

Para obtener más información sobre las licencias, consulte la sección [Licencias necesarias para la escritura diferida de contraseñas](active-directory-passwords-licensing.md#licenses-required-for-password-writeback) o visite los sitios siguientes:

* [Sitio sobre precios de Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/)
* [Enterprise Mobility + Security](https://www.microsoft.com/cloud-platform/enterprise-mobility-security)
* [Microsoft 365 Enterprise](https://www.microsoft.com/secure-productive-enterprise/default.aspx)

### <a name="on-premises-authentication-modes-that-are-supported-for-password-writeback"></a>Modos de autenticación local admitidos en la escritura diferida de contraseñas

La escritura diferida de contraseñas es compatible con los siguientes tipos de contraseña de usuario:

* **Usuarios que solo usan la nube**: la escritura diferida de contraseñas no se aplica en este caso, porque no hay contraseña local.
* **Usuarios sincronizados con contraseña**: se admite la escritura diferida de contraseñas.
* **Usuarios federados**: se admite la escritura diferida de contraseñas.
* **Usuarios de autenticación de paso a través**: se admite la escritura diferida de contraseñas.

### <a name="user-and-admin-operations-that-are-supported-for-password-writeback"></a>Operaciones de usuario y administrador admitidas en la escritura diferida de contraseñas

Las contraseñas se escriben en diferido en todas las situaciones siguientes:

* **Operaciones de usuario final admitidas**
  * Cualquier operación de cambio de contraseña voluntaria de autoservicio del usuario final
  * Cualquier operación exigida de cambio de contraseña de autoservicio del usuario final (por ejemplo, la expiración de contraseña)
  * Cualquier restablecimiento de contraseña de autoservicio del usuario final que se origina en el [portal de restablecimiento de contraseñas](https://passwordreset.microsoftonline.com)
* **Operaciones de administrador admitidas**
  * Cualquier operación de cambio de contraseña voluntaria de autoservicio del administrador
  * Cualquier operación exigida de cambio de contraseña de autoservicio del administrador (por ejemplo, la expiración de contraseña)
  * Cualquier restablecimiento de contraseña de autoservicio del administrador que se origina en el [portal de restablecimiento de contraseñas](https://passwordreset.microsoftonline.com)
  * Cualquier restablecimiento de contraseña del usuario final iniciado por el administrador desde [Azure Portal](https://portal.azure.com)

### <a name="user-and-admin-operations-that-are-not-supported-for-password-writeback"></a>Operaciones de usuario y administrador que no se admiten en la escritura diferida de contraseñas

Las contraseñas *no* se escriben en diferido en ninguna de las situaciones siguientes:

* **Operaciones de usuario final no admitidas**
  * Cualquier usuario final que restablezca su propia contraseña mediante PowerShell v1, v2 o la API Graph de Azure AD
* **Operaciones de administrador no admitidas**
  * Cualquier restablecimiento de contraseña del usuario final iniciado por el administrador desde el [Portal de administración de Office](https://portal.office.com)
  * Cualquier restablecimiento de contraseña del usuario final que inicie el administrador desde PowerShell v1, v2 o la API Graph de Azure AD

Aunque estamos trabajando para quitar estas limitaciones, todavía no hay una fecha concreta para ello.

## <a name="password-writeback-security-model"></a>Modelo de seguridad de la escritura diferida de contraseñas

La escritura diferida de contraseñas es un servicio muy seguro. Para garantizar que su información esté protegida, se habilita un modelo de seguridad de cuatro niveles que se describe a continuación:

* **Service Bus Relay específico del inquilino**
  * Al configurar el servicio, se configura una instancia de Service Bus Relay específica del inquilino que está protegida por una contraseña segura generada aleatoriamente y a la que Microsoft nunca tiene acceso.
* **Clave de cifrado de contraseñas bloqueadas y criptográficamente segura**
  * Una vez creada la instancia de Service Bus Relay, se crea una clave simétrica segura que se usa para cifrar la contraseña tal cual llega de la red. Esta clave solo reside en el almacén secreto en la nube de la compañía, el cual se bloquea y audita de forma estricta como se haría con cualquier contraseña del directorio.
* **Seguridad de la capa de transporte (TLS) estándar del sector**
  1. Cuando se produce una operación de restablecimiento o cambio de contraseña, se cifra la contraseña de texto no cifrado con la clave pública.
  2. A continuación, se inserta en un mensaje HTTPS que se envía a través de un canal cifrado con certificados SSL de Microsoft a Service Bus Relay.
  3. Una vez que el mensaje llega a Service Bus, el agente local se activa y se autentica en Service Bus con la contraseña segura que se generó anteriormente.
  4. El agente local selecciona el mensaje cifrado y lo descifra con la clave privada que se generó.
  5. El agente local intenta definir la contraseña con SetPassword API de AD DS. Este paso permite aplicar la directiva de contraseñas local de Active Directory (como, por ejemplo, complejidad, antigüedad, historial y filtros) en la nube.
* **Directivas de expiración de mensajes** 
  * Si el mensaje se queda en Service Bus porque el servicio local está inactivo, se agotará el tiempo de espera y se eliminará después de varios minutos. El tiempo de espera y la eliminación del mensaje aumenta aún más la seguridad.

### <a name="password-writeback-encryption-details"></a>Detalles de cifrado de la escritura diferida de contraseñas

Después de que un usuario envíe un restablecimiento de contraseña, la solicitud de restablecimiento pasa por varias fases de cifrado antes de que llegue al entorno local. Estos pasos de cifrado garantizan una seguridad y confiabilidad máximas del servicio. A continuación se detalla la descripción de estos pasos:

* **Paso 1: cifrado de contraseñas con la clave RSA de 2048 bits**: cuando el usuario envía una contraseña para que se escriba en diferido en el entorno local, se cifra la propia contraseña enviada con una clave RSA de 2048 bits.
* **Paso 2: cifrado a nivel de paquete con AES-GCM**: todo el paquete (la contraseña y los metadatos necesarios) se cifra mediante AES-GCM. Este cifrado evita que cualquier persona con acceso directo al canal de Service Bus subyacente vea o manipule el contenido.
* **Paso 3: toda comunicación se realiza a través de TLS/SSL**: toda comunicación con Service Bus tiene lugar en un canal SSL/TLS. Este cifrado protege el contenido de terceras personas no autorizadas.
* **Sustitución de clave automática cada seis meses**: cada seis meses o cada vez que se deshabilita y se vuelve a habilitar la escritura diferida de contraseñas en Azure AD Connect. Se sustituyen automáticamente todas las claves para garantizar la máxima seguridad del servicio.

### <a name="password-writeback-bandwidth-usage"></a>Uso de ancho de banda de la escritura diferida de contraseñas

La escritura diferida de contraseñas es un servicio de bajo consumo de ancho de banda que solo envía solicitudes al agente local si se dan las siguientes circunstancias:

* Se envían dos mensajes al habilitar o deshabilitar la característica a través de Azure AD Connect.
* Se envía un mensaje cada cinco minutos como un latido del servicio siempre que el servicio está en ejecución.
* Se envían dos mensajes cada vez que se envía una nueva contraseña:
    * El primer mensaje es una solicitud para realizar la operación.
    * El segundo mensaje, que contiene el resultado de la operación, se envía en las siguientes circunstancias:
        * Cada vez que se envía una nueva contraseña durante un restablecimiento de la contraseña de autoservicio de un usuario.
        * Cada vez que se envía una nueva contraseña durante una operación de cambio de la contraseña de un usuario.
        * Cada vez que se envía una nueva contraseña durante el restablecimiento de contraseña de usuario que inició el administrador (solo desde los portales de administración de Azure).

#### <a name="message-size-and-bandwidth-considerations"></a>Consideraciones sobre el ancho de banda y el tamaño de mensaje

El tamaño de cada uno de los mensajes que se ha descrito anteriormente suele ser inferior a 1 KB. Incluso bajo cargas extremas, el propio servicio de escritura diferida de contraseñas solo usa como máximo unos kilobits por segundo del ancho de banda. Puesto que cada mensaje se envía en tiempo real solo cuando lo requiere una operación de actualización de contraseña, y dado que el tamaño del mensaje es tan pequeño, el uso de ancho de banda de la funcionalidad de escritura diferida es demasiado pequeño para tener ningún impacto cuantificable real.

## <a name="next-steps"></a>pasos siguientes

* [¿Cómo se realiza un lanzamiento correcto de SSPR?](active-directory-passwords-best-practices.md)
* [Restablecimiento o modificación de la contraseña](active-directory-passwords-update-your-own-password.md).
* [Registro para el autoservicio de restablecimiento de contraseñas](active-directory-passwords-reset-register.md).
* [¿Tiene alguna pregunta acerca de las licencias?](active-directory-passwords-licensing.md)
* [¿Qué datos usa SSPR y cuáles se deben rellenar en lugar de los usuarios?](active-directory-passwords-data.md)
* [¿Qué métodos de autenticación están disponibles para los usuarios?](active-directory-passwords-how-it-works.md#authentication-methods)
* [¿Cuáles son las opciones de directiva con SSPR?](active-directory-passwords-policy.md)
* [¿Cómo se informa sobre la actividad de SSPR?](active-directory-passwords-reporting.md)
* [¿Cuáles son todas las opciones en SSPR y qué significan?](active-directory-passwords-how-it-works.md)
* [Creo que algo se ha roto. ¿Cómo se solucionan problemas en SSPR?](active-directory-passwords-troubleshoot.md)
* [Tengo una pregunta que no se ha comentado en ningún otro sitio](active-directory-passwords-faq.md)

[Writeback]: ./media/active-directory-passwords-writeback/enablepasswordwriteback.png "Habilitar la escritura diferida de contraseñas en Azure AD Connect"
