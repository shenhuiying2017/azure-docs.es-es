---
title: Paso de valores complejos entre plantillas de Azure | Microsoft Docs
description: Muestra los enfoques recomendados para usar objetos complejos a fin de compartir datos de estado con plantillas vinculadas y plantillas de Azure Resource Manager.
services: azure-resource-manager
documentationcenter: 
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: fd2f5e2d-d56d-4e01-a57d-34f3eaead4a9
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/26/2016
ms.author: tomfitz
ms.openlocfilehash: e163f3c2e9a78b057dc2a7a42924c59d0aac3fab
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="share-state-to-and-from-azure-resource-manager-templates"></a>Uso compartido del estado en y desde las plantillas de Azure Resource Manager
En este tema se muestran los procedimientos recomendados para administrar y compartir el estado en las plantillas. Los parámetros y variables que se muestran en este tema son ejemplos del tipo de objetos que puede definir para organizar con facilidad sus requisitos de implementación. Desde estos ejemplos, puede implementar sus propios objetos con valores de propiedad que tengan sentido para su entorno.

Este tema forma parte de un artículo más extenso. Para leer el documento completo, descargue [World Class Resource Manager Templates Considerations and Proven Practices](http://download.microsoft.com/download/8/E/1/8E1DBEFA-CECE-4DC9-A813-93520A5D7CFE/World Class ARM Templates - Considerations and Proven Practices.pdf) (Consideraciones sobre las plantillas de Resource Manager de talla mundial y prácticas comprobadas).

## <a name="provide-standard-configuration-settings"></a>Proporcionar opciones de configuración estándar
En lugar de ofrecer una plantilla que proporciona total flexibilidad e infinidad de variaciones, un patrón común es proporcionar una selección de configuraciones conocidas. De hecho, los usuarios pueden seleccionar tamaños estándar de camiseta como espacio aislado, pequeño, mediano y grande. Otros ejemplos de tamaños de camiseta son ofertas de productos, como edición para la comunidad o edición para impresa. En otros casos, pueden ser configuraciones de una tecnología específicas de la carga de trabajo, como MapReduce o NoSQL.

Con objetos complejos, puede crear las variables que contengan colecciones de datos, a veces conocidas como "contenedores de propiedades" y usar esos datos para dirigir la declaración de recursos de tu plantilla. Este enfoque proporciona configuraciones bien conocidas de tamaños variables que están preconfiguradas para los clientes. Sin configuraciones conocidas, los usuarios de las plantillas deben determinar el tamaño del clúster por su cuenta, tener en cuenta las restricciones de recursos de plataforma y realizar cálculos matemáticos para identificar el particionamiento resultante de las cuentas de almacenamiento y otros recursos (debido a restricciones en el tamaño del clúster y los recursos). Además de crear una experiencia mejor para el cliente, unas cuantas configuraciones conocidas son más fáciles de mantener y puede ayudarle a ofrecer un nivel mayor de densidad.

En el ejemplo siguiente se muestra cómo definir variables que contienen objetos complejos para representar colecciones de datos. Las colecciones definen valores que se usan para el tamaño de la máquina virtual, la configuración de red, la configuración del sistema operativo y la configuración de disponibilidad.

```json
"variables": {
  "tshirtSize": "[variables(concat('tshirtSize', parameters('tshirtSize')))]",
  "tshirtSizeSmall": {
    "vmSize": "Standard_A1",
    "diskSize": 1023,
    "vmTemplate": "[concat(variables('templateBaseUrl'), 'database-2disk-resources.json')]",
    "vmCount": 2,
    "storage": {
      "name": "[parameters('storageAccountNamePrefix')]",
      "count": 1,
      "pool": "db",
      "map": [0,0],
      "jumpbox": 0
    }
  },
  "tshirtSizeMedium": {
    "vmSize": "Standard_A3",
    "diskSize": 1023,
    "vmTemplate": "[concat(variables('templateBaseUrl'), 'database-8disk-resources.json')]",
    "vmCount": 2,
    "storage": {
      "name": "[parameters('storageAccountNamePrefix')]",
      "count": 2,
      "pool": "db",
      "map": [0,1],
      "jumpbox": 0
    }
  },
  "tshirtSizeLarge": {
    "vmSize": "Standard_A4",
    "diskSize": 1023,
    "vmTemplate": "[concat(variables('templateBaseUrl'), 'database-16disk-resources.json')]",
    "vmCount": 3,
    "slaveCount": 2,
    "storage": {
      "name": "[parameters('storageAccountNamePrefix')]",
      "count": 2,
      "pool": "db",
      "map": [0,1,1],
      "jumpbox": 0
    }
  },
  "osSettings": {
    "scripts": [
      "[concat(variables('templateBaseUrl'), 'install_postgresql.sh')]",
      "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/shared_scripts/ubuntu/vm-disk-utils-0.1.sh"
    ],
    "imageReference": {
      "publisher": "Canonical",
      "offer": "UbuntuServer",
      "sku": "14.04.2-LTS",
      "version": "latest"
    }
  },
  "networkSettings": {
    "vnetName": "[parameters('virtualNetworkName')]",
    "addressPrefix": "10.0.0.0/16",
    "subnets": {
      "dmz": {
        "name": "dmz",
        "prefix": "10.0.0.0/24",
        "vnet": "[parameters('virtualNetworkName')]"
      },
      "data": {
        "name": "data",
        "prefix": "10.0.1.0/24",
        "vnet": "[parameters('virtualNetworkName')]"
      }
    }
  },
  "availabilitySetSettings": {
    "name": "pgsqlAvailabilitySet",
    "fdCount": 3,
    "udCount": 5
  }
}
```

Tenga en cuenta que la variable **tshirtSize** concatena el tamaño de la camiseta que proporcionó mediante un parámetro (**Small**, **Medium**, **Large**) en el texto **tshirtSize**. Use esta variable para recuperar la variable asociada del objeto complejo para ese tamaño de camiseta.

Luego, puede hacer referencia a estas variables más adelante en la plantilla. La posibilidad de hacer referencia a variables con nombre y sus propiedades simplifica la sintaxis de la plantilla y facilita la comprensión del contexto. En el ejemplo siguiente se define un recurso para implementar mediante los objetos mostrados anteriormente para definir los valores. Por ejemplo, el tamaño de VM se establece al recuperar el valor de `variables('tshirtSize').vmSize` mientras que el valor del tamaño del disco se recupera de `variables('tshirtSize').diskSize`. Además, el URI de una plantilla vinculada se establece con el valor de `variables('tshirtSize').vmTemplate`.

```json
"name": "master-node",
"type": "Microsoft.Resources/deployments",
"apiVersion": "2015-01-01",
"dependsOn": [
    "[concat('Microsoft.Resources/deployments/', 'shared')]"
],
"properties": {
    "mode": "Incremental",
    "templateLink": {
      "uri": "[variables('tshirtSize').vmTemplate]",
      "contentVersion": "1.0.0.0"
    },
    "parameters": {
      "adminPassword": {
        "value": "[parameters('adminPassword')]"
      },
      "replicatorPassword": {
        "value": "[parameters('replicatorPassword')]"
      },
      "osSettings": {
        "value": "[variables('osSettings')]"
      },
      "subnet": {
        "value": "[variables('networkSettings').subnets.data]"
      },
      "commonSettings": {
        "value": {
          "region": "[parameters('region')]",
          "adminUsername": "[parameters('adminUsername')]",
          "namespace": "ms"
        }
      },
      "storageSettings": {
        "value":"[variables('tshirtSize').storage]"
      },
      "machineSettings": {
        "value": {
          "vmSize": "[variables('tshirtSize').vmSize]",
          "diskSize": "[variables('tshirtSize').diskSize]",
          "vmCount": 1,
          "availabilitySet": "[variables('availabilitySetSettings').name]"
        }
      },
      "masterIpAddress": {
        "value": "0"
      },
      "dbType": {
        "value": "MASTER"
      }
    }
  }
}
```

## <a name="pass-state-to-a-template"></a>Pasar el estado a una plantilla
Comparte el estado en una plantilla mediante parámetros que proporciona directamente durante la implementación.

En la tabla siguiente se enumeran los parámetros usados normalmente en las plantillas.

| Nombre | Valor | Descripción |
| --- | --- | --- |
| location |Cadena de una lista restringida de regiones de Azure |La ubicación donde se implementan los recursos. |
| storageAccountNamePrefix |Cadena |Nombre DNS único de la cuenta de almacenamiento donde se colocarán los discos de la VM |
| domainName |String |Nombre de dominio de la VM de JumpBox accesible públicamente en el formato: **{nombreDominio}.{ubicación}.cloudapp.com** Por ejemplo: **mydomainname.westus.cloudapp.azure.com** |
| adminUsername |String |Nombre de usuario de las máquinas virtuales |
| adminPassword |String |Contraseña de las máquinas virtuales |
| tshirtSize |Cadena de una lista restringida de tamaños de camiseta ofrecidos |El tamaño de la unidad de escalado con nombre para aprovisionar. Por ejemplo, "Pequeña", "Mediana", "Grande" |
| virtualNetworkName |String |Nombre de la red virtual que el consumidor desea usar. |
| enableJumpbox |Cadena de una lista restringida (habilitada o deshabilitada) |Parámetro que identifica si se habilitará un JumpBox para el entorno. Valores: "habilitado", "deshabilitado" |

El parámetro **tshirtSize** usado en la sección anterior se define como:

```json
"parameters": {
  "tshirtSize": {
    "type": "string",
    "defaultValue": "Small",
    "allowedValues": [
      "Small",
      "Medium",
      "Large"
    ],
    "metadata": {
      "Description": "T-shirt size of the MongoDB deployment"
    }
  }
}
```

## <a name="pass-state-to-linked-templates"></a>Pasar el estado a las plantillas vinculadas
Al conectarse a plantillas vinculadas, a menudo usa una combinación de variables estáticas y generadas.

### <a name="static-variables"></a>Variables estáticas
Las variables estáticas se suelen usar para proporcionar valores base, como direcciones URL, que se usan en toda la plantilla.

En el siguiente fragmento de la plantilla, `templateBaseUrl` especifica la ubicación raíz de la plantilla en GitHub. La línea siguiente crea una nueva variable `sharedTemplateUrl` que concatena la dirección URL base con el nombre conocido de la plantilla de recursos compartidos. Debajo de esa línea, una variable de objeto complejo se usa para almacenar un tamaño de camiseta, donde la dirección URL base se concatena a la ubicación de la plantilla de configuración conocida y almacenada en la propiedad `vmTemplate`.

La ventaja de este enfoque es que si cambia la ubicación de la plantilla, solo tendrá que cambiar la variable estática en un lugar, que la pase a todas las plantillas vinculadas.

```json
"variables": {
  "templateBaseUrl": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/postgresql-on-ubuntu/",
  "sharedTemplateUrl": "[concat(variables('templateBaseUrl'), 'shared-resources.json')]",
  "tshirtSizeSmall": {
    "vmSize": "Standard_A1",
    "diskSize": 1023,
    "vmTemplate": "[concat(variables('templateBaseUrl'), 'database-2disk-resources.json')]",
    "vmCount": 2,
    "slaveCount": 1,
    "storage": {
      "name": "[parameters('storageAccountNamePrefix')]",
      "count": 1,
      "pool": "db",
      "map": [0,0],
      "jumpbox": 0
    }
  }
}
```

### <a name="generated-variables"></a>Variables generadas
Además de las variables estáticas, hay varias variables que se generan dinámicamente. En esta sección se identifican algunos de los tipos comunes de variables generadas.

#### <a name="tshirtsize"></a>tshirtSize
Está familiarizado con esta variable generada de los ejemplos mencionados anteriormente.

#### <a name="networksettings"></a>networkSettings
En una plantilla de solución de capacidad o con ámbito completo, las plantillas vinculadas suele crean recursos que existen en una red. Un enfoque sencillo es usar un objeto complejo para almacenar la configuración de red y pasarla a las plantillas vinculadas.

A continuación puede verse un ejemplo de comunicación de la configuración de red.

```json
"networkSettings": {
  "vnetName": "[parameters('virtualNetworkName')]",
  "addressPrefix": "10.0.0.0/16",
  "subnets": {
    "dmz": {
      "name": "dmz",
      "prefix": "10.0.0.0/24",
      "vnet": "[parameters('virtualNetworkName')]"
    },
    "data": {
      "name": "data",
      "prefix": "10.0.1.0/24",
      "vnet": "[parameters('virtualNetworkName')]"
    }
  }
}
```

#### <a name="availabilitysettings"></a>availabilitySettings
Los recursos creados en las plantillas vinculadas a menudo se colocan en un conjunto de disponibilidad. En el ejemplo siguiente, se especifica el nombre del conjunto de disponibilidad y también el número de dominios de error y dominios de actualización que se van a usar.

```json
"availabilitySetSettings": {
  "name": "pgsqlAvailabilitySet",
  "fdCount": 3,
  "udCount": 5
}
```

Si necesita varios conjuntos de disponibilidad (por ejemplo, uno para los nodos principales y otro para los nodos de datos), puede usar un nombre como prefijo, especificar varios conjuntos de disponibilidad o seguir el modelo mostrado anteriormente para la creación de una variable para un tamaño de camiseta específico.

#### <a name="storagesettings"></a>storageSettings
Los detalles de almacenamiento a menudo se comparten con las plantillas vinculadas. En el ejemplo siguiente, un objeto *storageSettings* proporciona detalles sobre los nombres del contenedor y la cuenta de almacenamiento.

```json
"storageSettings": {
    "vhdStorageAccountName": "[parameters('storageAccountName')]",
    "vhdContainerName": "[variables('vmStorageAccountContainerName')]",
    "destinationVhdsContainer": "[concat('https://', parameters('storageAccountName'), variables('vmStorageAccountDomain'), '/', variables('vmStorageAccountContainerName'), '/')]"
}
```

#### <a name="ossettings"></a>osSettings
Con las plantillas vinculadas, tendrá que pasar la configuración del sistema operativo a varios tipos de nodos a través de diferentes tipos de configuración conocidos. Un objeto complejo es una forma sencilla de almacenar y compartir información del sistema operativo y también facilita la compatibilidad con varias opciones de sistema operativo en la implementación.

En el ejemplo siguiente se muestra un objeto de *osSettings*:

```json
"osSettings": {
  "imageReference": {
    "publisher": "Canonical",
    "offer": "UbuntuServer",
    "sku": "14.04.2-LTS",
    "version": "latest"
  }
}
```

#### <a name="machinesettings"></a>machineSettings
Una variable generada, *machineSettings* es un objeto complejo que contiene una combinación de variables principales para crear una VM. Las variables incluyen el nombre de usuario y la contraseña de administrador, un prefijo para los nombres de VM y una referencia de imagen del sistema operativo.

```json
"machineSettings": {
    "adminUsername": "[parameters('adminUsername')]",
    "adminPassword": "[parameters('adminPassword')]",
    "machineNamePrefix": "mongodb-",
    "osImageReference": {
        "publisher": "[variables('osFamilySpec').imagePublisher]",
        "offer": "[variables('osFamilySpec').imageOffer]",
        "sku": "[variables('osFamilySpec').imageSKU]",
        "version": "latest"
    }
},
```

Tenga en cuenta que *osImageReference* recupera los valores de la variable *osSettings* definida en la plantilla principal. Eso significa que puede cambiar fácilmente el sistema operativo de una máquina virtual, por completo, o en función de la preferencia de un consumidor de plantilla.

#### <a name="vmscripts"></a>vmScripts
El objeto *vmScripts* contiene detalles sobre los scripts que se descargarán y ejecutarán en una instancia de máquina virtual, incluidas las referencias externas e internas. Las referencias externas incluyen la infraestructura.
Las referencias internas incluyen el software instalado y la configuración.

La propiedad *scriptsToDownload* se usa para mostrar los scripts que se descargarán en la máquina virtual. Este objeto también contiene referencias a argumentos de línea de comandos para diferentes tipos de acciones. Estas acciones incluyen la ejecución de la instalación predeterminada de cada nodo individual, una instalación que se ejecuta después de que se implementan todos los nodos y los scripts adicionales que pueden ser específicos de una plantilla determinada.

Este ejemplo es de una plantilla que se usa para implementar MongoDB, lo que requiere un árbitro para proporcionar alta disponibilidad. *arbiterNodeInstallCommand* se ha agregado a *vmScripts* para instalar el árbitro.

En la sección de variables es donde encuentra las variables que definen el texto específico para ejecutar el script con los valores adecuados.

```json
"vmScripts": {
    "scriptsToDownload": [
        "[concat(variables('scriptUrl'), 'mongodb-', variables('osFamilySpec').osName, '-install.sh')]",
        "[concat(variables('sharedScriptUrl'), 'vm-disk-utils-0.1.sh')]"
    ],
    "regularNodeInstallCommand": "[variables('installCommand')]",
    "lastNodeInstallCommand": "[concat(variables('installCommand'), ' -l')]",
    "arbiterNodeInstallCommand": "[concat(variables('installCommand'), ' -a')]"
},
```

## <a name="return-state-from-a-template"></a>Devolver el estado de una plantilla
No solo puede pasar datos a una plantilla, también puede compartir datos de nuevo con la plantilla de llamada. En la sección **Salidas** de una plantilla vinculada, puede proporcionar pares de clave/valor que pueden usarse en la plantilla de origen.

En el ejemplo siguiente se muestra cómo pasar la dirección IP privada generada en una plantilla vinculada.

```json
"outputs": {
    "masterip": {
        "value": "[reference(concat(variables('nicName'),0)).ipConfigurations[0].properties.privateIPAddress]",
        "type": "string"
     }
}
```

Dentro de la plantilla principal, puede usar esos datos con la sintaxis siguiente:

    "[reference('master-node').outputs.masterip.value]"

Puede usar esta expresión en la sección de salidas o en la sección de recursos de la plantilla principal. No puede usar la expresión en la sección de variables porque se basa en el estado de tiempo de ejecución. Para devolver este valor desde la plantilla principal, use:

```json
"outputs": {
  "masterIpAddress": {
    "value": "[reference('master-node').outputs.masterip.value]",
    "type": "string"
  }
```

Para obtener un ejemplo de cómo usar la sección de salidas de una plantilla vinculada para devolver discos de datos para una máquina virtual, consulte [Crear varios discos de datos para una máquina virtual](resource-group-create-multiple.md).

## <a name="define-authentication-settings-for-virtual-machine"></a>Definir la configuración de autenticación de una máquina virtual
Puede usar el mismo patrón que se mostró anteriormente para las opciones de configuración para especificar la configuración de autenticación de una máquina virtual. Cree un parámetro para pasarlo en el tipo de autenticación.

```json
"parameters": {
  "authenticationType": {
    "allowedValues": [
      "password",
      "sshPublicKey"
    ],
    "defaultValue": "password",
    "metadata": {
      "description": "Authentication type"
    },
    "type": "string"
  }
}
```

Agregue variables para los diferentes tipos de autenticación, y una variable para almacenar qué tipo se usa en esta implementación basándose en el valor del parámetro.

```json
"variables": {
  "osProfile": "[variables(concat('osProfile', parameters('authenticationType')))]",
  "osProfilepassword": {
    "adminPassword": "[parameters('adminPassword')]",
    "adminUsername": "notused",
    "computerName": "[parameters('vmName')]",
    "customData": "[base64(variables('customData'))]"
  },
  "osProfilesshPublicKey": {
    "adminUsername": "notused",
    "computerName": "[parameters('vmName')]",
    "customData": "[base64(variables('customData'))]",
    "linuxConfiguration": {
      "disablePasswordAuthentication": "true",
      "ssh": {
        "publicKeys": [
          {
            "keyData": "[parameters('sshPublicKey')]",
            "path": "/home/notused/.ssh/authorized_keys"
          }
        ]
      }
    }
  }
}
```

Al definir la máquina virtual, establece **osProfile** a la variable que creó.

```json
{
  "type": "Microsoft.Compute/virtualMachines",
  ...
  "osProfile": "[variables('osProfile')]"
}
```

## <a name="next-steps"></a>Pasos siguientes
* Para obtener información sobre las secciones de la plantilla, consulte [Creación de plantillas de Azure Resource Manager](resource-group-authoring-templates.md)
* Para ver las funciones que están disponibles en una plantilla, consulte [Funciones de plantillas de Azure Resource Manager](resource-group-template-functions.md)
