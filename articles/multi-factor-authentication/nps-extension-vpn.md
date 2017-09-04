---
title: "Integración de la VPN con Azure MFA con la extensión NPS | Microsoft Docs"
description: "Este artículo describe la integración de la infraestructura de VPN con Azure MFA utilizando la extensión Servidor de directivas de redes (NPS) para Microsoft Azure."
services: active-directory
keywords: "Azure MFA, integración de VPN, Azure Active Directory, extensión Servidor de directivas de redes"
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2017
ms.author: kgremban
ms.reviewer: jsnow
ms.custom: it-pro
ms.translationtype: HT
ms.sourcegitcommit: 5b6c261c3439e33f4d16750e73618c72db4bcd7d
ms.openlocfilehash: 3dfcf25856ede50266336c2ebb057dd3f7b8897e
ms.contentlocale: es-es
ms.lasthandoff: 08/28/2017

---

# <a name="integrate-your-vpn-infrastructure-with-azure-multi-factor-authentication-mfa-using-the-network-policy-server-nps-extension-for-azure"></a>Este artículo describe la integración de la infraestructura de VPN con Azure Multi-Factor Authentication (MFA) utilizando la extensión Servidor de directivas de redes (NPS) para Azure

## <a name="overview"></a>Información general

La extensión Servidor de directivas de redes (NPS) para Azure permite a las organizaciones proteger la autenticación de cliente del Servicio de autenticación remota telefónica de usuario (RADIUS) utilizando la autenticación basada en la nube [Azure Multi-Factor Authentication (MFA)](multi-factor-authentication-get-started-server-rdg.md), que proporciona verificación en dos pasos.

Este artículo proporciona instrucciones para integrar la infraestructura NPS con Azure MFA con la extensión NPS para Azure para habilitar la verificación segura en dos pasos para los usuarios que intentan conectarse a la red mediante una VPN. 

La directiva de red y servicios de acceso (NPS) ofrece a las organizaciones las siguientes posibilidades:

* Especificar ubicaciones centrales para la administración y el control de las solicitudes de red mediante la especificación de quién puede conectarse, las franjas horarias en las que se permiten conexiones, la duración de las conexiones, el nivel de seguridad que los clientes deben usar para conectarse, etc. En vez de especificar estas directivas en cada VPN o servidor de puerta de enlace de Escritorio remoto (RD), estas directivas se especifican una vez en una ubicación central. El protocolo RADIUS se usa para proporcionar autenticación, autorización y contabilización de cuentas (AAA) centralizadas. 
* Establecer y aplicar directivas de mantenimiento de cliente de Protección de acceso a redes (NAP) que determinan si los dispositivos tienen acceso restringido o sin restricciones a los recursos de la red.
* Proporcionar un medio para aplicar la autenticación y autorización para el acceso a puntos de acceso inalámbricos 802.1x y conmutadores Ethernet.    

Consulte [Servidor de directivas de redes (NPS)](https://docs.microsoft.com/windows-server/networking/technologies/nps/nps-top) para más información. 

Para mejorar la seguridad y proporcionar un nivel elevado de cumplimiento, las organizaciones pueden integrar NPS con Azure MFA para asegurarse de que los usuarios utilizan la verificación en dos pasos para poder conectarse al puerto virtual en el servidor VPN. Para tener acceso, los usuarios deben proporcionar su combinación de nombre de usuario y contraseña junto con información que el usuario tiene bajo su control. Esta información debe ser de confianza y no duplicable fácilmente, como un número de teléfono móvil, el número fijo o una aplicación en un dispositivo móvil, entre otros.

Antes de la disponibilidad de la extensión NPS para Azure, los clientes que querían implementar la verificación en dos pasos para entornos integrados de NPS y Azure MFA tenían que configurar y mantener un servidor independiente de MFA en el entorno local tal como se documenta en Puerta de enlace de Escritorio remoto y Servidor Azure Multi-Factor Authentication con RADIUS.

La disponibilidad de la extensión NPS para Azure ahora da a las organizaciones la opción de implementar una solución MFA basada en el entorno local o una solución MFA basada en la nube para la autenticación segura de clientes RADIUS.
 
## <a name="authentication-flow"></a>Flujo de autenticación
Cuando un usuario se conecta a un puerto virtual en un servidor VPN, debe autenticarse primero con una variedad de protocolos, lo que permite el uso de una combinación de nombre de usuario y contraseña y métodos de autenticación basados en certificados. 

Además de la autenticación y verificación de identidad, los usuarios deben tener los permisos adecuados de acceso telefónico. En implementaciones sencillas, estos permisos de acceso telefónico que permiten tener acceso se establecen directamente en los objetos de usuario de Active Directory. 

 ![Propiedades de usuario](./media/nps-extension-vpn/image1.png)

Para implementaciones sencillas, cada servidor VPN concede o deniega el acceso en función de las directivas definidas en cada servidor VPN local.

En implementaciones más grandes y escalables, las directivas que conceden o deniegan el acceso a la VPN están centralizadas en servidores RADIUS. En este caso, el servidor VPN actúa como un servidor de acceso (cliente RADIUS) que reenvía las solicitudes de conexión y mensajes de la cuenta a un servidor RADIUS. Para conectarse al puerto virtual en el servidor VPN, los usuarios deben autenticarse y cumplir las condiciones definidas de forma centralizada en servidores RADIUS. 

Cuando la extensión NPS para Azure está integrada con el NPS, el flujo de una autenticación correcta es como sigue:

1. El servidor VPN recibe una solicitud de autenticación de un usuario de VPN que incluye el nombre de usuario y contraseña para conectarse a un recurso, como una sesión de escritorio remoto. 
2. Actuando como un cliente RADIUS, el servidor VPN convierte la solicitud en un mensaje de solicitud de acceso RADIUS y envía el mensaje (la contraseña se cifra) al servidor RADIUS (NPS) donde está instalada la extensión NPS. 
3. La combinación de nombre de usuario y contraseña se verifica en Active Directory. Si el nombre de usuario y contraseña son incorrectos, el servidor RADIUS envía un mensaje de rechazo de acceso. 
4. Si se cumplen todas las condiciones especificadas en la solicitud de conexión NPS y las directivas de red (por ejemplo, la hora del día o restricciones por pertenencia a un grupo), la extensión NPS desencadena una solicitud de autenticación secundaria con Azure MFA. 
5. Azure MFA se comunica con Azure Active Directory, recupera los detalles del usuario y realiza la autenticación secundaria con el método de verificación configurado por el usuario (mensaje de texto, aplicación móvil, etc). 
6. Cuando se realiza correctamente el desafío de MFA, Azure MFA comunica el resultado a la extensión NPS.
7. Después de que el intento de conexión se autentica y autoriza, el servidor NPS donde está instalada la extensión envía un mensaje de aceptación de acceso RADIUS al servidor VPN (cliente RADIUS).
8. Se concede acceso al usuario al puerto virtual en el servidor VPN y se establece un túnel VPN cifrado.

## <a name="prerequisites"></a>Requisitos previos
En esta sección se detallan los requisitos previos necesarios antes de integrar Azure MFA con la puerta de enlace de Escritorio remoto. Antes de comenzar, debe cumplir los siguientes requisitos previos.

* Infraestructura de VPN
* Directiva de red y rol de servicios de acceso (NPS)
* Licencia de Azure MFA
* Software de Windows Server
* Bibliotecas
* Azure AD sincronizado con AD local 
* Identificador de GUID de Azure Active Directory

### <a name="vpn-infrastructure"></a>Infraestructura de VPN
En este artículo se da por supuesto que tiene una infraestructura VPN en funcionamiento con Microsoft Windows Server 2016 y que el servidor VPN actualmente no está configurado para reenviar solicitudes de conexión a un servidor RADIUS. En esta guía se va a configurar la infraestructura de VPN para usar un servidor RADIUS central.

Si no tiene una infraestructura en funcionamiento, puede crear rápidamente esta infraestructura siguiendo las instrucciones indicadas en numerosos tutoriales de instalación de VPN que puede encontrar en sitios de terceros y de Microsoft. 

### <a name="network-policy-and-access-services-nps-role"></a>Directiva de red y rol de servicios de acceso (NPS)

El servicio de rol NPS proporciona la funcionalidad de cliente y servidor RADIUS. En este artículo se da por supuesto que ha instalado el rol NPS en un servidor miembro o en un controlador de dominio en su entorno. En esta guía configurará RADIUS para una configuración de VPN. Instalación del rol NPS en un servidor _distinto_ del servidor VPN.

Para obtener información acerca de cómo instalar el servicio de rol NPS en Windows Server 2012 o posterior, consulte [Instalación de un servidor de directivas de mantenimiento de NAP](https://technet.microsoft.com/library/dd296890.aspx). La directiva de acceso a redes (NAP) está en desuso en Windows Server 2016. Para obtener una descripción de las prácticas recomendadas para NPS, incluida la recomendación de instalar NPS en un controlador de dominio, consulte [Prácticas recomendadas para NPS](https://technet.microsoft.com/library/cc771746).

### <a name="licenses"></a>Licencias

Es necesaria una licencia para Azure MFA, que está disponible a través de una suscripción de Azure AD Premium, de Enterprise Mobility + Security (EMS) o de MFA. Para más información, consulte [Cómo obtener Azure Multi-Factor Authentication](multi-factor-authentication-versions-plans.md). Para realizar pruebas, puede usar una suscripción de evaluación.

### <a name="software"></a>Software

La extensión NPS requiere Windows Server 2008 R2 SP1 o posterior con el servicio de rol NPS instalado. Todos los pasos de esta guía se han realizado con Windows Server 2016.

### <a name="libraries"></a>Bibliotecas

Se requieren las dos bibliotecas siguientes:

* [Paquetes redistribuibles de Visual C++ para Visual Studio 2013 (X64)](https://www.microsoft.com/download/details.aspx?id=40784)
* _Módulo Microsoft Azure Active Directory para Windows PowerShell versión1.1.166.0_ o posterior. Para la versión más reciente e instrucciones de instalación, consulte [Historial de versiones del Módulo Microsoft Azure Active Directory para PowerShell](https://social.technet.microsoft.com/wiki/contents/articles/28552.microsoft-azure-active-directory-powershell-module-version-release-history.aspx).

Estas bibliotecas no se empaquetan con los archivos de instalación de la extensión NPS (versión 0.9.1.2), a pesar de que en la documentación existente se indique lo contrario. Como mínimo, debe instalar los paquetes redistribuibles de Visual C++ para Visual Studio 2013. El módulo Microsoft Azure Active Directory para Windows PowerShell se instala, si todavía no está presente, a través de un script de configuración que se ejecuta como parte del proceso de instalación. No es necesario instalar este módulo con antelación si aún no está instalado.

### <a name="azure-active-directory-synched-with-on-premises-active-directory"></a>Azure Active Directory sincronizado con Active Directory local 

Para usar la extensión NPS, los usuarios locales deben estar sincronizados con Azure Active Directory y habilitados para Multi-Factor Authentication. En esta guía se da por supuesto que los usuarios locales están sincronizados con Azure Active Directory mediante AD Connect. A continuación, se proporcionan instrucciones para habilitar a los usuarios para usar MFA.
Para obtener información sobre Azure AD Connect, consulte [Integración de los directorios locales con Azure Active Directory](../active-directory/connect/active-directory-aadconnect.md). 

### <a name="azure-active-directory-guid-id"></a>Identificador de GUID de Azure Active Directory 
Para instalar NPS, debe conocer el GUID de Azure Active Directory. En la siguiente sección se proporcionan instrucciones para buscar el GUID de Azure Active Directory.

## <a name="configure-radius-for-vpn-connections"></a>Configuración de RADIUS para conexiones VPN

Si ha instalado el rol de servidor NPS en un servidor miembro, debe configurarlo para autenticar y autorizar clientes VPN que soliciten conexiones VPN. 

En esta sección se supone que ha instalado el rol de servidor de directivas de redes pero no lo ha configurado para su uso en su infraestructura.

>[!NOTE]
>Si ya tiene un servidor VPN en funcionamiento que usa un servidor RADIUS centralizado para la autenticación, puede omitir esta sección.
>

### <a name="register-server-in-active-directory"></a>Registro del servidor en Active Directory
Para que funcione correctamente en este escenario, el servidor NPS debe estar registrado en Active Directory.

1. Abra el Administrador del servidor.
2. En el Administrador del servidor, haga clic en **Herramientas** y, a continuación, haga clic en **Servidor de directivas de redes**. 
3. En la consola del servidor de directivas de redes, haga clic con el botón derecho en **NPS (Local)** y, a continuación, haga clic en **Registrar el servidor en Active Directory**. Haga clic en **Aceptar** dos veces.

 ![Servidor de directivas de redes](./media/nps-extension-vpn/image2.png)

4. Deje la consola abierta para el siguiente procedimiento.

### <a name="use-wizard-to-configure-radius-server"></a>Utilice el asistente para configurar el servidor RADIUS
Puede usar la opción de configuración estándar (basada en asistente) o la opción avanzada para configurar el servidor RADIUS. Esta sección supone el uso de la opción de configuración estándar con el asistente.

1. En la consola del Servidor de directivas de redes, haga clic en **NPS (Local)**.
2. En la configuración estándar, seleccione **Servidor RADIUS para conexiones telefónicas o VPN** y, a continuación, haga clic en **Configurar VPN o acceso telefónico**.

 ![Configuración de VPN](./media/nps-extension-vpn/image3.png)

3. En la página Seleccione tipo de conexión de acceso telefónico o red privada virtual, seleccione **Conexiones de red privada virtual** y haga clic en **Siguiente**.

 ![Red privada virtual](./media/nps-extension-vpn/image4.png)

4. En la página Especificar servidor de acceso telefónico o VPN, haga clic en **Agregar**.
5. En el cuadro de diálogo **Nuevo cliente RADIUS**, proporcione un nombre descriptivo, escriba el nombre para resolver o la dirección IP del servidor VPN y escriba una contraseña secreta compartida. Elija una contraseña secreta compartida larga y compleja. Registre esta contraseña, será necesaria para los pasos de la sección siguiente.

 ![Nuevo cliente RADIUS](./media/nps-extension-vpn/image5.png)

6. Haga clic en **Aceptar** y, a continuación, en **Siguiente**.
7. En la página **Configurar métodos de autenticación**, acepte la selección predeterminada (Microsoft Encrypted Authentication versión 2 [MS-CHAPv2]) o elija otra opción y haga clic en **Siguiente**.

  >[!NOTE]
  >Si configura el Protocolo de autenticación extensible (EAP), debe usar MS CHAPv2 o PEAP. No se admite ningún otro tipo de EAP.
 
8. En la página Especificar grupos de usuarios, haga clic en **Agregar** y seleccione un grupo apropiado, si existe alguno. En caso contrario, deje la selección en blanco para conceder acceso a todos los usuarios.

 ![Especificar grupos de usuarios](./media/nps-extension-vpn/image7.png)

9. Haga clic en **Siguiente**.
10. En la página Especificar filtros IP, haga clic en **Siguiente**.
11. En la página Especificar la configuración de cifrado, acepte la configuración predeterminada y haga clic en **Siguiente**.

 ![Especificar cifrado](./media/nps-extension-vpn/image8.png)

12. En Especificar un nombre de dominio Kerberos, deje el nombre en blanco, acepte la configuración predeterminada y haga clic en **Siguiente**.

 ![Especificar un nombre de dominio Kerberos](./media/nps-extension-vpn/image9.png)

13. En la página Finalizando nueva conexión de acceso telefónico o VPN y cliente RADIUS, haga clic en **Finalizar**.

 ![Conexiones finalizadas](./media/nps-extension-vpn/image10.png)

### <a name="verify-radius-configuration"></a>Comprobación de la configuración de RADIUS
En esta sección se detalla la configuración creada con el asistente.

1. En el servidor NPS, en la consola NPS (Local), expanda Clientes RADIUS y seleccione **Clientes RADIUS**.
2. En el panel de detalles, haga clic con el botón derecho en el cliente RADIUS que ha creado con el asistente y haga clic en **Propiedades**. Las propiedades del cliente RADIUS (el servidor VPN) deben ser similares a las que se muestran a continuación.

 ![Propiedades de VPN](./media/nps-extension-vpn/image11.png)

3. Haga clic en **Cancelar**.
4. En el servidor NPS, en la consola NPS (Local), expanda **Directivas** y seleccione **Directivas de solicitud de conexión**. Debería ver la directiva de conexiones VPN, con un aspecto similar a la imagen siguiente.

 ![Solicitudes de conexión](./media/nps-extension-vpn/image12.png)

5. En Directivas, seleccione **Directivas de red**. Debería ver una directiva de conexiones de red privada virtual (VPN), con un aspecto similar a la imagen siguiente.

 ![Propiedades de red](./media/nps-extension-vpn/image13.png)

## <a name="configure-vpn-server-to-use-radius-authentication"></a>Configuración del servidor VPN para que utilice la autenticación RADIUS
En esta sección, configurará el servidor VPN para utilizar la autenticación RADIUS. En esta sección se da por supuesto que tiene una configuración de servidor VPN en funcionamiento, pero no se ha configurado el servidor VPN para utilizar la autenticación RADIUS. Después de configurar el servidor VPN, confirme que la configuración funciona según lo previsto.

>[!NOTE]
>Si ya tiene una configuración de servidor VPN en funcionamiento que usa autenticación RADIUS, puede omitir esta sección.
>

### <a name="configure-authentication-provider"></a>Configuración del proveedor de autenticación
1. En el servidor VPN, abra el Administrador del servidor.
2. En el Administrador del servidor, haga clic en **Herramientas** y, a continuación, en **Enrutamiento y acceso remoto**.
3. En la consola de Enrutamiento y acceso remoto, haga clic con el botón derecho en  **\[nombre del servidor\] (local)** y, a continuación, haga clic en **Propiedades**.

 ![Enrutamiento y acceso remoto](./media/nps-extension-vpn/image14.png)
 
4. En el cuadro de diálogo **Propiedades de [nombre del servidor} (local)**, haga clic en la pestaña **Seguridad**. 
5. En la pestaña **Seguridad**, en Proveedor de autenticación, haga clic en **Autenticación RADIUS** y, a continuación, en **Configurar**.

 ![Autenticación RADIUS](./media/nps-extension-vpn/image15.png)
 
6. En el cuadro de diálogo Autenticación RADIUS, haga clic en **Agregar**.
7. En Agregar servidor RADIUS, en nombre del servidor, agregue el nombre o la dirección IP del servidor RADIUS que configuró en la sección anterior.
8. En Secreto compartido, haga clic en **Cambiar** y agregue la contraseña secreta compartida que creó y anotó anteriormente.
9. En Tiempo de espera (segundos), cambie el valor a un valor entre **30** y **60**. Esto es necesario para dar tiempo suficiente para completar el segundo factor de autenticación.
 
 ![Agregar servidor RADIUS](./media/nps-extension-vpn/image16.png)
 
10. Haga clic en **Aceptar** hasta cerrar todos los cuadros de diálogo.

### <a name="test-vpn-connectivity"></a>Probar la conectividad VPN
En esta sección, confirmará que el cliente VPN se autentica y es autorizado por el servidor RADIUS cuando intenta conectarse al puerto virtual de VPN. En esta sección se supone que usa Windows 10 como cliente de VPN. 

>[!NOTE]
>Si ya tiene configurado un cliente VPN para conectarse al servidor VPN y ha guardado la configuración, puede omitir los pasos descritos para configurar y guardar un objeto de conexión VPN.
>

1. En el equipo del cliente VPN, haga clic en **Inicio** y, a continuación, en **Configuración** (icono de engranaje).
2. En configuración de Windows, haga clic en **Red e Internet**.
3. Haga clic en **VPN**.
4. Haga clic en **Agregar una conexión VPN**.
5. En Agregar una conexión VPN, especifique Windows (integrada) como el Proveedor de VPN, a continuación, complete los campos restantes según corresponda y haga clic en **Guardar**. 

 ![Agregar una conexión VPN](./media/nps-extension-vpn/image17.png)
 
6. Abra el **Centro de redes y recursos compartidos** en el Panel de Control.
7. Haga clic en **Cambiar configuración del adaptador**.

 ![Cambiar configuración del adaptador](./media/nps-extension-vpn/image18.png)

8. Haga clic con el botón derecho en la conexión de red VPN y haga clic en Propiedades. 

 ![Propiedades de red VPN](./media/nps-extension-vpn/image19.png)

9. En el cuadro de diálogo Propiedades de VPN, haga clic en la pestaña **Seguridad**. 
10. En la pestaña Seguridad, asegúrese de que solo está seleccionado **Microsoft CHAP versión 2 (MS-CHAP v2)** y haga clic en Aceptar.

 ![Protocolos permitidos](./media/nps-extension-vpn/image20.png)

11. Haga clic con el botón derecho en la conexión VPN y haga clic en **Conectar**.
12. En la página Configuración, haga clic en **Conectar**.

Aparece una conexión correcta en el registro de seguridad en el servidor RADIUS con el identificador de evento 6272, como se muestra a continuación.

 ![Propiedades de evento](./media/nps-extension-vpn/image21.png)

## <a name="troubleshoot-guide"></a>Guía de solución de problemas
Se supone que la configuración de VPN funcionaba correctamente antes de configurar el servidor VPN para que utilice un servidor RADIUS centralizado para la autenticación y autorización. En este caso, es probable que el problema pueda deberse a una configuración incorrecta del servidor RADIUS o al uso de un nombre de usuario o contraseña no válidos. Por ejemplo, si usa el sufijo UPN alternativo en el nombre de usuario, el intento de inicio de sesión puede producir un error (debe usar el mismo nombre de cuenta para obtener los mejores resultados). 

Para solucionar estos problemas, un lugar ideal para comenzar es examinar los registros de eventos de seguridad en el servidor RADIUS. Para ahorrar tiempo buscando eventos, puede utilizar la vista personalizada basada en roles Servidor de directivas de red y acceso en el Visor de eventos, como se muestra a continuación. El identificador de evento 6273 indica eventos en los que el servidor de directivas de redes deniega el acceso a un usuario. 

 ![Visor de eventos](./media/nps-extension-vpn/image22.png)
 
## <a name="configure-multi-factor-authentication"></a>Configuración de Multi-Factor Authentication
Esta sección proporciona instrucciones para habilitar a los usuarios para MFA y para configurar las cuentas para la verificación en dos pasos. 

### <a name="enable-multi-factor-authentication"></a>Habilitar Multi-Factor Authentication
En esta sección, se habilitan cuentas de Azure AD para MFA. Use el **portal clásico** para habilitar a los usuarios para MFA. 

1. Abra un explorador y vaya a [https://manage.windowsazure.com](https://manage.windowsazure.com). 
2. Inicie sesión como administrador.
3. En el panel de navegación izquierdo del portal, haga clic en **Active Directory**.

 ![Directorio predeterminado](./media/nps-extension-vpn/image23.png)

4. En la columna nombre, haga clic en **Directorio predeterminado** (o en otro directorio, si procede).
5. En la página Inicio rápido, haga clic en **Configurar**.

 ![Configurar valor predeterminado](./media/nps-extension-vpn/image24.png)

6. En la página Configurar, desplácese hacia abajo y, en la sección de la autenticación multifactor, haga clic en **Administrar el valor de configuración del servicio**.

 ![Administrar la configuración de MFA](./media/nps-extension-vpn/image25.png)
 
7. En la página de la autenticación multifactor, revise la configuración predeterminada del servicio y, a continuación, haga clic en **Usuarios**. 

 ![Usuarios de MFA](./media/nps-extension-vpn/image26.png)
 
8. En la página Usuarios, seleccione los usuarios que desea habilitar para MFA y, a continuación, haga clic en **Habilitar**.

 ![Propiedades](./media/nps-extension-vpn/image27.png)
 
9. Cuando se le solicite, haga clic en **Habilitar autenticación multifactor**.

 ![Habilitar MFA](./media/nps-extension-vpn/image28.png)
 
10. Haga clic en **Cerrar**. 
11. Actualice la página. Se cambia el estado de MFA a Habilitado.

Para información sobre cómo habilitar usuarios para Multi-Factor Authentication, consulte [Introducción a Azure Multi-Factor Authentication en la nube](multi-factor-authentication-get-started-cloud.md). 

### <a name="configure-accounts-for-two-step-verification"></a>Configuración de cuentas para la verificación en dos pasos
Una vez que una cuenta se ha habilitado para MFA, los usuarios no pueden iniciar sesión en los recursos controlados por la directiva MFA hasta que hayan configurado correctamente un dispositivo de confianza que se utilizará para el segundo factor de autenticación de la verificación en dos pasos.

En esta sección, configurará un dispositivo de confianza para su uso con la verificación en dos pasos. Hay varias opciones disponibles para configurar estos elementos, incluidas las siguientes:

* **Aplicación móvil**. Instale la aplicación Microsoft Authenticator en un dispositivo Windows Phone, Android o iOS. Según las directivas de su organización, deberá usar la aplicación en uno de estos dos modos: Recibir notificaciones de comprobación (se envía una notificación al dispositivo) o Usar código de comprobación (deberá escribir un código de comprobación que se actualiza cada 30 segundos). 
* **Llamada de teléfono móvil o mensaje de texto**. Puede recibir un mensaje de texto o una llamada de teléfono automática. En la opción de llamada de teléfono, debe responder a la llamada y presionar el signo # para autenticarse. En la opción de mensaje de texto, puede responder al mensaje o escribir el código de comprobación en la interfaz de inicio de sesión.
* **Llamada de teléfono de la oficina**. Este proceso es el mismo que se ha descrito para las llamadas de teléfono automatizadas anteriores.

Siga estas instrucciones para configurar un dispositivo para usar la aplicación móvil para recibir una notificación push para la comprobación.

1. Inicie sesión en [https://aka.ms/mfasetup](https://aka.ms/mfasetup) o en cualquier otro sitio, como [https://portal.azure.com](https://portal.azure.com), donde sea necesario autenticarse con sus credenciales basadas en MFA. 
2. Al iniciar sesión con su nombre de usuario y contraseña, se le presentará una pantalla que le pregunta si desea configurar la cuenta para la comprobación de seguridad adicional.

 ![Seguridad adicional](./media/nps-extension-vpn/image29.png)

3. Haga clic en **Configurar ahora**.
4. En la página Comprobación de seguridad adicional, seleccione un tipo de contacto (aplicación móvil, teléfono del trabajo o teléfono de autenticación). A continuación, seleccione un país o región y seleccione un método. El método varía según el tipo de contacto que seleccione. Por ejemplo, si elige Aplicación móvil, puede seleccionar si desea recibir notificaciones de comprobación o usar un código de comprobación. En los pasos siguientes se supone que ha elegido **Aplicación móvil** como el tipo de contacto.

 ![Teléfono de autenticación](./media/nps-extension-vpn/image30.png)

5. Seleccione Aplicación móvil, haga clic en **Recibir notificaciones de comprobación** y, a continuación, haga clic en **Configurar**. 

 ![Comprobación con aplicación móvil](./media/nps-extension-vpn/image31.png)
 
6. Si aún no lo ha hecho, instale la aplicación móvil Authenticator en el dispositivo. 
7. Siga las instrucciones de la aplicación móvil para examinar el código de barras que se le presenta o escriba manualmente la información y, a continuación, haga clic en **Listo**.

 ![Configuración de la aplicación móvil](./media/nps-extension-vpn/image32.png)

8. En la página Comprobación de seguridad adicional, haga clic en **Contactarme** y responda a la notificación que se envía al dispositivo.
9. En la página Comprobación de seguridad adicional, escriba un número de contacto para utilizar en caso de que pierda el acceso a la aplicación móvil y haga clic en **Siguiente**.

 ![Número de teléfono móvil](./media/nps-extension-vpn/image33.png)
 
10. En Comprobación de seguridad adicional, haga clic en **Listo**.

El dispositivo está configurado ahora para proporcionar un segundo método de comprobación. Para obtener información sobre cómo configurar cuentas para la verificación en dos pasos, consulte [Configurar mi cuenta para la verificación en dos pasos](./end-user/multi-factor-authentication-end-user-first-time.md).

## <a name="install-and-configure-nps-extension"></a>Instalación y configuración de la extensión NPS

Esta sección proporciona instrucciones para configurar la VPN para usar Azure MFA para la autenticación de cliente con el servidor de VPN.

Una vez que instale y configure la extensión NPS, toda la autenticación de cliente basada en RADIUS procesada por este servidor debe usar Azure MFA. Si todos los usuarios de VPN no están inscritos en Azure MFA, puede configurar otro servidor RADIUS para autenticar a los usuarios que no están configurados para usar MFA. O bien, puede crear una entrada del registro que permite a sus usuarios proporcionar un segundo factor de autenticación solo si están inscritos en MFA. 

Cree un nuevo valor de cadena denominado _REQUIRE_USER_MATCH en HKLM\SOFTWARE\Microsoft\AzureMfa_ y establezca el valor como TRUE o FALSE. 

 ![Require User Match (Requerir coincidencia de usuario)](./media/nps-extension-vpn/image34.png)
 
Si el valor se establece en TRUE o no está establecido, todas las solicitudes de autenticación están sujetas a un desafío MFA. Si el valor se establece en FALSE, se emiten desafíos MFA únicamente a los usuarios que están inscritos en MFA. Use solo el valor FALSE en las pruebas o en entornos de producción durante un período de incorporación.

### <a name="acquire-azure-active-directory-guid-id"></a>Obtener el identificador de GUID de Azure Active Directory

Como parte de la configuración de la extensión NPS, debe proporcionar las credenciales de administrador y el identificador de Azure Active Directory para el inquilino de Azure AD. Los pasos siguientes muestran cómo obtener el identificador del inquilino.

1. Inicie sesión en Azure Portal en [https://portal.azure.com](https://portal.azure.com) como administrador global del inquilino de Azure.
2. En la barra de navegación de la izquierda, haga clic en el icono de **Azure Active Directory**.
3. Haga clic en **Propiedades**.
4. Para copiar el identificador de directorio en el Portapapeles, seleccione el icono **Copiar**.
 
 ![Identificador de directorio](./media/nps-extension-vpn/image35.png)

### <a name="install-the-nps-extension"></a>Instalación de la extensión de NPS
La extensión NPS debe instalarse en un servidor que tenga el rol Directiva de red y servicios de acceso (NPS) instalado y que funcione como servidor RADIUS en el diseño. No instale la extensión NPS en el servidor de Escritorio remoto.

1. Puede descargar la extensión NPS de [https://aka.ms/npsmfa](https://aka.ms/npsmfa). 
2. Copie el archivo ejecutable de instalación (NpsExtnForAzureMfaInstaller.exe) en el servidor NPS.
3. En el servidor NPS, haga doble clic en **NpsExtnForAzureMfaInstaller.exe**. Cuando se le solicite, haga clic en **Ejecutar**.
4. En el cuadro de diálogo Extensión NPS para Azure MFA, revise los términos de licencia de software, marque la casilla **Acepto los términos de licencia y condiciones** y haga clic en **Instalar**.

 ![Extensión NPS](./media/nps-extension-vpn/image36.png)
 
5. En el cuadro de diálogo Extensión NPS para Azure MFA, haga clic en **Cerrar**.  

 ![Instalación correcta](./media/nps-extension-vpn/image37.png) 
 
### <a name="configure-certificates-for-use-with-the-nps-extension-using-a-powershell-script"></a>Configuración de los certificados para su uso con la extensión NPS mediante un script de PowerShell
Debe configurar los certificados para su uso por la extensión NPS para garantizar la seguridad de las comunicaciones. Los componentes de NPS incluyen un script de Windows PowerShell que configura un certificado autofirmado para su uso con NPS. 

Este script realiza las acciones siguientes:

* Crea un certificado autofirmado
* Asocia la clave pública del certificado a la entidad de servicio en Azure AD
* Almacena el certificado en el almacén de certificados del equipo local
* Concede acceso a la clave privada del certificado al usuario de red
* Reinicia el servicio Servidor de directivas de redes

Si desea utilizar sus propios certificados, debe asociar la clave pública de su certificado para la entidad de servicio en Azure AD, etc.
Para usar el script, indique a la extensión sus credenciales de administrador de Azure Active Directory y el identificador del inquilino de Azure Active Directory que copió anteriormente. Ejecute el script en cada servidor NPS donde instaló la extensión NPS.

1. Abra un símbolo del sistema administrativo de Windows PowerShell.
2. En el símbolo del sistema de PowerShell, escriba _cd 'c:\Program Files\Microsoft\AzureMfa\Config'_ y presione **ENTRAR**.
3. Escriba _.\AzureMfsNpsExtnConfigSetup.ps1_ y presione **ENTRAR**. 
 * El script comprueba si está instalado el módulo de PowerShell de Azure Active Directory. Si no está instalado, el script instala el módulo.
 
 ![PowerShell](./media/nps-extension-vpn/image38.png)
 
4. Una vez que el script comprueba la instalación del módulo de PowerShell, muestra el cuadro de diálogo del módulo de PowerShell de Azure Active Directory. En el cuadro de diálogo, escriba sus credenciales de administrador de Azure AD y la contraseña y haga clic en **Iniciar sesión**. 
 
 ![Inicio de sesión de PowerShell](./media/nps-extension-vpn/image39.png)
 
5. Cuando se le solicite, pegue el identificador del inquilino que copió al Portapapeles anteriormente y presione **ENTRAR**. 

 ![Id. de inquilino](./media/nps-extension-vpn/image40.png)

6. El script crea un certificado autofirmado y realiza otros cambios en la configuración. La salida es similar a la imagen que se muestra a continuación.

 ![Certificado autofirmado](./media/nps-extension-vpn/image41.png)

7. Reinicie el servidor.
 
### <a name="verify-configuration"></a>Comprobación de la configuración
Para comprobar la configuración, debe establecer una conexión VPN nueva con el servidor VPN. Después de escribir correctamente las credenciales para la autenticación principal, la conexión VPN espera a que la autenticación secundaria sea correcta para establecer la conexión, como se muestra a continuación. 

 ![Comprobación de la configuración](./media/nps-extension-vpn/image42.png)

Si se autentica correctamente con el método de comprobación secundario que ha configurado previamente en Azure MFA, se conecta al recurso. Sin embargo, si la autenticación secundaria no se realiza correctamente, se deniega el acceso al recurso. 

En el ejemplo siguiente, la aplicación Authenticator en un dispositivo Windows Phone se utiliza para proporcionar la autenticación secundaria.

 ![Comprobar cuenta](./media/nps-extension-vpn/image43.png)

Una vez que se haya autenticado correctamente con el método secundario, se le concede acceso al puerto virtual en el servidor VPN. Sin embargo, como ha sido necesario usar un método de autenticación secundario con una aplicación móvil en un dispositivo de confianza, el proceso de inicio de sesión es más seguro que utilizando solo una combinación de nombre de usuario y contraseña.

### <a name="view-event-viewer-logs-for-successful-logon-events"></a>Ver los registros del Visor de eventos para eventos de inicio de sesión correcto
Para ver los eventos de inicio de sesión correctos en los registros del Visor de eventos de Windows, puede utilizar el siguiente comando de Windows PowerShell para consultar el registro de seguridad de Windows en el servidor NPS.

Para consultar los eventos de inicio de sesión correcto en los registros del Visor de eventos de seguridad, use el comando siguiente,
* _Get-WinEvent -Logname Security_ | where {$_.ID -eq '6272'} | FL 

 ![Visor de eventos de seguridad](./media/nps-extension-vpn/image44.png)
 
También puede ver el registro de seguridad o la vista personalizada de Directivas de red y servicios de acceso, tal y como se muestra a continuación:

 ![Acceso a la directiva de red](./media/nps-extension-vpn/image45.png)

En el servidor donde instaló la extensión NPS para Azure MFA, puede encontrar registros de aplicación del Visor de eventos específicos de la extensión en **Application and Services Logs\Microsoft\AzureMfa**. 

* _Get-WinEvent -Logname Security_ | where {$_.ID -eq '6272'} | FL

 ![Número de eventos](./media/nps-extension-vpn/image46.png)

## <a name="troubleshoot-guide"></a>Guía de solución de problemas
Si la configuración no funciona según lo esperado, un buen lugar para comenzar es comprobar que el usuario está configurado para usar Azure MFA. Haga que el usuario se conecte a [https://portal.azure.com](https://portal.azure.com). Si a los usuarios se les solicita la autenticación secundaria y se pueden autenticar correctamente, puede descartar una configuración incorrecta de Azure MFA.

Si Azure MFA está funcionando para los usuarios, debe revisar los registros de eventos pertinentes. Esto incluye los eventos de seguridad, de operativa de la puerta de enlace y los registros de Azure MFA que se describen en la sección anterior. 

A continuación, se muestra un ejemplo de salida del registro de seguridad que muestra un evento de inicio de sesión incorrecto (identificador de evento 6273):

 ![Registro de seguridad](./media/nps-extension-vpn/image47.png)

A continuación, se muestra un evento relacionado de los registros de AzureMFA:

 ![Registros de Azure MFA](./media/nps-extension-vpn/image48.png)

Para realizar opciones avanzadas de solución de problemas, consulte los archivos de registro de formato de la base de datos de NPS donde está instalado el servicio NPS. Estos archivos de registro se crean en la carpeta _%SystemRoot%\System32\Logs_ como archivos de texto delimitado por comas. Para obtener una descripción de estos archivos de registro, consulte [Interpretación de los archivos de registro de formato de la base de datos de NPS](https://technet.microsoft.com/library/cc771748.aspx). 

Las entradas de estos archivos de registro son difíciles de interpretar sin importarlos en una hoja de cálculo o una base de datos. Puede encontrar varios analizadores de IAS en línea que le ayudarán a interpretar los archivos de registro. A continuación, se muestra la salida de una [aplicación shareware](http://www.deepsoftware.com/iasviewer) que se puede descargar: 

 ![Aplicación shareware](./media/nps-extension-vpn/image49.png)

Por último, para más opciones de solución de problemas, puede utilizar un analizador de protocolos, como Wireshark o el [Analizador de mensajes de Microsoft](https://technet.microsoft.com/library/jj649776.aspx). La siguiente imagen de Wireshark muestra los mensajes RADIUS entre el servidor VPN y el servidor NPS.

 ![Analizador de mensajes de Microsoft](./media/nps-extension-vpn/image50.png)

Para más información, consulte [Integración de la infraestructura NPS existente con Azure Multi-Factor Authentication](multi-factor-authentication-nps-extension.md).  

## <a name="next-steps"></a>Pasos siguientes
[Cómo conseguir Azure Multi-Factor Authentication](multi-factor-authentication-versions-plans.md)

[Puerta de enlace de Escritorio remoto y Servidor Azure Multi-Factor Authentication con RADIUS](multi-factor-authentication-get-started-server-rdg.md)

[Integración de los directorios locales con Azure Active Directory](../active-directory/connect/active-directory-aadconnect.md)


