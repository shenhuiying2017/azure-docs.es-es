---
title: "¿Qué es el Proceso de ciencia de datos en equipo (TDSP)? | Microsoft Docs"
description: "Se proporciona una metodología de ciencia de datos que ofrece soluciones de análisis predictivo y aplicaciones inteligentes."
services: machine-learning
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: b1f677bb-eef5-4acb-9b3b-8a5819fb0e78
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/20/2017
ms.author: bradsev;
ms.openlocfilehash: 75e170f29f9a6abeeb3393e43e6a4c355749044d
ms.sourcegitcommit: cf4c0ad6a628dfcbf5b841896ab3c78b97d4eafd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/21/2017
---
# <a name="what-is-the-team-data-science-process"></a>¿Qué es el Proceso de ciencia de datos en equipo (TDSP)?

El proceso de ciencia de datos en equipo (TDSP) es una metodología de ciencia de datos ágil e iterativa para proporcionar soluciones de análisis predictivo y aplicaciones inteligentes de manera eficiente. TDSP ayuda a mejorar la colaboración en equipo y el aprendizaje. Contiene una extracción de los mejores procedimientos y estructuras de Microsoft y otros fabricantes del sector que facilitan la correcta implementación de iniciativas de ciencia de datos. El objetivo es ayudar a las empresas a que se den cuenta de las ventajas de su programa de análisis.

En este artículo se proporciona una introducción a TDSP y sus componentes principales. Aquí se ofrece una descripción genérica del proceso que se puede implementar con diversas herramientas. En vínculos a temas adicionales, se incluye una descripción más detallada de las tareas del proyecto y de los roles implicados en el ciclo de vida del proceso. También se proporcionan instrucciones sobre cómo implementar el TDSP mediante un conjunto específico de herramientas de Microsoft y la infraestructura que se usa para implementar el TDSP en nuestros equipos.

## <a name="key-components-of-the-tdsp"></a>Principales componentes del TDSP

El TDSP consta de los siguientes componentes clave:

- Una definición de **ciclo de vida de ciencia de datos**
- Una **estructura de proyecto estandarizada**
- **Infraestructura y recursos** para proyectos de ciencia de datos
- **Herramientas y utilidades** para la ejecución de proyectos


## <a name="data-science-lifecycle"></a>Ciclo de vida de ciencia de datos

El proceso de ciencia de datos en equipo (TDSP) proporciona un ciclo de vida para estructurar el desarrollo de los proyectos de ciencia de datos. El ciclo de vida describe el proceso, de principio a fin, que suelen seguir los proyectos al ejecutarlos.

Aunque esté usando otro ciclo de vida de ciencia de datos, como [CRISP-DM](https://wikipedia.org/wiki/Cross_Industry_Standard_Process_for_Data_Mining), [KDD](https://wikipedia.org/wiki/Data_mining#Process) o el proceso personalizado de su organización, puede usar también el TDSP basado en tareas en el contexto de esos ciclos de vida de desarrollo. En un nivel alto, estas distintas metodologías tienen mucho en común. 

Este ciclo de vida se ha diseñado para proyectos de ciencia de datos que se enviarán como parte de aplicaciones inteligentes. Estas aplicaciones implementan modelos de aprendizaje o inteligencia artificial de máquina para realizar un análisis predictivo. Los proyectos de ciencia de datos exploratorios o proyectos de análisis ad hoc también se pueden beneficiar del uso de este proceso. Pero, en estos casos, puede que algunos de los pasos descritos no sean necesarios.    

El ciclo de vida describe las fases principales por las que pasan normalmente los proyectos, a menudo de forma iterativa:

* **Conocimiento del negocio**
* **Adquisición y comprensión de los datos**
* **Modelado**
* **Implementación**
* **Aceptación del cliente**

Esta es una representación visual del **ciclo de vida del proceso de ciencia de datos en equipo**. 

![TDSP-Lifecycle2](./media/overview/tdsp-lifecycle2.png) 

En el tema [Team Data Science Process lifecycle](lifecycle.md) (Ciclo de vida del proceso de ciencia de datos en equipo) se describen los objetivos, las tareas y los artefactos de documentación de cada fase del ciclo de vida de TDSP. Estas tareas y artefactos están asociados con roles de proyecto:

- Arquitecto de soluciones
- Jefe de proyecto
- Científico de datos
- Responsable de proyecto 

En el siguiente diagrama se proporciona una vista de cuadrícula de las tareas (en azul) y los artefactos (en verde) asociados con cada fase del ciclo de vida (eje horizontal) de estos roles (eje vertical). 

![Roles y tareas de TDSP](./media/overview/tdsp-tasks-by-roles.png)

## <a name="standardized-project-structure"></a>Estructura de proyecto estandarizada

Cuando todos los proyectos comparten una estructura de directorio y usan plantillas para los documentos de proyecto, resulta fácil para los miembros del equipo encontrar información sobre sus proyectos. Todo el código y los documentos se almacenan en un sistema de control de versiones (VCS), como Git, TFS o Subversion para permitir la colaboración en equipo. El seguimiento de las tareas y las características en un sistema de seguimiento de proyectos ágil, como Jira, Rally o Visual Studio Team Services permite seguir más de cerca el código para conocer sus características individuales. Este seguimiento también permite a los equipos obtener mejores estimaciones de los costos. TDSP recomienda crear un repositorio independiente para cada proyecto en el VCS de cara al control de versiones, la seguridad de la información y la colaboración. La estructura estandarizada para todos los proyectos ayuda a crear conocimiento institucional en toda la organización.

Se proporcionan plantillas para la estructura de carpetas y los documentos necesarios en ubicaciones estándar. Esta estructura de carpetas organiza los archivos que contienen código para la exploración de datos y la extracción de características, y los que registran las iteraciones de los modelos. Estas plantillas permiten a los miembros del equipo comprender el trabajo que otros realizan, y agregar nuevos miembros a los equipos de forma fácil. Las plantillas de documento se pueden ver y actualizar fácilmente en formato de marcado. Use plantillas para proporcionar listas de comprobación con preguntas clave en cada proyecto y de esta forma garantizar que el problema esté bien definido y que los resultados entregados satisfagan la calidad esperada. Algunos ejemplos son:

- una carta de constitución de proyecto para documentar los problemas empresariales y el ámbito del proyecto
- informes de datos para documentar la estructura y las estadísticas de los datos sin procesar
- informes de modelo para documentar las características derivadas
- métricas de rendimiento de modelo, como curvas ROC o MSE


![Directorios de TDSP](./media/overview/tdsp-dir-structure.png)

La estructura de directorio se puede clonar desde [Github](https://github.com/Azure/Azure-TDSP-ProjectTemplate).

## <a name="infrastructure-and-resources-for-data-science-projects"></a>Infraestructura y recursos para los proyectos de ciencia de datos  

TDSP proporciona recomendaciones para administrar análisis compartido e infraestructura de almacenamiento, por ejemplo:

- sistemas de archivos en la nube para almacenar conjuntos de datos 
- bases de datos
- clústeres de macrodatos (Hadoop o Spark) 
- servicios de aprendizaje automático. 

La infraestructura de análisis y almacenamiento puede estar en la nube o en el entorno local. Aquí es donde se almacenan los conjuntos de datos sin procesar y procesados. Esta infraestructura permite un análisis reproducible. También evita la duplicación, lo que puede llevar a incoherencias y costos de infraestructura innecesarios. Se proporcionan herramientas para aprovisionar los recursos compartidos, realizar un seguimiento de ellos y permitir que cada miembro del equipo se conecte a dichos recursos de forma segura. También es una buena práctica pedir a los miembros del proyecto que creen un entorno de proceso coherente. Luego, diferentes miembros del equipo pueden replicar y validar los experimentos.

Este es un ejemplo de un equipo que trabaja en varios proyectos y que comparte diversos componentes de la infraestructura de análisis.

![Infraestructura de TDSP](./media/overview/tdsp-analytics-infra.png)


## <a name="tools-and-utilities-for-project-execution"></a>Herramientas y utilidades para la ejecución de proyectos

En la mayoría de las organizaciones la introducción de procesos presenta ciertos desafíos. Las herramientas proporcionadas para implementar el proceso y el ciclo de vida de ciencia de datos ayudan a reducir las barreras a su adopción y la normalizan. TDSP proporciona un conjunto inicial de herramientas y scripts para impulsar la adopción de TDSP dentro de un equipo. También ayuda a automatizar algunas de las tareas comunes del ciclo de vida de ciencia de datos, como la exploración de datos y el modelado de línea de base. Existe una estructura bien definida que se proporciona a los individuos para que contribuyan con herramientas y utilidades compartidas al repositorio de código compartido de su equipo. Estos recursos se pueden aprovechar luego en otros proyectos dentro del equipo o en la organización. TDSP también tiene previsto habilitar las contribuciones de herramientas y utilidades a toda la comunidad. Las utilidades de TDSP se pueden clonar desde [Github](https://github.com/Azure/Azure-TDSP-Utilities).


## <a name="next-steps"></a>Pasos siguientes

En [Team Data Science Process: Roles and tasks](https://github.com/Azure/Microsoft-TDSP/blob/master/Docs/roles-tasks.md) (Proceso de ciencia de datos en equipo: roles y tareas) se subrayan los roles y sus tareas asociadas en un equipo de ciencia de datos que se estandariza en este proceso. 
