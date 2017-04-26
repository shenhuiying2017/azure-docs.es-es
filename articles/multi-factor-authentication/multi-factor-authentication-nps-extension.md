---
title: Uso de servidores NPS existentes para proporcionar funcionalidades de Azure MFA | Microsoft Docs
description: "La extensión Servidor de directivas de redes para Azure Multi-Factor Authentication es una solución sencilla para agregar funcionalidades de verificación de dos pasos basadas en la nube a la infraestructura de autenticación existente."
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
editor: yossib
ms.assetid: 
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/03/2017
ms.author: kgremban
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: e851a3e1b0598345dc8bfdd4341eb1dfb9f6fb5d
ms.openlocfilehash: 3dba9ebc8eb23be00f9b52907ba4bc565eeb5688
ms.lasthandoff: 04/15/2017

---
# <a name="integrate-your-existing-nps-infrastructure-with-azure-multi-factor-authentication---public-preview"></a>Integración de la infraestructura existente de NPS con Azure Multi-Factor Authentication (versión preliminar pública)

La extensión de Servidor de directivas de redes (NPS) para Azure MFA agrega funcionalidades de MFA basadas en la nube a la infraestructura de autenticación mediante los servidores existentes. Con la extensión de NPS, puede agregar verificación de llamadas de teléfono, SMS o de aplicaciones de teléfono al flujo de autenticación existente sin tener que instalar, configurar ni mantener servidores nuevos. 
 
Al usar la extensión de NPS para Azure MFA, el flujo de autenticación incluye los siguientes componentes: 

1. **Servidor NAS/VPN** recibe solicitudes de los clientes VPN y las convierte en solicitudes RADIUS para servidores NPS. 
2. **Servidor NPS** se conecta a Active Directory para realizar la autenticación principal para las solicitudes RADIUS y, cuando se realiza correctamente, pasa la solicitud a todas las extensiones instaladas.  
3. **Extensión de NPS** desencadena una solicitud para Azure MFA para la autenticación secundaria. Una vez que la extensión recibe la respuesta y, si el desafío de MFA se realiza correctamente, se completa la solicitud de autenticación proporcionando al servidor NPS tokens de seguridad que incluyen una notificación de MFA, emitidos por Azure STS.  
4. **Azure MFA** se comunica con Azure Active Directory para recuperar los detalles del usuario y realiza la autenticación secundaria con un método de verificación configurado para el usuario.

El diagrama siguiente ilustra este flujo de solicitud de autenticación de alto nivel: 

![Diagrama de flujo de autenticación](./media/multi-factor-authentication-nps-extension/auth-flow.png)

## <a name="plan-your-deployment"></a>Planeamiento de la implementación

La extensión de NPS controla automáticamente la redundancia, por lo que no necesita ninguna configuración especial. 

Puede crear tantos servidores NPS habilitados para autenticación multifactor de Azure como necesite. Si instala varios servidores, debe usar un certificado de cliente distinto para cada uno de ellos. La creación de un certificado para cada servidor significa que puede actualizar individualmente cada certificado y no preocuparse por el tiempo de inactividad en los servidores. 

Los servidores VPN enrutan las solicitudes de autenticación, por lo que tienen que ser conscientes de los nuevos servidores NPS habilitados para MFA de Azure. 

## <a name="prerequisites"></a>Requisitos previos

La extensión de NPS está diseñada para funcionar con la infraestructura existente. Asegúrese de que cumple los siguientes requisitos previos antes de empezar.

### <a name="licenses"></a>Licencias

La extensión de NPS para Azure MFA está disponible para los clientes con [licencias para Azure Multi-Factor Authentication](multi-factor-authentication.md) (incluida con Azure AD Premium, EMS o una suscripción de MFA).

### <a name="software"></a>Software

Windows Server 2008 R2 SP1 o superior.

### <a name="libraries"></a>Bibliotecas

Estas bibliotecas se instalan automáticamente con la extensión. 
-    [Paquetes redistribuibles de Visual C++ para Visual Studio 2013 (X64)](https://www.microsoft.com/download/details.aspx?id=40784)
-    [Módulo Microsoft Azure Active Directory para Windows PowerShell versión1.1.166.0](https://connect.microsoft.com/site1164/Downloads/DownloadDetails.aspx?DownloadID=59185)

### <a name="azure-active-directory"></a>Azure Active Directory

Todos los usuarios que utilizan la extensión de NPS deben estar sincronizados con Azure Active Directory mediante Azure AD Connect y estar habilitados para MFA.

Para instalar la extensión, necesita el identificador de directorio y las credenciales de administrador para el inquilino de Azure AD. Puede encontrar el identificador de directorio en [Azure Portal](https://portal.azure.com). Inicie sesión como administrador, seleccione el icono **Azure Active Directory** en la parte izquierda y luego **Propiedades**. Copie el GUID en el cuadro **Id. de directorio** y guárdelo. Usará este GUID como identificador del inquilino al instalar la extensión de NPS.

![Busque el identificador de directorio en las propiedades de Azure Active Directory.](./media/multi-factor-authentication-nps-extension/find-directory-id.png)

## <a name="prepare-your-environment"></a>Preparación del entorno

Antes de instalar la extensión de NPS, quiere preparar el entorno para controlar el tráfico de autenticación. 

### <a name="enable-the-nps-role-on-a-domain-joined-server"></a>Habilitar el rol NPS en un servidor unido a dominio

El servidor NPS se conecta a Azure Active Directory y autentica las solicitudes de MFA. Elija un servidor para este rol. Se recomienda elegir un servidor que no controle solicitudes de otros servicios, puesto que la extensión de NPS produce errores para cualquier solicitud que no sea RADIUS.

1. En el servidor, abra el **Asistente para agregar roles y características** desde el menú de inicio rápido del Administrador de servidores.
2. Elija **Instalación basada en características o en roles** como tipo de instalación.
3. Seleccione el rol de servidor **Servicios de acceso y directivas de redes**. Puede emerger una ventana para informarle de las características necesarias para ejecutar este rol.
4. Continúe con los pasos del asistente hasta la página Confirmación. Seleccione **Instalar**. 

Ahora que tiene un servidor designado para NPS, también debe configurarlo para administrar las solicitudes RADIUS entrantes de la solución VPN. 

### <a name="configure-your-vpn-solution-to-communicate-with-the-nps-server"></a>Configurar la solución VPN para comunicarse con el servidor NPS

Según la solución VPN que use, así varían los pasos para configurar la directiva de autenticación RADIUS. Configure esta directiva de modo que apunte al servidor NPS RADIUS. 

### <a name="sync-domain-users-to-the-cloud"></a>Sincronizar los usuarios del dominio en la nube

Puede que este paso ya se haya completado en el inquilino, pero es conveniente comprobar que Azure AD Connect ha sincronizado las bases de datos recientemente. 

1. Inicie sesión en [Azure Portal](https://portal.azure.com) como administrador.
2. Seleccione **Azure Active Directory** > **Azure AD Connect**.
3. Compruebe que el estado de sincronización es **Habilitado** y que la última sincronización fue hace menos de una hora.

Si necesita iniciar una nueva ronda de sincronización, use las instrucciones de [Sincronización de Azure AD Connect: Programador](../active-directory/connect/active-directory-aadconnectsync-feature-scheduler.md#start-the-scheduler).

### <a name="enable-users-for-mfa"></a>Habilitar a los usuarios para MFA

Antes de implementar la extensión de NPS completa, debe habilitar MFA para los usuarios que quieran realizar la verificación en dos pasos. De forma más inmediata, para probar la extensión mientras se implementa, necesita al menos una cuenta de prueba totalmente registrada para la autenticación multifactor. 

Para iniciar una cuenta de prueba, use estos pasos:
1. [Habilite una cuenta para MFA](multi-factor-authentication-get-started-user-states.md).
2. Vaya a cualquier sitio web que inicie una autenticación de Azure AD, como https://portal.azure.com.
3. [Regístrese para la verificación en dos pasos](./end-user/multi-factor-authentication-end-user-first-time.md).

## <a name="install-the-nps-extension"></a>Instalación de la extensión de NPS

> [!IMPORTANT]
> Instale la extensión NPS en un servidor diferente al punto de acceso VPN. 

### <a name="download-and-install-the-nps-extension-for-azure-mfa"></a>Descargar e instalar la extensión de NPS para Azure MFA 

1.    [Descargue la extensión de NPS](https://aka.ms/npsmfa) desde el Centro de descarga de Microsoft.
2.    Copie el archivo binario en el Servidor de directivas de redes que quiere configurar.
3.    Ejecute *setup.exe* y siga las instrucciones de instalación. Si se producen errores, compruebe que las dos bibliotecas de la sección de requisitos previos se han instalado correctamente.

### <a name="run-the-powershell-script"></a>Ejecución del script de PowerShell

El instalador crea un script de PowerShell en esta ubicación: `C:\Program Files\Microsoft\AzureMfa\Config` (donde C:\ es la unidad de instalación). Este script de PowerShell realiza las acciones siguientes:

-    Creación de un certificado autofirmado.
-    Asociación de la clave pública del certificado a la entidad de servicio en Azure AD.
-    Almacenamiento del certificado en el almacén de certificados del equipo local.
-    Concesión de acceso a la clave privada del certificado al usuario de red.
-    Reinicio de NPS.

A menos que desee utilizar sus propios certificados (en lugar de los certificados autofirmados que genera el script de PowerShell), ejecute el script de PowerShell para completar la instalación. Si instala la extensión en varios servidores, cada uno debe tener su propio certificado.

1. Ejecute Windows PowerShell como administrador.
2. Cambie los directorios.

   `cd "C:\Program Files\Microsoft\AzureMfa\Config"`

3. Ejecute el script de PowerShell creado por el instalador.

   `.\AzureMfaNpsExtnConfigSetup.ps1`

4. PowerShell le pide el identificador de inquilino. Use el GUID de id. de directorio que ha copiado desde Azure Portal en la sección de requisitos previos. 
5. Inicie sesión como administrador en Azure AD.
6. PowerShell muestra un mensaje de operación correcta cuando finaliza el script.  

## <a name="configure-your-nps-extension"></a>Configuración de la extensión de NPS

En esta sección se incluyen consideraciones de diseño y sugerencias para las implementaciones correctas de la extensión de NPS.

### <a name="configurations-limitations"></a>Limitaciones de configuraciones

- La extensión de NPS para Azure MFA no incluye herramientas para migrar usuarios y configuraciones desde el servidor MFA a la nube. Por este motivo se recomienda usar la extensión para las nuevas implementaciones en lugar de la existente. Si usa la extensión en una implementación existente, los usuarios tendrán que autenticarse de nuevo para rellenar los detalles de MFA en la nube.  
- La extensión de NPS usa UPN de la instancia de Active Directory local para identificar al usuario en Azure MFA que realiza la autenticación secundaria. La extensión no se puede configurar para que utilice un identificador diferente como identificador de inicio de sesión alternativo o un campo personalizado de AD que no sea UPN.  

### <a name="control-radius-clients-that-require-mfa"></a>Control de clientes RADIUS que requieren MFA

Una vez que habilite MFA para un cliente RADIUS mediante la extensión de NPS, son necesarias todas las autenticaciones de este cliente para ejecutar MFA. Si desea habilitar MFA para algunos clientes RADIUS pero no para otros, puede configurar dos servidores NPS e instalar la extensión solo en uno de ellos. Configure los clientes RADIUS para los que desea que se exija MFA para enviar solicitudes en el servidor NPS configurado con la extensión y otros clientes RADIUS en el servidor NPS no configurado con la extensión.

### <a name="prepare-for-users-that-arent-enrolled-for-mfa"></a>Preparación para los usuarios que no están inscritos en MFA

Si tiene usuarios que no están inscritos en MFA, puede determinar lo que sucederá cuando intenten realizar la autenticación. Use la configuración de registro *REQUIRE_USER_MATCH* en la ruta de acceso del registro *HKLM\Software\Microsoft\AzureMFA* para controlar el comportamiento de la característica. Esta opción tiene una única opción de configuración:

| Clave | Valor | Valor predeterminado |
| --- | ----- | ------- |
| REQUIRE_USER_MATCH | TRUE/FALSE | No establecido (equivalente a TRUE) |

El propósito de esta configuración es determinar qué hacer cuando un usuario no está inscrito en MFA. Cuando la clave no existe, no está establecida o está establecida en TRUE, y el usuario no está inscrito, se produce un error en la extensión al completar el desafío de MFA. Cuando la clave está establecida en FALSE y el usuario no está inscrito, la autenticación continúa sin realizar el desafío de MFA.

Puede decidir crear esta clave y establecerla en FALSE durante la incorporación de usuarios. Puesto que el establecimiento de la clave permite a los usuarios que no estén inscritos en MFA iniciar sesión, debe quitar esta clave antes de pasar a producción.

## <a name="troubleshooting"></a>Solución de problemas

### <a name="how-do-i-verify-that-the-client-cert-is-installed-as-expected"></a>¿Cómo se puede comprobar que el certificado de cliente está instalado según lo previsto?

Busque el certificado autofirmado creado por el instalador en el almacén de certificados y compruebe que la clave privada tenga permisos concedidos al usuario **NETWORK SERVICE**. El certificado tiene un nombre de asunto **CN \<Id_inquilino\>, OU = Microsoft NPS Extension**

-------------------------------------------------------------

### <a name="how-can-i-verify-that-my-client-cert-is-associated-to-my-tenant-in-azure-active-directory"></a>¿Cómo se puede comprobar que el certificado de cliente está asociado a mi inquilino en Azure Active Directory?

Abra el símbolo del sistema de PowerShell y ejecute el comando siguiente:

```
import-module MSOnline
Connect-MsolService
Get-MsolServicePrincipalCredential -AppPrincipalId "981f26a1-7f43-403b-a875-f8b09b8cd720" -ReturnKeyValues 1 
```

Estos comandos imprimen todos los certificados que asocian a su inquilino con su instancia de la extensión de NPS en la sesión de PowerShell. Busque el certificado al exportar el certificado de cliente como un archivo "X.509 codificado base 64 (.cer)" sin la clave privada y compárelo con la lista de PowerShell.

Las marcas de tiempo válido-desde y válido-hasta, que se encuentran en un formato legible, se pueden utilizar para filtrar desajustes evidentes si el comando devuelve más de un certificado.

-------------------------------------------------------------

### <a name="why-are-my-requests-failing-with-adal-token-error"></a>¿Por qué las solicitudes provocan un error de token de ADAL?

Este error puede deberse a varias razones. Siga estos pasos para poder resolver el problema:

1. Reinicie el servidor NPS.
2. Compruebe que el certificado de cliente esté instalado según lo previsto.
3. Compruebe que el certificado esté asociado a su inquilino en Azure AD.
4. Compruebe que https://login.windows.net/ esté accesible desde el servidor que ejecuta la extensión.

-------------------------------------------------------------

### <a name="why-does-authentication-fail-with-an-error-in-http-logs-stating-that-the-user-is-not-found"></a>¿Por qué produce un error de autenticación en los registros HTTP que indica que no se encuentra el usuario?

Compruebe que se esté ejecutando AD Connect y que el usuario esté presente en Windows Active Directory y Azure Active Directory.

------------------------------------------------------------

### <a name="why-do-i-see-http-connect-errors-in-logs-with-all-my-authentications-failing"></a>¿Por qué aparecen errores de conexión de HTTP en los registros con errores en todas las autenticaciones?

Compruebe que https://adnotifications.windowsazure.com sea accesible desde el servidor que ejecuta la extensión de NPS.

## <a name="next-steps"></a>Pasos siguientes

Vea cómo integrar Azure MFA con [Active Directory](multi-factor-authentication-get-started-server-dirint.md), [Autenticación de RADIUS](multi-factor-authentication-get-started-server-radius.md) y [Autenticación de LDAP](multi-factor-authentication-get-started-server-ldap.md).

