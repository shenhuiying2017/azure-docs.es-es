---
title: "Solución de problemas de errores del cliente Docker en Windows con Visual Studio | Microsoft Docs"
description: Solucione los problemas que encuentre al usar Visual Studio para crear e implementar aplicaciones web en Docker en Windows mediante Visual Studio 2017.
services: azure-container-service
documentationcenter: na
author: devinb
manager: douge
editor: 
ms.assetid: 346f70b9-7b52-4688-a8e8-8f53869618d3
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 10/13/2017
ms.author: devinb
ms.openlocfilehash: 90dd5df4a607568e2f3a60791da2948af7ce4e50
ms.sourcegitcommit: 38c9176c0c967dd641d3a87d1f9ae53636cf8260
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/06/2017
---
# <a name="troubleshoot-visual-studio-2017-development-with-docker"></a>Solución de problemas de desarrollo de Visual Studio 2017 con Docker

Al trabajar con Visual Studio Tools para Docker, se pueden producir problemas al generar o depurar la aplicación. A continuación, se especifican algunos pasos comunes de solución de problemas.

## <a name="volume-sharing-is-not-enabled-enable-volume-sharing-in-the-docker-ce-for-windows-settings--linux-containers-only"></a>El uso compartido de volúmenes no está habilitado. Habilite el uso compartido de volúmenes en la configuración de Docker CE para Windows (solo en contenedores con Linux)

Para resolver este problema:

1. Haga clic derecho en **Docker para Windows** en el área de notificación y, a continuación, seleccione **Configuración**.
1. Seleccione **Shared Drives** (Unidades compartidas) y comparta la unidad del sistema con la unidad donde reside el proyecto.

> [!NOTE]
> Si los archivos aparecen compartidos, puede que deba hacer clic en el vínculo "Reset credentials..." (Restablecer credenciales...) en la parte inferior del cuadro de diálogo para volver a habilitar el uso compartido de volúmenes.

![unidades compartidas](./media/vs-azure-tools-docker-troubleshooting-docker-errors/shareddrives.png)

## <a name="unable-to-start-debugging"></a>No se puede iniciar la depuración

Una razón podría estar relacionada con tener componentes de depuración obsoletos en la carpeta del perfil del usuario. Ejecute los comandos siguientes para quitar estas carpetas, a fin de que se descarguen los últimos componentes de depuración en la siguiente sesión de depuración.

- del %userprofile%\vsdbg
- del %userprofile%\onecoremsvsmon

## <a name="errors-specific-to-networking-when-debugging-your-application"></a>Errores específicos de redes al depurar una aplicación

Intente ejecutar el script que puede descargar de [Cleanup Container Host Networking](https://github.com/MicrosoftDocs/Virtualization-Documentation/tree/master/windows-server-container-tools/CleanupContainerHostNetworking) (Limpieza de redes host de contenedores), que actualizará los componentes de red en el equipo host.


## <a name="microsoftdockertools-github-repo"></a>Repositorio de GitHub de Microsoft/DockerTools

Para otros problemas que detecte, consulte los problemas de [Microsoft/DockerTools](https://github.com/microsoft/dockertools/issues).