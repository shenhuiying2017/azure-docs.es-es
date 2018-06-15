---
title: Introducción a Azure DevOps Projects | Microsoft Docs
description: Comprender el valor para Azure DevOps Projects
services: devops-project
documentationcenter: ''
author: mlearned
manager: douge
editor: mlearned
ms.assetid: ''
ms.service: devops-project
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: ''
ms.date: 05/03/2018
ms.author: mlearned
ms.openlocfilehash: b87cb14fc707d14638c2d6a52af6f295276a2279
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/07/2018
ms.locfileid: "33779147"
---
# <a name="overview-of-azure-devops-project"></a>Introducción a Azure DevOps Projects

Azure DevOps Projects facilita la introducción a Azure. Los recursos de DevOps Projects le ayudarán a iniciar su tipo de aplicación favorito en el servicio de Azure que elija en unos pocos pasos rápidos desde Azure Portal. DevOps Projects configura todo lo necesario para desarrollar, implementar y supervisar la aplicación.
El panel de DevOps Projects le permite supervisar confirmaciones de código, compilaciones e implementaciones. Todo ello, desde una sola vista en Azure Portal.

## <a name="why-should-i-use-the-azure-devops-project"></a>¿Por qué debo usar Azure DevOps Projects?

Azure DevOps Projects automatiza la configuración de toda una canalización de integración continua (CI) y de entrega continua (CD) en Azure.  Puede comenzar con el código existente o usar uno de las aplicaciones de ejemplo proporcionadas y, a continuación, implementar rápidamente la aplicación en varios servicios de Azure, como Virtual Machines, App Service, Azure Container Service, Azure SQL Database y Azure Service Fabric.  

Azure DevOps Projects hace todo el trabajo para la configuración inicial de una canalización de DevOps, como configurar el repositorio Git inicial, configurar la canalización de CI/CD, crear un recurso de Application Insights para supervisarlo o proporcionar una sola vista de toda la creación con un panel de Azure DevOps Projects en Azure Portal.

Puede usar Azure DevOps Projects para:

* Implementar rápidamente la aplicación en Azure
* Automatizar la configuración de una canalización de CI/CD de VSTS
* Usar DevOps Projects como plantilla para ver y comprender cómo configurar correctamente CI/CD en Azure con VSTS
* Introducción a la canalización de CI/CD en Azure y, a continuación, más personalización de la canalización de versión en función de escenarios específicos

## <a name="how-do-i-use-the-azure-devops-project"></a>¿Cómo uso Azure DevOps Projects?

Azure DevOps Projects está disponible desde Azure Portal.  Un recurso de Azure DevOps Projects se crea del mismo modo que cualquier otro recurso de Azure desde el portal.  Azure DevOps Projects proporciona una experiencia de asistente paso a paso para las distintas opciones de configuración.  

Elija varias opciones de configuración como parte de la instalación inicial.  Entre estas opciones se incluyen:

* Usar la aplicación de ejemplo proporcionada o su propio código
* Seleccionar un lenguaje de aplicación
* Elegir un marco de aplicación basado en lenguaje
* Seleccionar un servicio de Azure (destino de implementación)
* Cuenta de VSTS (nueva o existente)
* Elegir la suscripción de Azure
* Seleccionar la ubicación de los servicios de Azure
* Elegir entre varios planes de tarifa de servicios de Azure

Después de usar Azure DevOps Projects, también puede eliminar todos los recursos de un solo lugar desde el panel de Azure DevOps Projects en Azure Portal.

## <a name="azure-devops-project-and-vsts-integration"></a>Integración de Azure DevOps Projects y VSTS

DevOps Projects cuenta con tecnología de VSTS.  DevOps Projects automatiza todo el trabajo necesario en VSTS para configurar elementos de CI/CD en Azure.  Se crea un repositorio Git en una cuenta de VSTS nueva o existente.  DevOps Projects confirma una aplicación de ejemplo o el código existente en un nuevo repositorio Git.  La automatización también establece un desencadenador de CI para la compilación, de modo que cada código de confirmación iniciará una compilación.  DevOps Projects también creará un desencadenador de CD e implementará cada nueva compilación correcta en el servicio de Azure de su elección.  Las definiciones de compilación y versión se pueden personalizar para otros escenarios.  También puede clonar las definiciones de compilación y versión para usarlas en otros proyectos.

Después de crear la instancia de DevOps Projects, también puede:

* Personalizar la canalización de compilación y versión
* Usar solicitudes de incorporación de cambios para administrar el flujo de código y mantener un alto nivel de calidad
* Probar y compilar cada confirmación antes de fusionar el código para elevar el listón de calidad
* Realizar un seguimiento de los problemas y el trabajo pendiente del proyecto junto con la aplicación

## <a name="how-do-i-start-using-the-azure-devops-project"></a>¿Cómo empiezo a usar Azure DevOps Projects?

* [Get Started with the Azure DevOps Projects](https://docs.microsoft.com/vsts/build-release/actions/azure-devops-project-github) (Introducción a Azure DevOps Projects)

## <a name="azure-devops-project-videos"></a>Vídeos de Azure DevOps Projects

* [Creación de integración continua y entrega continua con Azure DevOps Project](https://channel9.msdn.com/Events/Connect/2017/T174/player/)
