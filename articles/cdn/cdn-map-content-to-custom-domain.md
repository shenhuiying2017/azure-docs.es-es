---
title: "Adición de un dominio personalizado al punto de conexión de CDN | Microsoft Docs"
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
ms.date: 10/09/2017
ms.author: mazha
ms.openlocfilehash: ec53b91b8aba4e38a8f7cb4b010d6be2a62150d5
ms.sourcegitcommit: 42ee5ea09d9684ed7a71e7974ceb141d525361c9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/09/2017
---
# <a name="add-a-custom-domain-to-your-cdn-endpoint"></a>Adición de un dominio personalizado al punto de conexión de CDN
Después de crear un perfil, normalmente también creará uno o más puntos de conexión de CDN (un subdominio de azureedge.net) para entregar el contenido mediante HTTP y HTTPS. De forma predeterminada, este punto de conexión se incluye en todas las direcciones URL, por ejemplo, `http(s)://contoso.azureedge.net/photo.png`). Para su comodidad, Azure CDN proporciona la opción de asociar un dominio personalizado (por ejemplo, `www.contoso.com`) al punto de conexión. Con esta opción, usará un dominio personalizado para entregar el contenido, en lugar del punto de conexión. Esta opción es útil si, por ejemplo, desea que su propio nombre de dominio sea visibles para sus clientes con fines de personalización de marca.

Si no dispone ya de un dominio personalizado, primero debe adquirir uno con un proveedor de dominios. Después de haber obtenido un dominio personalizado, siga estos pasos:
1. [Acceda a los registros DNS de su proveedor de dominios](#step-1-access-dns-records-by-using-your-domain-provider).
2. [Cree los registros DNS de CNAME](#step-2-create-the-cname-dns-records).
    - Opción 1: Asignación directa del dominio personalizado al punto de conexión de CDN
    - Opción 2: Asignación del dominio personalizado al punto de conexión de CDN mediante el subdominio **cdnverify** 
3. [Habilite la asignación de registros CNAME en Azure](#step-3-enable-the-cname-record-mapping-in-azure).
4. [Compruebe que el subdominio personalizado hace referencia al punto de conexión de CDN](#step-4-verify-that-the-custom-subdomain-references-your-cdn-endpoint).
5. [(Paso dependiente) Asigne el dominio personalizado permanente al punto de conexión de CDN](#step-5-dependent-step-map-the-permanent-custom-domain-to-the-cdn-endpoint).

## <a name="step-1-access-dns-records-by-using-your-domain-provider"></a>Paso 1: Acceso a los registros DNS mediante el proveedor de dominios

Si usa Azure para hospedar sus [dominios de DNS](https://docs.microsoft.com/azure/dns/dns-overview), debe delegar el DNS del proveedor de dominios en una instancia de Azure DNS. Para más información, vea [Delegación de un dominio en DNS de Azure](https://docs.microsoft.com/azure/dns/dns-delegate-domain-azure-dns).

De lo contrario, si va a usar el proveedor de dominios para gestionar el dominio DNS, inicie sesión en el sitio web de su proveedor de dominios. Para encontrar la página de administración de los registros DNS, consulte la documentación del proveedor o busque las áreas del sitio web con la etiqueta **Nombre de dominio**, **DNS** o **Name Server Management** (Administración del servidor de nombres). Con frecuencia, la forma de encontrar la página de registros DNS es examinar la información de la cuenta y buscar un vínculo, como **Mis dominios**. Algunos proveedores tienen diferentes vínculos para agregar diferentes tipos de registros.

> [!NOTE]
> En el caso de ciertos proveedores, como GoDaddy, los cambios en los registros DNS no entran en vigor hasta que se selecciona un vínculo **Guardar cambios** independiente. 


## <a name="step-2-create-the-cname-dns-records"></a>Paso 2: Creación de los registros DNS de CNAME

Para poder usar un dominio personalizado con un punto de conexión de Azure CDN, primero debe crear un registro de nombre canónico (CNAME) con su proveedor de dominios. Un registro CNAME es un tipo de registro en el Sistema de nombres de dominio (DNS) que asigna un dominio de origen a un dominio de destino mediante la especificación de un nombre de dominio de alias para el nombre de dominio "canónico" o verdadero. En Azure CDN, el dominio de origen es su dominio (y subdominio) personalizado y el dominio de destino es el punto de conexión de CDN. Azure CDN comprueba el registro DNS de CNAME cuando se agrega el dominio personalizado al punto de conexión desde el portal o la API. 

Un registro CNAME asigna un dominio y un subdominio específicos, como `www.contoso.com` o `cdn.contoso.com`; no es posible asignar un registro CNAME a un dominio raíz, como `contoso.com`. Un subdominio solamente se puede asociar con un extremo de red CDN. Un registro CNAME enruta todo el tráfico dirigido al subdominio al punto de conexión especificado. Por ejemplo, si asocia `www.contoso.com` al punto de conexión de CDN, no puede asociarlo a otro punto de conexión de Azure, como un punto de conexión de una cuenta de almacenamiento o un punto de conexión de un servicio en la nube. Sin embargo, puede utilizar sus dominios diferentes desde el mismo dominio para distintos puntos de conexión de servicio. También puede asignar diferentes subdominios al mismo extremo de red CDN.

Para asignar su dominio personalizado a un punto de conexión de CDN, use una de las siguientes opciones:

- Opción 1: Asignación directa del dominio personalizado al punto de conexión de CDN. Si no hay ningún tráfico de producción ejecutándose en el dominio personalizado, puede asignar un dominio personalizado a un punto de conexión de CDN directamente. El proceso de asignación de un dominio personalizado a un punto de conexión de CDN puede provocar un breve período de inactividad en el dominio mientras lo registra en Azure Portal. La entrada de asignación de CNAME debe tener este formato: 
 
  | NOMBRE             | TIPO  | VALOR                  |
  |------------------|-------|------------------------|
  | `www.contoso.com` | `CNAME` | `contoso.azureedge.net` |


- Opción 2: Asignación del dominio personalizado al punto de conexión de CDN mediante el subdominio **cdnverify**. Si en el dominio personalizado se ejecuta tráfico de producción que no se puede interrumpir, puede crear una asignación de CNAME temporal al punto de conexión. Con esta opción, usará el subdominio **cdnverify** de Azure para proporcionar un paso de registro intermedio para que los usuarios puedan acceder al dominio sin interrupción mientras se realiza la asignación de DNS.

   1. Cree un nuevo registro CNAME y proporcione un alias de subdominio que incluya el subdominio **cdnverify**. Por ejemplo, `cdnverify.www` o `cdnverify.cdn`. 
   2. Proporcione el nombre de host, que es el punto de conexión de CDN, en el siguiente formato: `cdnverify.<EndpointName>.azureedge.net`. La entrada de asignación de CNAME debe tener este formato: 

   | NOMBRE                       | TIPO  | VALOR                            |
   |----------------------------|-------|----------------------------------|
   | `cdnverify.www.contoso.com` | `CNAME` | `cdnverify.contoso.azureedge.net` | 


## <a name="step-3-enable-the-cname-record-mapping-in-azure"></a>Paso 3. Habilitación de la asignación de registros CNAME en Azure

Después de haber registrado su dominio personalizado mediante uno de los procedimientos anteriores, puede habilitar la característica de dominio personalizado en Azure CDN. 

1. Inicie sesión en [Azure Portal](https://portal.azure.com/) y examine el perfil de CDN con el punto de conexión al que quiere asignar un dominio personalizado.  
2. En la hoja **Perfil de CDN**, seleccione el punto de conexión de CDN al que quiere asociar el subdominio.
3. En la parte superior izquierda de la hoja del punto de conexión, haga clic en **Dominio personalizado**. 

   ![Botón Dominio personalizado](./media/cdn-map-content-to-custom-domain/cdn-custom-domain-button.png)

4. En el cuadro de texto **Nombre de host personalizado**, escriba su dominio personalizado, incluido el subdominio. Por ejemplo, `www.contoso.com` o `cdn.contoso.com`.

   ![Cuadro de diálogo Agregar un dominio personalizado](./media/cdn-map-content-to-custom-domain/cdn-add-custom-domain-dialog.png)

5. Haga clic en **Agregar**.

   Azure comprobará que el registro CNAME existe para el nombre de dominio que ha escrito. Si el registro CNAME es correcto, el dominio personalizado se valida. La propagación del registro CNAME a los servidores de nombres puede llevar cierto tiempo. Si el dominio no se valida inmediatamente, compruebe que el registro CNAME sea correcto, espere unos minutos e inténtelo de nuevo. En el caso de los puntos de conexión de **Azure CDN de Verizon** (estándar y premium), la configuración del dominio personalizado puede tardar hasta 90 minutos en propagarse a todos los nodos perimetrales de la red CDN.  


## <a name="step-4-verify-that-the-custom-subdomain-references-your-cdn-endpoint"></a>Paso 4. Comprobación de que el subdominio personalizado hace referencia al punto de conexión de CDN

Después de haber completado el registro del dominio personalizado, compruebe que el subdominio personalizado haga referencia el punto de conexión de CDN.
 
1. Asegúrese de que tiene contenido público almacenado en caché en el punto de conexión. Por ejemplo, si su punto de conexión de red CDN está asociado a una cuenta de almacenamiento, la red CDN almacena en caché el contenido en contenedores de blob públicos. Para probar el dominio personalizado, asegúrese de que su contenedor está establecido para permitir acceso público y que contiene al menos un blob.

2. En el explorador, navegue a la dirección del blob usando el dominio personalizado. Por ejemplo, si el dominio personalizado es `cdn.contoso.com`, la dirección URL al blob almacenado en caché debe ser similar a la siguiente: `http://cdn.contoso.com/mypubliccontainer/acachedblob.jpg`.


## <a name="step-5-dependent-step-map-the-permanent-custom-domain-to-the-cdn-endpoint"></a>Paso 5 (Paso dependiente): Asignación del dominio personalizado permanente al punto de conexión de CDN

Este paso depende del paso 2, opción 2: Asignación del dominio personalizado al punto de conexión de CDN mediante el subdominio **cdnverify**. Si está usando el subdominio **cdnverify** temporal y ha comprobado que funciona, puede asignar entonces su dominio personalizado permanente al punto de conexión de CDN.

1. En el sitio de web del proveedor de dominios, cree un registro DNS de CNAME para asignar su dominio personalizado permanente al punto de conexión de CDN. La entrada de asignación de CNAME debe tener este formato: 
 
   | NOMBRE             | TIPO  | VALOR                  |
   |------------------|-------|------------------------|
   | `www.contoso.com` | `CNAME` | `contoso.azureedge.net` |
2. Elimine el registro CNAME con el subdominio **cdnverify** que creó anteriormente.

## <a name="see-also"></a>Otras referencias
[Habilitar Content Delivery Network (CDN) para Azure](cdn-create-new-endpoint.md)  
[Delegación del dominio en Azure DNS](../dns/dns-domain-delegation.md)
