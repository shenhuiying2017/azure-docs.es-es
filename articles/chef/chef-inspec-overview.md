---
title: Uso de InSpec para automatizar el cumplimiento de normativas en una infraestructura de Azure
description: Información acerca de cómo usar InSpec para detectar problemas en las implementaciones de Azure
keywords: azure, chef, devops, máquinas virtuales, información general, automatizar, inspec
ms.service: virtual-machines-linux
author: tomarcher
manager: jeconnoc
ms.author: tarcher
ms.date: 05/15/2018
ms.topic: article
ms.openlocfilehash: 4193b7fdb3932cbffa2b56b5d7eee6f3b573bd99
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/17/2018
ms.locfileid: "34259730"
---
# <a name="use-inspec-for-compliance-automation-of-your-azure-infrastructure"></a>Uso de InSpec para automatizar el cumplimiento de normativas en una infraestructura de Azure
[InSpec](https://www.chef.io/inspec/) es una plataforma sin costo y de código abierto que permite probar y auditar aplicaciones e infraestructuras. InSpec compara el estado real del sistema con el estado deseado que usted expresa en código de InSpec, que puede leer y escribir fácilmente. InSpec detecta infracciones y muestra los resultados en informes, pero le permite decidir cómo corregirlas. Puede usar InSpec para validar el estado de las máquinas virtuales que se ejecutan en Azure. También puede utilizar InSpec para analizar y validar el estado de los recursos y grupos de recursos dentro de una suscripción.

Este artículo describe las ventajas de usar InSpec para facilitar la seguridad y cumplimiento de normas en Azure.

## <a name="make-compliance-easy-to-understand-and-assess"></a>Cumplimiento fácil de entender y evaluar
Con InSpec, transformará sus requisitos en código de lenguaje natural, ejecutable y con control de versiones. Esto le permite organizar las pruebas en perfiles que admiten composición, donde define y personaliza excepciones según sea necesario.

## <a name="detect-fleet-wide-issues-and-prioritize-their-remediation"></a>Detección de problemas globales y priorización de su corrección
El modo de detección sin agente de InSpec le permite evaluar rápidamente (a escala) el nivel de riesgo. Los metadatos integrados para la puntuación de la gravedad ayudan a determinar en qué áreas centrar las estrategias de corrección.

## <a name="inspect-machines-data-and-new-saas-apis"></a>Inspección de máquinas, datos y nuevas API de SaaS
Las funcionalidades de cumplimiento de la API en la nube de InSpec permiten realizar aserciones generales y específicas sobre el cumplimiento en la nube y elaborar continuamente informes al respecto.

## <a name="satisfy-audits"></a>Cumplimiento de auditorías
Con InSpec, puede responder a las cuestiones que se formulan en una auditoría en cualquier momento, no solo en intervalos predeterminados, como trimestral o anualmente. Cuando llegue un ciclo de auditoría, InSpec le permitirá saber exactamente su nivel de cumplimiento, con lo que las conclusiones de los auditores no le pillarán por sorpresa.

## <a name="reduce-ambiguity-and-miscommunication-regarding-rules"></a>Menor ambigüedad y falta de comunicación en lo que respecta a las normas
En ocasiones, las configuraciones y procesos que se describen en los documentos pueden interpretarse de varias maneras. El código ejecutable acaba con las especulaciones sobre qué debería evaluarse reemplazándolas por pruebas tangibles con un propósito bien definido.

## <a name="keep-up-with-rapidly-changing-threat-and-compliance-landscapes"></a>Rápida adaptación a nuevos escenarios de cumplimiento y amenazas
InSpec permite escribir y publicar código de detección en el mismo día y escribir nuevas reglas para responder rápidamente a nuevas normativas. De esta manera, las modificaciones que se produzcan en las normativas o las amenazas no supondrán ya una emergencia.

## <a name="next-steps"></a>Pasos siguientes
* [Creación de una máquina virtual Windows en Azure mediante Chef](/azure/virtual-machines/windows/chef-automation)