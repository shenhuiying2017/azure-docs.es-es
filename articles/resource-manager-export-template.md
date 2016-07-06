<properties
	pageTitle="Exportación de plantillas de Azure Resource Manager | Microsoft Azure"
	description="Use Azure Resource Manager para exportar una plantilla desde un grupo de recursos existente."
	services="azure-resource-manager"
	documentationCenter=""
	authors="tfitzmac"
	manager="timlt"
	editor="tysonn"/>

<tags
	ms.service="azure-resource-manager"
	ms.workload="multiple"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="06/28/2016"
	ms.author="tomfitz"/>

# Exportación de plantillas de Azure Resource Manager desde recursos existentes

Resource Manager permite exportar una plantilla de Resource Manager a partir de recursos existentes en la suscripción. Puede usar esa plantilla generada para aprender sobre la sintaxis de plantillas o para automatizar la nueva implementación de su solución según sea necesario.

Es importante tener en cuenta que hay dos formas diferentes de exportar una plantilla:

- Puede exportar la plantilla real que se utilizó para una implementación. La plantilla exportada incluye todos los parámetros y variables exactamente como se definieron en la plantilla original. Este enfoque es especialmente útil cuando se han implementado los recursos mediante el portal y ahora quiere ver cómo construir la plantilla para crear esos recursos.
- Puede exportar una plantilla que representa el estado actual del grupo de recursos. La plantilla exportada no se basa en ninguna plantilla que se usara para la implementación. Al contrario, crea una plantilla que es una instantánea del grupo de recursos. La plantilla exportada tendrá muchos valores codificados de forma rígida y es probable que no tenga tantos parámetros como normalmente se definirían. Este enfoque es útil cuando se ha modificado el grupo de recursos mediante el portal o scripts y ahora debe capturar al grupo de recursos como una plantilla.

En este tema se muestran ambos enfoques. En el artículo [Personalización de una plantilla de Azure Resource Manager exportada](resource-manager-customize-template.md), verá cómo tomar una plantilla que se generó a partir del estado actual del grupo de recursos y hacerla más útil para volver a implementar su solución.

En este tutorial, va a iniciar sesión en el Portal de Azure, crear una cuenta de almacenamiento por medio del portal y exportar la plantilla para esa cuenta de almacenamiento. Agregará una red virtual para modificar el grupo de recursos. Por último, exportará una nueva plantilla que representa su estado actual. Aunque este artículo se centra en una infraestructura simplificada, puede usar estos mismos pasos para exportar una plantilla para una solución más complicada.

## Crear una cuenta de almacenamiento

1. En el [Portal de Azure](https://portal.azure.com), seleccione **Nuevo** > **Datos y almacenamiento** > **Cuenta de almacenamiento**.

      ![Crear almacenamiento](./media/resource-manager-export-template/create-storage.png)

2. Cree una cuenta de almacenamiento con el nombre **storage**, sus iniciales y la fecha. El nombre de la cuenta de almacenamiento debe ser único en Azure. Si inicialmente ha probado un nombre que ya está en uso, intente una variación. Para el grupo de recursos, use **ExportGroup**. Puede usar los valores predeterminados para los demás campos. Seleccione **Crear**.

      ![Proporcionar los valores de almacenamiento](./media/resource-manager-export-template/provide-storage-values.png)

Una vez finalizada la implementación, la suscripción contiene la cuenta de almacenamiento.

## Exportación de la plantilla desde el historial de implementación

1. Vaya a la hoja del grupo de recursos que ha creado. Observará que aparece el resultado de la última implementación. Seleccione este vínculo.

      ![Hoja del grupo de recursos](./media/resource-manager-export-template/resource-group-blade.png)

2. Verá un historial de implementaciones para el grupo. En su caso, probablemente solo aparezca una implementación. Selecciónela.

     ![última implementación](./media/resource-manager-export-template/last-deployment.png)

3. Se muestra un resumen de la implementación. El resumen incluye el estado de la implementación y sus operaciones, además de los valores que proporcionó para los parámetros. Para ver la plantilla que se usó para la implementación, seleccione **Ver plantilla**.

     ![Ver el resumen de la implementación](./media/resource-manager-export-template/deployment-summary.png)

4. El Administrador de recursos recupera los cinco archivos siguientes para usted:

   1. **Plantilla**: la plantilla que define la infraestructura de la solución. Cuando creó la cuenta de almacenamiento por medio del portal, Resource Manager usó una plantilla para implementarla y la guardó para futura referencia.
   2. **Parámetros**: un archivo de parámetros que puede usar para pasar valores durante la implementación. Contiene los valores que proporcionó durante la primera implementación, pero puede cambiar cualquiera de ellos al volver a implementar la plantilla.
   3. **CLI**: un archivo de script de la interfaz de la línea de comandos (CLI) de Azure que puede usar para implementar la plantilla.
   4. **PowerShell**: un archivo de script de Azure PowerShell que puede usar para implementar la plantilla.
   5. **.NET**: una clase .NET que puede utilizar para implementar la plantilla.

     Los archivos están disponibles mediante vínculos en la hoja. De forma predeterminada, la plantilla está seleccionada.

       ![Ver plantilla](./media/resource-manager-export-template/view-template.png)

     Preste especial atención a la plantilla. Su plantilla debería parecerse a esta:

        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "name": {
              "type": "String"
            },
            "accountType": {
              "type": "String"
            },
            "location": {
              "type": "String"
            },
            "encryptionEnabled": {
              "defaultValue": false,
              "type": "Bool"
            }
          },
          "resources": [
            {
              "type": "Microsoft.Storage/storageAccounts",
              "sku": {
                "name": "[parameters('accountType')]"
              },
              "kind": "Storage",
              "name": "[parameters('name')]",
              "apiVersion": "2016-01-01",
              "location": "[parameters('location')]",
              "properties": {
                "encryption": {
                  "services": {
                    "blob": {
                      "enabled": "[parameters('encryptionEnabled')]"
                    }
                  },
                  "keySource": "Microsoft.Storage"
                }
              }
            }
          ]
        }
 
Esta es la plantilla real usada para crear la cuenta de almacenamiento. Observe que contiene parámetros que le permiten implementar distintos tipos de cuentas de almacenamiento. Para aprender más sobre la estructura de una plantilla, consulte [Creación de plantillas de Azure Resource Manager](resource-group-authoring-templates.md). Para ver la lista completa de funciones que puede usar en una plantilla, consulte [Funciones de plantilla de Azure Resource Manager](resource-group-template-functions.md).


## Incorporación de una red virtual

La plantilla que descargó en la sección anterior representaba la infraestructura para esa implementación original, pero no incluirá los cambios que realice después de la implementación. Para ilustrar este problema, vamos a modificar el grupo de recursos agregando una red virtual por medio del portal.

1. En la hoja del grupo de recursos, seleccione **Agregar**.

      ![agregar recurso](./media/resource-manager-export-template/add-resource.png)

2. Seleccione **Red virtual** en los recursos disponibles.

      ![seleccionar red virtual](./media/resource-manager-export-template/select-vnet.png)

2. Asigne **VNET** como nombre de la red virtual y use los valores predeterminados para las demás propiedades. Seleccione **Crear**.

      ![Establecer alerta](./media/resource-manager-export-template/create-vnet.png)

3. Una vez implementada la red virtual correctamente en el grupo de recursos, vuelva a consultar el historial de implementación. Ahora verá dos implementaciones. Si no ve la segunda implementación, puede que deba cerrar la hoja del grupo de recursos y abrirla de nuevo. Seleccione la más reciente.

      ![Historial de implementación](./media/resource-manager-export-template/deployment-history.png)

4. Eche un vistazo a la plantilla para esa implementación. Observe que solamente define los cambios realizados para agregar la red virtual.

Por lo general, se recomienda trabajar con una plantilla que implemente toda la infraestructura de la solución en una sola operación, en lugar de tener que recordar las muchas plantillas diferentes que hay que implementar.


## Exportación de la plantilla desde el grupo de recursos

Aunque cada implementación solo muestra los cambios que haya realizado en el grupo de recursos, puede exportar en cualquier momento una plantilla para mostrar los atributos del grupo de recursos completo.

1. Para ver la plantilla de un grupo de recursos, seleccione **Exportar plantilla**.

      ![exportar grupo de recursos](./media/resource-manager-export-template/export-resource-group.png)

     No todos los tipos de recursos admiten la función de exportación de plantilla. Si el grupo de recursos solo contiene la cuenta de almacenamiento y la red virtual mostradas en este artículo, no verá un error. Sin embargo, si ha creado otros tipos de recursos, verá un error que indica que hay un problema con la exportación. Puede aprender más sobre cómo resolver estos problemas en la sección [Solución de problemas de exportación](#fixing-export-issues).

      

2. Volverá a ver los cinco archivos que puede usar para volver a implementar la solución, pero esta vez la plantilla es algo diferente. Esta plantilla solo contiene dos parámetros (uno para el nombre de la cuenta de almacenamiento y otro para el nombre de la red virtual).

        "parameters": {
          "virtualNetworks_VNET_name": {
            "defaultValue": "VNET",
            "type": "String"
          },
          "storageAccounts_storagetf05092016_name": {
            "defaultValue": "storagetf05092016",
            "type": "String"
          }
        },

     Resource Manager no recuperó las plantillas usadas durante la implementación. En su lugar, generó una nueva plantilla basada en la configuración actual de los recursos. Por ejemplo, los valores de ubicación de la cuenta de almacenamiento y replicación se establecen en:

        "location": "northeurope",
        "tags": {},
        "properties": {
            "accountType": "Standard_RAGRS"
        },

3. Descargue la plantilla para trabajar en ella localmente.

      ![Descargar plantilla](./media/resource-manager-export-template/download-template.png)

4. Busque el archivo .zip que descargó y extraiga el contenido. Puede usar esta plantilla descargada para volver a implementar su infraestructura.

## Solución de problemas de exportación

No todos los tipos de recursos admiten la función de exportación de plantilla. Algunos tipos de recursos en concreto no se exportan para evitar la exposición de información confidencial. Por ejemplo, si tiene una cadena de conexión en la configuración del sitio, probablemente no querrá que se muestre explícitamente en una plantilla exportada. Puede evitar este problema si agrega manualmente los recursos que faltan a la plantilla.

> [AZURE.NOTE] Solo encontrará problemas de exportación si exporta desde un grupo de recursos en lugar de desde el historial de implementación. Si su última implementación representa con precisión el estado actual del grupo de recursos, debe exportar la plantilla desde el historial de implementación en lugar de desde el grupo de recursos. Exporte solo desde un grupo de recursos si ha realizado cambios en el grupo de recursos que no están definidos en una única plantilla.

Por ejemplo, si exporta una plantilla para un grupo de recursos que contiene una aplicación web, una base de datos SQL y una cadena de conexión en la configuración del sitio, verá el mensaje siguiente.

![show error](./media/resource-manager-export-template/show-error.png)

Al selecciona el mensaje se mostrará exactamente qué tipos de recursos no se han exportado.
     
![show error](./media/resource-manager-export-template/show-error-details.png)

Algunas soluciones comunes se muestran a continuación. Para implementar estos recursos, debe agregar parámetros a la plantilla. Para más información, consulte [Personalizar y volver a implementar una plantilla exportada](resource-manager-customize-template.md).

### Cadena de conexión

En el recurso de sitios web, agregue una definición para la cadena de conexión a la base de datos:

```
{
  "type": "Microsoft.Web/sites",
  ...
  "resources": [
    {
      "apiVersion": "2015-08-01",
      "type": "config",
      "name": "connectionstrings",
      "dependsOn": [
          "[concat('Microsoft.Web/Sites/', parameters('<site-name>'))]"
      ],
      "properties": {
          "DefaultConnection": {
            "value": "[concat('Data Source=tcp:', reference(concat('Microsoft.Sql/servers/', parameters('<database-server-name>'))).fullyQualifiedDomainName, ',1433;Initial Catalog=', parameters('<database-name>'), ';User Id=', parameters('<admin-login>'), '@', parameters('<database-server-name>'), ';Password=', parameters('<admin-password>'), ';')]",
              "type": "SQLServer"
          }
      }
    }
  ]
}
```    

### Extensión de sitio web

En el recurso de sitio web, agregue una definición para el código que se instalará:

```
{
  "type": "Microsoft.Web/sites",
  ...
  "resources": [
    {
      "name": "MSDeploy",
      "type": "extensions",
      "location": "[resourceGroup().location]",
      "apiVersion": "2015-08-01",
      "dependsOn": [
        "[concat('Microsoft.Web/sites/', parameters('<site-name>'))]"
      ],
      "properties": {
        "packageUri": "[concat(parameters('<artifacts-location>'), '/', parameters('<package-folder>'), '/', parameters('<package-file-name>'), parameters('<sas-token>'))]",
        "dbType": "None",
        "connectionString": "",
        "setParameters": {
          "IIS Web Application Name": "[parameters('<site-name>')]"
        }
      }
    }
  ]
}
```

### Extensión de máquina virtual

Para ver ejemplos de extensiones de máquina virtual, consulte [Ejemplos de configuración de la extensión de máquina virtual de Windows de Azure](./virtual-machines/virtual-machines-windows-extensions-configuration-samples.md).

### Puerta de enlace de red virtual

Agregue un tipo de recurso de puerta de enlace de red virtual.

```
{
  "type": "Microsoft.Network/virtualNetworkGateways",
  "name": "[parameters('<gateway-name>')]",
  "apiVersion": "2015-06-15",
  "location": "[resourceGroup().location]",
  "properties": {
    "gatewayType": "[parameters('<gateway-type>')]",
    "ipConfigurations": [
      {
        "name": "default",
        "properties": {
          "privateIPAllocationMethod": "Dynamic",
          "subnet": {
            "id": "[resourceId('Microsoft.Network/virtualNetworks/subnets', parameters('<vnet-name>'), parameters('<new-subnet-name>'))]"
          },
          "publicIpAddress": {
            "id": "[resourceId('Microsoft.Network/publicIPAddresses', parameters('<new-public-ip-address-Name>'))]"
          }
        }
      }
    ],
    "enableBgp": false,
    "vpnType": "[parameters('<vpn-type>')]"
  },
  "dependsOn": [
    "Microsoft.Network/virtualNetworks/codegroup4/subnets/GatewaySubnet",
    "[concat('Microsoft.Network/publicIPAddresses/', parameters('<new-public-ip-address-Name>'))]"
  ]
},
```

### Puerta de enlace de red local

Agregue un tipo de recurso de puerta de enlace de red local.

```
{
    "type": "Microsoft.Network/localNetworkGateways",
    "name": "[parameters('<local-network-gateway-name>')]",
    "apiVersion": "2015-06-15",
    "location": "[resourceGroup().location]",
    "properties": {
      "localNetworkAddressSpace": {
        "addressPrefixes": "[parameters('<address-prefixes>')]"
      }
    }
}
```

### Conexión

Agregue un tipo de recurso de conexión.

```
{
    "apiVersion": "2015-06-15",
    "name": "[parameters('<connection-name>')]",
    "type": "Microsoft.Network/connections",
    "location": "[resourceGroup().location]",
    "properties": {
        "virtualNetworkGateway1": {
        "id": "[resourceId('Microsoft.Network/virtualNetworkGateways', parameters('<gateway-name>'))]"
      },
      "localNetworkGateway2": {
        "id": "[resourceId('Microsoft.Network/localNetworkGateways', parameters('<local-gateway-name>'))]"
      },
      "connectionType": "IPsec",
      "routingWeight": 10,
      "sharedKey": "[parameters('<shared-key>')]"
    }
},
```


## Pasos siguientes

¡Enhorabuena! Ha aprendido a exportar una plantilla desde recursos creados en el portal.

- En la segunda parte de este tutorial, personalizará la plantilla que acaba de descargar agregando más parámetros y la volverá a implementar mediante un script. Consulte [Personalizar y volver a implementar la plantilla exportada](resource-manager-customize-template.md).
- Para ver cómo exportar una plantilla mediante PowerShell, consulte [Uso de Azure PowerShell con Azure Resource Manager](powershell-azure-resource-manager.md).
- Para ver cómo exportar una plantilla mediante la CLI de Azure, consulte [Uso de la CLI de Azure para Mac, Linux y Windows con Azure Resource Manager](xplat-cli-azure-resource-manager.md).

<!---HONumber=AcomDC_0629_2016-->