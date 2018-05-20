---
title: 'Tutorial: Adición de un dominio personalizado a un punto de conexión de Azure CDN | Microsoft Docs'
description: En este tutorial se asigna el contenido del punto de conexión de Azure CDN a un dominio personalizado.
services: cdn
documentationcenter: ''
author: dksimpson
manager: akucer
editor: ''
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/06/2018
ms.author: v-deasim
ms.custom: mvc
ms.openlocfilehash: f57cfe19fd2eee0952a20fabcaa0d73ca96894ef
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/28/2018
---
# <a name="tutorial-add-a-custom-domain-to-your-azure-cdn-endpoint"></a>Tutorial: Adición de un dominio personalizado a un punto de conexión de Azure CDN
En este tutorial se muestra cómo agregar un dominio personalizado a un punto de conexión de Azure Content Delivery Network (CDN). Si se usa un punto de conexión de CDN para entregar contenido, se necesita un dominio personalizado si se desea que el nombre de dominio propio esté visible en la dirección URL de la red CDN. El hecho de tener un nombre de dominio visible puede ser cómodo para sus clientes y útil con fines de personalización de marca. 

Después de crear un punto de conexión de CDN en el perfil, el nombre del punto de conexión, que es un subdominio de azureedge.net, se incluye de forma predeterminada en la dirección URL de entrega de contenido de la red CDN (por ejemplo, https:\//contoso.azureedge.net/photo.png). Para su comodidad, Azure CDN permite asociar un dominio personalizado a un punto de conexión de CDN. Con esta opción, entrega el contenido con un dominio personalizado en la dirección URL, en lugar de un nombre de punto de conexión (por ejemplo, https:\//www.contoso.com/photo.png). 

En este tutorial, aprenderá a:
> [!div class="checklist"]
> - Crear un registro DNS de CNAME
> - Asociar el dominio personalizado a un punto de conexión de CDN
> - Comprobar el dominio personalizado

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>requisitos previos

Para poder completar los pasos de este tutorial, primero es preciso crear un perfil de CDN y al menos un punto de conexión de CDN. Para más información, consulte [Guía de inicio rápido: Creación de un perfil y un punto de conexión de Azure CDN](cdn-create-new-endpoint.md).

Si no dispone ya de un dominio personalizado, primero debe adquirir uno con un proveedor de dominios. Por ejemplo, vea [Compra de un nombre de dominio personalizado](https://docs.microsoft.com/azure/app-service/custom-dns-web-site-buydomains-web-app).

Si usa Azure para hospedar sus [dominios DNS](https://docs.microsoft.com/azure/dns/dns-overview), debe delegar el sistema de nombres de dominio (DNS) del proveedor de dominios en una instancia de Azure DNS. Para más información, vea [Delegación de un dominio en DNS de Azure](https://docs.microsoft.com/azure/dns/dns-delegate-domain-azure-dns). Por el contrario, si utiliza un proveedor de dominios para controlar su dominio DNS, diríjase a [Creación de un registro DNS de CNAME](#create-a-cname-dns-record).


## <a name="create-a-cname-dns-record"></a>Creación de un registro DNS de CNAME

Para poder usar un dominio personalizado con un punto de conexión de Azure CDN, primero debe crear un registro de nombre canónico (CNAME) con su proveedor de dominios para señalar a su punto de conexión de CDN. Un registro CNAME es un tipo de registro de DNS que asigna un nombre de dominio de origen a un nombre de dominio de destino. En el caso de Azure CDN, el nombre de dominio de origen es su nombre de dominio personalizado y el nombre de dominio de destino es el nombre de host del punto de conexión de CDN. Después de que Azure CDN comprueba el registro CNAME que crea, el tráfico dirigido al dominio personalizado de origen (como www.contoso.com) se enruta al nombre de host del punto de conexión de CDN de destino especificado (como contoso.azureedge.net). 

Un dominio personalizado y su subdominio no se pueden asociar con más de un solo punto de conexión a la vez. Sin embargo, puede utilizar sus dominios diferentes desde el mismo dominio personalizado para distintos puntos de conexión de servicio de Azure mediante el uso de varios registros de CNAME. También puede asignar un dominio personalizado con diferentes subdominios al mismo punto de conexión de CDN.


## <a name="map-the-temporary-cdnverify-subdomain"></a>Asignación del subdominio temporal cdnverify

Al asignar un dominio existente que esté en producción, existen consideraciones especiales. Mientras se registra un dominio personalizado en Azure Portal, se puede producir un breve período de inactividad en el dominio. Para evitar la interrupción del tráfico web, en primer lugar asigne su dominio personalizado al nombre de host de su punto de conexión de CDN con el subdominio cdnverify Azure para crear una asignación de CNAME temporal. Con este método, los usuarios pueden acceder a un dominio sin interrupción mientras se realiza la asignación de DNS. 

Por el contrario, si es la primera vez que usa un dominio personalizado y no se ejecuta tráfico de producción en él, puede asignar directamente el dominio personalizado en su punto de conexión de CDN. Diríjase a [Asignación de un dominio personalizado permanente](#map-permanent-custom-domain).

Para crear un registro CNAME con el subdominio cdnverify:

1. Inicie sesión en el sitio web del proveedor de dominios de su dominio personalizado.

2. Busque la página para administrar registros DNS mediante la consulta de la documentación del proveedor o busque las áreas del sitio web con las etiquetas **Nombre de dominio**, **DNS** o **Name Server Management** (Administración del servidor de nombres). 

3. Cree una entrada de registro CNAME para el dominio personalizado y rellene los campos como se muestra en la tabla siguiente (los nombres de campo pueden variar):

    | Origen                    | Escriba  | Destino                     |
    |---------------------------|-------|---------------------------------|
    | cdnverify.www.contoso.com | CNAME | cdnverify.contoso.azureedge.net |

    - Origen: escriba un nombre de dominio personalizado, incluido el subdominio cdnverify, con el siguiente formato: cdnverify. _&lt;nombre de dominio personalizado&gt;. Por ejemplo, cdnverify.www.contoso.com.

    - Tipo: escriba *CNAME*.

    - Destination (Destino): escriba el nombre de host del punto de conexión de CDN, incluido el subdominio cdnverify, con el siguiente formato: cdnverify._&lt;nombre de punto de conexión&gt;_.azureedge.net. Por ejemplo, cdnverify.contoso.azureedge.net.

4. Guarde los cambios.

Por ejemplo, este es el procedimiento para el registrador de dominios GoDaddy:

1. Inicie sesión y seleccione el dominio personalizado que desea usar.

2. En la sección Domains (Dominio)º, seleccione **Manage all** (Administrar todos) y, después, seleccione **DNS** | **Manage Zones** (Administrar Zonas).

3. En **Domain Name** (Nombre de dominio), escriba el dominio personalizado y seleccione **Search** (Buscar).

4. En la página **DNS Management** (Administración de SNS), seleccione **Add** (Agregar) y, después, seleccione **CNAME** en la lista **Type** (Tipo).

5. Complete los siguientes campos de la entrada de CNAME:

    ![Entrada de CNAME](./media/cdn-map-content-to-custom-domain/cdn-cdnverify-cname-entry.png)

    - Tipo: deje *CNAME* seleccionado.

    - Host: escriba el subdominio del dominio personalizado que va a usar, incluido el nombre de subdominio cdnverify. Por ejemplo, cdnverify.www.

    - Points to (Apunta a): escriba el nombre de host del punto de conexión de CDN, incluido el nombre de subdominio cdnverify. Por ejemplo, cdnverify.contoso.azureedge.net. 

    - TTL: deje la opción *1 Hour* (1 hora) seleccionada.

6. Seleccione **Guardar**.
 
    La entrada de CNAME se agrega a la tabla de registros DNS.

    ![Tabla de registros DNS](./media/cdn-map-content-to-custom-domain/cdn-cdnverify-dns-table.png)


## <a name="associate-the-custom-domain-with-your-cdn-endpoint"></a>Asociación del dominio personalizado a un punto de conexión de CDN

Una vez que haya registrado un dominio personalizado, puede agregarlo a su punto de conexión de CDN. 

1. Inicie sesión en [Azure Portal](https://portal.azure.com/) y navegue al perfil de CDN que contenga el punto de conexión que desea asignar un dominio personalizado.
    
2. En la página **Perfil de CDN**, seleccione el punto de conexión de CDN que se va a asociar con el dominio personalizado.

    Se abre la página **Punto de conexión**.
    
3. Seleccione **Dominio personalizado**. 

   ![Botón Dominio personalizado de CDN](./media/cdn-map-content-to-custom-domain/cdn-custom-domain-button.png)

4. En **Nombre de host personalizado**, escriba su dominio personalizado, incluido el subdominio. Por ejemplo, www.contoso.com o cdn.contoso.com. No utilice el nombre de subdominio cdnverify.

   ![Cuadro de diálogo Dominio personalizado de CDN](./media/cdn-map-content-to-custom-domain/cdn-add-custom-domain.png)

5. Seleccione **Agregar**.

   Azure comprueba que el registro CNAME existe para el nombre de dominio personalizado que ha especificado. Si el registro CNAME es correcto, el dominio personalizado se valida. La propagación del registro CNAME a los servidores de nombres puede llevar cierto tiempo. Si el dominio no se valida inmediatamente, compruebe que el registro CNAME sea correcto, espere unos minutos e inténtelo de nuevo. En el caso de los puntos de conexión de la red **CDN de Verizon**, la configuración del dominio personalizado puede tardar hasta 90 minutos en propagarse a todos los nodos perimetrales de la red CDN.  


## <a name="verify-the-custom-domain"></a>Comprobación del dominio personalizado

Después de haber completado el registro del dominio personalizado, compruebe que hace referencia a su punto de conexión de CDN.
 
1. Asegúrese de que tiene contenido público almacenado en caché en el punto de conexión. Por ejemplo, si su punto de conexión de CDN está asociado a una cuenta de almacenamiento, Azure CDN almacenará el contenido en la caché, en un contenedor público. Para probar el dominio personalizado, compruebe que el contenedor está establecido para permitir acceso público y que contiene al menos un archivo.

2. En el explorador, vaya a la dirección del archivo usando el dominio personalizado. Por ejemplo, si el dominio personalizado es cdn.contoso.com, la dirección URL al archivo almacenado en la caché sería similar a la siguiente: http:\//cdn.contoso.com/my-public-container/my-file.jpg.


## <a name="map-the-permanent-custom-domain"></a>Asignación de un dominio personalizado permanente

Si ha comprobado que el subdominio cdnverify se ha asignado correctamente a su punto de conexión (o si utiliza un nuevo dominio personalizado que no esté en producción), puede asignar el dominio personalizado directamente al nombre de host de su punto de conexión de CDN.

Para crear un registro CNAME para un dominio personalizado:

1. Inicie sesión en el sitio web del proveedor de dominios de su dominio personalizado.

2. Para encontrar la página de administración de los registros DNS, consulte la documentación del proveedor o busque las áreas del sitio web con la etiqueta **Nombre de dominio**, **DNS** o **Name Server Management** (Administración del servidor de nombres). 

3. Cree una entrada de registro CNAME para el dominio personalizado y rellene los campos como se muestra en la tabla siguiente (los nombres de campo pueden variar):

    | Origen          | Escriba  | Destino           |
    |-----------------|-------|-----------------------|
    | www.contoso.com | CNAME | contoso.azureedge.net |

    - Source (Origen): escriba el nombre de dominio personalizado (por ejemplo, www.contoso.com).

    - Type (Tipo): escriba *CNAME*.

    - Destination (Destino): escriba el nombre de host del punto de conexión de CDN. Debe tener el siguiente formato:_&lt;nombre de punto de conexión&gt;_. azureedge.net. Por ejemplo, contoso.azureedge.net.

4. Guarde los cambios.

5. Si previamente ha creado un registro CNAME de un subdominio cdnverify temporal, elimínelo. 

6. La primera vez que use este dominio personalizado en producción, siga los pasos de [Asociación del dominio personalizado a un punto de conexión de CDN](#associate-the-custom-domain-with-your-cdn-endpoint) y [Comprobación del dominio personalizado](#verify-the-custom-domain).

Por ejemplo, este es el procedimiento para el registrador de dominios GoDaddy:

1. Inicie sesión y seleccione el dominio personalizado que desea usar.

2. En la sección Domains (Dominio)º, seleccione **Manage all** (Administrar todos) y, después, seleccione **DNS** | **Manage Zones** (Administrar Zonas).

3. En **Domain Name** (Nombre de dominio), escriba el dominio personalizado y seleccione **Search** (Buscar).

4. En la página **DNS Management** (Administración de SNS), seleccione **Add** (Agregar) y, después, seleccione **CNAME** en la lista **Type** (Tipo).

5. Complete los campos de la entrada de CNAME:

    ![Entrada de CNAME](./media/cdn-map-content-to-custom-domain/cdn-cname-entry.png)

    - Tipo: deje *CNAME* seleccionado.

    - Host: escriba el subdominio del dominio personalizado que va a usar. Por ejemplo, www o cdn.

    - Points to (Apunta a): escriba el nombre de host del punto de conexión de CDN. Por ejemplo, contoso.azureedge.net. 

    - TTL: deje la opción *1 Hour* (1 hora) seleccionada.

6. Seleccione **Guardar**.
 
    La entrada de CNAME se agrega a la tabla de registros DNS.

    ![Tabla de registros DNS](./media/cdn-map-content-to-custom-domain/cdn-dns-table.png)

7. Si tiene un registro CNAME cdnverify, seleccione el icono de lápiz que encontrará al lado y, después, seleccione el icono de la papelera.

8. Seleccione **Delete** (Eliminar) para eliminar el registro CNAME.


## <a name="clean-up-resources"></a>Limpieza de recursos

En los pasos anteriores, ha agregado un dominio personalizado a un punto de conexión de CDN. Si desea dejar de asociar el punto de conexión a un dominio personalizado, siga estos pasos para quitar el dominio personalizado:
 
1. En el perfil de CDN, seleccione el punto de conexión con el dominio personalizado que desee quitar.

2. En la página **Punto de conexión**, en Dominios personalizados, haga clic con el botón derecho en el dominio personalizado que desea quitar y, después, seleccione **Eliminar** en el menú contextual.  

   El dominio personalizado se desasocia del punto de conexión.


## <a name="next-steps"></a>Pasos siguientes

En este tutorial aprendió lo siguiente:

> [!div class="checklist"]
> - Crear un registro DNS de CNAME
> - Asociar el dominio personalizado a un punto de conexión de CDN
> - Comprobar el dominio personalizado

En el siguiente tutorial aprenderá a configurar el protocolo HTTPS en un dominio personalizado de CDN de Azure.

> [!div class="nextstepaction"]
> [Tutorial: Configuración de HTTPS en un dominio personalizado de Azure CDN](cdn-custom-ssl.md)


