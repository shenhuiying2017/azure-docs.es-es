---
title: Registros de contenedores privados de Docker en Azure
description: Introducción al servicio Azure Container Registry, que proporciona registros de Docker basados en la nube, administrados y privados.
services: container-registry
author: stevelas
manager: jeconnoc
ms.service: container-registry
ms.topic: overview
ms.date: 05/08/2018
ms.author: stevelas
ms.custom: mvc
ms.openlocfilehash: 883da7d1487922f5cd986a67d7a7bded78ea3759
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/11/2018
ms.locfileid: "34058087"
---
# <a name="introduction-to-private-docker-container-registries-in-azure"></a>Introducción a los registros de contenedores privados de Docker en Azure

Azure Container Registry es un servicio administrado de [Docker Registry](https://docs.docker.com/registry/) basado en Docker Registry 2.0 de código abierto. Cree y mantenga los registros de contenedor de Azure para almacenar y administrar las imágenes privadas del [contenedor Docker](https://www.docker.com/what-docker).

Use los registros de contenedor en Azure con el desarrollo de contenedores y las canalizaciones de implementación existentes. Use la Azure Container Registry Build (ACR Build) para compilar imágenes de contenedor en Azure. Compile bajo demanda o automatice completamente sus compilaciones gracias al código fuente y los desencadenadores de la compilación para la actualización de la imagen de base.

Para más información sobre Docker y los contenedores, consulte la [información general de Docker](https://docs.docker.com/engine/docker-overview/).

## <a name="use-cases"></a>Casos de uso

Extraiga imágenes desde un registro de contenedor de Azure a varios destinos de implementación:

* **Sistemas escalables de orquestación** que administran aplicaciones en contenedores a través de clústeres de hosts, incluidos [DC/OS](https://docs.mesosphere.com/), [Docker Swarm](https://docs.docker.com/swarm/) y [Kubernetes](http://kubernetes.io/docs/).
* Los **Servicios de Azure** que admiten la compilación y ejecución de aplicaciones a escala, entre los que se incluyen [Azure Kubernetes Service (AKS)](../aks/index.yml), [App Service](/app-service/index.md), [Batch](../batch/index.yml), [Service Fabric](/azure/service-fabric/) y otros más.

Los desarrolladores también pueden insertar en un registro de contenedor como parte de un flujo de trabajo de desarrollo de contenedor. Por ejemplo, puede dirigir un registro de contenedor desde una herramienta de desarrollo e integración continua como [Visual Studio Team Services](https://www.visualstudio.com/docs/overview) o [Jenkins](https://jenkins.io/).

Configure las tareas de compilación de [ACR Build](#azure-container-registry-build) para volver a compilar automáticamente las imágenes de la aplicación cuando se actualicen las imágenes base. Utilice ACR Build para automatizar las compilaciones de imágenes cuando el equipo guarde el código en un repositorio de Git. *ACR Build se encuentra actualmente en versión preliminar.*

## <a name="key-concepts"></a>Conceptos clave

* **Registro**: cree un registro de contenedor o varios en la suscripción de Azure. Los registros están disponibles en tres SKU: [Basic, Standard y Premium](container-registry-skus.md), cada una de las cuales admite la integración de webhook, la autenticación del registro con Azure Active Directory y la funcionalidad de eliminación. Aproveche las ventajas del almacenamiento local, cercano a la red, de las imágenes de contenedor mediante la creación de un registro en la misma ubicación de Azure que las implementaciones. Use la característica de [replicación geográfica](container-registry-geo-replication.md) de los registros Premium en escenarios avanzados de replicación y de distribución de imágenes de contenedor. Un nombre completo de registro tiene el formato `myregistry.azurecr.io`.

  Puede [controlar el acceso](container-registry-authentication.md) a un registro de contenedor mediante una [entidad de servicio](../active-directory/active-directory-application-objects.md) respaldada por Azure Active Directory o una cuenta de administrador proporcionada. Ejecute el comando estándar `docker login` para autenticarse con un registro.

* **Repositorio**: un registro contiene uno o varios repositorios, que son grupos de imágenes de contenedor. Azure Container Registry es compatible con los espacios de nombres del repositorio de varios niveles. Con espacios de nombres de varios niveles puede agrupar colecciones de imágenes relacionadas con una aplicación específica o una colección de aplicaciones con equipos operativos o de desarrollo específicos. Por ejemplo: 

  * `myregistry.azurecr.io/aspnetcore:1.0.1` representa una imagen de toda la organización
  * `myregistry.azurecr.io/warrantydept/dotnet-build` representa una imagen que se utiliza para compilar aplicaciones .NET y compartirlas en el departamento de garantías
  * `myregistry.azurecr.io/warrantydept/customersubmissions/web` representa una imagen web, agrupada en la aplicación de envíos al cliente, propiedad del departamento de garantías.

* **Imagen**: se almacena en un repositorio, cada imagen es una instantánea de solo lectura de un contenedor Docker. Los registros de contenedor de Azure pueden incluir imágenes de Windows y de Linux. Controle los nombres de imagen de todas las implementaciones de contenedor. Use [comandos de Docker](https://docs.docker.com/engine/reference/commandline/) estándar para insertar imágenes en un repositorio o extraer una imagen de un repositorio.

* **Contenedor**: un contenedor define una aplicación de software y las dependencias ajustadas en un sistema de archivos completo que incluye el código, el tiempo de ejecución, las herramientas del sistema y las bibliotecas. Ejecute contenedores Docker basados en imágenes de Windows o Linux que extrae de un registro de contenedor. Los contenedores que se ejecutan en una sola máquina comparten el kernel del sistema operativo. Los contenedores de Docker son totalmente portátiles a las principales distribuciones de Linux, macOS y Windows.

## <a name="azure-container-registry-build-preview"></a>Azure Container Registry Build (versión preliminar)

[Azure Container Registry Build](container-registry-build-overview.md) (ACR Build) es un conjunto de características de Azure Container Registry que proporcionan compilaciones de imágenes de contenedor de Docker optimizadas y eficientes en Azure. Utilice ACR Build para llevar el bucle interno a la nube descargando operaciones de `docker build` en Azure. Configure las tareas de compilación para automatizar el sistema operativo del contenedor y la canalización de aplicaciones de revisión de marcos, y compile imágenes de forma automática cuando el equipo guarde el código en el control de origen.

[!INCLUDE [container-registry-build-preview-note](../../includes/container-registry-build-preview-note.md)]

## <a name="next-steps"></a>Pasos siguientes

* [Creación de un registro de contenedor mediante Azure Portal](container-registry-get-started-portal.md)
* [Creación de un registro de contenedor con la CLI de Azure](container-registry-get-started-azure-cli.md)
* [Automatizar la aplicación de revisiones del sistema operativo y los marcos con ACR Build](container-registry-build-overview.md) (versión preliminar)
