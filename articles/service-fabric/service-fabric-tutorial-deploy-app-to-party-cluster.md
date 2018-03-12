---
title: "Implementación de una aplicación de Azure Service Fabric en un clúster desde Visual Studio | Microsoft Docs"
description: "Aprenda a implementar una aplicación en un clúster desde Visual Studio"
services: service-fabric
documentationcenter: .net
-author: mikkelhegn
-manager: msfussell
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/21/2018
ms.author: mikkelhegn
ms.custom: mvc
ms.openlocfilehash: 21c991a4e3f9ae19a4ad4a96427fdc1c91c55a1c
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/05/2018
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

## <a name="deploy-the-sample-application"></a>Implementación de la aplicación de ejemplo

### <a name="select-a-service-fabric-cluster-to-which-to-publish"></a>Selección del clúster de Service Fabric en el que se va a realizar la publicación
Ahora que la aplicación está lista, puede implementarla en un clúster directamente desde Visual Studio.

Tiene dos opciones para implementarla:
- Crear un clúster desde Visual Studio. Esta opción permite crear un clúster seguro directamente desde Visual Studio con las configuraciones preferidas. Este tipo de clúster es ideal para escenarios de prueba, donde se puede crear el clúster y, después, publicar directamente en él en Visual Studio.
- Publicar en un clúster existente en su suscripción.

En este tutorial se darán los pasos necesarios para crear un clúster desde Visual Studio. Para las demás opciones, puede copiar y pegar el punto de conexión o elegirlo desde la suscripción.
> [!NOTE]
> Muchos servicios usan el proxy inverso para comunicarse entre ellos. Tanto los clústeres creados desde Visual Studio como los clústeres de entidad tienen el proxy inverso habilitado de forma predeterminada.  Si usa un clúster existente, debe [habilitar el proxy inverso en el clúster](service-fabric-reverseproxy.md#setup-and-configuration).

### <a name="deploy-the-app-to-the-service-fabric-cluster"></a>Implementación de la aplicación en el clúster de Service Fabric

1. Haga clic con el botón derecho en el proyecto de la aplicación en el Explorador de soluciones y elija **Publicar**.

2. Inicie sesión con su cuenta de Azure para poder acceder a sus suscripciones. Este paso es opcional si se usa un clúster de entidad.

3. Despliegue la lista **Punto de conexión** y seleccione la opción "<Create New Cluster...>".
    
    ![Cuadro de diálogo de publicación](./media/service-fabric-tutorial-deploy-app-to-party-cluster/publish-app.png)
    
4. En el cuadro de diálogo "Crear clúster", modifique los siguientes valores:

    1. Especifique el nombre del clúster en el campo "Nombre del clúster", así como la suscripción y la ubicación que desee utilizar.
    2. Opcional: puede modificar el número de nodos. De forma predeterminada tiene tres nodos, lo mínimo necesario para probar escenarios de Service Fabric.
    3. Seleccione la pestaña "Certificado". En ella, escriba la contraseña que se utilizará para proteger el certificado del clúster. Este certificado le ayuda a proteger el clúster. También puede modificar la ruta de acceso a la ubicación en que desea guardar el certificado. Visual Studio también puede importar el certificado automáticamente, ya que este es un paso necesario para publicar la aplicación en el clúster.
    4. Seleccione la pestaña "Detalle de máquina virtual". Especifique la contraseña que desea usar para las máquinas virtuales (VM) que componen el clúster. El nombre de usuario y la contraseña se pueden utilizar para conectarse a las máquinas virtuales de forma remota. También debe seleccionar el tamaño de la máquina virtual y puede cambiar la imagen de la misma si fuera necesario.
    5. Opcional: en la pestaña "Opciones avanzadas" puede modificar la lista de puertos que desea que se abran en el equilibrador de carga que se crearán junto con el clúster. También puede agregar una clave de Application Insights existente que se va a utilizar para enrutar los archivos de registro de aplicaciones.
    6. Cuando haya acabado de modificar la configuración, haga clic en el botón "Crear". La creación tarda varios minutos en completarse; la ventana de salida indicará el momento en que el clúster está totalmente creado.
    
    ![Cuadro de diálogo Crear clúster](./media/service-fabric-tutorial-deploy-app-to-party-cluster/create-cluster.png)

4. Una vez que el clúster que desee usar esté listo, haga clic con el botón derecho en el proyecto de la aplicación y elija **Publicar**.

    Cuando la publicación haya finalizado, debería poder enviar una solicitud a la aplicación a través de un explorador.

5. Abra su explorador preferido, escriba la dirección del clúster (punto de conexión sin la información de puerto; por ejemplo, win1kw5649s.westus.cloudapp.azure.com).

    Ahora debería ver el mismo resultado que vio cuando se ejecuta la aplicación localmente.

    ![Respuesta de API desde el clúster](./media/service-fabric-tutorial-deploy-app-to-party-cluster/response-from-cluster.png)

## <a name="next-steps"></a>Pasos siguientes
En este tutorial aprendió lo siguiente:

> [!div class="checklist"]
> * Creación de un clúster desde Visual Studio
> * Implementar una aplicación en un clúster remoto con Visual Studio

Avance hasta el siguiente tutorial:
> [!div class="nextstepaction"]
> [Configurar la integración continua con Visual Studio Team Services](service-fabric-tutorial-deploy-app-with-cicd-vsts.md)
