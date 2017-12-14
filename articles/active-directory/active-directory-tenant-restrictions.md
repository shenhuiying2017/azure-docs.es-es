---
title: "Administración del acceso a aplicaciones en la nube mediante la restricción de inquilinos - Azure | Microsoft Docs"
description: "Uso de Restricciones de inquilino para administrar los usuarios que pueden acceder a las aplicaciones según su inquilino de Azure AD."
services: active-directory
documentationcenter: 
author: kgremban
manager: mtillman
editor: yossib
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2017
ms.author: kgremban
ms.openlocfilehash: 63e0fa54433a60fe7384d21cf7d215cc8283afca
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="use-tenant-restrictions-to-manage-access-to-saas-cloud-applications"></a>Uso de Restricciones de inquilino para administrar el acceso a aplicaciones en la nube SaaS

Las organizaciones grandes que hacen hincapié en la seguridad desean moverse a servicios en la nube como Office 365 pero deben saber que sus usuarios solo podrán acceder a recursos aprobados. Tradicionalmente, las empresas restringen los nombres de dominio o las direcciones IP cuando desean administrar el acceso. Este enfoque no sirve en un mundo donde las aplicaciones SaaS se hospedan en una nube pública, ejecutándose en nombres de dominio compartidos como outlook.office.com y login.microsoftonline.com. El bloqueo de estas direcciones evitaría por completo que los usuarios accedieran a Outlook en la web, en lugar de simplemente limitarlos a identidades y recursos aprobados.

La solución de Azure Active Directory para este desafío es una característica denominada Restricciones de inquilino. Restricciones de inquilino permite a las organizaciones controlar el acceso a aplicaciones en la nube SaaS según el inquilino de Azure AD que usan las aplicaciones para el inicio de sesión único. Por ejemplo, puede que le interese permitir el acceso a aplicaciones de Office 365 de su organización y, al mismo tiempo, impedir el acceso a instancias de otras organizaciones de estas mismas aplicaciones.  

Restricciones de inquilino ofrece a las organizaciones la posibilidad de especificar la lista de inquilinos a los que sus usuarios pueden acceder. Después, Azure AD solo concede el acceso a estos inquilinos con permiso.

Este artículo se centra en Restricciones de inquilino para Office 365, pero la característica debe funcionar con cualquier aplicación en la nube SaaS que use protocolos de autenticación moderna con Azure AD para inicio de sesión único. Si usa aplicaciones SaaS con un inquilino de Azure AD diferente al inquilino que usa Office 365, asegúrese de que todos los inquilinos necesarios tienen permiso. Para más información sobre aplicaciones en la nube SaaS, consulte [Active Directory Marketplace](https://azure.microsoft.com/en-us/marketplace/active-directory/).

## <a name="how-it-works"></a>Cómo funciona

La solución general consta de los siguientes componentes: 

1. **Azure AD**: si `Restrict-Access-To-Tenants: <permitted tenant list>` está presente, Azure AD solo emite tokens de seguridad para los inquilinos permitidos. 

2. **Infraestructura de servidor proxy local**: un dispositivo proxy con funcionalidades de inspección SSL, configurado para insertar el encabezado que contiene la lista de inquilinos permitidos en el tráfico destinado a Azure AD. 

3. **Software de cliente**: para admitir Restricciones de inquilino, el software de cliente debe solicitar tokens directamente de Azure AD, de forma que la infraestructura del proxy pueda interceptar el tráfico. Actualmente, cuando se utiliza autenticación moderna (como OAuth 2.0), tanto las aplicaciones de Office 365 basadas en explorador como los clientes de Office admiten Restricciones de inquilino. 

4. **Autenticación moderna**: los servicios en la nube deben usar la autenticación moderna para utilizar Restricciones de inquilino y bloquear el acceso a todos los inquilinos no permitidos. Los servicios en la nube de Office 365 deben configurarse para usar protocolos de autenticación moderna de forma predeterminada. Para ver la información más reciente sobre la compatibilidad con Office 365 para la autenticación moderna, lea [Updated Office 365 modern authentication](https://blogs.office.com/2015/11/19/updated-office-365-modern-authentication-public-preview/) (Autenticación moderna actualizada de Office 365).

En el siguiente diagrama se ilustra el flujo de tráfico de alto nivel. La inspección SSL solo es necesaria en tráfico hacia Azure AD, no hacia los servicios en la nube de Office 365. Esta distinción es importante porque el volumen de tráfico para autenticación hacia Azure AD es normalmente mucho menor que el volumen de tráfico hacia aplicaciones SaaS como Exchange Online y SharePoint Online.

![Flujo de tráfico de Restricciones de inquilino: diagrama](./media/active-directory-tenant-restrictions/traffic-flow.png)

## <a name="set-up-tenant-restrictions"></a>Configuración de Restricciones de inquilino

Se deben realizar dos pasos para empezar a trabajar con Restricciones de inquilino. El primer paso es asegurarse de que los clientes pueden conectarse a las direcciones correctas. El segundo consiste en configurar la infraestructura del proxy.

### <a name="urls-and-ip-addresses"></a>Direcciones URL e IP reservadas

Para usar Restricciones de inquilino, los clientes deben ser capaces de conectarse a las siguientes direcciones URL de Azure AD para autenticarse: login.microsoftonline.com, login.microsoft.com y login.windows.net. Además, para acceder a Office 365, los clientes también deben ser capaces de conectarse a las direcciones URL o FQDN y a las direcciones IP definidas en [URL de Office 365 e intervalos de direcciones IP](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2). 

### <a name="proxy-configuration-and-requirements"></a>Requisitos y configuración de proxy

Se necesita la configuración siguiente para habilitar Restricciones de inquilino a través de la infraestructura del proxy. Esta guía es genérica, por lo que debe remitirse a la documentación del proveedor del proxy para conocer los pasos de implementación específicos.

#### <a name="prerequisites"></a>Requisitos previos

- El proxy debe ser capaz de realizar la intercepción de SSL y la inserción de encabezados HTTP, así como filtrar destinos mediante direcciones URL o FQDN. 

- Los clientes deben confiar en la cadena de certificados que presenta el proxy para las comunicaciones SSL. Por ejemplo, si se usan certificados de una PKI interna, el certificado de la entidad de certificación raíz emisora interna debe ser de confianza.

- Esta característica se incluye en las suscripciones de Office 365, pero si desea usar Restricciones de inquilino para controlar el acceso a otras aplicaciones SaaS, necesitará licencias de Azure AD Premium 1.

#### <a name="configuration"></a>Configuración

Para cada solicitud entrante para login.microsoftonline.com, login.microsoft.com y login.windows.net, inserte dos encabezados HTTP: *Restrict-Access-To-Tenants* y *Restrict-Access-Context*.

Los encabezados deben incluir los siguientes elementos: 
- Para *Restrict-Access-To-Tenants*, un valor de \<permitted tenant list\>, que es una lista separada por comas de los inquilinos a los que desea que los usuarios puedan acceder. Se puede utilizar cualquier dominio que esté registrado con un inquilino para identificar al inquilino en esta lista. Por ejemplo, para permitir el acceso a los inquilinos Contoso y Fabrikam, el par nombre-valor puede ser algo así como `Restrict-Access-To-Tenants: contoso.onmicrosoft.com,fabrikam.onmicrosoft.com` 
- Para *Restrict-Access-Context*, un valor de un identificador de directorio único que declare qué inquilino está estableciendo Restricciones de inquilino. Por ejemplo, para declarar Contoso como el inquilino que establece la directiva de Restricciones de inquilino, el par nombre-valor puede ser algo así como `Restrict-Access-Context: 456ff232-35l2-5h23-b3b3-3236w0826f3d`  

> [!TIP]
> Puede encontrar el identificador de directorio en [Azure Portal](https://portal.azure.com). Inicie sesión como administrador, seleccione **Azure Active Directory** y luego seleccione **propiedades**.

Para evitar que los usuarios inserten su propio encabezado HTTP con inquilinos no aprobados, el proxy debe reemplazar el encabezado Restrict-Access-To-Tenants si ya está presente en la solicitud entrante. 

Se debe exigir a los clientes que usen el proxy para todas las solicitudes para login.microsoftonline.com, login.microsoft.com y login.windows.net. Por ejemplo, si los archivos PAC se emplean para indicar a los clientes que usen el proxy, los usuarios finales no deben poder editar o deshabilitar los archivos PAC.

## <a name="the-user-experience"></a>La experiencia del usuario final

En esta sección se muestra la experiencia de los usuarios finales y los administradores.

### <a name="end-user-experience"></a>Experiencia del usuario final

Un usuario de ejemplo se encuentra en la red de Contoso, pero está intentando acceder a la instancia de Fabrikam de una aplicación SaaS compartida como Outlook en línea. Si Contoso es un inquilino no permitido para esa instancia, el usuario ve la página siguiente:

![Página de acceso denegado para usuarios en inquilinos no permitidos](./media/active-directory-tenant-restrictions/end-user-denied.png)

### <a name="admin-experience"></a>Experiencia del administrador

Mientras la configuración de Restricciones de inquilino se realice en la infraestructura del proxy corporativo, los administradores podrán acceder a los informes de Restricciones de inquilino directamente en Azure Portal. Para ver los informes, vaya a la página de introducción de Azure Active Directory y mire en "Otras funcionalidades".

El administrador del inquilino especificado como el inquilino Restricted-Access-Context puede usar este informe para ver todos los inicios de sesión bloqueados debido a la directiva de Restricciones de inquilino, incluida la identidad que se utiliza y el identificador de directorio de destino.

![Uso de Azure Portal para ver los intentos de inicio de sesión restringidos](./media/active-directory-tenant-restrictions/portal-report.png)

Al igual que otros informes en Azure Portal, puede usar filtros para especificar el ámbito del informe. Puede filtrar por un usuario, una aplicación, un cliente o un intervalo de tiempo específico.

## <a name="office-365-support"></a>Compatibilidad con Office 365

Las aplicaciones de Office 365 deben cumplir dos criterios para que sean totalmente compatibles con Restricciones de inquilino:

1. El cliente utilizado admite la autenticación moderna
2. La autenticación moderna está habilitada como el protocolo de autenticación predeterminado para el servicio en la nube.

Consulte [Updated Office 365 modern authentication](https://blogs.office.com/2015/11/19/updated-office-365-modern-authentication-public-preview/) (Autenticación moderna actualizada de Office 365) para ver la información más reciente sobre qué clientes de Office admiten actualmente la autenticación moderna. Esa página también incluye vínculos a instrucciones para habilitar la autenticación moderna en inquilinos específicos de Exchange Online y Skype Empresarial Online. La autenticación moderna ya está habilitada de forma predeterminada en SharePoint Online.

Actualmente, las aplicaciones de Office 365 basadas en explorador (portal de Office, Yammer, sitios de SharePoint, Outlook en la web, etc.) son compatibles con Restricciones de inquilino. Para clientes gruesos (Outlook, Skype Empresarial, Word, Excel, PowerPoint, etc.), Restricciones de inquilinos solo se puede aplicar cuando se usa autenticación moderna.  

Los clientes de Outlook y Skype Empresarial que admiten autenticación moderna pueden seguir usando protocolos heredados en inquilinos donde la autenticación moderna no está habilitada, evitando eficazmente Restricciones de inquilino. Para Outlook en Windows, los clientes pueden optar por implementar restricciones que impidan a los usuarios finales agregar cuentas de correo no aprobadas a sus perfiles. Por ejemplo, vea el establecimiento de directiva de grupo [Prevent adding non-default Exchange accounts](http://gpsearch.azurewebsites.net/default.aspx?ref=1) (Impedir la incorporación de cuentas de Exchange no predeterminadas). Para Outlook en plataformas distintas de Windows y para Skype Empresarial en todas las plataformas, actualmente no está disponible la compatibilidad total para restricciones de inquilino.

## <a name="testing"></a>Prueba

Si desea probar la característica Restricciones de inquilino antes de implementarla para toda la organización, tiene dos opciones: un enfoque basado en host mediante una herramienta como Fiddler o un lanzamiento por fases de configuración de proxy.

### <a name="fiddler-for-a-host-based-approach"></a>Fiddler para un enfoque basado en host

Fiddler es un proxy de depuración web gratis que puede usarse para capturar y modificar el tráfico HTTP/HTTPS, incluida la inserción de encabezados HTTP. Para configurar Fiddler para probar Restricciones de inquilino, realice los pasos siguientes:

1.  [Descargue e instale Fiddler](http://www.telerik.com/fiddler).
2.  Configure Fiddler para descifrar el tráfico HTTPS siguiendo las indicaciones de la [documentación de la ayuda de Fiddler](http://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS).
3.  Configure Fiddler para insertar los encabezados *Restrict-Access-To-Tenants* y *Restrict-Access-Context* utilizando reglas personalizadas:
  1. En la herramienta Fiddler Web Debugger, seleccione el menú **Reglas** y luego **Personalizar reglas...** para abrir el archivo CustomRules.
  2. Agregue las siguientes líneas al principio de la función *OnBeforeRequest*. Reemplace \<tenant domain\> por un dominio registrado con el inquilino, por ejemplo, contoso.onmicrosoft.com. Reemplace \<directory ID\> por el identificador GUID de Azure AD del inquilino.

  ```
  if (oSession.HostnameIs("login.microsoftonline.com") || oSession.HostnameIs("login.microsoft.com") || oSession.HostnameIs("login.windows.net")){      oSession.oRequest["Restrict-Access-To-Tenants"] = "<tenant domain>";      oSession.oRequest["Restrict-Access-Context"] = "<directory ID>";}
  ```

  Si necesita permitir varios inquilinos, use una coma para separar los nombres de los mismos. Por ejemplo:

  ```
  oSession.oRequest["Restrict-Access-To-Tenants"] = "contoso.onmicrosoft.com,fabrikam.onmicrosoft.com";
  ```

4. Guarde y cierre el archivo CustomRules.

Después de configurar Fiddler, podrá capturar el tráfico yendo al menú **File** (Archivo) y seleccionando **Capture Traffic** (Capturar tráfico).

### <a name="staged-rollout-of-proxy-settings"></a>Lanzamiento por fases de configuración de proxy

Dependiendo de las funcionalidades de la infraestructura del proxy, es posible que pueda llevar a cabo el lanzamiento de la configuración a los usuarios. Aquí tiene un par de opciones de alto nivel a tener en cuenta:

1.  Usar archivos PAC para dirigir a los usuarios de prueba a una infraestructura del proxy de prueba, mientras que los usuarios convencionales siguen usando la infraestructura del proxy de producción.
2.  Algunos servidores proxy pueden admitir distintas configuraciones mediante grupos.

Consulte la documentación del servidor proxy para obtener detalles específicos.

## <a name="next-steps"></a>Pasos siguientes

- Lea [Updated Office 365 modern authentication](https://blogs.office.com/2015/11/19/updated-office-365-modern-authentication-public-preview/) (Autenticación moderna actualizada de Office 365)

- Revise [URL de Office 365 e intervalos de direcciones IP](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2)
