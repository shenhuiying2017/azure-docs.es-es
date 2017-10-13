---
title: Publicar versiones de la API con Azure API Management | Microsoft Docs
description: "Siga los pasos de este tutorial para obtener información sobre cómo publicar varias versiones en API Management."
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
ms.openlocfilehash: 7c355e2feb5ebe5971d8391b326422a1abec1497
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="publish-multiple-versions-of-your-api-in-a-predictable-way"></a>Publicar varias versiones de la API de manera predecible
En este tutorial se explica cómo configurar distintas versiones de la API y cómo elegir el modo en que van a llamarlas los desarrolladores de API.

## <a name="prerequisites"></a>Requisitos previos
Para completar este tutorial, debe crear un servicio API Management y disponer de una API existente que pueda modificar (en lugar de Conference API).

## <a name="about-versions"></a>Acerca de las versiones
A veces resulta poco práctico que todos los que llaman a la API usen exactamente la misma versión. Habrá quienes quieran publicar características de la API nuevas o diferentes para algunos usuarios, mientras que otros preferirán quedarse con la API que actualmente les vale. Si alguien que llama a una API quiere actualizarla a una versión posterior, querrá hacerlo con un método que sea fácil de comprender.  Esto se logra en Azure API Management con las **versiones**.

## <a name="walkthrough"></a>Tutorial
En este tutorial se agregará una nueva versión de una API existente, seleccionando para ello un esquema de control de versiones y un identificador de versión.

## <a name="add-a-new-version"></a>Agregar una nueva versión
![Menú contextual de la API: agregar versión](media/api-management-getstarted-publish-versions/AddVersionMenu.png)
1. Vaya a la página de **API** del servicio API Management en Azure Portal.
2. Seleccione **Conference API** de la lista de API y, después, seleccione el menú contextual (**...**) que hay junto a ella.
3. Seleccione **+ Agregar versión**.

    > [!TIP]
    > Las versiones también se pueden habilitar al crear una API desde cero (para ello, seleccione **¿Definir versión de esta API?** en la pantalla **Agregar API**).

## <a name="choose-a-versioning-scheme"></a>Elegir un esquema de control de versiones
Con Azure API Management, puede elegir la forma en la que quiere que quienes llamen a una API puedan especificar qué versión de dicha API quieren. Para ello, elija un **Esquema de control de versiones**. Este esquema puede ser una **ruta de acceso, un encabezado o una cadena de consulta**. En el ejemplo, se usa una ruta de acceso.
![Pantalla Agregar versión](media/api-management-getstarted-publish-versions/AddVersion.PNG)
1. Deje **ruta de acceso** seleccionada como **esquema de control de versiones**.
2. Agregue **v1** como **identificador de versión**.

    > [!TIP]
    > Si selecciona **encabezado** o **cadena de consulta** como esquema de control de versiones, deberá indicar un valor más: el nombre del parámetro de encabezado o de cadena de consulta.

3. Aporte una descripción si así lo desea.
4. Seleccione **Crear** para configurar la nueva versión.
5. Debajo de la directiva **Big Conference API** en la lista de API, ahora verá dos API distintas: **Original** y **v1**.
![Versiones que aparecen en una API en Azure Portal](media/api-management-getstarted-publish-versions/VersionList.PNG)

    > [!Note]
    > Si agrega una versión a una API sin control de versiones, siempre se crea un **Original** automáticamente que responde a la dirección URL predeterminada. Esto garantiza que quienes estén llamando en ese momento no sufran ninguna interrupción a causa del proceso de adición de la versión. Si crea una API con las versiones habilitadas desde el principio, no se creará un original.

6. Ahora puede modificar y configurar **v1** como una API completamente independiente de **Original**. Los cambios en una versión no afectan a otra.

## <a name="add-the-version-to-a-product"></a>Agregar la versión a un producto
Para que quienes llamen a la API vean la nueva versión, esta debe agregarse a un **producto** (los productos no se heredan de las versiones principales).

1. Seleccione **Productos** en la página de Service Management.
2. Seleccione **Ilimitado**.
3. Seleccione **API**.
4. Seleccione **Agregar**.
5. Seleccione **Conference API, versión v1**.
6. Vuelva a la página de Service Management y seleccione **API**.

## <a name="browse-the-developer-portal-to-see-the-version"></a>Examinar el portal para desarrolladores para ver la versión
1. Seleccione **Portal para desarrolladores** en el menú superior.
2. Seleccione **API** y observe que **Conference API** muestra las versiones **Original** y **v1**.
3. Seleccione **v1**.
4. Fíjese en la **Dirección URL de la solicitud** de la primera operación de la lista. Refleja que la ruta de acceso de la dirección URL de la API incluye **v1**.
![Versión que se muestra en el portal para desarrolladores](media/api-management-getstarted-publish-versions/VersionDevPortal.PNG)
