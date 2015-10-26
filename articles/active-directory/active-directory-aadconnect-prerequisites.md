<properties
   pageTitle="Requisitos previos de Azure AD Connect | Microsoft Azure"
   description="Descripción del artículo que se mostrará en las páginas de aterrizaje y en la mayoría de los resultados de búsqueda"
   services="active-directory"
   documentationCenter=""
   authors="andkjell"
   manager="stevenpo"
   editor="curtand"/>

<tags
   ms.service="active-directory"
   ms.workload="identity"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="10/13/2015"
   ms.author="andkjell;billmath"/>

# Requisitos previos de Azure AD Connect
En este tema se describen los requisitos previos y los requisitos de hardware de Azure AD Connect.

## Antes de instalar Azure AD Connect
Antes de instalar Azure AD Connect, hay algunas cosas que necesitará.

**Azure AD**

- Una suscripción de Azure o una [suscripción de prueba de Azure](http://azure.microsoft.com/pricing/free-trial/): solo es necesario para el acceso al Portal de Azure, no para usar Azure AD Connect. Si usa PowerShell u Office 365 no es necesario utilizar una suscripción de Azure para usar Azure AD Connect. Si tiene una licencia de Office 365 también puede utilizar el portal de Office 365. Con una licencia de Office 365 de pago también puede entrar en el portal de Azure desde el portal de Office 365.
- Compruebe el dominio que pretende usar en Azure AD. Por ejemplo, si tiene previsto usar contoso.com para los usuarios, asegúrese de que este dominio se ha comprobado y no usa solamente el dominio predeterminado contoso.onmicrosoft.com.
- De forma predeterminada, un directorio de Azure AD permitirá 50.000 objetos. Al comprobar el dominio el límite se incrementará a 300.000 objetos. Si todavía necesita más objetos en Azure AD, tiene que abrir una incidencia para aumentar el límite aún más. Si necesita más de 500.000 objetos, necesitará una licencia como Office 365, Azure AD Básico, Azure AD Premium o Enterprise Mobility Suite.

**Entorno y servidores locales**

- La versión del esquema de AD y el nivel funcional del bosque deben ser Windows Server 2003 o una versión posterior. Los controladores de dominio pueden ejecutar cualquier versión siempre que se cumplan los requisitos de nivel de bosque y esquema.
- Azure AD Connect debe instalarse en Windows Server 2008 o en una versión superior. Este servidor puede ser un controlador de dominio o un servidor miembro si se usa la configuración rápida. Si usa la configuración personalizada, el servidor también puede ser independiente y no tiene que estar unido a un dominio.
- Si pretende usar la sincronización de contraseñas de la característica, el servidor debe estar en Windows Server 2008 R2 SP1 o posterior.
- Si se implementa Servicios de federación de Active Directory, los servidores en los que se instale AD FS o el Proxy de aplicación web deben ser Windows Server 2012 R2 o versiones posteriores. Administración remota de Windows debe estar habilitada en estos servidores para la instalación remota.
- Azure AD Connect requiere una base de datos de SQL Server para almacenar datos de identidad. De forma predeterminada se instala SQL Server 2012 Express LocalDB (una versión ligera de SQL Server Express) y se crea la cuenta de servicio para el servicio en el equipo local. SQL Server Express tiene un límite de tamaño de 10 GB que le permite que administre aproximadamente 100.000 objetos. Si tiene que administrar un volumen superior de objetos de directorio, es preciso que el proceso de instalación apunte a otra versión de SQL Server. Azure AD Connect admite todas las versiones de Microsoft SQL Server de SQL Server 2008 (con Service Pack 4) a SQL Server 2014.

**Cuentas**

- Una cuenta de administrador global de Azure AD para el directorio de Azure AD con el que desea realizar la integración.
- Una cuenta de administrador de empresa para su Active Directory local si usa la configuración rápida.
- [Cuentas es Active Directory](active-directory-aadconnect-accounts-permissions.md) si usa la opción de instalación de configuración personalizada.

**Conectividad**

- Si utiliza un proxy saliente para la conexión a Internet, la siguiente configuración del archivo **C:\\Windows\\Microsoft.NET\\Framework64\\v4.0.30319\\Config\\machine.config** debe agregarse para que el asistente para instalación y Azure AD puedan conectarse a Internet y a Azure AD.

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

Este texto debe escribirse en la parte inferior del archivo. En este código, &lt;PROXYADRESS&gt; representa la dirección IP del proxy real o el nombre de host. -Si el proxy limita a qué direcciones URL se puede acceder, las direcciones URL que se documentan en [URL de Office 365 e intervalos de direcciones IP](https://support.office.com/es-ES/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2) deben abrirse en el proxy.

**Otros**

- Opcional: una cuenta de usuario de prueba para comprobar la sincronización.

## Requisitos previos de los componentes

Azure AD Connect depende de PowerShell y .Net 4.5.1. Dependiendo de la versión de Windows Server, haga lo siguiente:


- Windows Server 2012R2
  - PowerShell se instala de forma predeterminada, no se requiere ninguna acción.
  - .Net 4.5.1 y versiones posteriores se ofrecen a través de Windows Update. Asegúrese de que ha instalado las actualizaciones más recientes de Windows Server en el Panel de Control.
- Windows Server 2008R2 y Windows Server 2012
  - La versión más reciente de PowerShell está disponible en **Windows Management Framework 4.0**, que encontrará en el [Centro de descarga de Microsoft](http://www.microsoft.com/downloads).
  - .Net 4.5.1 y versiones posteriores están disponibles en el [Centro de descarga de Microsoft](http://www.microsoft.com/downloads).
- Windows Server 2008
  - La versión más reciente admitida de PowerShell está disponible en **Windows Management Framework 3.0**, que encontrará en el [Centro de descarga de Microsoft](http://www.microsoft.com/downloads).
 - .Net 4.5.1 y versiones posteriores están disponibles en el [Centro de descarga de Microsoft](http://www.microsoft.com/downloads).

## Componentes de soporte de Azure AD Connect

La siguiente es una lista de componentes que Azure AD Connect instalará en el servidor en el que está instalado Azure AD Connect. Esta lista es para una instalación rápida básica. Si decide usar un SQL Server diferente en la página Instalar servicios de sincronización, SQL Express LocalDB no se instalará localmente.

- Utilidades de línea de comandos de Microsoft SQL Server 2012
- Microsoft SQL Server 2012 Native Client
- Microsoft SQL Server 2012 Express LocalDB
- Módulo de Active Directory para Windows PowerShell
- Microsoft Online Services - Ayudante para el inicio de sesión para profesionales de TI
- Paquete de redistribución de Microsoft Visual C++ 2013


## Requisitos de hardware de Azure AD Connect
La tabla siguiente muestra los requisitos mínimos de un equipo con sincronización de Azure AD Connect.

| Cantidad de objetos en Active Directory | CPU | Memoria | Tamaño de disco duro |
| ------------------------------------- | --- | ------ | --------------- |
| Menos de 10.000 | 1,6 GHz | 4 GB | 70 GB |
| 10\.000–50.000 | 1,6 GHz | 4 GB | 70 GB |
| 50\.000–100.000 | 1,6 GHz | 16 GB | 100 GB* |
| Para 100.000 o más objetos, se requiere la versión completa de SQL Server| | | |
| 100\.000–300.000 | 1,6 GHz | 32 GB | < 300 GB |
| 300\.000–600.000 | 1,6 GHz | 32 GB | 450 GB |
| Más de 600.000 | 1,6 GHz | 32 GB | 500 GB |

Los requisitos mínimos para equipos que ejecutan AD FS o servidores de aplicaciones web son los siguientes:

- CPU: 1,6 GHz con doble núcleo o superior
- MEMORIA: 2 GB o superior
- VM de Azure: configuración A2 o superior


## Pasos siguientes
Obtenga más información sobre la [Integración de las identidades locales con Azure Active Directory](active-directory-aadconnect.md)

<!---HONumber=Oct15_HO3-->