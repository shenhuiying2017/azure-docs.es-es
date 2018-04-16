---
title: Creación de una aplicación contenedora de Azure Service Fabric en Linux | Microsoft Docs
description: En esta guía de inicio rápido, creará su primera aplicación contenedora Linux en Azure Service Fabric.  Cree una imagen de Docker con la aplicación, inserte la imagen en un registro de contenedor y compile e implemente una aplicación contenedora en Service Fabric.
services: service-fabric
documentationcenter: linux
author: suhuruli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: python
ms.topic: quickstart
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/05/2017
ms.author: suhuruli
ms.custom: mvc
ms.openlocfilehash: 1b65f54547821654f35571c55f61074a0106a292
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/06/2018
---
# <a name="quickstart-deploy-an-azure-service-fabric-linux-container-application-on-azure"></a>Inicio rápido: Implementación de una aplicación contenedora Linux de Azure Service Fabric en Azure
Azure Service Fabric es una plataforma de sistemas distribuidos para implementar y administrar microservicios y contenedores escalables y confiables. 

Esta guía de inicio rápido muestra cómo implementar contenedores Linux en un clúster de Service Fabric. Una vez completado, tendrá una aplicación de votación que consta de un front-end web de Python y un back-end de Redis en ejecución en un clúster de Service Fabric. También aprenderá cómo conmutar por error una aplicación y cómo escalar una aplicación en el clúster.

![Página web de la aplicación de votación][quickstartpic]

En esta guía de inicio rápido, se utiliza el entorno Bash en Azure Cloud Shell para ejecutar los comandos de la CLI de Service Fabric. Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/) antes de empezar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Si es la primera vez que ejecuta Cloud Shell, deberá configurar el recurso compartido de archivos `clouddrive`. Puede aceptar los valores predeterminados o conectar un recurso compartido de archivos existente. Para más información, consulte [Configuración de un recurso compartido de archivos de `clouddrive`](https://docs.microsoft.com/azure/cloud-shell/persisting-shell-storage#set-up-a-clouddrive-file-share).

## <a name="get-the-application-package"></a>Obtención del paquete de la aplicación
Para implementar contenedores en Service Fabric, necesita un conjunto de archivos de manifiesto (la definición de la aplicación), que describen los contenedores individuales y la aplicación.

En Cloud Shell, utilice git para clonar una copia de la definición de aplicación; a continuación, cambie los directorios al directorio `Voting` en el clon. 

```bash
git clone https://github.com/Azure-Samples/service-fabric-containers.git

cd service-fabric-containers/Linux/container-tutorial/Voting
```

## <a name="create-a-service-fabric-cluster"></a>Creación de un clúster de Service Fabric
Para implementar la aplicación en Azure, se necesita un clúster de Service Fabric que ejecute la aplicación. Los Party Cluster ofrecen una manera fácil de crear rápidamente un clúster de Service Fabric. Los Party Cluster son clústeres de Service Fabric gratuitos, de duración limitada, hospedados en Azure y operados por el equipo de Service Fabric. Puede usar un Party Cluster para implementar aplicaciones y aprender todo lo relacionado con la plataforma. El clúster usa un único certificado autofirmado para la seguridad de nodo a nodo y de cliente a nodo.

Inicie sesión y [únase a un clúster de Linux](http://aka.ms/tryservicefabric). Descargue los certificados PFX en el equipo. Para ello, haga clic en el vínculo **PFX**. Haga clic en el vínculo **Léame** para buscar la contraseña del certificado e instrucciones sobre cómo configurar varios entornos para el certificado. Conserve las páginas **Bienvenida** y **Léame** abiertas; se usarán algunas de las instrucciones en los pasos siguientes. 

> [!Note]
> Hay un número limitado Party Cluster disponibles por hora. Si se produce un error al intentar registrarse en un Party Cluster, puede esperar un tiempo y volver a intentarlo o puede seguir estos pasos de [Creación de un clúster de Service Fabric en Azure](service-fabric-tutorial-create-vnet-and-linux-cluster.md) para crear un clúster en su suscripción. 
> 
>Si crea su propio clúster, tenga en cuenta que el servicio de front-end web está configurado para escuchar el tráfico entrante en el puerto 80. Asegúrese de que dicho puerto está abierto en el clúster. (Si está usando un Party Cluster, el puerto estará abierto).
>

## <a name="configure-your-environment"></a>Configuración del entorno
Service Fabric proporciona varias herramientas que puede usar para administrar un clúster y sus aplicaciones:

- Service Fabric Explorer, una herramienta basada en explorador.
- Interfaz de la línea de comandos (CLI) de Service Fabric, que se ejecuta sobre la CLI de Azure 2.0.
- Comandos de PowerShell. 

En esta guía de inicio rápido, se usan la CLI de Service Fabric en Cloud Shell y Service Fabric Explorer. Las secciones siguientes muestran cómo instalar el certificado necesario para conectarse a su clúster seguro con estas herramientas.

### <a name="configure-certificate-for-the-service-fabric-cli"></a>Configuración del certificado para la CLI de Service Fabric
Para utilizar la CLI en Cloud Shell, debe cargar el archivo PFX del certificado en Cloud Shell y, a continuación, utilizarlo para crear un archivo PEM.

1. Para cargar el certificado en el directorio de trabajo actual en Cloud Shell, arrastre el archivo PFX del certificado desde la carpeta donde lo descargó en el equipo hasta la ventana de Cloud Shell.  

2. Para convertir el archivo PFX en un archivo PEM, use el comando siguiente. (En el caso de los Party Cluster, puede copiar un comando específico para su archivo PFX y la contraseña siguiendo las instrucciones de la página **Léame**).

    ```bash
    openssl pkcs12 -in party-cluster-1486790479-client-cert.pfx -out party-cluster-1486790479-client-cert.pem -nodes -passin pass:1486790479
    ``` 

### <a name="configure-certificate-for-service-fabric-explorer"></a>Configuración del certificado para Service Fabric Explorer
Para usar Service Fabric Explorer, tendrá que importar el archivo PFX del certificado que descargó del sitio web de Party Cluster en el almacén de certificados (Windows o Mac) o en el explorador mismo (Ubuntu). Es necesaria la contraseña de la clave privada del archivo PFX; puede obtenerla en la página **Léame**.

Utilice el método con el que se sienta más cómodo para importar el certificado en el sistema. Por ejemplo: 

- En Windows: haga doble clic en el archivo PFX y siga las indicaciones para instalar el certificado en el almacén personal, `Certificates - Current User\Personal\Certificates`. Como alternativa, puede usar el comando de PowerShell de las instrucciones de la página **Léame**.
- En Mac: haga doble clic en el archivo PFX y siga las indicaciones para instalar el certificado en la cadena de claves.
- En Ubuntu: Mozilla Firefox es el explorador predeterminado en Ubuntu 16.04. Para importar el certificado en Firefox, haga clic en el botón de menú en la esquina superior derecha del explorador y, a continuación, haga clic en **Opciones**. En la página **Preferencias**, utilice el cuadro de búsqueda para buscar "certificados". Haga clic en **Ver certificados**, seleccione la pestaña **Sus certificados**, haga clic en **Importar** y siga las indicaciones para importar el certificado.
 
   ![Instalación del certificado en Firefox](./media/service-fabric-quickstart-containers-linux/install-cert-firefox.png) 

## <a name="deploy-the-service-fabric-application"></a>Implementación de la aplicación de Service Fabric 
1. En Cloud Shell, conéctese al clúster de Service Fabric en Azure mediante la CLI. El punto de conexión es el punto de conexión de administración del clúster. Ha creado el archivo PEM en la sección anterior. (En el caso de los Party Cluster, puede copiar un comando específico para su archivo PEM y el punto de conexión de administración siguiendo las instrucciones de la página **Léame**).

    ```bash
    sfctl cluster select --endpoint https://linh1x87d1d.westus.cloudapp.azure.com:19080 --pem party-cluster-1277863181-client-cert.pem --no-verify
    ```

2. Use el script de instalación para copiar la definición de la aplicación de votación en el clúster, registrar el tipo de aplicación y crear una instancia de la aplicación.

    ```bash
    ./install.sh
    ```

2. Abra un explorador web y navegue hasta el punto de conexión de Service Fabric Explorer del clúster. El punto de conexión tiene el formato siguiente: https://\<my-azure-service-fabric-cluster-url>:19080/Explorer; por ejemplo, `https://linh1x87d1d.westus.cloudapp.azure.com:19080/Explorer`. </br>(En el caso de los Party Cluster, puede encontrar el punto de conexión de Service Fabric Explorer del clúster en la página **Bienvenida**). 

3. Expanda el nodo **Aplicaciones** para comprobar que ahora hay una entrada para el tipo de aplicación de votación y la instancia que ha creado.

    ![Service Fabric Explorer][sfx]

3. Para conectar con el contenedor en ejecución, abra un explorador web y navegue a la dirección URL del clúster; por ejemplo, `http://linh1x87d1d.westus.cloudapp.azure.com:80`. Debería ver la aplicación de votación en el explorador.

    ![Página web de la aplicación de votación][quickstartpic]


> [!NOTE]
> También puede implementar aplicaciones de Service Fabric con Docker Compose. Por ejemplo, puede usar el siguiente comando para implementar e instalar la aplicación en el clúster mediante Docker Compose.
>  ```bash
> sfctl compose create --deployment-name TestApp --file-path ../docker-compose.yml
> ```

## <a name="fail-over-a-container-in-a-cluster"></a>Conmutación por error de un contenedor en un clúster
Service Fabric se asegura de que las instancias de contenedor se mueven automáticamente a otros nodos del clúster en caso de error. También puede descargar manualmente un nodo de contenedores y moverlos correctamente a otros nodos del clúster. Service Fabric proporciona varias formas de escalar los servicios. En los pasos siguientes, se utiliza Service Fabric Explorer.

Para conmutar por error el contenedor de front-end, siga estos pasos:

1. Abra Service Fabric Explorer en el clúster, por ejemplo, `https://linh1x87d1d.westus.cloudapp.azure.com:19080/Explorer`.
2. Haga clic en el nodo **fabric:/Voting/azurevotefront** en la vista de árbol y expanda el nodo de partición (representado por un GUID). Observe el nombre de nodo de la vista de árbol, que le muestra los nodos en los que el contenedor se ejecuta actualmente; por ejemplo, `_nodetype_4`.
3. Expanda el nodo **Nodos** en la vista de árbol. Haga clic en el botón de puntos suspensivos (...) situado junto al nodo que ejecuta el contenedor.
4. Elija **Reiniciar** para reiniciar ese nodo y confirmar la acción de reinicio. El reinicio hace que el contenedor conmute por error a otro nodo del clúster.

    ![Vista de nodo en Service Fabric Explorer][sfxquickstartshownodetype]

## <a name="scale-applications-and-services-in-a-cluster"></a>Escalar aplicaciones y servicios en un clúster
Es fácil escalar servicios de Service Fabric en un clúster para adaptarse a la carga de los servicios. Para escalar un servicio, cambie el número de instancias que se ejecutan en el clúster.

Para escalar el servicio front-end web, siga estos pasos:

1. Abra Service Fabric Explorer en el clúster, por ejemplo, `https://linh1x87d1d.westus.cloudapp.azure.com:19080`.
2. Haga clic en el botón de puntos suspensivos (...) situado junto al nodo **fabric:/Voting/azurevotefront** en la vista de árbol y seleccione **Scale Service** (Escalar servicio).

    ![Inicio del servicio de escalado de Service Fabric Explorer][containersquickstartscale]

  Ahora puede escalar el número de instancias del servicio front-end web.

3. Cambie el número a **2** y haga clic en **Scale Service** (Escalar servicio).
4. Haga clic en el nodo **fabric:/Voting/azurevotefront** en la vista de árbol y expanda el nodo de partición (representado por un GUID).

    ![Finalización del servicio de escalado de Service Fabric Explorer][containersquickstartscaledone]

    Ahora puede ver que el servicio tiene dos instancias. En la vista de árbol, puede ver en qué nodos se ejecutan las instancias.

Mediante esta sencilla tarea de administración, hemos duplicado los recursos disponibles para el servicio de front-end para procesar la carga de usuarios. Es importante entender que no hacen falta varias instancias de un servicio para que se ejecute de forma confiable. Si se produce un error en un servicio, Service Fabric se asegurará de que se ejecute una nueva instancia de servicio en el clúster.

## <a name="clean-up-resources"></a>Limpieza de recursos
1. Para eliminar la instancia de la aplicación del clúster y anular el registro del tipo de aplicación, utilice el script de desinstalación (uninstall.sh) proporcionado en la plantilla. Este script tarda algún tiempo en limpiar la instancia, por lo que no se debe ejecutar el script de instalación inmediatamente después de este script. Puede usar Service Fabric Explorer para determinar cuándo se han eliminado la instancia y el registro del tipo de aplicación. 

    ```bash
    ./uninstall.sh
    ```

2. Si ya ha terminado de trabajar con el clúster, puede eliminar el certificado del almacén de certificados. Por ejemplo: 
   - En Windows: use el [complemento MMC de certificados](https://docs.microsoft.com/dotnet/framework/wcf/feature-details/how-to-view-certificates-with-the-mmc-snap-in). No olvide seleccionar **Mi cuenta de usuario** al agregar el complemento. Vaya a `Certificates - Current User\Personal\Certificates` y elimine el certificado.
   - En Mac: use la aplicación Keychain.
   - En Ubuntu: siga los pasos utilizados para ver los certificados y elimine el certificado.

3. Si no desea seguir usando Cloud Shell, puede eliminar la cuenta de almacenamiento asociada para evitar cargos. Cierre la sesión de Cloud Shell. En Azure Portal, haga clic en la cuenta de almacenamiento asociada con Cloud Shell y, a continuación, haga clic en **Eliminar** en la parte superior de la página y responda a las preguntas.

## <a name="next-steps"></a>Pasos siguientes
En esta guía de inicio rápido, ha implementado una aplicación de contenedor de Linux en un clúster de Service Fabric en Azure, ha realizado una conmutación por error de la aplicación y ha escalado la aplicación en el clúster. Para más información sobre cómo trabajar con contenedores de Linux en Service Fabric, siga el tutorial para aplicaciones de contenedor de Linux.

> [!div class="nextstepaction"]
> [Creación de una aplicación contenedor de Linux](./service-fabric-tutorial-create-container-images.md)


[sfx]: ./media/service-fabric-quickstart-containers-linux/containersquickstartappinstance.png
[quickstartpic]: ./media/service-fabric-quickstart-containers-linux/votingapp.png
[sfxquickstartshownodetype]:  ./media/service-fabric-quickstart-containers-linux/containersquickstartrestart.png
[containersquickstartscale]: ./media/service-fabric-quickstart-containers-linux/containersquickstartscale.png
[containersquickstartscaledone]: ./media/service-fabric-quickstart-containers-linux/containersquickstartscaledone.png
