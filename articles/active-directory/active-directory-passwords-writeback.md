---
title: "SSPR de Azure AD con escritura diferida de contraseñas | Microsoft Docs"
description: "Uso de Azure AD y Azure AD Connect para la escritura diferida de contraseñas en el directorio local"
services: active-directory
keywords: "Administración de contraseñas de Active Directory, administración de contraseñas, autoservicio de restablecimiento de contraseña de Azure AD"
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.reviewer: gahug
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2017
ms.author: joflore
ms.custom: it-pro
ms.translationtype: HT
ms.sourcegitcommit: 9569f94d736049f8a0bb61beef0734050ecf2738
ms.openlocfilehash: e460e734973622fb0d5745adfc4c1aa0178dd22e
ms.contentlocale: es-es
ms.lasthandoff: 08/31/2017

---
# <a name="password-writeback-overview"></a>Información general sobre la escritura diferida de contraseñas

La escritura diferida de contraseñas le permite configurar Azure AD para que escriba contraseñas en diferido en Active Directory local. Esto elimina la necesidad de configurar y administrar una solución de autoservicio de restablecimiento de contraseña complicado local y ofrece una manera conveniente basada en la nube para que los usuarios restablezcan sus contraseñas locales dondequiera que estén. La escritura diferida de contraseñas es un componente de [Azure Active Directory Connect](./connect/active-directory-aadconnect.md) que los suscriptores actuales de [Azure Active Directory Premium](active-directory-editions.md) pueden habilitar y utilizar.

La escritura diferida de contraseñas ofrece las siguientes características:

* **Comentarios con retraso de cero**: la escritura diferida de contraseñas es una operación sincrónica. Si la contraseña de un usuario no cumple la directiva o no se puede restablecer o modificar por algún motivo, a dicho usuario se le envía una notificación inmediatamente.
* **Admite el restablecimiento de contraseñas de usuarios que usan AD FS u otras tecnologías de federación**: con la escritura diferida de contraseñas, siempre que las cuentas de usuario federadas estén sincronizadas en el inquilino de Azure AD, podrán administrar sus contraseñas de AD locales desde la nube.
* **Admite el restablecimiento de contraseñas de usuarios que usan la [sincronización de hash de contraseñas](./connect/active-directory-aadconnectsync-implement-password-synchronization.md)**: cuando el servicio de restablecimiento de contraseñas detecta que una cuenta de usuario sincronizada está habilitada para la sincronización de hash de contraseñas, se restablece la contraseña local y de la nube de la cuenta al mismo tiempo.
* **Admite la modificación de contraseñas desde el panel de acceso y Office 365**: si los usuarios con sincronización de contraseñas o federados modifican las contraseñas expiradas o no expiradas, se escriben tales contraseñas en diferido en el entorno local de AD.
* [Admite la escritura diferida de contraseñas si un administrador las restablece desde Azure Portal](https://portal.azure.com): cada vez que un administrador restablece una contraseña de usuario en **Azure Portal**, si ese usuario se federa o se sincroniza con contraseña, también se establece la contraseña que el administrador seleccione en el entorno local de AD. Esto actualmente no se admite en el Portal de administración de Office.
* **Implementa las directivas de contraseña de AD local**: cuando un usuario restablece su contraseña, se garantiza que cumpla la directiva del entorno local de AD antes de enviarla a ese directorio. Esto incluye el historial, la complejidad, la antigüedad, los filtros de contraseñas y otras restricciones de contraseña que ha definido en el entorno local de AD.
* **No requiere ninguna regla de firewall de entrada**: la escritura diferida de contraseñas usa Azure Service Bus Relay como un canal de comunicación subyacente, lo que significa que no tendrá que abrir puertos de entrada en el firewall para que esta característica funcione.
* **No se admite para cuentas de usuario que existen en grupos protegidos en Active Directory local**: para más información sobre grupos protegidos, vea [Cuentas protegidas y grupos de Active Directory](https://technet.microsoft.com/library/dn535499.aspx).

## <a name="how-password-writeback-works"></a>Funcionamiento de la escritura diferida de contraseñas

Si un usuario federado o con sincronización de hash de contraseña cambia o restablece su contraseña en la nube, ocurre lo siguiente:

1. Comprobamos qué tipo de contraseña tiene el usuario.
    * Si se observa que la contraseña se administra de forma local:
        * Se comprueba si el servicio de escritura diferida está en funcionamiento y, de ser así, se deja que el usuario continúe.
        * Si el servicio de escritura diferida no está activo, se informa al usuario de que la contraseña no se puede restablecer ahora.
2. A continuación, el usuario pasa las puertas de autenticación adecuadas y llega a la pantalla de restablecimiento de contraseña.
3. El usuario selecciona una nueva contraseña y la confirma.
4. Al hacer clic en enviar, se cifra la contraseña de texto no cifrado con una clave simétrica que se creó durante el proceso de configuración de la escritura diferida.
5. Después de cifrar la contraseña, se incluye en una carga que se envía a través de un canal HTTPS a Service Bus Relay específico del inquilino (que también se establece automáticamente durante el proceso de configuración de la escritura diferida). Esta retransmisión está protegida por una contraseña generada de forma aleatoria que sólo conoce la instalación local.
6. Una vez que el mensaje llega al bus de servicio, el extremo de restablecimiento de contraseña se activa automáticamente y observa que tiene una solicitud de restablecimiento pendiente.
7. A continuación, el servicio busca al usuario correspondiente mediante el atributo delimitador de la nube. Para que esta búsqueda se realice correctamente:

    * El objeto de usuario debe existir en el espacio del conector de AD.
    * El objeto de usuario debe estar vinculado al objeto MV correspondiente.
    * El objeto de usuario debe estar vinculado al objeto del conector de AAD correspondiente.
    * El vínculo del objeto del conector de AD al objeto MV debe tener la regla de sincronización `Microsoft.InfromADUserAccountEnabled.xxx` en el vínculo. <br> <br>
    Si la llamada procede de la nube, el motor de sincronización utiliza el atributo cloudAnchor para buscar el objeto del espacio del conector de AAD, sigue el vínculo al objeto MV y, después, sigue el vínculo al objeto de AD. Dado que podría haber varios objetos de AD (bosque múltiple) para el mismo usuario, el motor de sincronización se basa en el `Microsoft.InfromADUserAccountEnabled.xxx` vínculo para seleccionar el correcto.

    > [!Note]
    > Como resultado de esta lógica, Azure AD Connect debe poder comunicarse con el emulador de PDC para que la escritura diferida de contraseñas funcione. Si necesita habilitarlo manualmente, puede conectar Azure AD Connect al emulador de PDC; para ello, haga clic con el botón derecho en las **propiedades** del conector de sincronización de Active Directory y, luego, seleccione **configure directory partitions** (configurar particiones del directorio). Desde allí, busque la sección de **configuración de conexión de controlador de dominio** y active la casilla titulada **only use preferred domain controllers** (Solo utilizar los controladores de dominio preferidos). Si el controlador de dominio preferido no es un emulador de PDC, Azure AD Connect intentará conectarse al PDC para la escritura diferida de contraseñas.

8. Una vez que se encuentra la cuenta de usuario, se intenta restablecer la contraseña directamente en el bosque de AD correspondiente.
9. Si la operación de establecimiento de la contraseña se realiza correctamente, se notifica al usuario que la contraseña se ha modificado.
    > [!NOTE]
    > Si la contraseña del usuario se sincroniza con Azure AD mediante la sincronización de contraseñas, existe la posibilidad de que la directiva de contraseñas local sea menos segura que la directiva de contraseñas en la nube. En este caso, se recomienda que, sea cual sea la directiva local, se permita que la sincronización de hash de contraseña sincronice el hash de dicha contraseña. Esto garantiza que la directiva local se aplica en la nube, sin importar si utiliza la sincronización de contraseñas o la federación para proporcionar el inicio de sesión único.

10. Si se produce un error en la operación de establecimiento de la contraseña, se devolverá el error al usuario y se le permitirá que vuelva a intentarlo.
    * La operación puede producir un error debido a lo siguiente:
        * El servicio estaba inactivo.
        * La contraseña seleccionada no cumple las directivas de la organización.
        * No se pudo encontrar el usuario en AD local.

    Disponemos de un mensaje específico para muchos de estos casos, a fin de indicar al usuario qué puede hacer para resolver el problema.

## <a name="configuring-password-writeback"></a>Configuración de la escritura diferida de contraseñas

Se recomienda que use la característica de actualización automática de [Azure AD Connect](./connect/active-directory-aadconnect-get-started-express.md) si desea utilizar la escritura diferida de contraseñas.

DirSync y Sincronización de Azure AD ya no son formas compatibles de habilitar la escritura diferida de contraseñas; el artículo sobre la [actualización desde DirSync y Sincronización de Azure AD](connect/active-directory-aadconnect-dirsync-deprecated.md) proporciona más información para ayudar con la transición.

En los pasos siguientes se da por supuesto que ya ha configurado Azure AD Connect en su entorno con la configuración [Rápida](./connect/active-directory-aadconnect-get-started-express.md) o [Personalizada](./connect/active-directory-aadconnect-get-started-custom.md).

1. Para configurar y habilitar la escritura diferida de contraseñas, inicie sesión en su servidor de Azure AD Connect e inicie el asistente para configuración de **Azure AD Connect**.
2. En la pantalla de bienvenida, haga clic en **Configurar**.
3. En la pantalla Tareas adicionales, haga clic en **Personalizar las opciones de sincronización** y elija **Siguiente**.
4. En la pantalla Conectar con Azure AD, escriba una credencial de administrador global y elija **Siguiente**.
5. En las pantallas Conectar sus directorios y Filtrado de dominios y unidades organizativas, puede elegir **Siguiente**.
6. En la pantalla Características opcionales, active la casilla junto a **Escritura diferida de contraseñas** y haga clic en **Siguiente**.
   ![Habilitar la escritura diferida de contraseñas en Azure AD Connect][Writeback]
7. En la pantalla Listo para configurar, haga clic en **Configurar** y espere a que se complete el proceso.
8. Cuando aparezca Configuración completa, puede hacer clic en **Salir**.

## <a name="licensing-requirements-for-password-writeback"></a>Requisitos de licencia para la escritura diferida de contraseñas

Para obtener información sobre licencias, vea el tema [Licencias necesarias para la escritura diferida de contraseñas](active-directory-passwords-licensing.md#licenses-required-for-password-writeback) o visite los sitios siguientes:

* [Sitio sobre precios de Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/)
* [Enterprise Mobility + Security](https://www.microsoft.com/cloud-platform/enterprise-mobility-security)
* [Secure Productive Enterprise](https://www.microsoft.com/secure-productive-enterprise/default.aspx)

### <a name="on-premises-authentication-modes-supported-for-password-writeback"></a>Modos de autenticación local admitidos para la escritura diferida de contraseñas

La escritura diferida de contraseñas funciona para los siguientes tipos de contraseña de usuario:

* **Usuarios solo en la nube**: La escritura diferida de contraseñas no se aplica en este caso, porque no hay contraseña local
* **Usuarios sincronizados con contraseña**: se admite la escritura diferida de contraseñas
* **Usuarios federados**: Se admite la escritura diferida de contraseñas
* **Usuarios de autenticación de paso a través**: Se admite la escritura diferida de contraseñas

### <a name="user-and-admin-operations-supported-for-password-writeback"></a>Operaciones de usuario y administrador admitidas para la escritura diferida de contraseñas

Las contraseñas se escriben en diferido en todas las situaciones siguientes:

* **Operaciones de usuario final admitidas**
  * Cualquier operación de cambio de contraseña voluntaria de autoservicio del usuario final
  * Cualquier operación de cambio de contraseña exigida de autoservicio del usuario final (por ejemplo, la expiración de contraseña)
  * Cualquier restablecimiento de contraseña de autoservicio del usuario final que se origina en el [portal de restablecimiento de contraseña](https://passwordreset.microsoftonline.com)
* **Operaciones de administrador admitidas**
  * Cualquier operación de cambio de contraseña voluntaria de autoservicio del administrador
  * Cualquier operación de cambio de contraseña exigida de autoservicio del administrador (por ejemplo, la expiración de contraseña)
  * Cualquier restablecimiento de contraseña de autoservicio del administrador que se origina en el [portal de restablecimiento de contraseña](https://passwordreset.microsoftonline.com)
  * Cualquier restablecimiento de contraseña del usuario final iniciado por el administrador desde el [Portal de Azure clásico](https://manage.windowsazure.com)
  * Cualquier restablecimiento de contraseña del usuario final iniciado por el administrador desde [Azure Portal](https://portal.azure.com)

### <a name="user-and-admin-operations-not-supported-for-password-writeback"></a>Operaciones de usuario y administrador no admitidas para la escritura diferida de contraseñas

Las contraseñas **no** se escriben en diferido en ninguna de las situaciones siguientes:

* **Operaciones de usuario final no admitidas**
  * Cualquier usuario final que restablece su propia contraseña mediante PowerShell v1, v2 o la API Graph de Azure AD
* **Operaciones de administrador no admitidas**
  * Cualquier restablecimiento de contraseña del usuario final iniciado por el administrador desde el [Portal de administración de Office](https://portal.office.com)
  * Cualquier restablecimiento de contraseña del usuario final iniciado por el administrador desde PowerShell v1, v2 o la API Graph de Azure AD

Aunque estamos trabajando para quitar estas limitaciones, todavía no tenemos una escala de tiempo concreta que podemos compartir.

## <a name="password-writeback-security-model"></a>Modelo de seguridad de la escritura diferida de contraseñas

La escritura diferida de contraseñas es un servicio muy seguro.  Para asegurarse de que su información está protegida, se habilita un modelo de seguridad de cuatro niveles que se describe a continuación.

* **Service Bus Relay específico del inquilino**
  * Al configurar el servicio, se configura una instancia de Service Bus Relay específica del inquilino que está protegida por una contraseña segura generada aleatoriamente a la que Microsoft nunca tiene acceso.
* **Clave de cifrado de contraseñas bloqueadas y criptográficamente segura**
  * Una vez creada la instancia de Service Bus Relay, se crea una clave simétrica segura que se utiliza para cifrar la contraseña tal como se transfiere a través de la red. Esta clave reside solo en el almacén secreto de la compañía en la nube, que se bloquea y audita de forma estricta como cualquier contraseña en el directorio.
* **TLS estándar del sector**
  1. Cuando se produce una operación de restablecimiento o cambio de contraseña, se cifra la contraseña de texto no cifrado con la clave pública.
  2. A continuación, se inserta en un mensaje HTTPS que se envía a través de un canal cifrado con certificados SSL de Microsoft a Service Bus Relay.
  3. Una vez que el mensaje llega a Service Bus, el agente local se activa y se autentica en Service Bus con la contraseña segura que se generó anteriormente.
  4. El agente local selecciona el mensaje cifrado y lo descifra con la clave privada que se ha generado.
  5. El agente local intenta definir la contraseña con SetPassword API de AD DS.
     * Este paso permite aplicar la directiva de contraseñas local de AD (complejidad, edad, historial, filtros, etc.) en la nube.
* **Directivas de expiración de mensajes** 
  * Si el mensaje se queda en Service Bus porque el servicio local está inactivo, agotará el tiempo de espera y se eliminará después de varios minutos para aumentar la seguridad aún más.

### <a name="password-writeback-encryption-details"></a>Detalles de cifrado de la escritura diferida de contraseñas

A continuación se describen los pasos de cifrado que se llevan a cabo para una solicitud de restablecimiento de contraseña después de que un usuario la envía, pero antes de que llegue a su entorno local, para garantizar la seguridad y confiabilidad máximas del servicio.

* **Paso 1: Cifrado de contraseña con la clave RSA de 2048 bits**: Una vez que un usuario envía una contraseña para que se escriba en diferido en local, primero se cifra la propia contraseña enviada con una clave RSA de 2048 bits.
* **Paso 2: Cifrado a nivel de paquete con AES-GCM**: A continuación, todo el paquete (contraseña + metadatos necesarios) se cifra mediante AES-GCM. Esto evita que cualquier persona con acceso directo al canal de ServiceBus subyacente vea o manipula el contenido.
* **Paso 3: Toda la comunicación se realiza a través de TLS/SSL**: toda la comunicación con ServiceBus tiene lugar en un canal SSL/TLS. Esto protege el contenido de terceras personas no autorizadas.
* **Sustitución de clave automática cada seis meses**: cada 6 meses y de forma automática, o cada vez que la escritura diferida de contraseñas se deshabilita o se vuelve a habilitar en Azure AD Connect, se sustituyen todas estas claves para garantizar la máxima seguridad del servicio.

### <a name="password-writeback-bandwidth-usage"></a>Uso de ancho de banda de la escritura diferida de contraseñas

La escritura diferida de contraseñas es un servicio de bajo consumo de ancho de banda que envía solicitudes al agente local solo en las siguientes circunstancias:

1. Se enviaron dos mensajes al habilitar o deshabilitar la característica a través de Azure AD Connect.
2. Se envía un mensaje cada cinco minutos como un latido del servicio siempre que el servicio está en ejecución.
3. Se envían dos mensajes cada vez que se envía una nueva contraseña.
    * El primer mensaje, como una solicitud para realizar la operación.
    * El segundo mensaje, que contiene el resultado de la operación y se envía en las siguientes circunstancias:
        * Cada vez que se envía una nueva contraseña durante un restablecimiento de la contraseña de autoservicio de un usuario.
        * Cada vez que se envía una nueva contraseña durante una operación de cambio de la contraseña de un usuario.
        * Cada vez que se envía una nueva contraseña durante un restablecimiento de contraseña de usuario iniciado por el administrador (solo desde los portales de administración de Azure).

#### <a name="message-size-and-bandwidth-considerations"></a>Consideraciones sobre el ancho de banda y el tamaño de mensaje

El tamaño de cada uno de los mensajes que se ha descrito anteriormente está normalmente por debajo de 1 kb, incluso bajo cargas extremas, el propio servicio de escritura diferida de contraseñas utiliza solo unos kilobits por segundo como máximo del ancho de banda. Puesto que cada mensaje se envía en tiempo real, solo cuando es necesario debido a una operación de actualización de contraseña, y dado que el tamaño del mensaje es tan pequeño, el uso de ancho de banda de la funcionalidad de escritura diferida es demasiado pequeño para tener ningún impacto cuantificable real.

## <a name="next-steps"></a>Pasos siguientes

Los vínculos siguientes proporcionan información adicional sobre el restablecimiento de contraseñas con Azure AD:

* [**Inicio rápido**](active-directory-passwords-getting-started.md): preparativos para el autoservicio de administración de contraseñas de Azure AD 
* [**Licencias**](active-directory-passwords-licensing.md): configuración de licencias de Azure AD
* [**Datos**](active-directory-passwords-data.md): información sobre los datos necesarios y cómo se usan para administrar contraseñas
* [**Implementación**](active-directory-passwords-best-practices.md): planee e implemente SSPR en sus usuarios mediante las instrucciones que se encuentran aquí.
* [**Personalización**](active-directory-passwords-customize.md): personalice el aspecto de la experiencia SSPR para su empresa.
* [**Directiva**](active-directory-passwords-policy.md): información sobre las directivas de contraseñas de Azure AD y cómo establecerlas
* [**Informes**](active-directory-passwords-reporting.md): informes para detectar si los usuarios acceden a la funcionalidad de SSPR que especifican el momento y el lugar del acceso
* [**Profundización técnica**](active-directory-passwords-how-it-works.md): conozca lo que hay detrás para comprender cómo funciona.
* [**Preguntas más frecuentes**](active-directory-passwords-faq.md): ¿Cómo? ¿Por qué? ¿Qué? ¿Dónde? ¿Quién? ¿Cuándo? : respuestas a las preguntas que siempre se ha hecho.
* [**Solución de problemas**](active-directory-passwords-troubleshoot.md): información para resolver problemas habituales de SSPR

[Writeback]: ./media/active-directory-passwords-writeback/enablepasswordwriteback.png "Habilitar la escritura diferida de contraseñas en Azure AD Connect"

