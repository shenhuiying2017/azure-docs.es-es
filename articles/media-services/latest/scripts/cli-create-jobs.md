---
title: 'Ejemplo de script de la CLI de Azure: creación y envío de un trabajo | Microsoft Docs'
description: El script de la CLI de Azure de este tema muestra cómo enviar un trabajo a una transformación de codificación sencilla mediante una dirección URL HTTPS.
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
ms.openlocfilehash: 4c11a959f23369aba68175ac3968cdd0deb804e8
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/14/2018
ms.locfileid: "34161336"
---
# <a name="cli-example-create-and-submit-a-job"></a>Ejemplo de CLI: creación y envío de un trabajo

El script de la CLI de Azure de este artículo muestra cómo crear y enviar un trabajo a una transformación de codificación sencilla mediante una dirección URL HTTPS.

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Si decide instalar y usar la CLI localmente, para este artículo es preciso que ejecute la versión 2.0.20 o posterior de la CLI de Azure. Ejecute `az --version` para encontrar la versión. Si necesita instalarla o actualizarla, consulte [Instalación de la CLI de Azure 2.0](/cli/azure/install-azure-cli). 

## <a name="example-script"></a>Script de ejemplo

[!code-azurecli-interactive[main](../../../../cli_scripts/media-services/create-jobs/Create-Jobs.sh "Create and submit jobs")]

## <a name="next-steps"></a>Pasos siguientes

Para más información, consulte los [ejemplos de la CLI de Azure](../cli-samples.md).
