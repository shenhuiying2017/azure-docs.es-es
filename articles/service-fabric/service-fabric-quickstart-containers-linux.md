---
title: "Creación de una aplicación contenedora de Azure Service Fabric en Linux | Microsoft Docs"
description: "Cree la primera aplicación contenedora en Linux en Azure Service Fabric.  Cree una imagen de Docker con la aplicación, inserte la imagen en un registro de contenedor y compile e implemente una aplicación contenedora en Service Fabric."
services: service-fabric
documentationcenter: linux
author: suhuruli
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: python
ms.topic: quickstart
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/05/2017
ms.author: suhuruli
ms.custom: mvc
ms.openlocfilehash: 6aec2146d83c18a1e1714843cd49890f178e4fb3
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/09/2018
---
# <a name="deploy-an-azure-service-fabric-linux-container-application-on-azure"></a>Implementación de una aplicación contenedora Linux de Azure Service Fabric en Azure
Azure Service Fabric es una plataforma de sistemas distribuidos para implementar y administrar microservicios y contenedores escalables y confiables. 

Esta guía de inicio rápido muestra cómo implementar contenedores Linux en un clúster de Service Fabric. Una vez terminado, tendrá una aplicación de votación que consta de un front-end web de Python y un back-end de Redis que se ejecutan en un clúster de Service Fabric. 

![quickstartpic][quickstartpic]

En esta guía de inicio rápido, ha aprendido a hacer lo siguiente:
> [!div class="checklist"]
> * Implementar contenedores en un clúster de Service Fabric Linux de Azure
> * Escalar y realizar una conmutación por error de contenedores en Service Fabric

## <a name="prerequisite"></a>Requisito previo
1. Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/) antes de empezar.

2. Si decide instalar y usar la interfaz de la línea de comandos (CLI) localmente, asegúrese de que ejecuta la CLI de Azure versión 2.0.4 o posterior. Para averiguar la versión, ejecute el comando az --version. Si necesita instalarla o actualizarla, consulte [Instalación de la CLI de Azure 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli).

## <a name="get-application-package"></a>Obtención del paquete de aplicación
Para implementar contenedores en Service Fabric, necesita un conjunto de archivos de manifiesto (la definición de la aplicación), que describen los contenedores individuales y la aplicación.

En Cloud Shell, use git para clonar una copia de la definición de la aplicación.

```bash
git clone https://github.com/Azure-Samples/service-fabric-containers.git

cd service-fabric-containers/Linux/container-tutorial/Voting
```
## <a name="deploy-the-application-to-azure"></a>Implementación de la aplicación en Azure

### <a name="set-up-your-azure-service-fabric-cluster"></a>Configuración del clúster de Service Fabric
Para implementar la aplicación en un clúster de Azure, cree su propio clúster.

Los Party Cluster son clústeres de Service Fabric gratuitos, de duración limitada, hospedados en Azure. Los ejecuta el equipo de Service Fabric, donde cualquier usuario puede implementar aplicaciones y conocer más información sobre la plataforma. Para obtener acceso a un Party Cluster, [siga estas instrucciones](http://aka.ms/tryservicefabric). 

Para poder realizar operaciones de administración en el clúster de entidad segura, puede utilizar Service Fabric Explorer, CLI o Powershell. Para usar Service Fabric Explorer, tendrá que descargar el archivo PFX desde el sitio web de Party Cluster e importar el certificado en el almacén de certificados (Windows o Mac) o en el explorador mismo (Ubuntu). No hay ninguna contraseña para los certificados autofirmados desde el clúster de entidad. 

Para realizar operaciones de administración con Powershell o CLI, necesitará el PFX (Powershell) o PEM (CLI). Para convertir el archivo PFX en un archivo PEM, ejecute el siguiente comando:  

```bash
openssl pkcs12 -in party-cluster-1277863181-client-cert.pfx -out party-cluster-1277863181-client-cert.pem -nodes -passin pass:
```

Para obtener información sobre cómo crear su propio clúster, vea [Creación de un clúster de Service Fabric en Azure](service-fabric-tutorial-create-vnet-and-linux-cluster.md).

> [!Note]
> El servicio front-end web está configurado para escuchar en el puerto 80 el tráfico entrante. Asegúrese de que dicho puerto está abierto en el clúster. Si está usando el Party Cluster, el puerto estará abierto.
>

### <a name="install-service-fabric-command-line-interface-and-connect-to-your-cluster"></a>Instalación de la interfaz de la línea de comandos de Service Fabric y conexión al clúster

Conéctese al clúster de Service Fabric en Azure mediante la CLI de Azure. El punto de conexión es el punto de conexión de administración del clúster, por ejemplo `https://linh1x87d1d.westus.cloudapp.azure.com:19080`.

```bash
sfctl cluster select --endpoint https://linh1x87d1d.westus.cloudapp.azure.com:19080 --pem party-cluster-1277863181-client-cert.pem --no-verify
```

### <a name="deploy-the-service-fabric-application"></a>Implementación de la aplicación de Service Fabric 
Las aplicaciones de contenedores Service Fabric pueden implementarse utilizando el paquete de aplicación de Service Fabric descrito o Docker Compose. 

#### <a name="deploy-using-service-fabric-application-package"></a>Implementación con el paquete de aplicación mediante Service Fabric
Use el script de instalación proporcionado para copiar la definición de la aplicación de votación en el clúster, registrar el tipo de aplicación y crear una instancia de la aplicación.

```bash
./install.sh
```

#### <a name="deploy-the-application-using-docker-compose"></a>Implementación de la aplicación mediante Docker Compose
Implemente e instale la aplicación en el clúster de Service Fabric mediante Docker Compose con el comando siguiente.
```bash
sfctl compose create --deployment-name TestApp --file-path docker-compose.yml
```

Abra un explorador y navegue hasta Service Fabric Explorer en http://\<my-azure-service-fabric-cluster-url>:19080/Explorer - por ejemplo, `http://linh1x87d1d.westus.cloudapp.azure.com:19080/Explorer`. Expanda el nodo Applications para comprobar que ahora hay una entrada para el tipo de aplicación de votación y la instancia que ha creado.

![Service Fabric Explorer][sfx]

Conéctese al contenedor en ejecución.  Abra un explorador web que apunte a la dirección URL del clúster, por ejemplo, `http://linh1x87d1d.westus.cloudapp.azure.com:80`. Debería ver la aplicación de votación en el explorador.

![quickstartpic][quickstartpic]

## <a name="fail-over-a-container-in-a-cluster"></a>Conmutación por error de un contenedor en un clúster
Service Fabric se asegura de que sus instancias de contenedor se mueven automáticamente a otros nodos del clúster, en caso de error. También puede descargar manualmente un nodo de contenedores y moverlos correctamente a otros nodos del clúster. Tiene varias formas de escalar sus servicios. En este ejemplo, usamos Service Fabric Explorer.

Para conmutar por error el contenedor de front-end, siga estos pasos:

1. Abra Service Fabric Explorer en el clúster, por ejemplo, `http://linh1x87d1d.westus.cloudapp.azure.com:19080/Explorer`.
2. Haga clic en el nodo **fabric:/Voting/azurevotefront** en la vista de árbol y expanda el nodo de partición (representado por un GUID). Observe el nombre de nodo de la vista de árbol, que le muestra los nodos en los que el contenedor se ejecuta actualmente (por ejemplo, `_nodetype_4`)
3. Expanda el nodo **Nodos** en la vista de árbol. Haga clic en el botón de puntos suspensivos (tres puntos) situado junto al nodo que ejecuta el contenedor.
4. Elija **Reiniciar** para reiniciar ese nodo y confirmar la acción de reinicio. El reinicio hace que el contenedor conmute por error a otro nodo del clúster.

![sfxquickstartshownodetype][sfxquickstartshownodetype]

## <a name="scale-applications-and-services-in-a-cluster"></a>Escalar aplicaciones y servicios en un clúster
Es fácil escalar servicios de Service Fabric en un clúster para adaptarse a la carga de los servicios. Para escalar un servicio, cambie el número de instancias que se ejecutan en el clúster.

Para escalar el servicio front-end web, siga estos pasos:

1. Abra Service Fabric Explorer en el clúster, por ejemplo, `http://linh1x87d1d.westus.cloudapp.azure.com:19080`.
2. Haga clic en el botón de puntos suspensivos (tres puntos) situado junto al nodo **fabric:/Voting/azurevotefront** en la vista de árbol y seleccione **Scale Service** (Escalar servicio).

    ![containersquickstartscale][containersquickstartscale]

  Ahora puede escalar el número de instancias del servicio front-end web.

3. Cambie el número a **2** y haga clic en **Scale Service** (Escalar servicio).
4. Haga clic en el nodo **fabric:/Voting/azurevotefront** en la vista de árbol y expanda el nodo de partición (representado por un GUID).

    ![containersquickstartscaledone][containersquickstartscaledone]

    Ahora puede ver que el servicio tiene dos instancias. En la vista de árbol, puede ver en qué nodos se ejecutan las instancias.

Mediante esta sencilla tarea de administración, hemos duplicado los recursos disponibles para el servicio front-end para procesar la carga de usuarios. Es importante entender que no hacen falta varias instancias de un servicio para que se ejecute de forma confiable. Si se produce un error en un servicio, Service Fabric se asegurará de que se ejecute una nueva instancia de servicio en el clúster.

## <a name="clean-up"></a>Limpieza
Para eliminar la instancia de aplicación del clúster y anular el registro del tipo de aplicación, utilice el script de desinstalación proporcionado en la plantilla. Este comando tarda algún tiempo en limpiar la instancia y no se debe ejecutar el comando 'install'sh' inmediatamente después de este script. 

```bash
./uninstall.sh
```

## <a name="next-steps"></a>pasos siguientes
En este tutorial, ha aprendido a hacer lo siguiente:
> [!div class="checklist"]
> * Implementar una aplicación contenedora de Linux en Azure
> * Realizar una conmutación por error de un contenedor en un clúster de Service Fabric
> * Escalar un contenedor en un clúster de Service Fabric

* Más información acerca de cómo ejecutar [contenedores en Service Fabric](service-fabric-containers-overview.md).
* Más información acerca del [ciclo de vida de aplicaciones](service-fabric-application-lifecycle.md) de Service Fabric.
* Consulte [los ejemplos de código de contenedor de Service Fabric](https://github.com/Azure-Samples/service-fabric-containers) en GitHub.

[sfx]: ./media/service-fabric-quickstart-containers-linux/containersquickstartappinstance.png
[quickstartpic]: ./media/service-fabric-quickstart-containers-linux/votingapp.png
[sfxquickstartshownodetype]:  ./media/service-fabric-quickstart-containers-linux/containersquickstartrestart.png
[containersquickstartscale]: ./media/service-fabric-quickstart-containers-linux/containersquickstartscale.png
[containersquickstartscaledone]: ./media/service-fabric-quickstart-containers-linux/containersquickstartscaledone.png
