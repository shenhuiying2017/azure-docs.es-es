---
title: Introducción a entornos de equipos basados en máquina virtual de ciencia de datos| Microsoft Docs
description: Patrones para implementar la máquina virtual de ciencia de datos como entorno de equipos de empresa.
keywords: aprendizaje profundo, AI, herramientas de ciencia de datos, data science virtual machine, análisis geoespacial, proceso de ciencia de datos en equipo
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2018
ms.author: gokuma
ms.openlocfilehash: 6a755ef4d933046377a6a25be76655b44f4bf508
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/20/2018
ms.locfileid: "34361390"
---
# <a name="data-science-virtual-machine-based-team-analytics-and-ai-environment"></a>Entorno de Team Analytics y AI basado en máquina virtual de ciencia de datos 
La [máquina virtual de ciencia de datos](overview.md) ofrece un rico un completo entorno en la nube de Azure con software pregenerado para análisis de datos y AI. Tradicionalmente, la máquina virtual de ciencia de datos se ha utilizado como escritorio de análisis individuales, y los científicos de datos, en su trabajo individual, obtienen productividad con esta noción compartida de su entorno de análisis precompilado. Como los equipos de análisis de gran tamaño prevén sus entornos de análisis para los científicos de datos y desarrolladores de AI, uno de los temas recurrentes es la obtención de una infraestructura de desarrollo y experimentación de análisis compartida que se administre en consonancia con las directivas de TI de la empresa, que también facilitan la colaboración y la coherencia entre todos los equipos de análisis o ciencia de datos. Una infraestructura compartida también permite al equipo de TI usar mejor el entorno de análisis. La infraestructura de análisis o ciencia de datos basada en equipos también se conoce por algunas organizaciones como "Analytics Sandbox", que permite a los científicos de datos entender los datos rápidamente, ejecutar experimentos, validar hipótesis y generar modelos predictivos de forma segura sin que el entorno de producción resulte afectado, al tiempo que tienen acceso a los diversos activos de datos. 

Puesto que la máquina virtual de ciencia de datos funciona en el nivel de infraestructura de Azure, los administradores de TI pueden configurarla con facilidad para que funcione conforme a las directivas de TI de la empresa, y ofrece una flexibilidad completa en la implementación de varias arquitecturas de uso compartido con acceso a activos de datos corporativos de forma controlada. 

En esta sección se describen algunos patrones e instrucciones que pueden utilizarse para implementar la máquina virtual de ciencia de datos como una infraestructura de ciencia de datos basada en equipos.  Los bloques de creación para estos patrones se aprovechan directamente en Azure IaaS (infraestructura como servicio) y, por lo tanto, son aplicables a todas las máquinas virtuales de Azure. En esta serie de artículos, se pone de relieve específicamente la aplicación de estas capacidades de infraestructura de Azure estándar a la máquina virtual de ciencia de datos. 

Algunos de los principales bloques de creación de un entorno de análisis de equipo empresarial son:

* [Auto-scaled pool of Data Science Virtual Machines](dsvm-pools.md) (Grupo autoescalado de máquinas virtuales de Data Science Virtual Machine)
* [Common Identity and access to workspace from any of the DSVMs in the pool](dsvm-common-identity.md) (Identidad común y acceso al área de trabajo desde cualquiera de las máquinas virtual de ciencia de datos del grupo)
* [Secure Access to data sources](dsvm-secure-access-keys.md) (Acceso seguro a orígenes de datos)


En esta serie de artículos, se proporcionan instrucciones e indicaciones sobre cada uno de los aspectos mencionados anteriormente. Obviamente, hay varias consideraciones adicionales y necesidades para implementar DSVM en configuraciones de empresa de gran tamaño que aún no se cubren directamente en esta serie de artículos. Aquí presentamos algunas de las otras consideraciones e indicaciones para la documentación general de Azure que se pueden usar con facilidad cuando se implementa en las instancias DSVM de su empresa. 

* [Seguridad de redes](https://docs.microsoft.com/azure/security/azure-network-security)
* [Supervisión](https://docs.microsoft.com/azure/virtual-machines/windows/monitor) y [administración](https://docs.microsoft.com/azure/virtual-machines/windows/maintenance-and-updates)
* [Registro y auditoría](https://docs.microsoft.com/azure/security/azure-log-audit)
* [Control de acceso basado en rol](https://docs.microsoft.com/azure/role-based-access-control/overview)
* [Cumplimiento y configuración de directivas](https://docs.microsoft.com/azure/azure-policy/azure-policy-introduction)
* [Antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware)
* [Cifrado](https://docs.microsoft.com/azure/virtual-machines/windows/encrypt-disks)
* [Gobernanza y detección de datos](https://docs.microsoft.com/azure/data-catalog/)

El [centro de arquitectura de Azure](https://docs.microsoft.com/en-us/azure/architecture/) también es un excelente recurso que proporciona arquitectura detallada de un extremo a otro y patrones para compilar y administrar la infraestructura de análisis basada en la nube. 
