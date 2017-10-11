---
title: "Configuración del estado deseado con una plantilla de Resource Manager| Microsoft Docs"
description: "Definición de la plantilla de Resource Manager para la configuración de estado deseado en Azure con ejemplos y solución de problemas"
services: virtual-machines-windows
documentationcenter: 
author: zjalexander
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager
keywords: 
ms.assetid: b5402e5a-1768-4075-8c19-b7f7402687af
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: na
ms.date: 09/15/2016
ms.author: zachal
ms.openlocfilehash: 4292f9d8cd181073fdf0adff99fcb9624e0e9f55
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/03/2017
---
# <a name="windows-vmss-and-desired-state-configuration-with-azure-resource-manager-templates"></a>VMSS de Windows y configuración de estado deseado con plantillas de Azure Resource Manager
En este artículo, se describe la plantilla de Resource Manager para el [controlador de extensiones de configuración de estado deseado](extensions-dsc-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

## <a name="template-example-for-a-windows-vm"></a>Ejemplo de plantilla para una máquina virtual de Windows
El siguiente fragmento corresponde a la sección Resource de la plantilla.

```json
            "name": "Microsoft.Powershell.DSC",
            "type": "extensions",
             "location": "[resourceGroup().location]",
             "apiVersion": "2015-06-15",
             "dependsOn": [
                  "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
              ],
              "properties": {
                  "publisher": "Microsoft.Powershell",
                  "type": "DSC",
                  "typeHandlerVersion": "2.20",
                  "autoUpgradeMinorVersion": true,
                  "forceUpdateTag": "[parameters('dscExtensionUpdateTagVersion')]",
                  "settings": {
                      "configuration": {
                          "url": "[concat(parameters('_artifactsLocation'), '/', variables('dscExtensionArchiveFolder'), '/', variables('dscExtensionArchiveFileName'))]",
                          "script": "dscExtension.ps1",
                          "function": "Main"
                      },
                      "configurationArguments": {
                          "nodeName": "[variables('vmName')]"
                      }
                  },
                  "protectedSettings": {
                      "configurationUrlSasToken": "[parameters('_artifactsLocationSasToken')]"
                  }
              }

```

## <a name="template-example-for-windows-vmss"></a>Ejemplo de plantilla para VMSS de Windows
Un nodo de VMSS tiene una sección "properties" con el atributo "extensionProfile" de "VirtualMachineProfile". DSC se agrega en "extensions". 

```json
"extensionProfile": {
            "extensions": [
                {
                    "name": "Microsoft.Powershell.DSC",
                    "properties": {
                        "publisher": "Microsoft.Powershell",
                        "type": "DSC",
                        "typeHandlerVersion": "2.20",
                        "autoUpgradeMinorVersion": true,
                        "forceUpdateTag": "[parameters('DscExtensionUpdateTagVersion')]",
                        "settings": {
                            "configuration": {
                                "url": "[concat(parameters('_artifactsLocation'), '/', variables('DscExtensionArchiveFolder'), '/', variables('DscExtensionArchiveFileName'))]",
                                "script": "DscExtension.ps1",
                                "function": "Main"
                            },
                            "configurationArguments": {
                                "nodeName": "localhost"
                            }
                        },
                        "protectedSettings": {
                            "configurationUrlSasToken": "[parameters('_artifactsLocationSasToken')]"
                        }
                    }
                }
            ]
        }
```

## <a name="detailed-settings-information"></a>Información de configuración detallada
Este es el esquema para la parte de configuración (settings) de la extensión DSC de Azure en una plantilla de Azure Resource Manager.

```json

"settings": {
  "wmfVersion": "latest",
  "configuration": {
    "url": "http://validURLToConfigLocation",
    "script": "ConfigurationScript.ps1",
    "function": "ConfigurationFunction"
  },
  "configurationArguments": {
    "argument1": "Value1",
    "argument2": "Value2"
  },
  "configurationData": {
    "url": "https://foo.psd1"
  },
  "privacy": {
    "dataCollection": "enable"
  },
  "advancedOptions": {
    "downloadMappings": {
      "customWmfLocation": "http://myWMFlocation"
    }
  } 
},
"protectedSettings": {
  "configurationArguments": {
    "parameterOfTypePSCredential1": {
      "userName": "UsernameValue1",
      "password": "PasswordValue1"
    },
    "parameterOfTypePSCredential2": {
      "userName": "UsernameValue2",
      "password": "PasswordValue2"
    }
  },
  "configurationUrlSasToken": "?g!bber1sht0k3n",
  "configurationDataUrlSasToken": "?dataAcC355T0k3N"
}

```

## <a name="details"></a>Detalles
| Nombre de propiedad | Tipo | Description |
| --- | --- | --- |
| settings.wmfVersion |string |Especifica la versión de Windows Management Framework que debe instalarse en la máquina virtual. Si se establece esta propiedad en "latest", se instala la versión más reciente de WMF. Los únicos valores posibles actuales para esta propiedad son **"4.0", "5.0", "5.0PP" y "latest"**. Estos valores posibles están sujetos a actualizaciones. El valor predeterminado es "latest". |
| settings.configuration.url |string |Especifica la ubicación de la dirección URL desde la que descargar el archivo zip de la configuración de DSC. Si la dirección URL proporcionada requiere un token de SAS para el acceso, debe establecer la propiedad protectedSettings.configurationUrlSasToken en el valor de su token de SAS. Esta propiedad es necesaria si se definen settings.configuration.script o settings.configuration.function. |
| settings.configuration.script |string |Especifica el nombre de archivo del script que contiene la definición de la configuración de DSC. Este script debe estar en la carpeta raíz del archivo zip descargado de la dirección URL especificada en la propiedad configuration.url. Esta propiedad es necesaria si se definen settings.configuration.url o settings.configuration.script. |
| settings.configuration.function |string |Especifica el nombre de la configuración de DSC. La configuración con nombre debe incluirse en el script definido en configuration.script. Esta propiedad es necesaria si se definen settings.configuration.url o settings.configuration.function. |
| settings.configurationArguments |Colección |Define los parámetros que desea pasar a la configuración de DSC. Esta propiedad no está cifrada. |
| settings.configurationData.url |string |Especifica la dirección URL desde la que descargar el archivo de datos de configuración (.psd1) que se usará como entrada para la configuración de DSC. Si la dirección URL proporcionada requiere un token de SAS para el acceso, debe establecer la propiedad protectedSettings.configurationDataUrlSasToken en el valor de su token de SAS. |
| settings.privacy.dataEnabled |string |Habilita o deshabilita la recopilación de telemetría. Los únicos valores posibles para esta propiedad son **"Enable", "Disable", '' o $null**. Si se deja esta propiedad en blanco o como null, se habilita la telemetría. El valor predeterminado es ". [Más información](https://blogs.msdn.microsoft.com/powershell/2016/02/02/azure-dsc-extension-data-collection-2/) |
| settings.advancedOptions.downloadMappings |Colección |Define las ubicaciones alternativas desde las que descargar WMF. [Más información](http://blogs.msdn.com/b/powershell/archive/2015/10/21/azure-dsc-extension-2-2-amp-how-to-map-downloads-of-the-extension-dependencies-to-your-own-location.aspx) |
| protectedSettings.configurationArguments |Colección |Define los parámetros que desea pasar a la configuración de DSC. Esta propiedad no está cifrada. |
| protectedSettings.configurationUrlSasToken |string |Especifica el token de SAS para acceder a la dirección URL definida en configuration.url. Esta propiedad no está cifrada. |
| protectedSettings.configurationDataUrlSasToken |string |Especifica el token de SAS para acceder a la dirección URL definida en configurationData.url. Esta propiedad no está cifrada. |

## <a name="settings-vs-protectedsettings"></a>Settings frente a ProtectedSettings
Todas las configuraciones se guardan en un archivo de texto de configuración en la máquina virtual.
Las propiedades en "settings" son públicas porque no están cifradas en el archivo de texto de configuración.
Las propiedades en "protectedSettings" se cifran con un certificado y no se muestran en texto sin formato en este archivo de la máquina virtual.

Si la configuración necesita credenciales, pueden incluirse en protectedSettings:

```json
"protectedSettings": {
    "configurationArguments": {
        "parameterOfTypePSCredential1": {
               "userName": "UsernameValue1",
               "password": "PasswordValue1"
        }
    }
}
```

## <a name="example"></a>Ejemplo
El ejemplo siguiente se deriva de la sección "Introducción" de la [página de introducción al controlador de extensiones DSC](extensions-dsc-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
Este ejemplo usa plantillas de Resource Manager en lugar de cmdlets para implementar la extensión. Guardar la configuración "IisInstall.ps1", colóquela en un archivo .ZIP y cargue el archivo en una dirección URL accesible. Este ejemplo usa el almacenamiento de blobs de Azure, pero es posible descargar archivos .ZIP desde cualquier ubicación.

En la plantilla de Azure Resource Manager, el siguiente código indica a la máquina virtual que descargue el archivo correcto y ejecute la función de PowerShell adecuada:

```json
"settings": {
    "configuration": {
        "url": "https://demo.blob.core.windows.net/",
        "script": "IisInstall.ps1",
        "function": "IISInstall"
    }
    } 
},
"protectedSettings": {
    "configurationUrlSasToken": "odLPL/U1p9lvcnp..."
}
```

## <a name="updating-from-the-previous-format"></a>Actualización del formato anterior
Cualquier configuración en el formato anterior (que contiene las propiedades públicas ModulesUrl, ConfigurationFunction, SasToken o Properties) se adaptará automáticamente al formato actual y se ejecutará tal como lo hacía antes.

El esquema de configuración anterior tenía un aspecto similar al siguiente:

```json
"settings": {
    "WMFVersion": "latest",
    "ModulesUrl": "https://UrlToZipContainingConfigurationScript.ps1.zip",
    "SasToken": "SAS Token if ModulesUrl points to private Azure Blob Storage",
    "ConfigurationFunction": "ConfigurationScript.ps1\\ConfigurationFunction",
    "Properties":  {
        "ParameterToConfigurationFunction1":  "Value1",
        "ParameterToConfigurationFunction2":  "Value2",
        "ParameterOfTypePSCredential1": {
            "UserName": "UsernameValue1",
            "Password": "PrivateSettingsRef:Key1" 
        },
        "ParameterOfTypePSCredential2": {
            "UserName": "UsernameValue2",
            "Password": "PrivateSettingsRef:Key2"
        }
    }
},
"protectedSettings": { 
    "Items": {
        "Key1": "PasswordValue1",
        "Key2": "PasswordValue2"
    },
    "DataBlobUri": "https://UrlToConfigurationDataWithOptionalSasToken.psd1"
}
```

Así se adapta el formato anterior al actual:

| Nombre de propiedad | Equivalente en el esquema anterior |
| --- | --- |
| settings.wmfVersion |settings.wmfVersion |
| settings.configuration.url |settings.ModulesUrl |
| settings.configuration.script |Primera parte de settings.ConfigurationFunction (antes de '\\\\') |
| settings.configuration.function |Segunda parte de settings.ConfigurationFunction (después de '\\\\') |
| settings.configurationArguments |settings.Properties |
| settings.configurationData.url |protectedSettings.DataBlobUri (sin token de SAS) |
| settings.privacy.dataEnabled |settings.privacy.dataEnabled |
| settings.advancedOptions.downloadMappings |settings.advancedOptions.downloadMappings |
| protectedSettings.configurationArguments |protectedSettings.Properties |
| protectedSettings.configurationUrlSasToken |settings.SasToken |
| protectedSettings.configurationDataUrlSasToken |Token de SAS de protectedSettings.DataBlobUri |

## <a name="troubleshooting---error-code-1100"></a>Solución de problemas: código de error 1100
El código de error 1100 indica que hay un problema con la entrada del usuario a la extensión DSC.
El texto de estos errores es variable y puede cambiar.
Estos son algunos de los errores que pueden surgir y cómo corregirlos.

### <a name="invalid-values"></a>Valores no válidos
"Privacy.dataCollection is '{0}'. The only possible values are '', 'Enable', and 'Disable'" (Privacy.dataCollection es '{0}'. Los únicos valores posibles son ", "Enable" y "Disable") "WmfVersion is '{0}'. Los únicos valores posibles son: y "latest".

Problema: No se permite un valor proporcionado.

Solución: Cambie el valor no válido a uno válido. Consulte la tabla en la sección Detalles.

### <a name="invalid-url"></a>Dirección URL no válida
"ConfigurationData.url is '{0}'. This is not a valid URL" (ConfigurationData.url es '{0}'. No es una dirección URL válida) "DataBlobUri is '{0}'. This is not a valid URL" (DataBlobUri es '{0}'. No es una dirección URL válida) "Configuration.url is '{0}'. This is not a valid URL" This is not a valid URL" (Configuration.url es '{0}'. No es una dirección URL válida)

Problema: Se ha proporcionado una dirección URL que no es válida.

Solución: Compruebe todas las direcciones URL proporcionadas. Asegúrese de que todas las direcciones URL se resuelvan en ubicaciones válidas a las que la extensión pueda acceder en la máquina remota.

### <a name="invalid-configurationargument-type"></a>Tipo de ConfigurationArguments no válido
"Invalid configurationArguments type {0}" (Tipo de configurationArguments no válido {0})

Problema: La propiedad ConfigurationArguments no se puede resolver en un objeto Hashtable. 

Solución: Convierta la propiedad ConfigurationArguments en Hashtable. Siga el formato proporcionado en el ejemplo anterior. Esté atento a las comillas, comas y llaves.

### <a name="duplicate-configurationarguments"></a>ConfigurationArguments duplicadas
"Found duplicate arguments '{0}' in both public and protected configurationArguments" (Se encontraron argumentos duplicados '{0}' en propiedades configurationArguments públicas y privadas)

Problema: La propiedad ConfigurationArguments en la configuración pública y la propiedad ConfigurationArguments en la configuración protegida contienen propiedades con el mismo nombre.

Solución: Quite una de las propiedades duplicadas.

### <a name="missing-properties"></a>Propiedades que faltan
"Configuration.function requires that configuration.url or configuration.module is specified" (Configuration.function requiere que se especifiquen configuration.url o configuration.module)

"Configuration.url requires that configuration.script is specified" (Configuration.url requiere que se especifique configuration.script)

"Configuration.script requires that configuration.url is specified" (Configuration.script requiere que se especifique configuration.url)

"Configuration.url requires that configuration.function is specified" (Configuration.url requiere que se especifique configuration.function)

"ConfigurationUrlSasToken requires that configuration.url is specified" (ConfigurationUrlSasToken requiere que se especifique configuration.url)

"ConfigurationDataUrlSasToken requires that configurationData.url is specified" (ConfigurationDataUrlSasToken requiere que se especifique configurationData.url)

Problema: Una propiedad definida requiere otra propiedad que falta.

Soluciones: 

* Proporcione la propiedad que falta.
* Quite la propiedad que necesita la propiedad que falta.

## <a name="next-steps"></a>Pasos siguientes
Aprenda sobre DSC y los conjuntos de escalado de máquina virtual en [Using Virtual Machine Scale Sets with the Azure DSC Extension](../../virtual-machine-scale-sets/virtual-machine-scale-sets-dsc.md)

Puede encontrar más información en el artículo sobre [administración segura de credenciales de DSC](extensions-dsc-credentials.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

Para más información sobre el controlador de extensiones DSC de Azure, consulte [Introducción al controlador de extensiones de configuración de estado deseado de Azure](extensions-dsc-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

Para más información sobre DSC de PowerShell, [visite el centro de documentación de PowerShell](https://msdn.microsoft.com/powershell/dsc/overview). 

