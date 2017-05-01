---
title: "Asignación de un nombre DNS personalizado a una aplicación web de Azure| Microsoft Docs"
description: "Aprenda a agregar un nombre de dominio DNS personalizado (por ejemplo, dominio personal) a aplicaciones web, back-ends de aplicaciones móviles o aplicaciones de API en Azure App Service."
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
ms.date: 03/29/2017
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: 26d460a699e31f6c19e3b282fa589ed07ce4a068
ms.openlocfilehash: 25ddbd0c89ec7eee8eb240cf111245bb8b5add48
ms.lasthandoff: 04/04/2017


---
# <a name="map-a-custom-dns-name-to-an-azure-web-app"></a>Asignación de un nombre DNS personalizado a una aplicación web de Azure

En este tutorial se muestra cómo asignar un nombre DNS personalizado (por ejemplo, un dominio personal) a su aplicación web, al back-end de aplicaciones móviles o a una aplicación de API en [Azure App Service](../app-service/app-service-value-prop-what-is.md). 

![Navegación en el portal a la aplicación de Azure](./media/app-service-web-tutorial-custom-domain/app-with-custom-dns.png)

En este tutorial se sigue el escenario de ejemplo de la asignación de dos nombres DNS a una aplicación en App Service:

- `contoso.com`: un dominio raíz. Usará un [registro D](https://en.wikipedia.org/wiki/List_of_DNS_record_types#A) para asignarlo a Azure. 
- `www.contoso.com`: un subdominio de `contoso.com`. Puede usar un [ registro D](https://en.wikipedia.org/wiki/List_of_DNS_record_types#A) o un [registro CNAME](https://en.wikipedia.org/wiki/CNAME_record).

En el tutorial también se muestra cómo asignar un nombre [DNS con comodín](https://en.wikipedia.org/wiki/Wildcard_DNS_record) a App Service (por ejemplo, `*.contoso.com`).

## <a name="before-you-begin"></a>Antes de empezar

Dado que en este tutorial se muestra cómo asignar un nombre DNS a Azure App Service, debe tener acceso administrativo a la página de configuración de DNS de su proveedor de dominios respectivo (por ejemplo, GoDaddy). Por ejemplo, para agregar una asignación para `contoso.com` y `www.contoso.com`, es necesario que pueda configurar entradas DNS para `contoso.com`.

Si todavía no ha registrado un dominio con un proveedor de dominios, siempre puede [comprar simplemente un dominio directamente a Azure y asignarlo a la aplicación](custom-dns-web-site-buydomains-web-app.md).

## <a name="step-1---prepare-your-app"></a>Paso 1: Preparación de la aplicación
Para asignar un nombre DNS personalizado, su [plan de App Service](https://azure.microsoft.com/pricing/details/app-service/) debe estar en el nivel **Compartido** o superior. En este paso, asegúrese de que la aplicación de Azure se encuentra en el plan de tarifa admitido.

### <a name="log-in-to-azure"></a>Inicie sesión en Azure.

Abra Azure Portal. 

Para ello, inicie sesión en [https://portal.azure.com/](https://portal.azure.com) con su cuenta de Azure.

### <a name="navigate-to-your-app"></a>Navegación a la aplicación
En el menú izquierdo, haga clic en **App Service** y luego haga clic en el nombre de la aplicación de Azure.

![Navegación en el portal a la aplicación de Azure](./media/app-service-web-tutorial-custom-domain/select-app.png)

Ha llegado a la _hoja_ de la aplicación (una página del portal que se abre horizontalmente).  

### <a name="check-the-pricing-tier"></a>Comprobación del plan de tarifa
En la página **Descripción general**, que se abre de forma predeterminada, compruebe que la aplicación no esté en el nivel **Gratis**.

![Navegación en el portal a la aplicación de Azure](./media/app-service-web-tutorial-custom-domain/check-pricing-tier.png)

No se admiten DNS personalizados en el nivel **Gratis**. Si necesita escalar verticalmente, siga la sección siguiente. De lo contrario, vaya al [Paso 2]().

### <a name="scale-up-your-app-service-plan"></a>Escalado vertical del plan de App Service

Para escalar verticalmente el plan, haga clic en **Escalar verticalmente (plan de App Service)** en el panel izquierdo.

Seleccione el nivel que quiera escalar. Por ejemplo, seleccione **Compartido**. Cuando esté listo, haga clic en **Seleccionar**.

![Navegación en el portal a la aplicación de Azure](./media/app-service-web-tutorial-custom-domain/choose-pricing-tier.png)

Cuando aparezca la siguiente notificación, la operación de escalado se habrá completado.

![Navegación en el portal a la aplicación de Azure](./media/app-service-web-tutorial-custom-domain/scale-notification.png)

<a name="info"></a>

## <a name="step-2---get-mapping-information"></a>Paso 2: Obtención de información de asignación

En este paso, obtendrá la información que necesitará para crear los registros DNS más adelante. 

### <a name="open-the-custom-domain-ui"></a>Apertura de la IU del dominio personalizado

En la hoja de la aplicación, haga clic en **Dominios personalizados** en el menú. 

![Navegación en el portal a la aplicación de Azure](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

### <a name="copy-the-mapping-information"></a>Copia de la información de asignación

En la página **Dominios personalizados**, copie la **dirección IP** de la aplicación y su nombre de host predeterminado en **Nombres de host asignados al sitio**.

Más adelante necesitará la dirección IP para la asignación de registros D y el nombre de host predeterminado para la asignación de registros CNAME.

![Navegación en el portal a la aplicación de Azure](./media/app-service-web-tutorial-custom-domain/mapping-information.png)

## <a name="step-3---add-dns-records"></a>Paso 3: Adición de registros DNS 

En este paso, agregará los registros DNS necesarios para asignar su DNS personalizado a la aplicación. Este paso se realiza con su propio proveedor de dominios.

### <a name="access-dns-records-with-domain-provider"></a>Acceso a los registros DNS con el proveedor de dominios

En primer lugar, inicie sesión en el sitio web de su proveedor de dominios.

A continuación, deberá buscar la página de administración de registros DNS. Cada proveedor de dominios tiene su propia interfaz de registros DNS, por lo que debe consultar la documentación del proveedor. En general, debe buscar vínculos o áreas del sitio etiquetadas como **Nombre de dominio**, **DNS** o **Administración de servidores de nombres**. A menudo, puede encontrar el vínculo al consultar la información de la cuenta y al buscar un vínculo como **Mis dominios**. 

Después, busque un vínculo que le permita administrar registros DNS. Este vínculo podría denominarse **Archivo de zona**, **Registros DNS** o **Configuración avanzada**.

La captura de pantalla siguiente es un ejemplo de la apariencia que podría tener su página de registros DNS:

![Navegación en el portal a la aplicación de Azure](./media/app-service-web-tutorial-custom-domain/example-record-ui.png)

En la captura de pantalla de ejemplo, haga clic en **Agregar** para crear un registro. Algunos proveedores tienen diferentes vínculos para agregar diferentes tipos de registros. De nuevo, consulte la documentación del proveedor.

> [!NOTE]
> En el caso de ciertos proveedores, como GoDaddy, los cambios en los registros DNS no se harán efectivos hasta que haga clic en un vínculo **Guardar cambios** independiente después de crearlos. 
>
>

<a name="a"></a>

### <a name="create-an-a-record"></a>Creación de un registro A

En el ejemplo del tutorial, desea agregar un registro D para el dominio raíz, `contoso.com`. 

Para asignar un registro D a la aplicación, App Service necesita en realidad _dos_ registros DNS:

- Un registro **D** para asignar a la dirección IP de la aplicación.
- Un registro **TXT** para asignar al nombre de host predeterminado de la aplicación. Este registro permite a App Service comprobar que posee el dominio personalizado que quiere asignar.

En la tabla siguiente se muestra cómo configurar una asignación de registro D para los tipos de dominio admitidos (`@` normalmente representa el dominio raíz). 

<table cellspacing="0" border="1">
  <tr>
    <th>Tipo de dominio</th>
    <th>Tipo de registro</th>
    <th>Host</th>
    <th>Valor</th>
  </tr>
  <tr>
    <td rowspan="2" align="left">Dominio raíz<br>(por ejemplo, <code>contoso.com</code>)</td>
    <td>Una </td>
    <td>@</td>
    <td>Dirección IP del <a href="#info">Paso 2</a></td>
  </tr>
  <tr>
    <td>TXT</td>
    <td>@</td>
    <td>Nombre de host predeterminado del <a href="#info">Paso2</a></td>
  </tr>
  <tr>
    <td rowspan="2" align="left">Subdominio<br>(por ejemplo, <code>www.contoso.com</code>)</td>
    <td>Una </td>
    <td>www</td>
    <td>Dirección IP del <a href="#info">Paso 2</a></td>
  </tr>
  <tr>
    <td>TXT</td>
    <td>www</td>
    <td>Nombre de host predeterminado del <a href="#info">Paso2</a></td>
  </tr>
  <tr>
    <td rowspan="2" align="left">Dominio con comodín<br>(por ejemplo, <code>*.contoso.com</code>)</td>
    <td>Una </td>
    <td>\*</td>
    <td>Dirección IP del <a href="#info">Paso 2</a></td>
  </tr>
  <tr>
    <td>TXT</td>
    <td>\*</td>
    <td>Nombre de host predeterminado del <a href="#info">Paso2</a></td>
  </tr>
</table>

Para el ejemplo de dominio `contoso.com`, la página de registros DNS aparece como en la siguiente captura de pantalla:

![Navegación en el portal a la aplicación de Azure](./media/app-service-web-tutorial-custom-domain/a-record.png)

<a name="cname"></a>

### <a name="create-a-cname-record"></a>Creación de un registro CNAME
En el ejemplo del tutorial, quiere agregar un registro CNAME para el dominio raíz, `contoso.com`. 

A diferencia de la [asignación de un registro D](#a), para asignar un registro CNAME a su aplicación no es necesario ningún registro adicional.

> [!IMPORTANT]
> Se recomienda el uso de una asignación de CNAME. Si elimina la aplicación y la crea de nuevo o cambia de un nivel de hospedaje dedicado al nivel **Compartido**, la dirección IP virtual de la aplicación puede cambiar. Una asignación de CNAME es válida a pesar de un cambio así, mientras que una asignación de D se invalida potencialmente con una nueva dirección IP. 
>
> Sin embargo, _no_puede crear un registro CNAME para el dominio raíz (por ejemplo, el "registro raíz"). Para más información, consulte [Why can't a CNAME record be used at the root domain](http://serverfault.com/questions/613829/why-cant-a-cname-record-be-used-at-the-apex-aka-root-of-a-domain)(Por qué no se puede usar un registro CNAME en el dominio raíz).
> Para asignar un dominio raíz a la aplicación de Azure, utilice en su lugar un [registro A](#a) .
> 
> 

En la tabla siguiente se muestra cómo configurar una asignación de registros CNAME para los tipos de dominio admitidos.

Configure su registro CNAME de la siguiente manera.

<table cellspacing="0" border="1">
  <tr>
    <th>Tipo de dominio</th>
    <th>Host CNAME</th>
    <th>Valor CNAME</th>
  </tr>
  <tr>
    <td>Subdominio<br>(por ejemplo, <code>www.contoso.com</code>)</td>
    <td>www</td>
    <td>Nombre de host predeterminado del <a href="#info">Paso2</a></td>
  </tr>
  <tr>
    <td>Dominio con comodín<br>(por ejemplo, <code>*.contoso.com</code>)</td>
    <td>\*</td>
    <td>Nombre de host predeterminado del <a href="#info">Paso2</a></td>
  </tr>
</table>

Para el ejemplo de dominio `www.contoso.com`, la página de registros DNS aparece como en la siguiente captura de pantalla:

![Navegación en el portal a la aplicación de Azure](./media/app-service-web-tutorial-custom-domain/cname-record.png)

<a name="enable"></a>

## <a name="step-4---enable-the-custom-dns-in-your-app"></a>Paso 4: Habilitación de DNS personalizado en la aplicación

Ahora puede agregar sus nombres DNS configurados (por ejemplo, `contoso.com` y `www.contoso.com`) a su aplicación.

De vuelta a la página **Dominios personalizados** de su aplicación en el portal de Azure (consulte el [Paso 1](#info)), deberá agregar el nombre de dominio completo (FQDN) de su dominio personalizado a la lista.

### <a name="add-the-a-hostname-to-your-app"></a>Adición del nombre de host D a la aplicación

Haga clic en el icono **+** situado junto a **Agregar nombre de host**.

![Navegación en el portal a la aplicación de Azure](./media/app-service-web-tutorial-custom-domain/add-host-name.png)

Escriba el FQDN para el que configuró anteriormente el registro D (por ejemplo, `contoso.com`) y, a continuación, haga clic en **Validar**.

Si se olvidó de un paso o cometió un error tipográfico en alguna parte anteriormente, verá un error de comprobación en la parte inferior de la página.

![Navegación en el portal a la aplicación de Azure](./media/app-service-web-tutorial-custom-domain/verification-error.png)

De lo contrario, se activa el botón **Agregar nombre de host**. 

Asegúrese de que el **tipo de registro de nombre de host** esté establecido en el **registro D (ejemplo.com)**.

Haga clic en **Agregar nombre de host** para agregar el nombre DNS a la aplicación.

![Navegación en el portal a la aplicación de Azure](./media/app-service-web-tutorial-custom-domain/validate-domain-name.png)

El nuevo nombre de host puede tardar algún tiempo en reflejarse en la página **Dominios personalizados** de la aplicación. Intente actualizar el explorador para actualizar los datos.

![Navegación en el portal a la aplicación de Azure](./media/app-service-web-tutorial-custom-domain/a-record-added.png)

<a name="add-cname"></a>
### <a name="add-the-cname-hostname-to-your-app"></a>Adición del nombre de host de CNAME a la aplicación

Haga clic en el icono **+** situado junto a **Agregar nombre de host**.

![Navegación en el portal a la aplicación de Azure](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

Escriba el FQDN para el que ha configurado anteriormente el registro CNAME (por ejemplo, `www.contoso.com`) y, a continuación, haga clic en **Validar**.

Si se olvidó de un paso o cometió un error tipográfico en alguna parte anteriormente, verá un error de comprobación en la parte inferior de la página.

![Navegación en el portal a la aplicación de Azure](./media/app-service-web-tutorial-custom-domain/verification-error-cname.png)

De lo contrario, se activa el botón **Agregar nombre de host**. 

Asegúrese de que el **tipo de registro de nombre de host** esté establecido en el **registro D (ejemplo.com)**.

Haga clic en **Agregar nombre de host** para agregar el nombre DNS a la aplicación.

![Navegación en el portal a la aplicación de Azure](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname.png)

El nuevo nombre de host puede tardar algún tiempo en reflejarse en la página **Dominios personalizados** de la aplicación. Intente actualizar el explorador para actualizar los datos.

![Navegación en el portal a la aplicación de Azure](./media/app-service-web-tutorial-custom-domain/cname-record-added.png)

### <a name="step-5---test-in-browser"></a>Paso 5: Prueba en el explorador

En el explorador, busque los nombres DNS que configuró anteriormente (`contoso.com` y `www.contoso.com`).

![Navegación en el portal a la aplicación de Azure](./media/app-service-web-tutorial-custom-domain/app-with-custom-dns.png)

## <a name="more-resources"></a>Más recursos

[Comprar y configurar un nombre de dominio personalizado en el Servicio de aplicaciones de Azure](custom-dns-web-site-buydomains-web-app.md)

