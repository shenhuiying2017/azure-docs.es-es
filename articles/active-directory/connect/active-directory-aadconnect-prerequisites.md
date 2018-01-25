---
title: 'Azure AD Connect: Requisitos previos y hardware | Microsoft Docs'
description: En este tema se describen los requisitos previos y los requisitos de hardware de Azure AD Connect.
services: active-directory
documentationcenter: 
author: billmath
manager: mtillman
editor: 
ms.assetid: 91b88fda-bca6-49a8-898f-8d906a661f07
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: billmath
ms.openlocfilehash: 9c35e796cb823b2b059b726f099d658ee5e8192b
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/18/2018
---
# <a name="prerequisites-for-azure-ad-connect"></a>Requisitos previos de Azure AD Connect
En este tema se describen los requisitos previos y los de hardware de Azure AD Connect.

## <a name="before-you-install-azure-ad-connect"></a>Antes de instalar Azure AD Connect
Antes de instalar Azure AD Connect, hay algunas cosas que necesita.

### <a name="azure-ad"></a>Azure AD
* Una suscripción de Azure o una [suscripción de prueba de Azure](https://azure.microsoft.com/pricing/free-trial/). Esta suscripción solo es necesaria para el acceso a Azure Portal, no para usar Azure AD Connect. Si usa PowerShell u Office 365, no es necesario utilizar una suscripción de Azure para usar Azure AD Connect. Si tiene una licencia de Office 365, también puede utilizar el portal de Office 365. Con una licencia de Office 365 de pago también puede entrar en Azure Portal desde el portal de Office 365.
  * También puede usar [Azure Portal](https://portal.azure.com). Este portal no requiere una licencia de Azure AD.
* [Agregue y compruebe el dominio](../active-directory-domains-add-azure-portal.md) que pretende usar en Azure AD. Por ejemplo, si tiene previsto usar contoso.com para los usuarios, asegúrese de que este dominio se ha comprobado y no usa solamente el dominio predeterminado contoso.onmicrosoft.com.
* Un inquilino de Azure AD permite de forma predeterminada objetos de 50 k. Al comprobar el dominio, el límite se incrementa a 300 000 objetos. Si todavía necesita más objetos en Azure AD, tiene que abrir una incidencia para aumentar el límite aún más. Si necesita más de 500 000 objetos, necesitará una licencia como Office 365, Azure AD Basic, Azure AD Premium o Enterprise Mobility and Security.

### <a name="prepare-your-on-premises-data"></a>Preparación de los datos locales
* Use [IdFix](https://support.office.com/article/Install-and-run-the-Office-365-IdFix-tool-f4bd2439-3e41-4169-99f6-3fabdfa326ac) para identificar errores tales como duplicados y problemas de formato en el directorio antes de sincronizar con Azure AD y Office 365.
* Revise [las características de sincronización opcionales que se pueden habilitar en Azure AD](active-directory-aadconnectsyncservice-features.md) y valore cuáles son las que debe habilitar.

### <a name="on-premises-active-directory"></a>Active Directory local
* La versión del esquema de AD y el nivel funcional del bosque deben ser Windows Server 2003 o una versión posterior. Los controladores de dominio pueden ejecutar cualquier versión siempre que se cumplan los requisitos de nivel de bosque y esquema.
* Si pretende usar la característica de **escritura diferida de contraseñas**, los controladores de dominio deben estar en Windows Server 2008 (con el SP más reciente) o posterior. Si los controladores de dominio están en 2008 (antes de la versión R2), también debe aplicar la [revisión KB2386717](http://support.microsoft.com/kb/2386717).
* El controlador de dominio usado por Azure AD debe ser grabable. **No se admite** el uso de un RODC (controlador de dominio de solo lectura) y Azure AD Connect no sigue las redirecciones de escritura.
* **No se admite** el uso de bosques o dominios locales con dominios de una sola etiqueta.
* **No se admite** el uso de bosques o dominios con locales con nombres de NetBios con puntos (el nombre contiene un punto ".").
* Se recomienda [habilitar la papelera de reciclaje de Active Directory](active-directory-aadconnectsync-recycle-bin.md).

### <a name="azure-ad-connect-server"></a>Servidor de Azure AD Connect
* Azure AD Connect no puede instalarse en Small Business Server o Windows Server Essentials. El servidor debe usar Windows Server estándar o una versión superior.
* El servidor de Azure AD Connect debe tener una GUI completa instalada. **No se admite** la instalación en Server Core.
* Azure AD Connect debe instalarse en Windows Server 2008 o en una versión superior. Este servidor puede ser un controlador de dominio o un servidor miembro si usa la configuración rápida. Si usa la configuración personalizada, el servidor también puede ser independiente y no tiene que estar unido a un dominio.
* Si instala Azure AD Connect en Windows Server 2008 o Windows Server 2008 R2, asegúrese de aplicar las revisiones más recientes de Windows Update. La instalación no se puede iniciar con un servidor sin revisiones.
* Si pretende usar la característica de **sincronización de contraseñas**, el servidor de Azure AD Connect debe estar en Windows Server 2008 R2 SP1 o posterior.
* Si piensa utilizar una **cuenta de servicio administrada de grupo**, el servidor de Azure AD Connect debe ser Windows Server 2012 o versiones posteriores.
* El servidor de Azure AD Connect debe tener instalado [.NET Framework 4.5.1](#component-prerequisites) o versiones posteriores y [Microsoft PowerShell 3.0](#component-prerequisites) o versiones posteriores.
* El servidor de Azure AD Connect no debe tener la directiva de grupo de transcripción de PowerShell habilitada.
* Si se implementa Servicios de federación de Active Directory, los servidores en los que se instale AD FS o el Proxy de aplicación web deben ser Windows Server 2012 R2 o versiones posteriores. [Administración remota de Windows](#windows-remote-management) debe estar habilitada en estos servidores para la instalación remota.
* Si se implementa Servicios de federación de Active Directory, necesita [Certificados SSL](#ssl-certificate-requirements).
* Si se implementan los Servicios de federación de Active Directory, necesitará configurar la [resolución de nombres](#name-resolution-for-federation-servers).
* Si los administradores globales tienen MFA habilitado, la dirección URL **https://secure.aadcdn.microsoftonline-p.com** debe estar en la lista de sitios de confianza. Se le solicita que agregue este sitio a la lista de sitios de confianza cuando se le pide un desafío MFA y no se ha agregado antes. Puede utilizar Internet Explorer para agregarla a los sitios de confianza.

### <a name="sql-server-used-by-azure-ad-connect"></a>SQL Server usado por Azure AD Connect
* Azure AD Connect requiere una base de datos de SQL Server para almacenar datos de identidad. De forma predeterminada, se instala SQL Server 2012 Express LocalDB (versión ligera de SQL Server Express). SQL Server Express tiene un límite de tamaño de 10 GB que le permite administrar aproximadamente 100 000 objetos. Si tiene que administrar un volumen superior de objetos de directorio, es necesario que el asistente para la instalación apunte a otra instalación de SQL Server.
* Si utiliza una instancia de SQL Server independiente, se aplican estos requisitos:
  * Azure AD Connect admite todas las versiones de Microsoft SQL Server, desde SQL Server 2008 (con el Service Pack más reciente) hasta SQL Server 2016 SP1. **No se admite** Microsoft Azure SQL Database como base de datos.
  * Debe usar una intercalación de SQL sin distinción de mayúsculas y minúsculas. Estas intercalaciones se identifican porque el nombre incluye \_CI_. **No se puede utilizar** para emplear una intercalación sin distinción de mayúsculas y minúsculas, y se identifica porque el nombre incluye \_CS_.
  * Solo se puede tener un motor de sincronización por cada instancia de SQL. **No se puede** compartir una instancia de SQL con FIM/MIM Sync, DirSync o la Sincronización de Azure AD.

### <a name="accounts"></a>Cuentas
* Una cuenta de administrador global de Azure AD para el inquilino de Azure AD con el que desea realizar la integración. Debe tratarse de una **cuenta profesional o educativa** y no puede ser una **cuenta Microsoft**.
* Si usa la configuración rápida o actualiza desde DirSync, debe tener una cuenta de administrador de empresa para su instancia de Active Directory local.
* [Cuentas de Active Directory](active-directory-aadconnect-accounts-permissions.md) si usa la ruta de instalación de la configuración personalizada.

### <a name="connectivity"></a>Conectividad
* El servidor de Azure AD Connect necesita resolución DNS para intranet e Internet. El servidor DNS debe ser capaz de resolver nombres en su Active Directory local y en los puntos de conexión de Azure AD.
* Si tiene firewalls en la Intranet y necesita abrir puertos entre los servidores de Azure AD Connect y los controladores de dominio, consulte [La identidad híbrida requería puertos y protocolos](active-directory-aadconnect-ports.md) para más información.
* Si el proxy o firewall limita a qué direcciones URL se puede acceder, deben abrirse las direcciones URL que se documentan en [URL de Office 365 e intervalos de direcciones IP](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2).
  * Si está usando Microsoft Cloud en Alemania o Microsoft Azure Government Cloud, consulte [Azure AD Connect: consideraciones especiales para instancias](active-directory-aadconnect-instances.md) para ver las direcciones URL.
* Azure AD Connect (versión 1.1.614.0 y posteriores) usa de forma predeterminada TLS 1.2 para cifrar la comunicación entre el motor de sincronización y Azure AD. Si TLS 1.2 no está disponible en el sistema operativo subyacente, Azure AD Connect recurre de mayor a menor a los protocolos anteriores (TLS 1.1 y TLS 1.0). Por ejemplo, Azure AD Connect en Windows Server 2008 usa TLS 1.0 porque Windows Server 2008 no admite TLS 1.1 ni TLS 1.2.
* Antes de la versión 1.1.614.0, Azure AD Connect usa de forma predeterminada TLS 1.0 para cifrar la comunicación entre el motor de sincronización y Azure AD. Para cambiar este protocolo a TLS 1.2, siga los pasos que se describen en [Habilitación de TLS 1.2 en Azure AD Connect](#enable-tls-12-for-azure-ad-connect).
* Si usa un proxy de salida para realizar la conexión a Internet, se tiene que agregar la siguiente configuración del archivo **C:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config** con el fin de que el Asistente para instalación y la sincronización de Azure AD Connect se puedan conectar a Internet y Azure AD. Este texto debe escribirse en la parte inferior del archivo. En este código, &lt;PROXYADRESS&gt; representa el nombre de host o la dirección IP de proxy real.

```
    <system.net>
        <defaultProxy>
            <proxy
            usesystemdefault="true"
            proxyaddress="http://<PROXYADDRESS>:<PROXYPORT>"
            bypassonlocal="true"
            />
        </defaultProxy>
    </system.net>
```

* Si el servidor proxy requiere autenticación, la [cuenta de servicio](active-directory-aadconnect-accounts-permissions.md#azure-ad-connect-sync-service-account) tiene que estar ubicada en el dominio y tendrá que usar la ruta de instalación de la configuración personalizada para especificar una [cuenta de servicio personalizada](active-directory-aadconnect-get-started-custom.md#install-required-components). También necesita otro cambio en el archivo machine.config. Con este cambio en el archivo machine.config, el asistente para instalación y el motor de sincronización responden a las solicitudes de autenticación del servidor proxy. En todas las páginas del asistente para instalación, excepto la página **Configurar** , se usan las credenciales del usuario que ha iniciado sesión. En la página **Configurar** al final del Asistente para instalación, el contexto se cambia a la [cuenta de servicio](active-directory-aadconnect-accounts-permissions.md#azure-ad-connect-sync-service-account) que creó. La sección del archivo machine.config debería tener este aspecto.

```
    <system.net>
        <defaultProxy enabled="true" useDefaultCredentials="true">
            <proxy
            usesystemdefault="true"
            proxyaddress="http://<PROXYADDRESS>:<PROXYPORT>"
            bypassonlocal="true"
            />
        </defaultProxy>
    </system.net>
```

* Cuando Azure AD Connect envía una solicitud web a Azure AD como parte de la sincronización de directorios, Azure AD puede tardar hasta 5 minutos en responder. Es habitual que los servidores proxy tengan la configuración de tiempo de espera de inactividad de conexión. Asegúrese de que la configuración esté establecida en 6 minutos o más.

Para obtener más información, consulte el artículo de MSDN sobre el [elemento de proxy predeterminado](https://msdn.microsoft.com/library/kd3cf2ex.aspx).  
Para obtener más información si tiene problemas de conectividad, consulte [Solución de problemas de conectividad](active-directory-aadconnect-troubleshoot-connectivity.md).

### <a name="other"></a>Otros
* Opcional: una cuenta de usuario de prueba para comprobar la sincronización.

## <a name="component-prerequisites"></a>Requisitos previos de los componentes
### <a name="powershell-and-net-framework"></a>PowerShell y .NET Framework
Azure AD Connect depende de Microsoft PowerShell y .NET Framework 4.5.1. Necesita que esta versión o una posterior esté instalada en el servidor. Dependiendo de la versión de Windows Server, haga lo siguiente:

* Windows Server 2012R2
  * Microsoft PowerShell se instala de forma predeterminada, no se requiere ninguna acción.
  * .NET Framework 4.5.1 y versiones posteriores se ofrecen mediante Windows Update. Asegúrese de que ha instalado las actualizaciones más recientes de Windows Server en el Panel de Control.
* Windows Server 2008R2 y Windows Server 2012
  * La versión más reciente de Microsoft PowerShell está disponible en **Windows Management Framework 4.0**, que encontrará en el [Centro de descarga de Microsoft](http://www.microsoft.com/downloads).
  * .NET Framework 4.5.1 y versiones posteriores están disponibles en el [Centro de descarga de Microsoft](http://www.microsoft.com/downloads).
* Windows Server 2008
  * La versión más reciente admitida de PowerShell está disponible en **Windows Management Framework 3.0**, que encontrará en el [Centro de descarga de Microsoft](http://www.microsoft.com/downloads).
  * .NET Framework 4.5.1 y versiones posteriores están disponibles en el [Centro de descarga de Microsoft](http://www.microsoft.com/downloads).

### <a name="enable-tls-12-for-azure-ad-connect"></a>Habilitación de TLS 1.2 en Azure AD Connect
Antes de la versión 1.1.614.0, Azure AD Connect usa de forma predeterminada TLS 1.0 para cifrar la comunicación entre el servidor de sincronización y Azure AD. Puede cambiar este protocolo configurando las aplicaciones .NET para que empleen TLS 1.2 de forma predeterminada en el servidor. Puede encontrar más información sobre TLS 1.2 en [Documento informativo sobre seguridad de Microsoft 2960358 ](https://technet.microsoft.com/security/advisory/2960358).

1. No se puede habilitar TLS 1.2 en Windows Server 2008. Se requiere Windows Server 2008 R2 o posterior. Asegúrese de que la revisión de .NET 4.5.1 esté instalada en el sistema operativo. Consulte [Documento informativo sobre seguridad de Microsoft 2960358](https://technet.microsoft.com/security/advisory/2960358). Puede que ya tenga esta revisión o una posterior instalada en el servidor.
2. Si usa Windows Server 2008 R2, asegúrese de que TLS 1.2 esté habilitado. En el servidor de Windows Server 2012 y versiones posteriores, TLS 1.2 ya debe estar habilitado.
   ```
   [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2]
   [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
   [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
   ```
3. Para todos los sistemas operativos, establezca esta clave del registro y reinicie el servidor.
   ```
   HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319
   "SchUseStrongCrypto"=dword:00000001
   ```
4. Si también desea habilitar TLS 1.2 entre el servidor del motor de sincronización y una instancia de SQL Server remota, asegúrese de que tiene instaladas las versiones necesarias para que [TLS 1.2 sea compatible con Microsoft SQL Server](https://support.microsoft.com/kb/3135244).

## <a name="prerequisites-for-federation-installation-and-configuration"></a>Requisitos previos para la instalación y la configuración de la federación
### <a name="windows-remote-management"></a>Administración remota de Windows
Al utilizar Azure AD Connect para implementar los Servicios de federación de Active Directory (AD FS) o el Proxy de aplicación web, compruebe los requisitos siguientes:

* Si el servidor de destino está unido al dominio, compruebe que está habilitada la opción Administración remota de Windows.
  * En una ventana de comandos PSH con privilegios elevados, use el comando `Enable-PSRemoting –force`
* Si el servidor de destino es un equipo WAP no unido al dominio, hay un par de requisitos adicionales.
  * En el equipo de destino (equipo WAP):
    * Asegúrese de que winrm (Administración remota de Windows / WS-Management) se está ejecutando mediante el complemento Servicios.
    * En una ventana de comandos PSH con privilegios elevados, use el comando `Enable-PSRemoting –force`
  * En el equipo en el que se está ejecutando el asistente (si el equipo de destino no está unido al dominio o el dominio no es de confianza):
    * En una ventana de comandos PSH con privilegios elevados, use el comando `Set-Item WSMan:\localhost\Client\TrustedHosts –Value <DMZServerFQDN> -Force –Concatenate`
    * En el Administrador de servidores:
      * Agregue el host WAP de DMZ al grupo de máquinas (pestaña Administrador de servidores -> Administrar -> Agregar servidores... usar DNS)
      * Pestaña Todos los servidores del Administrador de servidores: haga clic con el botón derecho en el servidor WAP y elija Administrar como..., escriba credenciales locales (no de dominio) para la máquina WAP.
      * Para validar la conectividad remota de PSH, en la pestaña Todos los servidores del Administrador de servidores, haga clic con el botón derecho en el servidor WAP y elija Windows PowerShell. Debe abrirse una sesión remota de PSH para asegurarse de que se pueden establecer sesiones remotas de PowerShell.

### <a name="ssl-certificate-requirements"></a>Requisitos del certificado SSL
* Se recomienda encarecidamente utilizar el mismo certificado SSL en todos los nodos de la granja de AD FS, así como todos los servidores del Proxy de aplicación web.
* El certificado debe ser del tipo X 509.
* Puede usar un certificado autofirmado en servidores de federación en un entorno de laboratorio de pruebas. Sin embargo, para un entorno de producción, se recomienda obtener el certificado de una CA pública.
  * Si usa un certificado que no es de confianza pública, asegúrese de que el certificado instalado en cada servidor del Proxy de aplicación web sea de confianza tanto en el servidor local como en todos los servidores de federación.
* La identidad del certificado debe coincidir con el nombre del servicio de federación (por ejemplo, sts.contoso.com).
  * La identidad es una extensión de nombre alternativo del firmante (SAN) de tipo dNSName, o bien, si no hay ninguna entrada de SAN, el nombre del firmante se especifica como un nombre común.  
  * Puede haber varias entradas de SAN en el certificado, siempre que una de ellas coincida con el nombre de servicio de federación.
  * Si piensa usar Workplace Join, se necesita un SAN adicional con el valor **enterpriseregistration** seguido del sufijo de nombre principal de usuario (UPN) de su organización, por ejemplo, **enterpriseregistration.contoso.com**.
* No se admiten certificados basados en claves CryptoAPI Next Generation (CNG) ni en proveedores de almacenamiento de claves. Esto significa que debe utilizar un certificado basado en un CSP (proveedor de servicios criptográficos) y no en un KSP (proveedor de almacenamiento de claves).
* Se admiten certificados comodín.

### <a name="name-resolution-for-federation-servers"></a>Resolución de nombres para los servidores de federación
* Configure registros DNS para el nombre de servicio de federación de AD FS (por ejemplo, sts.contoso.com) para la intranet (servidor DNS interno) y la extranet (DNS público a través de su registrador de dominios). Para el registro DNS de la intranet, asegúrese de que usa registros D y no registros CNAME. Esto es necesario para que funcione correctamente la autenticación de Windows desde la máquina unida al dominio.
* Si va a implementar más de un servidor de AD FS o servidor Proxy de aplicación web, asegúrese de que ha configurado el equilibrador de carga y que los registros DNS para el nombre de servicio de federación de AD FS (por ejemplo, sts.contoso.com) apuntan al equilibrador de carga.
* Para que la autenticación integrada de Windows funcione para las aplicaciones del explorador que usan Internet Explorer en la intranet, asegúrese de que se agrega el nombre de servicio de federación de AD FS (por ejemplo, sts.contoso.com) a la zona de intranet en Internet Explorer. Esto se puede controlar mediante la directiva de grupo e implementarlo en todos los equipos unidos a un dominio.

## <a name="azure-ad-connect-supporting-components"></a>Componentes de soporte de Azure AD Connect
La siguiente es una lista de componentes que Azure AD Connect instala en el servidor en el que está instalado Azure AD Connect. Esta lista es para una instalación rápida básica. Si decide usar un SQL Server diferente en la página Instalar servicios de sincronización, SQL Express LocalDB no se instalará localmente.

* Azure AD Connect Health
* Microsoft Online Services - Ayudante para el inicio de sesión, para profesionales de TI (instalado pero sin ninguna dependencia)
* Utilidades de línea de comandos de Microsoft SQL Server 2012
* Microsoft SQL Server 2012 Express LocalDB
* Microsoft SQL Server 2012 Native Client
* Paquete de redistribución de Microsoft Visual C++ 2013

## <a name="hardware-requirements-for-azure-ad-connect"></a>Requisitos de hardware de Azure AD Connect
La tabla siguiente muestra los requisitos mínimos de un equipo con sincronización de Azure AD Connect.

| Cantidad de objetos en Active Directory | CPU | Memoria | Tamaño de disco duro |
| --- | --- | --- | --- |
| Menos de 10.000 |1,6 GHz |4 GB |70 GB |
| 10.000–50.000 |1,6 GHz |4 GB |70 GB |
| 50.000–100.000 |1,6 GHz |16 GB |100 GB |
| Para 100.000 o más objetos, se requiere la versión completa de SQL Server | | | |
| 100.000–300.000 |1,6 GHz |32 GB |< 300 GB |
| 300.000–600.000 |1,6 GHz |32 GB |450 GB |
| Más de 600.000 |1,6 GHz |32 GB |500 GB |

Los requisitos mínimos para equipos que ejecutan AD FS o servidores de aplicaciones web son los siguientes:

* CPU: 1,6 GHz con doble núcleo o superior
* MEMORIA: 2 GB o superior
* VM de Azure: configuración A2 o superior

## <a name="next-steps"></a>pasos siguientes
Obtenga más información sobre la [Integración de las identidades locales con Azure Active Directory](active-directory-aadconnect.md).
