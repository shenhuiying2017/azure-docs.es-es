---
title: Administración de instancias de Azure SQL Database mediante Azure Automation | Microsoft Docs
description: Obtenga información acerca de cómo puede usarse el servicio Azure Automation para administrar bases de datos SQL de Azure a escala.
services: sql-database, automation
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: monitor & tune
ms.topic: article
ms.date: 04/01/2018
ms.author: carlrab
ms.openlocfilehash: f0f071c2ad5e79168d89361c89a005c247599655
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/06/2018
---
# <a name="managing-azure-sql-databases-using-azure-automation"></a>Administración de instancias de Azure SQL Database mediante Azure Automation
Esta guía le ofrece el servicio Azure Automation y cómo se puede usar para simplificar la administración de las bases de datos SQL de Azure.

## <a name="what-is-azure-automation"></a>¿Qué es Azure Automation?
[Azure Automation](https://azure.microsoft.com/services/automation/) es un servicio de Azure para simplificar la administración en la nube mediante la automatización de procesos. Mediante Azure Automation, se pueden automatizar las tareas de ejecución prolongada, manuales, propensas a errores y que se repiten con frecuencia para aumentar la confiabilidad, la eficiencia y el valioso tiempo para su organización.

Azure Automation proporciona un motor de ejecución de flujo de trabajo altamente confiable y de alta disponibilidad que realiza la escalación para satisfacer sus necesidades a medida que crece la organización. En Azure Automation, los sistemas de terceros pueden interrumpir los procesos manualmente o en intervalos programados para que las tareas se realicen justo cuando sea necesario.

Reduzca la sobrecarga operativa y libere al personal de TI/DevOps para concentrarse en el trabajo que proporciona valor al negocio trasladando las tareas de administración en la nube para que se ejecuten automáticamente mediante Azure Automation.

## <a name="how-can-azure-automation-help-manage-azure-sql-databases"></a>¿Cómo puede Azure Automation ayudar a administrar bases de datos SQL de Azure?
Azure SQL Database puede administrarse en Azure Automation mediante los [cmdlets de PowerShell de Azure SQL Database](https://docs.microsoft.com/powershell/servicemanagement/azure.sqldatabase/v1.6.1/azure.sqldatabase/) que están disponibles en las [herramientas de Azure PowerShell](/powershell/azure/overview). Azure Automation tiene estos cmdlets de PowerShell de Azure SQL Database disponibles directamente para que pueda realizar todas las tareas de administración de SQL Database dentro del servicio. También puede emparejar estos cmdlets en Azure Automation con los cmdlets para otros servicios de Azure, para automatizar tareas complejas a través de los servicios de Azure y sistemas de terceros.

Azure Automation también tiene la capacidad de comunicarse con servidores SQL Server directamente, mediante la emisión de comandos SQL con PowerShell.

La [Galería de runbooks de Azure Automation](https://azure.microsoft.com/blog/2014/10/07/introducing-the-azure-automation-runbook-gallery/) contiene una gran variedad de runbooks de comunidad y equipo de producto para empezar a automatizar la administración de Azure SQL Database, otros servicios de Azure y sistemas de terceros. Los runbooks de la Galería incluyen:

* [Ejecución de consultas SQL en una Base de datos de SQL Server](https://gallery.technet.microsoft.com/scriptcenter/How-to-use-a-SQL-Command-be77f9d2)
* [Escalación vertical (arriba o abajo) de una instancia de Azure SQL Database en una programación](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-Database-e957354f)
* [Truncamiento de una tabla SQL si su base de datos se aproxima al tamaño máximo](https://gallery.technet.microsoft.com/scriptcenter/Azure-Automation-Your-SQL-30f8736b)
* [Indexación de tablas en Azure SQL Database si están muy fragmentadas](https://gallery.technet.microsoft.com/scriptcenter/Indexes-tables-in-an-Azure-73a2a8ea)

## <a name="next-steps"></a>Pasos siguientes
Ahora que ha aprendido los aspectos básicos de Azure Automation y cómo se puede usar para administrar bases de datos SQL de Azure, siga estos vínculos para obtener más información acerca de Azure Automation.

* [Información general sobre Azure Automation](../automation/automation-intro.md)
* [Mi primer runbook](../automation/automation-first-runbook-graphical.md)
* [Mapa de aprendizaje de Azure Automation](https://azure.microsoft.com/documentation/learning-paths/automation/)
* [Azure Automation: el agente SQL en la nube](https://azure.microsoft.com/blog/2014/06/26/azure-automation-your-sql-agent-in-the-cloud/) 

