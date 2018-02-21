---
title: "Notas de la versión de sprint de Azure Machine Learning Workbench para el 3 de enero de 2018"
description: "En este documento se detallan las actualizaciones de la versión de sprint 3 de Azure Machine Learning."
services: machine-learning
author: raymondlaghaeian
ms.author: raymondl
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 01/22/2018
ms.openlocfilehash: 266a56d5e247fd4926031e563c8be302599838eb
ms.sourcegitcommit: 95500c068100d9c9415e8368bdffb1f1fd53714e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/14/2018
---
# <a name="sprint-3---january-2018"></a>Sprint 3: enero de 2018 

#### <a name="version-number-01171218263"></a>Número de la versión: 0.1.1712.18263

>Le mostramos cómo puede [buscar el número de versión](known-issues-and-troubleshooting-guide.md).

Esta es la cuarta actualización de Azure Machine Learning Workbench. A continuación, encontrará las actualizaciones y mejoras de este sprint. Muchas de las actualizaciones descritas aquí son consecuencia directa de los comentarios de los usuarios. 

## <a name="notable-new-features-and-changes"></a>Nuevas características y cambios destacables
- Las actualizaciones de la pila de autenticación fuerzan el inicio de sesión y la selección de cuenta al inicio.

## <a name="detailed-updates"></a>Actualizaciones detalladas
A continuación figura una lista de actualizaciones detalladas de cada área de componente de Azure Machine Learning en esta versión de sprint.

### <a name="workbench"></a>Workbench
- Capacidad para instalar o desinstalar la aplicación mediante la opción Agregar o quitar programas.
- Las actualizaciones de la pila de autenticación fuerzan el inicio de sesión y la selección de cuenta al inicio.
- Experiencia mejorada de inicio de sesión único (SSO) en Windows.
- Los usuarios que pertenecen a varios inquilinos con credenciales diferentes ahora podrán iniciar sesión en Workbench.

#### <a name="ui"></a>UI
- Correcciones de errores y mejoras generales

### <a name="notebooks"></a>Cuadernos
- Correcciones de errores y mejoras generales

### <a name="data-preparation"></a>Preparación de los datos 
- Se han mejorado las sugerencias automáticas durante la realización de transformaciones By Example.
- Algoritmo mejorado para el inspector de frecuencia de patrones.
- Capacidad para enviar datos y comentarios de ejemplo durante la realización de transformaciones By Example. ![Imagen del vínculo par enviar comentarios sobre la transformación de la columna derivada](media/release-notes-sprint-3/SendFeedbackFromDeriveColumn.png)
- Mejoras de Spark en tiempo de ejecución
- Scala ha reemplazado a Pyspark
- Se corrigió la incapacidad para cerrar datos no aplicables del inspector de series temporales. 
- Se corrigió el tiempo de suspensión de la ejecución de preparación de datos para HDI.

### <a name="model-management-cli-updates"></a>Actualizaciones de la CLI de Administración de modelos 
  - La propiedad de la suscripción ya no es necesaria para el aprovisionamiento de recursos. El acceso del colaborador al grupo de recursos será suficiente para configurar el entorno de implementación.
  - Se habilitó la configuración del entorno local para suscripciones gratuitas. 
