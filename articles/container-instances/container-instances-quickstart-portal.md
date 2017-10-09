---
title: "Guía de inicio rápido: Creación del primer contenedor de Azure Container Instances con Azure Portal"
description: Implemente y empiece a usar Azure Container Instances
services: container-instances
documentationcenter: 
author: mmacy
manager: timlt
editor: 
tags: 
keywords: 
ms.assetid: 
ms.service: container-instances
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/25/2017
ms.author: marsma
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 16ae3d77c084651416cbc3bb1c5d28fee5e9124b
ms.contentlocale: es-es
ms.lasthandoff: 09/25/2017

---

# <a name="create-your-first-container-in-azure-container-instances"></a>Creación del primer contenedor en Azure Container Instances

Azure Container Instances facilita la creación y administración de contenedores en Azure. En esta guía de inicio rápido, crearemos un contenedor en Azure y lo expondremos en Internet con una dirección IP pública. Esta operación se completa con Azure Portal. Con solo unos clics, podrá ver esto en el explorador:

![Aplicación implementada mediante Azure Container Instances vista en el explorador][aci-app-browser]

## <a name="log-in-to-azure"></a>Inicie sesión en Azure.

Inicie sesión en Azure Portal: http://portal.azure.com/.

## <a name="create-a-container-instance"></a>Creación de instancia de contenedor

Seleccione **Nuevo** > **Contenedores** > **Azure Container Instances (versión preliminar)**.

![Comenzar a crear una instancia de contenedor nueva en Azure Portal][aci-portal-01]

Escriba los valores siguientes en los cuadros de texto **Nombre de contenedor**, **Imagen de contenedor** y **Grupo de recursos**. Deje los otros valores predeterminados y haga clic en **Aceptar**.

* Nombre de contenedor: `mycontainer`
* Imagen de contenedor: `microsoft/aci-helloworld`
* Grupos de recursos: `myResourceGroup`

![Configuración básica de una instancia de contenedor nueva en Azure Portal][aci-portal-03]

Puede crear los contenedores de Windows y Linux en Azure Container Instances. En esta guía de inicio rápido, dejaremos la configuración predeterminada de **Linux** porque especificamos un contenedor basado en Linux (`microsoft/aci-helloworld`) en el paso anterior.

Deje los otros valores en **Configuration** en su configuración predeterminada y haga clic en **Aceptar** para validar la configuración.

![Configuración de una instancia de contenedor nueva en Azure Portal][aci-portal-04]

Cuando se complete la validación, verá un resumen de la configuración del contenedor. Seleccione **Aceptar** para enviar la solicitud de implementación de contenedor.

![Resumen de la configuración de una instancia de contenedor nueva en Azure Portal][aci-portal-05]

Cuando se inicia una implementación, en el panel del portal aparece un icono que indica el progreso de la implementación. Una vez que se completa la implementación, el icono se actualiza para mostrar el nuevo grupo de contenedores **mycontainer-myc1**.

![Progreso de la creación de una instancia de contenedor nueva en Azure Portal][aci-portal-08]

Seleccione el grupo de contenedores **mycontainer-myc1** para ver sus propiedades. Anote la **dirección IP** del grupo de contenedores, además del valor **STATE** del contenedor.

![Información general del grupo de contenedores en Azure Portal][aci-portal-06]

Una vez que el contenedor pasa al estado **En ejecución**, vaya a la dirección IP que anotó en el paso anterior para mostrar la aplicación hospedada en el contenedor nuevo.

![Aplicación implementada mediante Azure Container Instances vista en el explorador][aci-app-browser]

<!-- IMAGES -->
[aci-portal-01]: ./media/container-instances-quickstart-portal/qs-portal-01.png
[aci-portal-02]: ./media/container-instances-quickstart-portal/qs-portal-02.png
[aci-portal-03]: ./media/container-instances-quickstart-portal/qs-portal-03.png
[aci-portal-04]: ./media/container-instances-quickstart-portal/qs-portal-04.png
[aci-portal-05]: ./media/container-instances-quickstart-portal/qs-portal-05.png
[aci-portal-06]: ./media/container-instances-quickstart-portal/qs-portal-06.png
[aci-app-browser]: ./media/container-instances-quickstart-portal/qs-portal-07.png
[aci-portal-08]: ./media/container-instances-quickstart-portal/qs-portal-08.png

## <a name="next-steps"></a>Pasos siguientes

En esta guía de inicio rápido, creó una instancia de Azure Container Instances a partir de una imagen en un repositorio público de Docker Hub. Si quiere intentar compilar un contenedor usted mismo e implementarlo en Azure Container Instances mediante Azure Container Registry, vaya al tutorial de Azure Container Instances.

> [!div class="nextstepaction"]
> [Tutoriales de Azure Container Instances](./container-instances-tutorial-prepare-app.md)
