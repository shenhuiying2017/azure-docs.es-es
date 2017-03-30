---
title: "Más información: administración de contraseñas de Azure Active Directory | Microsoft Docs"
description: "Temas avanzados sobre administración de contraseñas de Azure AD, entre otros, el funcionamiento de la escritura diferida de contraseñas, la seguridad de la escritura diferida de contraseñas, el funcionamiento del portal de restablecimiento de contraseñas y los datos que sirven para el restablecimiento de contraseñas."
services: active-directory
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
editor: curtand
ms.assetid: d3be2912-76c8-40a0-9507-b7b1a7ce2f8f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/28/2017
ms.author: joflore
translationtype: Human Translation
ms.sourcegitcommit: 07635b0eb4650f0c30898ea1600697dacb33477c
ms.openlocfilehash: dca6f5189693fc98cec4f92eac81b6985e691889
ms.lasthandoff: 03/28/2017


---
# <a name="learn-more-about-password-management"></a>Más información sobre la administración de contraseñas
> [!IMPORTANT]
> **¿Está aquí porque tiene problemas para iniciar sesión?** Si es así, [aquí aprenderá a cambiar y restablecer la contraseña](active-directory-passwords-update-your-own-password.md#reset-your-password).
>
>

Si ya ha implementado la administración de contraseñas, o simplemente desea obtener más información sobre los detalles técnicos del funcionamiento antes de implementarla, en esta sección se le ofrece una introducción apropiada de los conceptos técnicos subyacentes al servicio. Trataremos lo siguiente:

* [**¿Cómo funciona el portal de restablecimiento de contraseñas?**](#how-does-the-password-reset-portal-work)
* [**Información general sobre la escritura diferida de contraseñas**](#password-writeback-overview)
 * [Funcionamiento de la escritura diferida de contraseñas](#how-password-writeback-works)
* [**Escenarios admitidos para la escritura diferida de contraseñas**](#scenarios-supported-for-password-writeback)
 * [Cliente de Azure AD Connect, Sincronización de Azure AD y DirSync admitido](#supported-clients)
 * [Licencias necesarias para la escritura diferida de contraseñas](#licenses-required-for-password-writeback)
 * [Modos de autenticación local admitidos para la escritura diferida de contraseñas](#on-premises-authentication-modes-supported-for-password-writeback)
 * [Operaciones de usuario y administrador admitidas para la escritura diferida de contraseñas](#user-and-admin-operations-supported-for-password-writeback)
 * [Operaciones de usuario y administrador no admitidas para la escritura diferida de contraseñas](#user-and-admin-operations-not-supported-for-password-writeback)
* [**Modelo de seguridad de la escritura diferida de contraseñas**](#password-writeback-security-model)
 * [Detalles de cifrado de la escritura diferida de contraseñas](#password-writeback-encryption-details)
 * [Uso de ancho de banda de la escritura diferida de contraseñas](#password-writeback-bandwidth-usage)
* [**Implementación y administración de los datos de restablecimiento de contraseña para los usuarios y acceso a dichos datos**](#deploying-managing-and-accessing-password-reset-data-for-your-users)
 * [¿Qué datos sirven para restablecer la contraseña?](#what-data-is-used-by-password-reset)
 * [Implementación del restablecimiento de contraseña sin necesidad de registro del usuario final](#deploying-password-reset-without-requiring-end-user-registration)
 * [¿Qué ocurre cuando se registra un usuario para el restablecimiento de contraseña?](#what-happens-when-a-user-registers)
 * [Cómo obtener acceso a los datos de restablecimiento de contraseña de los usuarios](#how-to-access-password-reset-data-for-your-users)
 * [Establecimiento de datos de restablecimiento de contraseña con PowerShell](#setting-password-reset-data-with-powershell)
 * [Lectura de datos de restablecimiento de contraseña con PowerShell](#reading-password-reset-data-with-powershell)
* [**¿Cómo funciona el restablecimiento de contraseña para usuarios B2B?**](#how-does-password-reset-work-for-b2b-users)

## <a name="how-does-the-password-reset-portal-work"></a>¿Cómo funciona el portal de restablecimiento de contraseñas?
Cuando un usuario navega al portal de restablecimiento de contraseñas, se inicia un flujo de trabajo para determinar si esa cuenta de usuario es válida, a qué organización pertenece, dónde se administra la contraseña del usuario y si el usuario dispone o no de una licencia para usar la característica.  Lea los pasos siguientes para obtener información sobre la lógica de la página de restablecimiento de contraseña.

1. El usuario hace clic en el vínculo ¿No puede acceder a su cuenta? o va directamente a [https://passwordreset.microsoftonline.com](https://passwordreset.microsoftonline.com).
2. El usuario escribe un identificador de usuario y pasa un captcha.
3. Azure AD comprueba si el usuario es capaz de utilizar esta característica; para ello, hace lo siguiente:
   * Comprueba que el usuario tiene esta característica habilitada y una licencia de Azure AD asignada.
     * Si el usuario no tiene asignada una licencia o esta característica no está habilitada, se solicita al usuario que se ponga en contacto con el administrador para restablecer la contraseña.
   * Comprueba que el usuario tiene los datos de comprobación definidos en la cuenta según la directiva del administrador.
     * Si la directiva requiere solo una comprobación, se garantiza que el usuario tiene los datos correspondientes definidos para al menos una de las comprobaciones habilitadas por la directiva del administrador.
       * Si el usuario no está configurado, se recomienda al usuario que se ponga en contacto con el administrador para restablecer la contraseña.
     * Si la directiva requiere dos comprobaciones, se garantiza que el usuario tiene los datos correspondientes definidos para al menos dos de las comprobaciones habilitadas por la directiva del administrador.
       * Si el usuario no está configurado, se recomienda al usuario que se ponga en contacto con el administrador para restablecer la contraseña.
   * Comprueba si la contraseña del usuario se administra o no a nivel local (federada o sincronizada con hash de contraseña).
     * Si la escritura diferida de contraseñas está implementada y la contraseña del usuario se administra de forma local, se le permite continuar con la autenticación y restablecer la contraseña.
     * Si la escritura diferida de contraseñas no está implementada y la contraseña del usuario se administra de forma local, se le pide que se ponga en contacto con el administrador para restablecer la contraseña.
4. Si se determina que el usuario puede restablecer correctamente la contraseña, se le guiará a través del proceso de restablecimiento.

Aprenda más sobre cómo implementar la escritura diferida de contraseñas en [Introducción a la administración de contraseñas en Azure AD](active-directory-passwords-getting-started.md).

## <a name="password-writeback-overview"></a>Información general sobre la escritura diferida de contraseñas
La escritura diferida de contraseñas es un componente de [Azure Active Directory Connect](connect/active-directory-aadconnect.md) que los suscriptores actuales de Azure Active Directory Premium pueden habilitar y utilizar. Para obtener más información, consulte [Ediciones de Azure Active Directory](active-directory-editions.md).

La escritura diferida de contraseñas le permite configurar el inquilino de nube para que escriba contraseñas en diferido en su Active Directory local.  Evita tener que configurar y administrar una solución de restablecimiento de contraseñas de autoservicio local y ofrece una manera conveniente basada en la nube para que los usuarios restablezcan sus contraseñas locales dondequiera que estén.  Siga leyendo para conocer algunas de las características clave de la escritura diferida de contraseñas:

* **Comentarios sin ningún retraso.**  La escritura diferida de contraseñas es una operación sincrónica.  Si la contraseña de un usuario no cumple la directiva o no se puede restablecer o modificar por algún motivo, a dicho usuario se le enviará una notificación inmediatamente.
* **Admite el restablecimiento de contraseñas de los usuarios que usan AD FS u otras tecnologías de federación.**  Con la escritura diferida de contraseñas, siempre que las cuentas de usuario federadas estén sincronizadas en el inquilino de Azure AD, podrán administrar sus contraseñas de AD locales desde la nube.
* **Admite el restablecimiento de las contraseñas de usuario mediante la sincronización de hash de contraseñas.** Cuando el servicio de restablecimiento de contraseñas detecta que una cuenta de usuario sincronizada está habilitada para la sincronización de hash de contraseñas, restablecemos la contraseña local y de la nube de la cuenta al mismo tiempo.
* **Permite cambiar las contraseñas del panel de acceso y Office 365.**  Si los usuarios con sincronización de contraseñas o federados modifican las contraseñas expiradas o no expiradas, escribiremos tales contraseñas en diferido en el entorno local de AD.
* **Admite la reescritura de contraseñas cuando un administrador las restablece desde el** [**Portal de administración de Azure**](https://manage.windowsazure.com).  Cada vez que un administrador restablece una contraseña de usuario en el [Portal de administración de Azure](https://manage.windowsazure.com), si ese usuario se federa o se sincroniza con contraseña, también estableceremos la contraseña que el administrador seleccione en el entorno local de AD.  Esto actualmente no se admite en el Portal de administración de Office.
* **Aplica las directivas de contraseñas del entorno de AD local.**  Cuando un usuario restablece su contraseña, nos aseguramos de que cumple la directiva del entorno local de AD antes de enviarla a ese directorio.  Esto incluye el historial, la complejidad, la antigüedad, los filtros de contraseñas y otras restricciones de contraseña que ha definido en el entorno local de AD.
* **No requiere ninguna regla de firewall de entrada.**  La escritura diferida de contraseñas usa una retransmisión de Bus de servicio de Azure como un canal de comunicación subyacente, lo que significa que no tendrá que abrir puertos de entrada en el firewall para que esta característica funcione.
* **No se admite para las cuentas de usuario que se encuentran en grupos protegidos del entorno local de Active Directory.** Para obtener más información sobre los grupos protegidos, consulte [Cuentas y grupos protegidos en Active Directory](https://technet.microsoft.com/library/dn535499.aspx).

### <a name="how-password-writeback-works"></a>Funcionamiento de la escritura diferida de contraseñas
La escritura diferida de contraseñas tiene tres componentes principales:

* Servicio en la nube de restablecimiento de contraseñas (también está integrado en las páginas de cambio de contraseña de Azure AD)
* Retransmisión de Bus de servicio de Azure específica del inquilino
* Extremo de restablecimiento de contraseña local

Se relacionan entre sí como se describe en el diagrama siguiente:

  ![][001]

Si un usuario federado o con sincronización de hash de contraseña cambia o restablece su contraseña en la nube, ocurre lo siguiente:

1. Comprobamos qué tipo de contraseña tiene el usuario.  Si observamos que la contraseña se administra de forma local, a continuación, garantizamos que el servicio de escritura diferida está en funcionamiento.  Si es así, permitimos al usuario continuar; de lo contrario, comunicaremos al usuario que la contraseña no se puede restablecer aquí.
2. A continuación, el usuario pasa las puertas de autenticación adecuadas y llega a la pantalla de restablecimiento de contraseña.
3. El usuario selecciona una nueva contraseña y la confirma.
4. Al hacer clic en enviar, se cifra la contraseña de texto no cifrado con una clave simétrica que se creó durante el proceso de configuración de la escritura diferida.
5. Después de cifrar la contraseña, la incluimos en una carga que se envía a través de un canal HTTPS a la retransmisión de Bus de servicio específico del inquilino (que también establecemos automáticamente durante el proceso de configuración de la escritura diferida).  Esta retransmisión está protegida por una contraseña generada de forma aleatoria que sólo conoce la instalación local.
6. Una vez que el mensaje llega al bus de servicio, el extremo de restablecimiento de contraseña se activa automáticamente y observa que tiene una solicitud de restablecimiento pendiente.
7. A continuación, el servicio busca al usuario correspondiente mediante el atributo delimitador de la nube.  Para que esta búsqueda se realice correctamente, el objeto de usuario debe existir en el espacio del conector AD, debe estar vinculado al objeto MV correspondiente y se debe vincular al objeto de conector de AAD correspondiente. Por último, para que la sincronización encuentre esta cuenta de usuario, el vínculo del objeto de conector de AD al objeto MV debe tener la regla de sincronización `Microsoft.InfromADUserAccountEnabled.xxx` en el vínculo.  Esto es necesario porque cuando se recibe la llamada de la nube, el motor de sincronización utiliza el atributo cloudAnchor para buscar el objeto del espacio del conector de AAD; posteriormente, sigue el vínculo al objeto MV y, a continuación, sigue el vínculo al objeto de AD. Dado que podría haber varios objetos de AD (bosque múltiple) para el mismo usuario, el motor de sincronización se basa en el `Microsoft.InfromADUserAccountEnabled.xxx` vínculo para seleccionar el correcto. Tenga en cuenta que, como resultado de esta lógica, debe conectar Azure AD Connect al controlador de dominio principal para que funcione la escritura diferida de contraseñas.  Si es necesario hacer esto, puede configurar Azure AD Connect para usar un emulador de controlador de dominio principal haciendo clic con el botón derecho en las **propiedades** del conector de sincronización de Active Directory; luego, seleccione **configure directory partitions** (Configurar particiones del directorio). Desde allí, busque la sección de **configuración de conexión de controlador de dominio** y active la casilla titulada **only use preferred domain controllers** (Solo utilizar los controladores de dominio preferidos). Nota: si el controlador de dominio preferido no es un emulador PDC, Azure AD Connect se seguirá conectando a los PDC para realizar la escritura diferida de contraseñas.
8. Una vez que se encuentra la cuenta de usuario, se intenta restablecer la contraseña directamente en el bosque de AD correspondiente.
9. Si la operación de establecimiento de la contraseña se realiza correctamente, notificamos al usuario que la contraseña se ha modificado y que puede continuar con su trabajo.
10. Si se produce un error en la operación de establecimiento de la contraseña, se devolverá el error al usuario y se le permitirá que vuelva a intentarlo.  La operación puede producir un error porque el servicio no estaba disponible, la contraseña seleccionada no cumplía las directivas de la organización, no se encontraba el usuario en el entorno local de AD o por otros motivos.  Disponemos de un mensaje específico para muchos de estos casos, a fin de indicar al usuario qué puede hacer para resolver el problema.

## <a name="scenarios-supported-for-password-writeback"></a>Escenarios admitidos para la escritura diferida de contraseñas
En la sección siguiente se describe qué escenarios se admiten para las versiones de nuestras capacidades de sincronización.  En general, siempre recomendamos que use la característica de actualización automática de Azure AD Connect o instale la versión más reciente de [Azure AD Connect](connect/active-directory-aadconnect.md#install-azure-ad-connect) si desea utilizar la escritura diferida de contraseñas.

* [**Clientes de Azure AD Connect, Sincronización de Azure AD y DirSync admitidos**](#supported-clients)
* [**Licencias necesarias para la escritura diferida de contraseñas**](#licenses-required-for-password-writeback)
* [**Modos de autenticación local admitidos para la escritura diferida de contraseñas**](#on-premises-authentication-modes-supported-for-password-writeback)
* [**Operaciones de usuario y administrador admitidas para la escritura diferida de contraseñas**](#user-and-admin-operations-supported-for-password-writeback)
* [**Operaciones de usuario y administrador no admitidas para la escritura diferida de contraseñas**](#user-and-admin-operations-not-supported-for-password-writeback)

### <a name="supported-clients"></a>Clientes compatibles
Siempre recomendamos que use la característica de actualización automática de Azure AD Connect o instale la versión más reciente de [Azure AD Connect](connect/active-directory-aadconnect.md#install-azure-ad-connect) si desea utilizar la escritura diferida de contraseñas.

* **DirSync (cualquier versión > 1.0.6862)** - _NO SE ADMITE_: Admite solo las funcionalidades de escritura diferida básicas y el grupo de productos ya no se admite.
* **Azure AD Sync** - _EN DESUSO_: Admite solo las funcionalidades de escritura en diferido básicas y no cuenta con funcionalidades de desbloqueo de cuenta, registro enriquecido y mejoras de confiabilidad realizadas en Azure AD Connect. Por lo tanto, recomendamos **encarecidamente** actualizar.
* **Azure AD Connect** - _TOTALMENTE COMPATIBLE_: Admite todas las funcionalidades de escritura diferida. Actualice a la versión más reciente para obtener las mejores y nuevas características, así como la máxima estabilidad y confiabilidad posibles.

### <a name="licenses-required-for-password-writeback"></a>Licencias necesarias para la escritura diferida de contraseñas
Para poder usar la escritura diferida de contraseñas, debe tener una de las siguientes licencias asignadas en el inquilino.

* **Azure AD Premium P1**: Ninguna limitación respecto al uso de la escritura diferida de contraseñas
* **Azure AD Premium P2**: Ninguna limitación respecto al uso de la escritura diferida de contraseñas
* **Enterprise Moblity Suite**: Ninguna limitación respecto al uso de la escritura diferida de contraseñas
* **Enterprise Cloud Suite**: Ninguna limitación respecto al uso de la escritura diferida de contraseñas

No se puede usar la escritura diferida de contraseñas con cualquier plan de licencias de Office 365, ya sea de prueba o de pago. Debe actualizar a uno de los planes anteriores para poder usar esta característica.

No tenemos pensado habilitar la escritura diferida de contraseñas para ningún SKU de Office 365.

### <a name="on-premises-authentication-modes-supported-for-password-writeback"></a>Modos de autenticación local admitidos para la escritura diferida de contraseñas
La escritura diferida de contraseñas funciona para los siguientes tipos de contraseña de usuario:

* **Usuarios solo en la nube**: La escritura diferida de contraseñas no se aplica en este caso, porque no hay contraseña local
* **Usuarios sincronizados con contraseña**: Se admite la escritura diferida de contraseñas
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
 * Cualquier restablecimiento de contraseña del usuario final iniciado por el administrador desde el [Portal de administración de Azure clásico](https://manage.windowsazure.com)
 * Cualquier restablecimiento de contraseña del usuario final iniciado por el administrador desde [Azure Portal](https://portal.azure.com)

### <a name="user-and-admin-operations-not-supported-for-password-writeback"></a>Operaciones de usuario y administrador no admitidas para la escritura diferida de contraseñas
Las contraseñas no se escriben en diferido en ninguna de las situaciones siguientes:

* **Operaciones de usuario final no admitidas**
 * Cualquier usuario final que restablece su contraseña mediante PowerShell v1, v2 o la API Graph de Azure AD
* **Operaciones de administrador no admitidas**
 * Cualquier restablecimiento de contraseña del usuario final iniciado por el administrador desde el [Portal de administración de Office](https://portal.office.com)
 * Cualquier restablecimiento de contraseña del usuario final iniciado por el administrador desde PowerShell v1, v2 o la API Graph de Azure AD

Aunque estamos trabajando para quitar estas limitaciones, todavía no tenemos una escala de tiempo concreta que podemos compartir.

## <a name="password-writeback-security-model"></a>Modelo de seguridad de la escritura diferida de contraseñas
La escritura diferida de contraseñas es un servicio sumamente seguro y sólido.  Para asegurarse de que su información está protegida, habilitamos un modelo de seguridad de cuatro niveles que describimos a continuación.

* **Retransmisión de Bus de servicio específica de inquilino** : al configurar el servicio, configuramos una retransmisión de Bus de servicio específica del inquilino que está protegida por una contraseña segura generada aleatoriamente a la que Microsoft nunca tiene acceso.
* **Clave de cifrado de contraseñas bloqueadas y criptográficamente segura** : una vez creada la retransmisión de Bus de servicio, creamos una clave simétrica segura que se utiliza para cifrar la contraseña tal como se transfiere a través de la red.  Esta clave reside solo en el almacén secreto de la compañía en la nube, que se bloquea y audita de forma estricta como cualquier contraseña en el directorio.
* **TLS estándar del sector** : cuando se produce una operación de restablecimiento o cambio de contraseña, ciframos la contraseña de texto no cifrado con la clave pública.  A continuación, la insertamos en un mensaje HTTPS que se envía a través de un canal cifrado con certificados SSL de Microsoft para la retransmisión de Bus de servicio.  Después de que ese mensaje llega al Bus de servicio, el agente local se activa, se autentica en el Bus de servicio con la contraseña segura que se había generado previamente, recoge el mensaje cifrado, lo descifra con la clave privada generada y, a continuación, intenta establecer la contraseña a través de la API SetPassword de AD DS.  Este paso nos permite aplicar la directiva de contraseñas local de AD (complejidad, edad, historial, filtros, etc.) en la nube.
* **Directivas de expiración de mensajes** : por último, si por alguna razón el mensaje espera en el Bus de servicio porque el servicio local no funciona, se agotará el tiempo de espera y se eliminará transcurridos unos minutos para aumentar aún más la seguridad.

### <a name="password-writeback-encryption-details"></a>Detalles de cifrado de la escritura diferida de contraseñas
A continuación se describen los pasos de cifrado que se llevan a cabo para una solicitud de restablecimiento de contraseña después de que un usuario la envía, pero antes de que llegue a su entorno local, para garantizar la seguridad y confiabilidad máximas del servicio.

* **Paso 1: Cifrado de contraseña con la clave RSA de 2048 bits**: Una vez que un usuario envía una contraseña para que se escriba en diferido en local, primero se cifra la propia contraseña enviada con una clave RSA de 2048 bits.

* **Paso 2: Cifrado a nivel de paquete con AES-GCM**: A continuación, todo el paquete (contraseña + metadatos necesarios) se cifra mediante AES-GCM. Esto evita que cualquier persona con acceso directo al canal de ServiceBus subyacente vea o manipula el contenido.

* **Paso 3: Toda la comunicación se realiza a través de TLS/SSL**: Además, toda la comunicación con ServiceBus tiene lugar en un canal SSL/TLS. Esto protege el contenido de terceras personas no autorizadas.

* **Paso 4: Sustitución de clave automática cada 6 meses**: Por último, cada 6 meses y de forma automática, o cada vez que la escritura diferida de contraseñas se deshabilita o se vuelve a habilitar en Azure AD Connect, se sustituyen todas estas claves para garantizar la máxima seguridad del servicio.

### <a name="password-writeback-bandwidth-usage"></a>Uso de ancho de banda de la escritura diferida de contraseñas

La escritura diferida de contraseñas es un servicio de muy bajo consumo de ancho de banda que envía solicitudes al agente local solo en las siguientes circunstancias:

1. Se enviaron dos mensajes al habilitar o deshabilitar la característica a través de Azure AD Connect.
2. Se envía un mensaje cada 5 minutos como un latido del servicio siempre que el servicio se está ejecutando.
3. Se envían dos mensajes cada vez que se envía una nueva contraseña: un mensaje como solicitud para realizar la operación y el mensaje posterior que contiene el resultado de la misma. Estos mensajes se envían en las siguientes circunstancias.
4. Cada vez que se envía una nueva contraseña durante un restablecimiento de la contraseña de autoservicio de un usuario.
5. Cada vez que se envía una nueva contraseña durante una operación de cambio de la contraseña de un usuario.
6. Cada vez que se envía una nueva contraseña durante un restablecimiento de contraseña de usuario iniciado por el administrador (solo desde los portales de administración de Azure)

#### <a name="message-size-and-bandwidth-considerations"></a>Consideraciones sobre el ancho de banda y el tamaño de mensaje

El tamaño de cada uno de los mensajes que se ha descrito anteriormente está normalmente por debajo de 1 kb, lo que significa, que incluso bajo cargas extremas, el propio servicio de escritura diferida de contraseña utilizará solo unos kilobits por segundo como máximo del ancho de banda. Puesto que cada mensaje se envía en tiempo real, solo cuando es necesario debido a una operación de actualización de contraseña, y dado que el tamaño del mensaje es tan pequeño, el uso de ancho de banda de la funcionalidad de escritura diferida es demasiado pequeño para tener ningún impacto cuantificable real.

## <a name="deploying-managing-and-accessing-password-reset-data-for-your-users"></a>Implementación y administración de los datos de restablecimiento de contraseña para los usuarios y acceso a dichos datos
Puede administrar los datos de restablecimiento de contraseña para los usuarios y acceder a dichos datos a través de Azure AD Connect, PowerShell, Graph o nuestras experiencias de registro.  Puede incluso implementar el restablecimiento de contraseña en toda la organización sin que los usuarios tengan que registrarse para ello aprovechando las opciones descritas a continuación.

  * [¿Qué datos sirven para restablecer la contraseña?](#what-data-is-used-by-password-reset)
  * [Implementación del restablecimiento de contraseña sin necesidad de registro del usuario final](#deploying-password-reset-without-requiring-end-user-registration)
  * [¿Qué ocurre cuando se registra un usuario para el restablecimiento de contraseña?](#what-happens-when-a-user-registers)
  * [Cómo obtener acceso a los datos de restablecimiento de contraseña de los usuarios](#how-to-access-password-reset-data-for-your-users)
  * [Establecimiento de datos de restablecimiento de contraseña con PowerShell](#setting-password-reset-data-with-powershell)
  * [Lectura de datos de restablecimiento de contraseña con PowerShell](#reading-password-reset-data-with-powershell)

### <a name="what-data-is-used-by-password-reset"></a>¿Qué datos sirven para restablecer la contraseña?
En la tabla siguiente se describe dónde y cómo se usan estos datos durante el restablecimiento de la contraseña y está diseñada para ayudarle a decidir qué opciones de autenticación resultan apropiadas para su organización. En esta tabla también se indican los requisitos de formato para los casos donde va a proporcionar datos en nombre de usuarios desde rutas de acceso de entrada que no validan estos datos.

> [!NOTE]
> El teléfono del trabajo no aparece en el portal de registro porque los usuarios actualmente no pueden editar esta propiedad en el directorio. Solo los administradores pueden establecer este valor.
>
>

<table>
          <tbody><tr>
            <td>
              <p>
                <strong>Nombre de método de contacto</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Elemento de datos de Active Directory</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Elemento de datos de Azure Active Directory</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>¿Dónde se usa/configura?</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Requisitos de formato</strong>
              </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Teléfono del trabajo</p>
            </td>
            <td>
              <p>telephoneNumber</p>
              <p>Esta propiedad se puede sincronizar con el atributo PhoneNumber en Azure Active Directory y utilizarse inmediatamente para restablecimiento de contraseña SIN exigir a un usuario que se registre primero.</p>
            </td>
            <td>
              <p>PhoneNumber</p>
              <p>P.ej. Set-MsolUser -UserPrincipalName JWarner@contoso.com -PhoneNumber "+1 1234567890x1234"</p>
            </td>
            <td>
              <p>Usado en:</p>
              <p>Portal de restablecimiento de contraseñas</p>
              <p>Configurable en:</p>
              <p>PhoneNumber se puede configurar desde PowerShell, DirSync, el Portal de administración de Azure y el Portal de administración de Office</p>
            </td>
            <td>
              <p>+ccc xxxyyyzzzz (p. ej.: +1 1234567890)</p>
              <ul>
                <li class="unordered">
Debe proporcionar un código de país.<br><br></li>
              </ul>
              <ul>
                <li class="unordered">
Debe proporcionar un código de área (si procede).<br><br></li>
              </ul>
              <ul>
                <li class="unordered">
Debe proporcionar un signo + antes del código de país.<br><br></li>
              </ul>
              <ul>
                <li class="unordered">
Debe haber un espacio entre el código de país y el resto del número.<br><br></li>
              </ul>
              <ul>
                <li class="unordered">
No se admiten extensiones; por tanto, si ha especificado extensiones, las quitaremos antes de enviar la llamada de teléfono.<br><br></li>
              </ul>
            </td>
          </tr>
          <tr>
            <td>
              <p>Teléfono móvil</p>
            </td>
            <td>
              <p>Móvil</p>
              <p>Esta propiedad se puede sincronizar con el atributo MobilePhone en Azure Active Directory y utilizarse inmediatamente para restablecimiento de contraseña SIN exigir a un usuario que se registre primero.</p>
              <p>En este momento, no es posible sincronizar esta propiedad con AuthenticationPhone.</p>
            </td>
            <td>
              <p>AuthenticationPhone</p>
              <p>OR</p>
              <p>MobilePhone</p>
              <p>(El teléfono de autenticación se utiliza si hay datos presentes; en caso contrario, recurre al campo de teléfono móvil).</p>
              <p>P.ej., Set-MsolUser -UserPrincipalName JWarner@contoso.com -MobilePhone "+1 1234567890x1234"</p>
            </td>
            <td>
              <p>Usado en:</p>
              <p>Portal de restablecimiento de contraseñas</p>
              <p>Portal de registro</p>
              <p>Configurable en: </p>
              <p>AuthenticationPhone se puede configurar desde el portal de registro de restablecimiento de contraseña o desde el portal de registro de MFA.</p>
              <p>MobilePhone se puede establecer desde PowerShell, Azure AD Connect, el Portal de administración de Azure y el Portal de administración de Office.</p>
            </td>
            <td>
              <p>+ccc xxxyyyzzzz (p. ej.: +1 1234567890)</p>
              <ul>
                <li class="unordered">
Debe proporcionar un código de país.<br><br></li>
              </ul>
              <ul>
                <li class="unordered">
Debe proporcionar un código de área (si procede).<br><br></li>
              </ul>
              <ul>
                <li class="unordered">
Debe proporcionar un signo + antes del código de país.<br><br></li>
              </ul>
              <ul>
                <li class="unordered">
Debe haber un espacio entre el código de país y el resto del número.<br><br></li>
              </ul>
              <ul>
                <li class="unordered">
No se admiten extensiones; por tanto, si ha especificado extensiones, las ignoraremos al realizar la llamada de teléfono.<br><br></li>
              </ul>
            </td>
          </tr>
          <tr>
            <td>
              <p>Correo electrónico alternativo</p>
            </td>
            <td>
              <p>No disponible</p>
              <p>En este momento, no es posible sincronizar los valores de Active Directory con la propiedad AuthenticationEmail o AlternateEmailAddresses[0]. </p>
              <p>Puede utilizar PowerShell para establecer AlternateEmailAddresses[0]. En la sección que hay justo debajo de esta tabla encontrará instrucciones para esto.</p>
            </td>
            <td>
              <p>AuthenticationEmail</p>
              <p>O</p>
              <p>AlternateEmailAddresses[0] </p>
              <p>(El correo electrónico de autenticación se utiliza si hay datos presentes; en caso contrario, recurre al campo de correo electrónico alternativo).</p>
              <p>Nota: El campo de correo electrónico alternativo se especifica como una matriz de cadenas en el directorio.  Usamos la primera entrada de esta matriz.</p>
              <p>Por ejemplo, Set-MsolUser -UserPrincipalName JWarner@contoso.com -AlternateEmailAddresses "email@live.com".</p>
            </td>
            <td>
              <p>Usado en:</p>
              <p>Portal de restablecimiento de contraseñas</p>
              <p>Portal de registro</p>
              <p>Configurable en: </p>
              <p>AuthenticationEmail se puede configurar desde el portal de registro de restablecimiento de contraseña o desde el portal de registro de MFA.</p>
              <p>AuthenticationEmail se puede configurar desde PowerShell, el Portal de administración de Azure y el Portal de administración de Office</p>
            </td>
            <td>
              <p>
                <a href="mailto:user@domain.com">user@domain.com</a> o 甲斐@黒川.日本</p>
              <ul>
                <li class="unordered">
Los mensajes de correo electrónico deben seguir el formato estándar.<br><br></li>
              </ul>
              <ul>
                <li class="unordered">
Se admiten los correos electrónicos Unicode.<br><br></li>
              </ul>
            </td>
          </tr>
          <tr>
            <td>
              <p>Preguntas y respuestas de seguridad</p>
            </td>
            <td>
              <p>No disponible</p>
              <p>En este momento, no es posible sincronizar preguntas o respuestas de seguridad de Active Directory.</p>
            </td>
            <td>
              <p>No está disponible para modificarlo directamente en el directorio.</p>
              <p>Solo se puede establecer durante el proceso de registro del usuario final de restablecimiento de contraseña.</p>
            </td>
            <td>
              <p>Usado en:</p>
              <p>Portal de restablecimiento de contraseñas</p>
              <p>Portal de registro </p>
              <p>Configurable en: </p>
              <p>Las preguntas de seguridad solo pueden realizarse en el Portal de administración de Azure.</p>
              <p>Las respuestas a preguntas de seguridad de un usuario determinado solo se pueden realizar en el Portal de registro.</p>
            </td>
            <td>
              <p>Las preguntas de seguridad deben contener un máximo de 200 caracteres y un mínimo de 3.</p>
              <p>Las preguntas de seguridad deben contener un máximo de 40 caracteres y un mínimo de 3.</p>
            </td>
          </tr>
        </tbody></table>


### <a name="deploying-password-reset-without-requiring-end-user-registration"></a>Implementación del restablecimiento de contraseña sin necesidad de registro del usuario final
Si va a implementar el restablecimiento de contraseña sin solicitar a los usuarios que se registren para ello, puede hacerlo fácilmente siguiendo una de las dos opciones siguientes. Esto puede ser una forma útil de desbloquear grandes cantidades de usuarios para usar SSPR mientras se sigue permitiendo que los usuarios validen esta información a través del proceso de registro.

Muchos de nuestros clientes más grandes utilizan esto hoy para empezar a trabajar con el restablecimiento de contraseña al momento.

#### <a name="synchronize-phone-numbers-with-azure-ad-connect"></a>Sincronización de números de teléfono con Azure AD Connect
Si sincroniza datos con uno o los dos campos siguientes, se pueden utilizar inmediatamente para restablecimiento de contraseña, sin necesidad de que los usuarios tengan que registrarse antes:

* Teléfono móvil
* Teléfono del trabajo

Para información sobre qué propiedades deben actualizarse de forma local, vaya a la sección [¿Qué datos sirven para restablecer la contraseña?](#what-data-is-used-by-password-reset) y busque los campos mencionados anteriormente.  

Asegúrese de que los números de teléfono tienen el formato "+ 1 1234567890" para que funcionen correctamente con nuestro sistema.

#### <a name="set-phone-numbers-or-emails-with-powershell"></a>Establecimiento de números de teléfono o correos electrónicos con PowerShell
Si establece uno o varios de estos campos, se pueden utilizar inmediatamente para restablecimiento de contraseña, sin necesidad de que los usuarios tengan que registrarse antes:

* Teléfono móvil
* Teléfono del trabajo
* Correo electrónico alternativo

Para información sobre cómo establecer estas propiedades mediante PowerShell, vaya a la sección [Establecimiento de datos de restablecimiento de contraseña con PowerShell](#setting-password-reset-data-with-powershell).

Asegúrese de que los números de teléfono tienen el formato "+ 1 1234567890" para que funcionen correctamente con nuestro sistema.

### <a name="what-happens-when-a-user-registers"></a>¿Qué ocurre cuando se registra un usuario?
Cuando se registre un usuario, la página de registro **siempre** establecerá los campos siguientes:

* Teléfono de autenticación
* Correo electrónico de autenticación
* Preguntas y respuestas de seguridad

Si ha especificado un valor para **Teléfono móvil** o **Correo electrónico alternativo**, los usuarios podrán usarlos inmediatamente para restablecer sus contraseñas, aunque no se hayan registrado para el servicio.  Además, los usuarios verán esos valores cuando se registren por primera vez, y podrán modificarlos si lo desean.  Sin embargo, una vez que se registren correctamente, dichos valores se conservarán en los campos **Teléfono de autenticación** y **Correo electrónico de autenticación**, respectivamente.

### <a name="how-to-access-password-reset-data-for-your-users"></a>Cómo obtener acceso a los datos de restablecimiento de la contraseña de los usuarios
#### <a name="data-settable-via-synchronization"></a>Datos configurables mediante la sincronización
Desde el entorno local, se pueden sincronizar los campos siguientes:

* Teléfono móvil
* Teléfono del trabajo

#### <a name="data-settable-with-azure-ad-powershell--azure-ad-graph"></a>Datos configurables con Azure AD PowerShell y Azure AD Graph
Los siguientes campos se pueden establecer utilizando Azure AD PowerShell y la API Graph de Azure AD:

* Correo electrónico alternativo
* Teléfono móvil
* Teléfono del trabajo

#### <a name="data-settable-with-registration-ui-only"></a>Datos configurables solamente con la IU de registro
Solo se puede acceder a los campos siguientes a través de la interfaz de usuario del registro de SSPR (https://aka.ms/ssprsetup):

* Preguntas y respuestas de seguridad

#### <a name="data-readable-with-azure-ad-powershell--azure-ad-graph"></a>Datos que se pueden leer con Azure AD PowerShell y Azure AD Graph
Es posible acceder a los siguientes campos mediante Azure AD PowerShell y la API Graph de Azure AD:

* Correo electrónico alternativo
* Teléfono móvil
* Teléfono del trabajo
* Teléfono de autenticación
* Correo electrónico de autenticación

### <a name="setting-password-reset-data-with-powershell"></a>Establecimiento de datos de restablecimiento de contraseñas con PowerShell
Puede establecer valores para los siguientes campos con Azure AD PowerShell.

* Correo electrónico alternativo
* Teléfono móvil
* Teléfono del trabajo

**_PowerShell V1_**

Para empezar, primero deberá [descargar e instalar el módulo de Azure AD PowerShell](https://msdn.microsoft.com/library/azure/jj151815.aspx#bkmk_installmodule).  Una vez instalado, puede seguir los pasos siguientes para configurar cada campo.

**_PowerShell V2_**

Para empezar, primero deberá [descargar e instalar el módulo de Azure AD V2 PowerShell](https://github.com/Azure/azure-docs-powershell-azuread/blob/master/Azure%20AD%20Cmdlets/AzureAD/index.md). Una vez instalado, puede seguir los pasos siguientes para configurar cada campo.

Para instalar rápidamente desde versiones recientes de PowerShell que admiten Install-Module, ejecute estos comandos (la primera línea simplemente comprueba si ya se ha instalado):

```
Get-Module AzureADPreview
Install-Module AzureADPreview
Connect-AzureAD
```

#### <a name="alternate-email---how-to-set-alternate-email-with-powershell"></a>Correo electrónico alternativo - Cómo establecer el correo electrónico alternativo con PowerShell
**_PowerShell V1_**

```
Connect-MsolService
Set-MsolUser -UserPrincipalName user@domain.com -AlternateEmailAddresses @("email@domain.com")
```

**_PowerShell V2_**

```
Connect-AzureAD
Set-AzureADUser -ObjectId user@domain.com -OtherMails @("email@domain.com")
```

#### <a name="mobile-phone---how-to-set-mobile-phone-with-powershell"></a>Teléfono móvil - Cómo establecer el teléfono móvil con PowerShell
**_PowerShell V1_**

```
Connect-MsolService
Set-MsolUser -UserPrincipalName user@domain.com -MobilePhone "+1 1234567890"
```

**_PowerShell V2_**

```
Connect-AzureAD
Set-AzureADUser -ObjectId user@domain.com -Mobile "+1 1234567890"
```

#### <a name="office-phone---how-to-set-office-phone-with-powershell"></a>Teléfono del trabajo - Cómo establecer el teléfono del trabajo con PowerShell
**_PowerShell V1_**

```
Connect-MsolService
Set-MsolUser -UserPrincipalName user@domain.com -PhoneNumber "+1 1234567890"
```

**_PowerShell V2_**

```
Connect-AzureAD
Set-AzureADUser -ObjectId user@domain.com -TelephoneNumber "+1 1234567890"
```

### <a name="reading-password-reset-data-with-powershell"></a>Lectura de datos de restablecimiento de contraseñas con PowerShell
Puede leer valores de los siguientes campos con Azure AD PowerShell.

* Correo electrónico alternativo
* Teléfono móvil
* Teléfono del trabajo
* Teléfono de autenticación
* Correo electrónico de autenticación

#### <a name="alternate-email---how-to-read-alternate-email-with-powershell"></a>Correo electrónico alternativo - Cómo leer el correo electrónico alternativo con PowerShell
**_PowerShell V1_**

```
Connect-MsolService
Get-MsolUser -UserPrincipalName user@domain.com | select AlternateEmailAddresses
```

**_PowerShell V2_**

```
Connect-AzureAD
Get-AzureADUser -ObjectID user@domain.com | select otherMails
```

#### <a name="mobile-phone---how-to-read-mobile-phone-with-powershell"></a>Teléfono móvil - Cómo leer el teléfono móvil con PowerShell
**_PowerShell V1_**

```
Connect-MsolService
Get-MsolUser -UserPrincipalName user@domain.com | select MobilePhone
```

**_PowerShell v2_**

```
Connect-AzureAD
Get-AzureADUser -ObjectID user@domain.com | select Mobile
```

#### <a name="office-phone---how-to-read-office-phone-with-powershell"></a>Teléfono del trabajo - Cómo leer el teléfono del trabajo con PowerShell
**_PowerShell V1_**

```
Connect-MsolService
Get-MsolUser -UserPrincipalName user@domain.com | select PhoneNumber
```

**_PowerShell V2_**

```
Connect-AzureAD
Get-AzureADUser -ObjectID user@domain.com | select TelephoneNumber
```

#### <a name="authentication-phone---how-to-read-authentication-phone-with-powershell"></a>Teléfono de autenticación - Cómo leer el teléfono de autenticación con PowerShell
**_PowerShell V1_**

```
Connect-MsolService
Get-MsolUser -UserPrincipalName user@domain.com | select -Expand StrongAuthenticationUserDetails | select PhoneNumber
```

**_PowerShell V2_**

```
Not possible in PowerShell V2
```

#### <a name="authentication-email---how-to-read-authentication-email-with-powershell"></a>Correo electrónico de autenticación - Cómo leer el correo electrónico de autenticación con PowerShell
**_PowerShell V1_**

```
Connect-MsolService
Get-MsolUser -UserPrincipalName user@domain.com | select -Expand StrongAuthenticationUserDetails | select Email
```

**_PowerShell V2_**

```
Not possible in PowerShell V2
```
## <a name="how-does-password-reset-work-for-b2b-users"></a>¿Cómo funciona el restablecimiento de contraseña para usuarios B2B?
Todas las configuraciones B2B admiten el cambio y restablecimiento de contraseña.  Lea la información que figura a continuación para conocer los 3 casos B2B explícitos compatibles con el restablecimiento de contraseña.

1. **Usuarios de una organización de asociados con un inquilino de Azure AD existente**: Si la organización con la que se está asociando tiene un inquilino de Azure AD existente, **respetaremos todas las directivas de restablecimiento de contraseña que estén habilitadas en dicho inquilino**. Para que el restablecimiento de contraseña funcione, la organización del asociado simplemente necesita asegurarse de que la función SSPR de Azure AD esté habilitada, lo que no supone un cargo adicional para los clientes de Office 365. Se puede habilitar siguiendo los pasos descritos en nuestra guía [Introducción a la administración de contraseñas](https://azure.microsoft.com/documentation/articles/active-directory-passwords-getting-started/#enable-users-to-reset-or-change-their-aad-passwords).
2. **Usuarios registrados mediante el [registro de autoservicio](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-signup)**: Si la organización con la que se está asociando usaba la función de [registro de autoservicio](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-signup) para entrar en un inquilino, les permitiremos el restablecimiento inmediato con el correo electrónico que registraron.
3. **Usuarios de B2B**: Los nuevos usuarios de B2B creados mediante las nuevas [funcionalidades de B2B de Azure AD](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b) también podrán restablecer inmediatamente sus contraseñas con el correo electrónico que registraron durante el proceso de invitación.

Para probar cualquiera de estos casos, vaya a http://passwordreset.microsoftonline.com con uno de estos usuarios asociados.  Siempre que tengan un correo electrónico alternativo o un correo electrónico de autenticación definido, el restablecimiento de contraseña funcionará según lo esperado.  En la información general [¿Qué datos sirven para restablecer la contraseña?](https://azure.microsoft.com/en-us/documentation/articles/active-directory-passwords-learn-more/#what-data-is-used-by-password-reset) puede encontrar más información sobre los datos que usa la función SSPR aquí.

## <a name="next-steps"></a>Pasos siguientes
A continuación se muestran vínculos a todas las páginas de documentación de restablecimiento de contraseña de Azure AD:

* **¿Está aquí porque tiene problemas para iniciar sesión?** Si es así, [aquí aprenderá a cambiar y restablecer la contraseña](active-directory-passwords-update-your-own-password.md#reset-your-password).
* [**Funcionamiento**](active-directory-passwords-how-it-works.md): obtenga información acerca de los seis componentes diferentes del servicio y lo que hace cada uno.
* [**Introducción**](active-directory-passwords-getting-started.md): obtenga información sobre cómo permitir a los usuarios restablecer y cambiar sus contraseñas en la nube o locales.
* [**Personalizar**](active-directory-passwords-customize.md): obtenga información acerca de cómo personalizar la apariencia y el comportamiento del servicio para ajustarse a las necesidades de su organización
* [**Procedimientos recomendados**](active-directory-passwords-best-practices.md): obtenga información acerca de cómo implementar rápidamente y administrar eficazmente las contraseñas de la organización
* [**Obtener información**](active-directory-passwords-get-insights.md): obtenga información acerca de nuestras funcionalidades integradas de creación de informes.
* [**P+F**](active-directory-passwords-faq.md) : obtenga respuestas a las preguntas más frecuentes.
* [**Solución de problemas**](active-directory-passwords-troubleshoot.md): obtenga información sobre cómo solucionar rápidamente los problemas del servicio.

[001]: ./media/active-directory-passwords-learn-more/001.jpg "Image_001.jpg"
[002]: ./media/active-directory-passwords-learn-more/002.jpg "Image_002.jpg"

