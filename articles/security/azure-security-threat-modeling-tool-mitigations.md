---
title: 'Mitigaciones: Microsoft Threat Modeling Tool (Azure) | Microsoft Docs'
description: "Página Mitigaciones de Microsoft Threat Modeling Tool, que presenta posibles soluciones para las amenazas generadas más expuestas."
services: security
documentationcenter: na
author: RodSan
manager: RodSan
editor: RodSan
ms.assetid: na
ms.service: security
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2017
ms.author: rodsan
ms.openlocfilehash: 07ef1fd3d81d795c9164741d22b5a689f86bd720
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/29/2017
---
# <a name="microsoft-threat-modeling-tool-mitigations"></a>Mitigaciones de Microsoft Threat Modeling Tool

Threat Modeling Tool es un elemento básico del Ciclo de vida de desarrollo de seguridad (SDL) de Microsoft. Permite a los arquitectos de software identificar y mitigar los posibles problemas de seguridad en una fase temprana, cuando son relativamente sencillos y poco costosos de resolver. En consecuencia, reduce en gran medida el costo total de desarrollo. Además, hemos diseñado la herramienta pensando en expertos no relacionados con la seguridad, lo que facilita el modelado de amenazas para todos los programadores, ya que se proporcionan instrucciones claras sobre cómo crear y analizar los modelos de amenazas.

Visite  **[Threat Modeling Tool](./azure-security-threat-modeling-tool.md)** .

## <a name="mitigation-categories"></a>Categorías de mitigación

Las mitigaciones de la Herramienta de modelado de amenazas se clasifican según el marco de seguridad de aplicación web, que consta de las siguientes acciones:

| Categoría | Descripción |
| -------- | ----------- |
| **[Auditoría y registro](./azure-security-threat-modeling-tool-auditing-and-logging.md)** | ¿Quién hizo qué y cuándo? La auditoría y el registro hacen referencia a cómo registra la aplicación los eventos relacionados con la seguridad. |
| **[Autenticación](./azure-security-threat-modeling-tool-authentication.md)** | ¿Quién es usted? La autenticación es el proceso en el que una entidad demuestra la identidad de otra entidad, normalmente por medio de credenciales, como un nombre de usuario y una contraseña. |
| **[Autorización](./azure-security-threat-modeling-tool-authorization.md)** | ¿Qué puede hacer? La autorización es la forma en que la aplicación proporciona controles de acceso para los recursos y operaciones. |
| **[Seguridad en las comunicaciones](./azure-security-threat-modeling-tool-communication-security.md)** | ¿Con quién está hablando? La seguridad de comunicaciones garantiza que todas las comunicaciones realizadas sean lo más seguras posibles. |
| **[Administración de configuración](./azure-security-threat-modeling-tool-configuration-management.md)** | ¿Quién ejecuta la aplicación? ¿A qué bases de datos se conecta? ¿Cómo se administra la aplicación? ¿Cómo se protege esta configuración? La administración de configuración hace referencia a cómo controla la aplicación estos problemas de funcionamiento. |
| **[Criptografía](./azure-security-threat-modeling-tool-cryptography.md)** | ¿Cómo mantiene los secretos (confidencialidad)? ¿Cómo altera y corrige los datos o las bibliotecas (integridad)? ¿Cómo proporciona valores de inicialización para valores aleatorios que deben ser de alta seguridad criptográfica? La criptografía hace referencia a cómo exige la aplicación confidencialidad e integridad. |
| **[Administración de excepciones](./azure-security-threat-modeling-tool-exception-management.md)** | Cuando se produce un error en una llamada al método en la aplicación, ¿qué hace la aplicación? ¿Cuánta información revela? ¿Devuelve una información de error descriptiva a los usuarios finales? ¿Envía información valiosa sobre excepciones al llamador? ¿La aplicación da errores leves? |
| **[Validación de entradas](./azure-security-threat-modeling-tool-input-validation.md)** | ¿Cómo sabe que las entradas que recibe la aplicación son válidas y seguras? La validación de entradas hace referencia a cómo filtra, anula o rechaza la aplicación las entradas antes de seguir con el procesamiento. Considere la posibilidad de restringir las entradas a través de puntos de entrada y de codificar las salidas a través de puntos de salida. ¿Confía en datos de orígenes como bases de datos y recursos compartidos de archivos? |
| **[Información confidencial](./azure-security-threat-modeling-tool-sensitive-data.md)** | ¿Cómo maneja la aplicación la información confidencial? La información confidencial hace referencia a cómo trata la aplicación los datos que deben protegerse en la memoria, en la red o en almacenes permanentes. |
| **[Administración de sesiones](./azure-security-threat-modeling-tool-session-management.md)** | ¿Cómo controla y protege la aplicación las sesiones de usuario? Una sesión hace referencia a una serie de interacciones relacionadas entre un usuario y la aplicación web. |

Esto ayuda a identificar:

* Dónde se realizan los errores más comunes
* Dónde son las mejoras más procesables

En consecuencia, utiliza estas categorías para centrarse y priorizar el trabajo de seguridad, por lo que si sabe que los problemas de seguridad más frecuentes se producen en las categorías de validación, autenticación y autorización de entradas, puede empezar por ahí. Para obtener más información, consulte **[este vínculo de patente](https://www.google.com/patents/US7818788)**

## <a name="next-steps"></a>Pasos siguientes

Visite  **[Amenazas de Threat Modeling Tool](./azure-security-threat-modeling-tool-threats.md)**  para obtener más información sobre las categorías de amenaza que la herramienta usa para generar las amenazas de diseño posibles.