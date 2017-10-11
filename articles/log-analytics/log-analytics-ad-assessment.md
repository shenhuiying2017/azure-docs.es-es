---
title: "Optimización del entorno de Active Directory con Azure Log Analytics | Microsoft Docs"
description: "Puede usar periódicamente la solución de evaluación de Active Directory para evaluar el riesgo y el estado de los entornos de servidor."
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: 81eb41b8-eb62-4eb2-9f7b-fde5c89c9b47
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2017
ms.author: banders
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 97368f0b9e89ffd0cd982b6e8670d5a1f62ad42c
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/18/2017
---
# <a name="optimize-your-active-directory-environment-with-the-active-directory-assessment-solution-in-log-analytics"></a>Optimización del entorno de Active Directory con la solución de evaluación de Active Directory en Log Analytics

![Símbolo de AD Assessment](./media/log-analytics-ad-assessment/ad-assessment-symbol.png)

Puede usar periódicamente la solución de evaluación de Active Directory para evaluar el riesgo y el estado de los entornos de servidor. Este artículo facilita la instalación y el uso de la solución para que pueda tomar acciones correctivas en caso de posibles problemas.

Esta solución proporciona una lista priorizada de recomendaciones específicas para su infraestructura de servidor implementada. Las recomendaciones se clasifican en cuatro áreas de enfoque que ayudan a comprender rápidamente el riesgo y a tomar una acción correctiva.

Las recomendaciones se basan en los conocimientos y la experiencia adquiridos por los ingenieros de Microsoft producto de miles de visitas de clientes. En cada recomendación, se explica por qué podría ser importante para usted un problema y se proporcionan instrucciones sobre cómo implementar los cambios sugeridos.

Puede elegir las áreas de enfoque que sean más importantes para su organización y realizar un seguimiento del progreso hacia la consecución de un entorno libre de riesgos y en buen estado.

Después de agregar la solución y completar una evaluación, se muestra información resumida sobre las áreas de enfoque en el panel **AD Assessment** (Evaluación de AD) para la infraestructura del entorno. Las secciones siguientes describen cómo usar la información que aparece en el panel **AD Assessment** (Evaluación de AD), donde puede ver y ejecutar las acciones recomendadas para su infraestructura de servicio de Active Directory.

![imagen del icono de evaluación de SQL](./media/log-analytics-ad-assessment/ad-tile.png)

![imagen del panel de evaluación de SQL](./media/log-analytics-ad-assessment/ad-assessment.png)

## <a name="installing-and-configuring-the-solution"></a>Instalación y configuración de la solución
Use la siguiente información para instalar y configurar las soluciones.

* Los agentes deben instalarse en controladores que sean miembros del dominio que se va a evaluar.
* La solución de evaluación de Active Directory requiere que esté instalada una versión compatible de .NET Framework 4 (4.5.2 o superior) en todos los equipos que tengan un agente de OMS.
* Agregue la solución Active Directory Assessment (evaluación de Active Directory) al área de trabajo de OMS desde [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.ADAssessmentOMS?tab=Overview) o mediante el proceso descrito en el artículo sobre [incorporación de soluciones de Log Analytics desde la Galería de soluciones](log-analytics-add-solutions.md).  No es necesario realizar ninguna configuración más.

  > [!NOTE]
  > Después de agregar la solución, el archivo AdvisorAssessment.exe se agrega a servidores con agentes. Los datos de configuración se leen y, luego, se envían al servicio de OMS en la nube para su procesamiento. Se aplica la lógica a los datos recibidos y el servicio de nube registra los datos.
  >
  >

## <a name="active-directory-assessment-data-collection-details"></a>Detalles de la recopilación de datos para la evaluación de Active Directory

Active Directory Assessment recopila datos de los siguientes orígenes mediante los agentes habilitados:

- Recopiladores de registros
- Recopiladores de LDAP
- .NET Framework
- Recopiladores de registros de eventos
- Interfaces ADSI
- Windows PowerShell
- Recopiladores de datos de archivo
- Instrumental de administración de Windows (WMI)
- API de la herramienta DCDIAG
- API del servicio de replicación de archivos (NTFRS)
- Código personalizado C#


En la siguiente tabla se muestran los métodos de recopilación de datos para agentes, si se necesita Operations Manager (SCOM) y la frecuencia con la que un agente recopila datos.

| plataforma | Agente directo | Agente de SCOM | Azure Storage | ¿Se necesita SCOM? | Datos del agente de SCOM enviados a través del grupo de administración | Frecuencia de recopilación |
| --- | --- | --- | --- | --- | --- | --- |
| Windows |&#8226; |&#8226; |  |  |&#8226; |7 días |

## <a name="understanding-how-recommendations-are-prioritized"></a>Cómo se establecen prioridades entre las recomendaciones
A cada recomendación efectuada se le asigna un valor de ponderación que identifica su importancia relativa. Se muestran solo las diez recomendaciones más importantes.

### <a name="how-weights-are-calculated"></a>Cálculo de las ponderaciones
Las ponderaciones son valores agregados en función de tres factores principales:

* La *probabilidad* de que un asunto identificado cause problemas. Una probabilidad más alta equivale a una puntuación total mayor para la recomendación.
* El *impacto* del asunto en su organización en caso de que se produzca un problema. Un mayor impacto equivale a una puntuación total mayor para la recomendación.
* El *esfuerzo* necesario para implementar la recomendación. Un mayor esfuerzo equivale a una puntuación total menor para la recomendación.

La ponderación de cada recomendación se expresa como un porcentaje de la puntuación total disponible para cada área de enfoque. Por ejemplo, si una recomendación en el área de enfoque de seguridad y cumplimiento tiene una puntuación del 5 %, la implementación de esa recomendación aumenta la puntuación total de seguridad y cumplimiento en un 5 %.

### <a name="focus-areas"></a>Áreas de enfoque
**Seguridad y cumplimiento** : este apartado muestra recomendaciones en caso de posibles amenazas e infracciones de seguridad, directivas corporativas y requisitos de cumplimiento técnico, legal y reglamentario.

**Disponibilidad y continuidad empresarial** : este apartado muestra recomendaciones relacionadas con la disponibilidad de servicio, la resistencia de la infraestructura y la protección del negocio.

**Rendimiento y escalabilidad** : este apartado muestra recomendaciones que ayudarán a crecer a la infraestructura de TI de su organización y garantizarán que el entorno de TI cumple los requisitos de rendimiento vigentes y que, además, puede responder a las cambiantes necesidades de infraestructura.

**Actualización, migración e implementación** : este apartado muestra recomendaciones que le ayudarán a actualizar, migrar e implementar Active Directory en la infraestructura existente.

### <a name="should-you-aim-to-score-100-in-every-focus-area"></a>¿Debe tratar de conseguir una puntuación del 100 % en cada área de enfoque?
No necesariamente. Las recomendaciones se basan en los conocimientos y las experiencias adquiridos por los ingenieros de Microsoft producto de miles de visitas de clientes. Sin embargo, no hay dos infraestructuras de servidores que sean iguales, y es posible que determinadas recomendaciones puedan ser más o menos relevantes para usted. Por ejemplo, algunas recomendaciones de seguridad pueden ser menos pertinentes si las máquinas virtuales no están expuestas a Internet. Algunas recomendaciones de disponibilidad pueden ser menos relevantes para los servicios que proporcionan informes y recopilaciones de datos ad hoc de baja prioridad. Los problemas que son importantes para un negocio maduro pueden no serlo para otro que esté en sus inicios. Puede que desee identificar qué áreas de enfoque son prioritarias para usted y, posteriormente, observar cómo cambian las puntuaciones con el tiempo.

Cada recomendación incluye pautas que indican por qué es importante. Debe utilizar estas directrices para evaluar si es adecuado o no para usted implementar la recomendación, en función de la naturaleza de los servicios de TI y las necesidades empresariales de su organización.

## <a name="use-assessment-focus-area-recommendations"></a>Uso de las recomendaciones de área de enfoque de evaluación
Antes de que pueda usar una solución de evaluación en OMS, debe tener instalada la solución. Para más información sobre cómo instalar las soluciones, consulte [Incorporación de soluciones de Log Analytics desde la galería de soluciones](log-analytics-add-solutions.md). Una vez instalada, puede ver el resumen de las recomendaciones mediante el icono Evaluación en el panel Información general de OMS.

Consulte un resumen de las evaluaciones de cumplimiento para su infraestructura y, a continuación, profundice las recomendaciones.

### <a name="to-view-recommendations-for-a-focus-area-and-take-corrective-action"></a>Visualización de las recomendaciones para un área de enfoque y adopción de las medidas correctivas
1. En la página **Overview** (Información general), haga clic en el icono **Assessment** (Evaluación) correspondiente a su infraestructura de servidor.
2. En la página **Evaluación** , revise la información de resumen de una de las hojas de las áreas de enfoque y, a continuación, haga clic en una de ellas para ver las recomendaciones para dicha área de enfoque.
3. En cualquiera de las páginas de área de enfoque, puede ver las recomendaciones priorizadas que se han efectuado para su entorno. Haga clic en una recomendación en **Objetos afectados** para ver los detalles sobre por qué se realiza la recomendación.  
    ![imagen de las recomendaciones de evaluación](./media/log-analytics-ad-assessment/ad-focus.png)
4. Puede tomar las medidas correctivas que se sugieren en **Acciones sugeridas**. Cuando se haya ocupado del asunto, las evaluaciones posteriores registran las acciones recomendadas que se han realizado y aumenta su puntuación de cumplimiento normativo. Los asuntos que se hayan corregido aparecerán en **Objetos superados**.

## <a name="ignore-recommendations"></a>Omisión de las recomendaciones
Si desea omitir ciertas recomendaciones, puede crear un archivo de texto que OMS usará para evitar que aparezcan recomendaciones en los resultados de la evaluación.

### <a name="to-identify-recommendations-that-you-will-ignore"></a>Para identificar las recomendaciones que omitirá
1. Inicie sesión en su área de trabajo y abra Búsqueda de registros. Use la siguiente consulta para mostrar las recomendaciones para los equipos que presentan errores en el entorno.

   ```
   Type=ADAssessmentRecommendation RecommendationResult=Failed | select  Computer, RecommendationId, Recommendation | sort  Computer
   ```
>[!NOTE]
> Si el área de trabajo se ha actualizado al [nuevo lenguaje de consulta de Log Analytics](log-analytics-log-search-upgrade.md), la consulta anterior cambiaría como sigue.
>
> `ADAssessmentRecommendation | where RecommendationResult == "Failed" | sort by Computer asc | project Computer, RecommendationId, Recommendation`

   Captura de pantalla que muestra la consulta de Búsqueda de registros: ![recomendaciones fallidas](./media/log-analytics-ad-assessment/ad-failed-recommendations.png)
2. Elija las recomendaciones que desea omitir. Usará los valores para RecommendationId en el procedimiento siguiente.

### <a name="to-create-and-use-an-ignorerecommendationstxt-text-file"></a>Para crear y usar un archivo de texto IgnoreRecommendations.txt
1. Cree un archivo llamado IgnoreRecommendations.txt.
2. Pegue o escriba cada RecommendationId para cada recomendación que desee que Log Analytics omita en una línea independiente y, luego, guarde y cierre el archivo.
3. Coloque el archivo en la carpeta siguiente en cada equipo donde desea que OMS omita las recomendaciones.
   * En equipos con Microsoft Monitoring Agent (conectado directamente o a través de Operations Manager): *UnidadDelSistema*:\Archivos de programa\Microsoft Monitoring Agent\Agent
   * En el servidor de administración de Operations Manager: *UnidadDelSistema*:\Archivos de programa\Microsoft System Center 2012 R2\Operations Manager\Server

### <a name="to-verify-that-recommendations-are-ignored"></a>Para comprobar que se omiten las recomendaciones
Después de que se ejecute la siguiente evaluación programada, de forma predeterminada cada 7 días, las recomendaciones especificadas se marcan como *omitidas* y no aparecen en el panel de evaluación.

1. Puede usar las consultas de búsqueda de registros siguientes para enumerar todas las recomendaciones omitidas.

    ```
    Type=ADAssessmentRecommendation RecommendationResult=Ignored | select  Computer, RecommendationId, Recommendation | sort  Computer
    ```
>[!NOTE]
> Si el área de trabajo se ha actualizado al [nuevo lenguaje de consulta de Log Analytics](log-analytics-log-search-upgrade.md), la consulta anterior cambiaría como sigue.
>
> `ADAssessmentRecommendation | where RecommendationResult == "Ignored" | sort by Computer asc | project Computer, RecommendationId, Recommendation`

2. Si posteriormente decide que desea ver las recomendaciones omitidas, quite todos los archivos IgnoreRecommendations.txt. También puede quitar RecommendationID de ellos.

## <a name="ad-assessment-solutions-faq"></a>Preguntas más frecuentes sobre las soluciones de evaluación de AD
*¿Con qué frecuencia se ejecuta una evaluación?*

* La evaluación se realiza cada 7 días.

*¿Se puede configurar la frecuencia con la que se realiza la evaluación?*

* De momento, no.

*Si se detecta otro servidor después de haber agregado una solución de evaluación, ¿se evaluará?*

* Sí, una vez que se detecte, se evaluará cada 7 días a partir de entonces.

*Si se retira un servidor, ¿cuándo dejará de incluirse en la evaluación?*

* Si un servidor no envía datos durante 3 semanas, se quitará.

*¿Cuál es el nombre del proceso que realiza la recopilación de datos?*

* AdvisorAssessment.exe

*¿Cuánto tiempo tarda en recopilar datos?*

* La recopilación de datos reales en el servidor tarda aproximadamente 1 hora. Puede demorar más en servidores que tengan una gran cantidad de servidores de Active Directory.

*¿Se puede configurar el momento en que se recopilan los datos?*

* De momento, no.

*¿Por qué se muestran solo las 10 recomendaciones principales?*

* En lugar de darle una lista exhaustiva y abrumadora de tareas, se recomienda centrarse primero en las recomendaciones prioritarias. Después de aplicarlas, se mostrarán más recomendaciones. Si prefiere ver una lista detallada, puede ver todas las recomendaciones mediante Búsqueda de registros.

*¿Se puede hacer caso omiso de una recomendación?*

* Sí, consulte la sección [Omisión de las recomendaciones](#ignore-recommendations) anterior.

## <a name="next-steps"></a>Pasos siguientes
* Use [Búsquedas de registros en Log Analytics](log-analytics-log-searches.md) para ver recomendaciones y datos detallados de evaluación de AD.
