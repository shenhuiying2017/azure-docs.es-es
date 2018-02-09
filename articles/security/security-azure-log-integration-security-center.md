---
title: Azure Log Integration en Security Center | Microsoft Docs
description: "Obtenga información sobre cómo funcionan las alertas de Azure Security Center con la integración de registros."
services: security
documentationcenter: na
author: Barclayn
manager: MBaldwin
editor: TomShinder
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ums.workload: na
ms.date: 08/29/2017
ms.author: barclayn
ms.custom: azlog
ms.openlocfilehash: 9acce21d544a43adcd0c0983771c02c6bb39caec
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-get-your-security-center-alerts-in-azure-log-integration"></a>Cómo obtener alertas de Security Center en Azure Log Integration

En este artículo se proporcionan los pasos necesarios para habilitar el servicio de Azure Log Integration para extraer información de alertas de seguridad generadas por Azure Security Center. Primero debe haber completado correctamente los pasos descritos en el artículo [Introducción](security-azure-log-integration-get-started.md) antes de realizar los pasos de este artículo.

## <a name="detailed-steps"></a>Pasos detallados

Con los siguientes pasos creará la entidad de servicio de Azure Active Directory necesaria y asignará permisos de lectura de la entidad de servicio a la suscripción:
1. Abra el símbolo del sistema y vaya a **C:\Archivos de programa\Microsoft Azure Log Integration**.
2. Ejecute el comando ``azlog createazureid``.

    Este comando solicita el inicio de sesión de Azure. A continuación, el comando crea una [Entidad de servicio de Azure Active Directory](../active-directory/develop/active-directory-application-objects.md) en los inquilinos de Azure AD que hospedan las suscripciones de Azure en las que el usuario inició sesión como administrador, coadministrador o propietario. El comando producirá un error si el usuario ha iniciado sesión solo como usuario invitado en el inquilino de Azure AD. La autenticación en Azure se hace con Azure Active Directory (AD). La creación de una entidad de servicio para la integración de registro de Azure creará la identidad de Azure AD a la que se otorgará acceso de lectura de las suscripciones de Azure.

3. Después ejecutará un comando que asigna acceso de lectura en la suscripción a la entidad de servicio recién creada. Si no especifica un SubscriptionID, entonces el comando intenta asignar el rol de lector de la entidad de servicio a todas las suscripciones a las que tiene acceso. </br></br>
``azlog authorize <SubscriptionID>`` </br> por ejemplo: </br>
``azlog authorize 0ee55555-0bc4-4a32-a4e8-c29980000000``

    >[!NOTE]
    Puede que vea advertencias si ejecuta el comando authorize inmediatamente después del comando ```createazureid```. Se produce cierta latencia entre el momento en que se crea la cuenta de Azure AD y el momento en que la cuenta está disponible para su uso. Si espera unos 60 segundos después de ejecutar el comando ```createazureid``` para ejecutar el comando authorize, no debería ver estas advertencias.

4. Compruebe las carpetas siguientes para confirmar que contienen los archivos JSON del registro de auditoría:
 * **c:\Users\azlog\AzureResourceManagerJson**
 * **c:\Users\azlog\AzureResourceManagerJsonLD** </br></br>
5. Compruebe las carpetas siguientes para confirmar que contienen las alertas de Security Center:</br></br>
 * **c:\Users\azlog\AzureSecurityCenterJson**
 * **c:\Users\azlog\AzureSecurityCenterJsonLD** </br></br>

Si experimenta problemas durante la instalación y la configuración, abra una [solicitud de soporte técnico](/azure-supportability/how-to-create-azure-support-request.md) y seleccione **Integración de registros** como el servicio para el que está solicitando soporte técnico.

## <a name="next-steps"></a>Pasos siguientes
Para más información sobre Azure Log Integration, vea los siguientes documentos:

* [Microsoft Azure Log Integration for Azure logs](https://www.microsoft.com/download/details.aspx?id=53324) (Integración de registros de Microsoft Azure para registros de Azure): Centro de descarga para obtener información, los requisitos del sistema y las instrucciones de instalación de la integración de registros de Azure.
* [Introduction to Azure log integration](security-azure-log-integration-overview.md) (Introducción a la integración de registro de Azure): este documento es una introducción del servicio de integración de registro de Azure, las principales funcionalidades y cómo funciona.
* [Pasos de configuración para soluciones de asociados](https://blogs.msdn.microsoft.com/azuresecurity/2016/08/23/azure-log-siem-configuration-steps/) : esta entrada de blog le muestra cómo configurar la integración de registros de Azure para que funcione con soluciones de asociados, como Splunk, HP ArcSight y IBM QRadar.
* [Preguntas más frecuentes sobre la integración de registro de Azure (P+F)](security-azure-log-integration-faq.md). Este artículo de preguntas más frecuentes responde a preguntas sobre la integración de registro de Azure.
* [New features for Azure diagnostics and Azure Audit Logs](https://azure.microsoft.com/blog/new-features-for-azure-diagnostics-and-azure-audit-logs/) (Nuevas características de Diagnósticos de Azure y registros de auditoría de Azure): esta entrada de blog es una introducción a los registros de auditoría de Azure y a otras características que le ayudarán a obtener información sobre las operaciones de los recursos de Azure.
