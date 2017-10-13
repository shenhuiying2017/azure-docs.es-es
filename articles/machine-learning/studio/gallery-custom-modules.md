---
title: "Módulos personalizados de la Galería de Cortana Intelligence | Microsoft Docs"
description: "Descubra los módulos de aprendizaje automático personalizados de la Galería de Cortana Intelligence."
services: machine-learning
documentationcenter: 
author: garyericson
manager: jhubbard
editor: cgronlun
ms.assetid: 16037a84-dad0-4a8c-9874-a1d3bd551cf0
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/14/2017
ms.author: roopalik;garye
ms.openlocfilehash: 4bab94c04f09261eaa88b9e6a225c05f57992ab0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="discover-custom-machine-learning-modules-in-cortana-intelligence-gallery"></a>Módulos de aprendizaje automático personalizados de la Galería de Cortana Intelligence
[!INCLUDE [machine-learning-gallery-item-selector](../../../includes/machine-learning-gallery-item-selector.md)]

## <a name="custom-modules-for-machine-learning-studio"></a>Módulos personalizados de Machine Learning Studio
La Galería de Cortana Intelligence ofrece varios [módulos personalizados](https://gallery.cortanaintelligence.com/customModules) que amplían las capacidades de Azure Machine Learning Studio. Puede importar los módulos para su uso en los experimentos, para poder desarrollar soluciones de análisis predictivo aún más avanzadas.

Actualmente, la Galería ofrece módulos sobre *análisis de series temporales*, *reglas de asociación*, *algoritmos de clústeres* (más allá de k-means) y *visualizaciones*, además de otros módulos de batalla.


## <a name="discover"></a>Descubra
Para examinar los módulos personalizados [de la Galería](http://gallery.cortanaintelligence.com), en **Más**, seleccione **Módulos personalizados**.

![Seleccionar Módulos personalizados en la página principal de la Galería](./media/gallery-custom-modules/select-custom-modules-in-gallery.png)

La página **[Módulos personalizados](https://gallery.cortanaintelligence.com/customModules)** muestra una lista de los módulos populares y los agregados recientemente. Para ver todos los módulos personalizados, seleccione el botón **Ver todos**. Para buscar un módulo personalizado concreto, seleccione **Ver todos** y luego seleccione criterios de filtro. También puede escribir términos de búsqueda en el cuadro **Buscar** de la parte superior de la página Galería.

![Seleccione "Ver todos" para examinar todos los módulos personalizados.](./media/gallery-custom-modules/click-see-all-for-all-custom-modules.png)

### <a name="understand"></a>Entender

Para entender cómo funciona un módulo personalizado publicado, seleccione el módulo personalizado para abrir su página de detalles. La página de detalles ofrece una experiencia de aprendizaje coherente e informativa. Por ejemplo, la página de detalles resalta la finalidad del módulo y enumera parámetros, salidas y entradas esperados. La página de detalles también incluye un vínculo al código fuente subyacente, que se puede examinar y personalizar.

### <a name="comment-and-share"></a>Comentario y recurso compartido
En la sección **Comentarios** de la página de detalles de un módulo personalizado puede comentar, dar opiniones o formular preguntas sobre ese módulo. Incluso puede compartir el módulo con amigos o compañeros en Twitter o LinkedIn. También puede enviar por correo electrónico un vínculo a la página de detalles de un módulo para invitar a otros usuarios a ver la página.

![Compartir este elemento con amigos](./media/gallery-how-to-use-contribute-publish/share-links.png)

![Agregar sus comentarios](./media/gallery-how-to-use-contribute-publish/comments.png)

## <a name="import"></a>Importar
Puede importar cualquier módulo personalizado desde la Galería a sus propios experimentos.

La Galería de Cortana Intelligence ofrece dos formas de importar una copia del módulo:

* **Desde la Galería**. Al importar un módulo personalizado desde la Galería, también obtendrá un experimento de ejemplo que le proporciona un ejemplo sobre cómo usar el módulo.
* **Desde Machine Learning Studio**. Puede importar cualquier módulo personalizado mientras está trabajando en Machine Learning Studio (en este caso no se obtiene el experimento de ejemplo).

### <a name="from-the-gallery"></a>Desde la galería

1. En la Galería, abra la página de detalles del módulo. 
2. Seleccione **Abrir en Studio**.
   
    ![Abrir módulo personalizado desde la galería](./media/gallery-custom-modules/open-custom-module-from-gallery.png)
   
Cada módulo personalizado incluye un experimento de ejemplo que muestra cómo utilizar el módulo. Cuando se selecciona **Abrir en Studio**, el experimento de ejemplo se abre en el área de trabajo de Machine Learning Studio. (Si aún no ha iniciado sesión en Studio, se le pide que primero lo haga con la cuenta de Microsoft).

Además del experimento de ejemplo, el módulo personalizado se copia en el área de trabajo. También se coloca en la paleta de módulos, con todos los módulos integrados o personalizados de Machine Learning Studio. Ahora puede usarlo en sus propios experimentos como cualquier otro módulo del área de trabajo.

### <a name="from-within-machine-learning-studio"></a>Desde Machine Learning Studio

1. En Machine Learning Studio, seleccione **NUEVO**.
2. Seleccione **Módulo**. Puede elegir entre una lista de módulos de la Galería o buscar un módulo determinado mediante el cuadro **Buscar**.
3. Coloque el mouse sobre un módulo y luego seleccione **Importar módulo**. (Para ver información sobre el módulo, seleccione **Ver en la Galería**. Esto lleva a la página de detalles del módulo en la Galería).
   
    ![Importar el módulo personalizado en Machine Learning Studio](./media/gallery-custom-modules/add-custom-module-in-studio.png)

El módulo personalizado se copia en el área de trabajo y se coloca en la paleta de módulos con los módulos personalizados o integrados de Machine Learning Studio. Ahora puede usarlo en sus propios experimentos como cualquier otro módulo del área de trabajo.

## <a name="use"></a>Uso

Independientemente del método que elija para importar un módulo personalizado, al importarlo, el módulo se coloca en la paleta de módulos de Machine Learning Studio. Desde la paleta de módulos, puede usar el módulo personalizado en cualquier experimento en el área de trabajo, como cualquier otro módulo.

Para usar un módulo importado:

1. Cree un experimento o abra uno existente.
2. Para expandir la lista de módulos personalizados en el área de trabajo, en la paleta de módulos, seleccione **Personalizados**. La paleta de módulos se encuentra a la izquierda del lienzo de experimentos.
   
    ![Lista de módulos personalizados en la paleta de Studio](./media/gallery-custom-modules/custom-module-in-studio-palette.png)
3. Seleccione el módulo importado y arrástrelo al experimento.


**[Ir a la Galería](http://gallery.cortanaintelligence.com)**

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

