---
title: "Introducción a Azure Automation | Microsoft Docs"
description: "Obtenga información sobre cómo usar Azure Automation para automatizar el ciclo de vida de la infraestructura y las aplicaciones."
services: automation
author: eamonoreilly
documentationcenter: 
keywords: "Azure Automation, DSC, PowerShell, Desired State Configuration, Update Management, Change Tracking, inventario, runbooks, Python, gráfico"
ms.assetid: 0cf1f3e8-dd30-4f33-b52a-e148e97802a9
ms.service: automation
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/13/2017
ms.author: eamono
ms.openlocfilehash: aab15392187a00aebf707f553a097961ce438194
ms.sourcegitcommit: a648f9d7a502bfbab4cd89c9e25aa03d1a0c412b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/22/2017
---
# <a name="an-introduction-to-azure-automation"></a>Introducción a Azure Automation

Azure Automation ofrece un servicio de configuración y de automatización basada en la nube que proporciona una administración coherente en los entornos que se encuentren dentro y fuera de Azure. Se compone de automatización de procesos, administración de actualizaciones y características de configuración. Azure Automation proporciona un control completo durante la implementación, las operaciones y la retirada de las cargas de trabajo y recursos.
En este artículo se ofrece una breve introducción sobre Azure Automation y se responden algunas preguntas habituales. Para obtener más información sobre las distintas funcionalidades, visite los vínculos a lo largo de esta introducción.

## <a name="azure-automation-capabilities"></a>Funcionalidades de Azure Automation

![Funcionalidades de la introducción a Automation](media/automation-overview/automation-overview.png)

### <a name="process-automation"></a>Automatización de procesos

Azure Automation le ofrece la posibilidad de automatizar las tareas de administración en la nube que requieren mucho tiempo, son frecuentes y propensas a errores. Esta automatización le ayuda a centrarse en el trabajo que agrega valor empresarial. Al reducir los errores y aumentar la eficacia, también contribuye en la reducción de los costos operativos. Puede integrar los servicios de Azure y otros sistemas públicos que son necesarios en la implementación, configuración y administración de los procesos de un extremo a otro. El servicio le permite [crear runbooks](automation-runbook-types.md) gráficamente en PowerShell o Python. Mediante el uso de un trabajo de runbook híbrido, puede unificar la administración mediante la organización de los entornos locales. Los [Webhook](automation-webhooks.md) proporcionan una manera de responder a solicitudes y asegurar las operaciones y la entrega continua. Para ello, activan la automatización desde ITSM, DevOps y los sistemas de supervisión.

### <a name="configuration-management"></a>Administración de configuración

[Desired State Configuration](automation-dsc-overview.md) de Azure Automation es una solución basada en la nube para DSC de PowerShell que proporciona los servicios necesarios para los entornos empresariales. Administre los recursos de DSC en Azure Automation y aplique las configuraciones a las máquinas virtuales o físicas desde un servidor de extracción DSC en la nube de Azure. Proporciona informes completos que le informan de eventos importantes, como cuando los nodos se desviaron de la configuración asignada. Puede supervisar y actualizar automáticamente la configuración de máquinas virtuales y físicas, tanto con Windows como con Linux, ya sea en la nube o en la infraestructura local.

Puede obtener un inventario sobre los recursos de los invitados para obtener una visualización de las aplicaciones instaladas y otros elementos de configuración. Las funciones de informe completo y de búsqueda están disponibles para encontrar información detallada rápidamente que le ayuda a entender qué está configurado en el sistema operativo. Puede realizar el seguimiento de los cambios en servicios, demonios, software, Registro y archivos para identificar rápidamente las posibles causas de problemas. Además, DSC puede ayudarle con el diagnóstico y alertarle cuando se produzcan cambios no deseados en su entorno.

### <a name="update-management"></a>Administración de actualizaciones

Actualice los sistemas Windows y Linux en entornos híbridos con Azure Automation. Con ello, obtiene visibilidad del cumplimiento de las actualizaciones en Azure, el entorno local y otras instancias en la nube. Puede crear implementaciones de programación para organizar la instalación de actualizaciones en una ventana de mantenimiento definida. Si una actualización no debería instalarse en una máquina, puede excluirla de una implementación.

### <a name="shared-capabilities"></a>Funcionalidades compartidas

Azure Automation consta de un conjunto de recursos compartidos que le facilitan la automatización y configuración de los entornos a escala.

* **[Control de acceso basado en rol](automation-role-based-access-control.md)**: controle el acceso a la cuenta con un rol de operador de Automation que permite que se ejecuten tareas sin tener que proporcionar funcionalidades de creación.
* **[Variables](automation-variables.md)**: proporcionan una manera de almacenar el contenido que puede usarse en runbooks y configuraciones. Puede cambiar los valores sin tener que modificar ningún runbook ni configuración que les hagan referencia.
* **[Credenciales](automation-credentials.md)**: almacene de forma segura la información confidencial que los runbooks y las configuraciones pueden usar en el tiempo de ejecución.
* **[Certificados](automation-certificates.md)**: almacénelos y póngalos a disposición en el tiempo de ejecución para que puedan usarse para la protección y autenticación de los recursos implementados.
* **[Conexiones](automation-connections.md)**: almacene información de pares de nombre-valor con información común de cuando se conecta a sistemas en los recursos de conexión. El autor del módulo define las conexiones para usarlas en el tiempo de ejecución de runbooks y configuraciones.
* **[Programaciones](automation-schedules.md)**: se usan en el servicio para activar la automatización en momentos predefinidos.
* **[Integración del control de código fuente](automation-source-control-integration.md)**: promueve la configuración como código donde los runbooks o la configuración pueden insertarse en un sistema de control de código fuente.
* **[Módulos de PowerShell](automation-integration-modules.md)**: usados para administrar Azure y otros sistemas. Importe a la cuenta de Automation para Microsoft, terceros, la comunidad o cmdlets definidos personalizados y recursos de DSC.

### <a name="windows-and-linux"></a>Windows y Linux

Azure Automation está diseñado para trabajar tanto en el entorno de nube híbrida como en Windows y Linux. Le ofrece una manera coherente de automatizar y configurar las cargas de trabajo implementadas y el sistema operativo en el cual se ejecutan.

### <a name="community-gallery"></a>Galería de la Comunidad

Examine la [Galería de Automation](automation-runbook-gallery.md) de runbooks y módulos para empezar a integrar y crear rápidamente los procesos desde la Galería de PowerShell y el Centro de scripts de Microsoft.

## <a name="common-scenarios-for-automation"></a>Escenarios comunes para Automation

Azure Automation se administra en el ciclo de vida de la infraestructura y las aplicaciones. Transferencia conocimientos al sistema sobre cómo la organización entrega y mantiene las cargas de trabajo. Cree en lenguajes comunes como PowerShell, Desired State Configuration, Python y runbooks gráficos. Obtenga un inventario completo de los recursos implementados con fines de identificación, creación de informes y cumplimiento. Identifique los cambios que pueden provocar una configuración incorrecta y mejore el cumplimiento operativo.

* **Compilación o implementación de recursos**: implemente máquinas virtuales en un entorno híbrido mediante Runbooks y plantillas de Azure Resource Manager. Intégrese en las herramientas de desarrollo como Jenkins y Visual Studio Team Services.
* **Configuración de máquinas virtuales**: evalúe y configure máquinas Windows y Linux con la configuración deseada para la infraestructura y la aplicación.
* **Supervisión**: identifique los cambios en las máquinas que están ocasionando problemas y corríjalos o remítalos a una instancia superior de los sistemas de administración.
* **Protección**: ponga en cuarentena una VM si se genera una alerta de seguridad. Establezca los requisitos de invitado.
* **Control**: configure el control de acceso basado en rol para equipos. Recupere recursos no utilizados.

## <a name="pricing-for-automation"></a>Precios para Automation

Puede revisar el precio para Azure Automation en la página de [precios](https://azure.microsoft.com/pricing/details/automation/).

## <a name="next-steps"></a>pasos siguientes

> [!div class="nextstepaction"]
> [Creación de una cuenta de Automation](automation-quickstart-create-account.md)
