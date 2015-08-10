<properties 
	pageTitle="Requisitos de Azure AD Connect Health." 
	description="Página de Azure AD Connect Health que describe los requisitos y la instalación del agente." 
	services="active-directory" 
	documentationCenter="" 
	authors="billmath" 
	manager="swadhwa" 
	editor="curtand"/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/12/2015" 
	ms.author="billmath"/>

# Requisitos de Azure Active Directory Connect Health
La documentación siguiente es una lista de los requisitos que se deben cumplir para usar Azure AD Connect Health.



## Una licencia de Azure AD Premium

Azure AD Connect Health es una característica de Azure AD Premium y requiere una licencia de este. Para obtenerla, consulte Introducción a Azure AD Premium.
 

## Debe ser administrador global del inquilino de Azure AD.

De manera predeterminada, los administradores globales tienen acceso a la información proporcionada por Azure AD Connect Health. Si no es administrador global del inquilino de Azure AD federado con la instalación de Active Directory local, no podrá crear una instancia de servicio de Azure AD Connect Health. Asegúrese de ser un administrador global. Para obtener información adicional, consulte [Administración de su directorio de Azure AD](https://msdn.microsoft.com/library/azure/hh967611.aspx).
 

## Agente de Azure AD Connect Health instalado en cada servidor de destino

Azure AD Connect Health requiere que haya un agente instalado en los servidores de destino para proporcionar los datos que se visualizan en el portal de Azure AD Connect Health. Esto significa que para obtener datos en la infraestructura local de AD FS, el agente debe instalarse en los servidores de AD FS. Esto incluye los servidores proxy de AD FS y de aplicación web. Para obtener información acerca de cómo instalar el agente de Azure AD Connect Health, consulte los Pasos de instalación del agente de Azure AD Connect Health.


## Requisitos del agente de Azure AD Connect Health

En las secciones siguientes, se describen los requisitos específicos del agente de Azure AD Connect Health.
 

### Descargar el agente de Azure AD Connect Health

Para empezar a utilizar Azure AD Connect Health, puede descargar la versión más reciente del agente aquí: [Descargar agente de Azure AD Connect Health](http://go.microsoft.com/fwlink/?LinkID=518973). Asegúrese de que ha agregado el servicio de Marketplace antes de instalar los agentes.

 
### Conectividad saliente a los extremos del servicio de Azure
Durante la instalación y el tiempo de ejecución, el agente requiere conectividad a los extremos del servicio de Azure AD Connect Health que se enumeran a continuación. Si bloquea la conectividad saliente, asegúrese de agregar lo siguiente a la lista de elementos permitidos:

- *.servicebus.windows.net - Puerto: 5671 - https://*.adhybridhealth.azure.com/
- https://*.table.core.windows.net/
- https://policykeyservice.dc.ad.msft.net/
- https://login.windows.net
- https://login.microsoftonline.com
- https://secure.aadcdn.microsoftonline-p.com 

## Permitir los siguientes sitios web si la seguridad mejorada de IE está habilitada
Los siguientes sitios web deben permitirse si la seguridad mejorada de IE está habilitada en el servidor donde se va a instalar el agente.

- https://login.microsoftonline.com 
- https://secure.aadcdn.microsoftonline-p.com
- https://login.windows.net
- El servidor de federación de su organización de confianza para Azure Active Directory. Por ejemplo: https://sts.contoso.com 


### En AD FS, la auditoría de AD FS debe estar habilitada para usar el análisis de uso

Para que la característica Análisis de uso pueda recopilar datos y analizarlos, el Agente de Azure AD Connect Health necesita la información de los registros de auditoría de AD FS, que no están habilitados de forma predeterminada. Esto solo se aplica a los servidores de federación de AD FS. No es necesario habilitar la auditoría en los servidores proxy de AD FS o de aplicación web. Use los procedimientos siguientes para habilitar la auditoría de AD FS y localizar los registros de auditoría de AD FS obtenidos.

#### Para habilitar la auditoría de AD FS 2.0

1. Haga clic en **Inicio**, seleccione **Programas**, **Herramientas administrativas** y luego haga clic en **Directiva de seguridad local**.
1. Navegue hasta la carpeta **Configuración de seguridad\\Directivas locales\\Administración de derechos de usuario** y haga doble clic en Generar auditorías de seguridad.
1. En la pestaña **Configuración de seguridad local**, compruebe que aparezca la cuenta de servicio de AD FS 2.0. Si no aparece, haga clic en **Agregar usuario o grupo**, agréguela a la lista y luego haga clic en **Aceptar**.
1. Abra un símbolo del sistema con privilegios elevados y ejecute el siguiente comando para habilitar la auditoría. `auditpol.exe /set /subcategory:"Application Generated" /failure:enable /success:enable`
1. Cierre Directiva de seguridad local y luego abra el complemento de administración. Para abrir el complemento de administración, haga clic en **Inicio**, seleccione **Programas**, **Herramientas administrativas** y luego haga clic en Administración de AD FS 2.0.
1. En el panel Acciones, haga clic en Editar propiedades del servicio de federación.
1. En el cuadro de diálogo **Propiedades del servicio de federación**, haga clic en la pestaña **Eventos**.
1. Active las casillas **Auditorías de aciertos** y **Auditorías de errores**.
1. Haga clic en **Aceptar**.

#### Para habilitar la auditoría de AD FS en Windows Server 2012 R2

1. Abra **Directiva de seguridad local**; para ello, abra **Administrador del servidor** en la pantalla Inicio o Administrador del servicio en la barra de tareas del escritorio y luego haga clic en **Herramientas/Directiva de seguridad local**.
1. Navegue hasta la carpeta **Configuración de seguridad\\Directivas locales\\Asignación de derechos de usuario** y haga doble clic en **Generar auditorías de seguridad**.
1. En la pestaña **Configuración de seguridad local**, compruebe que aparezca la cuenta de servicio de AD FS. Si no aparece, haga clic en **Agregar usuario o grupo**, agréguela a la lista y luego haga clic en **Aceptar**.
1. Abra un símbolo del sistema con privilegios elevados y ejecute el siguiente comando para habilitar la auditoría: `auditpol.exe /set /subcategory:"Application Generated" /failure:enable /success:enable.`
1. Cierre **Directiva de seguridad local** y luego abra el complemento de **administración de AD FS** (en Administrador del servidor, haga clic en Herramientas y luego seleccione Administración de AD FS).
1. En el panel Acciones, haga clic en **Editar propiedades del servicio de federación**.
1. En el cuadro de diálogo Propiedades del servicio de federación, haga clic en la pestaña **￼Eventos￼**.
1. Active las casillas **Auditorías de aciertos y Auditorías de errores** y luego haga clic en **Aceptar**.






#### Para buscar los registros de auditoría de AD FS


1. Abra **Visor de eventos**.</li>
1. Vaya a Registros de Windows y seleccione **Seguridad**.
1. A la derecha, haga clic en **Filtrar registros actuales**.
1. En Origen del evento, seleccione **Auditoría de AD FS**.

![Registros de auditoría de AD FS](./media/active-directory-aadconnect-health-requirements/adfsaudit.png)

> [AZURE.WARNING]Si tiene una directiva de grupo que deshabilita la auditoría de AD FS, el agente de Azure AD Connect Health no podrá recopilar información. Asegúrese de no tener ninguna directiva de grupo de este tipo.


### Instalación del agente en servidores de Windows Server 2008 R2

En el caso de los servidores de Windows Server 2008 R2, haga lo siguiente:

1. Asegúrese de que se esté ejecutando el Service Pack 1 o posterior del servidor.
1. Desactive ESC de Internet Explorer para la instalación del agente:
1. Instale Windows PowerShell 4.0 en cada uno de los servidores antes de instalar el agente de AD Health. Para instalar Windows PowerShell 4.0:
 - Instale [Microsoft .NET Framework 4.5](https://www.microsoft.com/download/details.aspx?id=40779) mediante el siguiente vínculo para descargar el instalador sin conexión.
 - Instale PowerShell ISE (desde Características de Windows)
 - Instale [Windows Management Framework 4.0.](https://www.microsoft.com/download/details.aspx?id=40855)
 - Instale Internet Explorer versión 10 o posterior en el servidor. Esto es necesario para que el servicio de mantenimiento autentique su identidad con las credenciales de administrador de Azure.
1. Para obtener información adicional acerca de cómo instalar Windows PowerShell 4.0 en Windows Server 2008 R2, consulte el artículo wiki [aquí](http://social.technet.microsoft.com/wiki/contents/articles/20623.step-by-step-upgrading-the-powershell-version-4-on-2008-r2.aspx).

## Implementación del agente de Azure AD Connect Health
Esta sección le guiará en la instalación y configuración del agente de Azure AD Connect Health en los servidores. Recuerde que para ver los datos de la instancia de Azure AD Connect Health, deberá instalar el agente de Azure AD Connect Health en los servidores de destino. Asegúrese de completar los requisitos anteriores antes de instalar el agente. Puede descargar el agente en el vínculo anterior y luego seguir los pasos siguientes.


Haga doble clic en el archivo .exe que descargó. En la primera pantalla, haga clic en Instalar.

![Comprobación de Azure AD Connect Health](./media/active-directory-aadconnect-health-requirements/install1.png)

Una vez que finalice la instalación, haga clic en Configurar ahora.

![Comprobación de Azure AD Connect Health](./media/active-directory-aadconnect-health-requirements/install2.png)

Con esto se iniciará un símbolo del sistema seguido de una instancia de PowerShell que ejecutará Register-AzureADConnectHealthADFSAgent. Se le pedirá que inicie sesión en Azure. Continúe e inicie sesión.

![Comprobación de Azure AD Connect Health](./media/active-directory-aadconnect-health-requirements/install2.png)

Después de iniciar sesión, PowerShell continuará. Una vez que termine, puede cerrar PowerShell y la configuración estará completa.

![Comprobación de Azure AD Connect Health](./media/active-directory-aadconnect-health-requirements/install3.png)

En este punto, los servicios se iniciarán automáticamente y el agente supervisará y recopilará datos. Tenga en cuenta que si no ha cumplido todos los requisitos previos descritos en las secciones anteriores, verá advertencias en la ventana de PowerShell. La captura de pantalla siguiente es un ejemplo.

![Comprobación de Azure AD Connect Health](./media/active-directory-aadconnect-health-requirements/install4.png)

Para comprobar que se instaló el agente, abra los servicios y busque lo siguiente. Si completó la configuración, estos servicios deben aparecer en ejecución. De lo contrario, no se iniciarán hasta que se complete la configuración.

- Servicio de diagnóstico de AD FS de Azure AD Connect Health
- Servicio de análisis de AD FS de Azure AD Connect Health
- Servicio de supervisión de AD FS de Azure AD Connect Health
 
![Comprobación de Azure AD Connect Health](./media/active-directory-aadconnect-health-requirements/install5.png)

<!---HONumber=July15_HO5-->