---
title: Introducción al servicio conectado de Key Vault en Visual Studio (proyectos de ASP.NET Core) | Microsoft Docs
description: Use este tutorial para obtener información sobre cómo agregar compatibilidad con Key Vault a una aplicación web de ASP.NET o ASP.NET Core.
services: key-vault
author: ghogen
manager: douge
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure
ms.topic: conceptual
ms.date: 04/15/2018
ms.author: ghogen
ms.openlocfilehash: 64d1a404eac955f47308f01edd56b3d008e250a0
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/07/2018
ms.locfileid: "33781784"
---
# <a name="get-started-with-key-vault-connected-service-in-visual-studio-aspnet-core-projects"></a>Introducción al servicio conectado de Key Vault en Visual Studio (proyectos de ASP.NET Core)

> [!div class="op_single_selector"]
> - [Introducción](vs-key-vault-aspnet-core-get-started.md)
> - [¿Qué ha ocurrido?](vs-key-vault-aspnet-core-what-happened.md)

En este artículo se proporcionan instrucciones adicionales después de haber agregado Key Vault a un proyecto de ASP.NET Core mediante el comando **Agregar servicios conectados** de Visual Studio. Si aún no ha agregado el servicio al proyecto, puede hacerlo en cualquier momento siguiendo las instrucciones de [Adición de Key Vault a una aplicación web mediante los servicios conectados de Visual Studio](vs-key-vault-add-connected-service.md).

Para ver los cambios realizados en el proyecto al agregar el servicio conectado, consulte [¿Qué le ha ocurrido a mi proyecto de ASP.NET Core?](vs-key-vault-aspnet-core-what-happened.md)

## <a name="after-you-connect"></a>Después de la conexión

1. Agregue un secreto a Key Vault en Azure. Para localizar la ubicación correcta en Azure Portal, haga clic en el vínculo para administrar los secretos almacenados en esta instancia de Key Vault. Si cerró la página o el proyecto, puede acceder a ellos en [Azure Portal](https://portal.azure.com); para ello, seleccione **Todos los servicios** en **Seguridad**, elija **Key Vault** y luego seleccione la instancia de Key Vault que acaba de crear.

   ![Navegación hasta Azure Portal](media/vs-key-vault-add-connected-service/manage-secrets-link.jpg)

1. En la sección Key Vault del almacén de claves creado, seleccione **Secretos** y luego **Generar/Importar**.

   ![Generar/Importar un secreto](media/vs-key-vault-add-connected-service/generate-secrets.jpg)

1. Escriba un secreto, como "MiSecreto", asígnele cualquier valor de cadena como prueba y luego seleccione el botón **Crear**.

   ![Crear un secreto](media/vs-key-vault-add-connected-service/create-a-secret.jpg)
 
1. (Opcional) Escriba otro secreto, pero, en esta ocasión, asígnele una categoría asignándole el nombre **Secretos--MiSecreto**. Esta sintaxis especifica una categoría **Secretos** que contiene un secreto **MiSecreto**.
1. En el proyecto de Visual Studio, ahora puede hacer referencia a estos secretos mediante el uso de las siguientes expresiones en el código:
 
   ```csharp
      config["MySecret"] // Access a secret without a section
      config["Secrets:MySecret"] // Access a secret in a section
      config.GetSection("Secrets")["MySecret"] // Get the configuration section and access a secret in it.
   ```

Enhorabuena, ahora se ha habilitado la aplicación web para utilizar Key Vault para acceder a los secretos almacenados de forma segura.

## <a name="clean-up-resources"></a>Limpieza de recursos

Cuando ya no necesite el grupo de recursos, elimínelo. De esta forma, se eliminan la instancia de Key Vault y los recursos relacionados. Para eliminar el grupo de recursos mediante el portal:

1. Escriba el nombre del grupo de recursos en el cuadro de búsqueda de la parte superior del portal. Cuando vea el grupo de recursos que se utiliza en esta guía de inicio rápido en los resultados de búsqueda, selecciónelo.
2. Seleccione **Eliminar grupo de recursos**.
3. En el cuadro **ESCRIBA EL NOMBRE DEL GRUPO DE RECURSOS:** escriba el nombre del grupo de recursos y seleccione **Eliminar**.

# <a name="next-steps"></a>Pasos siguientes

Más información sobre el desarrollo con Key Vault en la [Guía para desarrolladores de Key Vault](key-vault-developers-guide.md)