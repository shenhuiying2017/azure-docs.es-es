---
title: "Inicios de sesión tras varios errores"
description: "Un informe que indica los usuarios que han iniciado sesión correctamente después de varios intentos del inicio de sesión consecutivos."
services: active-directory
documentationcenter: 
author: SSalahAhmed
manager: femila
editor: 
ms.assetid: e4ec1a39-9c20-418f-8a75-6497d0117176
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/04/2016
ms.author: saah;kenhoff
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: e55e0145adbdb1f41a8b8753d5555f20e96bf161


---
# <a name="sign-ins-after-multiple-failures"></a>Inicios de sesión tras varios errores
Este informe indica los usuarios que han iniciado sesión correctamente después de varios intentos del inicio de sesión consecutivos. Entre las posibles causas se incluyen las siguientes:

* El usuario había olvidado su contraseña.</li><li>El usuario es víctima de un ataque por fuerza bruta de averiguación de contraseñas que ha logrado su objetivo.

Los resultados de este informe le mostrarán el número de intentos de inicio de sesión erróneos consecutivos realizados antes del inicio de sesión correcto, así como una marca de tiempo asociada con el primer inicio de sesión correcto.

**Configuración del informe**: puede configurar el número mínimo de intentos de inicio de sesión erróneos consecutivos que se deben producir para que aparezcan en el informe. Al realizar cambios en esta configuración, es importante tener en cuenta que estos cambios no se aplicarán a cualquier inicio de sesión erróneo existente que se muestra actualmente en el informe existente. Sin embargo, se aplicarán a todos los inicios de sesión futuros. Los cambios realizados en este informe solo los pueden llevar a cabo los administradores con licencia.

![Inicios de sesión tras varios errores](./media/active-directory-reporting-sign-ins-after-multiple-failures/signInsAfterMultipleFailures.PNG)




<!--HONumber=Nov16_HO3-->


