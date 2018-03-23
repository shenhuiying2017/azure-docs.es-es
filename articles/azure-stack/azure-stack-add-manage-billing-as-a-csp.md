---
title: Administración del uso y la facturación para Azure Stack como un proveedor de servicios en la nube | Microsoft Docs
description: Tutorial sobre cómo registrar Azure Stack como proveedor en la nube y cómo agregar clientes.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/27/2018
ms.author: mabrigg
ms.reviewer: alfredo
ms.openlocfilehash: 23e3a675e6a464c92d26df220c8126c970f590a0
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/12/2018
---
# <a name="manage-usage-and-billing-for-azure-stack-as-a-cloud-service-provider"></a>Administración del uso y la facturación para Azure Stack como un proveedor de servicios en la nube 

*Se aplica a: sistemas integrados de Azure Stack*

Este artículo le guía por el registro de Azure Stack como proveedor en la nube (CSP) y la adición de clientes.

Como CSP, es probable que tenga muchos clientes diferentes que utilizan Azure Stack. Cada cliente tiene una suscripción CSP en Azure, y usted necesitará dirigir el uso de su instancia de Azure Stack a la suscripción de cada usuario.

El diagrama siguiente muestra los pasos que necesita para elegir su cuenta de servicios compartidos y registrar la cuenta de Azure con la cuenta. Cuando se haya realizado esto, se podrán incorporar los clientes finales.

**Pasos para agregar seguimiento de uso como un CSP**

![Proceso para habilitar el uso y la administración como un proveedor de servicios en la nube.](media\azure-stack-add-manage-billing-as-a-csp\process-add-useage-as-a-csp.png)

## <a name="create-a-csp-or-cspss-subscription"></a>Creación de una suscripción de CSP o CSPSS

### <a name="cloud-service-provider-subscription-types"></a>Tipos de suscripción del proveedor de servicios en la nube

Tendrá que elegir el tipo de cuenta de servicios compartidos que utiliza para Azure Stack. Los tipos de suscripción que se pueden usar para el registro de una instancia multiinquilino de Azure Stack son los siguientes:

 - Proveedor de servicios en la nube 
 - Suscripción de servicios compartidos de asociados 

#### <a name="csp-shared-services"></a>Servicios compartidos de CSP

Las suscripciones de los servicios compartidos de proveedor de servicio en la nube (CSPSS) son la opción preferida para el registro cuando un distribuidor de CSP o CSP directo opera en Azure Stack.

Las suscripciones de CSPSS están asociadas a un inquilino de servicios compartidos. Cuando se registra Azure Stack, hay que proporcionar credenciales para una cuenta que es propietaria de la suscripción. La cuenta que utiliza para registrar Azure Stack puede ser diferente de la cuenta de administrador que utiliza para la implementación; las dos *no* tienen que pertenecer al mismo dominio. En otras palabras, puede realizar la implementación mediante el inquilino que ya usa. Por ejemplo puede usar ContosoCSP.onmicrosoft.com y, después, registrarse con un inquilino diferente, por ejemplo IURContosoCSP.onmicrosoft.com. Deberá recordar que debe iniciar sesión con ContosoCSP.onmicrosoft.com cuando realice la administración diaria de Azure Stack. Cuando inicia sesión en Azure usando IURContosoCSP.onmicrosoft.com cuando necesita hacer operaciones de registro.

Consulte lo siguiente para obtener una descripción de las suscripciones de CSPSS, así como instrucciones sobre cómo crear una suscripción en [Adición de servicios compartidos para partners de Azure](https://msdn.microsoft.com/partner-center/shared-services).

#### <a name="csp-subscriptions"></a>Suscripciones de CSP

Las suscripciones de un proveedor de servicio en la nube (CSP) son la opción preferida para el registro cuando un revendedor de CSP o CSP directo opera en Azure Stack.

## <a name="register-azure-stack"></a>Registro de Azure Stack

Para registrarse con Azure Stack, consulte [Registro de Azure Stack con una suscripción de Azure](azure-stack-registration.md).

## <a name="add-end-customer"></a>Adición a un cliente final

Para configurar Azure Stack de modo que cuando un nuevo inquilino utilice recursos su uso se notifique a su suscripción de un proveedor de servicios en la nube(CSP), consulte [Adición de inquilinos en Azure Stack para uso y facturación](azure-stack-csp-howto-register-tenants.md).

## <a name="charge-the-right-subscriptions"></a>Carga de las suscripciones correctas

Azure Stack utiliza una característica denominada registro. Un registro es un objeto, almacenado en Azure, que documenta qué suscripciones de Azure hay que usar para cobrar por una instancia determinada de Azure Stack. En esta sección se explica la importancia del registro.

Con el uso del registro de Azure Stack, se puede:
 - Desviar los datos de uso de Azure Stack a Azure Commerce y facture una suscripción a Azure.
 - Notificar el uso de cada cliente en una suscripción diferente con una implementación multiinquilino de Azure Stack. La arquitectura multiinquilino permite que Azure Stack admita distintas organizaciones en la misma instancia de Azure Stack.

Para cada instancia de Azure Stack, hay una suscripción predeterminada y tantas suscripciones de inquilinos como se necesite. La suscripción predeterminada es una suscripción de Azure que se cobra si no hay una suscripción específica de inquilino. Debe ser la primera en registrarse. Para que los informes de uso multiinquilinos funcionen, la suscripción debe ser un plan CSP o CSPSS.

Después, el registro se actualiza con una suscripción de Azure para cada inquilino que vaya a usar Azure Stack. Las suscripciones de inquilinos deben ser de tipo CSP y deben trasferirse al asociado que posee la suscripción predeterminada. En otras palabras, no puede registrar los clientes de otra persona.

Cuando Azure Stack envía información de uso a Azure global, un servicio de Azure consulta el registro y asigna el uso de cada inquilino a la suscripción del inquilino correspondiente. Si no se ha registrado un inquilino, ese uso se transfiere a la suscripción predeterminada para la instancia Azure Stack de la que se originó.

Dado que las suscripciones de inquilinos son suscripciones de CSP, se enviará la factura al asociado de CSP y la información de uso no será visible para el cliente final.



## <a name="next-steps"></a>Pasos siguientes

 - Para más información acerca del programa CSP, consulte [Programa Proveedor de soluciones en la nube](https://partnercenter.microsoft.com/en-us/partner/programs).
 - Para más información sobre cómo recuperar la información de utilización de recursos de Azure Stack, consulte [Uso y facturación en Azure Stack](/azure-stack-billing-and-chargeback.md).
