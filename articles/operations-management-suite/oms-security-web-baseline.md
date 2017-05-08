---
title: "Línea de base web de la solución Seguridad y auditoría de Operations Management Suite | Microsoft Docs"
description: "En este documento se explica cómo utilizar la solución Seguridad y auditoría de OMS para realizar una evaluación de línea de base web de todos los servidores web supervisados para fines de cumplimiento y seguridad."
services: operations-management-suite
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ROBOTS: NOINDEX
redirect_url: https://www.microsoft.com/cloud-platform/security-and-compliance
ms.assetid: 17837c8b-3e79-47c0-9b83-a51c6ca44ca6
ms.service: operations-management-suite
ms.custom: oms-security
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/27/2017
ms.author: yurid
ms.translationtype: Human Translation
ms.sourcegitcommit: be3ac7755934bca00190db6e21b6527c91a77ec2
ms.openlocfilehash: 0d4707dc0c0ffbf40d0d10a6d12b9709a9655258
ms.contentlocale: es-es
ms.lasthandoff: 05/03/2017


---
# <a name="web-baseline-assessment-in-operations-management-suite-security-and-audit-solution"></a>Evaluación de línea de base web en la solución Seguridad y auditoría de Operations Management Suite
Este documento le ayuda a usar las funcionalidades de evaluación de línea de base web de la [solución Seguridad y auditoría de Operations Management Suite (OMS)](operations-management-suite-overview.md) para acceder al estado de seguridad de los recursos supervisados.

## <a name="what-is-web-baseline-assessment"></a>¿Qué es la evaluación de línea de base web?
Actualmente, Seguridad de OMS proporciona una evaluación de línea de base de seguridad para sistemas operativos. Examina la configuración del sistema operativo de los servidores cada 24 horas y proporciona una vista de las configuraciones potencialmente vulnerables. Consulte [Evaluación de línea base en la solución Seguridad y auditoría de Operations Management Suite](oms-security-baseline.md) para más información sobre esto.

El objetivo de la evaluación de línea de base web es encontrar configuraciones del servidor web potencialmente vulnerables. Los tres orígenes principales de las configuraciones de línea de base web son la configuración de .NET, ASP.NET e IIS.  Al igual que la evaluación de línea de base de sistema operativo, Seguridad de OMS va a examinar cada 24 horas los servidores web y a proporcionar una vista de su estado de seguridad.  En Internet Information Services (IIS), las configuraciones son muy personalizables, lo que permite reemplazar diversos niveles de sitio y aplicación. El examen comprueba la configuración en cada nivel de aplicación o sitio, además del nivel raíz predeterminado. Esto le ayuda a identificar las ubicaciones de configuraciones potencialmente vulnerables y corregirlas rápidamente.


## <a name="web-security-baseline-assessment"></a>Evaluación de línea de base de seguridad web
Para esta versión preliminar, se va a acceder a esta característica mediante la opción de búsqueda de OMS. Siga los pasos a continuación para realizar la consulta adecuada:

1. En el panel principal de **Microsoft Operations Management Suite**, haga clic en el icono **Seguridad y auditoría**.
2. En el panel **Seguridad y auditoría**, haga clic en el botón **Búsqueda de registros**.
3. La primera consulta que puede usar es **Web Baseline Assessment Summary** (Resumen de evaluación de línea de base web). En el campo **Begin search here** (Empezar búsqueda aquí), escriba esta consulta: Type*=SecurityBaselineSummary BaselineType=web*. La siguiente pantalla tiene un ejemplo de salida:

![Resumen de evaluación de línea de base web](./media/oms-security-web-baseline/oms-security-web-baseline-fig1-new.png)

> [!NOTE]
> En esta consulta, cada registro indica el resumen de evaluación para un solo servidor.

Una vez que esté en **Búsqueda de registros**, puede escribir diferentes consultas para obtener más información sobre la evaluación de línea de base de web. Además de la consulta anterior, también puede usar las siguientes en esta versión preliminar.

**Web Baseline Rule Assessment** (Evaluación de regla de la línea de base web): cada registro representa una sola evaluación de regla de la línea de base web en un único servidor. Incluye todos los datos de la regla, la ubicación, el resultado esperado y el resultado real.

**Consulta**: Type*=SecurityBaseline BaselineType=web*

![Evaluación de regla de la línea de base web](./media/oms-security-web-baseline/oms-security-web-baseline-fig2.png)

**Show all results for a specific server** (Mostrar todos los resultados para un servidor específico): esta consulta muestra cómo ver los resultados de un servidor específico.

**Consulta**: *Type=SecurityBaseline BaselineType=web Computer=BaselineTestVM1*

![Todos los resultados](./media/oms-security-web-baseline/oms-security-web-baseline-fig3.png)

También puede usar estos registros y consultas para crear sus propios paneles, informes o alertas. La pantalla siguiente tiene un control de interfaz de usuario de ejemplo que se puede agregar al panel. Puede aprender a visualizar los datos mediante el Diseñador de vistas de OMS [aquí](https://blogs.technet.microsoft.com/msoms/2016/06/30/oms-view-designer-visualize-your-data-your-way/). La pantalla siguiente es un ejemplo de cómo se verá el icono una vez realizada esta personalización.

![Ejemplo de interfaz de usuario](./media/oms-security-web-baseline/oms-security-web-baseline-fig4.png)

> [!NOTE]
> Si desea conocer las configuraciones que se comprueban para la evaluación de línea de base, puede descargar [esta hoja de cálculo de Excel](https://gallery.technet.microsoft.com/OMS-Web-Baseline-1e811690), que las contiene.

## <a name="see-also"></a>Consulte también
En este documento, ha aprendido acerca de la evaluación de línea de base web de Seguridad y auditoría de OMS. Para obtener más información sobre Seguridad de OMS, consulte los siguientes artículos:

* [Información general de Operations Management Suite (OMS)](operations-management-suite-overview.md)
* [Supervisión de las alertas de seguridad y su respuesta en la solución Seguridad y auditoría de Operations Management Suite](oms-security-responding-alerts.md)
* [Supervisión de los recursos en la solución Seguridad y auditoría de Operations Management Suite](oms-security-monitoring-resources.md)


