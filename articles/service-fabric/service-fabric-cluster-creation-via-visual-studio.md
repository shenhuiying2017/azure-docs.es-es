---
title: "Configuración de un clúster de Service Fabric mediante Visual Studio | Microsoft Docs"
description: "Describe cómo configurar un clúster de Service Fabric con una plantilla de Azure Resource Manager creada por un proyecto de grupo de recursos de Azure en Visual Studio."
services: service-fabric
documentationcenter: .net
author: mikkelhegn
manager: adegeo
editor: 
ms.assetid: bd2c0511-36c9-4828-8dc3-69e4b6a70567
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/21/2017
ms.author: mikhegn
redirect_url: /azure/service-fabric/service-fabric-cluster-creation-via-arm
ms.translationtype: Human Translation
ms.sourcegitcommit: 0eefa64d6292dec14842c031ebad80fa9094436d
ms.openlocfilehash: 535e33eec22ed1c488cace9442328084b416b7a8
ms.contentlocale: es-es
ms.lasthandoff: 02/22/2017


---
# <a name="set-up-a-service-fabric-cluster-by-using-visual-studio"></a>Configuración de un clúster de Service Fabric mediante Visual Studio
Este artículo describe cómo configurar un clúster de Service Fabric con Visual Studio y una plantilla de Azure Resource Manager. Usaremos el proyecto Grupo de recursos de Azure de Visual Studio para crear la plantilla. Una vez creada la plantilla, puede implementarse directamente en Azure desde Visual Studio. También puede utilizarse desde un script o como parte de la instalación de integración continua (CI).

## <a name="create-a-service-fabric-cluster-template-by-using-an-azure-resource-group-project"></a>Creación de una plantilla de clúster de Service Fabric con un proyecto de grupo de recursos de Azure
Para empezar, abra Visual Studio y cree un proyecto de grupo de recursos de Azure (está disponible en la carpeta **Nube** ):

![Cuadro de diálogo Nuevo proyecto con el proyecto del Grupo de recursos de Azure seleccionado][1]

Puede crear una nueva solución de Visual Studio para este proyecto o agregarlo a una solución existente.

> [!NOTE]
> Si no ve el proyecto de grupo de recursos de Azure en el nodo Nube, no tiene instalado SDK de Azure. Inicie el Instalador de plataforma web ([instalar desde aquí](http://www.microsoft.com/web/downloads/platform.aspx) si no lo ha hecho todavía) y a continuación busque "SDK de Azure para .NET" e instale la versión que sea compatible con su versión de Visual Studio.
> 
> 

Una vez alcanzado el botón Aceptar, Visual Studio le pedirá que seleccione la plantilla del Administrador de recursos que quiere crear:

![Seleccione el cuadro de diálogo Plantilla de Azure con la plantilla del clúster de Service Fabric seleccionado][2]

Seleccione la plantilla de Clúster de Service Fabric y luego vuelva a presionar el botón Aceptar. Se creará el proyecto y la plantilla del Administrador de recursos.

## <a name="prepare-the-template-for-deployment"></a>Preparación de la plantilla para la implementación
Antes de implementar la plantilla para crear el clúster, debe proporcionar los valores para los parámetros necesarios de la plantilla. Estos valores de parámetro se leen desde el archivo `ServiceFabricCluster.parameters.json`, que se encuentra en la carpeta `Templates` del proyecto de grupo de recursos. Abra el archivo y proporcione los valores siguientes:

| Nombre de parámetro | Descripción |
| --- | --- |
| adminUserName |El nombre de la cuenta de administrador para máquinas de Service Fabric (nodos). |
| certificateThumbprint |La huella digital del certificado que protege al clúster. |
| sourceVaultResourceId |El *Identificador de recurso* del almacén de claves donde se guarda el certificado que protege al clúster. |
| certificateUrlValue |La dirección URL del certificado de seguridad del clúster. |

La plantilla del Administrador de recursos de Service Fabric de Visual Studio crea un clúster seguro que está protegido por un certificado. Este certificado se identifica mediante los tres últimos parámetros de plantilla (`certificateThumbprint`, `sourceVaultValue` y `certificateUrlValue`) y tiene que encontrarse en un **Almacén de claves de Azure**. Para obtener más información sobre cómo crear el certificado de seguridad del clúster, consulte el artículo [Escenarios de seguridad de los clústeres de Service Fabric](service-fabric-cluster-security.md#x509-certificates-and-service-fabric) .

## <a name="optional-change-the-cluster-name"></a>Opcional: cambio del nombre del clúster
Cada clúster de Service Fabric tiene un nombre. Cuando se crea un clúster de Fabric en Azure, el nombre determina (junto con la región de Azure) el nombre del Sistema de nombres de dominio (DNS) para el clúster. Por ejemplo, si le da a su clúster el nombre `myBigCluster` y la ubicación (región de Azure) del grupo de recursos que hospedará el nuevo clúster es Este de EE. UU., el nombre DNS del clúster será `myBigCluster.eastus.cloudapp.azure.com`.

De forma predeterminada, el nombre del clúster se genera automáticamente y se convierte en único cuando se le adjunta un sufijo aleatorio a un prefijo "clúster". De esta forma, resulta muy fácil usar la plantilla como parte de un sistema de **integración continua** (CI). Si quiere utilizar un nombre específico para el clúster, uno que sea significativo para usted, establezca el valor de la variable `clusterName` del archivo de plantilla de Resource Manager (`ServiceFabricCluster.json`) en el nombre elegido. Es la primera variable definida en ese archivo.

## <a name="optional-add-public-application-ports"></a>Opcional: agregar puertos de aplicación pública
Otro aspecto de la plantilla que quizá quiera cambiar antes de su implementación son los puertos de aplicación pública para el clúster. De forma predeterminada, la plantilla abre sólo dos puertos TCP públicos (80 y 8081); si necesita más para sus aplicaciones, tendrá que modificar la definición del equilibrador de carga de Azure en la plantilla. La definición se almacena en el archivo de plantilla principal (`ServiceFabricCluster.json`). Abra el archivo y busque `loadBalancedAppPort`. Cada puerto se asocia con tres artefactos:

1. Una variable de plantilla que define el valor del puerto TCP:
   
    ```json
    "loadBalancedAppPort1": "80"
    ```
2. Un *sondeo* que define con qué frecuencia y durante cuánto tiempo Azure Load Balancer intenta usar un nodo específico de Service Fabric antes de conmutar por error a otro. Los sondeos forman parte del recurso de equilibrador de carga. Esta es la definición de sondeo del primer puerto de aplicación predeterminado:
   
    ```json
    {
        "name": "AppPortProbe1",
        "properties": {
            "intervalInSeconds": 5,
            "numberOfProbes": 2,
            "port": "[variables('loadBalancedAppPort1')]",
            "protocol": "Tcp"
        }
    }
    ```
3. Una *regla de equilibrio de carga* que enlaza el puerto y el sondeo, que permite el equilibrio de carga entre un conjunto de nodos de clúster de Service Fabric:
   
    ```json
    {
        "name": "AppPortLBRule1",
        "properties": {
            "backendAddressPool": {
                "id": "[variables('lbPoolID0')]"
            },
            "backendPort": "[variables('loadBalancedAppPort1')]",
            "enableFloatingIP": false,
            "frontendIPConfiguration": {
                "id": "[variables('lbIPConfig0')]"
            },
            "frontendPort": "[variables('loadBalancedAppPort1')]",
            "idleTimeoutInMinutes": 5,
            "probe": {
                "id": "[concat(variables('lbID0'),'/probes/AppPortProbe1')]"
            },
            "protocol": "Tcp"
        }
    }
    ```
   Si las aplicaciones que va a implementar en el clúster necesitan más puertos, puede agregarlos mediante la creación de definiciones de reglas adicionales de sondeo y equilibrio de carga. Para obtener más información sobre cómo trabajar con Azure Load Balancer mediante plantillas de Resource Manager, consulte [Primeros pasos en la creación de un equilibrador de carga interno mediante una plantilla](../load-balancer/load-balancer-get-started-ilb-arm-template.md).

## <a name="deploy-the-template-by-using-visual-studio"></a>Implementación de la plantilla con Visual Studio.
Una vez que guarde todos los valores de parámetro necesarios en el archivo`ServiceFabricCluster.param.dev.json` , ya está listo para implementar la plantilla y crear el clúster de Service Fabric. Haga clic con el botón derecho en el proyecto de grupo de recursos del Explorador de soluciones de Visual Studio y elija **Implementar| Nueva implementación**. Si es necesario, Visual Studio mostrará el cuadro de diálogo **Implementar en grupo de recursos**, que le pedirá autenticarse en Azure:

![Cuadro de diálogo Implementar en grupo de recursos][3]

El cuadro de diálogo le permite elegir un grupo de recursos existente del Administrador de recursos para el clúster y le proporciona una opción para crear uno nuevo. Generalmente, tiene sentido usar un grupo de recursos independiente para un clúster de Service Fabric.

Una vez que haga clic en el botón Implementar, Visual Studio le pedirá que confirme los valores de parámetro de plantilla. Haga clic en el botón **Guardar** . Hay un parámetro que no tiene un valor almacenado, se trata de la contraseña de la cuenta administrativa para el clúster. Proporcione un valor de contraseña cuando Visual Studio se lo solicite.

> [!NOTE]
> A partir de Azure SDK 2.9, Visual Studio admite contraseñas de lectura de **Azure Key Vault** durante la implementación. En el cuadro de diálogo de parámetros de plantilla, observe que el cuadro de texto del parámetro `adminPassword` tiene un pequeño icono de "clave" a la derecha. Este icono permite seleccionar un secreto del almacén de claves existente como la contraseña administrativa para el clúster. Asegúrese de habilitar primero el acceso de Azure Resource Manager para implementación de plantillas en las Directivas de acceso avanzado de su almacén de claves. 
> 
> 

Puede supervisar el progreso del proceso de implementación en la ventana Resultados de Visual Studio. Una vez completada la implementación de plantilla, el nuevo clúster está listo para usarse.

> [!NOTE]
> Si nunca usó PowerShell para administrar Azure desde el equipo que está emplea ahora, es necesario realizar un pequeño mantenimiento.
> 
> 1. Habilite los scripts de PowerShell con el comando [`Set-ExecutionPolicy`](https://technet.microsoft.com/library/hh849812.aspx) . Para los equipos de desarrollo es normalmente aceptable una directiva "sin restricciones".
> 2. Decida si quiere permitir la recopilación de datos de diagnóstico de los comandos de Azure PowerShell y ejecute [`Enable-AzureRmDataCollection`](https://msdn.microsoft.com/library/mt619303.aspx) o [`Disable-AzureRmDataCollection`](https://msdn.microsoft.com/library/mt619236.aspx) según sea necesario. Esto evita solicitudes innecesarias durante la implementación de la plantilla.
> 
> 

Si hay errores, vaya al [Portal de Azure](https://portal.azure.com/) y abra el grupo de recursos en el que ha realizado la implementación. Haga clic en **Todas las configuraciones** y, luego, en la opción **Implementaciones** de la hoja de configuración. Una implementación de grupo de recursos con errores deja allí una información de diagnóstico detallada.

> [!NOTE]
> Los clústeres de Service Fabric requieren que un cierto número de nodos estén activos con el fin de mantener la disponibilidad y conservar el estado (esto se conoce como "mantenimiento del cuórum"). Por lo tanto, no es seguro apagar todas las máquinas del clúster a menos que antes haya realizado una [copia de seguridad completa del estado](service-fabric-reliable-services-backup-restore.md).
> 
> 

## <a name="next-steps"></a>Pasos siguientes
* [Obtener información acerca de la configuración de un clúster de Service Fabric en el Portal de Azure](service-fabric-cluster-creation-via-portal.md)
* [Obtener información sobre cómo administrar e implementar aplicaciones de Service Fabric con Visual Studio](service-fabric-manage-application-in-visual-studio.md)

<!--Image references-->
[1]: ./media/service-fabric-cluster-creation-via-visual-studio/azure-resource-group-project-creation.png
[2]: ./media/service-fabric-cluster-creation-via-visual-studio/selecting-azure-template.png
[3]: ./media/service-fabric-cluster-creation-via-visual-studio/deploy-to-azure.png

