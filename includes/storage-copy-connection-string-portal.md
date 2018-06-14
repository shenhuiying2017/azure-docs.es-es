---
title: archivo de inclusión
description: archivo de inclusión
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 04/04/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: bef314e60b962e39b3a55d0fb7acb40d4d1b4c32
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/06/2018
ms.locfileid: "30920541"
---
## <a name="copy-your-credentials-from-the-azure-portal"></a>Copia de las credenciales desde Azure Portal

La aplicación de ejemplo debe autenticar el acceso a su cuenta de almacenamiento. Para realizar la autenticación, proporcionará a la aplicación sus credenciales de cuenta de almacenamiento en forma de una cadena de conexión. Para ver las credenciales de la cuenta de almacenamiento:

1. Acceda a [Azure Portal](https://portal.azure.com).
2. Busque su cuenta de almacenamiento.
3. En la sección **Configuración** de la información general de la cuenta de almacenamiento, seleccione **Claves de acceso**. Aparecen las claves de acceso de la cuenta, así como la cadena de conexión completa de cada clave.   
4. Busque el valor de **Cadena de conexión** en **key1**y haga clic en el botón **Copiar** para copiar la cadena de conexión. En el paso siguiente, agregará el valor de la cadena de conexión a una variable de entorno.

    ![Captura de pantalla que muestra cómo copiar una cadena de conexión desde Azure Portal](media/storage-copy-connection-string-portal/portal-connection-string.png)
