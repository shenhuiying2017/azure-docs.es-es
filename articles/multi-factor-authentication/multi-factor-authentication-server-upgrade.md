---
title: "Actualización de Servidores Azure MFA | Microsoft Docs"
description: "Pasos e instrucciones para actualizar el Servidor Microsoft Azure Multi-Factor Authentication a una versión más reciente."
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: 50bb8ac3-5559-4d8b-a96a-799a74978b14
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/16/2017
ms.author: kgremban
ms.reviewer: yossib
ms.custom: it-pro
ms.openlocfilehash: 6e4e09f8539aad56f92ad9137f4a6b9eb0d82370
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="upgrade-to-the-latest-azure-multi-factor-authentication-server"></a>Actualización a la versión más reciente del Servidor Microsoft Azure Multi-Factor Authentication

Este artículo lo guiará a través del proceso de actualización de la versión 6.0 o superior del Servidor Microsoft Azure Multi-Factor Authentication (MFA). Si necesita actualizar una versión anterior de PhoneFactor Agent, consulte [Actualización de PhoneFactor Agent al Servidor Azure Multi-Factor Authentication](multi-factor-authentication-get-started-server-upgrade.md).

Si va a actualizar desde la versión 6.x o anterior a la 7.x o una más reciente, todos los componentes de .NET 2.0 a .NET 4.5. Todos los componentes también requieren Microsoft Visual C++ 2015 Redistributable Update 1 o una versión superior. El programa de instalación del Servidor MFA instalará las versiones x86 y x64 de estos componentes si no están ya instalados. Si el Portal de usuarios y el servicio web de aplicación móvil se ejecutan en servidores diferentes, debe instalar los paquetes antes de actualizar los componentes. También puede buscar la última actualización de Microsoft Visual C++ 2015 Redistributable en el [Centro de descarga de Microsoft](https://www.microsoft.com/en-us/download/). 

## <a name="install-the-latest-version-of-azure-mfa-server"></a>Instalación de la versión más reciente del Servidor Azure MFA

1. Siga las instrucciones [Descarga de Servidor Azure Multi-Factor Authentication](multi-factor-authentication-get-started-server.md#download-the-azure-multi-factor-authentication-server) para obtener la versión más reciente del Servidor Azure MFA.
2. Realice una copia de seguridad del archivo de datos de Servidor MFA ubicado en C:\Archivos de programa\Multi-Factor Authentication Server\Data\PhoneFactor.pfdata (se da por hecho que es la ubicación de instalación predeterminada) en la instancia principal de Servidor MFA.
3. Si ejecuta varios servidores para lograr alta disponibilidad, cambie los sistemas cliente que se autentican en el Servidor MFA para que se deje de enviar tráfico a los servidores que va a actualizar. Si usa un equilibrador de carga, quite un Servidor MFA del equilibrador de carga, realice la actualización y vuelva a agregarlo a la granja de servidores.
4. Ejecute el programa de instalación nuevo en cada Servidor MFA. Actualice primero los servidores subordinados porque pueden leer el archivo de datos antiguo que está replicando la instancia principal. 

  No hay que desinstalar el Servidor MFA actual antes de ejecutar el programa de instalación, ya que este realiza una actualización local. La ruta de instalación se selecciona del Registro de la instalación anterior, por lo que se instalará en la misma ubicación (por ejemplo, C:\Archivos de programa\Multi-Factor Authentication Server). 
  
5. Si se le solicita que instale un paquete de actualización de Microsoft Visual C++ 2015 Redistributable, acepte. Se instalan las versiones x86 y x64 del paquete.
5. Si utiliza el SDK del servicio web, deberá instalar el SDK del servicio web nuevo. Cuando se instala el nuevo SDK del servicio web, asegúrese de que el nombre del directorio virtual coincide con el que se instaló (por ejemplo, MultiFactorAuthWebServiceSdk).
6. Repita los pasos en todos los servidores subordinados. Elija uno de los subordinados como el nuevo servidor principal; después, actualice el servidor principal antiguo. 

## <a name="upgrade-the-user-portal"></a>Actualización del Portal de usuarios

1. Realice una copia de seguridad del archivo web.config que se encuentra en el directorio virtual de la ubicación de instalación del Portal de usuarios (por ejemplo, C:\inetpub\wwwroot\MultiFactorAuth). Si se realizaron cambios realizados en el tema predeterminado, realice también una copia de seguridad de la carpeta App_Themes\Default. Se recomienda crear una copia en la carpeta predeterminada y un nuevo tema en lugar de cambiar el tema predeterminado.
2. Si el Portal de usuarios se ejecuta en el mismo servidor que los demás componentes del Servidor MFA, durante la instalación se le pedirá que actualice el Portal de usuarios. Acepte el mensaje e instale la actualización del Portal de usuarios. Asegúrese de que el nombre del directorio virtual coincide con el que se instaló (por ejemplo, MultiFactorAuth).
3. Si el Portal de usuarios se encuentra en su propio servidor, copie el archivo MultiFactorAuthenticationUserPortalSetup64.msi de la ubicación de instalación de uno de los Servidores MFA y colóquelo en el servidor web del Portal de usuarios. Ejecute al programa de instalación. 

  Si se produce un error que indica que se requiere Microsoft Visual C++ 2015 Redistributable Update 1 o posterior, descargue e instale el paquete de actualización más reciente del [Centro de descarga de Microsoft](https://www.microsoft.com/download/). Instale las versiones x86 y x64.

4. Después de instala el software del Portal de usuarios actualizado, compare la copia de seguridad de web.config que realizó en el paso 1 con el nuevo archivo web.config. Si no hay ningún atributo nuevo en el archivo web.config nuevo, copie el archivo web.config de copia de seguridad en el directorio virtual para sobrescribir el nuevo. Otra opción consiste en copiar y pegar los valores de appSettings y la URL del SDK del servicio web desde el archivo de copia de seguridad al archivo web.config nuevo.

Si tiene el Portal de usuarios en varios servidores, repita la instalación en todos ellos. 


## <a name="upgrade-the-mobile-app-web-service"></a>Actualización del servicio web de aplicación móvil

1. Realice una copia de seguridad del archivo web.config que se encuentra en el directorio virtual de la ubicación de instalación del servicio web de aplicación móvil (por ejemplo, C:\inetpub\wwwroot\app o C:\inetpub\wwwroot\MultiFactorAuthMobileAppWebService).
2. Copie el archivo MultiFactorAuthenticationMobileAppWebServiceSetup64.msi de la ubicación de instalación de los Servidores MFA y colóquelo en el servidor web de registro de aplicación móvil.
3. Ejecute al programa de instalación. 

  Si se produce un error que indica que se requiere Microsoft Visual C++ 2015 Redistributable Update 1 o posterior, descargue e instale el paquete de actualización más reciente del [Centro de descarga de Microsoft](https://www.microsoft.com/download/). Instale las versiones x86 y x64.

4. Cuando se haya instalado el software del servicio web de aplicación móvil actualizado, compare el archivo web.config que se copió en el paso 1 con el archivo web.config nuevo. Si no hay ningún atributo nuevo en el archivo web.config nuevo, puede copiar el archivo web.config guardado en el directorio virtual y sobrescribir el nuevo. Otra opción consiste en copiar y pegar los valores de appSettings y la URL del SDK del servicio web desde el archivo de copia de seguridad al archivo web.config nuevo.

Si tiene el servicio web de aplicación móvil en varios servidores, repita la instalación en todos ellos. 

## <a name="upgrade-the-ad-fs-adapters"></a>Actualización de los adaptadores de AD FS


### <a name="if-mfa-runs-on-different-servers-than-ad-fs"></a>Si MFA se ejecuta en distintos servidores de AD FS

Estas instrucciones solo se aplican si ejecuta el Servidor Multi-Factor Authentication por separado de los servidores de AD FS. Si los dos servicios se ejecutan en los mismos servidores, pase por alto esta sección y avance a los pasos de instalación. 

1. Guarde una copia del archivo MultiFactorAuthenticationAdfsAdapter.config que se registró en AD FS o exporte la configuración con el siguiente comando de PowerShell: `Export-AdfsAuthenticationProviderConfigurationData -Name [adapter name] -FilePath [path to config file]`. El nombre del adaptador es "WindowsAzureMultiFactorAuthentication" o "AzureMfaServerAuthentication", dependiendo de la versión instalada previamente.
2. Copie los siguientes archivos desde la ubicación de instalación del Servidor MFA a los servidores de AD FS:

  - MultiFactorAuthenticationAdfsAdapterSetup64.msi
  - Register-MultiFactorAuthenticationAdfsAdapter.ps1
  - Unregister-MultiFactorAuthenticationAdfsAdapter.ps1
  - MultiFactorAuthenticationAdfsAdapter.config

3. Edite el script Register-MultiFactorAuthenticationAdfsAdapter.ps1; para ello, agregue `-ConfigurationFilePath [path]` al final del comando `Register-AdfsAuthenticationProvider`. Reemplace *[path]* por la ruta de acceso completa al archivo MultiFactorAuthenticationAdfsAdapter.config o por el archivo de configuración exportado en el paso anterior. 

  Compruebe los atributos del nuevo archivo MultiFactorAuthenticationAdfsAdapter.config para ver si coinciden con el archivo de configuración anterior. Si se han agregado o quitado atributos en la nueva versión, copie los valores de atributo del archivo de configuración antiguo en el nuevo o modifique el archivo de configuración antiguo para que coincida.

### <a name="install-new-ad-fs-adapters"></a>Instalación de nuevos adaptadores de AD FS

> [!IMPORTANT] 
> Los usuarios no tendrá que realizar verificación en dos pasos durante los pasos 3-8 de esta sección. Si ha configurado AD FS en varios clústeres, puede quitar, actualizar y restaurar cada clúster de la granja de servidores, independientemente de los otros, para evitar tiempos de inactividad.

1. Quite algunos servidores de AD FS de la granja de servidores. Actualice estos servidores mientras los otros sigan ejecutándose.
2. Instale al nuevo adaptador de AD FS en cada servidor quitado de la granja de servidores de AD FS. Si el Servidor MFA está instalado en cada servidor de AD FS, puede actualizar con la experiencia de usuario de administrador del Servidor MFS. En caso contrario, actualice ejecutando MultiFactorAuthenticationAdfsAdapterSetup64.msi. 

  Si se produce un error que indica que se requiere Microsoft Visual C++ 2015 Redistributable Update 1 o posterior, descargue e instale el paquete de actualización más reciente del [Centro de descarga de Microsoft](https://www.microsoft.com/download/). Instale las versiones x86 y x64.

3. Vaya a **AD FS** > **Directivas de autenticación** > **Editar directiva de autenticación multifactor global**. Desactive **WindowsAzureMultiFactorAuthentication** o **AzureMFAServerAuthentication** (en función de la versión actual instalada). 

  Una vez completado este paso, la verificación en dos pasos a través del Servidor MFA no estará disponible en este clúster de AD FS hasta que finalice el paso 8.

4. Anule el registro de la versión anterior del adaptador de AD FS ejecutando el script de PowerShell Unregister-MultiFactorAuthenticationAdfsAdapter.ps1. Asegúrese de que el parámetro *-Name* ("WindowsAzureMultiFactorAuthentication" o "AzureMFAServerAuthentication") coincide con el nombre que aparecía en el paso 3. Esto se aplica a todos los servidores del mismo clúster de AD FS porque no hay una configuración central.
5. Registre el nuevo adaptador de AD FS ejecutando el script de PowerShell Register-MultiFactorAuthenticationAdfsAdapter.ps1 para registrar el nuevo adaptador de AD FS. Esto se aplica a todos los servidores del mismo clúster de AD FS porque no hay una configuración central.
6. Reinicie el servicio de AD FS en cada servidor que se quitó de la granja de servidores de AD FS.
7. Vuelva a agregar los servidores actualizados a la granja de servidores de AD FS y quite los otros.
8. Vaya a **AD FS** > **Directivas de autenticación** > **Editar directiva de autenticación multifactor global**. Active **AzureMfaServerAuthentication**.
9. Repita el paso 2 para actualizar los servidores ya quitados de la granja de servidores de AD FS y reinicie el servicio de AD FS en esos servidores.
10. Vuelva a agregar los servidores a la granja de servidores de AD FS.

## <a name="next-steps"></a>Pasos siguientes

- Ejemplos de [escenarios avanzados con Azure Multi-Factor Authentication y VPN de terceros](multi-factor-authentication-advanced-vpn-configurations.md)

- [Sincronización de Servidores MFA con Windows Server Active Directory](multi-factor-authentication-get-started-server-dirint.md)

- [Configuración de la autenticación de Windows](multi-factor-authentication-get-started-server-windows.md) para sus aplicaciones
