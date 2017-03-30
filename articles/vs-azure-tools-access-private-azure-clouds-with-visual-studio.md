---
title: Acceso a nubes privadas de Azure con Visual Studio | Microsoft Docs
description: "Obtenga información sobre cómo tener acceso a recursos de nube privada mediante Visual Studio."
services: visual-studio-online
documentationcenter: na
author: TomArcher
manager: douge
editor: 
ms.assetid: 9d733c8d-703b-44e7-a210-bb75874c45c8
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 03/19/2017
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: b29a3299433e57709ea31de3c7849230ea09c09a
ms.lasthandoff: 03/21/2017


---
# <a name="accessing-private-azure-clouds-with-visual-studio"></a>Acceso a nubes privadas de Azure con Visual Studio
De forma predeterminada, Visual Studio admite extremos REST de nube pública de Azure. En este tema, aprenderá a usar el certificado de la nube privada para acceder a la nube privada desde Visual Studio e interactuar con ella.

## <a name="to-access-a-private-azure-cloud-in-visual-studio"></a>Para obtener acceso a una nube privada de Azure en Visual Studio
1. En el [Portal de Azure clásico](http://go.microsoft.com/fwlink/?LinkID=213885) de la nube privada, descargue el archivo de configuración de publicación o póngase en contacto con su administrador para obtenerlo. En la versión pública de Azure, el vínculo para descargarlo es [https://manage.windowsazure.com/publishsettings/](https://manage.windowsazure.com/publishsettings/). El archivo descargado debe tener la extensión de `.publishsettings`.

1. Abra Visual Studio.

1. En **Explorador de servidores**, haga clic con el botón derecho en el nodo **Azure** y, luego, seleccione **Administrar y filtrar suscripciones** en el menú contextual.
   
    ![Comando Administrar suscripciones](./media/vs-azure-tools-access-private-azure-clouds-with-visual-studio/IC790778.png)

1. En el cuadro de diálogo **Administrar suscripciones de Microsoft Azure**, elija la pestaña **Certificados** y, luego, seleccione **Importar**.
   
    ![Importación de certificados de Azure](./media/vs-azure-tools-access-private-azure-clouds-with-visual-studio/IC790779.png)

1. En el cuadro de diálogo **Importar suscripciones de Microsoft Azure**, seleccione **Examinar**.

    ![Botón Examinar del cuadro de diálogo Importar suscripciones de Microsoft Azure](./media/vs-azure-tools-access-private-azure-clouds-with-visual-studio/browse-button.png)

1. En el cuadro de diálogo **Abrir**, vaya al directorio donde guardó el archivo de configuración de publicación, seleccione el archivo y, luego, elija **Abrir**.

    ![Selección del archivo de configuración de publicación](./media/vs-azure-tools-access-private-azure-clouds-with-visual-studio/select-publish-settings-file.png)

1. Al volver al cuadro de diálogo **Importar suscripciones de Microsoft Azure**, seleccione **Importar**.

    ![Importación del archivo de configuración de publicación](./media/vs-azure-tools-access-private-azure-clouds-with-visual-studio/IC790780.png)

    Los certificados se importan desde el archivo de configuración de publicación a Visual Studio y ahora puede interactuar con los recursos de la nube privada.
   
## <a name="next-steps"></a>Pasos siguientes
- [Publicación en un servicio en la nube de Azure desde Visual Studio](https://msdn.microsoft.com/library/azure/ee460772.aspx)
- [Descarga e importación de la configuración de publicación y la información de suscripción](https://msdn.microsoft.com/library/dn385850\(v=nav.70\).aspx)

