---
title: "Azure Log Integration con registros de auditoría de Azure Active Directory | Microsoft Docs"
description: "Aquí aprenderá cómo instalar el servicio Azure Log Integration y cómo integrar los registros desde registros de auditoría de Azure"
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
ms.date: 08/08/2017
ms.author: barclayn
ms.custom: azlog
ms.openlocfilehash: 8a1295cc86057ed72940e774d0bd423d61142e31
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/29/2017
---
# <a name="integrate-azure-active-directory-audit-logs"></a>Integración de los registros de auditoría de Azure Active Directory

Los eventos de auditoría de Azure Active Directory (Azure AD) le ayudan a identificar las acciones con privilegios que se produjeron en Azure Active Directory. Puede ver los tipos de eventos de los que puede realizar un seguimiento mediante la revisión de [Eventos del informe de auditoría de Azure Active Directory](/active-directory/active-directory-reporting-audit-events#list-of-audit-report-events.md).

> [!NOTE]
> Debe revisar el artículo [Introducción](security-azure-log-integration-get-started.md) y completar los pasos allí antes de intentar seguir los pasos descritos en este artículo.

## <a name="steps-to-integrate-azure-active-directory-audit-logs"></a>Pasos para la integración de los registros de auditoría de Azure Active Directory

1. Abra el símbolo del sistema y ejecute este comando:

   ``cd c:\Program Files\Microsoft Azure Log Integration``

2. Ejecute este comando: 
 
   ``azlog createazureid``

   Este comando solicita el inicio de sesión de Azure. A continuación, el comando crea una entidad de servicio de Azure Active Directory en los inquilinos de Azure AD que hospedan las suscripciones de Azure en las que el usuario inició sesión como administrador, coadministrador o propietario. El comando producirá un error si el usuario ha iniciado sesión solo como usuario invitado en el inquilino de Azure AD. La autenticación en Azure se hace con Azure AD. La creación de una entidad de servicio para Azure Log Integration creará la identidad de Azure AD a la que se otorgará acceso de lectura de las suscripciones de Azure.

3. Ejecute el siguiente comando para proporcionar el identificador del inquilino. Debe ser miembro del rol de administrador de inquilinos para ejecutar el comando.

   ``Azlog.exe authorizedirectoryreader tenantId``

   Ejemplo:

   ``AZLOG.exe authorizedirectoryreader ba2c0000-d24b-4f4e-92b1-48c4469999``

4. Compruebe las siguientes carpetas para confirmar que se han creado los archivos JSON de registro de auditoría de Azure Active Directory en las siguientes rutas:

   * **C:\Users\azlog\AzureActiveDirectoryJson**
   * **C:\Users\azlog\AzureActiveDirectoryJsonLD**

En el siguiente vídeo se muestran los pasos que se describen en este artículo:

> [!VIDEO https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Log-Integration-Videos-Azure-AD-Integration/player]


> [!NOTE]
> Para obtener instrucciones específicas sobre cómo llevar la información de los archivos JSON al sistema de información de seguridad y administración de eventos (SIEM), póngase en contacto con el proveedor SIEM.

Puede encontrar ayuda de la comunidad en [Foro MSDN de Azure Log Integration](https://social.msdn.microsoft.com/Forums/office/home?forum=AzureLogIntegration). Este foro permite a los usuarios de la comunidad de Azure Log Integration ayudarse unos a otros con preguntas, respuestas, consejos y sugerencias. Además, el equipo de Azure Log Integration supervisa este foro y le ayudará siempre que sea posible.

También puede abrir una [solicitud de soporte técnico](../azure-supportability/how-to-create-azure-support-request.md). Seleccione **Azure Log Integration** como el servicio para el que está solicitando el soporte técnico.

## <a name="next-steps"></a>Pasos siguientes
Para más información sobre Azure Log Integration, consulte:

* [Microsoft Azure Log Integration for Azure logs](https://www.microsoft.com/download/details.aspx?id=53324) (Microsoft Azure Log Integration para registros de Azure): Esta página del Centro de descarga proporciona información, los requisitos del sistema y las instrucciones de instalación de Azure Log Integration.
* [Introduction to Azure log integration](security-azure-log-integration-overview.md) (Introducción a Azure Log Integration): este documento es una introducción de Azure Log Integration, sus principales funcionalidades y cómo funciona.
* [Pasos de configuración para soluciones de asociados](https://blogs.msdn.microsoft.com/azuresecurity/2016/08/23/azure-log-siem-configuration-steps/): esta entrada de blog le muestra cómo configurar Azure Log Integration para que funcione con soluciones de asociados, como Splunk, HP ArcSight y IBM QRadar.
* [Preguntas más frecuentes sobre Azure Log Integration](security-azure-log-integration-faq.md). Este artículo de preguntas más frecuentes responde a preguntas sobre Azure Log Integration.
* [Integración de las alertas de Security Center con Azure Log Integration (versión preliminar)](../security-center/security-center-integrating-alerts-with-log-integration.md). Este artículo le explica cómo sincronizar las alertas de Security Center, además de los eventos de seguridad de máquina virtual recopilados por Azure Diagnostics y los registros de auditoría de Azure, con sus análisis de registros o una solución SIEM.
* [New features for Azure diagnostics and Azure Audit Logs](https://azure.microsoft.com/blog/new-features-for-azure-diagnostics-and-azure-audit-logs/) (Nuevas características de Azure Diagnostics y registros de auditoría de Azure): esta entrada de blog es una introducción a los registros de auditoría de Azure y a otras características que le ayudarán a obtener información sobre las operaciones de los recursos de Azure.
