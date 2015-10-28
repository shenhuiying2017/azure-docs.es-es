<properties
	pageTitle="Habilitación de escritura diferida de dispositivos en Azure AD Connect | Microsoft Azure"
	description="En este documento se describe cómo habilitar la escritura diferida de dispositivo con Azure AD Connect"
	services="active-directory"
	documentationCenter=""
	authors="billmath"
	manager="StevenPo"
	editor="curtand"/>

<tags
	ms.service="active-directory"  
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/15/2015"
	ms.author="billmath"/>

# Habilitación de escritura diferida de dispositivos en Azure AD Connect

En la siguiente documentación se ofrece información sobre cómo habilitar la característica de escritura diferida de dispositivo en Azure AD Connect. La escritura diferida de dispositivo se usa en los siguientes escenarios:

Habilite el acceso condicional basado en dispositivos para aplicaciones protegida de ADFS (2012 R2 o superior) (relaciones de confianza para usuario autenticado).

Esto ofrece seguridad adicional y la garantía de que el acceso a las aplicaciones solo se concede para dispositivos de confianza. Para obtener más información sobre el acceso condicional, consulte [Administración de riesgos con el acceso condicional](active-directory-conditional-access.md) y [Configuración del acceso condicional local mediante el Registro de dispositivos de Azure Active Directory](https://msdn.microsoft.com/library/azure/dn788908.aspx).

>[AZURE.Note]Se requiere una suscripción a Office 365 o Azure AD Premium al usar dispositivos registrados en directivas de acceso condicional del servicio Registro de dispositivos de Azure Active Directory. Esto incluye las directivas que exigen los Servicios de federación de Active Directory (AD FS) con recursos locales.

## Parte 1: Preparar Azure AD Connect
Lleve a cabo los siguientes pasos para prepararse para usar la reescritura de dispositivos.

1.	Desde el equipo donde está instalado Azure AD Connect, inicie PowerShell en modo elevado.

2.	Si el módulo de Active Directory PowerShell NO está instalado. Instálelo con el comando siguiente:

	Install-WindowsFeature –Name AD-DOMAIN-Services –IncludeManagementTools

3.	Con credenciales de administrador de organización, ejecute los comandos siguientes y luego salga de PowerShell.

	Import-Module ‘C:\\Program Files\\Microsoft Azure Active Directory Connect\\AdPrep\\AdSyncAdPrep.psm1’

	Initialize-ADSyncDeviceWriteback –DomainName <name> -AdConnectorAccount <account>

Description:



- En caso de no existir, crea y configura nuevos contenedores y objetos en CN=Configuración de registro del dispositivo, CN=Servicios, CN=Configuración, <forest-dn>.



- En caso de no existir, crea y configura nuevos contenedores y objetos en CN=RegisteredDevices, <domain-dn>. Los objetos de dispositivo se crearán en este contenedor.



- Establece los permisos necesarios en la cuenta de Azure AD Connector, para administrar dispositivos en su Active Directory.



- Solo necesita ejecutarse en un bosque, incluso si se está instalando Azure AD Connect en varios bosques.

Parámetros:


- DomainName: dominio de Active Directory donde se crearán los objetos de dispositivo. Nota: todos los dispositivos para un bosque de Active Directory determinado se creará en un dominio único.


- AdConnectorAccount: la cuenta de Active Directory que usará Azure AD Connect para administrar objetos en el directorio.

## Parte 2: Habilitar la reescritura de dispositivos
Lleve a cabo el siguiente procedimiento para habilitar la reescritura de dispositivos en Azure AD Connect.

1.	Ejecute el Asistente para AAD Connect. Si esta es la primera vez que se usa el asistente, realice una instalación personalizada seleccionando Personalizar en la pantalla Configuración rápida ![Instalación personalizada](./media/active-directory-aadconnect-get-started-custom-device-writeback/devicewriteback1.png)
2.	Si esta no es la primera vez, seleccione las opciones de personalización de sincronización desde la página Tareas adicionales y haga clic en Siguiente. ![Instalación personalizada](./media/active-directory-aadconnect-get-started-custom-device-writeback/devicewriteback2.png)
3.	En la página Características opcionales, la reescritura de dispositivos ya no estará atenuada. Tenga en cuenta que si no se completan los pasos preparatorios de Azure AD Connect, la reescritura de dispositivos se atenuará en la página de características opcionales. Active la casilla para la reescritura de dispositivos y haga clic en Siguiente. ![Escritura diferida de dispositivos](./media/active-directory-aadconnect-get-started-custom-device-writeback/devicewriteback3.png)
4.	En la página de reescritura, verá el dominio suministrado como el bosque de reescritura de dispositivos predeterminado. ![Instalación personalizada](./media/active-directory-aadconnect-get-started-custom-device-writeback/devicewriteback4.png)
5.	Complete la instalación del asistente sin realizar ningún cambio de configuración adicional. En caso necesario, consulte [Instalación personalizada de Azure AD Connect](active-directory-aadconnect-get-started-custom.md).



## Habilitar el acceso condicional
Encontrará a su disposición instrucciones detalladas para habilitar este escenario en [Configuración del acceso condicional local mediante el registro de dispositivos de Azure Active Directory](https://msdn.microsoft.com/library/azure/dn788908.aspx).

## Comprobar que los dispositivos están sincronizados con Active Directory
La reescritura de dispositivos debería funcionar ahora correctamente. Tenga en cuenta que se puede tardar hasta tres horas en que los objetos de dispositivos se vuelvan a escribir en AD. Para comprobar que los dispositivos que se están sincronizados correctamente, siga este procedimiento después de completar las reglas de sincronización:

1.	Inicie el Centro de administración de Active Directory.
2.	Expanda RegisteredDevices dentro del dominio que se está federando. ![Instalación personalizada](./media/active-directory-aadconnect-get-started-custom-device-writeback/devicewriteback5.png)
3.	Los dispositivos registrados actuales aparecerá en la lista.

![Instalación personalizada](./media/active-directory-aadconnect-get-started-custom-device-writeback/devicewriteback6.png)

## Información adicional
- [Administración de riesgos con el acceso condicional](active-directory-conditional-access.md)
- [Configuración del acceso condicional local mediante el registro de dispositivos de Azure Active Directory](https://msdn.microsoft.com/library/azure/dn788908.aspx)

## Pasos siguientes
Obtenga más información sobre la [Integración de las identidades locales con Azure Active Directory](active-directory-aadconnect.md).

<!---HONumber=Oct15_HO3-->