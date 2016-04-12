<properties
   pageTitle="Solución de problemas de las extensiones de máquina virtual de Linux | Microsoft Azure"
   description="Información sobre la solución de problemas de la extensión de máquina virtual de Azure"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="kundanap"
   manager="timlt"
   editor=""
   tags="top-support-issue,azure-resource-manager"/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="09/01/2015"
   ms.author="kundanap"/>

# Solución de problemas de la extensión de máquina virtual de Linux Azure.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]modelo de implementación clásica.

[AZURE.INCLUDE [virtual-machines-common-extensions-troubleshoot](../../includes/virtual-machines-common-extensions-troubleshoot.md)]

## Consulta del estado de la extensión
Las plantillas del Administrador de recursos de Azure se pueden ejecutar desde la CLI de Azure. Cuando se ejecute la plantilla, el estado de extensión se puede ver desde el Explorador de recursos de Azure o las herramientas de la línea de comandos.

Aquí tiene un ejemplo:

CLI de Azure:

      azure vm get-instance-view


Este es la salida de ejemplo:

      Extensions:  {
      "ExtensionType": "Microsoft.Compute.CustomScriptExtension",
      "Name": "myCustomScriptExtension",
      "SubStatuses": [
        {
          "Code": "ComponentStatus/StdOut/succeeded",
          "DisplayStatus": "Provisioning succeeded",
          "Level": "Info",
          "Message": "    Directory: C:\\temp\\n\\n\\nMode                LastWriteTime     Length Name
              \\n----                -------------     ------ ----                              \\n-a---          9/1/2015   2:03 AM         11
              test.txt                          \\n\\n",
                      "Time": null
          },
        {
          "Code": "ComponentStatus/StdErr/succeeded",
          "DisplayStatus": "Provisioning succeeded",
          "Level": "Info",
          "Message": "",
          "Time": null
        }
    }
  ]

## Solucionar problemas de errores de extensión:

### Volver a ejecutar la extensión en la máquina virtual

Si está ejecutando los scripts en la máquina virtual mediante la extensión de script personalizada, a veces podría recibir un error en el que la máquina virtual se creó correctamente, pero en el que el script ha generado un error. En estas condiciones, la manera que se recomienda para recuperarse de este error es quitar la extensión y volver a ejecutar la plantilla. Nota: en el futuro, esta funcionalidad se mejoraría para eliminar la necesidad de desinstalar la extensión.

#### Quitar la extensión de la CLI de Azure

      azure vm extension set --resource-group "KPRG1" --vm-name "kundanapdemo" --publisher-name "Microsoft.Compute.CustomScriptExtension" --name "myCustomScriptExtension" --version 1.4 --uninstall

Donde "publisher-name" se corresponde con el tipo de extensión de la salida de "azure vm get-instance-view" y el nombre es el nombre del recurso de extensión de la plantilla.

Cuando se ha quitado la extensión, la plantilla puede volver a ejecutarse para ejecutar los scripts en la máquina virtual.

<!---HONumber=AcomDC_0323_2016-->