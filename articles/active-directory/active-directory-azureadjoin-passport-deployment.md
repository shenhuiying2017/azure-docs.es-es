<properties
	pageTitle="Habilitación de Microsoft Passport for Work en la organización | Microsoft Azure"
	description="Instrucciones de implementación para habilitar Microsoft Passport en su organización."
	services="active-directory"
	documentationCenter=""
	keywords="configurar Microsoft Passport, Microsoft Passport para la implementación de Work"
	authors="femila"
	manager="swadhwa"
	editor=""
	tags="azure-classic-portal"/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/23/2016"
	ms.author="femila"/>


# Habilitación de Microsoft Passport for Work en la organización

Una vez conectados dispositivos Windows 10 unidos a un dominio con Azure Active Directory (Azure AD), realice lo siguiente para habilitar Microsoft Passport for Work en la organización.

## Implemente System Center Configuration Manager Versión 1509 para Technical Preview
Para implementar certificados de usuario según las claves de Microsoft Passport, necesita lo siguiente:

- **Versión 1509 de System Center Configuration Manager para Technical Preview**. Para más información, consulte [Microsoft System Center Configuration Manager Technical Preview](https://technet.microsoft.com/library/dn965439.aspx#BKMK_TP3Update) (versión preliminar técnica de Microsoft System Center Configuration Manager) y [System Center Configuration Manager Team Blog](http://blogs.technet.com/b/configmgrteam/archive/2015/09/23/now-available-update-for-system-center-config-manager-tp3.aspx) (bloque del equipo de System Center Configuration Manager).
- **Infraestructura PKI**: para habilitar Microsoft Passport for Work mediante certificados de usuario tiene que tener una infraestructura de PKI establecida. Si no la tiene o no desea usar certificados de usuario, puede hacer lo siguiente:
 - **Implementar un controlador de dominio**: implemente un nuevo controlador de dominio que tenga Windows Server 2016 compilación 10551 (o posterior) instalado y siga los pasos para [instalar un controlador de dominio de réplica en un dominio existente](https://technet.microsoft.com/library/jj574134.aspx) o [instalar un nuevo bosque de Active Directory, si está creando un nuevo entorno](https://technet.microsoft.com/library/jj574166). (Las archivos ISO están disponibles para su descarga en [Signiant Media Exchange](https://datatransfer.microsoft.com/signiant_media_exchange/spring/main?sdkAccessible=true)).

## Configuración de Microsoft Passport for Work mediante directivas de grupo en Active Directory

 Puede usar una directiva de grupo de Windows Server Active Directory para configurar los dispositivos unidos al dominio de Windows 10 para aprovisionar las credenciales del usuario de Microsoft Passport al iniciar sesión en Windows:

1. 	Abra el Administrador del servidor y vaya a **Herramientas** > **Administración de directivas de grupo**.
2.	En Administración de directivas de grupo, vaya al nodo de dominio que corresponde al dominio en el que desea habilitar Azure AD Join.
3.	Haga clic con el botón derecho en **Objetos de directiva de grupo** y seleccione **Nuevo**. Asigne un nombre a su objeto de directiva de grupo; por ejemplo, Habilitar Microsoft Passport. Haga clic en **Aceptar**.
4.	Haga clic con el botón derecho en el nuevo objeto de directiva de grupo y luego seleccione **Editar**.
5.	Vaya a **Configuración del equipo** > **Directivas** > **Plantillas administrativas** > **Componentes de Windows** > **Passport for Work**.
6.	Haga clic con el botón derecho en **Habilitar Passport for Work** y seleccione **Editar**.
7.	Seleccione el botón de opción **Habilitado** y luego haga clic en **Aplicar**. Haga clic en **Aceptar**.
8.	Ahora puede vincular el objeto de directiva de grupo a la ubicación que elija. Para habilitar esta directiva para todos los dispositivos de Windows 10 unidos a un dominio en su organización, vincule la Directiva de grupo al dominio. Por ejemplo:
 - Una unidad organizativa (OU) específica en Active Directory donde se encontrarán los equipos unidos a un dominio de Windows 10.
 - Un grupo de seguridad específico que contiene equipos unidos a un dominio de Windows 10 que se registrarán automáticamente en Azure AD.

## Configurar Microsoft Passport for Work con PowerShell mediante el Administrador de configuración

Ejecute el siguiente comando de PowerShell:

    powershell.exe -ExecutionPolicy Bypass -NoLogo -NoProfile -Command "& {New-ItemProperty "HKLM:\Software\Policies\Microsoft\PassportForWork" -Name "Enabled" -Value 1 -PropertyType "DWord" -Force}"

## Configuración del perfil de certificado para usar el certificado de inscripción "Passport for Work" en el Administrador de configuración
Para usar el inicio de sesión/Microsoft Hello basado en certificado de Passport for Work, configure el perfil de certificado (**Activos y compatibilidad** -> **Configuración de cumplimiento** -> **Acceso a los recursos de la compañía** -> **Perfiles de certificado**). Seleccione una plantilla que tenga el uso mejorado de clave de inicio de sesión de la tarjeta inteligente.

## Configuración de una tarea programada para la evaluación de certificados de solicitud
Esta tarea programada es una solución a corto plazo. Los administradores tienen que crear una tarea programada que escuche la creación de un contenedor de Passport for Work y, después, solicite una evaluación de certificados. La tarea programada se desencadena cuando se habilita el contenedor Passport for Work. La tarea reduce el retraso en la configuración del contenedor y el PIN y su disponibilidad para su uso en el siguiente inicio de sesión.

**Para crear la tarea programada, puede usar la interfaz de usuario o usar el comando siguiente:**

    schtasks /create /xml %0\..<EnrollCertificate.xml> /tn <Task Name>

Este es el xml de ejemplo:

    <?xml version="1.0" encoding="UTF-16"?>
    <Task version="1.2" xmlns="http://schemas.microsoft.com/windows/2004/02/mit/task">
       <RegistrationInfo>
         <Date>2015-09-04T17:48:45.9973761</Date>
          <Author><DomainName/UserName></Author>
        <URI>\Enroll Certificates</URI>
      </RegistrationInfo>
      <Triggers>
        <EventTrigger>
          <Enabled>true</Enabled>
          <Subscription>&lt;QueryList&gt;&lt;Query Id="0" Path="Microsoft-Windows-User Device Registration/Admin"&gt;&lt;Select Path="Microsoft-Windows-User Device Registration/Admin"&gt;*[System[Provider[@Name='Microsoft-Windows-User Device Registration'] and EventID=300]]&lt;/Select&gt;&lt;/Query&gt;&lt;/QueryList&gt;</Subscription>
        </EventTrigger>
      </Triggers>
      <Principals>
      </Principals>
      <Settings>
        <MultipleInstancesPolicy>IgnoreNew</MultipleInstancesPolicy>
        <DisallowStartIfOnBatteries>false</DisallowStartIfOnBatteries>
        <StopIfGoingOnBatteries>true</StopIfGoingOnBatteries>
        <AllowHardTerminate>true</AllowHardTerminate>
        <StartWhenAvailable>true</StartWhenAvailable>
        <RunOnlyIfNetworkAvailable>false</RunOnlyIfNetworkAvailable>
        <IdleSettings>
          <StopOnIdleEnd>true</StopOnIdleEnd>
          <RestartOnIdle>false</RestartOnIdle>
        </IdleSettings>
        <AllowStartOnDemand>true</AllowStartOnDemand>
        <Enabled>true</Enabled>
        <Hidden>false</Hidden>
        <RunOnlyIfIdle>false</RunOnlyIfIdle>
        <WakeToRun>false</WakeToRun>
        <ExecutionTimeLimit>PT1H</ExecutionTimeLimit>
        <Priority>7</Priority>
        <RestartOnFailure>
          <Interval>PT1M</Interval>
          <Count>3</Count>
        </RestartOnFailure>
      </Settings>
      <Actions Context="Author">
        <Exec>
          <Command>wmic</Command>
          <Arguments>/namespace:\\root\ccm\dcm path SMS_DesiredConfiguration call TriggerEvaluation 1,0,"ScopeId_73F3BB5E-5EDC-4928-87BD-4E75EB4BBC34/ConfigurationPolicy_db89c51e-1d1b-4a17-8e42-a5459b742ccd",8</Arguments>
        </Exec>
      </Actions>
    </Task>

 El identificador de ámbito, ScopeId\_73F3BB5E-5EDC-4928-87BD-4E75EB4BBC34/ConfigurationPolicy\_db89c51e-1d1b-4a17-8e42-a5459b742ccd, es el identificador del perfil del certificado creado en el paso "Configuración del perfil de certificado para usar el certificado de inscripción "Passport for Work" en el Administrador de configuración. A continuación del identificador de ámbito está la versión, 8.

## Información adicional
* [Windows 10 para la empresa: formas de usar dispositivos para trabajar](active-directory-azureadjoin-windows10-devices-overview.md)
* [Ampliación de las capacidades de nube a dispositivos de Windows 10 a través de Azure Active Directory Join](active-directory-azureadjoin-user-upgrade.md)
* [Autenticación de identidades sin contraseñas a través de Microsoft Passport](active-directory-azureadjoin-passport.md)
* [Conozca los escenarios de uso de Azure AD Join](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Experiencias de conexión de dispositivos unidos a un dominio a Azure AD para Windows 10](active-directory-azureadjoin-devices-group-policy.md)
* [Configuración de Azure AD Join](active-directory-azureadjoin-setup.md)

<!---HONumber=AcomDC_0629_2016-->