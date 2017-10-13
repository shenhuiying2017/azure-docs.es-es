---
title: "Invalidación del comportamiento HTTP mediante el motor de reglas de la red CDN de Azure | Microsoft Docs"
description: "El motor de reglas permite personalizar cómo la red CDN de Azure controla las solicitudes HTTP, como el bloqueo de la entrega de determinados tipos de contenido, la definición de una directiva de almacenamiento en caché y la modificación de encabezados HTTP."
services: cdn
documentationcenter: 
author: zhangmanling
manager: erikre
editor: 
ms.assetid: 625a912b-91f2-485d-8991-128cc194ee71
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: abfe283476206b181018d187675b47112dc5ad2f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="override-http-behavior-using-the-azure-cdn-rules-engine"></a>Invalidación del comportamiento HTTP mediante el motor de reglas de la red CDN de Azure
[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="overview"></a>Información general
El motor de reglas le permite personalizar cómo se controlan las solicitudes HTTP, tales como el bloqueo de la entrega de determinados tipos de contenido, la definición de una directiva de almacenamiento en caché y la modificación de encabezados HTTP.  En este tutorial se mostrará la creación de una regla que cambiará el comportamiento del almacenamiento en caché de los recursos de la red CDN.  También hay contenido de vídeo en la sección "[Consulte también](#see-also)".

   > [!TIP] 
   > Para consultar la sintaxis de forma detallada, vea la [Referencia del motor de reglas](cdn-rules-engine-reference.md).
   > 


## <a name="tutorial"></a>Tutorial
1. En la hoja de perfil de CDN, haga clic en el botón **Administrar** .
   
    ![Botón de administración de hoja de perfil de red CDN](./media/cdn-rules-engine/cdn-manage-btn.png)
   
    Se abre el portal de administración de CDN.
2. Haga clic en la pestaña **HTTP Large** (HTTP grande) y, después, en **Motor de reglas**.
   
    Se muestran las opciones para una nueva regla.
   
    ![Opciones de nueva regla de CDN](./media/cdn-rules-engine/cdn-new-rule.png)
   
   > [!IMPORTANT]
   > El orden en que se muestran varias reglas afecta a la manera en que se controlan. Una regla posterior puede invalidar las acciones especificadas por una regla anterior.
   > 
   > 
3. Escriba un nombre en el cuadro de texto **Nombre/Descripción** .
4. Identifique el tipo de solicitudes al que se aplicará la regla.  De forma predeterminada, se selecciona la condición de coincidencia **Siempre** .  Usará **Siempre** para este tutorial, así pues, deje esa opción seleccionada.
   
   ![Condición de coincidencia de red CDN](./media/cdn-rules-engine/cdn-request-type.png)
   
   > [!TIP]
   > Hay muchos tipos de condiciones de coincidencia disponibles en la lista desplegable.  Haga clic en el icono de información azul de la izquierda de la condición de coincidencia para ver una explicación de la condición seleccionada actualmente en detalle.
   > 
   >  Para obtener la lista completa de las expresiones condicionales en detalle, consulte las [Expresiones condicionales del motor de reglas](cdn-rules-engine-reference-match-conditions.md).
   >  
   > Para obtener la lista completa de las condiciones de coincidencia en detalle, consulte las [Condiciones de coincidencia del motor de reglas](cdn-rules-engine-reference-match-conditions.md).
   > 
   > 
5. Haga clic en el botón **+** que está al lado de **Características** para agregar una nueva característica.  En la lista desplegable de la izquierda, seleccione **Aplicar Max-Age interno**.  En el cuadro de texto que aparece, escriba **300**.  Deje los valores predeterminados restantes.
   
   ![Característica de CDN](./media/cdn-rules-engine/cdn-new-feature.png)
   
   > [!NOTE]
   > Como con las condiciones de coincidencia, haga clic en el icono informativo azul de la izquierda de la nueva característica para ver la información sobre esta característica.  In the case of **Force Internal Max-Age**, vamos a invalidar los encabezados **Cache-Control** y **Expires** del recurso para controlar en qué momento el nodo del borde CDN va a actualizar el recurso desde el origen.  Nuestro ejemplo de 300 segundos significa que el nodo perimetral de CDN almacenará en caché el activo durante 5 minutos antes de actualizar el activo desde su origen.
   > 
   > Para obtener la lista completa de las características en detalle, consulte la [Información detallada de las características del motor de reglas](cdn-rules-engine-reference-features.md).
   > 
   > 
6. Haga clic en el botón **Agregar** para guardar la nueva regla.  La nueva regla ahora está pendiente de aprobación. Una vez que se haya aprobado, el estado cambiará de **Pending XML** (XML pendiente) a **Active XML** (XML activo).
   
   > [!IMPORTANT]
   > Los cambios de las reglas pueden tardar hasta 90 minutos en propagarse a través de la red CDN.
   > 
   > 

## <a name="see-also"></a>Otras referencias
* [Información general de la red CDN de Azure](cdn-overview.md)
* [Referencia del motor de reglas](cdn-rules-engine-reference.md)
* [Condiciones de coincidencia del motor de reglas](cdn-rules-engine-reference-match-conditions.md)
* [Expresiones condicionales del motor de reglas](cdn-rules-engine-reference-conditional-expressions.md)
* [Funciones del motor de reglas](cdn-rules-engine-reference-features.md)
* [Invalidación del comportamiento HTTP predeterminado mediante el motor de reglas](cdn-rules-engine.md)
* [Azure Fridays: Azure CDN's powerful new Premium Features](https://azure.microsoft.com/documentation/videos/azure-cdns-powerful-new-premium-features/) (Azure Fridays: Características nuevas y eficaces de la edición Premium de CDN de Azure) (vídeo)