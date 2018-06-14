---
title: Errores de nombres de recurso reservados de Azure | Microsoft Docs
description: Se explica cómo resolver errores cuando se especifica un nombre de recurso que incluye una palabra reservada.
services: azure-resource-manager
documentationcenter: ''
author: tfitzmac
manager: timlt
editor: ''
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 11/08/2017
ms.author: tomfitz
ms.openlocfilehash: b91a53d17d64afb0a56f745505f10e8cabbc22cc
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/20/2018
ms.locfileid: "34357068"
---
# <a name="resolve-reserved-resource-name-errors"></a>Resolver errores de nombres de recurso reservados

En este artículo se describe el error que puede producirse al implementar un recurso cuyo nombre contiene una palabra reservada.

## <a name="symptom"></a>Síntoma

Al implementar un recurso que está disponible a través de un punto de conexión público, puede surgir el siguiente error:

```
Code=ReservedResourceName;
Message=The resource name <resource-name> or a part of the name is a trademarked or reserved word.
```

## <a name="cause"></a>Causa

Los nombres de los recursos que tienen un punto de conexión público no pueden contener palabras reservadas o marcas comerciales.

Las siguientes palabras están reservadas:

* ACCESS
* AZURE
* BING
* BIZSPARK
* BIZTALK
* CORTANA
* DIRECTX
* DOTNET
* DYNAMICS
* EXCEL
* EXCHANGE
* FOREFRONT
* GROOVE
* HOLOLENS
* HYPER-V
* KINECT
* LYNC
* MSDN
* O365
* OFFICE
* OFFICE365
* ONEDRIVE
* ONENOTE
* OUTLOOK
* POWERPOINT
* SHAREPOINT
* SKYPE
* VISIO
* VISUALSTUDIO

Las siguientes palabras no se pueden usar ya sea como palabra completa o como subcadena en el nombre:

* LOGIN
* MICROSOFT
* WINDOWS
* XBOX

## <a name="solution"></a>Solución

Indique un nombre en el que no se use una de las palabras reservadas.