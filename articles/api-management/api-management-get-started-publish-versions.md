---
title: Publicar versiones de la API con Azure API Management | Microsoft Docs
description: "Siga los pasos de este tutorial para obtener información sobre cómo publicar varias versiones en API Management."
services: api-management
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.custom: mvc
ms.topic: tutorial
ms.date: 11/19/2017
ms.author: apimpm
ms.openlocfilehash: d63bdd3110f5c5db3e7bfec424644fdbc8d8d90c
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/04/2017
---
# <a name="publish-multiple-versions-of-your-api"></a>Publicación de varias versiones de la API 

A veces resulta poco práctico que todos los que llaman a la API usen exactamente la misma versión. Habrá quienes quieran publicar características de la API nuevas o diferentes para algunos usuarios, mientras que otros preferirán quedarse con la API que actualmente les vale. Si alguien que llama a una API quiere actualizarla a una versión posterior, querrá hacerlo con un método que sea fácil de comprender.  Esto se logra en Azure API Management con las **versiones**. Para más información, consulte [Versions & revisions](https://blogs.msdn.microsoft.com/apimanagement/2017/09/14/versions-revisions/) (Versiones y revisiones).

En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Agregar una nueva versión a una API existente
> * Elegir un esquema de versión
> * Agregar la versión a un producto
> * Examinar el portal para desarrolladores para ver la versión

![Versión que se muestra en el portal para desarrolladores](media/api-management-getstarted-publish-versions/azure_portal.PNG)

## <a name="prerequisites"></a>Requisitos previos

+ Completar la guía de inicio rápido siguiente: [Creación de una instancia de Azure API Management](get-started-create-service-instance.md).
+ Además, completar el tutorial siguiente: [Import and publish your first API](import-and-publish.md) (Importación y publicación de la primera API).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="add-a-new-version"></a>Agregar una nueva versión

![Menú contextual de la API: agregar versión](media/api-management-getstarted-publish-versions/AddVersionMenu.png)

1. Seleccione **Conference API** en la lista de API.
2. Seleccione el menú contextual (**...** ) junto a ella.
3. Seleccione **+ Agregar versión**.

    > [!TIP]
    > Las versiones también se pueden habilitar al crear una API desde cero (para ello, seleccione **¿Definir versión de esta API?** en la pantalla **Agregar API**).

## <a name="choose-a-versioning-scheme"></a>Elegir un esquema de control de versiones

Con Azure API Management, puede elegir la forma en la que quiere que quienes llamen a una API puedan especificar qué versión de dicha API quieren. Para ello, elija un **Esquema de control de versiones**. Este esquema puede ser una **ruta de acceso, un encabezado o una cadena de consulta**. En el ejemplo, se usa una ruta de acceso.

![Pantalla Agregar versión](media/api-management-getstarted-publish-versions/AddVersion.PNG)

1. Deje **ruta de acceso** seleccionada como **esquema de control de versiones**.
2. Agregue **v1** como **identificador de versión**.

    > [!TIP]
    > Si selecciona **encabezado** o **cadena de consulta** como esquema de control de versiones, debe proporcionar un valor más: el nombre del parámetro de encabezado o de cadena de consulta.

3. Aporte una descripción si así lo desea.
4. Seleccione **Crear** para configurar la nueva versión.
5. Debajo de la directiva **Big Conference API** en la lista de API, ahora verá dos API distintas: **Original** y **v1**.

    ![Versiones que aparecen en una API en Azure Portal](media/api-management-getstarted-publish-versions/VersionList.PNG)

    > [!Note]
    > Si agrega una versión a una API sin control de versiones, siempre se crea un **Original** automáticamente que responde a la dirección URL predeterminada. Esto garantiza que quienes estén llamando en ese momento no sufran ninguna interrupción a causa del proceso de adición de la versión. Si crea una API con las versiones habilitadas desde el principio, no se creará un original.

6. Ahora puede modificar y configurar **v1** como una API independiente de **Original**. Los cambios en una versión no afectan a otra.

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

    ![Menú contextual de la API: agregar versión](media/api-management-getstarted-publish-versions/developer_portal.png)

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Agregar una nueva versión a una API existente
> * Elegir un esquema de versión 
> * Agregar la versión a un producto
> * Examinar el portal para desarrolladores para ver la versión

Avance hasta el siguiente tutorial:

> [!div class="nextstepaction"]
> [Actualización y escalado](upgrade-and-scale.md)