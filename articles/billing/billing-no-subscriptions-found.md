---
title: "Error No se encontraron suscripciones al intentar iniciar sesión en Azure Portal o en el Centro de cuentas de Azure | Microsoft Docs"
description: "Proporciona la solución para un problema en el que se produce un error del tipo No se encontraron suscripciones al iniciar sesión en Azure Portal o en el Centro de cuentas de Azure."
services: 
documentationcenter: 
author: genlin
manager: jlian
editor: 
tags: billing
ms.assetid: d1545298-99db-4941-8e97-f24a06bb7cb6
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 08/30/2017
ms.author: genli
ms.openlocfilehash: 19f27aa81d7b5e03828f18f5a38cf3362df17694
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="no-subscriptions-found-error-in-azure-portal-or-azure-account-center"></a>Error No se encontraron suscripciones en Azure Portal o en el Centro de cuentas de Azure

Puede recibir un mensaje de error "No se encontraron suscripciones" cuando intente iniciar sesión en [Azure Portal](https://portal.azure.com/) o en el [Centro de cuentas de Azure](https://account.windowsazure.com/Subscriptions). En este artículo se brinda una solución para este problema.

## <a name="symptom"></a>Síntoma

Cuando intenta iniciar sesión en [Azure Portal](https://portal.azure.com/) o en el [Centro de cuentas de Azure](https://account.windowsazure.com/Subscriptions), recibe el mensaje de error siguiente: "No se encontraron suscripciones".

## <a name="cause"></a>Causa

Este problema se produce si seleccionó el directorio equivocado, o si la cuenta no tiene permisos suficientes. 

## <a name="solution"></a>Solución

### <a name="scenario-1-error-message-is-received-in-the-azure-portalhttpsportalazurecom"></a>Escenario 1: Se recibe el mensaje de error en [Azure Portal](https://portal.azure.com)

Para corregir este problema:

* Asegúrese de que se seleccionó el directorio correcto de Azure haciendo clic en la cuenta pertinente en la esquina superior derecha.

  ![Selección del directorio en la parte superior derecha de Azure Portal](./media/billing-no-subscriptions-found/directory-switch.png)
* Si se seleccionó el directorio correcto de Azure pero sigue recibiendo el mensaje de error, [agregue la cuenta como propietario](billing-add-change-azure-subscription-administrator.md).

### <a name="scenario-2-error-message-is-received-in-the-azure-account-centerhttpsaccountwindowsazurecomsubscriptions"></a>Escenario 2: se recibe el mensaje de error en el [Centro de cuentas de Azure](https://account.windowsazure.com/Subscriptions)

Compruebe si la cuenta que ha usado es el administrador de cuentas. Para comprobar quién es el administrador de cuentas, siga estos pasos:

1. Inicie sesión en la [vista de suscripciones de Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
1. Seleccione la suscripción que desee comprobar y, luego, consulte **Configuración**.
1. Seleccione **Propiedades**. El administrador de cuentas de la suscripción se muestra en el cuadro **Administrador de cuentas** .  

## <a name="need-help-contact-support"></a>¿Necesita ayuda? Póngase en contacto con el servicio de soporte técnico.

Si sigue necesitando ayuda, [póngase en contacto con el servicio de soporte técnico](http://go.microsoft.com/fwlink/?linkid=544831&clcid=0x409) para resolver el problema rápidamente. 
