---
title: Asignar un nombre DNS personalizado a Azure Web Apps | Microsoft Docs
description: "Aprenda a agregar un nombre de dominio DNS personalizado (dominio personal) a aplicaciones web, back-ends de aplicaciones móviles o aplicaciones de API en Azure App Service."
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
ms.topic: article
ms.date: 05/04/2017
ms.author: cephalin
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: 2db2ba16c06f49fd851581a1088df21f5a87a911
ms.openlocfilehash: 000440fb2c38eadc0ffdcab84a3c23bb034e834f
ms.contentlocale: es-es
ms.lasthandoff: 05/09/2017

---
# <a name="map-an-existing-custom-dns-name-to-azure-web-apps"></a>Asignar un nombre DNS personalizado a Azure Web Apps

En este tutorial se muestra cómo asignar un nombre DNS personalizado a [Azure Web Apps](app-service-web-overview.md). 

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
> 
> 

## <a name="prerequisites"></a>Requisitos previos

Para completar este tutorial, tiene que acceder al registro DNS del proveedor de dominios (como GoDaddy) y a los permisos para editar la configuración del dominio. 

Por ejemplo, para agregar entradas DNS para `contoso.com` y `www.contoso.com`, debe tener acceso para configurar las opciones de configuración DNS del dominio raíz `contoso.com`. 

> [!NOTE]
> Si no dispone de un nombre de dominio existente, considere la posibilidad de seguir el [tutorial sobre dominios de App Service](custom-dns-web-site-buydomains-web-app.md) para comprar un dominio mediante Azure Portal. 
>
>

## <a name="prepare-your-app"></a>Preparación de la aplicación
Para asignar un nombre de DNS personalizado, su [plan de App Service](https://azure.microsoft.com/pricing/details/app-service/) debe ser de nivel de pago (**Compartido**, **Básico**, **Estándar** o **Premium**). En este paso, asegúrese de que la aplicación de App Service se encuentra en el plan de tarifa compatible.

### <a name="sign-in-to-azure"></a>Inicio de sesión en Azure

Abra Azure Portal. Para ello, inicie sesión en [https://portal.azure.com/](https://portal.azure.com) con su cuenta de Azure.

### <a name="navigate-to-your-app"></a>Navegación a la aplicación
En el menú izquierdo, haga clic en **App Services** y luego, en el nombre de la aplicación.

![Navegación en el portal a la aplicación de Azure](./media/app-service-web-tutorial-custom-domain/select-app.png)

Ha llegado a la hoja de administración de la aplicación de App Service (_hoja_: página de portal que se abre horizontalmente).  

### <a name="check-the-pricing-tier"></a>Comprobación del plan de tarifa

En el panel de navegación izquierdo de la hoja de la aplicación, desplácese a la sección **Configuración** y seleccione **Escalar verticalmente (plan de App Service)**.

![Menú Escalar verticalmente](./media/app-service-web-tutorial-custom-domain/scale-up-menu.png)

Asegúrese de que la aplicación web no está en el nivel **Gratis**. El nivel actual de la aplicación aparece resaltado con un cuadro azul oscuro. 

![Comprobar plan de tarifa](./media/app-service-web-tutorial-custom-domain/check-pricing-tier.png)

No se admiten DNS personalizados en el nivel **Gratis**. Si necesita escalar verticalmente, siga la sección siguiente. Si no, cierre la hoja **Elija su plan de tarifa** y vaya directamente a [Asignar un registro CNAME](#cname) o [Asignar un registro A](#a).

### <a name="scale-up-your-app-service-plan"></a>Escalado vertical del plan de App Service

Seleccione alguno de los niveles de pago (**Compartido**, **Básico**, **Estándar** o **Premium**). 

Haga clic en **Seleccionar**.

![Comprobar plan de tarifa](./media/app-service-web-tutorial-custom-domain/choose-pricing-tier.png)

Cuando aparezca la siguiente notificación, la operación de escalado se habrá completado.

![Confirmación de la operación de escalado](./media/app-service-web-tutorial-custom-domain/scale-notification.png)

<a name="cname"></a>

## <a name="map-a-cname-record"></a>Asignar un registro CNAME

En el ejemplo del tutorial, quiere agregar un registro CNAME para el subdominio `www` (`www.contoso.com`). 

### <a name="access-dns-records-with-domain-provider"></a>Acceso a los registros DNS con el proveedor de dominios

En primer lugar, inicie sesión en el sitio web de su proveedor de dominios.

Busque la página de administración de registros DNS. Cada proveedor de dominios tiene su propia interfaz de registros DNS, por lo que debe consultar la documentación del proveedor. Busque los vínculos o áreas del sitio etiquetadas como **Nombre de dominio**, **DNS** o **Administración del servidor del nombres**. 

A menudo, puede encontrar el vínculo al consultar la información de la cuenta y al buscar un vínculo como **Mis dominios**. Después, busque un vínculo que le permita administrar registros DNS. Este vínculo podría denominarse **Archivo de zona**, **Registros DNS** o **Configuración avanzada**.

La captura de pantalla siguiente es un ejemplo de página de registros DNS:

![Página de registros DNS de ejemplo](./media/app-service-web-tutorial-custom-domain/example-record-ui.png)

En la captura de pantalla de ejemplo, haga clic en **Agregar** para crear un registro. Algunos proveedores tienen diferentes vínculos para agregar diferentes tipos de registros. De nuevo, consulte la documentación del proveedor.

> [!NOTE]
> En el caso de ciertos proveedores, como GoDaddy, los cambios en los registros DNS no se harán efectivos hasta que haga clic en un vínculo **Guardar cambios** independiente. 
>
>

### <a name="create-the-cname-record"></a>Crear un registro CNAME

Agregue un registro CNAME para asignar un subdominio al nombre de host predeterminado de la aplicación (`<app_name>.azurewebsites.net`).

En el dominio `www.contoso.com` del ejemplo, el registro CNAME debe apuntar al nombre `www` en `<app_name>.azurewebsites.net`.

La página de registros DNS debe tener un aspecto similar al de la captura de pantalla siguiente:

![Navegación en el portal a la aplicación de Azure](./media/app-service-web-tutorial-custom-domain/cname-record.png)

### <a name="enable-the-cname-record-mapping-in-your-app"></a>Habilitar la asignación de registros CNAME en la aplicación

Ahora puede agregar el nombre DNS configurado a la aplicación.

En el panel de navegación izquierdo de la hoja de la aplicación, haga clic en **Dominios personalizados**. 

![Menú Dominio personalizado](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

En la hoja **Dominios personalizados** de la aplicación, tiene que agregar el nombre completo del registro DNS personalizado (`www.contoso.com`) a la lista.

Haga clic en el icono **+** situado junto a **Agregar nombre de host**.

![Agregar nombre de host](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

Escriba el nombre de dominio completo para el que ha configurado anteriormente el registro CNAME (por ejemplo, `www.contoso.com`) y haga clic en **Validar**.

De lo contrario, se activa el botón **Agregar nombre de host**. 

Asegúrese de que **Tipo de registro de nombre de host** se establece en **Registro CNAME (example.com)**.

Haga clic en **Agregar nombre de host** para agregar el nombre DNS a la aplicación.

![Agregar nombre DNS a la aplicación](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname.png)

El nuevo nombre de host puede tardar algún tiempo en reflejarse en la página **Dominios personalizados** de la aplicación. Intente actualizar el explorador para actualizar los datos.

![Registro CNAME agregado](./media/app-service-web-tutorial-custom-domain/cname-record-added.png)

Si se olvidó de un paso o cometió un error tipográfico en alguna parte anteriormente, verá un error de comprobación en la parte inferior de la página.

![Error de comprobación](./media/app-service-web-tutorial-custom-domain/verification-error-cname.png)

<a name="a"></a>

## <a name="map-an-a-record"></a>Asignar un registro A

En el ejemplo del tutorial, desea agregar un registro D para el dominio raíz, `contoso.com`. 

<a name="info"></a>

### <a name="copy-your-apps-ip-address"></a>Copiar la dirección IP de la aplicación

Para asignar un registro A, necesita la dirección IP externa de la aplicación. Encontrará esta dirección IP en la hoja **Dominios personalizados**.

En el panel de navegación izquierdo de la hoja de la aplicación, haga clic en **Dominios personalizados**. 

![Menú Dominio personalizado](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

En la página **Dominios personalizados**, copie la dirección IP de la aplicación.

![Navegación en el portal a la aplicación de Azure](./media/app-service-web-tutorial-custom-domain/mapping-information.png)

### <a name="access-dns-records-with-domain-provider"></a>Acceso a los registros DNS con el proveedor de dominios

En primer lugar, inicie sesión en el sitio web de su proveedor de dominios.

Busque la página de administración de registros DNS. Cada proveedor de dominios tiene su propia interfaz de registros DNS, por lo que debe consultar la documentación del proveedor. Busque los vínculos o áreas del sitio etiquetadas como **Nombre de dominio**, **DNS** o **Administración del servidor del nombres**. 

A menudo, puede encontrar el vínculo al consultar la información de la cuenta y al buscar un vínculo como **Mis dominios**. Después, busque un vínculo que le permita administrar registros DNS. Este vínculo podría denominarse **Archivo de zona**, **Registros DNS** o **Configuración avanzada**.

La captura de pantalla siguiente es un ejemplo de página de registros DNS:

![Página de registros DNS de ejemplo](./media/app-service-web-tutorial-custom-domain/example-record-ui.png)

En la captura de pantalla de ejemplo, haga clic en **Agregar** para crear un registro. Algunos proveedores tienen diferentes vínculos para agregar diferentes tipos de registros. De nuevo, consulte la documentación del proveedor.

> [!NOTE]
> En el caso de ciertos proveedores, como GoDaddy, los cambios en los registros DNS no se harán efectivos hasta que haga clic en un vínculo **Guardar cambios** independiente. 
>
>

<a name="create-a"></a>

### <a name="create-the-a-record"></a>Crear el registro A

Para asignar un registro D a la aplicación, App Service necesita en realidad **dos** registros DNS:

- Un registro **D** para asignar a la dirección IP de la aplicación.
- Un registro **TXT** para asignar al nombre de host predeterminado de la aplicación, `<app_name>.azurewebsites.net`. Este registro permite a App Service comprobar que posee el dominio personalizado que quiere asignar.

En el dominio `www.contoso.com` del ejemplo, cree los registros D y TXT según la tabla siguiente (`@` suele representar el dominio raíz). 

| Tipo de registro | Host | Valor |
| - | - | - |
| Una  | `@` | Dirección IP del paso [Copiar la dirección IP de la aplicación](#info) |
| TXT | `@` | `<app_name>.azurewebsites.net` |

La página de registros DNS debe tener un aspecto similar al de la captura de pantalla siguiente:

![Página de registros DNS](./media/app-service-web-tutorial-custom-domain/a-record.png)

<a name="enable-a"></a>

### <a name="enable-the-a-record-mapping-in-your-app"></a>Habilitar la asignación de registros D en la aplicación

Ahora puede agregar el nombre DNS configurado a la aplicación.

De vuelta a la página **Dominios personalizados** de la aplicación en Azure Portal, tiene que agregar el nombre completo del registro DNS personalizado (`contoso.com`) a la lista.

Haga clic en el icono **+** situado junto a **Agregar nombre de host**.

![Agregar nombre de host](./media/app-service-web-tutorial-custom-domain/add-host-name.png)

Escriba el nombre de dominio completo para el que ha configurado anteriormente el registro D (por ejemplo, `contoso.com`) y haga clic en **Validar**.

De lo contrario, se activa el botón **Agregar nombre de host**. 

Asegúrese de que el **tipo de registro de nombre de host** esté establecido en el **registro D (ejemplo.com)**.

Haga clic en **Agregar nombre de host** para agregar el nombre DNS a la aplicación.

![Agregar nombre DNS a la aplicación](./media/app-service-web-tutorial-custom-domain/validate-domain-name.png)

El nuevo nombre de host puede tardar algún tiempo en reflejarse en la página **Dominios personalizados** de la aplicación. Intente actualizar el explorador para actualizar los datos.

![Registro D agregado](./media/app-service-web-tutorial-custom-domain/a-record-added.png)

Si se olvidó de un paso o cometió un error tipográfico en alguna parte anteriormente, verá un error de comprobación en la parte inferior de la página.

![Error de comprobación](./media/app-service-web-tutorial-custom-domain/verification-error.png)

<a name="wildcard"></a>

## <a name="map-a-wildcard-domain"></a>Asignar un dominio con caracteres comodín

También puede asignar un [DNS con caracteres comodín](https://en.wikipedia.org/wiki/Wildcard_DNS_record) (por ejemplo, `*.contoso.com`) a la aplicación de App Service. 

Los pasos que aquí se ofrecen muestran cómo asignar el dominio con caracteres comodín `*.contoso.com` mediante un registro CNAME. 

### <a name="access-dns-records-with-domain-provider"></a>Acceso a los registros DNS con el proveedor de dominios

En primer lugar, inicie sesión en el sitio web de su proveedor de dominios.

Busque la página de administración de registros DNS. Cada proveedor de dominios tiene su propia interfaz de registros DNS, por lo que debe consultar la documentación del proveedor. Busque los vínculos o áreas del sitio etiquetadas como **Nombre de dominio**, **DNS** o **Administración del servidor del nombres**. 

A menudo, puede encontrar el vínculo al consultar la información de la cuenta y al buscar un vínculo como **Mis dominios**. Después, busque un vínculo que le permita administrar registros DNS. Este vínculo podría denominarse **Archivo de zona**, **Registros DNS** o **Configuración avanzada**.

La captura de pantalla siguiente es un ejemplo de página de registros DNS:

![Página de registros DNS de ejemplo](./media/app-service-web-tutorial-custom-domain/example-record-ui.png)

En la captura de pantalla de ejemplo, haga clic en **Agregar** para crear un registro. Algunos proveedores tienen diferentes vínculos para agregar diferentes tipos de registros. De nuevo, consulte la documentación del proveedor.

> [!NOTE]
> En el caso de ciertos proveedores, como GoDaddy, los cambios en los registros DNS no se harán efectivos hasta que haga clic en un vínculo **Guardar cambios** independiente. 
>
>

### <a name="create-the-cname-record"></a>Crear un registro CNAME

Agregue un registro CNAME para asignar un nombre con caracteres comodín al nombre de host predeterminado de la aplicación (`<app_name>.azurewebsites.net`).

En el dominio `*.contoso.com` del ejemplo, el registro CNAME debe apuntar al nombre `*` en `<app_name>.azurewebsites.net`.

La página de registros DNS debe tener un aspecto similar al de la captura de pantalla siguiente:

![Navegación en el portal a la aplicación de Azure](./media/app-service-web-tutorial-custom-domain/cname-record.png)

### <a name="enable-the-cname-record-mapping-in-your-app"></a>Habilitar la asignación de registros CNAME en la aplicación

Ahora puede agregar cualquier subdominio que coincida con el nombre con caracteres comodín.

En el ejemplo de caracteres comodín `*.contoso.com`, ahora puede agregar `sub1.contoso.com` y `sub2.contoso.com`. 

En el panel de navegación izquierdo de la hoja de la aplicación, haga clic en **Dominios personalizados**. 

![Menú Dominio personalizado](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

Haga clic en el icono **+** situado junto a **Agregar nombre de host**.

![Agregar nombre de host](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

Escriba el nombre de dominio completo de un subdominio que coincida con su dominio con caracteres comodín (por ejemplo, `sub1.contoso.com`) y haga clic en **Validar**.

De lo contrario, se activa el botón **Agregar nombre de host**. 

Asegúrese de que **Tipo de registro de nombre de host** se establece en **Registro CNAME (example.com)**.

Haga clic en **Agregar nombre de host** para agregar el nombre DNS a la aplicación.

![Agregar nombre DNS a la aplicación](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname-wildcard.png)

El nuevo nombre de host puede tardar algún tiempo en reflejarse en la página **Dominios personalizados** de la aplicación. Intente actualizar el explorador para actualizar los datos.

Puede hacer clic en el icono **+** de nuevo para agregar otro nombre de host que coincida con su dominio con caracteres comodín.

Por ejemplo, agregue `sub2.contoso.com` siguiendo los mismos pasos de antes.

![Registro CNAME agregado](./media/app-service-web-tutorial-custom-domain/cname-record-added-wildcard2.png)

## <a name="test-in-browser"></a>Probar en el explorador

En el explorador, busque los nombres DNS que configuró anteriormente (`contoso.com` y `www.contoso.com`).

![Navegación en el portal a la aplicación de Azure](./media/app-service-web-tutorial-custom-domain/app-with-custom-dns.png)

## <a name="automate-with-scripts"></a>Automatizar con scripts

Puede automatizar la administración e dominios personalizados con scripts, mediante la [CLI de Azure](/cli/azure/install-azure-cli) o [Azure PowerShell](/powershell/azure/overview). 

### <a name="azure-cli"></a>CLI de Azure 

El comando siguiente agrega un nombre DNS personalizado configurado a una aplicación de App Service. 

```bash 
az appservice web config hostname add \
    --webapp <app_name> \
    --resource-group <resourece_group_name> \ 
    --name <fully_qualified_domain_name> 
``` 

Para obtener más información, vea [Asignación de un dominio personalizado a una aplicación web](scripts/app-service-cli-configure-custom-domain.md) 

### <a name="azure-powershell"></a>Azure PowerShell 

El comando siguiente agrega un nombre DNS personalizado configurado a una aplicación de App Service. 

```PowerShell  
Set-AzureRmWebApp `
    -Name <app_name> `
    -ResourceGroupName <resourece_group_name> ` 
    -HostNames @("<fully_qualified_domain_name>","<app_name>.azurewebsites.net") 
```

Para obtener más información, vea [Asignación de un dominio personalizado a una aplicación web](scripts/app-service-powershell-configure-custom-domain.md).

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha aprendido cómo:

> [!div class="checklist"]
> * Asignar un subdominio mediante el uso de un registro CNAME
> * Asignar un dominio raíz mediante el uso de un registro D
> * Asignar un dominio con comodín mediante el uso de un registro CNAME
> * Automatizar la asignación de dominio con scripts

Pase al siguiente tutorial para aprender a enlazarlo a un certificado SSL personalizado.

> [!div class="nextstepaction"]
> [Enlazar un certificado SSL personalizado a Azure Web Apps](app-service-web-tutorial-custom-ssl.md)

