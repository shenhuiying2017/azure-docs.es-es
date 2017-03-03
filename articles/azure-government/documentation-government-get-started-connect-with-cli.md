---
title: "Conexión de Azure Government con la CLI de Azure | Microsoft Docs"
description: "Información sobre la administración de su suscripción en Azure Government mediante conexión con la CLI de Azure"
services: azure-government
cloud: gov
documentationcenter: 
author: zakramer
manager: liki
ms.assetid: c7cbe993-375e-4aed-9aa5-2f4b2111f71c
ms.service: azure-government
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 02/13/2017
ms.author: zakramer
translationtype: Human Translation
ms.sourcegitcommit: 6b29e98aa3458a76e186234c2e9800294902bea9
ms.openlocfilehash: 6e070870a93d2636f076b339dd401358735a9bd0
ms.lasthandoff: 02/21/2017


---


# <a name="connect-to-azure-government-with-azure-cli"></a>Conexión de Azure Government con la CLI de Azure

Para usar la CLI de Azure, necesita conectarse con Azure Government en lugar de Azure Public. La CLI de Azure puede usarse para administrar una suscripción de gran tamaño a través del script o para tener acceso a características que no están actualmente disponibles en Azure Portal. Si ha usado la CLI de Azure en Azure Public, básicamente es lo mismo.  Las diferencias en Azure Government son:

Hay varias maneras de [instalar la CLI de Azure](https://docs.microsoft.com/en-us/azure/xplat-cli-install). Si ya tiene Node instalado, lo más sencillo es instalar el paquete npm:

Para instalar la CLI desde un paquete de npm, necesita la versión [más reciente de Node.js y npm instalada en su sistema](https://nodejs.org/en/download/package-manager/). A continuación, ejecute **instalar npm** para instalar el paquete azure-cli:

```bash
npm install -g azure-cli
```

En distribuciones de Linux, es posible que tenga que usar **sudo** para ejecutar correctamente el comando **npm**, tal como se muestra a continuación:

```bash
sudo npm install -g azure-cli
```

> [!NOTE]
> Si necesita instalar o actualizar Node.js y npm en la distribución de Linux o el SO, recomendamos instalar la versión LTS de Node.js más reciente (4.x). Si utiliza una versión anterior, podrían producirse errores de instalación.


Una vez que tenga la CLI de Azure instalada, debe iniciar sesión en Azure Government:

```
azure login --username your-user-name@your-gov-tenant.onmicrosoft.com  --environment AzureUSGovernment
```

Cuando haya iniciado sesión, puede ejecutar comandos de la CLI de Azure tal y como lo haría normalmente:

```
azure webapp list my-resource-group
```
