---
title: "Asignación de contenido de la red CDN de Azure a un dominio personalizado | Microsoft Docs"
description: Aprenda a asignar el contenido de la red CDN de Azure a un dominio personalizado.
services: cdn
documentationcenter: 
author: zhangmanling
manager: erikre
editor: 
ms.assetid: 289f8d9e-8839-4e21-b248-bef320f9dbfc
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: cd6db44f7776859d1e6a893543cf0666182ca41a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="map-azure-cdn-content-to-a-custom-domain"></a>Asignación del contenido de la red CDN de Azure a un dominio personalizado
Puede asignar un dominio personalizado a un punto de conexión de CDN para utilizar su propio nombre de dominio en direcciones URL para el contenido almacenado en caché, en lugar de utilizar un subdominio de azureedge.net.

Hay dos formas de asignar su dominio personalizado a un punto de conexión de CDN:

1. [Crear un registro CNAME en su registrador de dominios y asignar su dominio y subdominio personalizados al extremo de red CDN](#register-a-custom-domain-for-an-azure-cdn-endpoint).
   
    Un registro CNAME es una característica DNS que asigna un dominio de origen, como `www.contosocdn.com` o `cdn.contoso.com`, a un dominio de destino. En este caso, el dominio de origen es su dominio y subdominio personalizados (un subdominio, como **www** o **cdn**, es siempre necesario). El dominio de destino es el punto de conexión de red CDN.  
   
    Sin embargo, el proceso de asignación de un dominio personalizado a un punto de conexión de CDN puede provocar un período breve de inactividad en el dominio mientras registra el dominio en Azure Portal.
2. [Agregar un paso de registro intermedio con **cdnverify**](#register-a-custom-domain-for-an-azure-cdn-endpoint-using-the-intermediary-cdnverify-subdomain)
   
    Si el dominio personalizado ya es compatible con una aplicación con un contrato de nivel de servicio (SLA) que requiere que no exista tiempo de inactividad, puede usar el subdominio **cdnverify** de Azure para proporcionar un paso de registro intermedio para que los usuarios puedan obtener acceso al dominio mientras se realiza la asignación de DNS.  

Después de registrar el dominio personalizado mediante uno de los procedimientos anteriores, deberá [comprobar que el subdominio personalizado hace referencia el punto de conexión de CDN](#verify-that-the-custom-subdomain-references-your-cdn-endpoint).

> [!NOTE]
> Debe crear un registro CNAME con su registrador de dominio y asignar su dominio al extremo de red CDN. Los registros CNAME asignan subdominios específicos, como `www.contoso.com` o `cdn.contoso.com`. No es posible asignar un registro CNAME a un dominio raíz, como `contoso.com`.
> 
> Un subdominio solamente se puede asociar con un extremo de red CDN. El registro CNAME que crea enrutará todo el tráfico dirigido al subdominio al punto de conexión especificado.  Por ejemplo, si asocia `www.contoso.com` con su punto de conexión de CDN, no puede asociarlo con otros puntos de conexión de Azure, como un punto de conexión de cuenta de almacenamiento o un punto de conexión de servicio en la nube. Sin embargo, puede utilizar sus dominios diferentes desde el mismo dominio para distintos puntos de conexión de servicio. También puede asignar diferentes subdominios al mismo extremo de red CDN.
> 
> Tenga en cuenta que, en el caso de los puntos de conexión de la **red CDN de Azure de Verizon** (estándar y premium), los cambios realizados en el dominio personalizado tardan hasta **90 minutos** en propagarse a los nodos perimetrales de CDN.
> 
> 

## <a name="register-a-custom-domain-for-an-azure-cdn-endpoint"></a>Registrar un dominio personalizado para un punto de conexión de red CDN de Azure
1. Inicie sesión en el [Portal de Azure](https://portal.azure.com/).
2. Haga clic en **Examinar**, en **Perfiles de CDN** y, luego, en el perfil de red CDN con el punto de conexión que quieras asignar a un dominio personalizado.  
3. En la hoja **Perfil de CDN** , haga clic en el punto de conexión de CDN con el que quiera asociar el subdominio.
4. En la parte superior de la hoja del punto de conexión, haga clic en el botón **Agregar dominio personalizado** .  En la hoja **Agregar un dominio personalizado** , verá el nombre de host del punto de conexión, derivado del punto de conexión de red CDN, para usar en la creación de un nuevo registro CNAME. El formato de la dirección del nombre de host aparecerá como **&lt;NombrePuntoConexión>.azureedge.net**.  Puede copiar este nombre de host para usar en la creación del registro CNAME.  
5. Navegue al sitio web del registrador y localice la sección para crear registros DNS. Podría encontrarlo en una sección como **Nombre de dominio**, **DNS** o **Administración del servidor de nombres**.
6. Busque la sección para la administración de CNAME. Tiene que dirigirse a la página de configuración avanzada y buscar las palabras CNAME, Alias o Subdomains.
7. Cree un nuevo registro CNAME que asigne su subdominio elegido (por ejemplo, **www** o **cdn**) al nombre de host proporcionado en la hoja **Agregar un dominio personalizado**. 
8. Vuelva a la hoja **Agregar un dominio personalizado** y escriba su dominio personalizado, incluido el subdominio, en el cuadro personalizado. Por ejemplo, escriba el nombre del dominio con el formato `www.contoso.com` o `cdn.contoso.com`.   
   
   Azure comprobará que el registro CNAME existe para el nombre de dominio que ha escrito. Si el registro CNAME es correcto, el dominio personalizado se valida.  No obstante, en el caso de los puntos de conexión de la **red CDN de Azure de Verizon** (estándar y premium), la configuración del dominio personalizada puede tardar hasta 90 minutos en propagarse a todos los nodos perimetrales de CDN.  
   
   Tenga en cuenta que en algunos casos la propagación del registro CNAME puede tardar cierto tiempo en propagarse a los servidores de nombres. Si el dominio no se valida inmediatamente y cree que el registro CNAME es correcto, espere unos minutos e inténtelo de nuevo.

## <a name="register-a-custom-domain-for-an-azure-cdn-endpoint-using-the-intermediary-cdnverify-subdomain"></a>Registrar un dominio personalizado para un punto de conexión de red CDN de Azure usando el subdominio cdnverify intermediario
1. Inicie sesión en el [Portal de Azure](https://portal.azure.com/).
2. Haga clic en **Examinar**, en **Perfiles de CDN** y, luego, en el perfil de red CDN con el punto de conexión que quieras asignar a un dominio personalizado.  
3. En la hoja **Perfil de CDN** , haga clic en el punto de conexión de CDN con el que quiera asociar el subdominio.
4. En la parte superior de la hoja del punto de conexión, haga clic en el botón **Agregar dominio personalizado** .  En la hoja **Agregar un dominio personalizado** , verá el nombre de host del punto de conexión, derivado del punto de conexión de red CDN, para usar en la creación de un nuevo registro CNAME. El formato de la dirección del nombre de host aparecerá como **&lt;NombrePuntoConexión>.azureedge.net**.  Puede copiar este nombre de host para usar en la creación del registro CNAME.
5. Navegue al sitio web del registrador y localice la sección para crear registros DNS. Podría encontrarlo en una sección como **Nombre de dominio**, **DNS** o **Administración del servidor de nombres**.
6. Busque la sección para la administración de CNAME. Tiene que dirigirse a la página de configuración avanzada y buscar las palabras **CNAME**, **Alias** o **Subdomains**.
7. Cree un nuevo registro CNAME y proporcione un alias de subdominio que incluya el subdominio **cdnverify** . Por ejemplo, el subdominio que especifique estará en formato **cdnverify.www** o **cdnverify.cdn**. Luego, especifique el nombre de host, que es el punto de conexión de CDN, con el formato **cdnverify.&lt;NombrePuntoConexión>.azureedge.net**. La asignación de DNS debe tener el siguiente aspecto:`cdnverify.www.consoto.com   CNAME   cdnverify.consoto.azureedge.net`  
8. Vuelva a la hoja **Agregar un dominio personalizado** y escriba su dominio personalizado, incluido el subdominio, en el cuadro personalizado. Por ejemplo, escriba el nombre del dominio con el formato `www.contoso.com` o `cdn.contoso.com`. Tenga en cuenta que en este paso, no es necesario que el subdominio vaya precedido de **cdnverify**.  
   
    Azure comprobará que el registro CNAME existe para el nombre de dominio cdnverify que ha escrito.
9. En este momento, Azure ha comprobado el dominio personalizado, pero no se ha realizado el enrutamiento del tráfico al punto de conexión de red CDN. Después de esperar lo necesario para permitir que la configuración del dominio personalizado se propague a los nodos perimetrales de CDN (90 minutos para la **red CDN de Azure de Verizon** y entre 1 y 2 minutos para la **red CDN de Azure de Akamai**), vuelva al sitio web del registrador de DNS y cree otro registro CNAME que asigne el subdominio al punto de conexión de CDN. Por ejemplo, especifique el subdominio como **www** o **cdn** y el nombre de host como **&lt;NombrePuntoConexión>.azureedge.net**. Con este paso se completa el registro del dominio personalizado.
10. Finalmente, puede eliminar el registro CNAME que creó con **cdnverify**, ya que solo era necesario como paso intermedio.  

## <a name="verify-that-the-custom-subdomain-references-your-cdn-endpoint"></a>comprobar que el subdominio personalizado hace referencia el punto de conexión de CDN
* Después de haber completado el registro del dominio personalizado, puede tener acceso al contenido almacenado en caché en su punto de conexión de red CDN mediante el dominio personalizado.
  En primer lugar, asegúrese de que tiene contenido público almacenado en caché en el punto de conexión. Por ejemplo, si su punto de conexión de red CDN está asociado a una cuenta de almacenamiento, la red CDN almacena en caché el contenido en contenedores de blob públicos. Para probar el dominio personalizado, asegúrese de que su contenedor está establecido para permitir acceso público y que contiene al menos un blob.
* En el explorador, navegue a la dirección del blob usando el dominio personalizado. Por ejemplo, si el dominio personalizado es `cdn.contoso.com`, la dirección URL a un blob almacenado en caché será similar a la siguiente: http://cdn.contoso.com/mypubliccontainer/acachedblob.jpg.

## <a name="see-also"></a>Otras referencias
[Habilitar Content Delivery Network (CDN) para Azure](cdn-create-new-endpoint.md)  

