---
title: "¿Cómo publico mi oferta en Azure Marketplace?  | Microsoft Docs"
description: "La oferta pasa por diferentes etapas para garantizar que el contenido de marketing y la imagen de VM cumplen con los requisitos de calidad para tener la certificación Azure Certified y publicarse en el sitio web."
services: marketplace-publishing
documentationcenter: 
author: rupeshazure
manager: hamidm
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/29/2017
ms.author: rupesk@microsoft.com
translationtype: Human Translation
ms.sourcegitcommit: 233d62b176303ca384aae9453c2e125c0c70fad2
ms.openlocfilehash: 4acd94e4b83164d52d2ce8db414bb99de109aca1
ms.lasthandoff: 04/12/2017


---


# <a name="make-your-virtual-machine-offer-live-on-azure-marketplace"></a>Publicación de la oferta de máquina virtual en Azure Marketplace

Ahora que ha rellenado todos los detalles de la oferta, es el momento de publicarla y hacer que esté disponible en Azure Marketplace.  La oferta pasa por diferentes etapas para garantizar que el contenido de marketing y la imagen de VM cumplen con los requisitos de calidad para tener la certificación Azure Certified y publicarse en el sitio web.


![Secuencia 0 de publicación de la oferta](./media/cloud-partner-portal-offer-go-live-azure-marketplace/makeanofferlive.png)


Analicemos este proceso más detalladamente para entender mejor lo que sucede durante cada paso y en qué puntos de este proceso debe actuar para asegurarse de que su oferta continúe progresando. 


## <a name="publishing-process"></a>Proceso de publicación

Para iniciar el proceso de publicación, hará clic en "Publicar" en la pestaña Editor. 

![Secuencia 1 de publicación de la oferta](./media/cloud-partner-portal-offer-go-live-azure-marketplace/publish.png)


En la pestaña Estado, verá los pasos de publicación y dónde se encuentra la oferta en el proceso. 


![Secuencia 2 de publicación de la oferta](./media/cloud-partner-portal-offer-go-live-azure-marketplace/status.png)

En cualquier punto del proceso de publicación, también puede iniciar sesión y hacer clic en la pestaña Todas las ofertas para ver el estado más reciente de cualquiera de las ofertas. Puede hacer clic directamente en el estado de su oferta y ver los detalles relativos al punto en que su oferta se encuentra en el proceso de publicación.

![Secuencia 3 de publicación de la oferta](./media/cloud-partner-portal-offer-go-live-azure-marketplace/alloffersstatus.png)

Vamos a analizar cada uno de los pasos de publicación, explicar lo que sucede en cada uno de ellos y examinar su duración estimada. 

**Validar los requisitos previos (< 1 día)**

Al hacer clic en "Publicar", se llevará a cabo una comprobación automatizada para verificar que ha rellenado todos los campos obligatorios de su oferta. Si no se ha completado alguno de los campos, aparecerá una advertencia junto al campo en cuestión y tendrá que rellenarlo con la información pertinente. A continuación, haga clic de nuevo en "Publicar". 


Una vez que haya completado este paso correctamente, aparecerá un elemento emergente solicitando una dirección de correo electrónico. Este será el correo electrónico en el que recibirá las notificaciones del estado de publicación para el resto del proceso de publicación. Una vez que envíe la dirección de correo electrónico, este paso se habrá completado.

![Secuencia 4 de publicación de la oferta](./media/cloud-partner-portal-offer-go-live-azure-marketplace/publishyouroffer.png)

**Certificación (< 5 días)**

En este paso se ejecutan varias pruebas para comprobar que la imagen de máquina virtual cumple los requisitos de Azure Certified. [Aquí](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation-prerequisites) se encuentran todas las instrucciones que requiere para pasar los requisitos de certificación.

Dado que este paso puede tardar varios días, puede cerrar sesión en Cloud Partner Portal. Le enviaremos una notificación por correo electrónico si hay errores que deba solucionar. Si todo se completa correctamente, el proceso pasará automáticamente al paso de aprovisionamiento. 

**Aprovisionamiento (< 1 día)**

Durante esta fase, replicaremos sus imágenes en todos los centros de datos globales de Azure, lo que puede tardar hasta un día en completarse.

**Empaquetado y registro de la generación de clientes potenciales (< 1 hora)**

Durante esta fase, combinaremos el contenido técnico y de marketing en lo que será la página del producto en el sitio web. 

Además, si ha configurado la característica de generación de clientes potenciales, se validará el funcionamiento de la integración de CRM mediante el envío de un cliente potencial de prueba a su CRM. Verá que se rellena un registro con datos falsos en el CRM o la tabla de Azure una vez completado este paso. Toda la documentación para la generación de clientes potenciales se encuentra aquí.

**Oferta disponible en vista previa**

Recibirá un correo electrónico de notificación informando de que su oferta ha completado correctamente los pasos necesarios para tener acceso a su vista previa. Durante este paso, debe obtener una vista previa de su oferta y asegurarse de que todo tiene el aspecto que debería y que la máquina virtual se implementa correctamente en el entorno de ensayo. 

**Solamente las suscripciones de la lista blanca pueden realizar esta verificación.***

**Aprobación del publicador**

Cuando haya comprobado que todo está correcto y funciona adecuadamente en la vista previa, está listo para comenzar a trabajar. Para ello, haga clic en Publicar en la pestaña Estado y darán comienzo los pasos para publicar su oferta en producción y en el sitio web. Normalmente, pasarán varias horas entre el momento en que hace clic en Publicar y la publicación efectiva de la oferta en el sitio web. Le enviaremos una notificación por correo electrónico una vez que su oferta esté oficialmente publicada en el sitio web.

![Secuencia 5 de publicación de la oferta](./media/cloud-partner-portal-offer-go-live-azure-marketplace/golive.png)

**Publicación**    

La oferta ya está publicada en Azure Marketplace y Azure Portal y los clientes podrán ver e implementar su máquina virtual en sus suscripciones de Azure.
En cualquier momento, puede hacer clic en la pestaña Todas las ofertas y ver el estado de todas las ofertas que figuran en la columna derecha. Puede hacer clic en el estado para ver la situación del proceso de publicación con todo detalle para su oferta.

## <a name="error-handling"></a>Control de errores

Durante el proceso de publicación, puede encontrar algún error. De ser así, recibirá un correo electrónico de notificación que le informa de que se produjo un error con instrucciones sobre los pasos siguientes. También puede ver errores en cualquier momento durante este proceso haciendo clic en la pestaña Estado. Verá en qué punto del proceso ocurrió el problema junto con un mensaje de error en que se describe qué debe resolverse. 

![Secuencia 6 de publicación de la oferta](./media/cloud-partner-portal-offer-go-live-azure-marketplace/errormessage.png)


Si se producen errores durante el proceso de publicación, es necesario corregirlos y, a continuación, hacer clic en "Publicar" para reiniciar el proceso. Debe empezar al principio de los pasos de publicación, en Validar los requisitos previos, para volver a publicar después de cualquier corrección de errores.

Si tiene problemas para resolver un error, debe abrir una solicitud de soporte técnico para obtener ayuda de nuestros ingenieros de soporte técnico.


![Secuencia 7 de publicación de la oferta](./media/cloud-partner-portal-offer-go-live-azure-marketplace/getsupport.png)



## <a name="canceling-the-publishing-request"></a>Cancelación de la solicitud de publicación

Puede iniciar el proceso de publicación y que se presente la necesidad de cancelar su solicitud. Solo puede cancelar una solicitud de publicación una vez que esta alcance el paso de aprobación del publicador. Para cancelar, haga clic en "Cancelar publicación". El estado de publicación volverá al paso 1 y, para volver a publicar, debe hacer clic en Publicar y seguir los pasos del estado.

![Secuencia 8 de publicación de la oferta](./media/cloud-partner-portal-offer-go-live-azure-marketplace/status5.png)














