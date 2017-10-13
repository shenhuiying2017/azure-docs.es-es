---
title: "Restricción del contenido de la red CDN de Azure por país | Microsoft Docs"
description: "Aprenda cómo restringir el acceso a su contenido de la red CDN de Azure mediante la característica Filtrado geográfico."
services: cdn
documentationcenter: 
author: lichard
manager: akucer
editor: 
ms.assetid: 12c17cc5-28ee-4b0b-ba22-2266be2e786a
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: rli
ms.openlocfilehash: 30160088d9c770400f342e67527e1cf1cabc4f6b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="restrict-azure-cdn-content-by-country"></a>Restricción del contenido de la red CDN de Azure por país

## <a name="overview"></a>Información general
Cuando un usuario solicita su contenido, de forma predeterminada, el contenido se proporciona sin tener en cuenta el lugar desde el que el usuario realizó esta solicitud. En algunos casos, puede que desee restringir el acceso al contenido por país. En este tema se explica cómo configurar la característica **Filtrado geográfico** para permitir o bloquear el acceso por país.

> [!IMPORTANT]
> Los productos de Verizon y Akamai proporcionan la misma funcionalidad de filtrado geográfico, pero difieren ligeramente en los códigos de país que admiten. Vaya al Paso 3 para obtener un vínculo a las diferencias.


Para más información acerca de las consideraciones que se aplican a la configuración de este tipo de restricción, consulte la sección [Consideraciones](cdn-restrict-access-by-country.md#considerations) al final del tema.  

![Filtrado por país](./media/cdn-filtering/cdn-country-filtering-akamai.png)

## <a name="step-1-define-the-directory-path"></a>Paso 1: Definir la ruta de acceso al directorio
Seleccione el punto de conexión en el portal y busque la pestaña Filtrado geográfico en la navegación de la izquierda para buscar esta característica.

Al configurar un filtro de país, debe especificar la ruta de acceso relativa a la ubicación a la que se les permitirá o denegará el acceso a los usuarios. Puede aplicar el filtrado geográfico para todos los archivos mediante "/" o carpetas seleccionadas especificando las rutas de acceso de directorio "/pictures/". También puede aplicar el filtrado geográfica en un único archivo al especificar el archivo y omitiendo la barra diagonal "/pictures/city.png".

Filtro de ruta de acceso del directorio de ejemplo:

    /                                 
    /Photos/
    /Photos/Strasbourg/
      /Photos/Strasbourg/city.png

## <a name="step-2-define-the-action-block-or-allow"></a>Paso 2: Definir la acción: bloquear o permitir
**Bloquear** : a los usuarios de los países especificados se les denegará el acceso a los activos solicitados desde esa ruta recursiva. Si no se han configurado otras opciones de filtrado de país para esa ubicación, a continuación, se permitirá acceso a todos los demás usuarios.

**Permitir** : solo los usuarios de los países especificados podrán tener acceso a los recursos solicitados desde esa ruta recursiva.

## <a name="step-3-define-the-countries"></a>Paso 3: Definir los países
Seleccione los países que desee bloquear o permitir para la ruta de acceso. 

Por ejemplo, la regla de bloqueo /Fotos/Estrasburgo/ filtrará los archivos, incluidos:

    http://<endpoint>.azureedge.net/Photos/Strasbourg/1000.jpg
    http://<endpoint>.azureedge.net/Photos/Strasbourg/Cathedral/1000.jpg


### <a name="country-codes"></a>Códigos de país
La característica **Filtrado geográfico** usa códigos de país para definir los países desde los que se permitirá o bloqueará una solicitud para un directorio protegido. Encontrará los códigos de país en [Azure CDN Country Codes](https://msdn.microsoft.com/library/mt761717.aspx) (Códigos de países en la red CDN de Azure). 

## <a id="considerations"></a>Consideraciones
* Es posible que los cambios en la configuración del filtro de país tarden 90 minutos en el caso de Verizon, o un par de minutos en el de Akamai, en surtir efecto.
* Esta característica no admite caracteres comodín (por ejemplo, ‘*’).
* La configuración de filtrado geográfico asociada a la ruta de acceso relativa se aplicará de forma recursiva a esa ruta.
* Solo se puede aplicar una regla a la misma ruta de acceso relativa (no se pueden crear varios filtros de país que señalen a la misma ruta de acceso relativa). Sin embargo, una carpeta puede tener varios filtros de país. Esto se debe a la naturaleza recursiva de los filtros de país. En otras palabras, se puede asignar un filtro de país diferente a una subcarpeta de una carpeta configurada previamente.

