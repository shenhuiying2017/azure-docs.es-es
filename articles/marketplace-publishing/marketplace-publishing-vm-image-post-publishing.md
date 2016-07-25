<properties
   pageTitle="Administración de la imagen de máquina virtual en Azure Marketplace | Microsoft Azure"
   description="Guía detallada sobre cómo administrar la imagen de máquina virtual en Azure Marketplace después de la publicación inicial."
   services="Azure Marketplace"
   documentationCenter=""
   authors="HannibalSII"
   manager=""
   editor=""/>

<tags
   ms.service="marketplace"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="Azure"
   ms.workload="na"
   ms.date="06/01/2016"
   ms.author="hascipio;"/>

# Guía de postproducción para ofertas de máquina virtual en Azure Marketplace

En este artículo se explica cómo actualizar una oferta de máquina virtual activa en Azure Marketplace. También le guía en el proceso de agregar una o varias SKU a una oferta existente y quitar una oferta de máquina virtual activa o una SKU de Azure Marketplace.

**Una vez que la SKU está activa en Azure Marketplace, no se pueden actualizar los detalles que se indican a continuación:**

- **Identificador de SKU**
- **Identificador de publicador**
- **Identificador de oferta**
- **Cambio de precio**
- **Cambio del modelo de facturación**
- **Eliminación de regiones de facturación**


## 1\. Actualización de los detalles técnicos de una SKU

Puede actualizar una imagen de máquina virtual y volver a publicar su oferta siguiendo los pasos que se indican a continuación:

1. Inicie sesión en el [Portal de publicación](https://publish.windowsazure.com).
2. Vaya a la pestaña **VIRTUAL MACHINES** (MÁQUINAS VIRTUALES) y seleccione su oferta.
3. En el menú del lado izquierdo, haga clic en la pestaña **VM IMAGES** (IMÁGENES DE VM).
4. En la sección **SKU** de dicha pestaña, busque la SKU que quiere actualizar. A continuación, actualice el número de versión de la SKU. La nueva versión debe tener el formato X.Y.Z, donde X, Y, Z son números enteros. Los cambios de versión solo deben ser incrementales.
5. En el cuadro **OS VHD URL** (Dirección URL del VHD del sistema operativo), escriba el URI de la firma de acceso compartido creado para el VHD del sistema operativo y guarde los cambios.
6. Después de realizar los cambios, vaya a la pestaña **PUBLISH** (PUBLICAR) y haga clic en el botón **PUSH TO STAGING** (INSERTAR EN ENTORNO DE ENSAYO). Para obtener instrucciones detalladas sobre cómo probar la oferta en el entorno de ensayo, consulte este [vínculo](marketplace-publishing-vm-image-test-in-staging.md).
7. Cuando haya probado la oferta en el entorno de ensayo, vaya a la pestaña **PUBLISH** (PUBLICAR) en el Portal de publicación y haga clic en el botón **REQUEST APPROVAL TO PUSH TO PRODUCTION** (SOLICITAR APROBACIÓN PARA INSERTAR EN PRODUCCIÓN) para volver a publicar su oferta en Azure Marketplace.

    ![dibujo](media/marketplace-publishing-vm-image-post-publishing/img01.png)

## 2\. Actualización de los detalles no técnicos de una oferta o una SKU

Puede actualizar los detalles no técnicos (marketing, legales, soporte técnico, categorías) de la oferta activa o la SKU en Azure Marketplace.

### 2\.1 Actualización de la descripción y los logotipos de la oferta

Puede actualizar los detalles de la oferta y volver a publicarla mediante estos pasos:

1. Inicie sesión en el [Portal de publicación](https://publish.windowsazure.com).
2. Vaya a la pestaña **VIRTUAL MACHINES** (MÁQUINAS VIRTUALES) y seleccione su oferta.
3. En el menú del lado izquierdo, haga clic en la pestaña **MARKETING**.
4. Haga clic en el botón **ENGLISH (US)** (Inglés [EE. UU.]).
5. En el menú del lado izquierdo, haga clic en la pestaña **DETAILS** (DETALLES). En la sección *DESCRIPTION* (DESCRIPCIÓN) de la pestaña **DETAILS** (DETALLES), puede actualizar el título, el resumen o el resumen largo de la oferta y guardar los cambios.

    >[AZURE.NOTE] Cuando actualice los detalles de la SKU, tenga en cuenta lo siguiente. **No escriba texto duplicado en la descripción de la oferta y la descripción de la SKU. No escriba texto duplicado en el resumen largo de la oferta y el título de la SKU. No escriba texto duplicado en el resumen de la oferta y el título de la SKU.**

    ![dibujo](media/marketplace-publishing-vm-image-post-publishing/img02.png)

6. En la sección *LOGOS* (LOGOTIPOS) de la pestaña **DETAILS** (DETALLES), puede actualizar los logotipos. Sin embargo, asegúrese de que los logotipos sigan la [instrucciones de Azure Marketplace](marketplace-publishing-push-to-staging.md#step-1-provide-marketplace-marketing-content) (consulte la sección Paso 1: Especificación de contenido de marketing de Marketplace -> Detalles -> Instrucciones del logotipo de Azure Marketplace).

    >[AZURE.NOTE] El icono de imagen prominente es opcional. Puede elegir no cargar un icono de imagen prominente. Sin embargo, cuando se carga el icono de imagen prominente, no es necesario aprovisionarlo para eliminarlo del Portal de publicación. En ese caso, debe seguir las [instrucciones para la imagen prominente](marketplace-publishing-push-to-staging.md#step-1-provide-marketplace-marketing-content) (consulte la sección Paso 1: Especificación de contenido de marketing de Marketplace -> Detalles -> Instrucciones adicionales para la pancarta de logotipo de imagen prominente).

7. Después de realizar los cambios, vaya a la pestaña **PUBLISH** (PUBLICAR) y haga clic en el botón **PUSH TO STAGING** (INSERTAR EN ENTORNO DE ENSAYO). Para obtener instrucciones detalladas sobre cómo probar la oferta en el entorno de ensayo, consulte este [vínculo](marketplace-publishing-vm-image-test-in-staging.md).
8. Cuando haya probado la oferta en el entorno de ensayo, vaya a la pestaña **PUBLISH** (PUBLICAR) en el Portal de publicación y haga clic en el botón **REQUEST APPROVAL TO PUSH TO PRODUCTION** (SOLICITAR APROBACIÓN PARA INSERTAR EN PRODUCCIÓN) para volver a publicar su oferta en Azure Marketplace.

    ![dibujo](media/marketplace-publishing-vm-image-post-publishing/img02.1.png)

### 2\.2. Actualización de la descripción de la SKU

Puede actualizar los detalles de la SKU y volver a publicar su oferta siguiendo estos pasos:

1. Inicie sesión en el [Portal de publicación](https://publish.windowsazure.com).
2. Vaya a la pestaña **VIRTUAL MACHINES** (MÁQUINAS VIRTUALES) y seleccione su oferta.
3. En el menú del lado izquierdo, haga clic en la pestaña **MARKETING**.
4. Haga clic en el botón **ENGLISH (US)** (Inglés [EE. UU.]).
5. En el menú del lado izquierdo, haga clic en la pestaña **PLANS** (PLANES). En la sección *SKU* de la pestaña **PLANS** (PLANES), puede actualizar el título, el resumen y la descripción de la SKU y guardar los cambios.

    >[AZURE.NOTE] Cuando actualice los detalles de la SKU, tenga en cuenta lo siguiente. **No escriba texto duplicado en la descripción de la oferta y la descripción de la SKU. No escriba texto duplicado en el resumen largo de la oferta y el título de la SKU. No escriba texto duplicado en el resumen de la oferta y el título de la SKU.**

6. Después de realizar los cambios, vaya a la pestaña **PUBLISH** (PUBLICAR) y haga clic en el botón **PUSH TO STAGING** (INSERTAR EN ENTORNO DE ENSAYO). Para obtener instrucciones detalladas sobre cómo probar la oferta en el entorno de ensayo, consulte este vínculo.
7. Cuando haya probado la oferta en el entorno de ensayo, vaya a la pestaña **PUBLISH** (PUBLICAR) en el Portal de publicación y haga clic en el botón **REQUEST APPROVAL TO PUSH TO PRODUCTION** (SOLICITAR APROBACIÓN PARA INSERTAR EN PRODUCCIÓN) para volver a publicar su oferta en Azure Marketplace.

    ![dibujo](media/marketplace-publishing-vm-image-post-publishing/img02.2.png)

### 2\.3 Cambio de los vínculos existentes o incorporación de nuevos vínculos

Puede cambiar los vínculos existentes o agregar otros nuevos y luego volver a publicar la oferta siguiendo los pasos que se indican a continuación:

1. Inicie sesión en el [Portal de publicación](https://publish.windowsazure.com).
2. Vaya a la pestaña **VIRTUAL MACHINES** (MÁQUINAS VIRTUALES) y seleccione su oferta.
3. En el menú del lado izquierdo, haga clic en la pestaña **MARKETING**.
4. Haga clic en el botón **ENGLISH (US)** (Inglés [EE. UU.]).
5. En el menú del lado izquierdo, haga clic en la pestaña **LINKS** (VÍNCULOS).
6. Si quiere agregar un nuevo vínculo, en la sección *Links* (Vínculos), haga clic en el botón **ADD LINK** (AGREGAR VÍNCULO). Se abre el cuadro de diálogo *"Add Link"* (Agregar vínculo). En este cuadro de diálogo, puede agregar los campos de título y dirección URL del vínculo y guardar los cambios. Puede escribir cualquier vínculo que contenga información que pueda ser de ayuda para los clientes.
7. Si quiere actualizar o eliminar un vínculo existente, seleccione el vínculo adecuado y haga clic en el botón de edición o eliminación, según corresponda.

    >[AZURE.NOTE] Asegúrese de que los vínculos que ha especificado en esta sección funcionen correctamente, dado que dichos vínculos se validarán durante el proceso de solicitud para producción.

8. Después de realizar los cambios, vaya a la pestaña **PUBLISH** (PUBLICAR) y haga clic en el botón **PUSH TO STAGING** (INSERTAR EN ENTORNO DE ENSAYO). Para obtener instrucciones detalladas sobre cómo probar la oferta en el entorno de ensayo, consulte este [vínculo](marketplace-publishing-vm-image-test-in-staging.md).
9. Cuando haya probado la oferta en el entorno de ensayo, vaya a la pestaña **PUBLISH** (PUBLICAR) en el Portal de publicación y haga clic en el botón **REQUEST APPROVAL TO PUSH TO PRODUCTION** (SOLICITAR APROBACIÓN PARA INSERTAR EN PRODUCCIÓN) para volver a publicar su oferta en Azure Marketplace.

    ![dibujo](media/marketplace-publishing-vm-image-post-publishing/img02.3.png)

    ![dibujo](media/marketplace-publishing-vm-image-post-publishing/img02.3-2.png)

### 2\.4 Cambio de una imagen de ejemplo existente o incorporación de una nueva imagen de ejemplo

Puede cambiar las imágenes de ejemplo existentes o agregar otras nuevas y luego volver a publicar la oferta siguiendo los pasos que se indican a continuación:

>[AZURE.NOTE] Solo una imagen de ejemplo se muestra en [https://portal.azure.com](https://portal.azure.com).

1. Inicie sesión en el [Portal de publicación](https://publish.windowsazure.com).
2. Vaya a la pestaña **VIRTUAL MACHINES** (MÁQUINAS VIRTUALES) y seleccione su oferta.
3. En el menú del lado izquierdo, haga clic en la pestaña **MARKETING**.
4. Haga clic en el botón **ENGLISH (US)** (Inglés [EE. UU.]).
5. En el menú del lado izquierdo, haga clic en la pestaña **SAMPLE IMAGES** (IMÁGENES DE EJEMPLO).
6. Si quiere agregar una nueva imagen de ejemplo, en la sección *Sample Images* (Imágenes de ejemplo), haga clic en el botón **UPLOAD A NEW IMAGE** (CARGAR UNA NUEVA IMAGEN) y luego guarde los cambios.

    >[AZURE.NOTE] La inclusión de una imagen de ejemplo es un paso opcional.

7. Si quiere actualizar o eliminar una imagen de ejemplo existente, busque la que considere adecuada y haga clic en el botón **REPLACE IMAGE** (REEMPLAZAR IMAGEN) o en el botón de eliminación, según corresponda.

8. Después de realizar los cambios, vaya a la pestaña **PUBLISH** (PUBLICAR) y haga clic en el botón **PUSH TO STAGING** (INSERTAR EN ENTORNO DE ENSAYO). Para obtener instrucciones detalladas sobre cómo probar la oferta en el entorno de ensayo, consulte este [vínculo](marketplace-publishing-vm-image-test-in-staging.md).
9. Cuando haya probado la oferta en el entorno de ensayo, vaya a la pestaña **PUBLISH** (PUBLICAR) en el Portal de publicación y haga clic en el botón **REQUEST APPROVAL TO PUSH TO PRODUCTION** (SOLICITAR APROBACIÓN PARA INSERTAR EN PRODUCCIÓN) para volver a publicar su oferta en Azure Marketplace.

    ![dibujo](media/marketplace-publishing-vm-image-post-publishing/img02.4.png)

### 2\.5 Actualización del contenido legal

Puede actualizar el contenido legal y volver a publicar su oferta siguiendo estos pasos:

1. Inicie sesión en el [Portal de publicación](https://publish.windowsazure.com).
2. Vaya a la pestaña **VIRTUAL MACHINES** (MÁQUINAS VIRTUALES) y seleccione su oferta.
3. En el menú del lado izquierdo, haga clic en la pestaña **MARKETING**.
4. Haga clic en el botón **ENGLISH (US)** (Inglés [EE. UU.]).
5. En el menú del lado izquierdo, haga clic en la pestaña **LEGAL**. En la sección *Legal*, puede actualizar las directivas y los términos de uso. Escriba o pegue las directivas o los términos en el cuadro de texto *Terms of Use* (Términos de uso) y guarde los cambios.
6. El límite de caracteres para las condiciones de uso legales es de 1.000.000 de caracteres.
7. Después de realizar los cambios, vaya a la pestaña **PUBLISH** (PUBLICAR) y haga clic en el botón **PUSH TO STAGING** (INSERTAR EN ENTORNO DE ENSAYO). Para obtener instrucciones detalladas sobre cómo probar la oferta en el entorno de ensayo, consulte este [vínculo](marketplace-publishing-vm-image-test-in-staging.md).
8. Cuando haya probado la oferta en el entorno de ensayo, vaya a la pestaña **PUBLISH** (PUBLICAR) en el Portal de publicación y haga clic en el botón **REQUEST APPROVAL TO PUSH TO PRODUCTION** (SOLICITAR APROBACIÓN PARA INSERTAR EN PRODUCCIÓN) para volver a publicar su oferta en Azure Marketplace.

    ![dibujo](media/marketplace-publishing-vm-image-post-publishing/img02.5.png)

### 2\.6 Actualización de la información de soporte técnico

Puede actualizar la información de soporte técnico y volver a publicar su oferta siguiendo estos pasos:

1. Inicie sesión en el [Portal de publicación](https://publish.windowsazure.com).
2. Vaya a la pestaña **VIRTUAL MACHINES** (MÁQUINAS VIRTUALES) y seleccione su oferta.
3. En el menú del lado izquierdo, haga clic en la pestaña **SUPPORT** (SOPORTE TÉCNICO).
4. En la sección *Engineering Contact* (Contacto de ingeniería) de la pestaña **SUPPORT** (SOPORTE TÉCNICO), puede actualizar los detalles de contacto. Los detalles de contacto solo se usan para comunicaciones internas entre asociados y Microsoft.
5. En la sección *Customer Support* (Soporte al cliente) de la pestaña **SUPPORT** (SOPORTE TÉCNICO), puede actualizar los detalles de contacto de soporte técnico, como **nombre, dirección de correo electrónico, teléfono** y **dirección URL de soporte técnico**. Los detalles de contacto solo se usan para comunicaciones internas entre asociados y Microsoft.

    >[AZURE.NOTE] Si quiere proporcionar solo soporte técnico por correo electrónico, facilite un número de teléfono ficticio en la sección **Customer Support** (Soporte al cliente). En este caso, en su lugar se usará la dirección de correo proporcionada.

6. Después de realizar los cambios, vaya a la pestaña **PUBLISH** (PUBLICAR) y haga clic en el botón **PUSH TO STAGING** (INSERTAR EN ENTORNO DE ENSAYO). Para obtener instrucciones detalladas sobre cómo probar la oferta en el entorno de ensayo, consulte este [vínculo](marketplace-publishing-vm-image-test-in-staging.md).
7. Cuando haya probado la oferta en el entorno de ensayo, vaya a la pestaña **PUBLISH** (PUBLICAR) en el Portal de publicación y haga clic en el botón **REQUEST APPROVAL TO PUSH TO PRODUCTION** (SOLICITAR APROBACIÓN PARA INSERTAR EN PRODUCCIÓN) para volver a publicar su oferta en Azure Marketplace.

    ![dibujo](media/marketplace-publishing-vm-image-post-publishing/img02.7.png)

### 2\.7 Actualización de las categorías

Puede actualizar la sección de categorías de la oferta y volver a publicarla mediante estos pasos:

1. Inicie sesión en el [Portal de publicación](https://publish.windowsazure.com).
2. Vaya a la pestaña **VIRTUAL MACHINES** (MÁQUINAS VIRTUALES) y seleccione su oferta.
3. En el menú del lado izquierdo, haga clic en la pestaña **CATEGORIES** (CATEGORÍAS).
4. En la sección *Categories* (Categorías), puede actualizar las categorías de la oferta y guardar los cambios. Puede seleccionar hasta cinco categorías de la galería de Azure Marketplace.
5. Después de realizar los cambios, vaya a la pestaña **PUBLISH** (PUBLICAR) y haga clic en el botón **PUSH TO STAGING** (INSERTAR EN ENTORNO DE ENSAYO). Para obtener instrucciones detalladas sobre cómo probar la oferta en el entorno de ensayo, consulte este [vínculo](marketplace-publishing-vm-image-test-in-staging.md).
6. Cuando haya probado la oferta en el entorno de ensayo, vaya a la pestaña **PUBLISH** (PUBLICAR) en el Portal de publicación y haga clic en el botón **REQUEST APPROVAL TO PUSH TO PRODUCTION** (SOLICITAR APROBACIÓN PARA INSERTAR EN PRODUCCIÓN) para volver a publicar su oferta en Azure Marketplace.

    ![dibujo](media/marketplace-publishing-vm-image-post-publishing/img02.7.png)

## 3\. Incorporación de una nueva SKU en una oferta activa

Puede agregar una nueva SKU en la oferta activa mediante los pasos que se describen a continuación:

1. Inicie sesión en el [Portal de publicación](https://publish.windowsazure.com).
2. Vaya a la pestaña **VIRTUAL MACHINES** (MÁQUINAS VIRTUALES) y seleccione su oferta.
3. En el menú del lado izquierdo, haga clic en la pestaña **SKU**. A continuación, haga clic en el botón **ADD A SKU** (AGREGAR UNA SKU). Se abrirá un nuevo cuadro de diálogo. Escriba un identificador de SKU en minúsculas. Active la casilla para traer su propio modelo de facturación (BYOL) si quiere publicar la nueva SKU con él. De lo contrario, desactive esta casilla. A continuación, haga clic en la marca de verificación del cuadro de diálogo para crear una nueva SKU. Si no elige BYOL como modelo de facturación para la nueva SKU, este se establecerá automáticamente en Hourly (Cada hora). Si quiere habilitar la evaluación gratuita de 30 días para el modelo de facturación Hourly (Cada hora), haga clic en la opción "One Month" (Un mes) en "Is a free a free trial available?" (¿Hay disponible una evaluación gratuita?). En caso contrario, seleccione "NO TRIAL" (SIN EVALUACIÓN). [Nota: La opción "Is a free a free trial available?" (¿Hay disponible una evaluación gratuita?) solo aparece si NO ha seleccionado BYOL en el cuadro de diálogo al crear la nueva SKU].

    >[AZURE.IMPORTANT] La opción "Hide this SKU from the Marketplace because it should always be bought via a solution template" (Ocultar esta SKU en Marketplace porque siempre se debe comprar mediante una plantilla de solución) se debe marcar como "YES" (SÍ) SOLO si ha recibido aprobación para publicar una oferta de plantilla de solución en Azure Marketplace. De lo contrario, esta opción siempre se debe marcar como "NO".

4. Ahora, en el menú del lado izquierdo, haga clic en la pestaña **VM IMAGES** (IMÁGENES DE VM) y descubra la nueva SKU que ha creado.
5. Para configurar la nueva SKU, consulte el paso 5 de este [vínculo](marketplace-publishing-vm-image-creation.md#5-obtain-certification-for-your-vm-image) como guía.
6. Para agregar el material de marketing para la nueva SKU, consulte la sección Paso 1: Especificación de contenido de marketing de Marketplace -> Detalles -> los puntos del 2 al 5 de este [vínculo](marketplace-publishing-push-to-staging.md#step-1-provide-marketplace-marketing-content).
7. Para agregar la información de precios para la nueva SKU, consulte la sección 2.1. Establezca los precios de la máquina virtual mediante la información de este [vínculo](marketplace-publishing-push-to-staging.md#step-2-set-your-prices)
8. Después de realizar los cambios, vaya a la pestaña **PUBLISH** (PUBLICAR) y haga clic en el botón **PUSH TO STAGING** (INSERTAR EN ENTORNO DE ENSAYO). Para obtener instrucciones detalladas sobre cómo probar la oferta en el entorno de ensayo, consulte este [vínculo](marketplace-publishing-vm-image-test-in-staging.md).
9. Cuando haya probado la oferta en el entorno de ensayo, vaya a la pestaña **PUBLISH** (PUBLICAR) en el Portal de publicación y haga clic en el botón **REQUEST APPROVAL TO PUSH TO PRODUCTION** (SOLICITAR APROBACIÓN PARA INSERTAR EN PRODUCCIÓN) para volver a publicar su oferta en Azure Marketplace.

    ![dibujo](media/marketplace-publishing-vm-image-post-publishing/img03.1-1.png)

    ![dibujo](media/marketplace-publishing-vm-image-post-publishing/img03.1-2.png)

## 4\. Eliminación de una oferta activa o una SKU de Azure Marketplace

En el caso de una solicitud para quitar una oferta activa, son varios los aspectos que se deben tener en cuenta. Siga estos pasos para recibir ayuda del equipo de soporte técnico para quitar una oferta activa o una SDK de Azure Marketplace:

1.	Genere una incidencia de soporte técnico mediante este [vínculo](https://support.microsoft.com/es-ES/getsupport?wf=0&tenant=ClassicCommercial&oaspworkflow=start_1.0.0.0&locale=es-ES&supportregion=es-ES&pesid=15635&ccsid=635993707583706681).
2.	Como tipo de problema seleccione **"Managing offers"** (Administración de ofertas) y como categoría **"Modifying an offer and/or SKU already in production"** (Modificación de una oferta o una SKU ya en producción).
3.	Envíe la solicitud.

El equipo de soporte técnico le guiará en el proceso de eliminación de la oferta o la SKU.

>[AZURE.NOTE] Siempre se puede eliminar la oferta mientras se encuentre en estado preliminar (borrador) (es decir, no en ensayo o producción); para ello, haga clic en el botón **DISCARD DRAFT** (DESCARTAR BORRADOR) en la pestaña **HISTORY** (HISTORIAL).

## Otras referencias
- [Introducción: cómo publicar una oferta en Azure Marketplace](marketplace-publishing-getting-started.md)
- [Descripción de informes de perspectivas de vendedor](marketplace-publishing-report-seller-insights.md)
- [Descripción de informes de pago](marketplace-publishing-report-payout.md)
- [Cómo cambiar el incentivo de revendedores para proveedores de soluciones en la nube](marketplace-publishing-csp-incentive.md)
- [Solución de problemas comunes de publicación en Marketplace](marketplace-publishing-support-common-issues.md)
- [Obtención de soporte técnico como publicador](marketplace-publishing-get-publisher-support.md)
- [Creación de una imagen de máquina virtual local](marketplace-publishing-vm-image-creation-on-premise.md)
- [Creación de una máquina virtual que ejecuta Windows en el portal de vista previa de Azure](../virtual-machines/virtual-machines-windows-hero-tutorial.md)

<!---HONumber=AcomDC_0713_2016-->