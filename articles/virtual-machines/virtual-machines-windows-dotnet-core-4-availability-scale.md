---
title: Disponibilidad y escala en plantillas de Azure Resource Manager | Microsoft Docs
description: "Tutorial de DotNet Core para máquinas virtuales de Azure"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 494724b5-06af-4dea-a774-ba580cf27527
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 11/21/2016
ms.author: nepeters
translationtype: Human Translation
ms.sourcegitcommit: 9f944edcafb55634c9338bc8dd518a5f032f56e0
ms.openlocfilehash: e714a7e941b25dfb0926934a3dff387363f306d8


---
# <a name="availability-and-scale-in-azure-resource-manager-templates"></a>Disponibilidad y escala en plantillas de Azure Resource Manager
Los términos disponibilidad y escala hacen referencia al tiempo de actividad y la capacidad para satisfacer la demanda. Si una aplicación debe estar disponible el 99,9 % del tiempo, debe tener una arquitectura que permita varios recursos de proceso simultáneos. Por ejemplo, en lugar de tener un único sitio web, una configuración con un mayor nivel de disponibilidad incluye varias instancias del mismo sitio con una tecnología de equilibrio frente ellas. En esta configuración, se puede desactivar una instancia de la aplicación para su mantenimiento mientras las demás siguen funcionando correctamente. Por otro lado, la escala hace referencia a la capacidad de una aplicación para atender la demanda. Con una aplicación con equilibrio de carga, agregar o quitar instancias del grupo permite a una aplicación ajustar la escala para satisfacer la demanda.

En este documento se explica cómo configurar la disponibilidad y la escala de la implementación de ejemplo Music Store. Se resaltan todas las dependencias y configuraciones únicas. Para obtener la mejor experiencia, realice una implementación previa de una instancia de la solución en su suscripción de Azure y trabaje con la plantilla de Azure Resource Manager. La plantilla completa se puede encontrar aquí: [Music Store Deployment on Windows](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-windows)(Implementación de Music Store en Windows).

## <a name="availability-set"></a>Conjunto de disponibilidad
Un conjunto de disponibilidad abarca de forma lógica máquinas virtuales de Azure entre hosts físicos y otros componentes de infraestructura tales como fuentes de alimentación y hardware de red físico. Los conjuntos de disponibilidad garantizan que no todas las máquinas virtuales se vean afectadas durante las tareas de mantenimiento, los errores de dispositivo u otros tiempos de inactividad. Se puede agregar un conjunto de disponibilidad a una plantilla de Azure Resource Manager mediante el asistente Agregar nuevo recurso de Visual Studio o insertando un recurso JSON válido en una plantilla.

Siga este vínculo para ver el ejemplo de JSON en la plantilla de Resource Manager: [Availability Set](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-windows/azuredeploy.json#L368)(Conjunto de disponibilidad).

```json
{
  "apiVersion": "2015-06-15",
  "type": "Microsoft.Compute/availabilitySets",
  "name": "[variables('availabilitySetName')]",
  "location": "[resourceGroup().location]",
  "tags": {
    "displayName": "availability-set"
  },
  "properties": {
  }
}
```

Un conjunto de disponibilidad se declara como una propiedad de un recurso de máquina virtual. 

Siga este vínculo para ver el ejemplo de JSON en la plantilla de Resource Manager: [Availability Set association with Virtual Machine](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-windows/azuredeploy.json#L302)(Asociación de un conjunto de disponibilidad con una máquina virtual).

```json
"properties": {
  "availabilitySet": {
    "id": "[resourceId('Microsoft.Compute/availabilitySets', variables('availabilitySetName'))]"
  }
```
Conjunto de disponibilidad tal y como se muestra en Azure Portal. Aquí se detallan cada máquina virtual y los detalles acerca de la configuración.

![Conjunto de disponibilidad](./media/virtual-machines-windows-dotnet-core/ase-win.png)

Para más información detallada sobre los conjuntos de disponibilidad, consulte [Administración de la disponibilidad de las máquinas virtuales](virtual-machines-windows-manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

## <a name="network-load-balancer"></a>Equilibrador de carga de red
Mientras que un conjunto de disponibilidad proporciona tolerancia a errores de aplicación, un equilibrador de carga hace que haya muchas instancias de la aplicación disponibles en una única dirección de red. Se pueden hospedar varias instancias de una aplicación en muchas máquinas virtuales, cada una conectada a un equilibrador de carga. A medida que se obtiene acceso a la aplicación, el equilibrador de carga enruta la solicitud entrante entre todos los miembros asociados. Se puede agregar un equilibrador de carga mediante el asistente Agregar nuevo recurso de Visual Studio, o insertando un recurso JSON válido en la plantilla de Azure Resource Manager.

Siga este vínculo para ver el ejemplo de JSON en la plantilla de Resource Manager: [Network Load Balancer](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-windows/azuredeploy.json#L198)(Equilibrador de carga de red).

```json
{
  "apiVersion": "2015-06-15",
  "type": "Microsoft.Network/loadBalancers",
  "name": "[variables('loadBalancerName')]",
  "location": "[resourceGroup().location]",
  "tags": {
    "displayName": "load-balancer"
  },
  ........<truncated>
}
```

Como la aplicación de ejemplo se expone a Internet con una dirección IP pública, esta dirección está asociada con el equilibrador de carga. 

Siga este vínculo para ver el ejemplo de JSON en la plantilla de Resource Manager: [Asociación de un equilibrador de carga de red con una dirección IP pública](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-windows/azuredeploy.json#L211).

```json
"frontendIPConfigurations": [
  {
    "properties": {
      "publicIPAddress": {
        "id": "[resourceId('Microsoft.Network/publicIPAddresses', variables('publicIpAddressName'))]"
      }
    },
    "name": "LoadBalancerFrontend"
  }
]
```

En Azure Portal, la información general del equilibrador de carga de red muestra la asociación con la dirección IP pública.

![Equilibrador de carga de red](./media/virtual-machines-windows-dotnet-core/nlb-win.png)

## <a name="load-balancer-rule"></a>Regla de equilibrador de carga
Cuando se utiliza un equilibrador de carga, se configuran reglas que controlan cómo se equilibra el tráfico entre los recursos deseados. Con la aplicación Music Store, el tráfico llega al puerto 80 de la dirección IP pública y se distribuye al puerto 80 de todas las máquinas virtuales. 

Siga este vínculo para ver el ejemplo de JSON en la plantilla de Resource Manager: [Load Balancer Rule](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-windows/azuredeploy.json#L226)(Regla de equilibrador de carga).

```json
"loadBalancingRules": [
  {
    "name": "[variables('loadBalencerRule')]",
    "properties": {
      "frontendIPConfiguration": {
        "id": "[concat(resourceId('Microsoft.Network/loadBalancers', variables('loadBalancerName')), '/frontendIPConfigurations/LoadBalancerFrontend')]"
      },
      "backendAddressPool": {
        "id": "[variables('lbPoolID')]"
      },
      "protocol": "Tcp",
      "frontendPort": 80,
      "backendPort": 80,
      "enableFloatingIP": false,
      "idleTimeoutInMinutes": 5,
      "probe": {
        "id": "[variables('lbProbeID')]"
      }
    }
  }
]
```

Vista de la regla de equilibrador de carga de red desde el portal.

![Regla de equilibrador de carga de red](./media/virtual-machines-windows-dotnet-core/lbrule-win.png)

## <a name="load-balancer-probe"></a>Sondeo de equilibrador de carga
El equilibrador de carga también necesita supervisar cada máquina virtual para que las solicitudes se atiendan solamente en sistemas en ejecución. Esta supervisión se realiza mediante un sondeo constante en un puerto definido previamente. La implementación de Music Store está configurada para sondear el puerto 80 en todas las máquinas incluidas. 

Siga este vínculo para ver el ejemplo de JSON en la plantilla de Resource Manager: [Load Balancer Probe](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-windows/azuredeploy.json#L247)(Sondeo de equilibrador de carga).

```json
"probes": [
  {
    "properties": {
      "protocol": "Tcp",
      "port": 80,
      "intervalInSeconds": 15,
      "numberOfProbes": 2
    },
    "name": "lbprobe"
  }
]
```

Sondeo de equilibrador de carga visto desde Azure Portal.

![Sondeo de equilibrador de carga de red](./media/virtual-machines-windows-dotnet-core/lbprobe-win.png)

## <a name="inbound-nat-rules"></a>Reglas NAT de entrada
Cuando se usa un equilibrador de carga, se necesita poner en marcha reglas que proporcionen acceso sin equilibrio de carga a cada máquina virtual. Por ejemplo, al crear una conexión RDP con cada máquina virtual, este tráfico no debe tener equilibrio de carga; en su lugar, se debe configurar una ruta de acceso predeterminada. Las rutas de acceso predeterminadas se configuran mediante un recurso de regla NAT de entrada. Con este recurso, la comunicación entrante puede asignarse a cada máquina virtual. 

Con la aplicación Music Store, se asigna un puerto, a partir del 5000, al puerto 3389 de cada máquina virtual para el acceso RDP. La función `copyindex()` se usa para incrementar el puerto de entrada, de manera que la segunda máquina virtual recibe el puerto de entrada 5001, el tercero 5002 y así sucesivamente.

Siga este vínculo para ver el ejemplo de JSON en la plantilla de Resource Manager: [Inbound NAT Rules](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-windows/azuredeploy.json#L260)(Reglas NAT de entrada). 

```json
{
  "apiVersion": "2015-06-15",
  "type": "Microsoft.Network/loadBalancers/inboundNatRules",
  "name": "[concat(variables('loadBalancerName'), '/', 'RDP-VM', copyIndex())]",
  "location": "[resourceGroup().location]",
  "tags": {
    "displayName": "load-balancer-nat-rule"
  },
  "copy": {
    "name": "lbNatLoop",
    "count": "[parameters('numberOfInstances')]"
  },
  "dependsOn": [
    "[concat('Microsoft.Network/loadBalancers/', variables('loadBalancerName'))]"
  ],
  "properties": {
    "frontendIPConfiguration": {
      "id": "[variables('ipConfigID')]"
    },
    "protocol": "tcp",
    "frontendPort": "[copyIndex(5000)]",
    "backendPort": 3389,
    "enableFloatingIP": false
  }
}
```

Ejemplo de una regla NAT de entrada vista en Azure Portal. Se crea una regla NAT RDP para cada máquina virtual de la implementación.

![Regla NAT de entrada](./media/virtual-machines-windows-dotnet-core/natrule-win.png)

Para obtener información detallada sobre el equilibrador de carga de red de Azure, consulte [Equilibrio de carga para servicios de infraestructura de Azure](virtual-machines-windows-load-balance.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="deploy-multiple-vms"></a>Implementación de varias máquinas virtuales
Por último, para que un conjunto de disponibilidad o un equilibrador de carga funcionen eficazmente, se necesitan varias máquinas virtuales. Se pueden implementar varias máquinas virtuales con la función de copia de la plantilla de Azure Resource Manager. Con la función de copia, no es necesario definir un número finito de máquinas virtuales; en su lugar, este valor se puede proporcionar dinámicamente en el momento de la implementación. La función de copia crea el número de instancias necesario y controla la implementación del número apropiado de máquinas virtuales y recursos asociados.

En la plantilla de ejemplo de Music Store, se define un parámetro que toma un recuento de instancias. Este número se usa en toda la plantilla durante la creación de las máquinas virtuales y los recursos relacionados.

```json
"numberOfInstances": {
  "type": "int",
  "minValue": 1,
  "defaultValue": 2,
  "metadata": {
    "description": "Number of VM instances to be created behind load balancer."
  }
},
```

En el recurso de máquina virtual, al bucle de copia se le asigna un nombre y el parámetro de número de instancias que se usa para controlar el número de copias resultantes.

Siga este vínculo para ver el ejemplo de JSON en la plantilla de Resource Manager: [Virtual Machine Copy Function](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-windows/azuredeploy.json#L290)(Función de copia de máquina virtual). 

```json
{
  "apiVersion": "2015-06-15",
  "type": "Microsoft.Compute/virtualMachines",
  "name": "[concat(variables('vmName'),copyindex())]",
  "location": "[resourceGroup().location]",
  "copy": {
    "name": "virtualMachineLoop",
    "count": "[parameters('numberOfInstances')]"
  }
```

Se puede acceder a la iteración actual de la función de copia con la función `copyIndex()` . Se puede usar el valor de la función de índice de copia para asignar un nombre a las máquinas virtuales y a otros recursos. Por ejemplo, si se implementan dos instancias de una máquina virtual, necesitan nombres diferentes. La función `copyIndex()` puede usarse como parte del nombre de la máquina virtual para crear un nombre único. En el recurso de máquina virtual se puede ver un ejemplo de la función `copyindex()` usada para la asignación de nombres. En este caso, el nombre del equipo es una concatenación del parámetro `vmName` y la función `copyIndex()`. 

Siga este vínculo para ver el ejemplo de JSON en la plantilla de Resource Manager: [Copy Index Function](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-windows/azuredeploy.json#L309)(Función de índice de copia). 

```json
"osProfile": {
  "computerName": "[concat(variables('vmName'),copyindex())]",
  "adminUsername": "[parameters('adminUsername')]",
  "adminPassword": "[parameters('adminPassword')]"
}
```

La función `copyIndex` se usa varias veces en la plantilla de ejemplo de Music Store. Los recursos y las funciones que usan `copyIndex` son todos los específicos de una única instancia de la máquina virtual, por ejemplo, la interfaz de red, las reglas de equilibrador de carga y cualquier dependencia de las funciones. 

Para más información sobre la función de copia, consulte [Creación de varias instancias de recursos en Azure Resource Manager](../azure-resource-manager/resource-group-create-multiple.md).

## <a name="next-step"></a>Paso siguiente
<hr>

[Paso 4: implementación de aplicaciones con plantillas de Azure Resource Manager](virtual-machines-windows-dotnet-core-5-app-deployment.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)




<!--HONumber=Feb17_HO3-->


