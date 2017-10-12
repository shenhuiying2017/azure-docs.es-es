---
title: "Integración de la puerta de enlace de Escritorio remoto con la extensión NPS de Azure MFA | Microsoft Docs"
description: "Este artículo describe la integración de la infraestructura de la puerta de enlace de Escritorio remoto con Azure MFA utilizando la extensión Servidor de directivas de redes (NPS) para Microsoft Azure."
services: active-directory
keywords: "Azure MFA, integración de puerta de enlace de Escritorio remoto, Azure Active Directory, extensión Servidor de directivas de redes"
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
ms.openlocfilehash: 6ff9a341b31e5005949dcc0ecb2591060269846e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
#  <a name="integrate-your-remote-desktop-gateway-infrastructure-using-the-network-policy-server-nps-extension-and-azure-ad"></a>Integración de la infraestructura de la puerta de enlace de Escritorio remoto utilizando la extensión Servidor de directivas de redes (NPS) y Azure AD

Este proporciona detalles para la integración de la infraestructura de la puerta de enlace de Escritorio remoto con Azure Multi-Factor Authentication (MFA) utilizando la extensión Servidor de directivas de redes (NPS) para Microsoft Azure. 

La extensión Servidor de directivas de redes (NPS) para Azure permite a los clientes proteger la autenticación de cliente del Servicio de autenticación remota telefónica de usuario (RADIUS) utilizando la autenticación basada en la nube [Multi-Factor Authentication (MFA)](multi-factor-authentication.md) de Azure. Esta solución proporciona una verificación en dos pasos para agregar una segunda capa de seguridad a los inicios de sesión y transacciones de los usuarios.

Este artículo proporciona instrucciones paso a paso para la integración de la infraestructura NPS con Azure MFA con la extensión NPS para Azure. Esto permite la comprobación de seguridad para los usuarios que intenten iniciar sesión en una puerta de enlace de Escritorio remoto. 

La directiva de red y los servicios de acceso (NPS) dan a las organizaciones la posibilidad de hacer lo siguiente:
* Definir ubicaciones centrales para la administración y el control de las solicitudes de red mediante la especificación de quién puede conectarse, las franjas horarias en las que se permiten conexiones, la duración de las conexiones y el nivel de seguridad que los clientes deben usar para conectarse y así sucesivamente. En vez de especificar estas directivas en cada VPN o servidor de puerta de enlace de Escritorio remoto (RD), estas directivas se especifican una vez en una ubicación central. El protocolo RADIUS proporciona autenticación, autorización y contabilización de cuentas (AAA) centralizadas. 
* Establecer y aplicar directivas de mantenimiento de cliente de Protección de acceso a redes (NAP) que determinan si los dispositivos tienen acceso restringido o sin restricciones a los recursos de la red.
* Proporcionar un medio para aplicar la autenticación y autorización para el acceso a puntos de acceso inalámbricos 802.1x y conmutadores Ethernet.    

Normalmente, las organizaciones usan NPS (RADIUS) para simplificar y centralizar la administración de directivas de VPN. Sin embargo, muchas organizaciones también usan NPS para simplificar y centralizar la administración de directivas de autorización de conexiones de Escritorio remoto (CAP de RD). 

Las organizaciones también pueden integrar NPS con Azure MFA para mejorar la seguridad y proporcionar un alto nivel de cumplimiento. Esto ayuda a asegurarse de que los usuarios establecen la verificación en dos pasos para iniciar sesión en la puerta de enlace de Escritorio remoto. Para tener acceso, los usuarios deben proporcionar su combinación de nombre de usuario y contraseña junto con información que el usuario tiene bajo su control. Esta información debe ser de confianza y no duplicable fácilmente, como un número de teléfono móvil, el número fijo o una aplicación en un dispositivo móvil, entre otros.

Antes de la disponibilidad de la extensión NPS para Azure, los clientes que querían implementar la verificación en dos pasos para entornos integrados de NPS y Azure MFA tenían que configurar y mantener un servidor independiente de MFA en el entorno local tal como se documenta en [Puerta de enlace de Escritorio remoto y Servidor Azure Multi-Factor Authentication con RADIUS](multi-factor-authentication-get-started-server-rdg.md).

La disponibilidad de la extensión NPS para Azure ahora da a las organizaciones la opción de implementar una solución MFA basada en el entorno local o una solución MFA basada en la nube para la autenticación segura de clientes RADIUS.

## <a name="authentication-flow"></a>Flujo de autenticación

Para que los usuarios obtengan acceso a los recursos de red a través de una puerta de enlace de Escritorio remoto, deben cumplir con las condiciones especificadas en una directiva de autorización de conexiones de Escritorio remoto (CAP de RD) y en una directiva de administración de recursos de Escritorio remoto (RAP de RD). Las CAP de RD especifican quién está autorizado para conectarse a las puertas de enlace de Escritorio remoto. Las RAP de RD especifican los recursos de red, como equipos de escritorio remoto o aplicaciones remotas, a los que el usuario tiene permiso para conectarse a través de la puerta de enlace de Escritorio remoto. 

Una puerta de enlace de Escritorio remoto se puede configurar para usar un almacén de directivas central para las CAP de RD. Las RAP de RD no pueden usar una directiva central, ya que se procesan en la puerta de enlace de Escritorio remoto. Un ejemplo de una puerta de enlace de Escritorio remoto configurada para usar un almacén de directivas central para las CAP de RD podría ser un cliente RADIUS a otro servidor NPS que actúa como el almacén de directivas central.

Cuando la extensión NPS para Azure está integrada con el NPS y la puerta de enlace de Escritorio remoto, el flujo de una autenticación correcta es como sigue:

1. El servidor de la puerta de enlace de Escritorio remoto recibe una solicitud de autenticación de un usuario de escritorio remoto para conectarse a un recurso, como una sesión de escritorio remoto. Actuando como un cliente RADIUS, el servidor de puerta de enlace de Escritorio remoto convierte la solicitud en un mensaje de solicitud de acceso RADIUS y envía el mensaje al servidor RADIUS (NPS) donde está instalada la extensión NPS. 
2. Se comprueba la combinación de nombre de usuario y contraseña en Active Directory y se autentica al usuario.
3. Si se cumplen todas las condiciones especificadas en la solicitud de conexión NPS y las directivas de red (por ejemplo, la hora del día o restricciones por pertenencia a un grupo), la extensión NPS desencadena una solicitud de autenticación secundaria con Azure MFA. 
4. Azure MFA se comunica con Azure AD, recupera los detalles del usuario y realiza la autenticación secundaria con el método de verificación configurado por el usuario (mensaje de texto, aplicación móvil, etc). 
5. Cuando se realiza correctamente el desafío de MFA, Azure MFA comunica el resultado a la extensión NPS.
6. El servidor NPS donde está instalada la extensión envía un mensaje de aceptación de acceso de RADIUS para la directiva CAP de RD al servidor de puerta de enlace de Escritorio remoto.
7. Se concede al usuario acceso al recurso de red solicitado a través de la puerta de enlace de Escritorio remoto.

## <a name="prerequisites"></a>Requisitos previos
En esta sección se detallan los requisitos previos necesarios antes de integrar Azure MFA con la puerta de enlace de Escritorio remoto. Antes de comenzar, debe cumplir los siguientes requisitos previos.  

* Infraestructura de Servicios de Escritorio remoto (RDS)
* Licencia de Azure MFA
* Software de Windows Server
* Directiva de red y rol de servicios de acceso (NPS)
* Azure AD sincronizado con AD local 
* Identificador de GUID de Azure Active Directory

### <a name="remote-desktop-services-rds-infrastructure"></a>Infraestructura de Servicios de Escritorio remoto (RDS)
Debe tener una infraestructura de Servicios de Escritorio remoto (RDS) en funcionamiento. Si no dispone de ella, puede crear rápidamente esta infraestructura en Azure con la siguiente plantilla de inicio rápido: [Creación de una implementación de una colección de sesiones de Escritorio remoto](https://github.com/Azure/azure-quickstart-templates/tree/ad20c78b36d8e1246f96bb0e7a8741db481f957f/rds-deployment). 

Si desea crear manualmente una infraestructura local de RDS rápidamente con fines de prueba, siga los pasos para implementar una. 
**Para más información**: [Implementación de RDS con la plantilla de inicio rápido de Azure](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/rds-in-azure) e [Implementación de la infraestructura de RDS básica](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/rds-deploy-infrastructure). 

### <a name="licenses"></a>Licencias
Es necesaria una licencia para Azure MFA, que está disponible a través de una suscripción de Azure AD Premium, de Enterprise Mobility + Security (EMS) o de MFA. Para más información, consulte [Cómo obtener Azure Multi-Factor Authentication](multi-factor-authentication-versions-plans.md). Para realizar pruebas, puede usar una suscripción de evaluación.

### <a name="software"></a>Software
La extensión NPS requiere Windows Server 2008 R2 SP1 o posterior con el servicio de rol NPS instalado. Todos los pasos de esta sección se han realizado con Windows Server 2016.

### <a name="network-policy-and-access-services-nps-role"></a>Directiva de red y rol de servicios de acceso (NPS)
El servicio de rol NPS proporciona la funcionalidad de servidor y cliente RADIUS, así como el servicio de mantenimiento de Directiva de acceso de red. Este rol debe instalarse en al menos dos equipos de la infraestructura: la puerta de enlace de Escritorio remoto y otro servidor miembro o controlador de dominio. De forma predeterminada, el rol ya está presente en el equipo configurado como puerta de enlace de Escritorio remoto.  También debe instalar el rol NPS al menos en otro equipo, como un servidor miembro o controlador de dominio.

Para obtener información acerca de cómo instalar el servicio de rol NPS en Windows Server 2012 o anterior, consulte [Instalación de un servidor de directivas de mantenimiento de NAP](https://technet.microsoft.com/library/dd296890.aspx). Para obtener una descripción de las prácticas recomendadas para NPS, incluida la recomendación de instalar NPS en un controlador de dominio, consulte [Prácticas recomendadas para NPS](https://technet.microsoft.com/library/cc771746).

### <a name="azure-active-directory-synched-with-on-premises-active-directory"></a>Azure Active Directory sincronizado con Active Directory local 
Para usar la extensión NPS, los usuarios locales deben estar sincronizados con Azure AD y habilitados para MFA. En esta sección se da por supuesto que los usuarios locales están sincronizados con Azure AD mediante AD Connect. Para obtener información sobre Azure AD Connect, consulte [Integración de los directorios locales con Azure Active Directory](../active-directory/connect/active-directory-aadconnect.md). 

### <a name="azure-active-directory-guid-id"></a>Identificador de GUID de Azure Active Directory
Para instalar NPS, debe conocer el GUID de Azure AD. A continuación, se proporcionan instrucciones para buscar el GUID de Azure AD.

## <a name="configure-multi-factor-authentication"></a>Configuración de Multi-Factor Authentication 
Esta sección proporciona instrucciones para la integración de Azure MFA con la puerta de enlace de Escritorio remoto. Como administrador, debe configurar el servicio Azure MFA antes de que los usuarios puedan registrar ellos mismos sus aplicaciones o dispositivos de varios factores.

Siga los pasos de [Introducción a Azure Multi-Factor Authentication en la nube](multi-factor-authentication-get-started-cloud.md) para habilitar MFA para los usuarios de Azure AD. 

### <a name="configure-accounts-for-two-step-verification"></a>Configuración de cuentas para la verificación en dos pasos
Una vez que una cuenta se ha habilitado para MFA, no puede iniciar sesión en los recursos controlados por la directiva MFA hasta que se haya configurado correctamente un dispositivo de confianza que se utilizará para el segundo factor de autenticación de la verificación en dos pasos.

Siga los pasos de [¿Qué significa Azure Multi-Factor Authentication para mí?](./end-user/multi-factor-authentication-end-user.md) para comprender y configurar correctamente los dispositivos para MFA con su cuenta de usuario.

## <a name="install-and-configure-nps-extension"></a>Instalación y configuración de la extensión NPS
Esta sección proporciona instrucciones para configurar la infraestructura de RDS para usar Azure MFA para la autenticación de cliente con la puerta de enlace de Escritorio remoto.

### <a name="acquire-azure-active-directory-guid-id"></a>Obtener el identificador de GUID de Azure Active Directory

Como parte de la configuración de la extensión NPS, debe proporcionar las credenciales de administrador y el identificador de Azure AD para el inquilino de Azure AD. Los pasos siguientes muestran cómo obtener el identificador del inquilino.

1. Inicie sesión en [Azure Portal](https://portal.azure.com) como administrador global del inquilino de Azure.
2. En la barra de navegación de la izquierda, seleccione el icono de **Azure Active Directory**.
3. Seleccione **Propiedades**.
4. En la hoja Propiedades, junto a la identificación de directorio, haga clic en el icono **Copiar**, tal y como se muestra a continuación, para copiar el identificador en el Portapapeles.

 ![Propiedades](./media/nps-extension-remote-desktop-gateway/image1.png)

### <a name="install-the-nps-extension"></a>Instalación de la extensión de NPS
Instale la extensión NPS en un servidor que tenga instalado el rol Servicios de acceso y directiva de red (NPS). Este actúa como el servidor RADIUS en su diseño. 

>[!Important]
> Asegúrese de no instalar la extensión NPS en el servidor de puerta de enlace de Escritorio remoto.
> 

1. Descargue la [extensión NPS](https://aka.ms/npsmfa). 
2. Copie el archivo ejecutable de instalación (NpsExtnForAzureMfaInstaller.exe) en el servidor NPS.
3. En el servidor NPS, haga doble clic en **NpsExtnForAzureMfaInstaller.exe**. Cuando se le solicite, haga clic en **Ejecutar**.
4. En el cuadro de diálogo Extensión NPS para Azure MFA, revise los términos de licencia de software, marque la casilla **Acepto los términos de licencia y condiciones** y haga clic en **Instalar**.
 
  ![Instalación de Azure MFA](./media/nps-extension-remote-desktop-gateway/image2.png)

5. En el cuadro de diálogo Extensión NPS para Azure MFA, haga clic en Cerrar. 

  ![Extensión NPS para Azure MFA](./media/nps-extension-remote-desktop-gateway/image3.png)

### <a name="configure-certificates-for-use-with-the-nps-extension-using-a-powershell-script"></a>Configuración de los certificados para su uso con la extensión NPS mediante un script de PowerShell
A continuación, debe configurar los certificados para su uso por la extensión NPS para garantizar la seguridad de las comunicaciones. Los componentes de NPS incluyen un script de Windows PowerShell que configura un certificado autofirmado para su uso con NPS. 

Este script realiza las acciones siguientes:

* Crea un certificado autofirmado
* Asocia la clave pública del certificado a la entidad de servicio en Azure AD
* Almacena el certificado en el almacén de certificados del equipo local
* Concede acceso a la clave privada del certificado al usuario de red
* Reinicia el servicio Servidor de directivas de redes

Si desea utilizar sus propios certificados, debe asociar la clave pública de su certificado para la entidad de servicio en Azure AD, etc.

Para usar el script, indique a la extensión sus credenciales de administrador de Azure AD y el identificador del inquilino de Azure AD que copió anteriormente. Ejecute el script en cada servidor NPS donde instaló la extensión NPS. A continuación, haga lo siguiente:

1. Abra un símbolo del sistema administrativo de Windows PowerShell.
2. En el símbolo del sistema de PowerShell, escriba **cd 'c:\Program Files\Microsoft\AzureMfa\Config'** y presione **ENTRAR**.
3. Escriba _.\AzureMfsNpsExtnConfigSetup.ps1_ y presione **ENTRAR**. El script comprueba si está instalado el módulo de PowerShell de Azure Active Directory. Si no está instalado, el script instala el módulo.

  ![PowerShell de Azure AD](./media/nps-extension-remote-desktop-gateway/image4.png)
  
4. Una vez que el script comprueba la instalación del módulo de PowerShell, muestra el cuadro de diálogo del módulo de PowerShell de Azure Active Directory. En el cuadro de diálogo, escriba sus credenciales de administrador de Azure AD y la contraseña y haga clic en **Iniciar sesión**.

  ![Abrir cuenta de Powershell](./media/nps-extension-remote-desktop-gateway/image5.png)

5. Cuando se le solicite, pegue el identificador del inquilino que copió al Portapapeles anteriormente y presione **ENTRAR**.

  ![Escriba el identificador del inquilino](./media/nps-extension-remote-desktop-gateway/image6.png)

6. El script crea un certificado autofirmado y realiza otros cambios en la configuración. La salida será similar a la imagen que se muestra a continuación.

  ![Certificado autofirmado](./media/nps-extension-remote-desktop-gateway/image7.png)

## <a name="configure-nps-components-on-remote-desktop-gateway"></a>Configuración de los componentes de NPS en la puerta de enlace de Escritorio remoto
En esta sección, configurará las directivas de autorización de conexión de la puerta de enlace de Escritorio remoto y otras opciones de RADIUS.

El flujo de autenticación requiere que se intercambien mensajes RADIUS entre la puerta de enlace de Escritorio remoto y el servidor NPS donde está instalado el servicio NPS. Esto significa que debe configurar los valores del cliente RADIUS tanto en la puerta de enlace de Escritorio remoto como en el servidor NPS donde está instalada la extensión NPS. 

### <a name="configure-remote-desktop-gateway-connection-authorization-policies-to-use-central-store"></a>Configuración de las directivas de autorización de conexiones de la puerta de enlace de Escritorio remoto para usar un almacén central
Las directivas de autorización de conexiones de Escritorio remoto (CAP de RD) especifican los requisitos para conectarse a un servidor de puerta de enlace de Escritorio remoto. Las CAP de RD pueden almacenarse localmente (valor predeterminado) o pueden almacenarse en un almacén de CAP de RD central que ejecute NPS. Para configurar la integración de Azure MFA con RDS, debe especificar el uso de un almacén central.

1. En el servidor de la puerta de enlace de Escritorio remoto, abra el **Administrador del servidor**. 
2. En el menú, haga clic en **Herramientas**, seleccione **Servicios de escritorio remoto** y, a continuación, haga clic en el **Administrador de puerta de enlace de Escritorio remoto**.

  ![Servicios de Escritorio remoto](./media/nps-extension-remote-desktop-gateway/image8.png)

3. En el Administrador de puerta de enlace de Escritorio remoto, haga clic con el botón derecho en  **\[Nombre del servidor\] (Local)** y haga clic en **Propiedades**.

  ![Nombre del servidor](./media/nps-extension-remote-desktop-gateway/image9.png)

4. En el cuadro de diálogo Propiedades, seleccione la pestaña Almacén de **CAP de RD**.
5. En la pestaña Almacén de CAP de RD, seleccione **Servidor central que ejecuta NPS**. 
6. En el campo **Escriba un nombre o dirección IP para el servidor que ejecuta NPS**, escriba la dirección IP o nombre del servidor donde instaló la extensión NPS.

  ![Escriba el nombre o dirección IP](./media/nps-extension-remote-desktop-gateway/image10.png)
  
7. Haga clic en **Agregar**.
8. En el cuadro de diálogo **Secreto compartido**, escriba un secreto compartido y, a continuación, haga clic en **Aceptar**. Asegúrese de registrar este secreto compartido y almacenar el registro de forma segura.

 >[!NOTE]
 >El secreto compartido se utiliza para establecer la confianza entre los clientes y servidores RADIUS. Cree una contraseña larga y compleja.
 >

 ![Secreto compartido](./media/nps-extension-remote-desktop-gateway/image11.png)

9. Haga clic en **Aceptar** para cerrar el cuadro de diálogo.

### <a name="configure-radius-timeout-value-on-remote-desktop-gateway-nps"></a>Configuración del valor de tiempo de espera de RADIUS en el NPS de la puerta de enlace de Escritorio remoto
Para asegurarse de que hay tiempo para validar las credenciales de los usuarios, realizar la verificación en dos pasos, recibir respuestas y responder a los mensajes de RADIUS, es necesario ajustar el valor de tiempo de espera de RADIUS.

1. En el servidor de la puerta de enlace de Escritorio remoto, en el administrador del servidor, haga clic en **Herramientas** y, a continuación, haga clic en **Servidor de directivas de redes**. 
2. En la consola de **NPS (Local)**, expanda **Clientes y servidores RADIUS** y seleccione **Servidor RADIUS remoto**.

 ![Servidor RADIUS remoto](./media/nps-extension-remote-desktop-gateway/image12.png)

3. En el panel de detalles, haga doble clic en **Grupo de servidores de puerta de enlace de TS**.

 >[!NOTE]
 >Este grupo de servidores RADIUS se creó al configurar el servidor de directivas NPS central. La puerta de enlace de Escritorio remoto reenvía los mensajes RADIUS a este servidor o grupo de servidores, si hay más de uno en el grupo.
 >

4. En el cuadro de diálogo **Propiedades del grupo de servidores de puerta de enlace de TS**, seleccione la dirección IP o el nombre del servidor NPS configurado para almacenar las CAP de RD y, a continuación, haga clic en **Editar**. 

 ![Grupo de servidores de puerta de enlace de TS](./media/nps-extension-remote-desktop-gateway/image13.png)

5. En el cuadro de diálogo **Editar servidor RADIUS**, seleccione la pestaña **Equilibrio de carga**.
6. En la pestaña **Equilibrio de carga**, en el campo **Número de segundos sin respuesta antes de descartar la solicitud**, cambie el valor predeterminado de 3 a un valor entre 30 y 60 segundos.
7. En el campo **Número de segundos entre solicitudes cuando el servidor se identifica como no disponible**, cambie el valor predeterminado de 30 segundos a un valor igual o mayor que el valor especificado en el paso anterior.

 ![Editar servidor Radius](./media/nps-extension-remote-desktop-gateway/image14.png)

8.  Haga clic dos veces en Aceptar para cerrar los cuadros de diálogo.

### <a name="verify-connection-request-policies"></a>Verificación de las directivas de solicitud de conexión 
De forma predeterminada, al configurar la puerta de enlace de Escritorio remoto para usar un almacén de directivas central para las directivas de autorización de conexiones, la puerta de enlace de Escritorio remoto se configura para reenviar las solicitudes de CAP al servidor NPS. El servidor NPS con la extensión Azure MFA instalada procesa la solicitud de acceso RADIUS. Los pasos siguientes muestran cómo verificar la directiva de solicitud de conexión predeterminada. 

1. En la puerta de enlace de Escritorio remoto, en la consola NPS (Local), expanda **Directivas** y seleccione **Directivas de solicitud de conexión**.
2. Haga clic con el botón derecho en **Directivas de solicitud de conexión** y haga doble clic en **Directiva de autorización de puerta de enlace de TS**.
3. En el cuadro de diálogo **Propiedades de la directiva de autorización de puerta de enlace de TS**, haga clic en la pestaña **Configuración**.
4. En la pestaña **Configuración**, bajo Reenvío de solicitud de conexión, haga clic en **Autenticación**. El cliente RADIUS está configurado para reenviar las solicitudes de autenticación.

 ![Configuración de autenticación](./media/nps-extension-remote-desktop-gateway/image15.png)
 
5. Haga clic en **Cancelar**. 

## <a name="configure-nps-on-the-server-where-the-nps-extension-is-installed"></a>Configuración de NPS en el servidor donde está instalada la extensión NPS
El servidor NPS donde está instalada la extensión NPS debe ser capaz de intercambiar mensajes de RADIUS con el servidor NPS de la puerta de enlace de Escritorio remoto. Para habilitar este intercambio de mensajes, debe configurar los componentes de NPS en el servidor donde está instalado el servicio de la extensión NPS. 

### <a name="register-server-in-active-directory"></a>Registro del servidor en Active Directory
Para que funcione correctamente en este escenario, el servidor NPS debe estar registrado en Active Directory.

1. Abra el **Administrador del servidor**.
2. En el Administrador del servidor, haga clic en **Herramientas** y, a continuación, haga clic en **Servidor de directivas de redes**. 
3. En la consola del servidor de directivas de redes, haga clic con el botón derecho en **NPS (Local)** y, a continuación, haga clic en **Registrar el servidor en Active Directory**. 
4. Haga clic en **Aceptar** dos veces.

 ![Registro del servidor en AD](./media/nps-extension-remote-desktop-gateway/image16.png)

5. Deje la consola abierta para el siguiente procedimiento.

### <a name="create-and-configure-radius-client"></a>Creación y configuración del cliente RADIUS 
La puerta de enlace de Escritorio remoto debe configurarse como un cliente RADIUS en el servidor NPS. 

1. En el servidor NPS donde está instalada la extensión NPS, en la consola **NPS (Local)**, haga clic con el botón derecho en **Clientes RADIUS** y haga clic en **Nuevo**.

 ![Nuevo cliente RADIUS](./media/nps-extension-remote-desktop-gateway/image17.png)

2. En el cuadro de diálogo **Nuevo cliente RADIUS**, proporcione un nombre descriptivo, como _Puerta_de_enlace_ y la dirección IP o nombre DNS del servidor de puerta de enlace de Escritorio remoto. 
3. En los campos **Secreto compartido** y **Confirmar secreto compartido**, escriba el mismo secreto que usó anteriormente.

 ![Nombre y dirección](./media/nps-extension-remote-desktop-gateway/image18.png)

4. Haga clic en **Aceptar** para cerrar el cuadro de diálogo Nuevo cliente RADIUS.

### <a name="configure-network-policy"></a>Configuración de la directiva de red
Recuerde que el servidor NPS con la extensión de Azure MFA es el almacén de directivas central designado para la directiva de autorización de conexiones (CAP). Por lo tanto, debe implementar un CAP en el servidor NPS para autorizar las solicitudes de conexión válidas.  

1. En la consola NPS (Local), expanda **Directivas** y haga clic en **Directivas de red**.
2. Haga clic con el botón derecho en **Conexiones a otros servidores de acceso** y haga clic en **Duplicar directiva**. 

 ![Duplicar directiva](./media/nps-extension-remote-desktop-gateway/image19.png)

3. Haga clic con el botón derecho en **Copia de Conexiones a otros servidores de acceso** y haga clic en **Propiedades**.

 ![Propiedades de red](./media/nps-extension-remote-desktop-gateway/image20.png)

4. En el cuadro de diálogo **Copia de Conexiones a otros servidores de acceso**, en Nombre de directiva, escriba un nombre adecuado, como **RDG_CAP**. Marque la casilla **Directiva habilitada** y seleccione **Conceder acceso**. Si lo desea, en Tipo de acceso a la red, seleccione **Puerta de enlace de Escritorio remoto** o puede dejarlo como **Sin especificar**.

 ![Copia de las conexiones](./media/nps-extension-remote-desktop-gateway/image21.png)

5.  Haga clic en la pestaña **Restricciones** y marque la casilla **Permitir que los clientes se conecten sin negociar un método de autenticación**.

 ![Permitir que los clientes se conecten](./media/nps-extension-remote-desktop-gateway/image22.png)

6. Si lo desea, haga clic en la pestaña **Condiciones** y agregue las condiciones que deben cumplirse para que la conexión se autorice, por ejemplo, la pertenencia a un grupo de Windows específico.

 ![Condiciones](./media/nps-extension-remote-desktop-gateway/image23.png)

7. Haga clic en **Aceptar**. Cuando se le solicite ver el tema de ayuda correspondiente, haga clic en **No**.
8. Asegúrese de que la nueva directiva está en la parte superior de la lista, que la directiva está habilitada y que concede acceso.

 ![Directivas de red](./media/nps-extension-remote-desktop-gateway/image24.png)

## <a name="verify-configuration"></a>Comprobación de la configuración
Para comprobar la configuración, debe iniciar sesión en la puerta de enlace de Escritorio remoto con un cliente RDP adecuado. Asegúrese de usar una cuenta que está permitida por las directivas de autorización de conexión y está habilitada para Azure MFA. 

Como se muestra en la siguiente imagen, puede utilizar la página **Acceso web a Escritorio remoto**.

![Acceso web de Escritorio remoto](./media/nps-extension-remote-desktop-gateway/image25.png)

Después de especificar correctamente las credenciales para la autenticación principal, el cuadro de diálogo Conexión a Escritorio remoto muestra un estado de Iniciando conexión remota, tal y como se muestra a continuación. 

Si se autentica correctamente con el método de autenticación secundario que ha configurado previamente en Azure MFA, se conecta al recurso. Sin embargo, si la autenticación secundaria no se realiza correctamente, se deniega el acceso al recurso. 

![Iniciar la conexión remota](./media/nps-extension-remote-desktop-gateway/image26.png)

En el ejemplo siguiente, la aplicación Authenticator en un dispositivo Windows Phone se utiliza para proporcionar la autenticación secundaria.

![Cuentas](./media/nps-extension-remote-desktop-gateway/image27.png)

Una vez que se haya autenticado correctamente con el método de autenticación secundario, iniciará sesión de modo normal en la puerta de enlace de Escritorio remoto. Sin embargo, como ha sido necesario usar un método de autenticación secundario con una aplicación móvil en un dispositivo de confianza, el proceso de inicio de sesión es más seguro que de otro modo.

### <a name="view-event-viewer-logs-for-successful-logon-events"></a>Ver los registros del Visor de eventos para eventos de inicio de sesión correcto
Para ver los eventos de inicio de sesión correctos en los registros del Visor de eventos de Windows, puede utilizar el siguiente comando de Windows PowerShell para consultar los registros de Windows Terminal Services y el registro de seguridad de Windows.

Para consultar los eventos de inicio de sesión correctos de los registros operativos de la puerta de enlace _(Event Viewer\Applications and Services Logs\Microsoft\Windows\TerminalServices-Gateway\Operational_, use los siguientes comandos:

* _Get-WinEvent -Logname Microsoft-Windows-TerminalServices-Gateway/Operational_ | where {$_.ID -eq '300'} | FL 
* Este comando muestra los eventos de Windows que muestran que el usuario cumple los requisitos de la directiva de administración de recursos (RAP de RD) y se le concedió acceso.

![Visualización del Visor de eventos](./media/nps-extension-remote-desktop-gateway/image28.png)

* _Get-WinEvent -Logname Microsoft-Windows-TerminalServices-Gateway/Operational_ | where {$_.ID -eq '200'} | FL
* Este comando muestra los eventos que muestran que el usuario cumplió los requisitos de la directiva de autorización de conexiones.

![Autorización de conexión](./media/nps-extension-remote-desktop-gateway/image29.png)

También puede ver este registro y filtrar por los identificadores de los eventos, 300 y 200. Para consultar los eventos de inicio de sesión correcto en los registros del Visor de eventos de seguridad, use el comando siguiente:

* _Get-WinEvent -Logname Security_ | where {$_.ID -eq '6272'} | FL 
* Este comando se puede ejecutar en el NPS central o en el servidor de puerta de enlace de Escritorio remoto. 

![Eventos de inicio de sesión correcto](./media/nps-extension-remote-desktop-gateway/image30.png)

También puede ver el registro de seguridad o la vista personalizada de Directivas de red y servicios de acceso, tal y como se muestra a continuación:

![Directiva de red y servicios de acceso](./media/nps-extension-remote-desktop-gateway/image31.png)

En el servidor donde instaló la extensión NPS para Azure MFA, puede encontrar registros de aplicación del Visor de eventos específicos de la extensión en _Application and Services Logs\Microsoft\AzureMfa_. 

![Registros de aplicación del Visor de eventos](./media/nps-extension-remote-desktop-gateway/image32.png)

## <a name="troubleshoot-guide"></a>Guía de solución de problemas

Si la configuración no funciona según lo esperado, el primer lugar para comenzar es comprobar que el usuario está configurado para usar Azure MFA. Haga que el usuario se conecte a [Azure Portal](https://portal.azure.com). Si a los usuarios se les solicita la comprobación secundaria y se pueden autenticar correctamente, puede descartar una configuración incorrecta de Azure MFA.

Si Azure MFA está funcionando para los usuarios, debe revisar los registros de eventos pertinentes. Esto incluye los eventos de seguridad, de operativa de la puerta de enlace y los registros de Azure MFA que se describen en la sección anterior. 

A continuación, se muestra un ejemplo de salida del registro de seguridad que muestra un evento de inicio de sesión incorrecto (identificador de evento 6273).

![Evento de inicio de sesión incorrecto](./media/nps-extension-remote-desktop-gateway/image33.png)

A continuación, se muestra un evento relacionado de los registros de AzureMFA:

![Registro de Azure MFA](./media/nps-extension-remote-desktop-gateway/image34.png)

Para realizar opciones avanzadas de solución de problemas, consulte los archivos de registro de formato de la base de datos de NPS donde está instalado el servicio NPS. Estos archivos de registro se crean en la carpeta _%SystemRoot%\System32\Logs_ como archivos de texto delimitado por comas. 

Para obtener una descripción de estos archivos de registro, consulte [Interpretación de los archivos de registro de formato de la base de datos de NPS](https://technet.microsoft.com/library/cc771748.aspx). Las entradas de estos archivos de registro pueden ser difíciles de interpretar sin importarlos en una hoja de cálculo o una base de datos. Puede encontrar varios analizadores de IAS en línea que le ayudarán a interpretar los archivos de registro. 

La imagen siguiente muestra la salida de una [aplicación shareware](http://www.deepsoftware.com/iasviewer) que se puede descargar. 

![Aplicación shareware](./media/nps-extension-remote-desktop-gateway/image35.png)

Por último, para más opciones de solución de problemas, puede utilizar un analizador de protocolos, como el [Analizador de mensajes de Microsoft](https://technet.microsoft.com/library/jj649776.aspx). 

La imagen siguiente del Analizador de mensajes de Microsoft muestra el tráfico de red filtrado por el protocolo RADIUS y que contiene el nombre de usuario **Contoso\AliceC**.

![Analizador de mensajes de Microsoft](./media/nps-extension-remote-desktop-gateway/image36.png)

## <a name="next-steps"></a>Pasos siguientes
[Cómo conseguir Azure Multi-Factor Authentication](multi-factor-authentication-versions-plans.md)

[Puerta de enlace de Escritorio remoto y Servidor Azure Multi-Factor Authentication con RADIUS](multi-factor-authentication-get-started-server-rdg.md)

[Integración de los directorios locales con Azure Active Directory](../active-directory/connect/active-directory-aadconnect.md)
