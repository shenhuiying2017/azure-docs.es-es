---
title: 'Administración de Azure: supervisión | Microsoft Docs'
description: Azure tiene varios servicios y herramientas que funcionan conjuntamente para proporcionar una administración completa no solo a las aplicaciones que se ejecutan en Azure, sino también en otras nubes y un entorno local.  Este artículo proporciona una descripción de alto nivel de las diferentes áreas de administración y tiene vínculos al contenido de las herramientas de Azure para administrar los recursos y aplicaciones en la nube.
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: monitoring
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/09/2018
ms.author: bwren
ms.openlocfilehash: 36dd04be167d9e8e63ab38e4af80c5766ba55370
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/16/2018
---
# <a name="azure-management---monitoring"></a>Administración de Azure: supervisión

La supervisión de Azure es uno de los aspectos de la administración de Azure.  En este artículo se describen de forma sucinta las diferentes áreas de administración necesarias para implementar y mantener las aplicaciones y recursos en Azure e incluye vínculos a documentación que puede ayudarle a comenzar.

## <a name="management-in-azure"></a>Administración en Azure

Administración hace referencia a las tareas y procesos necesarios para mantener las aplicaciones empresariales y los recursos que las dan soporte.  Azure tiene varios servicios y herramientas que funcionan conjuntamente para proporcionar una administración completa no solo a las aplicaciones que se ejecutan en Azure, sino también en otras nubes y un entorno local.  El conocimiento de las distintas herramientas disponibles y cómo pueden usarse conjuntamente en varios escenarios de administración es el primer paso para diseñar un entorno de administración completo.

El siguiente diagrama muestra las diferentes áreas de administración necesarias para mantener cualquier aplicación o recurso.  Estas diferentes áreas pueden concebirse como las distintas partes de un ciclo de vida, donde cada una de ellos es necesaria en una sucesión continua a lo largo de la duración de un recurso.  Comienza con su implementación inicial, pasa por el funcionamiento continuado y, por último, el momento en que se retira.

![Funcionalidades de administración](media/management-overview/management-capabilities.png)


Por sí solo, ningún servicio de Azure cumple totalmente los requisitos de un área de administración concreto, para ello deben trabajar varios conjuntamente.  Algunos servicios proporcionan una funcionalidad de destino, como por ejemplo Application Insights, que ofrece supervisión de aplicaciones web.  Otros proporcionan funciones comunes como Log Analytics, que almacena datos de administración de otros servicios, lo que permite analizar datos de diferentes tipos recopilados por distintos servicios.  

En las secciones siguientes se describen sucintamente las distintas áreas de administración y se proporcionan vínculos a información detallada acerca de los principales servicios de Azure diseñados para trabajar en ellas.

## <a name="monitor"></a>Supervisión
La supervisión es el acto de recopilar y analizar datos para determinar el rendimiento, el mantenimiento y la disponibilidad de su aplicación empresarial y de los recursos de los que esta depende. Una estrategia de supervisión eficaz facilitará la comprensión de la operación detallada de los distintos componentes de la aplicación y aumentará el tiempo de actividad con la notificación anticipada de errores críticos, para poder solucionarlos antes de que se conviertan en problemas.  En [Supervisión de aplicaciones y recursos de Azure](monitoring-overview.md) encontrará una descripción general de la supervisión en Azure, en la que se identifican los distintos servicios que se usan en una estrategia de supervisión.


## <a name="configure"></a>Configuración
Configurar hace referencia a la implementación inicial y a la configuración de aplicaciones y recursos, y a su mantenimiento continuado con revisiones y actualizaciones.  La automatización de estas tareas a través de scripts y directivas le permite eliminar la redundancia, reducir el tiempo y esfuerzo que debe invertir, y aumentar la precisión y eficiencia.  [Azure Automation](..\automation\automation-intro.md) proporciona la mayor parte de los servicios para automatizar las tareas de configuración.  Además de runbooks para automatizar los procesos, proporciona configuración y administración de las actualizaciones, lo que le ayudará no solo a administrar la configuración mediante directivas, sino también a identificar e implementar las actualizaciones.

## <a name="govern"></a>Control
El gobierno proporciona mecanismos y procesos para mantener el control de las aplicaciones y recursos de Azure.  Conlleva la planificación de las iniciativas y el establecimiento de prioridades estratégicas.  En Azure, el gobierno se implementa principalmente con dos servicios.  [Azure Policy](../azure-policy/azure-policy-introduction.md) permite crear, asignar y administrar definiciones de directivas que aplican distintas reglas y acciones a los recursos, con el fin de que estos sigan siendo compatibles con los estándares corporativos y los contratos de nivel de servicio. [Azure Cost Management de Cloudyn](../cost-management/overview.md) permite realizar un seguimiento del uso y de los gastos de la nube de los recursos no solo de Azure, sino también de otros proveedores de servicios en la nube, entre los que se incluyen AWS y Google.

## <a name="secure"></a>Protección
La administración de la seguridad de las aplicaciones, recursos y datos conlleva la combinación de la evaluación de las amenazas, la recopilación y el análisis de los datos de seguridad, y la garantía de que tanto las aplicaciones como los recursos están diseñados y configurados de forma segura.  La supervisión de la seguridad y el análisis de amenazas los proporciona [Azure Security Center](../security-center/security-center-intro.md), que incluye una administración unificada de la seguridad y una protección avanzada contra amenazas para cargas de trabajo en la nube híbrida.  También debe consultar [Introducción a la seguridad de Azure](../security/azure-security.md), donde encontrará información completa acerca la seguridad en Azure e instrucciones para configurar de forma segura los recursos de Azure.


## <a name="protect"></a>Protección
Por protección se entiende garantizar que las aplicaciones y los datos estén siempre disponibles, incluso en el caso de interrupciones fuera de su control.  En Azure, la protección se proporciona mediante dos servicios.  [Azure Backup](../backup/backup-introduction-to-azure-backup.md) proporciona copias de seguridad y recuperación de los datos, tanto los de la nube como los de un entorno local.    [Azure Site Recovery](../site-recovery/site-recovery-overview.md) garantiza un alta disponibilidad de cualquier aplicación, ya que proporciona continuidad empresarial y recuperación inmediata en caso de desastre.

## <a name="migrate"></a>Migrar 
La migración hace referencia a la transición de las cargas de trabajo que se ejecutan de forma local a la nube de Azure.  [Azure Migrate](../migrate/migrate-overview.md) es un servicio que ayuda a evaluar la idoneidad de la migración a Azure, lo que incluye el cálculo del costo y el ajuste de tamaño basados en el rendimiento, de las máquinas virtuales locales.  Azure Site Recovery puede ayudarle a realizar la migración real de máquinas virtuales bien [desde entornos locales](../site-recovery/migrate-tutorial-on-premises-azure.md) o [desde Amazon Web Services](../site-recovery/migrate-tutorial-aws-azure.md).  [Azure Database Migration](../dms/dms-overview.md) le ayudará en la migración de varios orígenes de base de datos a plataformas de datos de Azure.


## <a name="operations-management-suite"></a>Operations Management Suite
La documentación técnica anterior relacionada con la administración de Azure incluía Operations Management Suite (OMS), que es una agrupación de los siguientes servicios de administración de Azure:

- Azure Automation
- Azure Backup
- Log Analytics
- Site Recovery

Hemos empezado a dejar de describir esta agrupación en nuestra documentación técnica ya que toda la administración de Azure se ha expandido para incluir otros servicios. Ninguno de los servicios que forman parte de OMS han cambiado y cada uno tiene todavía un papel fundamental en la administración de las aplicaciones y recursos de Azure. Ahora debería centrarse en las tareas de administración que necesita realizar y en los diferentes servicios de Azure que trabajan juntos en cada tarea.