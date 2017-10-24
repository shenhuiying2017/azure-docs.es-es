---
title: "Implementación de nuestra oferta en Azure Marketplace | Microsoft Docs"
description: "Conozca y recorra paso a paso las instrucciones para implementar su oferta (una imagen de máquina virtual, un servicio para desarrolladores, un servicio de datos, etc.) en Azure Marketplace."
services: marketplace-publishing
documentationcenter: 
author: HannibalSII
manager: hascipio
editor: 
ms.assetid: 8f79b891-84e2-4f41-ba0d-66420e2c6b2e
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/02/2016
ms.author: hascipio
ms.openlocfilehash: 12dc81642905cd9449a1032c7ab57298e6b69ba8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="deploy-your-offer-to-the-azure-marketplace"></a>Implementación de su oferta en Azure Marketplace
Cuando quede satisfecho con su oferta (es decir, haya probado escenarios de clientes, contenido de marketing, etc.) y esté listo para el lanzamiento, solicite **Enviar a producción** en la pestaña **Publicar**.  

1. Los cuatro pasos de la página WALKTHROUGH (TUTORIAL) del Portal de publicación deben completarse y mostrarse en verde. Para las ofertas de máquinas virtuales, asegúrese seguir estas instrucciones.
   
    ![dibujo][img-pubportal-walkthru-checked]
2. Seleccione la pestaña **Publicar** en la lista del lado izquierdo.
   
    ![dibujo][img-pubportal-menu-publish]
3. Haga clic en el botón **Request approval to push to production**(Solicitar aprobación para enviar a producción). Cuando se realice la solicitud, el equipo de aprobación ejecuta una revisión final y luego su oferta estará disponible en Azure Marketplace.
   
    ![dibujo][img-pubportal-publish-pushproduction]

> [!IMPORTANT]
> En el caso de las máquinas virtuales, al hacer clic en el botón Request approval (Solicitar aprobación) para enviar la oferta a producción, los siguientes pasos se realizan automáticamente. Podrá ver el progreso de cada paso en la pestaña Publicar en el Portal de publicación. Debe comprobar esta página de forma periódica (hasta que el estado muestre que está activa) para consultar cualquier información de error que tenga que solucionar.
> 
> * En primer lugar, la solicitud de producción pasa al equipo de certificación que valida el VHD. Sin embargo, si va a actualizar la oferta ya activa y la solicitud tiene solo incluye un cambio de marketing, se omite el paso de certificación.
> * En el paso siguiente, la solicitud pasa al equipo de validación del contenido que comprueba el contenido de marketing de la oferta.
> * Si los pasos anteriores se realizan correctamente, se aprueba que la oferta pase a producción. En este momento, el estado muestra que la oferta está activa en el Portal de publicación. Sin embargo, este estado no implica que el proceso se haya completado. Los pasos siguientes deben finalizarse antes de que la oferta esté disponible en Azure Marketplace.
> * Cuando la oferta se haya aprobado para enviarse a producción en el paso anterior, se inicia la replicación de la oferta en los centros de datos de Azure. Normalmente, el proceso de replicación de la oferta tarda en completarse entre 24 y 48 horas, aunque puede extenderse hasta una semana en función del tamaño del VHD. Sin embargo, si va a actualizar la oferta ya activa y la solicitud tiene solo incluye un cambio de marketing, la replicación se realiza más rápidamente.
> * Cuando se complete la replicación, la oferta se mostrará en Azure Marketplace.
> 
> Siempre se puede eliminar la oferta mientras está en estado de **borrador** (es decir, nunca **Enviar a ensayo** ni **Enviar a producción**). En la pestaña **Historial**, haga clic en el botón **Descartar borrador** que se encuentra en la parte inferior de la página para eliminar un borrador.
> 
> 

## <a name="production-checklist-for-all-virtual-machine-offers"></a>Lista de comprobación de producción para todas las ofertas de máquinas virtuales
* Asegúrese de ser asociado Microsoft Azure Certified.
* En la pestaña SKU, la opción Hide this SKU from the Marketplace because it should always be bought via a solution template (Ocultar esta SKU en Marketplace porque siempre se debe comprar mediante una plantilla de solución) se debe marcar como SÍ solo si la SKU forma parte de una plantilla de solución. En el resto de los casos, esta opción siempre se debe marcar como NO.
* Recuerde: No debe cambiar la configuración de visibilidad de la SKU una vez que esté activa. No se admite esta funcionalidad.
* Asegúrese de que los logotipos sigan las instrucciones del logotipo de Azure Marketplace que se mencionan a continuación.
* La descripción de la oferta y la SKU no debe ser la misma.
* El título de la SKU y el resumen largo de la oferta no deben ser los mismos.
* El título de la SKU y el resumen de la oferta no deben ser los mismos.
* Los títulos de la SKU no deben ser idénticos para una oferta con varias SKU.

**Instrucciones del logotipo de Azure Marketplace**

* El diseño de Azure tiene una paleta de colores simple. Utilice pocos colores primarios y secundarios en el logotipo.
* Los colores del tema del Portal de Azure son el blanco y el negro. Por lo tanto, evite emplear estos colores como fondo de los logotipos. Utilice algún color que haga destacar a los logotipos en el Portal de Azure. Nosotros recomendamos usar colores primarios simples. Si utiliza un fondo transparente, asegúrese de que el texto y los logotipos no sean de color blanco o negro.
* No utilice un fondo degradado en el logotipo.
* Evite colocar texto, incluso la empresa o el nombre de marca, en el logotipo.
* El aspecto del logotipo debe ser "plano" y debe evitar degradados.
* El logotipo no se debe extender.

**Instrucciones adicionales para el logotipo de imagen prominente:**

* El logotipo de imagen prominente es opcional. El publicador puede decidir no cargar un logotipo de imagen prominente. **Sin embargo, una vez que cargue la imagen prominente, no podrá eliminarla del Portal de publicación. A partir de ese momento, el asociado debe seguir las instrucciones de Azure Marketplace para imágenes prominentes; en caso contrario, la oferta no contará con la aprobación para pasar a producción.**
* El nombre para mostrar del publicador, el título de la SKU y el resumen largo de la oferta se muestran en color blanco. Por lo tanto, debe evitar usar colores claros en el fondo de la imagen prominente. Los fondos transparentes y de color negro y blanco no pueden utilizarse en las imágenes prominentes.
* El nombre para mostrar del publicador, el título de la SKU, el resumen largo de la oferta y el botón Crear se incrustan mediante programación dentro del logotipo de imagen prominente cuando se publica la oferta. Por lo tanto, no hace falta especificarlos cuando diseñe el logotipo de imagen prominente. Deje espacio vacío a la derecha, ya que es ahí donde incluiremos, mediante programación, el texto (es decir, el nombre para mostrar del publicador, el título del SKU y el resumen largo de la oferta). El espacio vacío para el texto debe tener un tamaño de 415 x 100 a la derecha (con un desplazamiento de 370 píxeles a la izquierda).

## <a name="additional-production-checklist-for-already-listed-virtual-machine-offers"></a>Lista de comprobación de producción adicional para ofertas de máquinas virtuales ya activas
* Compruebe si ya hay una oferta con el mismo nombre de la oferta de su compañía. En caso afirmativo, debe agregar una nueva versión de la SKU en la oferta existente en lugar de crear una nueva oferta duplicada.
* El disco de datos no debe cambiar entre dos versiones de la misma SKU.
* Azure Marketplace no admite el cambio de precios de las SKU activas puesto que afecta a la facturación de los clientes existentes. Asegúrese de no cambiar el precio de las SKU activas en las regiones donde estén disponibles. Sin embargo, puede agregar nuevas SKU o regiones a una SKU existente.

## <a name="next-steps"></a>Pasos siguientes
Cuando se ponga en marcha la oferta, realice pruebas de los escenarios de cliente para comprobar que todos los contratos y la funcionalidad funcionen correctamente en el entorno de producción, tal como se comprobó y validó en el entorno de ensayo.

## <a name="see-also"></a>Consulte también
* [Introducción: Publicación de una oferta en Azure Marketplace](marketplace-publishing-getting-started.md)

[img-pubportal-walkthru-checked]:media/marketplace-publishing-push-to-production/pubportal-walkthru-checked.png
[img-pubportal-menu-publish]:media/marketplace-publishing-push-to-production/pubportal-menu-publish.png
[img-pubportal-publish-pushproduction]:media/marketplace-publishing-push-to-production/pubportal-publish-pushproduction.png
