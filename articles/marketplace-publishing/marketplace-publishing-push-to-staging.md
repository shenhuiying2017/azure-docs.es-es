---
title: "Preparación y prueba de la oferta para su implementación en Azure Marketplace | Microsoft Docs"
description: "Se ofrecen instrucciones detalladas sobre cómo proporcionar contenido de marketing, configurar planes de precios y probar la oferta antes de implementarla en Azure Marketplace."
services: marketplace-publishing
documentationcenter: 
author: HannibalSII
manager: hascipio
editor: 
ms.assetid: 3ccd2448-895b-477e-adf6-ab655a21d2fa
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: Azure
ms.workload: na
ms.date: 08/17/2016
ms.author: hascipio
ms.openlocfilehash: 7db86716cdf8f9eb921c3c1813970acae7a3016b
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="complete-the-offer-creation-with-marketing-content"></a>Finalización de la creación de ofertas con contenido de marketing
En este paso del proceso de publicación, deberá proporcionar determinados contenidos de marketing y los detalles acerca de la oferta o SKU en Azure Marketplace. Por ejemplo, proporcionará una descripción de su producto, logotipos de empresa, planes de precios, detalles de los planes y otra información necesaria para insertar su oferta o SKU en el entorno de ensayo. Esta información se usa como contenido de marketing en nuestro Portal de Azure. Comenzará este proceso en el [portal de publicación][link-pubportal].

## <a name="step-1-provide-marketplace-marketing-content"></a>Paso 1: Especificación de contenido de marketing de Marketplace
**El inglés es el idioma establecido de manera predeterminada y el único que se admite.** Asegúrese de que toda la información proporcionada en los campos esté en inglés. Toda la información se puede editar en cualquier momento hasta que pase a la etapa de ensayo.

1. Vaya al portal de publicación, [https://publish.windowsazure.com](https://publish.windowsazure.com).
2. En el menú de la izquierda, haga clic en la pestaña **Marketing** .
3. En el panel principal, haga clic en el botón **Inglés (Estados Unidos)** .
   
   > [!IMPORTANT]
   > Todos los campos deben tener entradas, incluidas las imágenes, para poder pasar a la etapa de ensayo.
   > 
   > 

### <a name="details-and-plans"></a>Detalles y planes
1. Escriba el título de la oferta (50 caracteres como máximo), el resumen de la oferta (100 caracteres como máximo), el resumen largo de la oferta (256 caracteres como máximo), la descripción de la oferta (1300 caracteres como máximo) y los logotipos en la pestaña **Detalles**
2. Escriba el título (50 caracteres como máximo), el resumen (100 caracteres como máximo) y la descripción del plan (2000 caracteres como máximo) en la pestaña **Planes** .
   
   > [!NOTE]
   > Puede utilizar las siguientes etiquetas HTML para dar formato al resumen, al resumen largo, y a la descripción de la oferta y los planes. Las etiquetas que se admiten son h1, h2, h3, h4, h5, p, ol, ul, li, a[target|href], strong, em, b, i.
   > 
   > 
3. No escriba texto duplicado en la descripción del plan y la oferta.
4. No escriba texto duplicado en el resumen largo de la oferta y el título del plan.
5. No escriba texto duplicado en el resumen de la oferta y el título del plan.
6. No escriba títulos de planes idénticos en una oferta con varios planes.
7. Cargue imágenes de las especificaciones necesarias (mencionadas en el Portal de Publicación) en formato PNG, una por tamaño.
8. Asegúrese de que los logotipos sigan las instrucciones del logotipo de Azure Marketplace que se mencionan a continuación.
   
   ![dibujo](media/marketplace-publishing-push-to-staging/pubportal-marketingcontent-details-02.png)

**Instrucciones del logotipo de Azure Marketplace**

Todos los logotipos que se cargan en el Portal de publicación deberían seguir las siguientes directrices:

* El diseño de Azure tiene una paleta de colores simple. Utilice pocos colores primarios y secundarios en el logotipo.
* Los colores del tema del Portal de Azure son el blanco y el negro. Por lo tanto, evite emplear estos colores como fondo de los logotipos. Utilice algún color que haga destacar a los logotipos en el Portal de Azure. Nosotros recomendamos usar colores primarios simples. **Si utiliza un fondo transparente, asegúrese de que el texto y los logotipos no sean de color azul, blanco o negro.**
* No utilice un fondo degradado en el logotipo.
* Evite colocar texto, incluso la empresa o el nombre de marca, en el logotipo. El aspecto del logotipo debe ser "plano" y debe evitar degradados.
* El logotipo no se debe extender.
* El logotipo pequeño debe tener un tamaño de 40 x 40 píxeles
* El logotipo mediano debe tener un tamaño de 90 x 90 píxeles
* El logotipo grande debe tener un tamaño de 115 X 115 píxeles
* El logotipo ancho debe tener un tamaño de 255 X 115 píxeles
* El logotipo de imagen prominente debe tener un tamaño de 815 X 290 píxeles

> [!NOTE]
> El logotipo de imagen prominente es opcional. El publicador puede decidir no cargar un logotipo de imagen prominente. Sin embargo, una vez que cargue la imagen prominente, no podrá eliminarla del Portal de publicación. A partir de ese momento, el asociado debe seguir las instrucciones de Azure Marketplace para imágenes prominentes.
> 
> 

  ![dibujo](media/marketplace-publishing-push-to-staging/pubportal-marketingcontent-details-03.png)

**Instrucciones adicionales para el icono de logotipo de imagen prominente (opcional)**

* El logotipo de imagen prominente es opcional. El publicador puede decidir no cargar un logotipo de imagen prominente. **Sin embargo, una vez que cargue la imagen prominente, no podrá eliminarla del Portal de publicación. A partir de ese momento, el asociado debe seguir las instrucciones de Azure Marketplace para imágenes prominentes; en caso contrario, la oferta no contará con la aprobación para pasar a producción.**
* El nombre para mostrar del publicador, el título del plan y el resumen largo de la oferta se muestran en color blanco. Por lo tanto, debe evitar usar colores claros en el fondo de la imagen prominente. Los fondos transparentes y de color negro y blanco no pueden utilizarse en las imágenes prominentes.
* El nombre para mostrar del publicador, el título del plan, el resumen largo de la oferta y el botón Crear se incrustan mediante programación dentro del logotipo de imagen prominente cuando se publica la oferta. Por lo tanto, no hace falta especificarlos cuando diseñe el logotipo de imagen prominente. Deje un espacio vacío a la derecha, ya que será ahí donde el texto (es decir, el nombre para mostrar del publicador, el título del plan y el resumen largo de la oferta) se incluirá automáticamente mediante programación. El espacio vacío para el texto debe tener un tamaño de 415 x 100 a la derecha (con un desplazamiento de 370 píxeles a la izquierda).
  
  ![dibujo](media/marketplace-publishing-push-to-staging/pubportal-herobanner.png)

### <a name="links"></a>Vínculos
En la pestaña **Vínculos** de la barra izquierda, incluya vínculos con información que puedan resultar útiles para los clientes. Escriba un nombre y una dirección URL para cada vínculo.

![dibujo](media/marketplace-publishing-push-to-staging/pubportal-marketingcontent-link-01.png)

### <a name="sample-images-optional"></a>Imágenes de ejemplo (opcional)
> [!NOTE]
> La inclusión de una imagen de ejemplo es un paso opcional.
> Aunque puede cargar varias imágenes de ejemplo en el Portal de publicación, solo se mostrará una (el sistema la selecciona aleatoriamente) en el Portal de Azure. Por este motivo, recomendamos cargar, como mínimo, una imagen de ejemplo.
> 
> 

En la pestaña **Imágenes de ejemplo** del menú izquierdo, cargue una nueva imagen haciendo clic en **Cargar una nueva imagen**. Si tiene una imagen existente y desea reemplazarla, haga clic en **Reemplazar imagen**.

![dibujo](media/marketplace-publishing-push-to-staging/pubportal-marketingcontent-sampleimg-01.png)

### <a name="legal"></a>Información legal
En la pestaña **Legal** , proporcione un vínculo a sus directivas o términos de uso. Escriba o pegue los términos en el cuadro grande **Términos de uso** . El límite de caracteres para las condiciones de uso legales es de 1.000.000 de caracteres.

![dibujo](media/marketplace-publishing-push-to-staging/pubportal-marketingcontent-legal-01.png)

**Nota** : Para las ofertas de máquinas virtuales, cuando una oferta o SKU se almacene provisionalmente en el Portal de Azure, no podrá cambiar los campos que se indican a continuación:

* **Offer Identifier** (Identificador de oferta): Portal de publicación -> Máquinas virtuales -> Select your Offer (Seleccionar oferta) -> pestaña Imágenes de VM -> Offer Identifier (Identificador de oferta)
* **SKU Identifier** (Identificador de SKU): Portal de publicación -> Máquinas virtuales -> Select your Offer (Seleccionar oferta) -> pestaña SKUs (SKU) -> Offer Identifier (Identificador de oferta)
* **Publisher Namespace** (Espacio de nombres de publicador): Portal de publicación -> Máquinas virtuales -> pestaña Walkthrough (Tutorial) -> Tell Us About Your Company (Indíquenos la información de su empresa) (esta opción se encuentra en el paso 2 sobre registro de la empresa) -> Publisher Namespace (Espacio de nombres de publicador) -> Espacio de nombres

Para las ofertas de máquinas virtuales, cuando una oferta o SKU se muestre en Azure Marketplace, no podrá cambiar los campos que se indican a continuación:

* **Offer Identifier** (Identificador de oferta): Portal de publicación -> Máquinas virtuales -> Select your Offer (Seleccionar oferta) -> Imágenes de VM -> Offer Identifier (Identificador de oferta)
* **SKU Identifier** (Identificador de SKU): Portal de publicación -> Máquinas virtuales -> Select your Offer (Seleccionar oferta) -> pestaña SKUs (SKU) -> Offer Identifier (Identificador de oferta)
* **Publisher Namespace** (Espacio de nombres de publicador): Portal de publicación -> Máquinas virtuales -> pestaña Walkthrough (Tutorial) -> Tell Us About Your Company (Indíquenos la información de su empresa) (esta opción se encuentra en el paso 2 sobre registro) -> Publisher Namespace (Espacio de nombres de publicador) -> Espacio de nombres
* **Puertos**: Portal de publicación -> Máquinas virtuales -> Select your Offer (Seleccionar oferta) -> pestaña Imágenes de VM -> Open Ports (Abrir puertos)
* **Pricing Change of listed SKU(s)**
* **Billing Model Change of listed SKU(s)**
* **Removal of billing regions of listed SKU(s)**
* **Changing the data disk count of listed SKU(s) (Cambio del número de discos de datos de las SKU activas)**

## <a name="step-2-set-your-prices"></a>Paso 2: Establecimiento de precios
### <a name="pricing-models"></a>Modelos de precios
| Modelo de precios | Descripción |
| --- | --- |
| Base |Tarifa plana mensual que se paga en el momento de la compra; por ejemplo, 10 $/mes |
| Consumo (también conocido como "medidor de uso") |Pago por uso, que define el publicador de la oferta. No se puede definir el superávit por puesto, por usuario, etc., ya que no existe un concepto de fracción de usuario ni la posibilidad de prorratear. El asociado informa del uso cada hora. El cliente paga al final del ciclo de facturación mensual en lugar de hacerlo por adelantado como en los planes mensuales. |
| Evaluación gratuita |El cliente puede usarla de forma gratuita durante un tiempo limitado y pagará tarifas normales a partir de entonces. |
| Nivel Gratis |El plan es siempre gratuito. |
| Migración (también conocido como "actualización" o "degradación") del plan |Concepto de un usuario que traslada su plan actual a otro plan aceptable; definido por el asociado. |

**Modelos de precios disponibles por tipo de oferta**

> [!IMPORTANT]
> La disponibilidad de ciertos modelos de precios varía según el tipo de la oferta. Consulte la siguiente tabla.
> 
> 

|  | Solo base | Solo consumo | Base y consumo |
| --- | --- | --- | --- |
| Imagen de máquina virtual |No |Sí |No |
| Servicio de desarrolladores |Sí |Sí |Sí |

### <a name="21-set-your-vm-prices"></a>2.1. Establecimiento de los precios de máquina virtual
En estos momentos, existen los **3 tipos de modelos de facturación**

* **Por hora** : A los clientes se les cobra por hora según las tarifas que hayan establecido los publicadores por los tamaños de máquina virtual. En el caso del modelo de **facturación horaria** de las SKU, el precio total será la suma de los costos de software que cobra el publicador y del costo de infraestructura que cobra Microsoft. Este costo total se mostrará al cliente como precio mensual y por hora cuando se estén planteando realizar la compra (consulte la siguiente captura de pantalla). **El publicador recibe el 80 % del costo de software que cobre.** Por lo tanto, realice el cálculo en consecuencia antes de establecer los precios de sus SKU.
  
    ![dibujo](media/marketplace-publishing-push-to-staging/img2.1-01.png)
* **Prueba gratuita** : se trata de otro tipo de modelo por hora. Al cliente no se le cobra el costo de software durante los primeros 30 días (gratis) después de implementar la máquina virtual. Cuando transcurra este periodo, se les cobra por hora según las tarifas que hayan establecido los publicadores por los tamaños de máquina virtual.
* **Traiga su propia licencia (BYOL)** : los publicadores administran las licencias del software que se ejecuta en la máquina virtual.

**Importante** : Cuando una oferta o SKU se muestre en Azure Marketplace, no podrá cambiar los campos que se indican a continuación.

* **Pricing Change of listed SKU(s) (Cambio de precios de las SKU activas)**
* **Billing Model Change of listed SKU(s)**
* **Removal of billing regions of listed SKU(s)**
* **Changing the data disk count of listed SKU(s)**
* **Offer Identifier** (Identificador de oferta): Portal de publicación -> Máquinas virtuales -> Select your Offer (Seleccionar oferta) -> Imágenes de VM -> Offer Identifier (Identificador de oferta)
* **SKU Identifier** (Identificador de SKU): Portal de publicación -> Máquinas virtuales -> Select your Offer (Seleccionar oferta) -> pestaña SKUs (SKU) -> Offer Identifier (Identificador de oferta)
* **Publisher Namespace** (Espacio de nombres de publicador): Portal de publicación -> Máquinas virtuales -> pestaña Walkthrough (Tutorial) -> Tell Us About Your Company (Indíquenos la información de su empresa) (esta opción se encuentra en el paso 2 sobre registro) -> Publisher Namespace (Espacio de nombres de publicador) -> Espacio de nombres
* **Puertos**: Portal de publicación -> Máquinas virtuales -> Select your Offer (Seleccionar oferta) -> pestaña Imágenes de VM -> Open Ports (Abrir puertos)

### <a name="sell-to-countries-of-the-sku"></a>Países a los que se vende la SKU
Debe ser cauteloso a la hora de poner a disposición del público sus SKU. Algunos países se clasifican como Microsoft remit (Envío por parte de Microsoft) y otros como ISV remit (Envío por parte del ISV).

* En los países Microsoft remit (Envío por parte de Microsoft), Microsoft recauda los impuestos de los clientes y los devuelve al gobierno.
* En los países ISV remit (Envío por parte del ISV), los asociados son los responsables de recaudar los impuestos de los clientes de impuestos y de devolverlos al gobierno. Si decide vender en países con esta clasificación, debe tener la capacidad de calcular y pagar los impuestos en los países que seleccione.

> [!NOTE]
> La SKU no estará disponible en los países hasta que no establezca sus precios en el [Portal de publicación](https://publish.windowsazure.com). A continuación, encontrará instrucciones para establecer los precios de cada SKU que se facture con los modelos por hora o BYOL.
> 
> 

### <a name="211-how-to-setup-hourly-pricing-model-for-a-sku"></a>2.1.1 Configuración del modelo de precios por hora de una SKU
Siga los pasos indicados a continuación para configurar el modelo de precios por hora en sus SKU:

1. Inicie sesión en el [Portal de publicación](https://publish.windowsazure.com).
2. Vaya a la pestaña **MÁQUINAS VIRTUALES** y seleccione la oferta.
3. En el menú del lado izquierdo, haga clic en la pestaña **SKUs** (SKU).
4. Asegúrese de que la SKU está marcada como Hourly Billing Model (Modelo de facturación por hora). En caso contrario, haga clic en el botón **EDITAR** para revertir el modelo de facturación. Se mostrará una ventana. Desactive la casilla de verificación Billing and licensing is done externally from Azure (aka Bring Your Own License) (La facturación y las licencias no se administran en Azure [Traiga su propia licencia]) y guarde los cambios.
5. Si quiere habilitar el modelo Prueba gratuita durante los primeros 30 días después de la implementación de SKU, seleccione la opción Un mes en la pregunta Is a free trial available? (¿Hay disponible una evaluación gratuita?) De lo contrario, seleccione la opción No Trial (Sin evaluación). Ahora, siga los pasos indicados a continuación.
6. En el menú del lado izquierdo, haga clic en la pestaña **PRECIOS** .
7. Seleccione la región base.
   
   ![dibujo](media/marketplace-publishing-push-to-staging/img2.1.1_07.png)
8. Establezca los precios de todos los núcleos. **Debe proporcionar el precio de todos los núcleos de una SKU, aunque no sea compatible con ella.**
   
    ![dibujo](media/marketplace-publishing-push-to-staging/img2.1.1_08.png)
9. Establezca los precios de las demás regiones manualmente; también puede hacerlo con el asistente de precios automáticos, que los fijará según la región base. Para usar este asistente, haga clic en el botón **AUTOPRICE OTHER MARKETS BASED ON PRICES IN UNITED STATES** (APLICAR AUTOMÁTICAMENTE LOS PRECIOS PARA ESTADOS UNIDOS A OTROS MERCADOS). **Nota**: La etiqueta del botón puede variar en función de la región que haya seleccionado. Como seleccionamos Estados Unidos al crear este documento, la etiqueta del botón es Auto price other markets based on prices in United States (Aplicar automáticamente los precios para Estados Unidos a otros mercados) en la captura de pantalla de abajo.
   
   ![dibujo](media/marketplace-publishing-push-to-staging/img2.1.1_09.png)
10. Se abrirá el asistente de precios automáticos. La primera página muestra la selección para el mercado base. Elija una opción y pase a la página siguiente haciendo clic en el botón ->.
    
    ![dibujo](media/marketplace-publishing-push-to-staging/img2.1.1_10.png)
11. La opción para seleccionar los planes y núcleos se mostrará en la página 2. Seleccione los planes que quiera y haga clic en el botón "->". Haga clic en el botón **Toggle All** (Alternar todo) para seleccionar todos los **planes de servicio** y **metros**; también puede activar manualmente las casillas. **Debe proporcionar el precio de todos los núcleos de una SKU, aunque no sea compatible con ella.** Por lo tanto, asegúrese de que estén seleccionados todos los tamaños de núcleo.
    
    ![dibujo](media/marketplace-publishing-push-to-staging/img2.1.1_11.png)
12. En la página 3 se muestran las regiones y los mercados. Haga clic en el botón **Toggle All** (Alternar todo) para seleccionar todas las regiones o active manualmente las casillas de la región que le corresponda. Haga clic en el botón "->" para ir a la página siguiente. **Nota** : Los países en los que Microsoft se encarga del pago de impuestos se indican con un icono similar a una casa. Para obtener más información, consulte la sección "Países a los que se vende la SKU" de esta página.
    
    ![dibujo](media/marketplace-publishing-push-to-staging/img2.1.1_12.png)
13. En la página 4 se muestran las tasas de cambio. Haga clic en el botón Finalizar para completar los pasos.

### <a name="212-how-to-setup-byol-pricing-model-for-a-sku"></a>2.1.2 Configuración del modelo de precios BYOL de una SKU
Siga los pasos indicados a continuación para configurar el modelo de precios BYOL en sus SKU:

1. Inicie sesión en el [Portal de publicación](https://publish.windowsazure.com).
2. Vaya a la pestaña **MÁQUINAS VIRTUALES** y seleccione la oferta.
3. En el menú del lado izquierdo, haga clic en la pestaña **SKUs** (SKU).
4. Asegúrese de que la SKU esté marcada como Bring your own license SKU (SKU Traiga su propia licencia). En caso contrario, haga clic en el botón EDITAR para revertir el modelo de facturación. Se mostrará una ventana. Active la casilla de verificación Billing and licensing is done externally from Azure (aka Bring Your Own License) (La facturación y las licencias no se administran en Azure [Traiga su propia licencia]) y guarde los cambios.
   
   ![dibujo](media/marketplace-publishing-push-to-staging/img2.1.2_04.png)
5. En el menú del lado izquierdo, haga clic en la pestaña **PRECIOS** .
6. Seleccione la región base y haga que la SKU esté disponible en la región activando la casilla de dicha SKU de la sección EXTERNALLY-LICENSED (BYOL) SKU AVAILABILITY (DISPONIBILIDAD DE SKU CON LICENCIA EXTERNA [BYOL]) (consulte la siguiente captura de pantalla).
   
   ![dibujo](media/marketplace-publishing-push-to-staging/img2.1.2_06.png)
7. Haga que la SKU esté disponible en las demás regiones manualmente; también puede usar el asistente de precios automáticos para este propósito. Consulte los puntos del 9 al 13 (donde se explica cómo usar el asistente de precios automático) de la sección **2.1.1 Configuración del modelo de precios por hora de una SKU** de esta página.

### <a name="22-set-your-developer-service-prices"></a>2.2. Establecer precios de servicio para desarrolladores
Los planes pueden ser cualquier combinación de base y consumo, donde base es precio mensual y superávit es el precio de pago por uso. (Vea a continuación para obtener más información).

**Ejemplo:** oferta de servicio de desarrolladores de Contoso

| Plan | Precio | Incluye | Ruta de migración |
| --- | --- | --- | --- |
| Gratuito |0 $/mes |Funcionalidad básica |Puede migrar a cualquier otro plan. |
| Bronze |10 $/mes |La funcionalidad básica y una cuota de 1.000 de la característica X. |Puede migrar a los planes Bronze Plus, Silver y Gold. |
| Bronze Plus |Período de evaluación gratuita: 0 $/mes + 0 $/meter01 |La funcionalidad básica y una cuota de 10 000 de la característica X. Cuando se consume la cuota de la característica X, el cliente puede pagar por uso a través de meter01. |Puede migrar a los planes Silver Plus y Gold. |
| Bronze Plus |Periodo pago (también conocido como "caducidad de evaluación gratuita"): 10 $/mes + 0,05 $/meter01. |La funcionalidad básica y una cuota de 10 000 de la característica X. Cuando se consume la cuota de la característica X, el cliente puede pagar por uso a través de meter01. |Puede migrar a los planes Silver Plus y Gold. |
| Silver |$ 0,15/meter01 |El cliente puede pagar por uso a través de meter01, que es para la característica X. |Puede migrar a los planes Bronze y Gold. |
| Silver Plus |20 $/mes + 0,15 $/meter01 + 0,01 dólares/meter02 |La funcionalidad básica y una cuota de 10 000 de la característica X y de 100 de la característica Y. Cuando se consume la cuota de la característica X, el cliente puede pagar por uso a través de meter01.  Cuando se consume la cuota de la característica Y, el cliente puede pagar por uso a través de meter02. |Puede migrar a los planes Bronze Plus y Gold. |
| Gold |1.000 $/mes |Cuota de 10.000 de la característica X, 1.000 de la característica Y e ilimitada de la característica Z. |Puede migrar a todos los planes excepto el Gratis. |

## <a name="step-3-provide-support-information"></a>Paso 3: Especificación de información de soporte técnico
Los detalles de contacto solo se usan para comunicaciones internas entre asociados y Microsoft. La dirección URL de soporte técnico estará disponible para los usuarios finales.

1. Vaya al encabezado **Soporte técnico** de la izquierda del portal de publicación.
2. Escriba la información de **Contacto de ingeniería**.
3. Escriba la información de **Asistencia al cliente**. Si solo proporciona soporte técnico por correo electrónico, escriba un número de teléfono ficticio, así se utilizará su dirección de correo electrónico.
4. Escriba la dirección URL de soporte técnico.

## <a name="step-4-choose-azure-marketplace-categories"></a>Paso 4: Elección de categorías de Azure Marketplace
La pestaña **Categorías** proporciona una matriz de selecciones. Su oferta puede estar dentro de estas y es posible seleccionar hasta cinco categorías.

## <a name="how-your-marketing-will-appear"></a>Apariencia del marketing
A continuación, se muestra una descripción detallada de cómo se utiliza la información de marketing de la oferta en el [sitio web de Azure Marketplace](https://azure.microsoft.com/marketplace/) y en el [Azure Portal](https://portal.azure.com).

### <a name="azure-marketplace-website"></a>Sitio web de Azure Marketplace
![dibujo](media/marketplace-publishing-push-to-staging/acom-catalog-01.png)

![dibujo](media/marketplace-publishing-push-to-staging/acom-catalog-02.png)

*Lista de las ofertas en el sitio web de Azure Marketplace*

![dibujo](media/marketplace-publishing-push-to-staging/acom-listing-details-01.png)

*Detalles de descripción de la oferta en el sitio web de Azure Marketplace*

![dibujo](media/marketplace-publishing-push-to-staging/acom-listing-details-02.png)

*Detalles de precios de la descripción de la oferta en el sitio web de Azure Marketplace*

### <a name="azure-portal"></a>Portal de Azure
![dibujo](media/marketplace-publishing-push-to-staging/azureportal-galleryblade-01.png)

*Lista de ofertas en el Portal de Azure*

![dibujo](media/marketplace-publishing-push-to-staging/azureportal-galleryblade-02.png)

*Detalles de descripción de la oferta en el Portal de Azure*

## <a name="next-steps"></a>Pasos siguientes
Ahora que el contenido de Marketplace está cargado, avanzamos a la prueba de la oferta en ensayo. Sin embargo, debe seleccionar el tipo de la oferta adecuado en la lista siguiente, ya que los pasos varían según el tipo de oferta.

* [Prueba de la oferta de máquina virtual en el entorno de ensayo](marketplace-publishing-vm-image-test-in-staging.md)
* [Prueba de la oferta de plantilla de solución en el entorno de ensayo](marketplace-publishing-solution-template-test-in-staging.md)

## <a name="see-also"></a>Consulte también
* [Introducción: Publicación de una oferta en Azure Marketplace](marketplace-publishing-getting-started.md)

[img-map-acom]:media/marketplace-publishing-push-to-staging/pubportal-mapping-acom.jpg
[img-map-portal]:media/marketplace-publishing-push-to-staging/pubportal-mapping-azure-portal.jpg
[img-map-link]:media/marketplace-publishing-push-to-staging/marketing-content-guide-links.jpg
[img-map-logo]:media/marketplace-publishing-push-to-staging/marketing-content-guide-logos.jpg
[img-map-title]:media/marketplace-publishing-push-to-staging/marketing-content-guide-publisher-offer.png

[link-pubportal]:https://publish.windowsazure.com
[link-push-to-production]:marketplace-publishing-push-to-production.md
