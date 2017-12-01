---
title: Mejora del rendimiento comprimiendo archivos en la red CDN de Azure | Microsoft Docs
description: "Este tema trata de cómo mejorar la velocidad de transferencia de archivos y aumentar el rendimiento de carga de páginas mediante la compresión de archivos en la red CDN de Azure."
services: cdn
documentationcenter: 
author: zhangmanling
manager: erikre
editor: 
ms.assetid: af1cddff-78d8-476b-a9d0-8c2164e4de5d
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: b4e27ec57543daed35811fff347f457b0dd2cd5c
ms.sourcegitcommit: 62eaa376437687de4ef2e325ac3d7e195d158f9f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/22/2017
---
# <a name="improve-performance-by-compressing-files-in-azure-cdn"></a>Mejora del rendimiento comprimiendo archivos en la red CDN de Azure
La compresión es un método sencillo y eficaz para mejorar la velocidad de transferencia de archivos y aumentar el rendimiento de carga de página al reducir el tamaño del archivo antes de que se envíe desde el servidor. Este método reduce los costos de ancho de banda y proporciona una mayor capacidad de respuesta para los usuarios.

La compresión se puede habilitar de dos maneras:

* Puede habilitar la compresión en el servidor de origen. En este caso, la red CDN pasa a través de los archivos comprimidos y los entrega a los clientes que los solicitan.
* Puede habilitar la compresión directamente en los servidores perimetrales de la red CDN. En este caso CDN comprime los archivos y los entrega a los usuarios finales, aunque el servidor de origen no los haya comprimido.

> [!IMPORTANT]
> Los cambios en la configuración de la red CDN pueden demorar un tiempo en propagarse por la red.  Para los perfiles de la <b>red CDN de Azure de Akamai</b> , la propagación normalmente se completa en menos de un minuto.  Para los perfiles de la <b>red CDN de Azure de Verizon</b>, normalmente los cambios se aplican en un período de 90 minutos.  Si está configurando la compresión por primera vez para su punto de conexión de la red CDN, considere esperar entre 1 y 2 horas antes de realizar la solución de problemas para garantizar que la configuración de la compresión se haya propagado a los POP.
> 
> 

## <a name="enabling-compression"></a>Habilitar la compresión
> [!NOTE]
> Los niveles estándar y premium de CDN proporcionan la misma funcionalidad de compresión, pero la interfaz de usuario es distinta.  Para obtener más información sobre las diferencias entre los niveles estándar y premium de CDN, consulte [Información general sobre la red CDN de Azure](cdn-overview.md).
> 
> 

### <a name="standard-tier"></a>Nivel Standard
> [!NOTE]
> Esta sección es aplicable a perfiles de la **red CDN estándar de Azure de Verizon** y de la **red CDN estándar de Azure de Akamai**.
> 
> 

1. En la página de perfil de la red CDN, haga clic en el punto de conexión de la red CDN que quiere administrar.
   
    ![Puntos de conexión de perfil de la red CDN](./media/cdn-file-compression/cdn-endpoints.png)
   
    Se abre la página del punto de conexión de la red CDN.
2. Elija el botón **Configurar** .
   
    ![Botón de administración de perfil de la red CDN](./media/cdn-file-compression/cdn-config-btn.png)
   
    Se abre la página de configuración de la red CDN.
3. Active la **Compresión**.
   
    ![Opciones de compresión de red CDN](./media/cdn-file-compression/cdn-compress-standard.png)
4. Use los tipos predeterminados o modifique la lista quitando o agregando los tipos de archivo.
   
   > [!TIP]
   > Aunque es posible, no se recomienda aplicar compresión a formatos comprimidos. Por ejemplo, ZIP, MP3, MP4 o JPG.
   > 
 
5. Tras efectuar los cambios, haga clic en el botón **Guardar** .

### <a name="premium-tier"></a>Nivel Premium
> [!NOTE]
> Esta sección es aplicable a perfiles de la **red CDN premium de Azure de Verizon** .
> 
> 

1. En la página de perfil de la red CDN, haga clic en el botón **Administrar**.
   
    ![Botón de administración de perfil de la red CDN](./media/cdn-file-compression/cdn-manage-btn.png)
   
    Se abre el portal de administración de CDN.
2. Desplace el mouse sobre la pestaña **HTTP grande** y luego mantenga el mouse sobre el control flotante **Configuración de caché**.  Haga clic en **Compresión**.

    ![Selección de compresión de archivos](./media/cdn-file-compression/cdn-compress-select.png)
   
    Se muestran las opciones de compresión.
   
    ![Opciones de compresión de archivos](./media/cdn-file-compression/cdn-compress-files.png)
3. Habilite la compresión con un clic en el botón de selección **Compresión habilitada** .  Escriba los tipos de MIME que desea comprimir como una lista separada por comas (sin espacios) en el cuadro de texto **Tipos de archivo** .
   
   > [!TIP]
   > Aunque es posible, no se recomienda aplicar compresión a formatos comprimidos. Por ejemplo, ZIP, MP3, MP4 o JPG.
   > 
4. Después de realizar los cambios, haga clic en el botón **Actualizar** .

## <a name="compression-rules"></a>Reglas de compresión
Estas tablas describen el comportamiento de la compresión CDN de Azure para cada escenario.

> [!IMPORTANT]
> En el caso de los perfiles de la **red CDN de Azure de Verizon** (Estándar y Premium), solo se comprimen determinados archivos válidos.  Para ser elegible para la compresión, un archivo debe cumplir con los siguientes requisitos:
> 
> * Debe tener más de 128 bytes.
> * Debe tener menos de 1 MB.
> 
> Estos perfiles admiten codificación **gzip** (GNU zip), **deflate**, **bzip2** o **br** (Brotli). En el caso de la codificación Brotli, la compresión debe realizarse en el origen. El explorador o el cliente debe enviar la solicitud de codificación Brotli y el recurso comprimido debe haberse comprimido primero en el origen. 

> [!IMPORTANT]
> En el caso de los perfiles de la **red CDN de Azure de Akamai**, todos los archivos son válidos para la compresión. Sin embargo, un archivo debe tener un tipo MIME [configurado para compresión](#enabling-compression).
> 
>Estos perfiles solo admiten la codificación **gzip**. Cuando un punto de conexión de perfil solicita los perfiles codificados **gzip**, siempre se solicitan al origen, independientemente de la solicitud del cliente. 

### <a name="compression-disabled-or-file-is-ineligible-for-compression"></a>La compresión está deshabilitada o el archivo no es elegible para la compresión
| Formato solicitado por el cliente (mediante el encabezado Accept-Encoding) | Formato de archivo almacenado en caché | Respuesta de la red CDN al cliente | Notas |
| --- | --- | --- | --- |
| Comprimidos |Comprimidos |Comprimidos | |
| Comprimidos |Sin comprimir |Sin comprimir | |
| Comprimidos |No almacenado en caché |Comprimidos o sin comprimir |Depende de la respuesta de origen |
| Sin comprimir |Comprimidos |Sin comprimir | |
| Sin comprimir |Sin comprimir |Sin comprimir | |
| Sin comprimir |No almacenado en caché |Sin comprimir | |

### <a name="compression-enabled-and-file-is-eligible-for-compression"></a>La compresión está habilitada y el archivo es elegible para la compresión
| Formato solicitado por el cliente (mediante el encabezado Accept-Encoding) | Formato de archivo almacenado en caché | Respuesta de la red CDN al cliente | Notas |
| --- | --- | --- | --- |
| Comprimidos |Comprimidos |Comprimidos |La red CDN transcodifica entre los formatos admitidos |
| Comprimidos |Sin comprimir |Comprimidos |La red CDN realiza la compresión |
| Comprimidos |No almacenado en caché |Comprimidos |La red CDN realiza la compresión si el origen se devuelve sin comprimir.  **CDN de Azure de Verizon** pasa el archivo descomprimido en la primera solicitud y luego lo comprime y lo almacena en caché para solicitudes posteriores.  Los archivos con el encabezado `Cache-Control: no-cache` nunca se comprimen. |
| Sin comprimir |Comprimidos |Sin comprimir |La red CDN realiza la descompresión |
| Sin comprimir |Sin comprimir |Sin comprimir | |
| Sin comprimir |No almacenado en caché |Sin comprimir | |

## <a name="media-services-cdn-compression"></a>Compresión de red CDN de servicios multimedia
Para los puntos de conexión de streaming habilitados para Media Services CDN, la compresión está habilitada de forma predeterminada para los siguientes tipos de contenido: 
- application/vnd.ms-sstr+xml 
- application/dash+xml
- application/vnd.apple.mpegurl
- application/f4m+xml. 

No se puede habilitar o deshabilitar la compresión de los tipos mencionados mediante Azure Portal.  

## <a name="see-also"></a>Otras referencias
* [Solución de problemas de compresión de archivos de red CDN](cdn-troubleshoot-compression.md)    

