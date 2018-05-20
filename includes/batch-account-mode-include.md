---
title: archivo de inclusión
description: archivo de inclusión
services: batch
author: dlepow
ms.service: batch
ms.topic: include
ms.date: 05/04/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 62bb91a2e51c39caf31719f72d68a6edab9205bc
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/08/2018
---
> [!NOTE]
> Al crear una cuenta de Batch, puede elegir entre dos modos de *asignación de grupo*: **suscripción de usuario** y **servicio Batch**. En la mayoría de los casos, debe usar el modo predeterminado servicio Batch, en el que los grupos se asignan en segundo plano en las suscripciones administradas por Azure. En el modo de suscripción de usuario alternativo, tanto las máquinas virtuales de Batch como otros recursos se crean directamente en su suscripción cuando se crea un grupo. El modo de suscripción de usuario es necesario si quiere crear grupos de [Azure Reserved Virtual Machine Instances](https://azure.microsoft.com/pricing/reserved-vm-instances/). Para crear una cuenta de Batch en modo de suscripción de usuario, también debe registrar su suscripción a Azure Batch y asociar la cuenta a una instancia de Azure Key Vault.