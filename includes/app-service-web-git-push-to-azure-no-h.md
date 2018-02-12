---
title: "archivo de inclusión"
description: "archivo de inclusión"
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 02/02/2018
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: de3be6fcd9cd1bee4cfc590a41e69d4ae2a2468b
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/03/2018
---
En la _ventana del terminal local_, agregue una instancia remota de Azure al repositorio de Git local. Reemplace _&lt;paste\_copied\_url\_here>_ por la dirección URL del repositorio de Git remoto que guardó en [Creación de una aplicación web](#create).

```bash
git remote add azure <deploymentLocalGitUrl-from-create-step>
```

Inserte en la instancia remota de Azure para implementar la aplicación con el comando siguiente. Cuando se le pida una contraseña, asegúrese de escribir la que creó en [Configuración de un usuario de implementación](#configure-a-deployment-user), no la que usa para iniciar sesión en Azure Portal.

```bash
git push azure master
```

Este comando puede tardar varios minutos en ejecutarse. Durante la ejecución, muestra información similar a la del ejemplo siguiente:
