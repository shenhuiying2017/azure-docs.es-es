---
title: "Restricción del acceso a contenido de la red CDN de Azure por país | Microsoft Docs"
description: "Aprenda cómo restringir el acceso a su contenido de la red CDN de Azure mediante la característica Filtrado geográfico."
services: cdn
documentationcenter: 
author: lichard
manager: akucer
editor: 
ms.assetid: 20943ac8-9dce-4cf2-85ed-e8362750aa63
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/13/2016
ms.author: rli
translationtype: Human Translation
ms.sourcegitcommit: 2ee72c31072fb5201e057f4c3567d33ce53e4fbc
ms.openlocfilehash: 1a1cf8cea1b321bb4e758e9ad06e42da59d3bcb4


---
# <a name="restrict-access-to-your-content-by-country---verizon"></a>Restricción del acceso a contenidos por país - Verizon
> [!div class="op_single_selector"]
> * [Verizon](cdn-restrict-access-by-country.md)
> * [Akamai Standard](cdn-restrict-access-by-country-akamai.md)
> 
> 

## <a name="overview"></a>Información general
Cuando un usuario solicita su contenido, de forma predeterminada, el contenido se proporciona sin tener en cuenta el lugar desde el que el usuario realizó esta solicitud. En algunos casos, puede que desee restringir el acceso al contenido por país. En este tema se explica cómo configurar la característica **Filtrado geográfico** para permitir o bloquear el acceso por país.

> [!IMPORTANT]
> Los productos de Verizon y Akamai proporcionan la misma funcionalidad de filtrado geográfico, pero difieren en su interfaz de usuario. En este documento se describe la interfaz de **Azure CDN Standard de Verizon** y **Azure CDN Premium de Verizon**. Para el filtrado geográfico con **Azure CDN Estándar de Akamai**, consulte [Restricción del acceso a contenidos por país - Akamai](cdn-restrict-access-by-country-akamai.md).
> 
> 

Para más información acerca de las consideraciones que se aplican a la configuración de este tipo de restricción, consulte la sección [Consideraciones](cdn-restrict-access-by-country.md#considerations) al final del tema.  

> [!NOTE]
> Una vez efectuada la configuración, esta se aplicará a todos los puntos de conexión de la **red CDN de Azure de Verizon** de este perfil de red CDN de Azure.
> 
> 

![Filtrado por país](./media/cdn-filtering/cdn-country-filtering.png)

## <a name="step-1-define-the-directory-path"></a>Paso 1: Definir la ruta de acceso al directorio
Al configurar un filtro de país, debe especificar la ruta de acceso relativa a la ubicación a la que se les permitirá o denegará el acceso a los usuarios. Puede aplicar el filtrado por países para todos los archivos mediante "/" o carpetas seleccionadas especificando las rutas de acceso de directorio.

Filtro de ruta de acceso del directorio de ejemplo:

    /                                 
    /Photos/
    /Photos/Strasbourg

## <a name="step-2-define-the-action-block-or-allow"></a>Paso 2: Definir la acción: bloquear o permitir
**Bloquear** : a los usuarios de los países especificados se les denegará el acceso a los activos solicitados desde esa ruta recursiva. Si no se han configurado otras opciones de filtrado de país para esa ubicación, a continuación, se permitirá acceso a todos los demás usuarios.

**Permitir** : solo los usuarios de los países especificados podrán tener acceso a los recursos solicitados desde esa ruta recursiva.

## <a name="step-3-define-the-countries"></a>Paso 3: Definir los países
Seleccione los países que desee bloquear o permitir para la ruta de acceso. Para más información, consulte [Azure CDN from Verizon Country Codes](https://msdn.microsoft.com/library/mt761717.aspx)(Red CDN de Azure desde códigos de país de Verizon).

Por ejemplo, la regla de bloqueo /Fotos/Estrasburgo/ filtrará los archivos, incluidos:

    http://<endpoint>.azureedge.net/Photos/Strasbourg/1000.jpg
    http://<endpoint>.azureedge.net/Photos/Strasbourg/Cathedral/1000.jpg


## <a name="country-codes"></a>Códigos de país
La característica **Filtrado geográfico** usa códigos de país para definir los países desde los que se permitirá o bloqueará una solicitud para un directorio protegido. Encontrará los códigos de país en [Azure CDN from Verizon Country Codes](https://msdn.microsoft.com/library/mt761717.aspx)(Red CDN de Azure desde códigos de país de Verizon). Si especifica "EU" (Europa) o "AP" (Asia Pacífico), se bloqueará o permitirá un subconjunto de direcciones IP que tengan su origen en cualquier país de esas regiones.

## <a name="a-idconsiderationsaconsiderations"></a><a id="considerations"></a>Consideraciones
* Es posible que los cambios en la configuración del filtro de país tarden hasta 90 minutos en surtir efecto.
* Esta característica no admite caracteres comodín (por ejemplo, ‘*’).
* La configuración de filtrado de país asociada a la ruta de acceso relativa se aplicará de forma recursiva a esa ruta.
* Solo se puede aplicar una regla a la misma ruta de acceso relativa (no se pueden crear varios filtros de país que señalen a la misma ruta de acceso relativa). Sin embargo, una carpeta puede tener varios filtros de país. Esto se debe a la naturaleza recursiva de los filtros de país. En otras palabras, se puede asignar un filtro de país diferente a una subcarpeta de una carpeta configurada previamente.




<!--HONumber=Nov16_HO3-->


