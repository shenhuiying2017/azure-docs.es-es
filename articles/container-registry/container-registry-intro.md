---
title: Registro de contenedores privado de Docker en Azure | Microsoft Docs
description: "Introducción al servicio Azure Container Registry, que proporciona registros de Docker basados en la nube, administrados y privados."
services: container-registry
documentationcenter: 
author: stevelas
manager: balans
editor: dlepow
tags: 
keywords: 
ms.assetid: ee2b652b-fb7c-455b-8275-b8d4d08ffeb3
ms.service: container-registry
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/14/2016
ms.author: stevelas
translationtype: Human Translation
ms.sourcegitcommit: f299cff22d00a1c765a32838647818d18f3df85d
ms.openlocfilehash: 44bfd57feecc45e037717a35b64dd8e4bfb6a1b3

---
# <a name="what-is-azure-container-registry"></a>¿Qué es Azure Container Registry?
> [!NOTE]
> Container Registry está actualmente en vista previa.


Azure Container Registry es un servicio administrado de [registro de Docker](https://docs.docker.com/registry/) basado en Docker Registry v2 de código abierto. Cree y mantenga los registros de contenedor de Azure para almacenar y administrar las imágenes privadas del [contenedor Docker](https://www.docker.com/what-docker). Use registros de contenedor en Azure con el desarrollo de contenedor y las canalizaciones de implementación existentes y apóyese en el conjunto de la experiencia de la comunidad de Docker.

Para más información sobre Docker y los contenedores, consulte:

* [Guía del usuario de Docker](https://docs.docker.com/engine/userguide/)
* [Anuncio de versión preliminar de Azure Container Registry](https://azure.microsoft.com/blog/azure-container-registry-preview/) 

## <a name="key-concepts"></a>Conceptos clave
* **Registro**: cree un registro de contenedor o varios en la suscripción de Azure. Cada registro está respaldado por una [cuenta de almacenamiento](../storage/storage-introduction.md) estándar de Azure en la misma ubicación. Cree un registro en la misma ubicación de Azure que las implementaciones para aprovechar las ventajas del almacenamiento local, cercano a la red, de las imágenes de contenedor. 

  Los registros se denominan en un dominio raíz en función del [inquilino de Azure Active Directory](../active-directory/active-directory-howto-tenant.md) de la suscripción. Por ejemplo, si tiene una cuenta profesional en el dominio Contoso, el nombre completo del registro se parecerá a `myregistry-contoso.azurecr.io`. 
  
  Puede [controlar el acceso](container-registry-authentication.md) a un registro de contenedor mediante una [entidad de servicio](../active-directory/active-directory-application-objects.md) respaldada por Azure Active Directory o una cuenta de administrador proporcionada. Ejecute el comando estándar `docker login` para autenticarse con un registro. 

* **Repositorio**: un registro contiene uno o varios repositorios, que son grupos de imágenes de contenedor. Azure Container Registry es compatible con los espacios de nombres del repositorio de varios niveles. Esta característica le permite agrupar colecciones de imágenes relacionadas con una aplicación específica o una colección de aplicaciones con equipos operativos o de desarrollo específicos. Por ejemplo:
  
  * `myregistry-contoso.azurecr.io/aspnetcore:1.0.1` representa una imagen de toda la organización
  * `myregistry-contoso.azurecr.io/warrantydept/dotnet-build` representa una imagen que se utiliza para compilar aplicaciones .NET y compartirlas en el departamento de garantías
  * `myregistry-contoso.azrecr.io/warrantydept/customersubmissions/web` representa una imagen web, agrupada en la aplicación constomersubmissions, propiedad del departamento de garantías

* **Imagen**: se almacena en un repositorio, cada imagen es una instantánea de solo lectura de un contenedor Docker. Los registros de contenedor de Azure pueden incluir imágenes de Windows y de Linux. Controle los nombres de imagen de todas las implementaciones de contenedor. Use [comandos de Docker](https://docs.docker.com/engine/reference/commandline/) estándar para insertar imágenes en un repositorio o extraer una imagen de un repositorio. 

* **Contenedor**: un contenedor define una aplicación de software y las dependencias ajustadas en un sistema de archivos completo que incluye el código, el tiempo de ejecución, las herramientas del sistema y las bibliotecas. Ejecute contenedores Docker basados en imágenes de Windows o Linux que extrae de un registro de contenedor. Los contenedores que se ejecutan en una sola máquina comparten el kernel del sistema operativo. Los contenedores Docker son totalmente portables a todas las principales distribuciones de Linux, Mac y Windows.

## <a name="use-cases"></a>Casos de uso
Extraiga imágenes desde un registro de contenedor de Azure a varios destinos de implementación:

* **Sistemas escalables de orquestación** que administran aplicaciones en contenedores a través de clústeres de hosts, incluidos [DC/OS](https://docs.mesosphere.com/), [Docker Swarm](https://docs.docker.com/swarm/) y [Kubernetes](http://kubernetes.io/docs/).
* **Servicios de Azure** que admiten la creación y ejecución de aplicaciones a escala, entre los que se incluyen [Container Service](../container-service/index.md), [App Service](/app-service/index.md), [Batch](../batch/index.md) y [Service Fabric](../service-fabric/index.md). 

Los desarrolladores también pueden insertar en un registro de contenedor como parte de un flujo de trabajo de desarrollo de contenedor. Por ejemplo, puede dirigir un registro de contenedor desde una herramienta de desarrollo e integración continua como [Visual Studio Team Services](https://www.visualstudio.com/docs/overview) o [Jenkins](https://jenkins.io/).





## <a name="next-steps"></a>Pasos siguientes
* [Creación de un registro de contenedor mediante Azure Portal](container-registry-get-started-portal.md)
* [Creación de un registro de contenedor con la CLI de Azure](container-registry-get-started-azure-cli.md)
* [Insertar la primera imagen mediante la CLI de Docker](container-registry-get-started-docker-cli.md)
* Para crear una integración continua y un flujo de trabajo de implementación mediante Visual Studio Team Services, Azure Container Service y Azure Container Registry, consulte [este tutorial](../container-service/container-service-setup-ci-cd.md).
* Si desea configurar su propio Docker Registry privado en Azure (sin un punto de conexión público), consulte [Implementación de su propio Docker Registry privado en Azure](../virtual-machines/virtual-machines-linux-docker-registry-in-blob-storage.md).



<!--HONumber=Jan17_HO4-->


