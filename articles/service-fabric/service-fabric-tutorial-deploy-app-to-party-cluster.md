---
title: Implementación de una aplicación de Azure Service Fabric en un clúster | Microsoft Docs
description: Aprenda a implementar una aplicación en un clúster desde Visual Studio.
services: service-fabric
documentationcenter: .net
-author: rwike77
-manager: msfussell
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/11/2018
ms.author: ryanwi,mikhegn
ms.custom: mvc
ms.openlocfilehash: f75a05e965a025a3041036679ac06cfe4f1ec8d7
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/12/2018
---
# <a name="tutorial-deploy-an-application-to-a-service-fabric-cluster-in-azure"></a>Tutorial: Implementación de una aplicación en un clúster de Service Fabric en Azure
Este tutorial es la segunda parte de una serie y muestra cómo implementar una aplicación de Azure Service Fabric en un clúster nuevo de Azure directamente desde Visual Studio.

En este tutorial, aprenderá a:
> [!div class="checklist"]
> * Creación de un clúster desde Visual Studio
> * Implementar una aplicación en un clúster remoto con Visual Studio


En esta serie de tutoriales, se aprende a:
> [!div class="checklist"]
> * [Crear una aplicación de .NET Service Fabric](service-fabric-tutorial-create-dotnet-app.md)
> * Implementar la aplicación en un clúster remoto
> * [Agregar un punto de conexión HTTPS a un servicio de front-end de ASP.NET Core](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md)
> * [Configurar CI/CD con Visual Studio Team Services](service-fabric-tutorial-deploy-app-with-cicd-vsts.md)
> * [Configurar la supervisión y el diagnóstico para la aplicación](service-fabric-tutorial-monitoring-aspnet.md)


## <a name="prerequisites"></a>requisitos previos
Antes de empezar este tutorial:
- Si no tiene ninguna suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Instale Visual Studio 2017](https://www.visualstudio.com/) y las cargas de trabajo de **desarrollo de Azure** y de **desarrollo web y de ASP.NET**.
- [Instale el SDK de Service Fabric](service-fabric-get-started.md).

## <a name="download-the-voting-sample-application"></a>Descarga de la aplicación de ejemplo de votación
Si no compiló la aplicación de ejemplo de votación en la [primera parte de esta serie de tutoriales](service-fabric-tutorial-create-dotnet-app.md), puede descargarla. En una ventana Comandos, ejecute el comando siguiente para clonar el repositorio de la aplicación de ejemplo en la máquina local.

```
git clone https://github.com/Azure-Samples/service-fabric-dotnet-quickstart
```

## <a name="create-a-service-fabric-cluster"></a>Creación de un clúster de Service Fabric
Ahora que la aplicación está lista, puede implementarla en un clúster directamente desde Visual Studio. Un [clúster de Service Fabric](/service-fabric/service-fabric-deploy-anywhere.md) es un conjunto de máquinas físicas o virtuales conectadas a la red, en las que se implementan y administran los microservicios.

Tiene dos opciones para la implementación en Visual Studio:
- Crear un clúster en Azure desde Visual Studio. Esta opción permite crear un clúster seguro directamente desde Visual Studio con las configuraciones preferidas. Este tipo de clúster es ideal para escenarios de prueba, donde se puede crear el clúster y, después, publicar directamente en él en Visual Studio.
- Publicar en un clúster existente en su suscripción.  Puede crear clústeres de Service Fabric mediante [Azure Portal](https://portal.azure.com), con los scripts de [PowerShell](./scripts/service-fabric-powershell-create-secure-cluster-cert.md) o la [CLI de Azure](./scripts/cli-create-cluster.md) secuencias de comandos, o a partir de una [plantilla de Azure Resource Manager](service-fabric-tutorial-create-vnet-and-windows-cluster.md).

Este tutorial permite crear un clúster desde Visual Studio. Si ya tiene un clúster implementado, puede copiar y pegar el punto de conexión o elegirlo desde la suscripción.
> [!NOTE]
> Muchos servicios usan el proxy inverso para comunicarse entre ellos. Tanto los clústeres creados desde Visual Studio como los clústeres de entidad tienen el proxy inverso habilitado de forma predeterminada.  Si usa un clúster existente, debe [habilitar el proxy inverso en el clúster](service-fabric-reverseproxy.md#setup-and-configuration).

### <a name="find-the-votingweb-service-endpoint"></a>Buscar el punto de conexión de servicio VotingWeb
En primer lugar, busque el punto de conexión del servicio web de front-end.  El servicio web de front-end está escuchando en un puerto específico.  Cuando la aplicación se implementa en un clúster de Azure, el clúster y la aplicación se ejecutan detrás de un equilibrador de carga de Azure.  El puerto de la aplicación se debe abrir en el equilibrador de carga de Azure para que el tráfico entrante pueda pasar al servicio web.  El puerto (por ejemplo, 8080) se encuentra en el archivo *VotingWeb/PackageRoot/ServiceManifest.xml* del elemento **Endpoint**:

```xml
<Endpoint Protocol="http" Name="ServiceEndpoint" Type="Input" Port="8080" />
```

En el paso siguiente, especifique este puerto en la pestaña **Opciones avanzadas** del cuadro de diálogo **Crear clúster**.  Si va a implementar la aplicación en un clúster existente, puede abrir este puerto en el equilibrador de carga de Azure mediante un [script de PowerShell](./scripts/service-fabric-powershell-open-port-in-load-balancer.md) o en [Azure Portal](https://portal.azure.com).

### <a name="create-a-cluster-in-azure-through-visual-studio"></a>Creación de un clúster en Azure mediante Visual Studio
Haga clic con el botón derecho en el proyecto de la aplicación en el Explorador de soluciones y elija **Publicar**.

Inicie sesión con su cuenta de Azure para poder acceder a sus suscripciones. Este paso es opcional si se usa un clúster de entidad.

Despliegue la lista **Punto de conexión** y seleccione la opción **<Create New Cluster...>**.
    
![Cuadro de diálogo de publicación](./media/service-fabric-tutorial-deploy-app-to-party-cluster/publish-app.png)
    
En el cuadro de diálogo **Crear clúster**, modifique los siguientes valores:

1. Especifique el nombre del clúster en el campo **Nombre del clúster**, así como la suscripción y la ubicación que desee utilizar.
2. Opcional: puede modificar el número de nodos. De forma predeterminada tiene tres nodos, lo mínimo necesario para probar escenarios de Service Fabric.
3. Seleccione la pestaña **Certificado**. En ella, escriba la contraseña que se utilizará para proteger el certificado del clúster. Este certificado le ayuda a proteger el clúster. También puede modificar la ruta de acceso a la ubicación en que desea guardar el certificado. Visual Studio también puede importar el certificado automáticamente, ya que este es un paso necesario para publicar la aplicación en el clúster.
4. Seleccione la pestaña **Detalle de máquina virtual**. Especifique la contraseña que desea usar para las máquinas virtuales (VM) que componen el clúster. El nombre de usuario y la contraseña se pueden utilizar para conectarse a las máquinas virtuales de forma remota. También debe seleccionar el tamaño de la máquina virtual y puede cambiar la imagen de la misma si fuera necesario.
5. En la pestaña **Opciones avanzadas** puede modificar la lista de puertos que desea que se abran en el equilibrador de carga de Azure que se creó junto con el clúster.  Agregue el punto de conexión VotingWeb que detectó en un paso anterior. También puede agregar una clave de Application Insights existente que se va a utilizar para enrutar los archivos de registro de aplicaciones.
6. Cuando haya acabado de modificar la configuración, haga clic en el botón **Crear**. La creación tarda varios minutos en completarse; la ventana de salida indicará el momento en que el clúster está totalmente creado.

![Cuadro de diálogo Crear clúster](./media/service-fabric-tutorial-deploy-app-to-party-cluster/create-cluster.png)

## <a name="deploy-the-sample-application"></a>Implementación de la aplicación de ejemplo
Una vez que el clúster que desee usar esté listo, haga clic con el botón derecho en el proyecto de la aplicación y elija **Publicar**.

Cuando la publicación haya finalizado, debería poder enviar una solicitud a la aplicación a través de un explorador.

Abra su explorador preferido, escriba la dirección del clúster (punto de conexión sin la información de puerto; por ejemplo, win1kw5649s.westus.cloudapp.azure.com).

Ahora debería ver el mismo resultado que vio cuando se ejecuta la aplicación localmente.

![Respuesta de API desde el clúster](./media/service-fabric-tutorial-deploy-app-to-party-cluster/response-from-cluster.png)

## <a name="next-steps"></a>Pasos siguientes
En este tutorial aprendió lo siguiente:

> [!div class="checklist"]
> * Creación de un clúster desde Visual Studio
> * Implementar una aplicación en un clúster remoto con Visual Studio

Avance hasta el siguiente tutorial:
> [!div class="nextstepaction"]
> [Habilitamiento de HTTPS](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md)
