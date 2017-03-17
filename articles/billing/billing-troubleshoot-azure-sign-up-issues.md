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
ms.date: 03/01/2017
ms.author: cjiang
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 459d0526961d03b564a35178f9ff4ff2e343e08c
ms.openlocfilehash: 70a9d96e36f5e386e8b1c0036ef8e2f770924930
ms.lasthandoff: 03/02/2017

---
# <a name="troubleshoot-sign-up-issues-for-azure"></a>Solucionar problemas de inicio de suscripción para Azure
Si no puede suscribirse a Azure, hay varias cosas que puede comprobar para solucionar el problema.

## <a name="progress-bar-hangs-in-identity-verification-by-card-section"></a>La barra de progreso se bloquea en la sección "Comprobación de identidad por tarjeta"

En el proceso de registro Azure, hay una sección denominada "Comprobación de identidad por tarjeta". Si se bloquea la barra de progreso:

![Captura de pantalla de la sección de comprobación de identidad por tarjeta quedándose bloqueada durante el registro](./media/billing-troubleshoot-azure-sign-up-issues/identity-verification-hangs.PNG)

Este problema se produce cuando se bloquean las cookies de terceros para su explorador.

### <a name="suggestion"></a>Sugerencia

1. Permita las cookies de terceros en la configuración del explorador.
  * En Edge, vaya a Configuración -> Ver configuración avanzada -> Cookies y seleccione "No bloquear cookies".
  * En Chrome, vaya a Configuración-> Mostrar configuración avanzada-> Privacidad-> Configuración de contenido y desactive la casilla "Bloquear los datos de sitios y las cookies de terceros".
2. Actualice la página de registro en Azure y compruebe si se ha resuelto el problema.
3. Si la actualización no soluciona el problema, salga y reinicie el explorador e inténtelo de nuevo.

## <a name="no-text-messages-or-calls-during-sign-up-account-verification"></a>No hay mensajes de texto o llamadas durante la comprobación de la cuenta de suscripción
* El código de texto puede tardar hasta cuatro minutos en entregarse.
* Compruebe que su número de teléfono puede recibir SMS.
* Repase el número de teléfono especificado, incluyendo la selección de código de país en el menú desplegable.
* Asegúrese de que el teléfono puede recibir mensajes de texto (SMS) si usa la opción "Enviar mensaje de texto" o llamadas de teléfono si elige la alternativa "Llamarme".
* Cuando reciba el mensaje de texto, inserte el código en el cuadro de texto y haga clic en el botón de comprobación para continuar.

### <a name="suggestions"></a>Sugerencias
* Si no recibe mensajes de texto (SMS) en el teléfono, use el método de verificación alternativo "Llamarme".
* Si no se puede realizar el paso de comprobación telefónica con los métodos de SMS y "Llamarme", utilice otro número de teléfono.
* Para el proceso de comprobación telefónica no se puede usar un número de teléfono VOIP.

## <a name="credit-card-declined-or-not-accepted"></a>Tarjeta de crédito rechazada o no aceptada
Asegúrese de que el método de pago que usa en la suscripción es uno compatible. También puede obtener más información sobre por qué [la tarjeta de débito o crédito se rechazó al suscribirse a Azure](billing-credit-card-fails-during-azure-sign-up.md).

## <a name="free-trial-is-not-available"></a>"La prueba gratuita no está disponible."
¿Ha utilizado una suscripción de Azure en el pasado? El contrato de términos de uso de Azure limita la activación de la evaluación gratuita solo para un usuario que es nuevo en Azure. Si tiene cualquier otro tipo de suscripción de Azure, no puede activar una evaluación gratuita.

### <a name="suggestion"></a>Sugerencia
* Considere la posibilidad de suscribirse a una [suscripción de pago por uso](https://azure.microsoft.com/offers/ms-azr-0003p/).

## <a name="cant-activate-azure-benefit-plan-like-msdn-bizspark-bizsparkplus-or-mpn"></a>No se puede activar un plan de prestaciones de Azure como MSDN, BizSpark, BizSparkPlus o MPN
Asegúrese de que está usando las credenciales de inicio de sesión correctas y compruebe en el canal de programa de prestaciones si tiene derecho al plan elegido:

* MSDN
  * Compruebe su estado de elegibilidad en la [página de la cuenta de MSDN](https://msdn.microsoft.com/subscriptions/manage/default.aspx).
  * Si no puede comprobar su estado, póngase en contacto con los [Centros de servicio al cliente de Suscripciones a MSDN](https://msdn.microsoft.com/subscriptions/contactus.aspx)
* MPN
  * Inicie sesión en el [portal de MPN](https://mspartner.microsoft.com/en/us/Pages/Locale.aspx) y compruebe su estado de idoneidad. Si tiene las [competencias de la plataforma de nube](https://mspartner.microsoft.com/en/us/pages/membership/cloud-platform-competency.aspx) pertinentes, puede ser apto para obtener más ventajas.
  * Si no puede comprobar su estado, póngase en contacto con el [soporte técnico de MPN](https://mspartner.microsoft.com/en/us/Pages/Support/Premium/contact-support.aspx).
* BizSpark
  * Inicie sesión en el [portal de BizSpark](https://www.microsoft.com/bizspark/default.aspx#start-two) y compruebe su estado de idoneidad para BizSpark y BizSpark Plus.
  * Si no puede comprobar el estado, puede [ponerse en contacto con el equipo de BizSpark](mailto:bizspark@microsoft.com?subject=BizSpark%20Support&body=Thank%20you%20for%20contacting%20BizSpark.%20Please%20provide%20as%20much%20of%20the%20following%20information%20as%20possible,%20as%20it%20will%20help%20expedite%20our%20response%20to%20you.%0aContact%20name:%0aStartup%20name:%0aMicrosoft%20Account/Live%20ID:%0aSpecific%20description%20of%20issue%20experienced%20or%20question:%0a%0aThank%20you,%0a%0aThe%20BizSpark%20Team).

## <a name="cant-activate-new-azure-in-open-subscription"></a>No se puede activar una nueva suscripción de Azure bajo licencia Open
Para activar una nueva suscripción de Azure bajo licencia Open tiene que tener una clave OSA válida, con un mínimo de un token de Azure bajo licencia Open asociado a dicha clave.

### <a name="suggestion"></a>Sugerencia
Si no tiene una clave OSA, póngase en contacto con uno de los asociados de Microsoft que aparecen en [Microsoft Pinpoint](http://pinpoint.microsoft.com/).

## <a name="need-help-contact-support"></a>¿Necesita ayuda? Póngase en contacto con el servicio de soporte técnico.
Si sigue necesitando ayuda, [póngase en contacto con el servicio de soporte técnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver el problema rápidamente.

