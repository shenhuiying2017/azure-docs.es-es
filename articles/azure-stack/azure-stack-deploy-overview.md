---
title: "Evaluación del Kit de desarrollo de Azure Stack | Microsoft Docs"
description: "Aprenda a implementar el Kit de desarrollo de Azure Stack con fines de evaluación."
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 01/22/2018
ms.author: jeffgilb
ms.custom: mvc
ms.openlocfilehash: 4ad2e0a91e2fd5023417722fc0a1a6fae93960d0
ms.sourcegitcommit: 5ac112c0950d406251551d5fd66806dc22a63b01
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/23/2018
---
# <a name="quickstart-evaluate-the-azure-stack-development-kit"></a>Guía de inicio rápido: Evaluación del Kit de desarrollo de Azure Stack

*Se aplica a: Azure Stack Development Kit*

[Azure Stack Development Kit](azure-stack-poc.md) es un entorno de desarrollo y pruebas que se puede implementar para evaluar y probar las características y servicios de Azure Stack. Para ponerlo en ejecución, debe preparar el hardware del entorno y ejecutar algunos scripts (esta operación tardará varias horas). Una vez finalizado, puede iniciar sesión en los portales del administrador y del usuario para administrar Azure Stack y probar ofertas. 

1. [**Planee el hardware, software y red**](azure-stack-deploy.md). El equipo que hospeda el kit de desarrollo (el host del kit de desarrollo) debe cumplir requisitos de hardware, software y red. También debe elegir entre el uso de Azure Active Directory o de los Servicios de federación de Active Directory. Asegúrese de cumplir estos requisitos previos antes de comenzar la implementación para que el proceso de instalación se ejecute sin problemas. 

2. [**Descargue y extraiga el paquete de implementación**](azure-stack-run-powershell-script.md#download-and-extract-the-development-kit). Puede descargar el paquete de implementación en el host del kit de desarrollo o en otro equipo. Los archivos de implementación extraídos necesitan hasta 60 GB de espacio libre en disco, por lo que usar otro equipo puede ayudar a reducir los requisitos de hardware para el host del kit de desarrollo.

3. [**Preparación del host del kit de desarrollo**](azure-stack-run-powershell-script.md) mediante el instalador. Después de este paso, el host del kit de desarrollo arrancará en Cloudbuilder.vhdx (una unidad de disco duro virtual que incluye un sistema operativo de arranque y los archivos de instalación de Azure Stack).

4. [**Implementación de Azure Stack Development Kit**](azure-stack-run-powershell-script.md) en el host del kit de desarrollo.

5. Si la implementación de Azure Stack usa Azure Active Directory, debe [registrar Azure Stack en Azure](azure-stack-register.md) para que pueda [descargar elementos de Azure Marketplace](azure-stack-download-azure-marketplace-item.md) a Azure Stack.

Después de completar estos pasos, tendrá un entorno de Development Kit con los portales del administrador y del usuario. A continuación, puede [conectarse e iniciar sesión](azure-stack-connect-azure-stack.md) en el portal. A continuación, puede iniciar la implementación de proveedores de recursos, crear [ofertas](azure-stack-key-features.md#regions-services-plans-offers-and-subscriptions) y rellenar [Marketplace](azure-stack-marketplace.md) de Azure Stack.
