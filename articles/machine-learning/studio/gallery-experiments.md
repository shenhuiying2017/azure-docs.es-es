---
title: "Experimentos de la Galería de Azure AI | Microsoft Docs"
description: "Descubra y comparta experimentos en la Galería de Azure AI."
services: machine-learning
documentationcenter: 
author: garyericson
manager: jhubbard
editor: cgronlun
ms.assetid: f4248922-c961-4d3a-9e1b-aec743210166
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/26/2017
ms.author: roopalik;garye
ms.openlocfilehash: ad41d431b78a69e54bb5e4df3aaea1ec7c858b7f
ms.sourcegitcommit: 0e1c4b925c778de4924c4985504a1791b8330c71
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/06/2018
---
# <a name="discover-experiments-in-azure-ai-gallery"></a>Descubrimiento de experimentos en la Galería de Azure AI
[!INCLUDE [machine-learning-gallery-item-selector](../../../includes/machine-learning-gallery-item-selector.md)]

## <a name="experiments-for-machine-learning-studio"></a>Experimentos de Machine Learning Studio
La Galería tiene una gran variedad de [experimentos](https://gallery.cortanaintelligence.com/experiments) que se han desarrollado en [Azure Machine Learning Studio](https://studio.azureml.net). Van desde experimentos rápidos de prueba de concepto que muestran una técnica específica de aprendizaje automático, hasta soluciones totalmente desarrolladas para problemas de aprendizaje automático complejos.

> [!NOTE]
> Un ***experimento*** es un lienzo de Machine Learning Studio que se puede usar para crear un modelo de análisis predictivo. El modelo se crea al conectar datos con diversos módulos analíticos. Puede probar diferentes ideas, realizar pruebas y finalmente implementar el modelo como un servicio web en Azure. Para ver un ejemplo de creación de un experimento básico, vea [Tutorial de aprendizaje automático: creación del primer experimento de ciencia de datos en Azure Machine Learning Studio](create-experiment.md). Para ver un tutorial más completo sobre la creación de una solución de análisis predictivo, vea [Tutorial: Desarrollo de una solución de análisis predictiva para la evaluación del riesgo de crédito en Azure Machine Learning](walkthrough-develop-predictive-solution.md).
>
>

## <a name="discover"></a>Descubra
Para buscar los experimentos [de la Galería](http://gallery.cortanaintelligence.com), en la parte superior de la página principal de la Galería, seleccione **Experimentos**.

La página **[Experimentos](https://gallery.cortanaintelligence.com/experiments)** muestra una lista de los experimentos populares y los agregados recientemente. Para ver todos los experimentos, seleccione el botón **Ver todos**. Para buscar un experimento concreto, seleccione **Ver todos** y luego seleccione criterios de filtro. También puede escribir términos de búsqueda en el cuadro **Buscar** de la parte superior de la página Galería.

Puede obtener más información sobre un experimento en la página de detalles de ese experimento. Para abrir la página de detalles de un experimento, seleccione ese experimento. En la sección **Comentarios** de la página de detalles de un experimento puede comentar, dar opiniones o formular preguntas sobre ese experimento. Incluso puede compartir el experimento con amigos o compañeros en Twitter o LinkedIn. También puede enviar por correo electrónico un vínculo a la página de detalles de un experimento para invitar a otros usuarios a ver la página.

![Compartir este elemento con amigos](./media/gallery-how-to-use-contribute-publish/share-links.png)

![Agregar sus comentarios](./media/gallery-how-to-use-contribute-publish/comments.png)

## <a name="download"></a>Descargar
Puede descargar una copia de cualquier experimento de la Galería en el área de trabajo de Machine Learning Studio. Luego puede modificar la copia para crear sus propias soluciones.

La Galería de Azure AI ofrece dos formas de importar una copia de un experimento:

* **Desde la Galería**. Si encuentra un experimento que le guste en la Galería, puede descargar una copia y luego abrirla en el área de trabajo de Machine Learning Studio.
* **Desde Machine Learning Studio**. En Machine Learning Studio, puede usar cualquier experimento de la Galería como plantilla para crear un nuevo experimento.

### <a name="from-the-gallery"></a>Desde la galería

1. En la Galería, abra la página de detalles del experimento.
2. Seleccione **Abrir en Studio**.

    ![Abrir un experimento desde la galería](./media/gallery-experiments/open-experiment-from-gallery.png)

Cuando se selecciona **Abrir en Studio**, el experimento se abre en el área de trabajo de Machine Learning Studio. (Si aún no ha iniciado sesión en Machine Learning Studio, se le pide que primero lo haga con la cuenta de Microsoft).

### <a name="from-within-machine-learning-studio"></a>Desde Machine Learning Studio

1. En Machine Learning Studio, seleccione **NUEVO**.
2. Seleccione **Experimento**. Puede elegir entre una lista de experimentos de la Galería o buscar un experimento determinado mediante el cuadro **Buscar**.
3. Coloque el mouse en un experimento y luego seleccione **Abrir en Studio**. (Para ver información sobre el experimento, seleccione **Ver en la Galería**. Esto lleva a la página de detalles del experimento en la Galería).

    ![Abrir el experimento de la galería desde dentro de Machine Learning Studio](./media/gallery-experiments/open-experiment-from-studio.png)

Puede personalizar, recorrer en iteración e implementar un experimento descargado como cualquier experimento que cree en Machine Learning Studio.

![Experimento abierto en Studio](./media/gallery-experiments/experiment-open-in-studio.png)

## <a name="contribute"></a>Contribuciones
En el momento en que inicia sesión en la Galería, se convierte en un miembro de la comunidad de la Galería. Como miembro de la comunidad, puede aportar sus propios experimentos para que otros usuarios puedan beneficiarse de las soluciones que haya descubierto.

### <a name="publish-your-experiment-to-the-gallery"></a>Publicar el experimento en la galería

1. Inicie sesión en Machine Learning Studio con la cuenta de Microsoft.
2. Cree el experimento y luego ejecútelo.
3. Cuando esté preparado para publicar el experimento en la Galería, seleccione **Publicar en la galería** en la lista de acciones debajo del lienzo de experimentos.

    ![Seleccionar "Publicar en la galería"](./media/gallery-experiments/publish-experiment-to-gallery.png)
4. En la página **Descripción del experimento**, escriba un título y etiquetas. Intente que las etiquetas y el título sean descriptivos. Resalte las técnicas que ha usado o el problema real que resuelve. Un ejemplo de un título de experimento descriptivo es "Clasificación binaria: análisis de opinión de Twitter".

    ![Escribir el título y las etiquetas para su publicación](./media/gallery-experiments/experiment-description.png)
5. En el cuadro **RESUMEN**, escriba un resumen del experimento. Describa brevemente el problema que resuelve el experimento y cómo se ha abordado.
6. En el cuadro **DESCRIPCIÓN DETALLADA**, describa los pasos de cada parte del experimento. Algunos temas útiles para incluir son:
   * Captura de pantalla del gráfico del experimento
   * Orígenes de datos y explicación
   * Procesamiento de datos
   * Ingeniería de características
   * Descripción del modelo
   * Resultados y evaluación del rendimiento del modelo.

   Puede usar Markdown para aplicar formato a la descripción. Para ver el aspecto de las entradas de la página de descripción del experimento una vez publicado este, seleccione **Vista previa**.

   ![Seleccionar "Vista previa" para ver el aspecto que tendrá el texto](./media/gallery-experiments/preview-markdown-text.png)

   > [!TIP]
   > Los cuadros de texto proporcionados para la edición y la vista previa de Markdown son pequeños. Se recomienda escribir la documentación completa del experimento en un editor de Markdown, copiarla y luego pegarla en el cuadro de texto de la Galería. Después de publicar el experimento, puede realizar cualquier corrección mediante herramientas estándar basadas en web que usen Markdown para la edición y la vista previa.

7. En la página **Selección de imagen**, elija una imagen en miniatura para el experimento. La imagen en miniatura aparece en la parte superior de la página de detalles y en el icono del experimento. Los demás usuarios verán la imagen en miniatura cuando examinen la Galería. Puede cargar una imagen de su equipo o seleccionar una imagen de archivo de la Galería.
    </br>
    ![Cargar o seleccionar una imagen de la galería](./media/gallery-experiments/select-gallery-image.png)
8. En la página **Configuración**, en **Visibilidad**, elija si va a publicar el contenido públicamente (**Público**) o si solo será accesible para las personas con un vínculo a la página (**No enumerado**).

    ![Elija si desea publicar públicamente o como no enumerado](./media/gallery-experiments/choose-public-or-unlisted.png)

    <!-- -->

   > [!TIP]
   > Si quiere asegurarse de que el aspecto de la documentación sea correcto antes de publicarla, puede publicar el experimento primero como **No enumerado**. Más adelante puede cambiar la configuración de visibilidad a **Público** en la página de detalles del experimento.
   >
   >
9. Para publicar el experimento en la Galería, active la casilla **Aceptar**.

    ![Activar la casilla Aceptar para publicar el experimento](./media/gallery-experiments/ok-checkmark.png)

Para obtener sugerencias sobre cómo publicar un experimento de la Galería de alta calidad, vea [Sugerencias para la publicación y documentación del experimento](#tips-for-documenting-and-publishing-your-experiment).

Eso es todo, ya ha terminado.

Ahora puede ver el experimento en la Galería y compartir el vínculo con otros usuarios. Si ha publicado el experimento con la configuración de visibilidad **Público**, se muestra en los resultados de exploración y búsqueda de la Galería. Puede editar la documentación del experimento en la página de detalles del experimento cada vez que inicie sesión en la Galería.

Para ver la lista de contribuciones, seleccione la imagen en la esquina superior derecha de cualquier página de la Galería. Luego seleccione el nombre para abrir la página de cuenta.

![Seleccionar el nombre de cuenta](./media/gallery-experiments/click-account-name.png)

### <a name="update-your-experiment"></a>Actualizar el experimento
Si lo necesita, puede realizar cambios en el flujo de trabajo (módulos, parámetros, etc.) de un experimento que haya publicado en la Galería. En Machine Learning Studio, realice los cambios que quiera en el experimento y luego vuelva a publicarlo. El experimento publicado se actualizará con los cambios.

Puede cambiar cualquiera de la siguiente información del experimento directamente en la Galería:

* Nombre del experimento
* Resumen o descripción
* Etiquetas
* Imagen
* Configuración de visibilidad (**Público** o **No enumerado**)

También puede eliminar el experimento de la Galería.

Puede realizar estos cambios, o eliminar el experimento, desde la página de detalles del experimento o desde la página de perfil de la Galería.


#### <a name="from-the-experiment-details-page"></a>Desde la página de detalles del experimento
En la página de detalles del experimento, seleccione **Editar** para cambiar los detalles del experimento.

![Seleccionar Editar para editar el experimento](./media/gallery-experiments/edit-button.png)

La página de detalles se pone en modo de edición. Para realizar cambios, seleccione **Editar** junto al nombre, el resumen o las etiquetas del experimento. Cuando haya terminado de realizar cambios, seleccione **Listo**.

![Seleccionar "Editar" para editar detalles y "Listo" cuando haya finalizado](./media/gallery-experiments/edit-details-page.png)

Para cambiar la configuración de visibilidad del experimento (**Público** o **No enumerado**), o para eliminar el experimento de la Galería, seleccione el icono **Configuración**.

![Seleccionar "Configuración" para cambiar la visibilidad del experimento o eliminarlo](./media/gallery-experiments/settings-button.png)

#### <a name="from-your-profile-page"></a>Desde su página de perfil
En la página de perfil, seleccione la flecha abajo del experimento y luego **Editar**. Esto le lleva a la página de detalles del experimento, en modo de edición. Cuando haya terminado de realizar cambios, seleccione **Listo**.

Para eliminar el experimento desde la Galería, seleccione **Eliminar**.

![Seleccionar "Editar" o "Eliminar"](./media/gallery-experiments/edit-delete-buttons.png)

### <a name="tips-for-documenting-and-publishing-your-experiment"></a>Sugerencias para la publicación y documentación del experimento
* Puede asumir que el lector tiene experiencia previa con la ciencia de datos, aunque puede resultar útil usar un lenguaje sencillo. Explique las cosas detalladamente siempre que sea posible.
* El conjunto de aplicaciones de Cortana Intelligence es relativamente reciente. No todos los lectores están familiarizados con su uso. Proporcione suficiente información y explicaciones paso a paso para ayudar a los lectores a desplazarse por el experimento.
* Los elementos visuales pueden resultar útiles para los lectores a fin de interpretar y usar la documentación del experimento correctamente. Los elementos visuales incluyen gráficos del experimento y capturas de pantalla de los datos. Para más información sobre cómo incluir imágenes en la documentación del experimento, vea la colección [Publishing Guidelines and Examples (Directrices y ejemplos de publicación)](https://gallery.cortanaintelligence.com/Collection/Publishing-Guidelines-and-Examples-1).
* Si incluye un conjunto de datos en el experimento (es decir, no importa el conjunto de datos mediante el módulo Importar datos), el conjunto de datos forma parte del experimento y se publica en la Galería. Asegúrese de que el conjunto de datos que publica tenga unos términos de licencia que permitan el uso compartido y la descarga por parte de cualquier usuario. Las contribuciones a la Galería están sujetas a los [Términos de uso](https://azure.microsoft.com/support/legal/website-terms-of-use/)de Azure.

## <a name="frequently-asked-questions"></a>Preguntas más frecuentes
**¿Cuáles son los requisitos para enviar o editar una imagen para mi experimento?**

Las imágenes que envía con el experimento se usan para crear un icono del experimento para la contribución. Se recomienda que las imágenes tengan menos de 500 KB, con una relación de aspecto de 3:2 y una resolución de 960 &#215; 640.

**¿Qué ocurre con el conjunto de datos que he usado en el experimento? ¿También se publica en la Galería?**

Si el conjunto de datos forma parte del experimento y no se importa mediante el módulo Importar datos, se publica en la Galería como parte del experimento. Asegúrese de que el conjunto de datos que publica con el experimento tenga los términos de licencia adecuados. Los términos de licencia deben permitir que cualquier persona comparta y descargue los datos.

**Tengo un experimento que usa un módulo Importar datos para extraer datos desde Azure HDInsight o SQL Server. Usa mis credenciales para recuperar los datos. ¿Puedo publicar este tipo de experimento? ¿Cómo puedo estar seguro de que mis credenciales no se compartirán?**

De momento no puede publicar un experimento que use credenciales en la Galería.

**¿Cómo se puede especificar varias etiquetas?**

Después de escribir una etiqueta, para escribir otra, presione la tecla Tab.

**[Ir a la Galería](http://gallery.cortanaintelligence.com)**

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]
