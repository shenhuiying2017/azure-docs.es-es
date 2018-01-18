---
title: "Integración de Azure DevTest Labs en la canalización de entrega e integración continuas de VSTS | Microsoft Docs"
description: "Aprenda a integrar Azure DevTest Labs en la canalización de entrega e integración continuas de VSTS."
services: devtest-lab,virtual-machines,visual-studio-online
documentationcenter: na
author: craigcaseyMSFT
manager: douge
editor: 
ms.assetid: a26df85e-2a00-462b-aac1-dd3539532569
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/07/2017
ms.author: v-craic
ms.openlocfilehash: 6c6bd4fbd89ec87cbbdbfb9ed42f86a484acf7ad
ms.sourcegitcommit: 48fce90a4ec357d2fb89183141610789003993d2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/12/2018
---
# <a name="integrate-azure-devtest-labs-into-your-vsts-continuous-integration-and-delivery-pipeline"></a>Integración de Azure DevTest Labs en la canalización de entrega e integración continuas de VSTS
Puede usar la extensión *Azure DevTest Labs Tasks*, que se instala en Visual Studio Team Services (VSTS), para integrar fácilmente la compilación de CI/CD y la canalización de versión con Azure DevTest Labs. La extensión instala tres tareas: 
* Crear una VM
* Crear una imagen personalizada a partir de una máquina virtual
* Eliminación de una máquina virtual 

El proceso facilita, por ejemplo, la implementación rápida de una "imagen maestra" para una tarea de prueba específica y, luego, la elimina cuando la prueba finaliza.

En este artículo se muestra cómo crear e implementar una máquina virtual, crear una imagen personalizada y, a continuación, eliminar la máquina virtual, todo ello como una canalización completa. Lo habitual sería realizar cada tarea de forma individual en su propia canalización de compilación, prueba e implementación.

## <a name="before-you-begin"></a>Antes de empezar
Antes de integrar la canalización CI/CD con Azure DevTest Labs, debe instalar la extensión desde Visual Studio Marketplace.
1. Vaya a [Azure DevTest Labs Tasks](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks).
1. Seleccione **Instalar**.
1. Realice los pasos del asistente.

## <a name="create-a-resource-manager-template"></a>Creación de una plantilla de Resource Manager
En esta sección se describe cómo crear la plantilla de Azure Resource Manager que se usa para crear una máquina virtual de Azure a petición.

1. Para crear una plantilla de Resource Manager en su suscripción, realice el procedimiento que se describe en [Uso de una plantilla de Resource Manager](devtest-lab-use-resource-manager-template.md).
1. Antes de generar la plantilla de Resource Manager, agregue el [artefacto WinRM](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts/windows-winrm) como parte de la creación de la máquina virtual.

   Para usar las tareas de implementación, como *Azure File Copy* y *PowerShell en las máquinas de destino*, es necesario el acceso a WinRM.

   > [!NOTE]
   > Cuando se usa WinRM con una dirección IP compartida, debe agregar una regla NAT para asignar un puerto externo al puerto de WinRM. Este paso no es necesario si ha creado la máquina virtual con una dirección IP pública.
   >
   >

1. Guarde la plantilla como un archivo en el equipo. Asigne al archivo el nombre **CreateVMTemplate.json**.
1. Compruebe la plantilla en el sistema de control de código fuente.
1. Abra un editor de texto y pegue el código siguiente.

   ```powershell
   Param( [string] $labVmId)

   $labVmComputeId = (Get-AzureRmResource -Id $labVmId).Properties.ComputeId

   # Get lab VM resource group name
   $labVmRgName = (Get-AzureRmResource -Id $labVmComputeId).ResourceGroupName

   # Get the lab VM Name
   $labVmName = (Get-AzureRmResource -Id $labVmId).Name

   # Get lab VM public IP address
   $labVMIpAddress = (Get-AzureRmPublicIpAddress -ResourceGroupName $labVmRgName
                   -Name $labVmName).IpAddress

   # Get lab VM FQDN
   $labVMFqdn = (Get-AzureRmPublicIpAddress -ResourceGroupName $labVmRgName
              -Name $labVmName).DnsSettings.Fqdn

   # Set a variable labVmRgName to store the lab VM resource group name
   Write-Host "##vso[task.setvariable variable=labVmRgName;]$labVmRgName"

   # Set a variable labVMIpAddress to store the lab VM Ip address
   Write-Host "##vso[task.setvariable variable=labVMIpAddress;]$labVMIpAddress"

   # Set a variable labVMFqdn to store the lab VM FQDN name
   Write-Host "##vso[task.setvariable variable=labVMFqdn;]$labVMFqdn"
   ```

1. Compruebe el script en el sistema de control de código fuente. Asígnele el nombre **GetLabVMParams.ps1**.

   Al ejecutar este script en el agente como parte de la definición de versión y si usa pasos de tareas, como *Azure File Copy* o *PowerShell en las máquinas de destino*, el script recopila los valores necesarios para implementar la aplicación en la máquina virtual. Estas tareas se usarían normalmente para implementar aplicaciones en una máquina virtual de Azure. Las tareas requieren valores como el nombre del grupo de recursos de máquina virtual, la dirección IP y el nombre de dominio completo (FQDN).

## <a name="create-a-release-definition-in-release-management"></a>Creación de una definición de versión en Release Management
Para crear la definición de la versión, siga estos pasos:

1. En la pestaña **Versiones** del concentrador **Compilación y versión**, seleccione el botón con el signo de la suma (+).
2. En la ventana **Crear definición de versión**, seleccione la plantilla **Vacía** y, luego, seleccione **Siguiente**.
3. Seleccione **Elegir más tarde** y, luego, seleccione **Crear** para crear una nueva definición de versión con un entorno predeterminado y ningún artefacto vinculado.
4. Para abrir el menú contextual, en la nueva definición de versión, seleccione los puntos suspensivos (...) junto al nombre del entorno y, luego, seleccione **Configurar variables**. 
5. En la ventana de **configuración del entorno**, escriba los siguientes valores para las variables que se usan en las tareas de definición de versión:

   a. En **vmName**, escriba el nombre que asignó a la máquina virtual cuando creó la plantilla de Resource Manager en Azure Portal.

   b. En **userName**, escriba el nombre de usuario que asignó a la máquina virtual cuando creó la plantilla de Resource Manager en Azure Portal.

   c. En **password**, escriba la contraseña que asignó a la máquina virtual cuando creó la plantilla de Resource Manager en Azure Portal. Use el icono de "candado" para ocultar y proteger la contraseña.

### <a name="create-a-vm"></a>Crear una VM

La siguiente fase de la implementación consiste en crear la máquina virtual que se usará como la "imagen maestra" en las sucesivas implementaciones. Creará la máquina virtual en su instancia de Azure DevTest Lab mediante la tarea desarrollada especialmente para este fin. 

1. En la definición de versión, seleccione **Agregar tareas**.
2. En la pestaña **Implementar**, agregue una tarea de *creación de una máquina virtual de Azure DevTest Labs*. Configure la tarea de la siguiente manera:

   > [!NOTE]
   > Para crear la máquina virtual que se usará en las implementaciones posteriores, consulte [Azure DevTest Labs Tasks](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks).

   a. En **Suscripción de Azure Resource Manager**, seleccione una conexión en la lista **Conexiones a servicios de Azure disponibles**, o cree una conexión con permisos más restrictivos a la suscripción de Azure. Para más información, consulte [Azure Resource Manager service endpoint](https://docs.microsoft.com/vsts/build-release/concepts/library/service-endpoints#sep-azure-rm) (Punto de conexión de servicio de Azure Resource Manager).

   b. En **Nombre de laboratorio**, seleccione el nombre de la instancia que creó anteriormente.

   c. En **Nombre de plantilla**, escriba la ruta de acceso completa y el nombre del archivo de plantilla que guardó en el repositorio de código fuente. Puede usar las propiedades integradas de Release Management para simplificar la ruta de acceso, por ejemplo:

   ```
   $(System.DefaultWorkingDirectory)/Contoso/ARMTemplates/CreateVMTemplate.json
   ```

   d. En **Parámetros de plantilla**, especifique los parámetros de las variables que se definen en la plantilla. Use los nombres de las variables que definió en el entorno, por ejemplo:

   ```
   -newVMName '$(vmName)' -userName '$(userName)' -password (ConvertTo-SecureString -String '$(password)' -AsPlainText -Force)
   ```

   e. En **Output Variables - Lab VM ID** (Variables de salida: id. de máquina virtual de laboratorio), necesitará el identificador de la máquina virtual recién creada en los pasos posteriores. El nombre predeterminado de la variable de entorno que se rellena automáticamente con este identificador se establecerá en la sección **Variables de salida**. Puede modificar la variable si es necesario, pero recuerde que debe usar el nombre correcto en las posteriores tareas. El identificador de la máquina virtual de laboratorio se escribe en el formato siguiente:

   ```
   /subscriptions/{subId}/resourceGroups/{rgName}/providers/Microsoft.DevTestLab/labs/{labName}/virtualMachines/{vmName}
   ```

3. Ejecute el script que creó anteriormente para recopilar los detalles de la máquina virtual de DevTest Labs. 
4. En la definición de versión, seleccione **Agregar tareas** y, luego, en la pestaña **Implementar**, agregue una tarea de *Azure PowerShell*. Configure la tarea de la siguiente manera:

   > [!NOTE]
   > Para recopilar los detalles de la máquina virtual de DevTest Labs, consulte [Deploy: Azure PowerShell](https://github.com/Microsoft/vsts-tasks/tree/master/Tasks/AzurePowerShell) (Implementación: Azure PowerShell) y ejecute el script.

   a. En **Tipo de conexión de Azure**, seleccione **Azure Resource Manager**.

   b. En **Suscripción de Azure Resource Manager**, seleccione una conexión en la lista **Conexiones a servicios de Azure disponibles**, o cree una conexión con permisos más restrictivos a la suscripción de Azure. Para más información, consulte [Azure Resource Manager service endpoint](https://docs.microsoft.com/vsts/build-release/concepts/library/service-endpoints#sep-azure-rm) (Punto de conexión de servicio de Azure Resource Manager).

   c. En **Tipo de script**, seleccione **Archivo de script**.
 
   d. En **Ruta de acceso del script**, escriba la ruta de acceso completa y el nombre del script que guardó en el repositorio de código fuente. Puede usar las propiedades integradas de Release Management para simplificar la ruta de acceso, por ejemplo:
      ```
      $(System.DefaultWorkingDirectory/Contoso/Scripts/GetLabVMParams.ps1
      ```
   e. En **Argumentos de script**, escriba el nombre de la variable de entorno que se rellenó automáticamente con el identificador de la máquina virtual de laboratorio en la tarea anterior, por ejemplo: 
      ```
      -labVmId '$(labVMId)'
      ```
    El script recopila los valores necesarios y los almacena en variables de entorno dentro de la definición de versión, para que así pueda consultarlos fácilmente en pasos posteriores.

5. Implemente la aplicación en la nueva máquina de DevTest Labs. Las tareas que se usan habitualmente para implementar la aplicación son *Azure File Copy* y *PowerShell en las máquinas de destino*.
   La información acerca de la máquina virtual que se necesita para los parámetros de estas tareas se almacena en tres variables de configuración denominadas **labVmRgName**, **labVMIpAddress**, y **labVMFqdn** dentro de la definición de versión. Si solo desea experimentar con la creación de una máquina virtual de DevTest Labs y una imagen personalizada, sin implementar una aplicación en ella, puede omitir este paso.

### <a name="create-an-image"></a>Crear una imagen

La siguiente fase consiste en crear una imagen de la máquina virtual recién implementada en la instancia de Azure DevTest Labs. Luego, puede usar la imagen para crear copias de la máquina virtual a petición siempre que quiera ejecutar una tarea de desarrollo o ejecutar algunas pruebas. 

1. En la definición de versión, seleccione **Agregar tareas**.
2. En la pestaña **Implementar**, agregue una tarea de **creación de imagen personalizada de Azure DevTest Labs**. Configúrelo de la siguiente forma:

   > [!NOTE]
   > Para crear la imagen, consulte [Azure DevTest Labs Tasks](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks).

   a. En **Suscripción de Azure Resource Manager**, seleccione una conexión en la lista **Conexiones a servicios de Azure disponibles**, o cree una conexión con permisos más restrictivos a la suscripción de Azure. Para más información, consulte [Azure Resource Manager service endpoint](https://docs.microsoft.com/vsts/build-release/concepts/library/service-endpoints#sep-azure-rm) (Punto de conexión de servicio de Azure Resource Manager).

   b. En **Nombre de laboratorio**, seleccione el nombre de la instancia que creó anteriormente.

   c. En **Custom Image Name** (Nombre de imagen personalizada), escriba un nombre para la imagen personalizada.

   d. Opcionalmente, en **Descripción**, escriba una descripción para facilitar la selección de la imagen correcta más adelante.

   e. En **Source Lab VM - Source Lab VM ID** (Máquina virtual de laboratorio de origen: id. de máquina virtual de laboratorio de origen), si cambió el nombre predeterminado de la variable de entorno que se rellenó automáticamente con el identificador de la máquina virtual de laboratorio en una tarea anterior, edítelo aquí. El valor predeterminado es **$(labVMId)**.

   f. En **Output Variables - Custom Image ID** (Variables de salida: id. de imagen personalizada), necesitará el identificador de la imagen recién creada si quiere administrarla o eliminarla. El nombre predeterminado de la variable de entorno que se rellena automáticamente con este identificador se establece en la sección **Variables de salida**. Puede modificar la variable si es necesario.

### <a name="delete-the-vm"></a>Eliminación de la máquina virtual

La fase final consiste en eliminar la máquina virtual que se implementó en la instancia de Azure DevTest Labs. Lo habitual es eliminar la máquina virtual después de ejecutar las tareas de desarrollo o de ejecutar las pruebas que necesita en la máquina virtual implementada. 

1. En la definición de versión, seleccione **Agregar tareas** y, luego, en la pestaña **Implementar**, agregue una tarea de *eliminación de una máquina virtual de Azure DevTest Labs*. Configúrelo de la siguiente forma:

      > [!NOTE]
      > Para eliminar la máquina virtual, consulte [Azure DevTest Labs Tasks](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks).

   a. En **Suscripción de Azure Resource Manager**, seleccione una conexión en la lista **Conexiones a servicios de Azure disponibles**, o cree una conexión con permisos más restrictivos a la suscripción de Azure. Para más información, consulte [Azure Resource Manager service endpoint](https://docs.microsoft.com/vsts/build-release/concepts/library/service-endpoints#sep-azure-rm) (Punto de conexión de servicio de Azure Resource Manager).
 
   b. En **Lab VM ID** (Id. de máquina virtual de laboratorio), si cambió el nombre predeterminado de la variable de entorno que se rellenó automáticamente con el identificador de la máquina virtual de laboratorio en una tarea anterior, edítelo aquí. El valor predeterminado es **$(labVMId)**.

2. Escriba un nombre para la definición de versión y, a continuación, guárdelo.
3. Cree una nueva versión, seleccione la última compilación e impleméntela en el entorno único de la definición.

En cada una de las fases, actualice la vista de la instancia de DevTest Labs en Azure Portal para ver de nuevo la máquina virtual y la imagen que se crean, y la máquina virtual que se elimina.

Ahora puede usar la imagen personalizada para crear máquinas virtuales cuando sea necesario.


[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>pasos siguientes
* Aprenda a [crear entornos de varias máquinas virtuales con plantillas de Resource Manager](devtest-lab-create-environment-from-arm.md).
* Explore más plantillas de inicio rápido de Resource Manager para la automatización de DevTest Labs desde el [repositorio público de GitHub de DevTest Labs](https://github.com/Azure/azure-quickstart-templates).
* Si es necesario, consulte la página de [solución de problemas de VSTS](https://docs.microsoft.com/vsts/build-release/actions/troubleshooting).
 
