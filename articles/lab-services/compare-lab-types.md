---
title: Comparar diferentes tipos de laboratorio en Azure Lab Services | Microsoft Docs
description: Explica y compara diferentes tipos de laboratorio que puede crear con Azure Lab Services (anteriormente DevTest Labs).
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 05/17/2018
ms.author: spelluru
ms.openlocfilehash: 22a1c90dd1a1ca305431d91a801e5293a6d08703
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/20/2018
ms.locfileid: "34361189"
---
# <a name="compare-managed-and-devtest-labs-in-azure-lab-services"></a>Comparación entre laboratorios administrados y DevTest Labs en Azure Lab Services
Puede crear dos tipos de laboratorio, **laboratorios administrados** con Azure Lab Services y **laboratorios personalizados** con Azure DevTest Labs. Si solo quiere entrar lo que necesita en un laboratorio y permitir que el servicio configure y administre la infraestructura necesaria para el laboratorio, elija un **laboratorio administrado**. Actualmente, **laboratorio educativo** es el único tipo de laboratorio administrado que se puede crear con Azure Lab Services. Si quiere administrar su propia infraestructura, cree un **laboratorio personalizado** con Azure DevTest Labs.

En las siguientes secciones, se proporcionan más detalles de estos laboratorios. 

## <a name="managed-labs"></a>Laboratorios administrados
Los laboratorios administrados ofrecen diferentes tipos de laboratorio que se ajusten a sus necesidades. Actualmente, Azure Lab Services solo admite **laboratorios educativos** como laboratorios administrados. Los laboratorios administrados le permiten comenzar inmediatamente, con una configuración mínima. El servicio controla toda la administración de la infraestructura para el laboratorio, desde la sincronización de máquinas virtuales al control de errores y el escalado de infraestructuras. Para crear un laboratorio administrado, debe crear antes una cuenta de laboratorio para la organización. La cuenta de laboratorio actúa como cuenta central en la que se administran todos los laboratorios de la organización. 

Al crear y usar recursos de Azure en estos laboratorios administrados, el servicio crea y administra los recursos en las suscripciones internas de Microsoft. No se crean en su propia suscripción de Azure. El servicio realiza un seguimiento del uso de estos recursos en las suscripciones internas de Microsoft. Este uso se factura a la suscripción de Azure que contiene la cuenta de laboratorio.   

A continuación, se indican algunos **casos de uso para laboratorios administrados**: 

- Proporcione a los alumnos un laboratorio de máquinas virtuales que se configuran exactamente con todo lo necesario para una clase. Asigne a cada alumno un número limitado de horas para el uso de máquinas virtuales para deberes o proyectos personales.
- Configure un grupo de máquinas virtuales de proceso de alto rendimiento para realizar investigaciones de procesos o gráficos intensivos. Ejecute las máquinas virtuales según sus necesidades y limpie las máquinas cuando haya terminado. 
- Desplace un laboratorio de equipos físicos del centro educativo a la nube. Escale automáticamente el número de máquinas virtuales solo al umbral de costo y uso máximo que establezca en el laboratorio.  
- Aprovisione rápidamente un laboratorio de máquinas virtuales para un host de Hackathon. Elimine el laboratorio con un solo clic cuando haya terminado. 


## <a name="devtest-labs"></a>DevTest Labs
Puede tener escenarios en los que quiera administrar toda la infraestructura y la configuración dentro de la suscripción. Para ello, puede crear un laboratorio personalizado con Azure DevTest Labs en Azure Portal. Para estos laboratorios, no es necesario crear ninguna cuenta de laboratorio. Estos laboratorios no aparecen en la cuenta de laboratorio (que existe para laboratorios administrados).  

A continuación, se indican algunos **casos de uso para DevTest Labs**: 

- Aprovisione rápidamente un laboratorio de máquinas virtuales para un host de Hackathon o una sesión práctica en una conferencia. Elimine el laboratorio con un solo clic cuando haya terminado. 
- Cree un grupo de máquinas virtuales configuradas con la aplicación y permita que el equipo use fácilmente una máquina virtual para búsquedas de errores.  
- Proporcione a los desarrolladores con máquinas virtuales configuradas con todas las herramientas que necesitan. Programe el inicio y el apagado automático para minimizar costos. 
- Cree repetidamente un laboratorio de máquinas de prueba como parte de la implementación. Pruebe los últimos bits y limpie las máquinas de prueba cuando haya terminado. 
- Configure una variedad de máquinas virtuales y agentes de pruebas configuradas de manera diferente para escalar y probar el rendimiento. 
- Ofrezca sesiones de aprendizaje para los clientes con un laboratorio configurado con la versión más reciente del producto. Ofrezca a cada cliente un número limitado de horas de uso en el laboratorio. 


## <a name="managed-labs-vs-devtest-labs"></a>Laboratorios administrados frente a DevTest Labs
En la tabla siguiente se comparan dos tipos de laboratorios compatibles con Azure Lab Services: 

| Características | Laboratorios administrados | DevTest Labs |
| -------- | ----------------  | ---------- |
| Administración de infraestructura de Azure en el laboratorio. |  Administrado automáticamente por el servicio | Administrado por cuenta propia  |
| Resistencia integrada a problemas de infraestructura | Controlado automáticamente por el servicio | Administrado por cuenta propia  |
| Administración de suscripciones | El servicio controla la asignación de recursos en las suscripciones de Microsoft compatibles con el servicio. El servicio controla automáticamente el escalado. | Administre por cuenta propia en la suscripción de Azure. Sin escalado automático de suscripciones. |
| Implementación de Azure Resource Manager en el laboratorio | No disponible | Disponible |

## <a name="next-steps"></a>Pasos siguientes
Introducción a la configuración de un laboratorio con Azure Lab Services:

- [Configuración de un laboratorio educativo](tutorial-setup-classroom-lab.md)
- [Configuración de un laboratorio personalizado](tutorial-create-custom-lab.md)
