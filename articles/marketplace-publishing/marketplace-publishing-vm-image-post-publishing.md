---
title: "Administración de la imagen de máquina virtual en Azure Marketplace | Microsoft Docs"
description: "Guía detallada sobre cómo administrar la imagen de máquina virtual en Azure Marketplace después de la publicación inicial."
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
translationtype: Human Translation
ms.sourcegitcommit: 5919c477502767a32c535ace4ae4e9dffae4f44b
ms.openlocfilehash: ed2921750f93f344a4c3dbef31d9f523dedc0aae


---
# <a name="post-production-guide-for-virtual-machine-offers-in-the-azure-marketplace"></a>Guía de postproducción para ofertas de máquina virtual en Azure Marketplace
En este artículo se explica cómo actualizar una oferta de máquina virtual activa en Azure Marketplace. También le guía en el proceso de agregar una o varias SKU a una oferta existente y quitar una oferta de máquina virtual activa o una SKU de Azure Marketplace.

Cuando una oferta o SKU se almacene provisionalmente en el [Portal de Azure](http://portal.azure.com), no podrá cambiar los campos que se indican a continuación:

* **Offer Identifier** (Identificador de oferta): Portal de publicación -> Máquinas virtuales -> Select your Offer (Seleccionar oferta) -> pestaña Imágenes de VM -> Offer Identifier (Identificador de oferta)
* **SKU Identifier** (Identificador de SKU): Portal de publicación -> Máquinas virtuales -> Select your Offer (Seleccionar oferta) -> pestaña SKUs (SKU) -> Offer Identifier (Identificador de oferta)
* **Publisher Namespace** (Espacio de nombres de publicador): Portal de publicación -> Máquinas virtuales -> pestaña Walkthrough (Tutorial) -> Tell Us About Your Company (Indíquenos la información de su empresa) (esta opción se encuentra en el paso 2 sobre registro de la empresa) -> Publisher Namespace (Espacio de nombres de publicador) -> Espacio de nombres

Cuando una oferta o SKU se muestre en [Azure Marketplace](http://azure.microsoft.com/marketplace), no podrá cambiar los campos que se indican a continuación:

* **Offer Identifier** (Identificador de oferta): Portal de publicación -> Máquinas virtuales -> Select your Offer (Seleccionar oferta) -> pestaña Imágenes de VM -> Offer Identifier (Identificador de oferta)
* **SKU Identifier** (Identificador de SKU): Portal de publicación -> Máquinas virtuales -> Select your Offer (Seleccionar oferta) -> pestaña SKUs (SKU) -> Offer Identifier (Identificador de oferta)
* **Publisher Namespace** (Espacio de nombres de publicador): Portal de publicación -> Máquinas virtuales -> pestaña Walkthrough (Tutorial) -> Tell Us About Your Company (Indíquenos la información de su empresa) (esta opción se encuentra en el paso 2 sobre registro) -> Publisher Namespace (Espacio de nombres de publicador) -> Espacio de nombres
* **Puertos**: Portal de publicación -> Máquinas virtuales -> Select your Offer (Seleccionar oferta) -> pestaña Imágenes de VM -> Open Ports (Abrir puertos)
* **Pricing Change of listed SKU(s) (Cambio de precios de las SKU activas)**
* **Billing Model Change of listed SKU(s)**
* **Removal of billing regions of listed SKU(s)**
* **Changing the data disk count of listed SKU(s)**

## <a name="1-how-to-update-the-technical-details-of-a-sku"></a>1. Actualización de los detalles técnicos de una SKU
Puede agregar una nueva versión a la SKU activa y volver a publicar la oferta siguiendo los pasos que se indican a continuación:

1. Inicie sesión en el [Portal de publicación](https://publish.windowsazure.com).
2. Vaya a la pestaña **MÁQUINAS VIRTUALES** y seleccione la oferta.
3. En el menú del lado izquierdo, haga clic en la pestaña **IMÁGENES DE VM** .
4. En la sección **SKUs** de la pestaña **IMÁGENES DE VM** , busque la SKU que quiere actualizar.
5. Después, agregue un nuevo número de versión de la SKU y haga clic en el botón **+** . La nueva versión debe tener el formato X.Y.Z, donde X, Y, Z son números enteros. Los cambios de versión solo deben ser incrementales.
6. En el cuadro **OS VHD URL** (Dirección URL del VHD del sistema operativo), escriba el URI de la firma de acceso compartido creado para el VHD del sistema operativo y guarde los cambios.
   
   > [!IMPORTANT]
   > No se puede aumentar ni disminuir el número de discos de datos de una SKU activa. En este caso, tendrá que crear una nueva SKU. Vea la sección [3. Incorporación de una nueva SKU en una oferta activa](#3-how-to-add-a-new-sku-under-a-live-offer) para obtener instrucciones detalladas.
   > 
   > 
7. Después de realizar los cambios, vaya a la pestaña **PUBLICAR** y haga clic en el botón **PUSH TO STAGING** (Pasar a ensayo). Para obtener instrucciones detalladas sobre cómo probar la oferta en el entorno de ensayo, haga clic en este [vínculo](marketplace-publishing-vm-image-test-in-staging.md)
8. Cuando haya probado la oferta en el entorno de ensayo, vaya a la pestaña **PUBLICAR** del Portal de publicación y haga clic en el botón **REQUEST APPROVAL TO PUSH TO PRODUCTION** (SOLICITAR APROBACIÓN PARA INSERTAR EN PRODUCCIÓN) para volver a publicarla en Azure Marketplace.
   
    ![dibujo](media/marketplace-publishing-vm-image-post-publishing/img01_07.png)

## <a name="2-how-to-update-the-non-technical-details-of-an-offer-or-a-sku"></a>2. Actualización de los detalles no técnicos de una oferta o una SKU
Puede actualizar los detalles no técnicos (marketing, legales, soporte técnico, categorías) de la oferta activa o la SKU en Azure Marketplace.

### <a name="21-update-the-offer-description-and-logos"></a>2.1 Actualización de la descripción y los logotipos de la oferta
Puede actualizar los detalles de la oferta y volver a publicarla mediante estos pasos:

1. Inicie sesión en el [Portal de publicación](https://publish.windowsazure.com).
2. Vaya a la pestaña **MÁQUINAS VIRTUALES** y seleccione la oferta.
3. En el menú del lado izquierdo, haga clic en la pestaña **MARKETING** .
4. Haga clic en el botón **Inglés (EU)** .
5. En el menú del lado izquierdo, haga clic en la pestaña **DETALLES** . En la sección *DESCRIPCIÓN* de la pestaña **DETALLES** , puede actualizar el título, el resumen o el resumen largo de la oferta y guardar los cambios.
   
   > [!NOTE]
   > Cuando actualice los detalles de la SKU, tenga en cuenta lo siguiente.
   > **No escriba texto duplicado en la descripción de la oferta y la descripción de la SKU. No escriba texto duplicado en el resumen largo de la oferta y el título de la SKU. No escriba texto duplicado en el resumen de la oferta y el título de la SKU.**
   > 
   > 
   
    ![dibujo](media/marketplace-publishing-vm-image-post-publishing/img02.1_05.png)
6. En la sección *LOGOTIPOS* de la pestaña **DETALLES** , puede actualizar los logotipos. Sin embargo, asegúrese de que los logotipos sigan las [instrucciones de Azure Marketplace](marketplace-publishing-push-to-staging.md#step-1-provide-marketplace-marketing-content) (vea la sección Paso 1: Especificación de contenido de marketing de Marketplace -> Detalles -> Instrucciones del logotipo de Azure Marketplace).
   
   > [!NOTE]
   > El icono de imagen prominente es opcional. Puede elegir no cargar un icono de imagen prominente. Sin embargo, cuando se carga el icono de imagen prominente, no es necesario aprovisionarlo para eliminarlo del Portal de publicación. En ese caso, debe seguir las [instrucciones para la imagen prominente](marketplace-publishing-push-to-staging.md#step-1-provide-marketplace-marketing-content) (vea la sección Paso 1: Especificación de contenido de marketing de Marketplace -> Detalles -> Instrucciones adicionales para el icono de logotipo de imagen prominente).
   > 
   > 
7. Después de realizar los cambios, vaya a la pestaña **PUBLICAR** y haga clic en el botón **PUSH TO STAGING** (Pasar a ensayo). Para obtener instrucciones detalladas sobre cómo probar la oferta en el entorno de ensayo, haga clic en este [vínculo](marketplace-publishing-vm-image-test-in-staging.md).
8. Cuando haya probado la oferta en el entorno de ensayo, vaya a la pestaña **PUBLICAR** del Portal de publicación y haga clic en el botón **REQUEST APPROVAL TO PUSH TO PRODUCTION** (SOLICITAR APROBACIÓN PARA INSERTAR EN PRODUCCIÓN) para volver a publicarla en Azure Marketplace.
   
    ![dibujo](media/marketplace-publishing-vm-image-post-publishing/img02.1_08.png)

### <a name="22-update-the-sku-description"></a>2.2. Actualización de la descripción de la SKU
Puede actualizar los detalles de la SKU y volver a publicar su oferta siguiendo estos pasos:

1. Inicie sesión en el [Portal de publicación](https://publish.windowsazure.com)
2. Vaya a la pestaña **MÁQUINAS VIRTUALES** y seleccione la oferta.
3. En el menú del lado izquierdo, haga clic en la pestaña **MARKETING** .
4. Haga clic en el botón **Inglés (EU)** .
5. En el menú del lado izquierdo, haga clic en la pestaña **PLANES** . En la sección *SKUs* (SKU) de la pestaña **PLANES** , puede actualizar el título, el resumen y la descripción de la SKU y guardar los cambios.
   
   > [!NOTE]
   > Cuando actualice los detalles de la SKU, tenga en cuenta lo siguiente. **No escriba texto duplicado en la descripción de la oferta y la descripción de la SKU. No escriba texto duplicado en el resumen largo de la oferta y el título de la SKU. No escriba texto duplicado en el resumen de la oferta y el título de la SKU.**
   > 
   > 
6. Después de realizar los cambios, vaya a la pestaña **PUBLICAR** y haga clic en el botón **PUSH TO STAGING** (Pasar a ensayo). Para obtener instrucciones detalladas sobre cómo probar la oferta en el entorno de ensayo, consulte este vínculo.
7. Cuando haya probado la oferta en el entorno de ensayo, vaya a la pestaña **PUBLICAR** del Portal de publicación y haga clic en el botón **REQUEST APPROVAL TO PUSH TO PRODUCTION** (SOLICITAR APROBACIÓN PARA INSERTAR EN PRODUCCIÓN) para volver a publicarla en Azure Marketplace.
   
    ![dibujo](media/marketplace-publishing-vm-image-post-publishing/img02.2_07.png)

### <a name="23-change-the-existing-links-or-add-new-links"></a>2.3 Cambio de los vínculos existentes o incorporación de nuevos vínculos
Puede cambiar los vínculos existentes o agregar otros nuevos y luego volver a publicar la oferta siguiendo los pasos que se indican a continuación:

1. Inicie sesión en el [Portal de publicación](https://publish.windowsazure.com)
2. Vaya a la pestaña **MÁQUINAS VIRTUALES** y seleccione la oferta.
3. En el menú del lado izquierdo, haga clic en la pestaña **MARKETING** .
4. Haga clic en el botón **Inglés (EU)** .
5. En el menú del lado izquierdo, haga clic en la pestaña **VÍNCULOS** .
6. Si quiere agregar un nuevo vínculo, en la sección *Vínculos* , haga clic en el botón **AGREGAR VÍNCULO** . Se abre el cuadro de diálogo *Agregar vínculo* . En este cuadro de diálogo, puede agregar los campos de título y dirección URL del vínculo y guardar los cambios. Puede escribir cualquier vínculo que contenga información que pueda ser de ayuda para los clientes.
7. Si quiere actualizar o eliminar un vínculo existente, seleccione el vínculo adecuado y haga clic en el botón de edición o eliminación, según corresponda.
   
   > [!NOTE]
   > Asegúrese de que los vínculos que ha especificado en esta sección funcionen correctamente, dado que dichos vínculos se validarán durante el proceso de solicitud para producción.
   > 
   > 
8. Después de realizar los cambios, vaya a la pestaña **PUBLICAR** y haga clic en el botón **PUSH TO STAGING** (Pasar a ensayo). Para obtener instrucciones detalladas sobre cómo probar la oferta en el entorno de ensayo, haga clic en este [vínculo](marketplace-publishing-vm-image-test-in-staging.md).
9. Cuando haya probado la oferta en el entorno de ensayo, vaya a la pestaña **PUBLICAR** del Portal de publicación y haga clic en el botón **REQUEST APPROVAL TO PUSH TO PRODUCTION** (SOLICITAR APROBACIÓN PARA INSERTAR EN PRODUCCIÓN) para volver a publicarla en Azure Marketplace.
   
    ![dibujo](media/marketplace-publishing-vm-image-post-publishing/img02.3_09-01.png)
   
    ![dibujo](media/marketplace-publishing-vm-image-post-publishing/img02.3-2.png)

### <a name="24-change-an-existing-sample-image-or-add-a-new-sample-image"></a>2.4 Cambio de una imagen de ejemplo existente o incorporación de una nueva imagen de ejemplo
Puede cambiar las imágenes de ejemplo existentes o agregar otras nuevas y luego volver a publicar la oferta siguiendo los pasos que se indican a continuación:

> [!NOTE]
> Solo una imagen de ejemplo se muestra en [https://portal.azure.com](https://portal.azure.com).
> 
> 

1. Inicie sesión en el [Portal de publicación](https://publish.windowsazure.com)
2. Vaya a la pestaña **MÁQUINAS VIRTUALES** y seleccione la oferta.
3. En el menú del lado izquierdo, haga clic en la pestaña **MARKETING** .
4. Haga clic en el botón **Inglés (EU)** .
5. En el menú del lado izquierdo, haga clic en la pestaña **SAMPLE IMAGES** (IMÁGENES DE EJEMPLO).
6. Si quiere agregar una nueva imagen de ejemplo, en la sección *Sample Images* (Imágenes de ejemplo), haga clic en el botón **UPLOAD A NEW IMAGE** (CARGAR UNA NUEVA IMAGEN) y, luego, guarde los cambios.
   
   > [!NOTE]
   > La inclusión de una imagen de ejemplo es un paso opcional.
   > 
   > 
7. Si quiere actualizar o eliminar una imagen de ejemplo existente, busque la que considere adecuada y haga clic en el botón **REEMPLAZAR IMAGEN** o en el botón de eliminación, según corresponda.
8. Después de realizar los cambios, vaya a la pestaña **PUBLICAR** y haga clic en el botón **PUSH TO STAGING** (Pasar a ensayo). Para obtener instrucciones detalladas sobre cómo probar la oferta en el entorno de ensayo, haga clic en este [vínculo](marketplace-publishing-vm-image-test-in-staging.md).
9. Cuando haya probado la oferta en el entorno de ensayo, vaya a la pestaña **PUBLICAR** del Portal de publicación y haga clic en el botón **REQUEST APPROVAL TO PUSH TO PRODUCTION** (SOLICITAR APROBACIÓN PARA INSERTAR EN PRODUCCIÓN) para volver a publicarla en Azure Marketplace.
   
    ![dibujo](media/marketplace-publishing-vm-image-post-publishing/img02.4_09.png)

### <a name="25-update-the-legal-content"></a>2.5 Actualización del contenido legal
Puede actualizar el contenido legal y volver a publicar su oferta siguiendo estos pasos:

1. Inicie sesión en el [Portal de publicación](https://publish.windowsazure.com)
2. Vaya a la pestaña **MÁQUINAS VIRTUALES** y seleccione la oferta.
3. En el menú del lado izquierdo, haga clic en la pestaña **MARKETING** .
4. Haga clic en el botón **Inglés (EU)** .
5. En el menú del lado izquierdo, haga clic en la pestaña **LEGAL** . En la sección *Legal* , puede actualizar las directivas y los términos de uso. Escriba o pegue las directivas o los términos en el cuadro de texto *Términos de uso* y guarde los cambios.
6. El límite de caracteres para las condiciones de uso legales es de 1.000.000 de caracteres.
7. Después de realizar los cambios, vaya a la pestaña **PUBLICAR** y haga clic en el botón **PUSH TO STAGING** (Pasar a ensayo). Para obtener instrucciones detalladas sobre cómo probar la oferta en el entorno de ensayo, haga clic en este [vínculo](marketplace-publishing-vm-image-test-in-staging.md)
8. Cuando haya probado la oferta en el entorno de ensayo, vaya a la pestaña **PUBLICAR** del Portal de publicación y haga clic en el botón **REQUEST APPROVAL TO PUSH TO PRODUCTION** (SOLICITAR APROBACIÓN PARA INSERTAR EN PRODUCCIÓN) para volver a publicarla en Azure Marketplace.
   
    ![dibujo](media/marketplace-publishing-vm-image-post-publishing/img02.5_08.png)

### <a name="26-update-the-support-information"></a>2.6 Actualización de la información de soporte técnico
Puede actualizar la información de soporte técnico y volver a publicar su oferta siguiendo estos pasos:

1. Inicie sesión en el [Portal de publicación](https://publish.windowsazure.com)
2. Vaya a la pestaña **MÁQUINAS VIRTUALES** y seleccione la oferta.
3. En el menú del lado izquierdo, haga clic en la pestaña **SOPORTE TÉCNICO** .
4. En la sección *Engineering Contact* (Contacto de ingeniería) de la pestaña **SOPORTE TÉCNICO** , puede actualizar los detalles de contacto. Los detalles de contacto solo se usan para comunicaciones internas entre asociados y Microsoft.
5. En la sección *Asistencia al cliente* de la pestaña **SOPORTE TÉCNICO**, puede actualizar los detalles de contacto de soporte técnico, como **el nombre, la dirección de correo electrónico, el teléfono** y la **dirección URL de soporte técnico**. Los detalles de contacto solo se usan para comunicaciones internas entre asociados y Microsoft.
   
   > [!NOTE]
   > Si quiere proporcionar solo soporte técnico por correo electrónico, facilite un número de teléfono ficticio en la sección **Soporte al cliente** . En este caso, en su lugar se usará la dirección de correo proporcionada.
   > 
   > 
6. Después de realizar los cambios, vaya a la pestaña **PUBLICAR** y haga clic en el botón **PUSH TO STAGING** (Pasar a ensayo). Para obtener instrucciones detalladas sobre cómo probar la oferta en el entorno de ensayo, haga clic en este [vínculo](marketplace-publishing-vm-image-test-in-staging.md)
7. Cuando haya probado la oferta en el entorno de ensayo, vaya a la pestaña **PUBLICAR** del Portal de publicación y haga clic en el botón **REQUEST APPROVAL TO PUSH TO PRODUCTION** (SOLICITAR APROBACIÓN PARA INSERTAR EN PRODUCCIÓN) para volver a publicarla en Azure Marketplace.
   
    ![dibujo](media/marketplace-publishing-vm-image-post-publishing/img02.6_07.png)

### <a name="27-update-the-categories"></a>2.7 Actualización de las categorías
Puede actualizar la sección de categorías de la oferta y volver a publicarla mediante estos pasos:

1. Inicie sesión en el [Portal de publicación](https://publish.windowsazure.com)
2. Vaya a la pestaña **MÁQUINAS VIRTUALES** y seleccione la oferta.
3. En el menú del lado izquierdo, haga clic en la pestaña **CATEGORÍAS** .
4. En la sección *Categorías* , puede actualizar las categorías de la oferta y guardar los cambios. Puede seleccionar hasta cinco categorías de la galería de Azure Marketplace.
5. Después de realizar los cambios, vaya a la pestaña **PUBLICAR** y haga clic en el botón **PUSH TO STAGING** (Pasar a ensayo). Para obtener instrucciones detalladas sobre cómo probar la oferta en el entorno de ensayo, haga clic en este [vínculo](marketplace-publishing-vm-image-test-in-staging.md)
6. Cuando haya probado la oferta en el entorno de ensayo, vaya a la pestaña **PUBLICAR** del Portal de publicación y haga clic en el botón **REQUEST APPROVAL TO PUSH TO PRODUCTION** (SOLICITAR APROBACIÓN PARA INSERTAR EN PRODUCCIÓN) para volver a publicarla en Azure Marketplace.
   
    ![dibujo](media/marketplace-publishing-vm-image-post-publishing/img02.7_06.png)

## <a name="3-how-to-add-a-new-sku-under-a-listed-offer"></a>3. Incorporación de una nueva SKU en una oferta activa
Puede agregar una nueva SKU en la oferta activa mediante los pasos que se describen a continuación:

1. Inicie sesión en el [Portal de publicación](https://publish.windowsazure.com).
2. Vaya a la pestaña **MÁQUINAS VIRTUALES** y seleccione la oferta.
3. En el menú del lado izquierdo, haga clic en la pestaña **SKUs** . Después, haga clic en el botón **ADD A SKU** (AGREGAR UNA SKU).  Se abrirá un nuevo cuadro de diálogo. Escriba un identificador de SKU en minúsculas. Active la casilla para traer su propio modelo de facturación (BYOL) si quiere publicar la nueva SKU con él. De lo contrario, desactive esta casilla. A continuación, haga clic en la marca de verificación del cuadro de diálogo para crear una nueva SKU. Si no elige BYOL como modelo de facturación para la nueva SKU, este se establecerá automáticamente en Hourly (Cada hora). Si quiere habilitar la evaluación gratuita de 30 días para el modelo de facturación Hourly (Cada hora), haga clic en la opción "One Month" (Un mes) en "Is a free a free trial available?" (¿Hay disponible una evaluación gratuita?). De lo contrario, seleccione “NO TRIAL” (SIN PRUEBA). [Nota: La opción “Is a free trial available?” (¿Hay una evaluación gratuita disponible?) solo se muestra si NO ha seleccionado BYOL en el cuadro de diálogo al crear la nueva SKU].
   
   > [!IMPORTANT]
   > La opción "Hide this SKU from the Marketplace because it should always be bought via a solution template" (Ocultar esta SKU en Marketplace porque siempre se debe comprar mediante una plantilla de solución) se debe marcar como "YES" (SÍ) SOLO si ha recibido aprobación para publicar una oferta de plantilla de solución en Azure Marketplace. De lo contrario, esta opción siempre se debe marcar como "NO".
   > 
   > 
4. Ahora, en el menú del lado izquierdo, haga clic en la pestaña **IMÁGENES DE VM** y descubra la nueva SKU que ha creado.
5. Para configurar la nueva SKU, consulte el paso 5 de este [vínculo](marketplace-publishing-vm-image-creation.md#5-obtain-certification-for-your-vm-image) como guía.
6. Para agregar el material de marketing para la nueva SKU, vea la sección Paso 1: Especificación de contenido de marketing de Marketplace -> Detalles -> los puntos del 2 al 5 de este [vínculo](marketplace-publishing-push-to-staging.md#step-1-provide-marketplace-marketing-content).
7. Para agregar la información de precios para la nueva SKU, consulte la sección 2.1. Establezca los precios de la máquina virtual mediante la información de este [vínculo](marketplace-publishing-push-to-staging.md#step-2-set-your-prices)
8. Después de realizar los cambios, vaya a la pestaña **PUBLICAR** y haga clic en el botón **PUSH TO STAGING** (Pasar a ensayo). Para obtener instrucciones detalladas sobre cómo probar la oferta en el entorno de ensayo, haga clic en este [vínculo](marketplace-publishing-vm-image-test-in-staging.md)
9. Cuando haya probado la oferta en el entorno de ensayo, vaya a la pestaña **PUBLICAR** del Portal de publicación y haga clic en el botón **REQUEST APPROVAL TO PUSH TO PRODUCTION** (SOLICITAR APROBACIÓN PARA INSERTAR EN PRODUCCIÓN) para volver a publicarla en Azure Marketplace.
   
    ![dibujo](media/marketplace-publishing-vm-image-post-publishing/img03_09-01.png)
   
    ![dibujo](media/marketplace-publishing-vm-image-post-publishing/img03_09-02.png)

## <a name="4-how-to-change-the-data-disk-count-for-a-listed-sku"></a>4. Cambio del número de discos de datos para una SKU activa
No se puede aumentar ni disminuir el número de discos de datos de una SKU activa. En este caso, tendrá que crear una nueva SKU. Vea la sección [3. Incorporación de una nueva SKU en una oferta activa](#3-how-to-add-a-new-sku-under-a-live-offer) para obtener instrucciones detalladas.

## <a name="5-how-to-delete-a-listed-offer-from-the-azure-marketplace"></a>5.    Eliminación de una oferta activa de Azure Marketplace
En el caso de una solicitud para quitar una oferta activa, son varios los aspectos que se deben tener en cuenta. Siga estos pasos si quiere recibir ayuda del equipo de soporte técnico para quitar una oferta activa de Azure Marketplace:

1. Genere una incidencia de soporte técnico mediante este [vínculo](https://support.microsoft.com/en-us/getsupport?wf=0&tenant=ClassicCommercial&oaspworkflow=start_1.0.0.0&locale=en-us&supportregion=en-us&pesid=15635&ccsid=635993707583706681)
2. Como tipo de problema, seleccione **“Administrar ofertas”**, y como categoría, **“Modificar una oferta o SKU ya en producción”**.
3. Envíe la solicitud.

El equipo de soporte técnico le guiará en el proceso de eliminación de la oferta o la SKU.

> [!NOTE]
> Las ofertas siempre pueden eliminarse mientras tengan el estado de borrador (es decir, no de ensayo ni de producción); para ello, haga clic en el botón **DESCARTAR BORRADOR** de la pestaña **HISTORIAL**.
> 
> 

## <a name="6-how-to-delete-a-listed-sku-from-the-azure-marketplace"></a>6. Eliminación de una SKU activa de Azure Marketplace
Puede eliminar una SKU activa de Azure Marketplace siguiendo los pasos indicados a continuación:

1. Inicie sesión en el [Portal de publicación](https://publish.windowsazure.com).
2. Vaya a la pestaña **MÁQUINAS VIRTUALES** y seleccione la oferta.
3. En el panel del lado izquierdo, haga clic en la pestaña **SKUs** (SKU).
4. Seleccione la SKU que quiera eliminar y haga clic en el botón Eliminar de dicha SKU.
5. Después, vaya a la pestaña PUBLICAR del Portal de publicación y haga clic en el botón **REQUEST APPROVAL TO PUSH TO PRODUCTION** (SOLICITAR APROBACIÓN PARA INSERTAR EN PRODUCCIÓN) para volver a publicarla en Azure Marketplace.
6. Cuando la oferta se vuelva a publicar en Azure Marketplace, se eliminará la SKU de Azure Marketplace y del Portal de Azure.

## <a name="7-how-to-delete-the-current-version-of-a-listed-sku-from-the-azure-marketplace"></a>7. Eliminación de la versión actual de una SKU activa de Azure Marketplace
Puede eliminar la versión actual de una SKU activa de Azure Marketplace siguiendo los pasos indicados a continuación. Cuando se complete el proceso, la SKU se revertirá a la versión anterior.

1. Inicie sesión en el [Portal de publicación](https://publish.windowsazure.com).
2. Vaya a la pestaña **MÁQUINAS VIRTUALES** y seleccione la oferta.
3. En el panel del lado izquierdo, haga clic en la pestaña **IMÁGENES DE VM** .
4. Seleccione la SKU cuya versión actual quiera eliminar y haga clic en el botón Eliminar de dicha versión.
5. Después, vaya a la pestaña **PUBLICAR** del Portal de publicación y haga clic en el botón **REQUEST APPROVAL TO PUSH TO PRODUCTION** (SOLICITAR APROBACIÓN PARA INSERTAR EN PRODUCCIÓN) para volver a publicarla en Azure Marketplace.
6. Cuando la oferta se vuelva a publicar en Azure Marketplace, se eliminará la versión actual de la SKU activa de Azure Marketplace y del Portal de Azure. La SKU se revertirá a la versión anterior.

## <a name="8-how-to-revert-listing-price-to-production-values"></a>8. Reversión del precio de lista a los valores de producción
He cambiado el precio de una SKU activa (o he quitado las regiones de facturación de una SKU activa). Quiero revertir los cambios a los valores de producción, pero no puede hacerse en Azure Marketplace. ¿Cómo puede hacerse?

Siga los pasos indicados a continuación:

1. Inicie sesión en el [Portal de publicación](https://publish.windowsazure.com).
2. Vaya a la pestaña **MÁQUINAS VIRTUALES** y seleccione la oferta.
3. En el menú del lado izquierdo, haga clic en la pestaña **PRECIOS** .
4. En la pestaña Precios, seleccione una región cuyos precios quiera restablecer.
   
    ![dibujo](media/marketplace-publishing-vm-image-post-publishing/img08-04.png)
5. En el caso de las SKU con el modelo de facturación por hora, restablezca los precios de todos los núcleos, ya que tienen el estado de producción en la región seleccionada. Para las SKU con el modelo de facturación BYOL, haga que la SKU esté disponible en la región activando la casilla de dicha SKU de la sección EXTERNALLY-LICENSED (BYOL) SKU AVAILABILITY (DISPONIBILIDAD DE SKU CON LICENCIA EXTERNA [BYOL]) (consulte la siguiente captura de pantalla).
   
    ![dibujo](media/marketplace-publishing-vm-image-post-publishing/img08-05.png)
6. Ahora, haga clic en el botón **AUTOPRICE OTHER MARKETS BASED ON PRICES IN UNITED STATES**(APLICAR AUTOMÁTICAMENTE LOS PRECIOS PARA ESTADOS UNIDOS A OTROS MERCADOS).
   
   > [!NOTE]
   > La etiqueta del botón puede variar en función de la región que haya seleccionado. Como seleccionamos Estados Unidos al crear este documento, la etiqueta del botón es Auto price other markets based on prices in United States (Aplicar automáticamente los precios para Estados Unidos a otros mercados) en la captura de pantalla de abajo.
   > 
   > 
   
    ![dibujo](media/marketplace-publishing-vm-image-post-publishing/img08-06.png)
7. Se abrirá el asistente de precios automáticos. La primera página muestra la selección para el mercado base. Elija una opción y pase a la página siguiente haciendo clic en el botón **“->”**.
   
    ![dibujo](media/marketplace-publishing-vm-image-post-publishing/img08-07.png)
8. La opción para seleccionar los planes y núcleos se mostrará en la página 2. Seleccione los planes y núcleos que quiera y haga clic en el botón "->".
   
    ![dibujo](media/marketplace-publishing-vm-image-post-publishing/img08-08.png)
9. En la página 3 se muestran las regiones y los mercados. Haga clic en el botón Toggle All (Alternar todo) para seleccionar todas las regiones o active manualmente las casillas de la región que le corresponda. Haga clic en el botón "->" para ir a la página siguiente.
   
    ![dibujo](media/marketplace-publishing-vm-image-post-publishing/img08-09.png)
10. En la página 4 se muestran las tasas de cambio. Haga clic en el botón Finalizar para completar los pasos. El asistente restablecerá los precios según lo que haya seleccionado.
11. Ahora, vaya a la pestaña Precios y haga clic en el botón VIEW SUMMARY AND CHANGES (VER RESUMEN Y CAMBIOS).
    Seleccione Borrador en la sección View Version (Ver versión) y Producción en la sección Comparar con (consulte la siguiente captura de pantalla). Si no ve ninguna diferencia de precio, significa que los precios se han revertido correctamente a los valores de producción.
    
    ![dibujo](media/marketplace-publishing-vm-image-post-publishing/img08-11.png)
12. Después de realizar los cambios, vaya a la pestaña PUBLICAR y haga clic en el botón **PUSH TO STAGING**(INSERTAR EN ENTORNO DE ENSAYO). Para obtener instrucciones detalladas sobre cómo probar la oferta en el entorno de ensayo, haga clic en este [vínculo](marketplace-publishing-vm-image-test-in-staging.md)
13. Cuando haya probado la oferta en el entorno de ensayo, vaya a la pestaña PUBLICAR del Portal de publicación y haga clic en el botón **REQUEST APPROVAL TO PUSH TO PRODUCTION** (SOLICITAR APROBACIÓN PARA INSERTAR EN PRODUCCIÓN) para volver a publicarla en Azure Marketplace.

## <a name="9-how-to-revert-billing-model-to-production-values"></a>9. Reversión del modelo de facturación a los valores de producción
He cambiado el modelo de facturación de una SKU activa. Quiero revertir los cambios a los valores de producción, pero no puede hacerse en Azure Marketplace. ¿Cómo puede hacerse?

Siga estos pasos:

1. Inicie sesión en el [Portal de publicación](https://publish.windowsazure.com).
2. Vaya a la pestaña **MÁQUINAS VIRTUALES** y seleccione la oferta.
3. En el menú del lado izquierdo, haga clic en la pestaña **SKUs** (SKU).
4. Haga clic en el botón EDITAR para revertir el modelo de facturación. Se mostrará una ventana. Active o desactive la casilla de verificación **Billing and licensing is done externally from Azure (aka Bring Your Own License)** (La facturación y las licencias no se administran en Azure [Traiga su propia licencia]) en consecuencia.
   
    ![dibujo](media/marketplace-publishing-vm-image-post-publishing/img09-04.png)
5. Después, consulte la respuesta de la pregunta 8 de este documento para revertir los precios.
6. Tras esto, vuelva a la pestaña **Publicar** del Portal de publicación y envíe la oferta al entorno provisional para probarla. Cuando haya terminado de probar la oferta, haga clic en el botón **REQUEST APPROVAL TO PUSH TO PRODUCTION** (SOLICITAR APROBACIÓN PARA INSERTAR EN PRODUCCIÓN) para volver a publicarla en Azure Marketplace.

## <a name="10-how-to-revert-visibility-setting-of-a-listed-sku-to-the-production-value"></a>10. Reversión de la configuración de visibilidad de una SKU activa al valor de producción
Siga estos pasos:

1. Inicie sesión en el [Portal de publicación](https://publish.windowsazure.com).
2. Vaya a la pestaña **MÁQUINAS VIRTUALES** y seleccione la oferta.
3. En el menú del lado izquierdo, haga clic en la pestaña **SKUs** (SKU).
4. Seleccione la SKU y revierta su configuración de visibilidad al valor de producción.
   
    ![dibujo](media/marketplace-publishing-vm-image-post-publishing/img10-04.png)
5. Cuando haya terminado de realizar los cambios, haga clic en el botón **REQUEST APPROVAL TO PUSH TO PRODUCTION** (SOLICITAR APROBACIÓN PARA INSERTAR EN PRODUCCIÓN) para volver a publicarla en Azure Marketplace.

## <a name="see-also"></a>Otras referencias
* [Introducción: cómo publicar una oferta en Azure Marketplace](marketplace-publishing-getting-started.md)
* [Descripción de informes de perspectivas de vendedor](marketplace-publishing-report-seller-insights.md)
* [Descripción de informes de pago](marketplace-publishing-report-payout.md)
* [Cómo cambiar el incentivo de revendedores para proveedores de soluciones en la nube](marketplace-publishing-csp-incentive.md)
* [Solución de problemas comunes de publicación en Marketplace](marketplace-publishing-support-common-issues.md)
* [Obtención de soporte técnico como publicador](marketplace-publishing-get-publisher-support.md)
* [Creación de una imagen de máquina virtual local](marketplace-publishing-vm-image-creation-on-premise.md)
* [Creación de una máquina virtual que ejecuta Windows en el portal de vista previa de Azure](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)




<!--HONumber=Nov16_HO3-->


