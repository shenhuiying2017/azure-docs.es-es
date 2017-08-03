---
title: "Habilitación de la recolección de datos en Azure Security Center | Microsoft Docs"
description: " Aprenda a habilitar la recolección de datos en Azure Security Center. "
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 411d7bae-c9d4-4e83-be63-9f2f2312b075
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/16/2017
ms.author: terrylan
ms.translationtype: Human Translation
ms.sourcegitcommit: ff2fb126905d2a68c5888514262212010e108a3d
ms.openlocfilehash: 7e9ad8cd8c77c57c37dc208b86b3727a4e1dc7b5
ms.contentlocale: es-es
ms.lasthandoff: 06/17/2017


---
# <a name="enable-data-collection-in-azure-security-center"></a>Habilitación de la recolección de datos en Azure Security Center

> [!NOTE]
> Desde primeros de junio de 2017, Security Center usará Microsoft Monitoring Agent para recopilar y almacenar datos. Para obtener más información, consulte [Migración de la plataforma de Azure Security Center](security-center-platform-migration.md). La información de este artículo representa la funcionalidad de Security Center después de la transición a Microsoft Monitoring Agent.
>
>

Security Center recopila datos de las máquinas virtuales (VM) para evaluar su estado de seguridad, proporcionar recomendaciones de seguridad y avisarle de las amenazas. La primera vez que se accede a Security Center, tiene la opción de habilitar la recopilación de datos en todas las máquinas virtuales de la suscripción. Si la recopilación de datos no está habilitada, Security Center recomendará activarla en la directiva de seguridad de esa suscripción.

Cuando se habilita la recopilación de datos, Security Center aprovisiona Microsoft Monitoring Agent en todas las máquinas virtuales de Azure compatibles y en las que se creen. Microsoft Monitoring Agent examina varias configuraciones relacionadas con la seguridad. Además, el sistema operativo crea eventos del registro de eventos. Estos son algunos ejemplos de dichos datos: tipo y versión del sistema operativo, registros del sistema operativo (registros de eventos de Windows), procesos en ejecución, nombre de la máquina, direcciones IP, usuario conectado e identificador de inquilino. Microsoft Monitoring Agent lee las configuraciones y entradas del registro de eventos y copia los datos en el área de trabajo con fines de análisis. Asimismo, copia los archivos de volcado de memoria en dicha área de trabajo.

Si se utiliza el nivel Gratis de Security Center, también se puede deshabilitar la recopilación de datos de las máquinas virtuales en la directiva de seguridad. Al deshabilitar la recopilación de datos, se limitarán las evaluaciones de seguridad de las máquinas virtuales. Para obtener más información, consulte [Deshabilitación de la recopilación de datos](#disabling-data-collection). Las instantáneas de disco de máquina virtual y la recopilación de artefactos seguirán habilitadas, aunque la recopilación de datos se deshabilite. La recopilación de datos es necesaria para las suscripciones del nivel Estándar de Security Center.

> [!NOTE]
> Obtén más información sobre los [planes de tarifa](security-center-pricing.md) Gratis y Estándar de Security Center.
>
>

## <a name="implement-the-recommendation"></a>Implementación de la recomendación

> [!NOTE]
> En este documento se presenta el servicio mediante una implementación de ejemplo. Este documento no es una guía paso a paso.
>
>

1. En la hoja **Recomendaciones**, seleccione **Habilitar la recopilación de datos de las suscripciones**.  Se abrirá la hoja **Activar recolección de datos**.
   ![Hoja Recomendaciones][2]
2. En la hoja **Activar recolección de datos** , seleccione la suscripción. Se abre la hoja **Directiva de seguridad** para esa suscripción.
3. En la hoja **Directiva de seguridad**, seleccione **Activar** en **Recolección de datos** para recopilar registros de forma automática. La activación de la recolección de datos proporcionará la extensión de supervisión en todas las máquinas virtuales actuales y nuevas en la suscripción.
4. Seleccione **Guardar**.
5. Seleccione **Aceptar**.

## <a name="disabling-data-collection"></a>Deshabilitación de la recopilación de datos
Si se utiliza el nivel Gratis de Security Center, también se puede deshabilitar en cualquier momento la recopilación de datos de las máquinas virtuales en la directiva de seguridad. La recopilación de datos es necesaria para las suscripciones del nivel Estándar de Security Center.

1. Vuelva a la hoja **Security Center** y seleccione el icono **Directiva**. Esta acción abre la hoja **Security policy-Define policy per subscription** (Directiva de seguridad: Definir directiva por suscripción).
   ![Seleccionar el icono de directiva][5]
2. En la hoja **Security policy-Define policy per subscription** (Directiva de seguridad: Definir directiva por suscripción), seleccione la suscripción en la que desea deshabilitar la recopilación de datos.
3. Se abre la hoja **Directiva de seguridad** para esa suscripción.  Seleccione **Desactivar** en recolección de datos.
4. Seleccione **Guardar** en la cinta de opciones superior.

## <a name="next-steps"></a>Pasos siguientes
En este documento, mostramos cómo implementar la recomendación "Habilitar recopilación de datos" de Security Center. Para más información sobre el Centro de seguridad, consulte los siguientes recursos:

* [Establecimiento de directivas de seguridad en Azure Security Center](security-center-policies.md): aprenda a configurar directivas de seguridad para las suscripciones y los grupos de recursos de Azure.
* [Administración de recomendaciones de seguridad en Azure Security Center](security-center-recommendations.md) : recomendaciones que le ayudan a proteger los recursos de Azure.
* [Supervisión del estado de seguridad en Azure Security Center](security-center-monitoring.md): aprenda a supervisar el estado de los recursos de Azure.
* [Administración y respuesta a las alertas de seguridad en el Centro de seguridad de Azure](security-center-managing-and-responding-alerts.md): obtenga información sobre cómo administrar y responder a alertas de seguridad.
* [Supervisión de las soluciones de asociados con Azure Security Center](security-center-partner-solutions.md): aprenda a supervisar el estado de mantenimiento de las soluciones de asociados.
- [Seguridad de datos de Azure Security Center](security-center-data-security.md): aprenda cómo se administran y protegen los datos en Security Center.
* [Preguntas más frecuentes acerca del Centro de seguridad de Azure](security-center-faq.md): busque las preguntas más frecuentes sobre cómo usar el servicio.
* [Blog de seguridad de Azure](http://blogs.msdn.com/b/azuresecurity/): obtenga las últimas noticias e información sobre la seguridad en Azure.

<!--Image references-->
[2]: ./media/security-center-enable-data-collection/recommendations.png
[3]: ./media/security-center-enable-data-collection/data-collection.png
[4]: ./media/security-center-enable-data-collection/storage-account.png
[5]: ./media/security-center-enable-data-collection/policy.png
[6]: ./media/security-center-enable-data-collection/disable-data-collection.png

