---
title: "Validación de alertas en Azure Security Center | Microsoft Docs"
description: Este documento le ayuda a validar las alertas de seguridad en Azure Security Center.
services: security-center
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: f8f17a55-e672-4d86-8ba9-6c3ce2e71a57
ms.service: security-center
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/07/2017
ms.author: yurid
ms.openlocfilehash: d7aa8544f50b42bacfa1e1f16fdce468d8fc81ef
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="alerts-validation-in-azure-security-center"></a>Validación de alertas en Azure Security Center
Este documento le ayuda a comprobar si el sistema está configurado correctamente para las alertas de Azure Security Center.

## <a name="what-are-security-alerts"></a>¿Qué son las alertas de seguridad?
Security Center recopila, analiza e integra automáticamente los datos de registro de los recursos de Azure, la red y las soluciones de asociados conectados, como firewalls y soluciones de protección de puntos de conexión, para detectar las amenazas y alertar de su existencia. Lea [Administración y respuesta a las alertas de seguridad en Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts) para obtener más información acerca de las alertas de seguridad y lea [Comprensión de las alertas de seguridad en Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-alerts-type) para obtener más información sobre los distintos tipos de alertas.

## <a name="alert-validation"></a>Validación de alertas
Una vez que el agente de Security Center esté instalado en el equipo, siga estos pasos desde el equipo donde desee que esté el recurso atacado de la alerta:

1. Copie un archivo ejecutable (por ejemplo calc.exe) en el escritorio del equipo o en otro directorio que prefiera.
2. Cambie el nombre de este archivo por **ASC_AlertTest_662jfi039N.exe**.
3. Abra el símbolo del sistema y ejecute este archivo con un argumento (un nombre de argumento falso), como: *ASC_AlertTest_662jfi039N.exe - foo*
4. Espere de cinco a diez minutos y abra Alertas de Security Center. Debería encontrar una alerta similar a la siguiente:

    ![Validación de alertas](./media/security-center-alert-validation/security-center-alert-validation-fig2.png)

Al revisar esta alerta, asegúrese de que el campo Arguments Auditing Enabled (Auditoría de argumentos habilitada) aparece como true. Si aparece como false, debe habilitar la auditoría de argumentos de línea de comandos. Puede habilitar esta opción mediante la línea de comandos siguiente:

*reg add "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\policies\system\Audit" /f /v "ProcessCreationIncludeCmdLine_Enabled"*


> [!NOTE]
> Puede ver el vídeo [Validación de alertas en Azure Security Center](https://channel9.msdn.com/Blogs/Azure-Security-Videos/Alert-Validation-in-Azure-Security-Center) para ver una demostración de esta característica. 

## <a name="see-also"></a>Otras referencias
En este artículo se explicó el proceso de validación de las alertas. Ahora que conoce esta validación, intente los siguientes pasos:

* [Administración y respuesta a las alertas de seguridad en Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts). Aprenda a administrar las alertas y responder a incidentes de seguridad en Security Center.
* [Supervisión del estado de seguridad en Azure Security Center](security-center-monitoring.md). Aprenda a supervisar el estado de los recursos de Azure.
* [Comprensión de las alertas de seguridad en Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-alerts-type). Obtenga información acerca de los distintos tipos de alertas de seguridad.
* [Guía de solución de problemas de Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide). Obtenga información acerca de cómo solucionar problemas comunes en Security Center. 
* [Preguntas más frecuentes sobre Azure Security Center](security-center-faq.md). Preguntas más frecuentes acerca del uso del servicio.
* [Blog de seguridad de Azure](http://blogs.msdn.com/b/azuresecurity/). Encuentre artículos de blog sobre el cumplimiento y la seguridad de Azure.

