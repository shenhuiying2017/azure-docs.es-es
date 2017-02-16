---
title: "Instalación del agente de Azure AD Connect Health | Microsoft Docs"
description: "Página de Azure AD Connect Health que describe la instalación del agente para AD FS y sincronización."
services: active-directory
documentationcenter: 
author: karavar
manager: samueld
editor: curtand
ms.assetid: 1cc8ae90-607d-4925-9c30-6770a4bd1b4e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/06/2017
ms.author: billmath
translationtype: Human Translation
ms.sourcegitcommit: 28b5da6098316f8fbe84966e0dac88f5b7d2cb1d
ms.openlocfilehash: 99cd66d0fc4fc96c0b49e1ce3e3e2095fbe62395


---
# <a name="azure-ad-connect-health-agent-installation"></a>Instalación del agente de Azure AD Connect Health
Este documento le guiará en la instalación y configuración de los agentes de Azure AD Connect Health. Puede descargar los agentes [aquí](active-directory-aadconnect-health.md#download-and-install-azure-ad-connect-health-agent).

## <a name="requirements"></a>Requisitos
En la tabla siguiente hay una lista de requisitos para utilizar Azure AD Connect Health.

| Requisito | Description |
| --- | --- |
| Azure AD Premium |Azure AD Connect Health es una característica de Azure AD Premium y, por tanto, requiere Azure AD Premium. </br></br>Para más información, consulte [Introducción a Azure Active Directory Premium](../active-directory-get-started-premium.md). </br>Para iniciar una evaluación gratuita de 30 días, consulte cómo [iniciar una evaluación](https://azure.microsoft.com/trial/get-started-active-directory/). |
| Debe ser un administrador global de su entorno de Azure AD para empezar a trabajar con Azure AD Connect Health |De forma predeterminada, solo los administradores globales pueden instalar y configurar los agentes de mantenimiento para empezar, acceder al portal y realizar operaciones en Azure AD Connect Health. Para más información, consulte [Administración del directorio de Azure AD](../active-directory-administer.md). <br><br> El uso de control de acceso basado en rol puede permitir que otros usuarios de la organización accedan a Azure AD Connect Health. Para más información, consulte cómo usar el [control de acceso basado en rol para Azure AD Connect Health](active-directory-aadconnect-health-operations.md#manage-access-with-role-based-access-control). </br></br>**Importante**: la cuenta que utilice al instalar los agentes debe ser una cuenta profesional o educativa. No puede ser una cuenta Microsoft. Para más información, consulte [Inicio de sesión en Azure como una organización](../sign-up-organization.md). |
| El agente de Azure AD Connect Health está instalado en cada servidor de destino |Azure AD Connect Health requiere la instalación de un agente en los servidores de destino para proporcionar los datos que se ven en el portal. </br></br>Por ejemplo, para obtener datos sobre su infraestructura local de AD FS, el agente se debe instalar en los servidores de AD FS, en los servidores proxy de AD FS y en los servidores proxy de aplicación web. De igual manera, para obtener datos sobre su infraestructura local de AD DS, el agente se debe instalar en los controladores de dominio. </br></br>**Importante**: la cuenta que utilice al instalar los agentes debe ser una cuenta profesional o educativa. No puede ser una cuenta Microsoft. Para más información, consulte [Inicio de sesión en Azure como una organización](../sign-up-organization.md). |
| Conectividad saliente a los extremos del servicio de Azure |Durante la instalación y el tiempo de ejecución, el agente requiere conectividad a los puntos de conexión del servicio de Azure AD Connect Health. Si la conectividad saliente está bloqueada, asegúrese de agregar los siguientes puntos de conexión a la lista de elementos permitidos: </br></br><li>&#42;.blob.core.windows.net </li><li>&#42;.queue.core.windows.net</li><li>adhsprodwus.servicebus.windows.net - Puerto: 5671 </li><li>https://management.azure.com </li><li>https://s1.adhybridhealth.azure.com/</li><li>https://policykeyservice.dc.ad.msft.net/</li><li>https://login.windows.net</li><li>https://login.microsoftonline.com</li><li>https://secure.aadcdn.microsoftonline-p.com</li> |
| Puertos de Firewall en el servidor que ejecuta al agente. |El agente requiere que los siguientes puertos de firewall estén abiertos para poder comunicarse con los puntos de conexión de Azure AD Health.</br></br><li>Puerto TCP o UDP 443</li><li>Puerto TCP o UDP 5671</li> |
| Permitir los siguientes sitios web si la seguridad mejorada de IE está habilitada |Si está habilitada la seguridad mejorada de Internet Explorer, los siguientes sitios web se deben permitir en el servidor en el que estará instalado el agente.</br></br><li>https://login.microsoftonline.com</li><li>https://secure.aadcdn.microsoftonline-p.com</li><li>https://login.windows.net</li><li>El servidor de federación de su organización en el que confía Azure Active Directory. Por ejemplo: https://sts.contoso.com</li> |

## <a name="installing-the-azure-ad-connect-health-agent-for-ad-fs"></a>Instalación del agente de Azure AD Connect Health para AD FS
Para iniciar la instalación del agente, haga doble clic en el archivo .exe que descargó. En la primera pantalla, haga clic en Instalar.

![Comprobación de Azure AD Connect Health](./media/active-directory-aadconnect-health-requirements/install1.png)

Una vez que finalice la instalación, haga clic en Configurar ahora.

![Comprobación de Azure AD Connect Health](./media/active-directory-aadconnect-health-requirements/install2.png)

Con esto se iniciará un símbolo del sistema seguido de una instancia de PowerShell que ejecutará Register-AzureADConnectHealthADFSAgent. Cuando se le solicite que inicie sesión en Azure, proceda e inicie sesión.

![Comprobación de Azure AD Connect Health](./media/active-directory-aadconnect-health-requirements/install3.png)

Después de iniciar sesión, PowerShell continuará. Una vez que termine, puede cerrar PowerShell y la configuración estará completa.

En este punto, los servicios se deberían iniciar automáticamente permitiendo que el agente supervise y recopile datos. Si no ha cumplido todos los requisitos previos descritos en las secciones anteriores, verá advertencias en la ventana de PowerShell. Asegúrese de completar todos los [requisitos](active-directory-aadconnect-health-agent-install.md#requirements) antes de instalar el agente. La captura de pantalla siguiente es un ejemplo de estos errores.

![Comprobación de Azure AD Connect Health](./media/active-directory-aadconnect-health-requirements/install4.png)

Para comprobar que se instaló el agente, busque los siguientes servicios en el servidor. Si completó la configuración, estos servicios deben aparecer en ejecución. De lo contrario, estarán detenidos hasta que se complete la configuración.

* Servicio de diagnóstico de AD FS de Azure AD Connect Health
* Servicio de análisis de AD FS de Azure AD Connect Health
* Servicio de supervisión de AD FS de Azure AD Connect Health

![Comprobación de Azure AD Connect Health](./media/active-directory-aadconnect-health-requirements/install5.png)

### <a name="agent-installation-on-windows-server-2008-r2-servers"></a>Instalación del agente en servidores de Windows Server 2008 R2
En el caso de los servidores de Windows Server 2008 R2, realice los siguientes pasos:

1. Asegúrese de que se esté ejecutando el Service Pack 1 o posterior del servidor.
2. Desactive ESC de Internet Explorer para la instalación del agente:
3. Instale Windows PowerShell 4.0 en cada uno de los servidores antes de instalar el agente de AD Health. Para instalar Windows PowerShell 4.0:
   * Instale [Microsoft .NET Framework 4.5](https://www.microsoft.com/download/details.aspx?id=40779) mediante el vínculo siguiente para descargar el instalador sin conexión.
   * Instale PowerShell ISE (desde Características de Windows)
   * Instale [Windows Management Framework 4.0.](https://www.microsoft.com/download/details.aspx?id=40855)
   * Instale Internet Explorer versión 10 o posterior en el servidor. (Necesario para que el servicio de mantenimiento autentique su identidad con las credenciales de administrador de Azure).
4. Para más información acerca de cómo instalar Windows PowerShell 4.0 en Windows Server 2008 R2, consulte el artículo wiki [aquí](http://social.technet.microsoft.com/wiki/contents/articles/20623.step-by-step-upgrading-the-powershell-version-4-on-2008-r2.aspx).

### <a name="enable-auditing-for-ad-fs"></a>Habilitación de la auditoría de AD FS
> [!NOTE]
> Esta sección solo se aplica a los servidores de federación de AD FS.
> 
> 

Para que la característica Análisis de uso pueda recopilar y analizar datos, el agente de Azure AD Connect Health necesita la información de los registros de auditoría de AD FS. que no están habilitados de forma predeterminada. Use los procedimientos siguientes para habilitar la auditoría de AD FS y localizar los registros de auditoría de AD FS en los servidores correspondientes.

#### <a name="to-enable-auditing-for-ad-fs-20"></a>Para habilitar la auditoría de AD FS 2.0
1. Haga clic en **Inicio**, seleccione **Programas**, **Herramientas administrativas** y luego haga clic en **Directiva de seguridad local**.
2. Navegue hasta la carpeta **Configuración de seguridad\Directivas locales\Administración de derechos de usuario** y haga doble clic en Generar auditorías de seguridad.
3. En la pestaña **Configuración de seguridad local** , compruebe que aparezca la cuenta de servicio de AD FS 2.0. Si no aparece, haga clic en **Agregar usuario o grupo**, agréguela a la lista y luego haga clic en **Aceptar**.
4. Para habilitar la auditoría, abra un símbolo del sistema con privilegios elevados y ejecute el siguiente comando para habilitar la auditoría: <code>auditpol.exe /set /subcategory:"Application Generated" /failure:enable /success:enable</code>
5. Cierre Directiva de seguridad local y luego abra el complemento de administración. Para abrir el complemento de administración, haga clic en **Inicio**, seleccione **Programas**, **Herramientas administrativas** y luego haga clic en Administración de AD FS 2.0.
6. En el panel Acciones, haga clic en Editar propiedades del servicio de federación.
7. En el cuadro de diálogo **Propiedades del servicio de federación**, haga clic en la pestaña **Eventos**.
8. Active las casillas **Auditorías de aciertos** y **Auditorías de errores**.
9. Haga clic en **Aceptar**.

#### <a name="to-enable-auditing-for-ad-fs-on-windows-server-2012-r2"></a>Para habilitar la auditoría de AD FS en Windows Server 2012 R2
1. Abra **Directiva de seguridad local**; para ello, abra **Administrador del servidor** en la pantalla Inicio o Administrador del servidor en la barra de tareas del escritorio y luego haga clic en **Herramientas/Directiva de seguridad local**.
2. Navegue hasta la carpeta **Configuración de seguridad\Directivas locales\Asignación de derechos de usuario** y haga doble clic en **Generar auditorías de seguridad**.
3. En la pestaña **Configuración de seguridad local** , compruebe que aparezca la cuenta de servicio de AD FS. Si no aparece, haga clic en **Agregar usuario o grupo**, agréguela a la lista y luego haga clic en **Aceptar**.
4. Para habilitar la auditoría, abra un símbolo del sistema con privilegios elevados y ejecute el siguiente comando para habilitar la auditoría: <code>auditpol.exe /set /subcategory:"Application Generated" /failure:enable /success:enable.</code>
5. Cierre **Directiva de seguridad local** y luego abra el complemento **Administración de AD FS** (en Administrador del servidor, haga clic en Herramientas y luego seleccione Administración de AD FS).
6. En el panel Acciones, haga clic en **Editar propiedades del servicio de federación**.
7. En el cuadro de diálogo Propiedades del servicio de federación, haga clic en la pestaña **Eventos** .
8. Active las casillas **Auditorías de aciertos y Auditorías de errores** y luego haga clic en **Aceptar**.

#### <a name="to-locate-the-ad-fs-audit-logs"></a>Para buscar los registros de auditoría de AD FS
1. Abra **Visor de eventos**.
2. Vaya a Registros de Windows y seleccione **Seguridad**.
3. A la derecha, haga clic en **Filtrar registros actuales**.
4. En Origen de evento, seleccione **Auditoría de AD FS**.

![Registros de auditoría de AD FS](./media/active-directory-aadconnect-health-requirements/adfsaudit.png)

> [!WARNING]
> Si hay una directiva de grupo que deshabilita la auditoría de AD FS, el agente de Azure AD Connect Health no podrá recopilar información. Asegúrese de no tener ninguna directiva de grupo de este tipo.
> 
> 

[//]: # (Start of Agent Proxy Configuration Section)

## <a name="installing-the-azure-ad-connect-health-agent-for-sync"></a>Instalación del agente de Azure AD Connect Health para sincronización
El agente de Azure AD Connect Health para sincronización se instala automáticamente en la última compilación de Azure AD Connect. Para usar Azure AD Connect para sincronización, debe descargar la versión más reciente de Azure AD Connect e instalarla. Puede descargar la versión más reciente [aquí](http://www.microsoft.com/download/details.aspx?id=47594).

Para comprobar que se instaló el agente, busque los siguientes servicios en el servidor. Si completó la configuración, estos servicios deben aparecer en ejecución. De lo contrario, estarán detenidos hasta que se complete la configuración.

* Servicio de análisis de sincronización de Azure AD Connect Health
* Servicio de supervisión de sincronización de Azure AD Connect Health

![Comprobación de Azure AD Connect Health para sincronización](./media/active-directory-aadconnect-health-sync/services.png)

> [!NOTE]
> Recuerde que el uso de Azure AD Connect Health requiere Azure AD Premium. Si no tiene Azure AD Premium no podrá completar la configuración en Azure Portal. Para más información, consulte la [página de requisitos](active-directory-aadconnect-health-agent-install.md#requirements).
> 
> 

## <a name="manual-azure-ad-connect-health-for-sync-registration"></a>Registro manual de Azure AD Connect Health para sincronización
Si se produce un error del agente de registro de Azure AD Connect Health para sincronización después de instalar correctamente Azure AD Connect, puede usar el siguiente comando de PowerShell para registrar el agente manualmente.

> [!IMPORTANT]
> El uso de este comando de PowerShell solo es necesario si se produce un error en el registro del agente después de instalar Azure AD Connect.
> 
> 

El siguiente comando de PowerShell solo es necesario cuando se produce un error en el registro del agente de mantenimiento incluso después de una correcta instalación y configuración de Azure AD Connect. Los servicios de Azure AD Connect Health se iniciarán después de que el agente se registre correctamente.

Puede registrar manualmente el agente de Azure AD Connect Health para sincronización con el siguiente comando de PowerShell:

`Register-AzureADConnectHealthSyncAgent -AttributeFiltering $false -StagingMode $false`

El comando toma los siguientes parámetros:

* AttributeFiltering: $true (predeterminado) si Azure AD Connect no está sincronizando el conjunto de atributos predeterminado y se ha personalizado para usar un conjunto de atributos filtrados. $false en caso contrario.
* StagingMode: $false (predeterminado) si el servidor de Azure AD Connect no está en modo de almacenamiento provisional; $true si el servidor está configurado en modo de almacenamiento provisional.

Cuando se le solicite autenticación, debe usar la misma cuenta de administrador global (como admin@domain.onmicrosoft.com) que se usó para configurar Azure AD Connect.

## <a name="installing-the-azure-ad-connect-health-agent-for-ad-ds"></a>Instalación del agente de Azure AD Connect Health para AD DS
Para iniciar la instalación del agente, haga doble clic en el archivo .exe que descargó. En la primera pantalla, haga clic en Instalar.

![Comprobación de Azure AD Connect Health](./media/active-directory-aadconnect-health/aadconnect-health-adds-agent-install1.png)

Una vez que finalice la instalación, haga clic en Configurar ahora.

![Comprobación de Azure AD Connect Health](./media/active-directory-aadconnect-health/aadconnect-health-adds-agent-install2.png)

Con esto se iniciará un símbolo del sistema seguido de una instancia de PowerShell que ejecutará Register-AzureADConnectHealthADDSAgent. Cuando se le solicite que inicie sesión en Azure, proceda e inicie sesión.

![Comprobación de Azure AD Connect Health](./media/active-directory-aadconnect-health/aadconnect-health-adds-agent-install3.png)

Después de iniciar sesión, PowerShell continuará. Una vez que termine, puede cerrar PowerShell y la configuración estará completa.

En este punto, los servicios se deberían iniciar automáticamente permitiendo que el agente supervise y recopile datos. Si no ha cumplido todos los requisitos previos descritos en las secciones anteriores, verá advertencias en la ventana de PowerShell. Asegúrese de completar todos los [requisitos](active-directory-aadconnect-health-agent-install.md#requirements) antes de instalar el agente. La captura de pantalla siguiente es un ejemplo de estos errores.

![Comprobación de Azure AD Connect Health para AD DS](./media/active-directory-aadconnect-health/aadconnect-health-adds-agent-install4.png)

Para comprobar que se instaló el agente, busque los siguientes servicios en el controlador de dominio.

* Servicio de análisis de AD DS de Azure AD Connect Health
* Servicio de supervisión de AD DS de Azure AD Connect Health

Si completó la configuración, estos servicios deben aparecer ya en ejecución. De lo contrario, estarán detenidos hasta que se complete la configuración.

![Comprobación de Azure AD Connect Health](./media/active-directory-aadconnect-health/aadconnect-health-adds-agent-install5.png)

## <a name="installing-the-azure-ad-connect-health-agent-for-ad-ds-on-server-core"></a>Instalación del agente de Azure AD Connect Health para AD DS en Server Core
Después de instalar el archivo .exe, puede completar el proceso de registro mediante el siguiente comando de PowerShell:

`Register-AzureADConnectHealthADDSAgent -Credential $cred`

## <a name="configure-azure-ad-connect-health-agents-to-use-http-proxy"></a>Configuración de agentes de Azure AD Connect Health para usar el proxy HTTP
Puede configurar agentes de Azure AD Connect Health para trabajar con un proxy HTTP

> [!NOTE]
> * No se admite el uso de “Netsh WinHttp set ProxyServerAddress”, ya que el agente utiliza System.Net para realizar solicitudes web en lugar de los servicios HTTP de Microsoft Windows.
> * Se utilizará la dirección del proxy HTTP configurada para transmitir mensajes HTTPS cifrados.
> * No se admiten los servidores proxy autenticados (mediante HTTPBasic).
> 
> 

### <a name="change-health-agent-proxy-configuration"></a>Cambio de configuración del proxy del agente de estado
Tiene las siguientes opciones para configurar el agente de Azure AD Connect Health para utilizar a un proxy HTTP.

> [!NOTE]
> Debe reiniciar todos los servicios del agente de Azure AD Connect Health para que se actualice la configuración de proxy. Ejecute el siguiente comando:<br>
>  Restart-Service AdHealth*
> 
> 

#### <a name="import-existing-proxy-settings"></a>Importación de configuración de proxy existente
##### <a name="import-from-internet-explorer"></a>Importación desde Internet Explorer.
La configuración del proxy HTTP de Internet Explorer se puede importar para que los agentes de mantenimiento de Azure AD Connect Health la usen. En cada uno de los servidores que ejecutan al agente de mantenimiento, ejecute el siguiente comando de PowerShell:

    Set-AzureAdConnectHealthProxySettings -ImportFromInternetSettings

##### <a name="import-from-winhttp"></a>Importación desde WinHTTP
La configuración del proxy WinHTTP se puede importar para que los agentes de mantenimiento de Azure AD Connect Health la usen. En cada uno de los servidores que ejecutan al agente de mantenimiento, ejecute el siguiente comando de PowerShell:

    Set-AzureAdConnectHealthProxySettings -ImportFromWinHttp

#### <a name="specify-proxy-addresses-manually"></a>Especificación manual de direcciones de proxy
Puede especificar un servidor proxy manualmente en cada uno de los servidores en los que se ejecuta el agente de mantenimiento mediante el siguiente comando de PowerShell:

    Set-AzureAdConnectHealthProxySettings -HttpsProxyAddress address:port

Ejemplo: *Set-AzureAdConnectHealthProxySettings -HttpsProxyAddress myservidorproxy: 443*

* "address" puede ser un nombre de servidor resoluble DNS o una dirección IPv4
* "port" se puede omitir. Si se omite, se elige 443 como puerto predeterminado.

#### <a name="clear-existing-proxy-configuration"></a>Borrado de la configuración de proxy existente
Puede borrar la configuración de proxy existente ejecutando el comando siguiente:

    Set-AzureAdConnectHealthProxySettings -NoProxy


### <a name="read-current-proxy-settings"></a>Lectura de la configuración de proxy actual
Puede ejecutar el comando siguiente para leer la configuración de proxy definida actualmente:

    Get-AzureAdConnectHealthProxySettings


## <a name="test-connectivity-to-azure-ad-connect-health-service"></a>Prueba de la conectividad al servicio Azure AD Connect Health
Pueden surgir problemas que hagan que el agente de Azure AD Connect Health pierda la conectividad con el servicio Azure AD Connect Health, por ejemplo, problemas de red, problemas de permisos o de otro tipo.

Si el agente no puede enviar datos al servicio Azure AD Connect Health durante más de dos horas, aparecerá la siguiente alerta en el portal: "Los datos del servicio de mantenimiento no están actualizados". Puede confirmar si el agente de Azure AD Connect Health es capaz de cargar datos en el servicio de Azure AD Connect Health ejecutando el siguiente comando de PowerShell:

    Test-AzureADConnectHealthConnectivity -Role ADFS

El parámetro de rol tiene actualmente los siguientes valores:

* ADFS
* Sync
* ADDS

Puede utilizar la marca - ShowResults en el comando para ver los registros detallados. Utilice el ejemplo siguiente:

    Test-AzureADConnectHealthConnectivity -Role Sync -ShowResult

> [!NOTE]
> Para poder utilizar la herramienta de conectividad, primero debe completar el registro del agente. Si no puede completar el registro del agente, asegúrese de que cumple todos los [requisitos](active-directory-aadconnect-health-agent-install.md#requirements) para Azure AD Connect Health. Esta prueba de conectividad se realiza de forma predeterminada durante el registro del agente.
> 
> 

## <a name="related-links"></a>Vínculos relacionados
* [Azure AD Connect Health](active-directory-aadconnect-health.md)
* [Operaciones de Azure AD Connect Health](active-directory-aadconnect-health-operations.md)
* [Uso de Azure AD Connect Health con AD FS](active-directory-aadconnect-health-adfs.md)
* [Uso de Azure AD Connect Health para sincronización](active-directory-aadconnect-health-sync.md)
* [Uso de Azure AD Connect Health con AD DS](active-directory-aadconnect-health-adds.md)
* [Preguntas más frecuentes de Azure AD Connect Health](active-directory-aadconnect-health-faq.md)
* [Historial de versiones de Azure AD Connect Health](active-directory-aadconnect-health-version-history.md)




<!--HONumber=Dec16_HO3-->


