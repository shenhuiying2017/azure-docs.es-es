---
title: "Resolución de problemas de suscripción a Azure | Microsoft Docs"
description: "Describe cómo solucionar algunos problemas comunes de suscripción a Azure."
services: 
documentationcenter: 
author: JiangChen79
manager: adpick
editor: 
tags: billing,top-support-issue
ms.assetid: a0907da1-cb2d-41d1-a97f-43841fabe355
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 07/05/2017
ms.author: cjiang
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: bb794ba3b78881c967f0bb8687b1f70e5dd69c71
ms.openlocfilehash: 647509ea36e487aca5db661adb3268e845988f78
ms.contentlocale: es-es
ms.lasthandoff: 07/06/2017

---
# <a name="troubleshoot-sign-up-issues-for-azure"></a>Solucionar problemas de inicio de suscripción para Azure
Si no puede suscribirse a Azure, siga las sugerencias de este artículo para solucionar problemas comunes. Si tiene algún problema con la tarjeta de crédito durante el inicio de sesión, vea [La tarjeta de débito o crédito se rechazó al suscribirse a Azure](billing-credit-card-fails-during-azure-sign-up.md). Si tiene una cuenta de Azure, pero no puede iniciar sesión, vea [No puedo iniciar sesión para administrar mi suscripción de Azure](billing-cannot-login-subscription.md).

## <a name="progress-bar-hangs-in-identity-verification-by-card-section"></a>La barra de progreso se bloquea en la sección "Comprobación de identidad por tarjeta"

Para completar la verificación de identidad mediante tarjeta, se deben permitir las cookies de terceros en explorador.

![Captura de pantalla de la sección de comprobación de identidad por tarjeta quedándose bloqueada durante el registro](./media/billing-troubleshoot-azure-sign-up-issues/identity-verification-hangs.PNG)

Utilice los pasos siguientes para actualizar la configuración de las cookies del explorador.

1. Si usa Chrome, vaya a **Configuración** > **Mostrar configuración avanzada** > **Privacidad** > **Configuración de contenido**. Desactive la opción **Bloquear los datos de sitios y las cookies de terceros**.
2. Si usa Edge, vaya a **Configuración** > **Ver configuración avanzada** > **Cookies**. Seleccione **No bloquear cookies**.
3. Actualice la página de registro en Azure y compruebe si se ha resuelto el problema.
4. Si la actualización no soluciona el problema, salga y reinicie el explorador e inténtelo de nuevo.

## <a name="credit-card-form-doesnt-support-my-billing-address"></a>Incompatibilidad del formulario de tarjeta de crédito con la dirección de facturación
La dirección de facturación debe estar en el país que seleccionó en la sección **Acerca de usted**. Asegúrese de seleccionar el país correcto.

## <a name="no-text-messages-or-calls-during-sign-up-account-verification"></a>No hay mensajes de texto o llamadas durante la comprobación de la cuenta de suscripción
Aunque suele ser mucho más rápido, la entrega del código de verificación puede tardar hasta cuatro minutos. El número de teléfono especificado a efectos de verificación no se guarda como un número de contacto para la cuenta.

Estas son algunas sugerencias adicionales:
* Para el proceso de verificación telefónica no se puede usar un número de teléfono VoIP.
* Revise el número de teléfono especificado, incluido el código de país seleccionado en el menú desplegable.
* Si el teléfono no recibe mensajes de texto (SMS), pruebe la opción **Llamarme**.
* Asegúrese de que su teléfono puede recibir llamadas o mensajes SMS desde un número de Estados Unidos.

Cuando reciba el mensaje de texto o la llamada telefónica, escriba en el cuadro de texto el código proporcionado.

## <a name="credit-card-declined-or-not-accepted"></a>Tarjeta de crédito rechazada o no aceptada
Las tarjetas de crédito o débito virtuales o de prepago no se aceptan como opciones de pago para suscripciones de Azure. Para saber qué otros motivos pueden dar lugar a que se rechace la tarjeta, vea [La tarjeta de débito o crédito se rechazó al suscribirse a Azure](billing-credit-card-fails-during-azure-sign-up.md).

## <a name="free-trial-is-not-available"></a>"La prueba gratuita no está disponible."
¿Ha utilizado una suscripción de Azure en el pasado? El contrato de términos de uso de Azure limita la activación de la evaluación gratuita solo para un usuario que es nuevo en Azure. Si tiene cualquier otro tipo de suscripción de Azure, no puede activar una evaluación gratuita. Considere la posibilidad de suscribirse a una [suscripción de pago por uso](https://azure.microsoft.com/offers/ms-azr-0003p/).

## <a name="i-saw-a-charge-on-my-free-trial-account"></a>Hay un cargo reflejado en la cuenta de evaluación gratuita
Es posible que vea reflejado un cargo de verificación mínimo en la tarjeta de crédito después de registrarse, que desaparecerá en un plazo de 3 a 5 días. Si le preocupan los costos de administración, lea más información en la página sobre [prevención de costos inesperados](https://docs.microsoft.com/azure/billing/billing-getting-started).

## <a name="cant-activate-azure-benefit-plan-like-msdn-bizspark-bizsparkplus-or-mpn"></a>No se puede activar un plan de prestaciones de Azure como MSDN, BizSpark, BizSparkPlus o MPN
Asegúrese de usar las credenciales de inicio de sesión correctas. A continuación, compruebe el programa de beneficios para asegurarse de que puede optar a él. 

* MSDN
  * Compruebe su estado de elegibilidad en la [página de la cuenta de MSDN](https://msdn.microsoft.com/subscriptions/manage/default.aspx).
  * Si no puede verificar su estado, póngase en contacto con los [Centros de servicio al cliente de Suscripciones a MSDN](https://msdn.microsoft.com/subscriptions/contactus.aspx).
* BizSpark
  * Inicie sesión en el [portal de BizSpark](https://www.microsoft.com/bizspark/default.aspx#start-two) y compruebe su estado de idoneidad para BizSpark y BizSpark Plus.
  * Si no puede verificar su estado, puede [obtener ayuda en los foros de BizSpark](http://aka.ms/bzforums).
* MPN
  * Inicie sesión en el [portal de MPN](https://mspartner.microsoft.com/en/us/Pages/Locale.aspx) y compruebe su estado de idoneidad. Si tiene las [competencias de la plataforma de nube](https://mspartner.microsoft.com/en/us/pages/membership/cloud-platform-competency.aspx) pertinentes, puede ser apto para obtener más ventajas.
  * Si no puede verificar el estado, póngase en contacto con el [soporte técnico de MPN](https://mspartner.microsoft.com/en/us/Pages/Support/Premium/contact-support.aspx).

## <a name="cant-activate-new-azure-in-open-subscription"></a>No se puede activar una nueva suscripción de Azure bajo licencia Open
Para crear una suscripción de Azure bajo licencia Open, debe contar con una clave válida de Activación del servicio en línea (OSA) que tenga asociado al menos un token de Azure bajo licencia Open. Si no tiene una clave OSA, póngase en contacto con alguno de los asociados de Microsoft que aparecen en [Microsoft Pinpoint](http://pinpoint.microsoft.com/).

## <a name="need-help-contact-support"></a>¿Necesita ayuda? Póngase en contacto con el servicio de soporte técnico.
Si sigue necesitando ayuda, [póngase en contacto con el servicio de soporte técnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver el problema rápidamente.

