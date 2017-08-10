---
title: "Integración de Azure DNS con los recursos de Azure | Microsoft Docs"
description: Aprenda a usar Azure DNS para proporcionar DNS a los recursos de Azure.
services: dns
documentationcenter: na
author: georgewallace
manager: timlt
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/31/2017
ms.author: gwallace
ms.translationtype: HT
ms.sourcegitcommit: fff84ee45818e4699df380e1536f71b2a4003c71
ms.openlocfilehash: 071c9c55d412514172106109eacc048722ab173e
ms.contentlocale: es-es
ms.lasthandoff: 08/01/2017

---

# <a name="use-azure-dns-to-provide-custom-domain-settings-for-an-azure-service"></a>Usar Azure DNS para proporcionar la configuración de un dominio personalizado para un servicio de Azure

Azure DNS proporciona DNS para un dominio personalizado de cualquiera de los recursos de Azure que admiten dominios personalizados o que tienen un nombre de dominio completo (FQDN). Por ejemplo, va a crear una aplicación web de Azure y desea que los usuarios obtengan acceso a ella a través de contoso.com o www.contoso.com como FQDN. Este artículo le guiará a través de la configuración del servicio de Azure con Azure DNS para usar dominios personalizados.

## <a name="prerequisites"></a>Requisitos previos

Para poder usar Azure DNS con su dominio personalizado, primero debe delegarlo a Azure DNS. Visite [Delegación de un dominio en Azure DNS](./dns-delegate-domain-azure-dns.md) para obtener instrucciones sobre cómo configurar los servidores de nombres para su delegación. Una vez que el dominio se ha delegado a la zona de Azure DNS, es posible configurar los registros DNS necesarios.

Puede configurar un dominio personal o personalizado para [aplicaciones de función de Azure](#azure-function-app), [IoT de Azure](#azure-iot), [direcciones IP públicas](#public-ip-address), [App Service (Web Apps)](#app-service-web-apps), [Blob Storage](#blob-storage) y [Azure CDN](#azure-cdn).

## <a name="azure-function-app"></a>Aplicación de función de Azure

Para configurar un dominio personalizado para las aplicaciones de función de Azure, se crea un registro CNAME, así como una configuración en la propia aplicación de función.
 
Vaya a **Otros** > **Aplicación de función** y seleccione la aplicación de función. Haga clic en **Características de la plataforma** y en **REDES** haga clic en **Dominios personalizados**.

![hoja de aplicación de función](./media/dns-custom-domain/functionapp.png)

Observe la dirección URL actual de la hoja **Dominios personalizados**. Esta dirección se utiliza como alias para el registro DNS que se creó.

![Hoja Dominios personalizados](./media/dns-custom-domain/functionshostname.png)

Vaya a la zona DNS y haga clic en **+ Conjunto de registros**. Rellene la información siguiente en la hoja **Agregar conjunto de registros** y haga clic en **Aceptar** para crearlo.

|Propiedad  |Valor  |Descripción  |
|---------|---------|---------|
|Nombre     | myfunctionapp        | Este valor junto con la etiqueta de nombre de dominio es el FQDN del nombre de dominio personalizado.        |
|Tipo     | CNAME        | Use un registro CNAME que use un alias.        |
|TTL     | 1        | 1 se usa para 1 hora        |
|Unidad de TTL     | Horas        | Las horas se utilizan como medida de tiempo         |
|Alias     | adatumfunction.azurewebsites.net        | El nombre DNS para el que va a crear el alias, en este ejemplo es el nombre DNS adatumfunction.azurewebsites.net proporcionado de forma predeterminada para la aplicación de función.        |

Regrese a la aplicación de función, haga clic en **Características de la plataforma** y, en **REDES**, haga clic en **Dominios personalizados**; a continuación, en **Nombres de host**, haga clic en **+ Agregar nombre de host**.

En la hoja **Agregar nombre de host**, escriba el registro CNAME en el campo de texto **Nombre de host** y haga clic en **Validar**. Si se pudo encontrar el registro, aparece el botón **Agregar nombre de host**. Haga clic en **Agregar nombre de host** para agregar el alias.

![hoja agregar nombre de host de aplicaciones de función](./media/dns-custom-domain/functionaddhostname.png)

## <a name="azure-iot"></a>Azure IoT

Azure IoT no dispone de las personalizaciones necesarias en el propio servicio. Para usar un dominio personalizado con IoT Hub solo se necesita un registro CNAME que señale hacia los recursos.

Vaya a **Internet de las cosas** > **IoT Hub** y seleccione su instancia de IoT Hub. En la hoja **Introducción**, observe el FQDN de la instancia de IoT Hub.

![Hoja del Centro de IoT](./media/dns-custom-domain/iot.png)

A continuación, vaya a la zona DNS y haga clic en **+ Conjunto de registros**. Rellene la información siguiente en la hoja **Agregar conjunto de registros** y haga clic en **Aceptar** para crearlo.


|Propiedad  |Valor  |Descripción  |
|---------|---------|---------|
|Nombre     | myiothub        | Este valor junto con la etiqueta de nombre de dominio es el FQDN del nombre de la instancia de IoT Hub.        |
|Tipo     | CNAME        | Use un registro CNAME que use un alias.
|TTL     | 1        | 1 se usa para 1 hora        |
|Unidad de TTL     | Horas        | Las horas se utilizan como medida de tiempo         |
|Alias     | adatumIOT.azure-devices.net        | El nombre DNS para el que va a crear el alias. En este ejemplo es el nombre de host adatumIOT.azure-devices.net proporcionado por la instancia de IoT Hub.

Una vez que se crea el registro, pruebe la resolución de nombre con el registro CNAME mediante `nslookup`

## <a name="public-ip-address"></a>Dirección IP pública

Para configurar un dominio personalizado para servicios que usan una dirección IP pública como, por ejemplo, Application Gateway, Load Balancer, Cloud Service, máquinas virtuales de Resource Manager o máquinas virtuales clásicas, se usa un registro CNAME.

Vaya a **Redes** > **Dirección IP pública**, seleccione el recurso de dirección IP pública y haga clic en **Configuración**. Anote la dirección IP.

![hoja ip pública](./media/dns-custom-domain/publicip.png)

Vaya a la zona DNS y haga clic en **+ Conjunto de registros**. Rellene la información siguiente en la hoja **Agregar conjunto de registros** y haga clic en **Aceptar** para crearlo.


|Propiedad  |Valor  |Descripción  |
|---------|---------|---------|
|Nombre     | mywebserver        | Este valor junto con la etiqueta de nombre de dominio es el FQDN del nombre de dominio personalizado.        |
|Tipo     | Una         | Use un registro A como recurso en una dirección IP.        |
|TTL     | 1        | 1 se usa para 1 hora        |
|Unidad de TTL     | Horas        | Las horas se utilizan como medida de tiempo         |
|Dirección IP     | <your ip address>       | La dirección IP pública.|

![creación de un registro A](./media/dns-custom-domain/arecord.png)

Una vez creado el registro A, ejecute `nslookup` para comprobar que el registro se resuelve.

![búsqueda de dns de dirección ip pública](./media/dns-custom-domain/publicipnslookup.png)

## <a name="app-service-web-apps"></a>App Service (Web Apps)

Los pasos siguientes le guiarán en el proceso de configuración de un dominio personalizado para una instancia de App Service Web Apps.

Vaya a **Web y móvil** > **App Service** y seleccione el recurso para el que está configurando un nombre de dominio personalizado y haga clic en **Dominios personalizados**.

Observe la dirección URL actual de la hoja **Dominios personalizados**. Esta dirección se utiliza como alias para el registro DNS que se creó.

![hoja dominios personalizados](./media/dns-custom-domain/url.png)

Vaya a la zona DNS y haga clic en **+ Conjunto de registros**. Rellene la información siguiente en la hoja **Agregar conjunto de registros** y haga clic en **Aceptar** para crearlo.


|Propiedad  |Valor  |Descripción  |
|---------|---------|---------|
|Nombre     | mywebserver        | Este valor junto con la etiqueta de nombre de dominio es el FQDN del nombre de dominio personalizado.        |
|Tipo     | CNAME        | Use un registro CNAME que use un alias. Si el recurso usa una dirección IP, se usará un registro A.        |
|TTL     | 1        | 1 se usa para 1 hora        |
|Unidad de TTL     | Horas        | Las horas se utilizan como medida de tiempo         |
|Alias     | webserver.azurewebsites.net        | El nombre DNS para el que va a crear el alias. En este ejemplo es el nombre DNS webserver.azurewebsites.net proporcionado de forma predeterminada para la aplicación web.        |


![Crear un registro CNAME](./media/dns-custom-domain/createcnamerecord.png)

Vuelva al servicio de aplicaciones configurado para el nombre de dominio personalizado. Haga clic en **Dominios personalizados** y, a continuación, en **Nombres de host**. Para agregar el registro CNAME que ha creado, haga clic en **+ Agregar nombre de host**.

![Figura 1](./media/dns-custom-domain/figure1.png)

Una vez completado el proceso, ejecute **nslookup** para comprobar que la resolución de nombres funciona.

![Figura 1](./media/dns-custom-domain/finalnslookup.png)

Para más información acerca de cómo asignar un dominio personalizado a App Service, visite [Asignar un nombre DNS personalizado a Azure Web Apps](../app-service-web/app-service-web-tutorial-custom-domain.md?toc=%dns%2ftoc.json).

Si necesita adquirir un dominio personalizado, visite [Comprar un nombre de dominio personalizado para Azure Web Apps](../app-service-web/custom-dns-web-site-buydomains-web-app.md) para más información sobre dominios de App Service.

## <a name="blob-storage"></a>Almacenamiento de blobs

Los siguientes pasos le guiarán en el proceso de configuración de un registro CNAME para una cuenta de Blob Storage mediante el método asverify. Este método garantiza que no hay ningún tiempo de inactividad.

Vaya a **Storage** > **Cuentas de almacenamiento**, seleccione la cuenta de almacenamiento y haga clic en **Dominio personalizado**. Anote el FQDN en el paso 2, este valor se utiliza para crear el primer registro CNAME

![dominio personalizado de blob storage](./media/dns-custom-domain/blobcustomdomain.png)

Vaya a la zona DNS y haga clic en **+ Conjunto de registros**. Rellene la información siguiente en la hoja **Agregar conjunto de registros** y haga clic en **Aceptar** para crearlo.


|Propiedad  |Valor  |Descripción  |
|---------|---------|---------|
|Nombre     | asverify.mystorageaccount        | Este valor junto con la etiqueta de nombre de dominio es el FQDN del nombre de dominio personalizado.        |
|Tipo     | CNAME        | Use un registro CNAME que use un alias.        |
|TTL     | 1        | 1 se usa para 1 hora        |
|Unidad de TTL     | Horas        | Las horas se utilizan como medida de tiempo         |
|Alias     | asverify.adatumfunctiona9ed.blob.core.windows.net        | El nombre DNS para el que va a crear el alias. En este ejemplo es el nombre DNS asverify.adatumfunctiona9ed.blob.core.windows.net proporcionado de forma predeterminada para la cuenta de almacenamiento.        |

Vuelva a la cuenta de almacenamiento haciendo clic en **Storage** > **Cuentas de almacenamiento**. Una vez allí, seleccione la cuenta de almacenamiento y haga clic en **Dominio personalizado**. Escriba el alias que ha creado sin el prefijo asverify en el cuadro de texto, active la casilla **Usar validación CNAME indirecta y haga clic en **Guardar**. Una vez completado este paso, vuelva a la zona DNS y cree un registro CNAME sin el prefijo asverify.  Después de ese momento, ya es seguro eliminar el registro CNAME con el prefijo cdnverify.

![dominio personalizado de blob storage](./media/dns-custom-domain/indirectvalidate.png)

Valide la resolución de DNS mediante la ejecución de `nslookup`

Para más información acerca de la asignación de un dominio personalizado a un punto de conexión de Blob Storage visite [Configurar un nombre de dominio personalizado para el punto de conexión de Blob Storage](../storage/storage-custom-domain-name.md?toc=%dns%2ftoc.json)

## <a name="azure-cdn"></a>Red CDN de Azure

Los siguientes pasos le guiarán en el proceso de configuración de un registro CNAME para un punto de conexión de CDN mediante el método cdnverify. Este método garantiza que no hay ningún tiempo de inactividad.

Vaya a **Redes** > **Perfiles de CDN**, seleccione el perfil de CDN y haga clic en **Puntos de conexión** en **General**.

Seleccione el punto de conexión con el que está trabajando y haga clic en **+ Dominio personalizado**. Observe el **nombre de host del punto de conexión** ya que este valor es el registro al que señala el registro CNAME.

![Dominio personalizado de CDN](./media/dns-custom-domain/endpointcustomdomain.png)

Vaya a la zona DNS y haga clic en **+ Conjunto de registros**. Rellene la información siguiente en la hoja **Agregar conjunto de registros** y haga clic en **Aceptar** para crearlo.

|Propiedad  |Valor  |Descripción  |
|---------|---------|---------|
|Nombre     | cdnverify.mycdnendpoint        | Este valor junto con la etiqueta de nombre de dominio es el FQDN del nombre de dominio personalizado.        |
|Tipo     | CNAME        | Use un registro CNAME que use un alias.        |
|TTL     | 1        | 1 se usa para 1 hora        |
|Unidad de TTL     | Horas        | Las horas se utilizan como medida de tiempo         |
|Alias     | cdnverify.adatumcdnendpoint.azureedge.net        | El nombre DNS para el que va a crear el alias. En este ejemplo es el nombre DNS cdnverify.adatumcdnendpoint.azureedge.net proporcionado de forma predeterminada para la cuenta de almacenamiento.        |

Vuelva al punto de conexión de CDN haciendo clic en **Redes** > **Perfiles de CDN** y, una vez allí, seleccione el perfil de CDN. Haga clic en **+ Dominio personalizado** y escriba el alias del registro CNAME sin el prefijo cdnverify y haga clic en **Agregar**.

Una vez completado este paso, vuelva a la zona DNS y cree un registro CNAME sin el prefijo cdnverify.  Después de ese momento, ya es seguro eliminar el registro CNAME con el prefijo cdnverify. Para más información sobre CDN y los procedimientos para configurar un dominio personalizado sin el paso de registro intermedio, visite [Asignación del contenido de la red CDN de Azure a un dominio personalizado](../cdn/cdn-map-content-to-custom-domain.md?toc=%dns%2ftoc.json).

## <a name="next-steps"></a>Pasos siguientes

Aprenda a [configurar búsquedas inversas de DNS para servicios hospedados en Azure](dns-reverse-dns-for-azure-services.md).
