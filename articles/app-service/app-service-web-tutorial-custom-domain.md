---
title: Asignar un nombre DNS personalizado a Azure Web Apps | Microsoft Docs
description: "Aprenda a agregar un nombre de dominio DNS personalizado (dominio personal) a una aplicación web, un back-end de una aplicación móvil o una aplicación de API en Azure App Service."
keywords: app service, azure app service, domain mapping, domain name, existing domain, hostname
services: app-service\web
documentationcenter: nodejs
author: cephalin
manager: erikre
editor: 
ms.assetid: dc446e0e-0958-48ea-8d99-441d2b947a7c
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 06/23/2017
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: 9b35572b3275b5a2c5e89adf4890a2659d09626e
ms.sourcegitcommit: 9ea2edae5dbb4a104322135bef957ba6e9aeecde
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/03/2018
---
# <a name="map-an-existing-custom-dns-name-to-azure-web-apps"></a>Asignar un nombre DNS personalizado a Azure Web Apps

[Azure Web Apps](app-service-web-overview.md) proporciona un servicio de hospedaje web muy escalable y con aplicación de revisiones de un modo automático. En este tutorial se muestra cómo asignar un nombre DNS personalizado a Azure Web Apps.

![Navegación en el portal a la aplicación de Azure](./media/app-service-web-tutorial-custom-domain/app-with-custom-dns.png)

En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Asignar un subdominio (por ejemplo, `www.contoso.com`) mediante el uso de un registro CNAME
> * Asignar un dominio raíz (por ejemplo, `contoso.com`) mediante el uso de un registro D
> * Asignar un dominio con comodín (por ejemplo, `*.contoso.com`) mediante el uso de un registro CNAME
> * Automatizar la asignación de dominio con scripts

Puede usar un **registro CNAME** o un **registro A** para asignar un nombre DNS personalizado a App Service. 

> [!NOTE]
> Se recomienda usar un CNAME para todos los nombres DNS personalizados, excepto un dominio raíz (por ejemplo, `contoso.com`).

Para migrar un sitio en vivo y su nombre de dominio DNS a App Service, consulte [Migración de un nombre DNS activo a Azure App Service](app-service-custom-domain-name-migrate.md).

## <a name="prerequisites"></a>requisitos previos

Para completar este tutorial:

* [Cree una aplicación de App Service](/azure/app-service/) o use alguna aplicación que haya creado para otro tutorial.
* Adquiera un nombre de dominio y asegúrese de que tiene acceso al registro de DNS de su proveedor de dominio (por ejemplo, GoDaddy).

  Por ejemplo, para agregar entradas DNS para `contoso.com` y `www.contoso.com`, debe poder configurar las opciones de DNS del dominio raíz de `contoso.com`.

  > [!NOTE]
  > Si no tiene un nombre de dominio, considere la posibilidad de [comprar un dominio mediante Azure Portal](custom-dns-web-site-buydomains-web-app.md). 

## <a name="prepare-the-app"></a>Preparación de la aplicación

Para asignar un nombre DNS personalizado a una aplicación web, el [plan de App Service](https://azure.microsoft.com/pricing/details/app-service/) de dicha aplicación debe ser un nivel de pago (**Compartido**, **Básico**, **Estándar** o **Premium**). En este paso, asegúrese de que la aplicación de App Service se encuentra en el plan de tarifa compatible.

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

### <a name="sign-in-to-azure"></a>Inicio de sesión en Azure

Abra [Azure Portal](https://portal.azure.com) e inicie sesión con su cuenta de Azure.

### <a name="navigate-to-the-app-in-the-azure-portal"></a>Navegue hasta la aplicación en Azure Portal

En el menú izquierdo, seleccione **App Services** y, después, el nombre de la aplicación.

![Navegación en el portal a la aplicación de Azure](./media/app-service-web-tutorial-custom-domain/select-app.png)

Consulte la página de administración de la aplicación de App Service.  

<a name="checkpricing"></a>

### <a name="check-the-pricing-tier"></a>Comprobar el plan de tarifa

En el panel de navegación izquierdo de la página de la aplicación, desplácese hasta la sección **Configuración** y seleccione **Escalar verticalmente (plan de App Service)**.

![Menú Escalar verticalmente](./media/app-service-web-tutorial-custom-domain/scale-up-menu.png)

El nivel actual de la aplicación aparece resaltado con un cuadro azul. Asegúrese de que la aplicación web no está en el nivel **Gratis**. No se admiten DNS personalizados en el nivel **Gratis**. 

![Comprobar plan de tarifa](./media/app-service-web-tutorial-custom-domain/check-pricing-tier.png)

Si el plan de App Service no es **Gratis**, cierre la página **Elija su plan de tarifa** y vaya directamente a [Asignar un registro CNAME](#cname).

<a name="scaleup"></a>

### <a name="scale-up-the-app-service-plan"></a>Escalado verticalmente del plan de App Service

Seleccione alguno de los niveles de pago (**Compartido**, **Básico**, **Estándar** o **Premium**). 

Haga clic en **Seleccionar**.

![Comprobar plan de tarifa](./media/app-service-web-tutorial-custom-domain/choose-pricing-tier.png)

Cuando vea la siguiente notificación, significará que la operación de escalado se habrá completado.

![Confirmación de la operación de escalado](./media/app-service-web-tutorial-custom-domain/scale-notification.png)

<a name="cname"></a>

## <a name="map-a-cname-record"></a>Asignar un registro CNAME

En el ejemplo del tutorial, agregue un registro CNAME para el subdominio `www` (por ejemplo, `www.contoso.com`).

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records.md)]

### <a name="create-the-cname-record"></a>Crear un registro CNAME

Agregue un registro CNAME para asignar un subdominio al nombre de host predeterminado de la aplicación (`<app_name>.azurewebsites.net`, donde `<app_name>` es el nombre de la aplicación).

En el caso del dominio `www.contoso.com` del ejemplo, agregue un registro CNAME que asigne el nombre `www` a `<app_name>.azurewebsites.net`.

Después de agregar CNAME, la página de registros DNS es como la del ejemplo siguiente:

![Navegación en el portal a la aplicación de Azure](./media/app-service-web-tutorial-custom-domain/cname-record.png)

### <a name="enable-the-cname-record-mapping-in-azure"></a>Habilitación de la asignación de registros CNAME en Azure

En el panel de navegación izquierdo de la página de la aplicación en Azure Portal, seleccione **Dominios personalizados**. 

![Menú Dominio personalizado](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

En la página **Dominios personalizados** de la aplicación, agregue el nombre DNS personalizado completo (`www.contoso.com`) a la lista.

Seleccione el icono **+** situado junto a **Agregar nombre de host**.

![Agregar nombre de host](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

Escriba el nombre de dominio completo para el que ha agregado un registro CNAME, como `www.contoso.com`. 

Seleccione **Validar**.

Se activa el botón **Agregar nombre de host**. 

Asegúrese de que en **Tipo de registro de nombre de host** está seleccionado **CNAME (www.example.com o cualquier subdominio)**.

Seleccione **Agregar nombre de host**.

![Agregar nombre DNS a la aplicación](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname.png)

El nuevo nombre de host puede tardar un tiempo en reflejarse en la página **Dominios personalizados** de la aplicación. Intente actualizar el explorador para actualizar los datos.

![Registro CNAME agregado](./media/app-service-web-tutorial-custom-domain/cname-record-added.png)

Si se olvidó de un paso o cometió un error tipográfico en alguna parte anteriormente, verá un error de comprobación en la parte inferior de la página.

![Error de comprobación](./media/app-service-web-tutorial-custom-domain/verification-error-cname.png)

<a name="a"></a>

## <a name="map-an-a-record"></a>Asignar un registro A

En el ejemplo del tutorial, se agrega un registro A al dominio raíz (por ejemplo, `contoso.com`). 

<a name="info"></a>

### <a name="copy-the-apps-ip-address"></a>Copiar la dirección IP de la aplicación

Para asignar un registro A, se necesita la dirección IP externa de la aplicación. Dicha dirección IP se puede encontrar en la página **Dominios personalizados** de la aplicación en Azure Portal.

En el panel de navegación izquierdo de la página de la aplicación en Azure Portal, seleccione **Dominios personalizados**. 

![Menú Dominio personalizado](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

En la página **Dominios personalizados**, copie la dirección IP de la aplicación.

![Navegación en el portal a la aplicación de Azure](./media/app-service-web-tutorial-custom-domain/mapping-information.png)

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records.md)]

### <a name="create-the-a-record"></a>Crear el registro A

Para asignar un registro A a un aplicación, App Service requiere **dos** registros DNS:

- Un registro **A** que se asigna a la dirección IP de la aplicación.
- Un registro **TXT** que se asigna al nombre de host predeterminado de la aplicación `<app_name>.azurewebsites.net`. App Service usa este registro solo durante la configuración para comprobar que posee el dominio personalizado. Después de que el dominio personalizado se valida y se configura en App Service, puede eliminar el registro TXT. 

En el dominio `contoso.com` del ejemplo, cree los registros D y TXT según la tabla siguiente (`@` suele representar el dominio raíz). 

| Tipo de registro | Host | Valor |
| - | - | - |
| Una  | `@` | Dirección IP de [Copiar la dirección IP de la aplicación](#info) |
| TXT | `@` | `<app_name>.azurewebsites.net` |

Cuando se agregan los registros, la página de registros DNS es como la del ejemplo siguiente:

![Página de registros DNS](./media/app-service-web-tutorial-custom-domain/a-record.png)

<a name="enable-a"></a>

### <a name="enable-the-a-record-mapping-in-the-app"></a>Habilitar la asignación de registros A en la aplicación

De vuelta a la página **Dominios personalizados** de la aplicación en Azure Portal, agregue el nombre DNS personalizado completo (por ejemplo, `contoso.com`) a la lista.

Seleccione el icono **+** situado junto a **Agregar nombre de host**.

![Agregar nombre de host](./media/app-service-web-tutorial-custom-domain/add-host-name.png)

Escriba el nombre de dominio completo para el que ha configurado el registro A, como `contoso.com`.

Seleccione **Validar**.

Se activa el botón **Agregar nombre de host**. 

Asegúrese de que el **tipo de registro de nombre de host** esté establecido en el **registro D (ejemplo.com)**.

Seleccione **Agregar nombre de host**.

![Agregar nombre DNS a la aplicación](./media/app-service-web-tutorial-custom-domain/validate-domain-name.png)

El nuevo nombre de host puede tardar un tiempo en reflejarse en la página **Dominios personalizados** de la aplicación. Intente actualizar el explorador para actualizar los datos.

![Registro D agregado](./media/app-service-web-tutorial-custom-domain/a-record-added.png)

Si se olvidó de un paso o cometió un error tipográfico en alguna parte anteriormente, verá un error de comprobación en la parte inferior de la página.

![Error de comprobación](./media/app-service-web-tutorial-custom-domain/verification-error.png)

<a name="wildcard"></a>

## <a name="map-a-wildcard-domain"></a>Asignar un dominio con caracteres comodín

En el ejemplo del tutorial, asigne un [nombre DNS con caracteres comodín](https://en.wikipedia.org/wiki/Wildcard_DNS_record) (por ejemplo, `*.contoso.com`) a la aplicación de App Service mediante la adición de un registro CNAME. 

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records.md)]

### <a name="create-the-cname-record"></a>Crear un registro CNAME

Agregue un registro CNAME para asignar un nombre con caracteres comodín al nombre de host predeterminado de la aplicación (`<app_name>.azurewebsites.net`).

En el dominio `*.contoso.com` del ejemplo, el registro CNAME asignará el nombre `*` a `<app_name>.azurewebsites.net`.

Cuando se agrega CNAME, la página de registros DNS es como la del ejemplo siguiente:

![Navegación en el portal a la aplicación de Azure](./media/app-service-web-tutorial-custom-domain/cname-record-wildcard.png)

### <a name="enable-the-cname-record-mapping-in-the-app"></a>Habilitación de la asignación del registro CNAME en la aplicación

Ahora puede agregar cualquier subdominio que coincida con el nombre con caracteres comodín a la aplicación (por ejemplo, `sub1.contoso.com` y `sub2.contoso.com` coinciden con `*.contoso.com`). 

En el panel de navegación izquierdo de la página de la aplicación en Azure Portal, seleccione **Dominios personalizados**. 

![Menú Dominio personalizado](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

Seleccione el icono **+** situado junto a **Agregar nombre de host**.

![Agregar nombre de host](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

Escriba un nombre de dominio completo que coincida con el dominio con caracteres comodín (por ejemplo, `sub1.contoso.com`) y, después, seleccione **Validar**.

Se activa el botón **Agregar nombre de host**. 

Asegúrese de que en **Tipo de registro de nombre de host** está seleccionado **Registro CNAME (www.example.com o cualquier subdominio)**.

Seleccione **Agregar nombre de host**.

![Agregar nombre DNS a la aplicación](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname-wildcard.png)

El nuevo nombre de host puede tardar un tiempo en reflejarse en la página **Dominios personalizados** de la aplicación. Intente actualizar el explorador para actualizar los datos.

Vuelva a seleccionar el icono **+** de nuevo para agregar otro nombre de host que coincida con el dominio con caracteres comodín. Por ejemplo, agregue `sub2.contoso.com`.

![Registro CNAME agregado](./media/app-service-web-tutorial-custom-domain/cname-record-added-wildcard2.png)

## <a name="test-in-browser"></a>Probar en el explorador

Desplácese a los nombres DNS que configuró anteriormente (por ejemplo, `contoso.com`, `www.contoso.com`, `sub1.contoso.com` y `sub2.contoso.com`).

![Navegación en el portal a la aplicación de Azure](./media/app-service-web-tutorial-custom-domain/app-with-custom-dns.png)

## <a name="resolve-404-error-web-site-not-found"></a>Resolución del error 404 "No se encuentra el sitio web"

Si recibe un error HTTP 404 (No se encuentra) al ir a la dirección URL de su dominio personalizado, compruebe que su dominio se resuelve en la dirección IP de la aplicación mediante <a href="https://www.whatsmydns.net/" target="_blank">WhatsmyDNS.net</a>. Si no es así, esto podría deberse a uno de los siguientes motivos:

- En el dominio personalizado configurado falta un registro A o un registro CNAME.
- El cliente del explorador ha almacenado en caché la dirección IP antigua del dominio. Borre la caché y pruebe la resolución DNS de nuevo. En un equipo Windows, borre la memoria caché con `ipconfig /flushdns`.

<a name="virtualdir"></a>

## <a name="direct-default-url-to-a-custom-directory"></a>Direccionamiento de una dirección URL predeterminada a un directorio personalizado

De forma predeterminada, App Service dirige las solicitudes web al directorio raíz del código de la aplicación. Sin embargo, algunos marcos web no se inician en el directorio raíz. Por ejemplo, [Laravel](https://laravel.com/) se inicia en el subdirectorio `public`. Para continuar con el ejemplo de DNS `contoso.com`, se podría acceder a una aplicación en `http://contoso.com/public`, pero, en su lugar, puede que desee redirigir `http://contoso.com` al directorio `public`. Este paso no conlleva la resolución de DNS, pero sí es necesario personalizar el directorio virtual.

Para ello, seleccione **Configuración de la aplicación** en el panel de navegación izquierdo de la página de la aplicación web. 

En la parte inferior de la página, el directorio virtual raíz `/` apunta a `site\wwwroot` de forma predeterminada, que es el directorio raíz del código de la aplicación. Cambie esta configuración para que, en su lugar, apunte, por ejemplo, a `site\wwwroot\public` y después guarde los cambios. 

![Personalización del directorio virtual](./media/app-service-web-tutorial-custom-domain/customize-virtual-directory.png)

Una vez completada la operación, la aplicación debe devolver la página derecha en la ruta de acceso raíz (por ejemplo, http://contoso.com).

## <a name="automate-with-scripts"></a>Automatizar con scripts

Puede automatizar la administración e dominios personalizados con scripts, mediante la [CLI de Azure](/cli/azure/install-azure-cli) o [Azure PowerShell](/powershell/azure/overview). 

### <a name="azure-cli"></a>Azure CLI 

El comando siguiente agrega un nombre DNS personalizado configurado a una aplicación de App Service. 

```bash 
az webapp config hostname add \
    --webapp-name <app_name> \
    --resource-group <resource_group_name> \ 
    --hostname <fully_qualified_domain_name> 
``` 

Para más información, consulte [Asignación de un dominio personalizado a una aplicación web](scripts/app-service-cli-configure-custom-domain.md). 

### <a name="azure-powershell"></a>Azure PowerShell 

El comando siguiente agrega un nombre DNS personalizado configurado a una aplicación de App Service. 

```PowerShell  
Set-AzureRmWebApp `
    -Name <app_name> `
    -ResourceGroupName <resource_group_name> ` 
    -HostNames @("<fully_qualified_domain_name>","<app_name>.azurewebsites.net") 
```

Para obtener más información, vea [Asignación de un dominio personalizado a una aplicación web](scripts/app-service-powershell-configure-custom-domain.md).

## <a name="next-steps"></a>pasos siguientes

En este tutorial aprendió lo siguiente:

> [!div class="checklist"]
> * Asignar un subdominio mediante el uso de un registro CNAME
> * Asignar un dominio raíz mediante el uso de un registro D
> * Asignar un dominio con comodín mediante el uso de un registro CNAME
> * Automatizar la asignación de dominio con scripts

Pase al siguiente tutorial para aprender a enlazar un certificado SSL personalizado a una aplicación web.

> [!div class="nextstepaction"]
> [Enlace de un certificado SSL personalizado a Azure Web Apps](app-service-web-tutorial-custom-ssl.md)
