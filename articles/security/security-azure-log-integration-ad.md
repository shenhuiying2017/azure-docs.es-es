---
title: "Azure Log Integration (AZLOG) con registros de auditoría de Active Directory | Microsoft Docs"
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
ms.date: 05/09/2017
ms.author: barclayn
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 5e135be1e21173add3236f851609f1df0a5b0dee
ms.contentlocale: es-es
ms.lasthandoff: 05/10/2017


---

#<a name="integrate-azure-active-directory-audit-logs"></a>Integración de los registros de auditoría de Azure Active Directory

Los eventos de auditoría de Azure Active Directory le ayudan a identificar las acciones con privilegios que se produjeron en Azure Active Directory. Puede ver los tipos de eventos de los que puede realizar un seguimiento mediante la revisión de [Eventos del informe de auditoría de Azure Active Directory](/active-directory/active-directory-reporting-audit-events#list-of-audit-report-events.md).

>[!NOTE]
Debe revisar el artículo de [introducción](security-azure-log-integration-get-started.md) y completar todos los pasos hasta el paso 3 de la sección **Pasos posteriores a la instalación y validación** antes de intentar los pasos de este artículo.

## <a name="steps-to-integrate-azure-active-directory-audit-logs"></a>Pasos para la integración de los registros de auditoría de Azure Active Directory

1. Abra el símbolo del sistema y ejecute cd en **c:\Program Files\Microsoft Azure Log Integration**.
2. Ejecute el comando:

 ``azlog createazureid``

 Este comando solicita el inicio de sesión de Azure. A continuación, el comando crea una Entidad de servicio de Azure Active Directory en los inquilinos de Azure AD que hospedan las suscripciones de Azure en las que el usuario inició sesión como administrador, coadministrador o propietario. El comando producirá un error si el usuario ha iniciado sesión solo como usuario invitado en el inquilino de Azure AD. La autenticación en Azure se hace con Azure Active Directory (AD). La creación de una entidad de servicio para la integración de registro de Azure creará la identidad de Azure AD a la que se otorgará acceso de lectura de las suscripciones de Azure.

3. Ejecute el comando proporcionando el valor de tenantID. Debe ser miembro del rol de administrador de inquilinos para ejecutar el comando.

``Azlog.exe authorizedirectoryreader tenantId``

Ejemplo

``AZLOG.exe authorizedirectoryreader ba2c0000-d24b-4f4e-92b1-48c4469999``

Compruebe las siguientes carpetas para confirmar que se han creado los archivos JSON de registro de auditoría de Azure Active Directory en las siguientes rutas:

* **C:\Users\azlog\AzureActiveDirectoryJson**
* **C:\Users\azlog\AzureActiveDirectoryJsonLD**

Dirija el conector del reenviador de archivos SIEM estándar a la carpeta adecuada para canalizar los datos a la instancia SIEM. Puede que necesite algunas asignaciones de campo según el producto de SIEM que utilice.

Puede encontrar ayuda de la comunidad en [Foro MSDN de Azure Log Integration](https://social.msdn.microsoft.com/Forums/office/home?forum=AzureLogIntegration). Este foro ofrece a la comunidad de AzLog la posibilidad de ayudarse en relación con preguntas, respuestas, consejos y sugerencias sobre cómo sacar el máximo partido de Azure Log Integration. Además, el equipo de Azure Log Integration supervisa este foro y le ayudará a cada vez que se pueda.

También puede abrir una [solicitud de soporte técnico](../azure-supportability/how-to-create-azure-support-request.md). Para ello, seleccione **Azure Log Integration** como el servicio para el que está solicitando el soporte técnico.

## <a name="next-steps"></a>Pasos siguientes
Para más información sobre Azure Log Integration, vea los siguientes documentos:

* [Microsoft Azure Log Integration for Azure logs](https://www.microsoft.com/download/details.aspx?id=53324) (Integración de registros de Microsoft Azure para registros de Azure): Centro de descarga para obtener información, los requisitos del sistema y las instrucciones de instalación de la integración de registros de Azure.
* [Introduction to Azure log integration](security-azure-log-integration-overview.md) (Introducción a la integración de registro de Azure): este documento es una introducción del servicio de integración de registro de Azure, las principales funcionalidades y cómo funciona.
* [Pasos de configuración para soluciones de asociados](https://blogs.msdn.microsoft.com/azuresecurity/2016/08/23/azure-log-siem-configuration-steps/) : esta entrada de blog le muestra cómo configurar la integración de registros de Azure para que funcione con soluciones de asociados, como Splunk, HP ArcSight y IBM QRadar.
* [Preguntas más frecuentes sobre la integración de registro de Azure (P+F)](security-azure-log-integration-faq.md). Este artículo de preguntas más frecuentes responde a preguntas sobre la integración de registro de Azure.
* [Integración de las alertas de Security Center con la integración de registro de Azure (versión preliminar)](../security-center/security-center-integrating-alerts-with-log-integration.md). Este documento le explica cómo sincronizar las alertas de Security Center, además de los eventos de seguridad de máquina virtual recopilados por Diagnósticos de Azure y los registros de auditoría de Azure, con sus análisis de registros o una solución SIEM.
* [New features for Azure diagnostics and Azure Audit Logs](https://azure.microsoft.com/blog/new-features-for-azure-diagnostics-and-azure-audit-logs/) (Nuevas características de Diagnósticos de Azure y registros de auditoría de Azure): esta entrada de blog es una introducción a los registros de auditoría de Azure y a otras características que le ayudarán a obtener información sobre las operaciones de los recursos de Azure.

