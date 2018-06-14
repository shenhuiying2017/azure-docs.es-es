---
title: Facturación y contracargo del cliente en Azure Stack | Microsoft Docs
description: Averigüe cómo recuperar la información de la utilización de recursos de Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/25/2018
ms.author: mabrigg
ms.reviewer: alfredop
ms.openlocfilehash: eca335797f48b7c44351655f17c8b6499f3d5999
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/12/2018
ms.locfileid: "29877490"
---
# <a name="usage-and-billing-in-azure-stack"></a>Utilización y facturación en Azure Stack

En este artículo se describe cómo se factura a los usuarios de Azure Stack por el uso de los recursos. Puede acceder a la información de facturación para análisis y cargos al usuario.

Azure Stack recopila y agrupa los datos de uso de todos los recursos utilizados y los reenvía a Azure Commerce. Azure Commerce factura el uso de Azure Stack de la misma forma que facturaría el uso de Azure.

También puede obtener datos de uso y exportarlos a su propio sistema de facturación y cargos al usuario con el uso de un adaptador de facturación, o bien exportarlos a una herramienta de inteligencia empresarial, como Microsoft Power BI, y usarlos para análisis.


## <a name="usage-pipeline"></a>Canalización de uso

Todos los proveedores de recursos de Azure Stack emiten datos de uso en función de la utilización de los recursos. El servicio de uso agrega periódicamente (cada hora y cada día) los datos de uso y los almacena en la base de datos de uso. Los operadores y usuarios de Azure Stack pueden acceder a los datos de uso almacenados mediante las API de uso de recursos de Azure Stack. 

Si ha [registrado una instancia de Azure Stack en Azure](azure-stack-register.md), Azure Stack se configura para enviar los datos de uso a Azure Commerce. Una vez que los datos estén cargados en Azure, puede acceder a ellos a través del portal de facturación o mediante las API de uso de recursos de Azure. En el tema sobre [informes de datos de uso](azure-stack-usage-reporting.md) puede obtener más información sobre los datos de uso que se notifican a Azure.  

La siguiente imagen muestra los principales componentes de la canalización de uso: 

![Canalización de uso](media\azure-stack-billing-and-chargeback\usagepipeline.png)

## <a name="what-usage-information-can-i-find-and-how"></a>¿Qué información de utilización se puede encontrar y cómo?

Los proveedores de recursos de Azure Stack como, por ejemplo, Compute, Storage y Network, generan datos de utilización a intervalos de horas para cada suscripción. Los datos de uso contienen información sobre los recursos consumidos, como el nombre del recurso, la suscripción y la cantidad utilizadas. Para obtener información sobre los recursos de identificadores de medidores, consulte el artículo de [preguntas más frecuentes de API de uso](azure-stack-usage-related-faq.md).

Después de que se hayan recopilado los datos de utilización, se [notifican a Azure](azure-stack-usage-reporting.md) para que genere una factura, que se puede ver en el Portal de facturación de Azure. 


> [!NOTE]
> El informe de datos de uso no es necesario para el Kit de desarrollo de Azure Stack ni para los usuarios del sistema integrado de Azure Stack cuya licencia esté dentro del modelo de capacidad. Para más información acerca de las licencias de Azure Stack, consulte la hoja de datos de [Packaging and pricing](https://azure.microsoft.com/mediahandler/files/resourcefiles/5bc3f30c-cd57-4513-989e-056325eb95e1/Azure-Stack-packaging-and-pricing-datasheet.pdf) (Empaquetado y precios).

El Portal de facturación de Azure muestra los datos de uso de los recursos facturables. Además de los recursos facturables, Azure Stack captura los datos de utilización de un conjunto más amplio de recursos, a los que se pueden obtener acceso en el entorno de Azure Stack a través de las API de REST o PowerShell. Los operadores de Azure Stack pueden obtener los datos de uso de todas las suscripciones de usuario. Los usuarios individuales solo pueden obtener sus detalles de uso. 

## <a name="usage-reporting-for-multitenant-cloud-service-providers"></a>Informes de uso de proveedores de servicios en la nube multiempresa

Un proveedor de servicios en la nube multiempresa que tenga muchos clientes que usan Azure Stack puede que tenga la necesidad de elaborar un informe de uso de cada cliente, para poder cargar el uso a distintas suscripciones de Azure. 

La identidad de cada cliente se representa mediante un inquilino de Azure Active Directory (Azure AD) distinto. Azure Stack admite la asignación de una suscripción de CSP a cada inquilino de Azure AD. Puede agregar los inquilinos y sus suscripciones al registro de base de Azure Stack. El registro de base se realiza para todas las instancias de Azure Stack. Si la suscripción de un inquilino no está registrada, el usuario puede seguir usando Azure Stack, y su uso se enviará a la suscripción utilizada para el registro de base. 


## <a name="next-steps"></a>Pasos siguientes

[Registro en Azure Stack](azure-stack-registration.md)

[Report Azure Stack usage data to Azure](azure-stack-usage-reporting.md) (Notificar los datos de utilización de Azure Stack a Azure)

[Provider Resource Usage API](azure-stack-provider-resource-api.md) (API de utilización de recursos de proveedor)

[Tenant Resource Usage API](azure-stack-tenant-resource-usage-api.md) (API de utilización de recursos de inquilino)

[Usage-related FAQ](azure-stack-usage-related-faq.md) (P+F relacionadas con la utilización)