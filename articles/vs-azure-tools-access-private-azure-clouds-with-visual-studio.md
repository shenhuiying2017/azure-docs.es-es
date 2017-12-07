---
title: Acceso a nubes privadas de Azure con Visual Studio | Microsoft Docs
description: "Obtenga información sobre cómo tener acceso a recursos de nube privada mediante Visual Studio."
services: visual-studio-online
documentationcenter: na
author: kraigb
manager: ghogen
editor: 
ms.assetid: 9d733c8d-703b-44e7-a210-bb75874c45c8
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 11/13/2017
ms.author: kraigb
ms.openlocfilehash: 54acfc7c686dc7025368c381d79cde93d7d48fc5
ms.sourcegitcommit: f847fcbf7f89405c1e2d327702cbd3f2399c4bc2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/28/2017
---
# <a name="accessing-private-azure-clouds-with-visual-studio"></a>Acceso a nubes privadas de Azure con Visual Studio

De forma predeterminada, Visual Studio admite los puntos de conexión REST de la nube de Azure. En este artículo, aprenderá a usar el certificado de la nube privada para acceder a la nube privada desde Visual Studio e interactuar con ella.

1. En Azure Portal de la nube privada, descargue el archivo de configuración de publicación o póngase en contacto con su administrador para obtenerlo. (El archivo tiene la extensión `.publishsettings`).

1. En el **Explorador de servidores** de Visual Studio, haga clic con el botón derecho en el nodo **Azure** y seleccione **Administrar y filtrar suscripciones**.

    ![Comando Administrar suscripciones](./media/vs-azure-tools-access-private-azure-clouds-with-visual-studio/IC790778.png)

1. En el cuadro de diálogo **Administrar suscripciones de Microsoft Azure**, seleccione la pestaña **Certificados** y, luego, seleccione **Importar**.

    ![Importación de certificados de Azure](./media/vs-azure-tools-access-private-azure-clouds-with-visual-studio/IC790779.png)

1. En el cuadro de diálogo **Importar suscripciones de Microsoft Azure**, seleccione **Examinar**.

    ![Botón Examinar del cuadro de diálogo Importar suscripciones de Microsoft Azure](./media/vs-azure-tools-access-private-azure-clouds-with-visual-studio/browse-button.png)

1. En el cuadro de diálogo **Abrir**, vaya al directorio donde guardó el archivo de configuración de publicación, seleccione el archivo y, luego, elija **Abrir**.

    ![Selección del archivo de configuración de publicación](./media/vs-azure-tools-access-private-azure-clouds-with-visual-studio/select-publish-settings-file.png)

1. Al volver al cuadro de diálogo **Importar suscripciones de Microsoft Azure**, seleccione **Importar**.

    ![Importación del archivo de configuración de publicación](./media/vs-azure-tools-access-private-azure-clouds-with-visual-studio/IC790780.png)

    Los certificados se importan desde el archivo de configuración de publicación a Visual Studio y ahora puede interactuar con los recursos de la nube privada.

