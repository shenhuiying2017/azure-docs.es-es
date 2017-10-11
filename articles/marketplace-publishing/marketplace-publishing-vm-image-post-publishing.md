---
title: "Administración de la imagen de máquina virtual en Azure Marketplace | Microsoft Docs"
description: "Guía detallada sobre cómo administrar la imagen de máquina virtual en Azure Marketplace después de la publicación inicial"
services: Azure Marketplace
documentationcenter: 
author: HannibalSII
manager: hascipio
editor: 
ms.assetid: cc8648d4-59c2-4678-b47d-992300677537
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: Azure
ms.workload: na
ms.date: 08/03/2016
ms.author: hascipio;
ms.openlocfilehash: e1f90650e71345957c2d353774cb8bef62c1868b
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="post-production-guide-for-virtual-machine-offers-in-the-azure-marketplace"></a>Guía de postproducción para ofertas de máquina virtual en Azure Marketplace
En este artículo se explica cómo actualizar una oferta de máquina virtual activa en Azure Marketplace. Este artículo le conduce a través del proceso de adición de una o varias SKU a una oferta existente. También le indica cómo eliminar una oferta de máquina virtual activa o una SKU de Marketplace.

Después de que una oferta o SKU se haya almacenado provisionalmente en [Azure Portal](http://portal.azure.com), no podrá cambiar los siguientes cuadros de texto:

* **Offer Identifier** (Identificador de oferta): En el Portal de publicación, vaya a **Máquinas virtuales** y seleccione la oferta. A continuación, haga clic en **IMÁGENES DE LA MÁQUINA VIRTUAL** > **Offer Identifier** (Identificador de oferta).
* **SKU Identifier** (Identificador de SKU): En el Portal de publicación, vaya a **Máquinas virtuales** y seleccione la oferta. A continuación, haga clic en **SKU** > **Add a SKU** (Agregar una SKU).
* **Publisher Namespace** (Espacio de nombres de publicador): En el Portal de publicación vaya a **Máquinas virtuales** > **Walkthrough (Tutorial)** > **Tell Us About Your Company (Indíquenos la información de su empresa)** (esta opción se encuentra en el paso 2 sobre registro de la empresa) > **Publisher Namespace (Espacio de nombres de publicador)** > **Espacio de nombres**.

Después de que una oferta o SKU aparezca en [Marketplace](http://azure.microsoft.com/marketplace), no podrá cambiar los siguientes cuadros de texto:

* **Offer Identifier** (Identificador de oferta): En el Portal de publicación, vaya a **Máquinas virtuales** y seleccione la oferta. A continuación, haga clic en **IMÁGENES DE LA MÁQUINA VIRTUAL** > **Offer Identifier** (Identificador de oferta).
* **SKU Identifier** (Identificador de SKU): En el Portal de publicación, vaya a **Máquinas virtuales** y seleccione la oferta. A continuación, haga clic en **SKU** > **Add a SKU** (Agregar una SKU).
* **Publisher Namespace** (Espacio de nombres de publicador): En el Portal de publicación vaya a **Máquinas virtuales** > **Walkthrough (Tutorial)** > **Tell Us About Your Company (Indíquenos la información de su empresa)** (esta opción se encuentra en el paso 2 sobre registro de la empresa) > **Publisher Namespace (Espacio de nombres de publicador)** > **Espacio de nombres**.
* **Puertos**: En el Portal de publicación, vaya a **Máquinas virtuales** y seleccione la oferta. A continuación, haga clic en **IMÁGENES DE LA MÁQUINA VIRTUAL** > **Open Ports** (Abrir puertos).
* **Pricing Change of listed SKU(s) (Cambio de precios de las SKU activas)**
* **Billing Model Change of listed SKU(s) (Cambio del modelo de facturación de las SKU activas)**
* **Removal of billing regions of listed SKU(s)**
* **Changing the data disk count of listed SKU(s)**

## <a name="update-the-technical-details-of-a-sku"></a>Actualización de los detalles técnicos de una SKU
Para agregar una nueva versión a las SKU de la lista y volver a publicar la oferta, siga estos pasos:

1. Inicie sesión en el [Portal de publicación](https://publish.windowsazure.com).
2. Vaya a la pestaña **Máquinas virtuales** y seleccione la oferta.
3. En el menú de la izquierda, haga clic en la pestaña **IMÁGENES DE LA MÁQUINA VIRTUAL**.
4. En la sección **SKU**, busque la SKU que quiere actualizar.
5. Agregue un nuevo número de versión de la SKU y haga clic en el botón **+**. La nueva versión debe tener el formato X.Y.Z, donde X, Y y Z son números enteros. Los cambios de versión solo deben ser incrementales.
6. En el cuadro **OS VHD URL** (Dirección URL del VHD del sistema operativo), escriba el URI de la firma de acceso compartido creado para el VHD del sistema operativo y guarde los cambios.

   > [!IMPORTANT]
   > No se puede aumentar ni disminuir el número de discos de datos de una SKU activa. En este caso, tendrá que crear una nueva SKU. Para obtener instrucciones detalladas, consulte la sección [Incorporación de una nueva SKU en una oferta activa](#add-a-new-sku-under-a-listed-offer).
   >
   >
7. Vaya a la pestaña **PUBLICAR** y haga clic en **PUSH TO STAGING** (PASAR A ENSAYO). Para obtener instrucciones detalladas sobre cómo probar la oferta en el entorno de ensayo, consulte [Probar su oferta de VM para Marketplace](marketplace-publishing-vm-image-test-in-staging.md).
8. Después de haber probado su oferta en el entorno de ensayo, vaya a la pestaña **PUBLICAR** del Portal de publicación. Haga clic en **REQUEST APPROVAL TO PUSH TO PRODUCTION** (SOLICITAR APROBACIÓN PARA ENVIAR A PRODUCCIÓN) para volver a publicar la oferta en Marketplace.

    ![Imágenes de máquinas virtuales](media/marketplace-publishing-vm-image-post-publishing/img01_07.png)

## <a name="update-the-nontechnical-details-of-an-offer-or-a-sku"></a>Actualización de los detalles no técnicos de una oferta o una SKU
Puede actualizar los detalles no técnicos (marketing, legales, soporte técnico, categorías) de la oferta activa o la SKU en Marketplace.

### <a name="update-the-offer-description-and-logos"></a>Actualización de la descripción y los logotipos de la oferta
Para actualizar los detalles de la oferta y volver a publicar su oferta, siga estos pasos:

1. Inicie sesión en el [Portal de publicación](https://publish.windowsazure.com).
2. Vaya a la pestaña **Máquinas virtuales** y seleccione la oferta.
3. En el menú de la izquierda, haga clic en la pestaña **MARKETING**.
4. Haga clic en **Inglés (EU)**.
5. Haga clic en la pestaña **DETALLES**. En la sección **Descripción**, actualice el **TÍTULO**, **RESUMEN** y **RESUMEN LARGO** de la oferta y guarde los cambios.

   > [!NOTE]
   > Cuando actualice los detalles de la SKU, tenga en cuenta estas restricciones: 
   * No escriba texto duplicado en la descripción de la oferta y la descripción de la SKU.
   * No escriba texto duplicado en el resumen largo de la oferta y el título de la SKU. 
   * No escriba texto duplicado en el resumen de la oferta y el título de la SKU.
   >
   >

    ![Detalles](media/marketplace-publishing-vm-image-post-publishing/img02.1_05.png)
6. En la sección **LOGOTIPOS** de la pestaña **DETALLES**, actualice los logotipos. Asegúrese de que los logotipos sigan las [instrucciones de Azure Marketplace](marketplace-publishing-push-to-staging.md#step-1-provide-marketplace-marketing-content).

   > [!NOTE]
   > El icono de imagen prominente es opcional. Puede elegir no cargar un icono de imagen prominente. Sin embargo, después de cargar un icono de imagen prominente, no es necesario aprovisionarlo para eliminarlo del Portal de publicación. Siga las [directrices del icono de imagen prominente](marketplace-publishing-push-to-staging.md#step-1-provide-marketplace-marketing-content).
   >
   >
7. Vaya a la pestaña **PUBLICAR** y haga clic en **PUSH TO STAGING** (PASAR A ENSAYO). Para obtener instrucciones detalladas sobre cómo probar la oferta en el entorno de ensayo, consulte [Probar su oferta de VM para Marketplace](marketplace-publishing-vm-image-test-in-staging.md).
8. Después de haber probado su oferta en el entorno de ensayo, vaya a la pestaña **PUBLICAR** del Portal de publicación. Haga clic en **REQUEST APPROVAL TO PUSH TO PRODUCTION** (SOLICITAR APROBACIÓN PARA ENVIAR A PRODUCCIÓN) para volver a publicar la oferta en Marketplace.

    ![Logotipos](media/marketplace-publishing-vm-image-post-publishing/img02.1_08.png)

### <a name="update-the-sku-description"></a>Actualización de la descripción de la SKU
Para actualizar los detalles de la SKU y volver a publicar la oferta, siga estos pasos:

1. Inicie sesión en el [Portal de publicación](https://publish.windowsazure.com).
2. Vaya a la pestaña **Máquinas virtuales** y seleccione la oferta.
3. En el menú de la izquierda, haga clic en la pestaña **MARKETING**.
4. Haga clic en **Inglés (EU)**.
5. Haga clic en la pestaña **PLANES**. En la sección **SKU**, actualice el **TÍTULO**, **RESUMEN** y **DESCRIPCIÓN** de la SKU y guarde los cambios.

   > [!NOTE]
   > Cuando actualice los detalles de la SKU, tenga en cuenta estas restricciones: 
   * No escriba texto duplicado en la descripción de la oferta y la descripción de la SKU. 
   * No escriba texto duplicado en el resumen largo de la oferta y el título de la SKU. 
   * No escriba texto duplicado en el resumen de la oferta y el título de la SKU.
   >
   >
6. Vaya a la pestaña **PUBLICAR** y haga clic en **PUSH TO STAGING** (PASAR A ENSAYO). Para obtener instrucciones detalladas sobre cómo probar la oferta en el entorno de ensayo, consulte [Probar su oferta de VM para Marketplace](marketplace-publishing-vm-image-test-in-staging.md).
7. Después de haber probado su oferta en el entorno de ensayo, vaya a la pestaña **PUBLICAR** del Portal de publicación. Haga clic en **REQUEST APPROVAL TO PUSH TO PRODUCTION** (SOLICITAR APROBACIÓN PARA ENVIAR A PRODUCCIÓN) para volver a publicar la oferta en Marketplace.

    ![Planes](media/marketplace-publishing-vm-image-post-publishing/img02.2_07.png)

### <a name="change-existing-links-or-add-new-links"></a>Cambio de los vínculos existentes o incorporación de nuevos vínculos
Para cambiar los vínculos existentes o agregar nuevos vínculos y, a continuación, volver a publicar la oferta, siga estos pasos:

1. Inicie sesión en el [Portal de publicación](https://publish.windowsazure.com).
2. Vaya a la pestaña **Máquinas virtuales** y seleccione la oferta.
3. En el menú de la izquierda, haga clic en la pestaña **MARKETING**.
4. Haga clic en **Inglés (EU)**.
5. Haga clic en la pestaña **VÍNCULOS**.
6. Para agregar un vínculo nuevo, en la sección **Vínculos**, haga clic en **+ AGREGAR VÍNCULO**. En el cuadro de diálogo **Agregar vínculo**, escriba el **TÍTULO** y la **URL** del vínculo y guarde los cambios. Puede escribir cualquier vínculo que contenga información que resulte útil para los clientes.
7. Para actualizar o eliminar un vínculo existente, seleccione el vínculo y haga clic en el botón **Editar** o **Eliminar**, según corresponda.

   > [!NOTE]
   > Asegúrese de que los vínculos que ha especificado en esta sección funcionen correctamente, ya que estos vínculos se validarán durante el proceso de solicitud para producción.
   >
   >
8. Vaya a la pestaña **PUBLICAR** y haga clic en **PUSH TO STAGING** (PASAR A ENSAYO). Para obtener instrucciones detalladas sobre cómo probar la oferta en el entorno de ensayo, consulte [Probar su oferta de VM para Marketplace](marketplace-publishing-vm-image-test-in-staging.md).
9. Después de haber probado su oferta en el entorno de ensayo, vaya a la pestaña **PUBLICAR** del Portal de publicación. Haga clic en **REQUEST APPROVAL TO PUSH TO PRODUCTION** (SOLICITAR APROBACIÓN PARA ENVIAR A PRODUCCIÓN) para volver a publicar la oferta en Marketplace.

    ![Vínculos](media/marketplace-publishing-vm-image-post-publishing/img02.3_09-01.png)

    ![Agregar vínculo](media/marketplace-publishing-vm-image-post-publishing/img02.3-2.png)

### <a name="change-an-existing-sample-image-or-add-a-new-sample-image"></a>Cambio de una imagen de muestra existente o incorporación de una nueva
Para cambiar las imágenes de muestra existentes o agregar otras nuevas y luego volver a publicar la oferta, siga estos pasos:

> [!NOTE]
> Solo una imagen de muestra se muestra en [Azure Portal](https://portal.azure.com).
>
>

1. Inicie sesión en el [Portal de publicación](https://publish.windowsazure.com).
2. Vaya a la pestaña **Máquinas virtuales** y seleccione la oferta.
3. En el menú de la izquierda, haga clic en la pestaña **MARKETING**.
4. Haga clic en **Inglés (EU)**.
5. Haga clic en la pestaña **IMÁGENES DE MUESTRA**.
6. Para agregar una nueva imagen de muestra, en la sección **Imágenes de muestra**, haga clic en **UPLOAD A NEW IMAGE** (CARGAR UNA NUEVA IMAGEN) y guarde los cambios.

   > [!NOTE]
   > La inclusión de una imagen de muestra es opcional.
   >
7. Vaya a la pestaña **PUBLICAR** y haga clic en **PUSH TO STAGING** (PASAR A ENSAYO). Para obtener instrucciones detalladas sobre cómo probar la oferta en el entorno de ensayo, consulte [Probar su oferta de VM para Marketplace](marketplace-publishing-vm-image-test-in-staging.md).
8. Después de haber probado su oferta en el entorno de ensayo, vaya a la pestaña **PUBLICAR** del Portal de publicación. Haga clic en **REQUEST APPROVAL TO PUSH TO PRODUCTION** (SOLICITAR APROBACIÓN PARA ENVIAR A PRODUCCIÓN) para volver a publicar la oferta en Marketplace.

    ![Imágenes de muestra](media/marketplace-publishing-vm-image-post-publishing/img02.4_09.png)

### <a name="update-the-legal-content"></a>Actualización del contenido legal
Para actualizar el contenido legal y volver a publicar la oferta, siga estos pasos:

1. Inicie sesión en el [Portal de publicación](https://publish.windowsazure.com).
2. Vaya a la pestaña **Máquinas virtuales** y seleccione la oferta.
3. En el menú de la izquierda, haga clic en la pestaña **MARKETING**.
4. Haga clic en **Inglés (EU)**.
5. Haga clic en la pestaña **LEGAL**. En la sección **Legal**, actualice las directivas y las condiciones de uso. Escriba o pegue las directivas o los términos en el cuadro de texto **CONDICIONES DE USO** y guarde los cambios.
6. El límite de caracteres para las condiciones de uso legales es de 1 millón de caracteres.
7. Vaya a la pestaña **PUBLICAR** y haga clic en **PUSH TO STAGING** (PASAR A ENSAYO). Para obtener instrucciones detalladas sobre cómo probar la oferta en el entorno de ensayo, consulte [Probar su oferta de VM para Marketplace](marketplace-publishing-vm-image-test-in-staging.md).
8. Después de haber probado su oferta en el entorno de ensayo, vaya a la pestaña **PUBLICAR** del Portal de publicación. Haga clic en **REQUEST APPROVAL TO PUSH TO PRODUCTION** (SOLICITAR APROBACIÓN PARA ENVIAR A PRODUCCIÓN) para volver a publicar la oferta en Marketplace.

    ![Información legal](media/marketplace-publishing-vm-image-post-publishing/img02.5_08.png)

### <a name="update-the-support-information"></a>Actualización de la información de soporte técnico
Para actualizar la información de soporte técnico y volver a publicar la oferta, siga estos pasos:

1. Inicie sesión en el [Portal de publicación](https://publish.windowsazure.com).
2. Vaya a la pestaña **Máquinas virtuales** y seleccione la oferta.
3. En el menú de la izquierda, haga clic en la pestaña **SOPORTE TÉCNICO**.
4. En la sección de **contacto de ingeniería**, actualice los detalles de este. Los detalles de contacto solo se usan para comunicaciones internas entre asociados y Microsoft.
5. En la sección de **asistencia al cliente**, actualice los detalles de contacto de soporte técnico y la **DIRECCIÓN URL DE SOPORTE TÉCNICO**. Los detalles de contacto solo se usan para comunicaciones internas entre asociados y Microsoft.

   > [!NOTE]
   > Si quiere proporcionar solo soporte técnico por correo electrónico, facilite un número de teléfono ficticio en la sección de **asistencia al cliente**. En este caso, el correo electrónico que proporcionó se utiliza en su lugar.
   >
   >
6. Vaya a la pestaña **PUBLICAR** y haga clic en **PUSH TO STAGING** (PASAR A ENSAYO). Para obtener instrucciones detalladas sobre cómo probar la oferta en el entorno de ensayo, consulte [Probar su oferta de VM para Marketplace](marketplace-publishing-vm-image-test-in-staging.md).
7. Después de haber probado su oferta en el entorno de ensayo, vaya a la pestaña **PUBLICAR** del Portal de publicación. Haga clic en **REQUEST APPROVAL TO PUSH TO PRODUCTION** (SOLICITAR APROBACIÓN PARA ENVIAR A PRODUCCIÓN) para volver a publicar la oferta en Marketplace.

    ![Soporte técnico](media/marketplace-publishing-vm-image-post-publishing/img02.6_07.png)

### <a name="update-the-categories"></a>Actualización de las categorías
Para actualizar la sección de categorías de la oferta y volver a publicarla, siga estos pasos:

1. Inicie sesión en el [Portal de publicación](https://publish.windowsazure.com).
2. Vaya a la pestaña **Máquinas virtuales** y seleccione la oferta.
3. En el menú de la izquierda, haga clic en la pestaña **CATEGORÍAS**.
4. En la sección **Categorías**, actualice las categorías de la oferta y guarde los cambios. Puede elegir hasta cinco categorías de la galería de Azure Marketplace.
5. Vaya a la pestaña **PUBLICAR** y haga clic en **PUSH TO STAGING** (PASAR A ENSAYO). Para obtener instrucciones detalladas sobre cómo probar la oferta en el entorno de ensayo, consulte [Probar su oferta de VM para Marketplace](marketplace-publishing-vm-image-test-in-staging.md).
6. Después de haber probado su oferta en el entorno de ensayo, vaya a la pestaña **PUBLICAR** del Portal de publicación. Haga clic en **REQUEST APPROVAL TO PUSH TO PRODUCTION** (SOLICITAR APROBACIÓN PARA ENVIAR A PRODUCCIÓN) para volver a publicar la oferta en Marketplace.

    ![Categorías](media/marketplace-publishing-vm-image-post-publishing/img02.7_06.png)

## <a name="add-a-new-sku-under-a-listed-offer"></a>Incorporación de una nueva SKU en una oferta activa
Para agregar una nueva SKU en la oferta activa, siga estos pasos:

1. Inicie sesión en el [Portal de publicación](https://publish.windowsazure.com).
2. Vaya a la pestaña **Máquinas virtuales** y seleccione la oferta.
3. En el menú de la izquierda, haga clic en la pestaña **SKU**. A continuación, haga clic en **Add a SKU** (Agregar una SKU). 
4. En el cuadro de diálogo, escriba un **identificador de SKU** en minúsculas. Active la casilla del **modelo de facturación de Traiga su propia licencia (BYOL)** si quiere publicar la nueva SKU con este modelo. En caso contrario, desactive la casilla. Haga clic en la marca de verificación para crear una nueva SKU. Si no eligió el modelo de facturación de BYOL, el modelo de facturación se establecerá automáticamente en un modelo por horas. Si desea que la evaluación gratuita de 30 días use el modelo de facturación por horas, seleccione **Un mes** en **Is a free trial available?** (¿Hay una evaluación gratuita disponible?) De lo contrario, seleccione **No Trial** (Sin prueba). (**¿Hay una evaluación gratuita disponible?**  solo aparece si no ha seleccionado la opción BYOL durante la creación de la nueva SKU).

   > [!IMPORTANT]
   > La opción **Hide this SKU from the Marketplace because it should always be bought via a solution template** (Ocultar esta SKU en Marketplace porque siempre se debe comprar mediante una plantilla de solución) se debe marcar como **Yes** (Sí) *solo* si ha recibido aprobación para publicar una plantilla de solución. De lo contrario, esta opción siempre se debe marcar como **No**.
   >
   >
4. En el menú de la izquierda, haga clic **IMÁGENES DE LA MÁQUINA VIRTUAL** y busque la nueva SKU que ha creado.
5. Para configurar la nueva SKU, consulte [Obtención de la certificación para su imagen de máquina virtual](marketplace-publishing-vm-image-creation.md#5-obtain-certification-for-your-vm-image) para obtener instrucciones.
6. Para agregar material de marketing a la nueva SKU, consulte [Especificación de contenido de marketing de Marketplace](marketplace-publishing-push-to-staging.md#step-1-provide-marketplace-marketing-content).
7. Para agregar información sobre los precios de la nueva SKU, consulte [Establecimiento de precios](marketplace-publishing-push-to-staging.md#step-2-set-your-prices).
8. Vaya a la pestaña **PUBLICAR** y haga clic en **PUSH TO STAGING** (PASAR A ENSAYO). Para obtener instrucciones detalladas sobre cómo probar la oferta en el entorno de ensayo, consulte [Probar su oferta de VM para Marketplace](marketplace-publishing-vm-image-test-in-staging.md).
9. Después de haber probado su oferta en el entorno de ensayo, vaya a la pestaña **PUBLICAR** del Portal de publicación. Haga clic en **REQUEST APPROVAL TO PUSH TO PRODUCTION** (SOLICITAR APROBACIÓN PARA ENVIAR A PRODUCCIÓN) para volver a publicar la oferta en Marketplace.

    ![SKU](media/marketplace-publishing-vm-image-post-publishing/img03_09-01.png)

    ![Agregar una SKU](media/marketplace-publishing-vm-image-post-publishing/img03_09-02.png)

## <a name="change-the-data-disk-count-for-a-listed-sku"></a>Cambio del número de discos de datos para una SKU activa
No se puede aumentar ni disminuir el número de discos de datos de una SKU activa. En este caso, tendrá que crear una nueva SKU. Para obtener instrucciones detalladas, consulte la sección [Incorporación de una nueva SKU en una oferta activa](#add-a-new-sku-under-a-listed-offer).

## <a name="delete-a-listed-offer-from-the-marketplace"></a>Eliminación de una oferta activa de Marketplace
En el caso de una solicitud para quitar una oferta activa, son varios los aspectos que se deben tener en cuenta. Siga estos pasos si quiere recibir ayuda del equipo de soporte técnico para quitar una oferta activa de Azure Marketplace:

1. Genere una incidencia de soporte técnico en la página [Crear un incidente](https://support.microsoft.com/en-us/getsupport?wf=0&tenant=ClassicCommercial&oaspworkflow=start_1.0.0.0&locale=en-us&supportregion=en-us&pesid=15635&ccsid=635993707583706681).

2. Como **tipo de problema**, seleccione **Administrar ofertas**, y como **categoría**, **Modificar una oferta o SKU ya en producción**.
3. Envíe la solicitud.

El equipo de soporte técnico le guiará en el proceso de eliminación de la oferta o la SKU.

> [!NOTE]
> Siempre se puede eliminar la oferta mientras está en estado de borrador (es decir, que no está en ensayo ni en producción). En la pestaña **HISTORIAL**, haga clic en **DESCARTAR BORRADOR**.
>
>

## <a name="delete-a-listed-sku-from-the-marketplace"></a>Eliminación de una SKU activa de Marketplace
Para eliminar una SKU activa de Marketplace, siga estos pasos:

1. Inicie sesión en el [Portal de publicación](https://publish.windowsazure.com).

2. Vaya a la pestaña **Máquinas virtuales** y seleccione la oferta.
3. En el panel de la izquierda, haga clic en la pestaña **SKU**.
4. Seleccione la SKU que quiera eliminar y haga clic en el botón **Eliminar**.
5. Vaya a la pestaña **PUBLICAR** del Portal de publicación. Haga clic en **REQUEST APPROVAL TO PUSH TO PRODUCTION** (SOLICITAR APROBACIÓN PARA ENVIAR A PRODUCCIÓN) para volver a publicar la oferta en Marketplace.
6. Después de que la oferta se haya vuelto a publicar en Marketplace, se eliminará la SKU de Marketplace y de Azure Portal.

## <a name="delete-the-current-version-of-a-listed-sku-from-the-marketplace"></a>Eliminación de la versión actual de una SKU activa de Marketplace
Para eliminar la versión actual de una SKU activa de Azure Marketplace, siga estos pasos: 

1. Inicie sesión en el [Portal de publicación](https://publish.windowsazure.com).

2. Vaya a la pestaña **Máquinas virtuales** y seleccione la oferta.
3. En el menú de la izquierda, haga clic en la pestaña **IMÁGENES DE LA MÁQUINA VIRTUAL**.
4. Seleccione la SKU cuya versión actual quiera eliminar y haga clic en el botón **Eliminar**.
5. Vaya a la pestaña **PUBLICAR** del Portal de publicación. Haga clic en **REQUEST APPROVAL TO PUSH TO PRODUCTION** (SOLICITAR APROBACIÓN PARA ENVIAR A PRODUCCIÓN) para volver a publicar la oferta en Marketplace.
6. Después de que la oferta se haya vuelto a publicar en Marketplace, se eliminará la versión actual de la SKU activa de Marketplace y de Azure Portal. La SKU se revertirá a la versión anterior.

## <a name="revert-the-listing-price-to-production-values"></a>Reversión del precio de lista a los valores de producción
Para revertir el precio de lista a los valores de producción, siga estos pasos:

1. Inicie sesión en el [Portal de publicación](https://publish.windowsazure.com).
2. Vaya a la pestaña **Máquinas virtuales** y seleccione la oferta.
3. En el menú de la izquierda, haga clic en la pestaña **PRECIOS**.
4. Seleccione una región cuyos precios quiera restablecer.

    ![Regiones de precios](media/marketplace-publishing-vm-image-post-publishing/img08-04.png)
5. En el caso de las SKU con un modelo de facturación por horas, restablezca los precios de todos los núcleos, ya que tienen el estado de producción en la región seleccionada. Para las SKU con el modelo de facturación BYOL, haga que la SKU esté disponible en la región activando la casilla de esta SKU en la sección **EXTERNALLY-LICENSED (BYOL) SKU AVAILABILITY** (DISPONIBILIDAD DE SKU CON LICENCIA EXTERNA [BYOL]).

    ![Modelos de precios](media/marketplace-publishing-vm-image-post-publishing/img08-05.png)
6. Haga clic en **AUTOPRICE OTHER MARKETS BASED ON PRICES IN UNITED STATES**(APLICAR AUTOMÁTICAMENTE LOS PRECIOS PARA ESTADOS UNIDOS A OTROS MERCADOS).

   > [!NOTE]
   > La etiqueta del botón puede variar en función de la región que haya seleccionado. Dado que hemos seleccionado Estados Unidos, la etiqueta del botón es **AUTOPRICE OTHER MARKETS BASED ON PRICES IN UNITED STATES** (APLICAR AUTOMÁTICAMENTE LOS PRECIOS PARA ESTADOS UNIDOS A OTROS MERCADOS).
   >
   >

    ![Aplicar automáticamente los precios](media/marketplace-publishing-vm-image-post-publishing/img08-06.png)
7. En la página 1 del asistente de aplicación automática de los precios, elija el mercado base y haga clic en el botón de **flecha**.

    ![Mercado base](media/marketplace-publishing-vm-image-post-publishing/img08-07.png)
8. En la página 2, elija los planes y medidores (núcleos) de servicio y haga clic en el botón de **flecha**.

    ![Planes y medidores de servicio](media/marketplace-publishing-vm-image-post-publishing/img08-08.png)
9. En la página 3, haga clic en **Toggle All** (Alternar todas) para seleccionar todas las regiones. O bien, puede seleccionar manualmente las casillas de regiones específicas. Haga clic en el botón de **flecha**.

    ![Elección de mercados](media/marketplace-publishing-vm-image-post-publishing/img08-09.png)
10. En la página 4, revise los tipos de cambio y haga clic en **Finalizar**. El asistente restablecerá los precios según lo que haya seleccionado.

11. En la pestaña **PRECIOS**, haga clic en **VIEW SUMMARY AND CHANGES** (VER RESUMEN Y CAMBIOS).
    En **View Version** (Ver versión), seleccione **Borrador**y en **Comparar con**, seleccione **Producción**. Si no ve ninguna diferencia de precios, significa que los precios se han revertido correctamente a los valores de producción.

    ![Ver resumen y cambios](media/marketplace-publishing-vm-image-post-publishing/img08-11.png)
12. Vaya a la pestaña **PUBLICAR** y haga clic en **PUSH TO STAGING** (PASAR A ENSAYO). Para obtener instrucciones detalladas sobre cómo probar la oferta en el entorno de ensayo, consulte [Probar su oferta de VM para Marketplace](marketplace-publishing-vm-image-test-in-staging.md).
13. Después de haber probado su oferta en el entorno de ensayo, vaya a la pestaña **PUBLICAR** del Portal de publicación. Haga clic en **REQUEST APPROVAL TO PUSH TO PRODUCTION** (SOLICITAR APROBACIÓN PARA ENVIAR A PRODUCCIÓN) para volver a publicar la oferta en Marketplace.

## <a name="revert-the-billing-model-to-production-values"></a>Reversión del modelo de facturación a los valores de producción
Para revertir el modelo de facturación a los valores de producción, siga estos pasos:

1. Inicie sesión en el [Portal de publicación](https://publish.windowsazure.com).

2. Vaya a la pestaña **Máquinas virtuales** y seleccione la oferta.
3. En el menú de la izquierda, haga clic en la pestaña **SKU**.
4. Haga clic en el botón **Editar** para revertir el modelo de facturación. En la ventana que se abre, active o desactive la casilla **Billing and licensing is done externally from Azure (aka Bring Your Own License)** (La facturación y las licencias no se administran en Azure [Traiga su propia licencia]).

    ![Editar facturación](media/marketplace-publishing-vm-image-post-publishing/img09-04.png)
5. Siga los pasos descritos en la sección "Reversión del precio de lista a los valores de producción" de este artículo.
6. Vaya a la pestaña **PUBLICAR** y haga clic en **PUSH TO STAGING** (PASAR A ENSAYO). Para obtener instrucciones detalladas sobre cómo probar la oferta en el entorno de ensayo, consulte [Probar su oferta de VM para Marketplace](marketplace-publishing-vm-image-test-in-staging.md).
7. Después de haber probado su oferta en el entorno de ensayo, vaya a la pestaña **PUBLICAR** del Portal de publicación. Haga clic en **REQUEST APPROVAL TO PUSH TO PRODUCTION** (SOLICITAR APROBACIÓN PARA ENVIAR A PRODUCCIÓN) para volver a publicar la oferta en Marketplace.

## <a name="revert-the-visibility-setting-of-a-listed-sku-to-the-production-value"></a>Reversión de la configuración de visibilidad de una SKU activa al valor de producción
Para revertir la configuración de visibilidad de una SKU activa al valor de producción, siga estos pasos:

1. Inicie sesión en el [Portal de publicación](https://publish.windowsazure.com).

2. Vaya a la pestaña **Máquinas virtuales** y seleccione la oferta.
3. En el menú de la izquierda, haga clic en la pestaña **SKU**.
4. Seleccione la SKU y revierta su configuración de visibilidad al valor de producción.

    ![Visibilidad](media/marketplace-publishing-vm-image-post-publishing/img10-04.png)
5. Una vez que haya terminado con los cambios, haga clic en **REQUEST APPROVAL TO PUSH TO PRODUCTION** (SOLICITAR APROBACIÓN PARA ENVIAR A PRODUCCIÓN) para volver a publicar la oferta en Marketplace.

## <a name="see-also"></a>Consulte también
* [Introducción: Publicación de una oferta en Azure Marketplace](marketplace-publishing-getting-started.md)
* [Descripción de informes de pago](marketplace-publishing-report-payout.md)
* [Cambio del incentivo de revendedores para proveedores de soluciones en la nube](marketplace-publishing-csp-incentive.md)
* [Solución de problemas comunes de publicación en Marketplace](marketplace-publishing-support-common-issues.md)
* [Obtención de soporte técnico como publicador](marketplace-publishing-get-publisher-support.md)
* [Creación de una imagen de máquina virtual local](marketplace-publishing-vm-image-creation-on-premise.md)
* [Creación de una máquina virtual que ejecuta Windows en el portal de vista previa de Azure](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
