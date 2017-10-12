---
title: "Actualización de PhoneFactor al Servidor Azure MFA | Microsoft Docs"
description: "Introducción al Servidor Azure MFA al actualizar desde una instancia anterior de PhoneFactor Agent."
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
editor: yossib
ms.assetid: 42838ff7-bdf2-4d06-bacc-b3839a00cd76
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 06/06/2017
ms.author: kgremban
ms.openlocfilehash: 7ab7e693909f807781744ae53eed75d425096590
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="upgrade-the-phonefactor-agent-to-azure-multi-factor-authentication-server"></a>Actualización de PhoneFactor Agent al Servidor Azure Multi-Factor Authentication
Para actualizar PhoneFactor Agent v5.x o una versión anterior al Servidor Azure Multi-Factor Authentication, desinstale primero PhoneFactor Agent y los componentes asociados. Luego, puede instalar el Servidor Multi-Factor Authentication y sus componentes asociados.

## <a name="uninstall-the-phonefactor-agent"></a>Desinstalación de PhoneFactor Agent

1. En primer lugar, haga una copia del archivo de datos de PhoneFactor. La ubicación de instalación predeterminada es C:\Archivos de programa\PhoneFactor\Data\Phonefactor.pfdata.

2. Si se ha instalado el Portal de usuarios:
  1. Desplácese hasta la carpeta de instalación y haga una copia de seguridad del archivo web.config. La ubicación de instalación predeterminada es C:\inetpub\wwwroot\PhoneFactor.

  2. Si ha agregado temas personalizados al portal, haga una copia de seguridad de la carpeta personalizada en el directorio C:\inetpub\wwwroot\PhoneFactor\App_Themes.

  3. Desinstale el Portal de usuarios con PhoneFactor Agent (solo disponible si está instalado en el mismo servidor que PhoneFactor Agent) o con Programas y características de Windows.

3. Si el servicio web de aplicación móvil está instalado:

  1. Vaya a la carpeta de instalación y haga una copia de seguridad del archivo web.config. La ubicación de instalación predeterminada es C:\inetpub\wwwroot\PhoneFactorPhoneAppWebService.

  2. Desinstale el servicio web de la aplicación móvil con Programas y características de Windows.

4. Si está instalado el SDK del servicio web, desinstálelo con PhoneFactor Agent o con Programas y características de Windows.

5. Desinstale el servicio web de la aplicación móvil con Programas y características de Windows.

## <a name="install-the-multi-factor-authentication-server"></a>Instalación del Servidor Multi-Factor Authentication

La ruta de instalación se selecciona en el registro de la instalación anterior de PhoneFactor Agent, por lo que se instalará en la misma ubicación (por ejemplo, C:\Archivos de programa\PhoneFactor). Las instalaciones nuevas tendrán una ruta de instalación predeterminada diferente (por ejemplo, C:\Archivos de programa\Servidor Multi-Factor Authentication). El archivo de datos que el PhoneFactor Agent anterior ha dejado, se debe actualizar durante la instalación, por lo que los usuarios y la configuración deben seguir allí después de instalar el nuevo Servidor Multi-Factor Authentication.

1. Si se lo pide, active el Servidor Multi-Factor Authentication y asegúrese de que esté asignado al grupo de replicación correcto.

2. Si el SDK del servicio web se ha instalado previamente, instale el nuevo SDK del servicio web desde la interfaz de usuario del Servidor Multi-Factor Authentication.

  El nombre del directorio virtual predeterminado es ahora **MultiFactorAuthWebServiceSdk** en lugar de **PhoneFactorWebServiceSdk**. Si desea usar el nombre anterior, debe cambiar el nombre del directorio virtual durante la instalación. De lo contrario, si permite que la instalación use el nuevo nombre predeterminado, tendrá que cambiar la dirección URL en las aplicaciones que hagan referencia al SDK del servicio web como el Portal de usuarios para que apunten a la ubicación correcta.

3. Si previamente se ha instalado el Portal de usuarios en el servidor de PhoneFactor Agent, instale el nuevo Portal de usuarios de Multi-Factor Authentication desde la interfaz de usuario del Servidor Multi-Factor Authentication.

  El nombre del directorio virtual predeterminado es ahora **MultiFactorAuth** en lugar de **PhoneFactor**. Si desea usar el nombre anterior, debe cambiar el nombre del directorio virtual durante la instalación. De lo contrario, si permite que la instalación use el nuevo nombre predeterminado, debe hacer clic en el icono de Portal de usuarios del Servidor Multi-Factor Authentication y actualizar la dirección URL del Portal de usuarios en la pestaña Configuración.

4. Si el portal de usuarios o el servicio web de aplicación móvil ya estaba instalado en un servidor diferente desde el agente de PhoneFactor:

  1. Vaya a la ubicación de instalación (por ejemplo, C:\Archivos de programa\PhoneFactor) y copie los instaladores adecuados en el otro servidor. Hay instaladores de 32 bits y 64 bits para el Portal de usuarios y el servicio web de la aplicación móvil. Se denominan MultiFactorAuthenticationUserPortalSetupXX.msi y MultiFactorAuthenticationMobileAppWebServiceSetupXX.msi.

  2. Para instalar el Portal de usuarios en el servidor web, abra un símbolo del sistema como administrador y ejecute MultiFactorAuthenticationUserPortalSetupXX.msi.

    El nombre del directorio virtual predeterminado es ahora **MultiFactorAuth** en lugar de **PhoneFactor**. Si desea usar el nombre anterior, debe cambiar el nombre del directorio virtual durante la instalación. De lo contrario, si permite que la instalación use el nuevo nombre predeterminado, debe hacer clic en el icono de Portal de usuarios del Servidor Multi-Factor Authentication y actualizar la dirección URL del Portal de usuarios en la pestaña Configuración. Es necesario informar a los usuarios existentes de la nueva dirección URL.

  3. Vaya a la ubicación de instalación del Portal de usuarios (por ejemplo, C:\inetpub\wwwroot\MultiFactorAuth) y edite el archivo web.config. Copie los valores en las secciones appSettings y applicationSettings del archivo web.config original del que se ha hecho copia de seguridad antes de actualizar al nuevo archivo web.config. Si se ha conservado el nuevo nombre de directorio virtual predeterminado al instalar el SDK del servicio web, cambie la dirección URL en la sección applicationSettings para que apunte a la ubicación correcta. Si se han cambiado otros valores predeterminados en el archivo web.config anterior, aplique estos mismos cambios al nuevo archivo web.config.

  4. Para instalar el servicio web de aplicación móvil en el servidor web, abra un símbolo del sistema como administrador y ejecute MultiFactorAuthenticationMobileAppWebServiceSetupXX.msi.

    El nombre del directorio virtual predeterminado es ahora **MultiFactorAuthMobileAppWebService** en lugar de **PhoneFactorPhoneAppWebService**. Si desea usar el nombre anterior, debe cambiar el nombre del directorio virtual durante la instalación. Puede elegir un nombre más corto para que les sea más fácil a los usuarios finales escribirlo en sus dispositivos móviles. De lo contrario, si permite que la instalación use el nuevo nombre predeterminado, debe hacer clic en el icono de Aplicación móvil en el Servidor Multi-Factor Authentication y actualizar la dirección URL del servicio web de la aplicación móvil.

  5. Vaya a la ubicación de instalación del servicio web de aplicación móvil (por ejemplo, C:\inetpub\wwwroot\MultiFactorAuthMobileAppWebService) y edite el archivo web.config. Copie los valores en las secciones appSettings y applicationSettings del archivo web.config original del que se ha hecho copia de seguridad antes de actualizar al nuevo archivo web.config. Si se ha conservado el nuevo nombre de directorio virtual predeterminado al instalar el SDK del servicio web, cambie la dirección URL en la sección applicationSettings para que apunte a la ubicación correcta. Si se han cambiado otros valores predeterminados en el archivo web.config anterior, aplique estos mismos cambios al nuevo archivo web.config.

## <a name="next-steps"></a>Pasos siguientes

- [Instalación del Portal de usuarios](multi-factor-authentication-get-started-portal.md) para el Servidor Azure Multi-Factor Authentication.

- [Configuración de la autenticación de Windows](multi-factor-authentication-get-started-server-windows.md) para sus aplicaciones. 
