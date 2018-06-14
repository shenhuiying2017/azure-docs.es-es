---
title: 'Ejemplo de script de la CLI Azure: restablecimiento de las credenciales de la cuenta | Microsoft Docs'
description: Use el script de la CLI de Azure para restablecer las credenciales de su cuenta y regresar a la configuración del archivo app.config.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.assetid: ''
ms.service: media-services
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/11/2018
ms.author: juliako
ms.openlocfilehash: 5ec63bd8be84481780337d0b5bc2497770f22b0d
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/14/2018
ms.locfileid: "34161387"
---
# <a name="cli-example-reset-the-account-credentials"></a>Ejemplo de la CLI: restablecimiento de las credenciales de la cuenta

El script de la CLI de Azure de este artículo le muestra cómo restablecer las credenciales de su cuenta y regresar a la configuración del archivo app.config.

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Si decide instalar y usar la CLI localmente, para este artículo es preciso que ejecute la versión 2.0.20 o posterior de la CLI de Azure. Ejecute `az --version` para encontrar la versión. Si necesita instalarla o actualizarla, consulte [Instalación de la CLI de Azure 2.0](/cli/azure/install-azure-cli). 

## <a name="example-script"></a>Script de ejemplo

[!code-azurecli-interactive[main](../../../../cli_scripts/media-services/reset-account-credentials/Reset-Account-Credentials.sh "Reset credentials")]

## <a name="next-steps"></a>Pasos siguientes

Para más información, consulte los [ejemplos de la CLI de Azure](../cli-samples.md).
