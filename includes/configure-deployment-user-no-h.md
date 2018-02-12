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
ms.openlocfilehash: 463e8b0122339831d5d6a65b6e41d4f697a82013
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/03/2018
---
En Cloud Shell, cree credenciales de implementación con el comando [`az webapp deployment user set`](/cli/azure/webapp/deployment/user?view=azure-cli-latest#az_webapp_deployment_user_set). Se requiere este usuario de implementación para la implementación de FTP y Git local en una aplicación web. El nombre de usuario y la contraseña predeterminados están en el nivel de la cuenta. _Son diferentes de las credenciales de suscripción de Azure._

En el siguiente ejemplo, reemplace *\<username>* y *\<password>* (corchetes angulares incluidos) por un nuevo nombre de usuario y contraseña. El nombre de usuario debe ser único en Azure. La contraseña debe tener al menos ocho caracteres, con dos de los tres elementos siguientes: letras, números y símbolos. 

```azurecli-interactive
az webapp deployment user set --user-name <username> --password <password>
```

Debe obtener una salida de JSON, con la contraseña mostrada como `null`. Si se produce un error `'Conflict'. Details: 409`, cambie el nombre de usuario. Si se produce un error ` 'Bad Request'. Details: 400`, use una contraseña más segura.

Solo es necesario crear este usuario de implementación una vez, y puede usarlo en todas las implementaciones de Azure.

> [!NOTE]
> Registre el nombre de usuario y la contraseña. Los va a necesitar para implementar la aplicación web más adelante.
>
>
