---
title: "Evaluación de base de referencia web en la solución Security and Audit de Operations Management Suite | Microsoft Docs"
description: "En este documento se explica cómo utilizar la evaluación de base de referencia web de la solución Security and Audit de OMS para realizar una evaluación de la base de referencia web de todos los servidores web supervisados para fines de cumplimiento y seguridad."
services: operations-management-suite
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: 17837c8b-3e79-47c0-9b83-a51c6ca44ca6
ms.service: operations-management-suite
ms.custom: oms-security
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/18/2017
ms.author: yurid
ms.openlocfilehash: 40b0c6ca933ea02ac9f5fe3bfaaf87a310542a8d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="web-baseline-assessment-in-operations-management-suite-security-and-audit-solution"></a>Evaluación de línea de base web en la solución Security and Audit de Operations Management Suite
Este documento le ayuda a usar las funcionalidades de evaluación de base de referencia web de Security and Audit de OMS para acceder al estado de seguridad de los recursos supervisados.

## <a name="what-is-web-baseline-assessment"></a>¿Qué es la evaluación de base de referencia web?
Actualmente, Seguridad de OMS proporciona una evaluación de línea de base de seguridad para sistemas operativos. Examina la configuración del sistema operativo de los servidores cada 24 horas y proporciona una vista de las configuraciones potencialmente vulnerables. Consulte [Evaluación de línea base en la solución Security and Audit de Operations Management Suite](https://docs.microsoft.com/azure/operations-management-suite/oms-security-baseline) para más información sobre esto.

El objetivo de la evaluación de base de referencia web es encontrar configuraciones del servidor web potencialmente vulnerables. Los tres orígenes principales de las configuraciones de línea de base web son la configuración de .NET, ASP.NET e IIS.  Al igual que la evaluación de línea de base de sistema operativo, Seguridad de OMS va a examinar cada 24 horas los servidores web y a proporcionar una vista de su estado de seguridad.  En Internet Information Services (IIS), las configuraciones son muy personalizables, lo que permite reemplazar diversos niveles de sitio y aplicación. El examen comprueba la configuración en cada nivel de aplicación o sitio, además del nivel raíz predeterminado. Esto le ayudará a identificar la configuración que pueda sufrir una vulnerabilidad y corregirla con rapidez, junto con nuestras recomendaciones para dicha configuración.

>[!NOTE] 
>Puede descargar los identificadores de configuración comunes y las reglas de línea de base utilizadas por la seguridad de OMS en esta [página](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335?redir=0).


## <a name="web-security-baseline-assessment"></a>Evaluación de base de referencia de seguridad web

En esta versión preliminar, se puede acceder a la característica a través de la opción de búsqueda de OMS y del panel Security and Audit de OMS. Siga los pasos a continuación para realizar la consulta adecuada:

1. En el panel principal de **Microsoft Operations Management Suite**, haga clic en el icono **Security and Audit**.
2. En el panel **Security and Audit**, puede ver la perspectiva de la base de referencia web junto con la de la base de referencia del sistema operativo.
   
    ![Evaluación de base de referencia de seguridad web de Security and Audit de OMS](./media/oms-security-web-baseline/oms-security-web-baseline-fig5.png)

3. El panel izquierdo muestra el número de servidores web en comparación con la base de referencia, el porcentaje medio de reglas que se pasan en todos los servidores que se evalúan y la lista de servidores evaluados.
4. El panel derecho muestra las reglas únicas que no tuvieron éxito por *gravedad* y *tipo*. Al hacer clic en cualquiera de las reglas del panel derecho, se mostrarán los detalles de esa regla. Se muestra un ejemplo en la ilustración siguiente. Aparece la regla que se evalúa en *Configuración de regla*. El campo *AzId* es un identificador único para cada regla que usa Microsoft para el seguimiento de las reglas de base de referencia. Además, los usuarios pueden ver el *resultado esperado* (valor recomendado de Microsoft) y otros detalles relacionados con el impacto en la seguridad de la regla.
    
    ![Consultar](./media/oms-security-web-baseline/oms-security-web-baseline-fig6.png)

5. Puede crear sus propias consultas para revisar los resultados. 

La primera consulta que puede usar es **Web Baseline Assessment Summary** (Resumen de evaluación de línea de base web). En el campo **Begin search here** (Empezar búsqueda aquí), escriba esta consulta: *Type=SecurityBaselineSummary BaselineType=Web*. A continuación se muestra un ejemplo de salida:

![Resultado de la consulta](./media/oms-security-web-baseline/oms-security-web-baseline-fig7.png)

>[!NOTE] 
>En esta consulta, cada registro indica el resumen de evaluación para un solo servidor.

Una vez que esté en **Búsqueda de registros**, puede escribir diferentes consultas para obtener más información sobre la evaluación de línea de base de web. Además de la consulta anterior, también puede usar las siguientes en esta versión preliminar:

**Web Baseline Rule Assessment** (Evaluación de regla de la línea de base web): cada registro representa una sola evaluación de regla de la línea de base web en un único servidor. Incluye todos los datos de una regla con errores, la *ruta de acceso al sitio* en que la regla se evaluó, el *resultado esperado* y el *resultado real*.

Consulta: *Type=SecurityBaseline BaselineType=Web AnalyzeResult=Failed*

![Resultado de la consulta 2](./media/oms-security-web-baseline/oms-security-web-baseline-fig8.png)

**Mostrar todos los resultados de un servidor específico**: esta consulta muestra cómo ver los resultados de un servidor específico: Consulta: *Type=SecurityBaseline BaselineType=Web Computer=BaselineTestVM1*

![Resultado de la consulta 3](./media/oms-security-web-baseline/oms-security-web-baseline-fig3.png)

También puede usar estos registros y consultas para crear sus propios paneles, informes o alertas. Aquí se muestra un control de interfaz de usuario de ejemplo que se puede agregar al panel. Puede aprender a visualizar los datos mediante el Diseñador de vistas de OMS [aquí](https://blogs.technet.microsoft.com/msoms/2016/06/30/oms-view-designer-visualize-your-data-your-way/). La pantalla siguiente es un ejemplo de cómo se verá el icono una vez realizada esta personalización.

![dashboard](./media/oms-security-web-baseline/oms-security-web-baseline-fig4.png)

## <a name="see-also"></a>Otras referencias
En este documento, ha aprendido acerca de la evaluación de base de referencia web de Security and Audit de OMS. Para obtener más información sobre Seguridad de OMS, consulte los siguientes artículos:

* [Información general de Operations Management Suite (OMS)](operations-management-suite-overview.md)
* [Supervisión de las alertas de seguridad y su respuesta en la solución Security and Audit de Operations Management Suite](oms-security-responding-alerts.md)
* [Supervisión de los recursos en la solución Seguridad y auditoría de Operations Management Suite](oms-security-monitoring-resources.md)

