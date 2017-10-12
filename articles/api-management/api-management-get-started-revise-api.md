---
title: Usar revisiones para actualizar la API en Azure API Management | Microsoft Docs
description: "Siga los pasos de este tutorial para obtener información sobre cómo realizar cambios que no producen interrupciones a través de revisiones en API Management."
services: api-management
documentationcenter: 
author: mattfarm
manager: anneta
editor: 
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 08/18/2017
ms.author: apimpm
ms.openlocfilehash: 0d67166a16ae4d640080ad83e7625e594b0dc246
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="make-non-breaking-changes-safely-using-revisions"></a>Realizar cambios que no producen interrupciones de forma segura con revisiones
En este tutorial se explica cómo realizar cambios de forma segura en la API y comunicar esos cambios a los desarrolladores.

## <a name="prerequisites"></a>Requisitos previos
Para completar este tutorial, debe crear un servicio API Management y disponer de una API existente que pueda modificar (en lugar de Conference API).

## <a name="about-revisions"></a>Acerca de las revisiones
Cuando la API esté lista y los desarrolladores empiecen a usarla, normalmente hay que tener cuidado al realizar cambios en dicha API (para que quienes llamen a la API no sufran interrupciones). También resulta útil informar a los desarrolladores de los cambios realizados. Esto se logra en Azure API Management con las **revisiones**.

## <a name="walkthrough"></a>Tutorial
En este tutorial agregaremos una nueva revisión, le agregaremos una operación y, luego, la convertiremos en actual, creando para ello una entrada en el registro de cambios cuando lo hagamos.

## <a name="add-a-new-revision"></a>Agregar una nueva revisión
1. Vaya a la página de **API** del servicio API Management en Azure Portal.
2. Seleccione **Conference API** de la lista de API y, después, seleccione la pestaña **Revisiones** en el menú junto a la parte superior de la página.
3. Seleccione **+ Agregar revisión**.

    > [!TIP]
    > También puede seleccionar **Agregar revisión** en el menú contextual (**...**) de la API.
![Menú Revisiones cerca de la parte superior de la pantalla](media/api-management-getstarted-revise-api/TopMenu.PNG)

4. Incluya una descripción de la nueva revisión que ayude a saber para qué va a servir.
5. Seleccione **Crear**
6. Ya tenemos creada la revisión.

    > [!NOTE]
    > La API original permanece en **Revisión 1**. Esta es la revisión que los usuarios seguirán llamando hasta que decida convertir otra en actual.

## <a name="make-non-breaking-changes-to-your-revision"></a>Realizar cambios que no producen interrupciones en la revisión
1. Seleccione la pestaña **Diseño** situada junto a la parte superior de la pantalla.
2. Fíjese en que el **selector de revisión** (justo encima de la pestaña Diseño) muestra la revisión actual como **Revisión 2**.

    > [!TIP]
    > Use el selector de revisión para alternar entre las revisiones con las que vaya a trabajar.

3. Seleccione **+ Agregar operación**.
4. Establezca la nueva operación como **POST** y especifique **Comentarios** como nombre y nombre para mostrar de esa operación.
5. **Guarde** la nueva operación.
6. Ya hemos hecho un cambio en **Revisión 2**. Use el **selector de revisión** junto a la parte superior de la página para volver a **Revisión 1**.
7. Observe que la nueva operación no figura en **Revisión 1**. 

## <a name="make-your-revision-current-and-add-a-change-log-entry"></a>Convertir la revisión en actual y agregar una entrada en el registro de cambios
1. Seleccione la pestaña **Revisiones** en el menú junto a la parte superior de la página.
![Menú de revisión en la pantalla de revisión.](media/api-management-getstarted-revise-api/RevisionsMenu.PNG)
2. Abra el menú contextual (**...**) de **Revisión 2**.
3. Seleccione **Convertir en actual**.
![Convertir la revisión en actual y publicar en el registro de cambios](media/api-management-getstarted-revise-api/MakeCurrent.PNG)
4. Seleccione **Post to Public Change Log for this API** (Publicar en el registro de cambios público de esta API).
5. Incluya una descripción del cambio que puedan ver los desarrolladores (por ejemplo, **"Se agregó una nueva operación de comentarios"**).
6. **Revisión 2** es ahora la revisión actual.

## <a name="browse-the-developer-portal-to-see-changes-and-change-log"></a>Examinar el portal para desarrolladores para ver los cambios y registro de cambios
1. Seleccione **Portal para desarrolladores** en el menú superior.
2. Seleccione **API** y, después, **Conference API**.
3. Observe que la nueva operación **Comentarios** ahora aparece disponible.
4. Seleccione **API Change History** (Historial de cambios de la API) debajo del nombre de la API.
5. Vea que la entrada en el registro de cambios aparece en esta lista.
![Registro de cambios en el portal para desarrolladores](media/api-management-getstarted-revise-api/ChangeLogDevPortal.PNG)

## <a name="next-steps"></a>Pasos siguientes
[Publicar versiones de API con Azure API Management](#api-management-getstarted-publish-versions.md)