---
title: "Control del comportamiento del almacenamiento en caché de Azure Content Delivery Network con reglas de almacenamiento en caché| Microsoft Docs"
description: "Puede usar reglas de almacenamiento en caché de la red CDN para establecer o modificar el comportamiento de expiración de caché predeterminado globalmente y con condiciones, como una y ruta de acceso de dirección URL y extensiones de archivo."
services: cdn
documentationcenter: 
author: dksimpson
manager: 
editor: 
ms.assetid: 
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/23/2017
ms.author: v-deasim
ms.openlocfilehash: 2a94ba5cb9f026f66bc1f3b379f00b291a2299c9
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="control-azure-content-delivery-network-caching-behavior-with-caching-rules"></a>Control del comportamiento del almacenamiento en caché de Azure Content Delivery Network con reglas de almacenamiento en caché

> [!NOTE] 
> Las reglas de almacenamiento en caché solo están disponibles para **Azure CDN de Verizon Standard** y **Azure CDN de Akamai Standard**. Para **Azure CDN de Verizon Premium**, puede usar el [motor de reglas de Azure CDN](cdn-rules-engine.md) en el portal **Administrar** para una funcionalidad similar.
 
Azure Content Delivery Network ofrece dos formas de controlar cómo los archivos se almacenan en caché: 

- Reglas de almacenamiento en caché: en este artículo se describe cómo puede usar reglas de almacenamiento en caché de la red de entrega de contenido (CDN) para establecer o modificar el comportamiento de expiración de caché predeterminado, tanto globalmente como con condiciones personalizadas, como una ruta de dirección URL y extensión de archivo. Azure CDN proporciona dos tipos de reglas de almacenamiento en caché:
   - Reglas de almacenamiento en caché globales: puede establecer una regla de almacenamiento en caché global para cada punto de conexión en su perfil, lo que afecta a todas las solicitudes al punto de conexión. La regla de almacenamiento en caché global invalida todos los encabezados de directiva de caché HTTP, si está establecida.
   - Reglas de almacenamiento en caché personalizadas: puede establecer una o varias reglas de almacenamiento en caché para cada punto de conexión en el perfil. Las reglas de almacenamiento en caché personalizadas coinciden con rutas de acceso y extensiones de archivo específicas, se procesan en orden e invalidan la regla de almacenamiento en caché global, si está establecida. 

- Almacenamiento en caché de cadenas de consulta: puede ajustar cómo Azure CDN trata el almacenamiento en caché para las solicitudes con cadenas de consulta. Para información, consulte [Control del comportamiento del almacenamiento en caché de Azure Content Delivery Network con cadenas de consulta](cdn-query-string.md). Si el archivo no se puede almacenar en caché, la configuración del almacenamiento en caché de las cadenas de consulta no tiene ningún efecto, según los comportamientos predeterminados de la red CDN y las reglas de almacenamiento en caché.

Para información sobre el comportamiento de almacenamiento en caché predeterminado y los encabezados de directiva de almacenamiento en caché, consulte [Cómo funciona el almacenamiento en caché](cdn-how-caching-works.md).

## <a name="tutorial"></a>Tutorial

Cómo establecer reglas de almacenamiento en caché de la red CDN:

1. Abra Azure Portal, seleccione un perfil de red CDN y luego seleccione un punto de conexión.
2. En el panel izquierdo, en Configuración, haga clic en **Reglas de caché**.

   ![Botón Reglas de caché de CDN](./media/cdn-caching-rules/cdn-caching-rules-btn.png)

1. Cree una regla de almacenamiento en caché global de la manera siguiente:
   1. En **Reglas de almacenamiento en caché globales**, establezca **Comportamiento del almacenamiento en caché de cadenas de consulta** en **Ignorar cadenas de consulta**.
   2. Establezca **Comportamiento de almacenamiento en caché** en **Establecer si falta**.
       
   3. Para **Duración de expiración de caché**, escriba 10 en el campo **Días**.

       La regla de almacenamiento en caché global afecta a todas las solicitudes al punto de conexión. Esta regla respeta los encabezados de directiva de caché de origen, si existen (`Cache-Control` o `Expires`); en caso contrario, si no se especifican, establece la memoria caché en 10 días. 

     ![Reglas de almacenamiento en caché globales](./media/cdn-caching-rules/cdn-global-caching-rules.png)

4. Cree una regla de almacenamiento en caché personalizada de la manera siguiente:
    1. En **Reglas de almacenamiento en caché personalizadas**, establezca **Condición de coincidencia** en **Ruta de acceso** y **Valor de coincidencia** en `/images/*.jpg`.
    2. Establezca **Comportamiento de almacenamiento en caché** en **Invalidar** y escriba 30 en el campo **Días**.
       
       Esta regla de almacenamiento en caché personalizada establece una duración de caché de 30 días en cualquier archivo de imagen `.jpg` en la carpeta `/images` de su punto de conexión. Invalida todos los encabezados HTTP `Cache-Control` o `Expires` que envía el servidor de origen.

    ![Reglas de almacenamiento en caché personalizadas](./media/cdn-caching-rules/cdn-custom-caching-rules.png)

> [!NOTE] 
> Los archivos que se almacenan en caché antes de un cambio de regla mantienen su valor de duración de caché de origen. Para restablecer sus duraciones de caché, debe [purgar el archivo](cdn-purge-endpoint.md). Para puntos de conexión de **Azure CDN de Verizon**, las reglas de almacenamiento en caché pueden tardar hasta 90 minutos en surtir efecto.

## <a name="reference"></a>Referencia

### <a name="caching-behavior-settings"></a>Configuración del comportamiento del almacenamiento en caché
Para las reglas de almacenamiento en caché globales y personalizadas, puede especificar la siguiente configuración para **Comportamiento de almacenamiento en caché**:

- **Omitir caché**: no almacenar en caché y omitir los encabezados de directiva de caché proporcionados por el origen.
- **Invalidar**: omitir los encabezados de directiva de caché proporcionados por el origen; en su lugar, utilizar la duración de caché proporcionada.
- **Establecer si falta**: respetar los encabezados de directiva de caché proporcionados por el origen, si existen; en caso contrario, utilizar la duración de caché proporcionada.

### <a name="cache-expiration-duration"></a>Duración de expiración de caché
Para las reglas de almacenamiento en caché globales y personalizadas, puede especificar la duración de expiración de caché en días, horas, minutos y segundos:

- Para las configuraciones **Invalidar** y **Establecer si falta** correspondientes a **Comportamiento de almacenamiento en caché**, el intervalo de las duraciones de caché válido está comprendido entre 0 segundos y 366 días. Para un valor de 0 segundos, la red CDN almacena en caché el contenido, pero debe volver a validar cada solicitud con el servidor de origen.
- Para la configuración **Omitir caché**, la duración de caché se establece automáticamente en 0 segundos y no se puede cambiar.

### <a name="custom-caching-rules-match-conditions"></a>Condiciones de coincidencia de reglas de almacenamiento en caché personalizadas

Para las reglas de caché personalizadas, hay dos condiciones de coincidencia disponibles:
 
- **Ruta de acceso**: esta condición coincide con la ruta de acceso de la dirección URL, sin incluir el nombre de dominio, y admite el símbolo de comodín (\*). Por ejemplo, `/myfile.html`, `/my/folder/*` y `/my/images/*.jpg`. La longitud máxima es de 260 caracteres.

- **Extensión**: esta condición coincide con la extensión de archivo del archivo solicitado. Puede proporcionar una lista de extensiones de archivo separadas por comas para coincidir. Por ejemplo, `.jpg`, `.mp3` o `.png`. El número máximo de extensiones es 50 y el número máximo de caracteres por extensión es 16. 

### <a name="global-and-custom-rule-processing-order"></a>Orden de procesamiento de las reglas globales y personalizadas
Las reglas de almacenamiento en caché globales y personalizadas se procesan en el orden siguiente:

- Las reglas de almacenamiento en caché globales tienen prioridad sobre el comportamiento predeterminado de almacenamiento en caché de la red CDN (configuración del encabezado de la directiva de caché HTTP). 

- Las reglas de almacenamiento en caché personalizadas tienen prioridad sobre las reglas de almacenamiento en caché globales, donde se apliquen. Las reglas de almacenamiento en caché personalizadas se procesan en orden de arriba a abajo. Es decir, si una solicitud cumple ambas condiciones, las reglas situadas abajo en la lista tienen prioridad sobre las reglas situadas en la parte de arriba de la lista. Por lo tanto, debe colocar las reglas más específicas en la parte inferior de la lista.

**Ejemplo**:
- Regla de almacenamiento en caché global: 
   - Comportamiento de almacenamiento en caché: **Invalidar**
   - Duración de expiración de caché: un día

- Regla de almacenamiento en caché personalizada nº 1:
   - Condición de coincidencia: **Ruta de acceso**
   - Valor de coincidencia: `/home/*`
   - Comportamiento de almacenamiento en caché: **Invalidar**
   - Duración de expiración de caché: dos días

- Regla de almacenamiento en caché personalizada nº 2:
   - Condición de coincidencia: **Extensión**
   - Valor de coincidencia: `.html`
   - Comportamiento de almacenamiento en caché: **Establecer si falta**
   - Duración de expiración de caché: tres días

Cuando estas reglas están establecidas, una solicitud para `<endpoint>.azureedge.net/home/index.html` desencadena la regla de almacenamiento en caché nº 2, que se establece en **Establecer si falta** y 3 días. Por lo tanto, si el archivo `index.html` tiene encabezados HTTP `Cache-Control` o `Expires`, se respetan; en caso contrario, si estos encabezados no están establecidos, el archivo se almacena en caché durante 3 días.

