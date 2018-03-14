---
title: "Invalidación del comportamiento HTTP mediante el motor de reglas de la red CDN de Azure | Microsoft Docs"
description: "El motor de reglas permite personalizar cómo la red CDN de Azure controla las solicitudes HTTP, como el bloqueo de la entrega de determinados tipos de contenido, la definición de una directiva de almacenamiento en caché y la modificación de encabezados HTTP."
services: cdn
documentationcenter: 
author: dksimpson
manager: akucer
editor: 
ms.assetid: 625a912b-91f2-485d-8991-128cc194ee71
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/21/2018
ms.author: mazha
ms.openlocfilehash: fe3df703f7eb244a52756c4d015e9ea598224ce1
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/02/2018
---
# <a name="override-http-behavior-using-the-azure-cdn-rules-engine"></a>Invalidación del comportamiento HTTP mediante el motor de reglas de la red CDN de Azure
[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="overview"></a>Información general
El motor de reglas de Azure CDN le permite personalizar cómo se administran las solicitudes HTTP. Por ejemplo, puede bloquear la entrega de determinados tipos de contenido, definir una directiva de almacenamiento en caché o modificar un encabezado HTTP. En este tutorial se muestra cómo crear una regla que cambia el comportamiento de almacenamiento en caché de los recursos de CDN. Para más información sobre la sintaxis del motor de reglas, consulte la [referencia del motor de reglas de Azure CDN](cdn-rules-engine-reference.md).

## <a name="access"></a>Access
Para acceder al motor de reglas, primero debe seleccionar **Manage** (Administrar) en la parte superior de la página **CDN profile** (Perfil de CDN) para ir a la página de administración de Azure CDN. En función de si el punto de conexión está optimizado para la aceleración de sitios dinámicos (DSA), accederá al motor de reglas con el conjunto de reglas adecuado para su tipo de punto de conexión:

- Puntos de conexión optimizados para la entrega web general u otra optimización no DSA: 
    
    Seleccione la pestaña **HTTP Large** (HTTP grande) y, a continuación, seleccione **Rules Engine** (Motor de reglas).

    ![Motor de reglas para HTTP](./media/cdn-rules-engine/cdn-http-rules-engine.png)

- Puntos de conexión optimizados para DSA: 
    
    Seleccione la pestaña **ADN** y, luego, **Rules Engine** (Motor de reglas). 
    
    ADN es un término que emplea Verizon para especificar el contenido de DSA. Los puntos de conexión del perfil que no estén optimizados para DSA omiten las reglas creadas aquí. 

    ![Motor de reglas para DSA](./media/cdn-rules-engine/cdn-dsa-rules-engine.png)

## <a name="tutorial"></a>Tutorial
1. En la página **CDN profile** (Perfil de CDN), seleccione **Manage** (Administrar).
   
    ![Botón de administración de perfil de la red CDN](./media/cdn-rules-engine/cdn-manage-btn.png)
   
    Se abre el Portal de administración de CDN.
2. Seleccione la pestaña **HTTP Large** (HTTP grande) y, a continuación, seleccione **Rules Engine** (Motor de reglas).
   
    Se muestran las opciones para una nueva regla.
   
    ![Opciones de nueva regla de CDN](./media/cdn-rules-engine/cdn-new-rule.png)
   
   > [!IMPORTANT]
   > El orden en que se muestran varias reglas afecta a la manera en que se controlan. Una regla posterior puede invalidar las acciones especificadas por una regla anterior.
   > 
3. Escriba un nombre en el cuadro de texto **Nombre/Descripción** .
4. Identifique el tipo de solicitudes al que se aplica la regla. Use la condición de coincidencia predeterminada **Always** (Siempre). 
   
   ![Condición de coincidencia de regla de CDN](./media/cdn-rules-engine/cdn-request-type.png)
   
   > [!NOTE]
   > Existen varias condiciones de coincidencia en la lista desplegable. Para más información sobre la condición de coincidencia actualmente seleccionada, seleccione el icono de información azul que se encuentra a su izquierda.
   > 
   >  Para ver una lista detallada de las expresiones condicionales, consulte [Expresiones condicionales del motor de reglas](cdn-rules-engine-reference-match-conditions.md).
   >  
   > Para ver una lista detallada de las condiciones de coincidencia, consulte [Condiciones de coincidencia del motor de reglas](cdn-rules-engine-reference-match-conditions.md).
   > 
   > 
1. Para agregar una nueva función, haga clic en el botón **+** junto a **Features** (Características).  En la lista desplegable de la izquierda, seleccione **Aplicar Max-Age interno**.  En el cuadro de texto que aparece, escriba **300**. No cambie los valores predeterminados restantes.
   
   ![Características de reglas de CDN](./media/cdn-rules-engine/cdn-new-feature.png)
   
   > [!NOTE]
   > Hay muchas características disponibles en la lista desplegable. Para más información sobre la característica seleccionada actualmente, seleccione el icono de información azul que se encuentra a su izquierda. 
   >
   > En **Force Internal Max-Age** (Forzar Max-Age interna), los encabezados `Cache-Control` y `Expires` del recurso se invalidan para controlar cuándo el nodo perimetral de la red CDN actualiza el recurso desde el origen. En este ejemplo, el nodo perimetral de la red CDN almacena en caché el recurso durante 300 segundos, o 5 minutos, antes de actualizar el recurso desde su origen.
   > 
   > Para obtener una lista detallada de características, consulte [Características del motor de reglas](cdn-rules-engine-reference-features.md).
   > 
   > 
1. Haga clic en el botón **Agregar** para guardar la nueva regla.  La nueva regla ahora está pendiente de aprobación. Una vez aprobada, el estado cambia de **Pending XML** (XML pendiente) a **Active XML** (XML activo).
   
   > [!IMPORTANT]
   > Los cambios de las reglas pueden tardar hasta 90 minutos en propagarse a través de la red CDN.
   > 
   > 

## <a name="see-also"></a>Otras referencias
* [Información general de Azure CDN](cdn-overview.md)
* [Referencia del motor de reglas](cdn-rules-engine-reference.md)
* [Condiciones de coincidencia del motor de reglas](cdn-rules-engine-reference-match-conditions.md)
* [Expresiones condicionales del motor de reglas](cdn-rules-engine-reference-conditional-expressions.md)
* [Características del motor de reglas](cdn-rules-engine-reference-features.md)
* [Azure Fridays: Azure CDN's powerful new Premium Features](https://azure.microsoft.com/documentation/videos/azure-cdns-powerful-new-premium-features/) (Azure Fridays: Características nuevas y eficaces de la edición Premium de Azure CDN) (vídeo)