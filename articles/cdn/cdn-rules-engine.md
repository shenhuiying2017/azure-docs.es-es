---
title: "Invalidación del comportamiento HTTP predeterminado en la red CDN de Azure mediante el motor de reglas | Microsoft Docs"
description: "El motor de reglas permite personalizar cómo la red CDN de Azure controla las solicitudes HTTP, como el bloqueo de la entrega de determinados tipos de contenido, la definición de una directiva de almacenamiento en caché y la modificación de encabezados HTTP."
services: cdn
documentationcenter: 
author: camsoper
manager: erikre
editor: 
ms.assetid: 625a912b-91f2-485d-8991-128cc194ee71
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/28/2016
ms.author: casoper
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 86e0227f147238177d310e58dc126d6a7875bd68


---
# <a name="override-default-http-behavior-using-the-rules-engine"></a>Invalidar el comportamiento HTTP predeterminado mediante el motor de reglas
[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="overview"></a>Información general
El motor de reglas le permite personalizar cómo se controlan las solicitudes HTTP, tales como el bloqueo de la entrega de determinados tipos de contenido, la definición de una directiva de almacenamiento en caché y la modificación de encabezados HTTP.  En este tutorial se mostrará la creación de una regla que cambiará el comportamiento del almacenamiento en caché de los recursos de la red CDN.  También hay contenido de vídeo en la sección "[Consulte también](#see-also)".

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
   > Para la lista completa de las condiciones de coincidencia en detalle, vea [Condición de coincidencia del motor de reglas y detalles de la característica](https://msdn.microsoft.com/library/mt757336.aspx#Anchor_0).
   > 
   > 
5. Haga clic en el botón **+** que está al lado de **Características** para agregar una nueva característica.  En la lista desplegable de la izquierda, seleccione **Aplicar Max-Age interno**.  En el cuadro de texto que aparece, escriba **300**.  Deje los valores predeterminados restantes.
   
   ![Característica de CDN](./media/cdn-rules-engine/cdn-new-feature.png)
   
   > [!NOTE]
   > Como con las condiciones de coincidencia, haga clic en el icono informativo azul de la izquierda de la nueva característica para ver la información sobre esta característica.  In the case of **Force Internal Max-Age**, vamos a invalidar los encabezados **Cache-Control** y **Expires** del recurso para controlar en qué momento el nodo del borde CDN va a actualizar el recurso desde el origen.  Nuestro ejemplo de 300 segundos significa que el nodo perimetral de CDN almacenará en caché el activo durante 5 minutos antes de actualizar el activo desde su origen.
   > 
   > Para la lista completa de las características en detalle, vea [Condición de coincidencia del motor de reglas y detalles de la característica](https://msdn.microsoft.com/library/mt757336.aspx#Anchor_1).
   > 
   > 
6. Haga clic en el botón **Agregar** para guardar la nueva regla.  La nueva regla ahora está pendiente de aprobación. Una vez que se haya aprobado, el estado cambiará de **Pending XML** (XML pendiente) a **Active XML** (XML activo).
   
   > [!IMPORTANT]
   > Los cambios de las reglas pueden tardar hasta 90 minutos en propagarse a través de la red CDN.
   > 
   > 

## <a name="see-also"></a>Consulte también
* [Azure Fridays: Azure CDN's powerful new Premium Features](https://azure.microsoft.com/documentation/videos/azure-cdns-powerful-new-premium-features/) (Azure Fridays: Características nuevas y eficaces de la edición Premium de CDN de Azure) (vídeo)
* [Detalles de características y condiciones de coincidencia del motor de reglas de](https://msdn.microsoft.com/library/mt757336.aspx)




<!--HONumber=Nov16_HO3-->


