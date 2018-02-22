---
title: "Extensión Desired State Configuration con plantillas de Azure Resource Manager | Microsoft Docs"
description: "Definición de la plantilla de Resource Manager para la extensión Desired State Configuration en Azure"
services: virtual-machines-windows
documentationcenter: 
author: mgreenegit
manager: timlt
editor: 
tags: azure-resource-manager
keywords: dsc
ms.assetid: b5402e5a-1768-4075-8c19-b7f7402687af
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: na
ms.date: 02/02/2018
ms.author: migreene
ms.openlocfilehash: f638d1530541526316f6e409f1efd44f136992a5
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/09/2018
---
# <a name="desired-state-configuration-extension-with-azure-resource-manager-templates"></a>Extensión Desired State Configuration con plantillas de Azure Resource Manager

En este artículo, se describe la plantilla de Azure Resource Manager para el [controlador de la extensión Desired State Configuration](extensions-dsc-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

*Nota: Puede que encuentre ejemplos de esquemas ligeramente diferentes.*
*El cambio de esquema se produjo en la publicación de octubre de 2016.*
*Los detalles se indican en la sección de esta página titulada*
*[Actualización del formato anterior](##Updating-from-the-Previous-Format)*.

## <a name="template-example-for-a-windows-vm"></a>Ejemplo de plantilla para una máquina virtual de Windows

El siguiente fragmento corresponde a la sección Resource de la plantilla.
La extensión DSC hereda las propiedades de extensión predeterminadas, como se documenta en [VirtualMachineExtension (clase)](https://docs.microsoft.com/en-us/dotnet/api/microsoft.azure.management.compute.models.virtualmachineextension?view=azure-dotnet.).

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
                  "typeHandlerVersion": "2.72",
                  "autoUpgradeMinorVersion": true,
                  "forceUpdateTag": "[parameters('dscExtensionUpdateTagVersion')]",
                  "settings": {
                    "configurationArguments": {
                        {
                            "Name": "RegistrationKey",
                            "Value": {
                                "UserName": "PLACEHOLDER_DONOTUSE",
                                "Password": "PrivateSettingsRef:registrationKeyPrivate"
                            },
                        },
                        "RegistrationUrl" : "[parameters('registrationUrl1')]",
                        "NodeConfigurationName" : "nodeConfigurationNameValue1"
                        }
                        },
                        "protectedSettings": {
                            "Items": {
                                        "registrationKeyPrivate": "[parameters('registrationKey1']"
                                    }
                        }
                    }
```

## <a name="template-example-for-windows-vmss"></a>Ejemplo de plantilla para VMSS de Windows

Un nodo de VMSS tiene una sección "properties" con el atributo "extensionProfile" de "VirtualMachineProfile". DSC se agrega en "extensions".

La extensión DSC hereda las propiedades de extensión predeterminadas, como se documenta en [VirtualMachineScaleSetExtension (clase)](https://docs.microsoft.com/en-us/dotnet/api/microsoft.azure.management.compute.models.virtualmachinescalesetextension?view=azure-dotnet).

```json
"extensionProfile": {
            "extensions": [
                {
                    "name": "Microsoft.Powershell.DSC",
                    "properties": {
                        "publisher": "Microsoft.Powershell",
                        "type": "DSC",
                        "typeHandlerVersion": "2.72",
                        "autoUpgradeMinorVersion": true,
                        "forceUpdateTag": "[parameters('DscExtensionUpdateTagVersion')]",
                        "settings": {
                            "configurationArguments": {
                                {
                                    "Name": "RegistrationKey",
                                    "Value": {
                                        "UserName": "PLACEHOLDER_DONOTUSE",
                                        "Password": "PrivateSettingsRef:registrationKeyPrivate"
                                    },
                                },
                                "RegistrationUrl" : "[parameters('registrationUrl1')]",
                                "NodeConfigurationName" : "nodeConfigurationNameValue1"
                        }
                        },
                        "protectedSettings": {
                            "Items": {
                                        "registrationKeyPrivate": "[parameters('registrationKey1']"
                                    }
                        }
                    }
                ]
            }
        }
```

## <a name="detailed-settings-information"></a>Información de configuración detallada

Este es el esquema para la parte de configuración (settings) de la extensión DSC de Azure en una plantilla de Azure Resource Manager.

*Para obtener una lista de los argumentos disponibles para el script de configuración predeterminada,*
*consulte la sección siguiente denominada*
*[Script de configuración predeterminada](##Default-Configuration-Script)*.

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

| Nombre de propiedad | type | DESCRIPCIÓN |
| --- | --- | --- |
| settings.wmfVersion |cadena |Especifica la versión de Windows Management Framework que debe instalarse en la máquina virtual. Si se establece esta propiedad en "latest", se instala la versión más reciente de WMF. Los únicos valores posibles actuales para esta propiedad son **"4.0", "5.0", "5.0PP" y "latest"**. Estos valores posibles están sujetos a actualizaciones. El valor predeterminado es "latest". |
| settings.configuration.url |cadena |Especifica la ubicación de la dirección URL desde la que descargar el archivo zip de la configuración de DSC. Si la dirección URL proporcionada requiere un token de SAS para el acceso, debe establecer la propiedad protectedSettings.configurationUrlSasToken en el valor de su token de SAS. Esta propiedad es necesaria si se definen settings.configuration.script o settings.configuration.function. Si no se especifica ningún valor para estas propiedades, la extensión llamará al script de configuración predeterminada para establecer los metadatos del administrador de configuración local (LCM), y deben proporcionarse los argumentos. |
| settings.configuration.script |cadena |Especifica el nombre de archivo del script que contiene la definición de la configuración de DSC. Este script debe estar en la carpeta raíz del archivo zip descargado de la dirección URL especificada en la propiedad configuration.url. Esta propiedad es necesaria si se definen settings.configuration.url o settings.configuration.script. Si no se especifica ningún valor para estas propiedades, la extensión llamará al script de configuración predeterminada para establecer los metadatos de LCM, y deben aplicarse los argumentos. |
| settings.configuration.function |cadena |Especifica el nombre de la configuración de DSC. La configuración con nombre debe incluirse en el script definido en configuration.script. Esta propiedad es necesaria si se definen settings.configuration.url o settings.configuration.function. Si no se especifica ningún valor para estas propiedades, la extensión llamará al script de configuración predeterminada para establecer los metadatos del administrador de configuración local (LCM), y deben proporcionarse los argumentos. |
| settings.configurationArguments |Colección |Define los parámetros que desea pasar a la configuración de DSC. Esta propiedad no está cifrada. |
| settings.configurationData.url |cadena |Especifica la dirección URL desde la que descargar el archivo de datos de configuración (.psd1) que se usará como entrada para la configuración de DSC. Si la dirección URL proporcionada requiere un token de SAS para el acceso, debe establecer la propiedad protectedSettings.configurationDataUrlSasToken en el valor de su token de SAS. |
| settings.privacy.dataEnabled |cadena |Habilita o deshabilita la recopilación de telemetría. Los únicos valores posibles para esta propiedad son **"Enable", "Disable", '' o $null**. Si se deja esta propiedad en blanco o como null, se habilita la telemetría. El valor predeterminado es ". [Más información](https://blogs.msdn.microsoft.com/powershell/2016/02/02/azure-dsc-extension-data-collection-2/) |
| settings.advancedOptions.downloadMappings |Colección |Define las ubicaciones alternativas desde las que descargar WMF. [Más información](http://blogs.msdn.com/b/powershell/archive/2015/10/21/azure-dsc-extension-2-2-amp-how-to-map-downloads-of-the-extension-dependencies-to-your-own-location.aspx) |
| protectedSettings.configurationArguments |Colección |Define los parámetros que desea pasar a la configuración de DSC. Esta propiedad no está cifrada. |
| protectedSettings.configurationUrlSasToken |cadena |Especifica el token de SAS para acceder a la dirección URL definida en configuration.url. Esta propiedad no está cifrada. |
| protectedSettings.configurationDataUrlSasToken |cadena |Especifica el token de SAS para acceder a la dirección URL definida en configurationData.url. Esta propiedad no está cifrada. |

## <a name="default-configuration-script"></a>Script de configuración predeterminada

Para obtener más información sobre estos valores, consulte la página de documentación [Configuración básica del administrador de configuración local](https://docs.microsoft.com/en-us/powershell/dsc/metaconfig#basic-settings).
Solo las propiedades del LCM en la tabla siguiente son configurables mediante el script de configuración predeterminada de la extensión DSC.

| Nombre de propiedad | type | DESCRIPCIÓN |
| --- | --- | --- |
| settings.configurationArguments.RegistrationKey |securestring |Propiedad obligatoria. Especifica la clave usada para que un nodo se registre en el servicio de Azure Automation, como la contraseña de un objeto de credencial de PowerShell. Este valor puede detectarse automáticamente mediante el método listkeys con la cuenta de Automation y debe establecerse como configuración protegida. |
| settings.configurationArguments.RegistrationUrl |cadena |Propiedad obligatoria. Especifica la dirección URL del punto de conexión de Azure Automation donde el nodo intentará registrarse. Este valor se puede detectar automáticamente mediante el método reference con la cuenta de Automation. |
| settings.configurationArguments.NodeConfigurationName |cadena |Propiedad obligatoria. Especifica la configuración del nodo en la cuenta de Azure Automation que se asignará al nodo. |
| settings.configurationArguments.ConfigurationMode |cadena |Especifica el modo del administrador de configuración local. Entre las opciones válidas se incluyen "ApplyOnly", "ApplyandMonitior" y "ApplyandAutoCorrect".  El valor predeterminado es "ApplyandMonitor". |
| settings.configurationArguments.RefreshFrequencyMins | uint32 | Especifica la frecuencia con que el LCM intentará consultar a la cuenta de Automation para comprobar si hay actualizaciones.  El valor predeterminado es 30.  El valor mínimo es 15. |
| settings.configurationArguments.ConfigurationModeFrequencyMins | uint32 | Especifica la frecuencia con que el LCM validará la configuración actual.  El valor predeterminado es 15.  El valor mínimo es 15. |
| settings.configurationArguments.RebootNodeIfNeeded | boolean | Especifica si un nodo puede reiniciarse automáticamente si una operación de DSC lo solicita.  El valor predeterminado es False. |
| settings.configurationArguments.ActionAfterReboot | cadena | Especifica lo que ocurre tras un reinicio al aplicar una configuración. Las opciones válidas son "ContinueConfiguration" y "StopConfiguration". El valor predeterminado es "ContinueConfiguration". |
| settings.configurationArguments.AllowModuleOverwrite | boolean | Especifica si el LCM sobrescribirá los módulos existentes en el nodo.  El valor predeterminado es False. |

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

En el ejemplo siguiente se muestra el comportamiento predeterminado para la extensión DSC, que consiste en proporcionar la configuración de los metadatos al administrador de configuración local y registrarse en el servicio de DSC de Azure Automation.
Los argumentos de configuración son obligatorios y se transferirán al script de configuración predeterminada para establecer los metadatos del LCM.

```json
"settings": {
    "configurationArguments": {
        {
            "Name": "RegistrationKey",
            "Value": {
                "UserName": "PLACEHOLDER_DONOTUSE",
                "Password": "PrivateSettingsRef:registrationKeyPrivate"
            },
        },
        "RegistrationUrl" : "[parameters('registrationUrl1')]",
        "NodeConfigurationName" : "nodeConfigurationNameValue1"
  }
},
"protectedSettings": {
    "Items": {
                "registrationKeyPrivate": "[parameters('registrationKey1']"
            }
}
```

## <a name="example-using-configuration-script-in-azure-storage"></a>Ejemplo con el script de configuración en Azure Storage

El ejemplo siguiente se deriva de la sección "Introducción" de la [página de introducción al controlador de extensiones DSC](extensions-dsc-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
Este ejemplo usa plantillas de Resource Manager en lugar de cmdlets para implementar la extensión.
Guardar la configuración "IisInstall.ps1", colóquela en un archivo .ZIP y cargue el archivo en una dirección URL accesible.
Este ejemplo usa el almacenamiento de blobs de Azure, pero es posible descargar archivos .ZIP desde cualquier ubicación.

En la plantilla de Azure Resource Manager, el siguiente código indica a la máquina virtual que descargue el archivo correcto y ejecute la función de PowerShell adecuada:

```json
"settings": {
    "configuration": {
        "url": "https://demo.blob.core.windows.net/",
        "script": "IisInstall.ps1",
        "function": "IISInstall"
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

"Privacy.dataCollection is '{0}'.
The only possible values are '', 'Enable', and 'Disable'" (Privacy.dataCollection es '{0}'. Los únicos valores posibles son ", "Enable" y "Disable").
"WmfVersion es '{0}'.
Los únicos valores posibles son: and "latest" (WmfVersion es '{0}'. Los únicos valores posibles son... y "latest").

Problema: No se permite un valor proporcionado.

Solución: Cambie el valor no válido a uno válido.
Consulte la tabla en la sección Detalles.

### <a name="invalid-url"></a>Dirección URL no válida

"ConfigurationData.url is '{0}'. This is not a valid URL" (ConfigurationData.url es '{0}'. No es una dirección URL válida) "DataBlobUri is '{0}'. This is not a valid URL" (DataBlobUri es '{0}'. No es una dirección URL válida) "Configuration.url is '{0}'. This is not a valid URL" This is not a valid URL" (Configuration.url es '{0}'. No es una dirección URL válida)

Problema: Se ha proporcionado una dirección URL que no es válida.

Solución: Compruebe todas las direcciones URL proporcionadas.
Asegúrese de que todas las direcciones URL se resuelvan en ubicaciones válidas a las que la extensión pueda acceder en la máquina remota.

### <a name="invalid-configurationargument-type"></a>Tipo de ConfigurationArguments no válido

"Invalid configurationArguments type {0}" (Tipo de configurationArguments no válido {0})

Problema: La propiedad ConfigurationArguments no se puede resolver en un objeto Hashtable.

Solución: Convierta la propiedad ConfigurationArguments en Hashtable.
Siga el formato proporcionado en el ejemplo anterior.
Esté atento a las comillas, comas y llaves.

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

- Proporcione la propiedad que falta.
- Quite la propiedad que necesita la propiedad que falta.

## <a name="next-steps"></a>Pasos siguientes

Aprenda sobre DSC y los conjuntos de escalado de máquina virtual en [Uso de conjuntos de escalado de máquinas virtuales con la extensión de DSC de Azure](../../virtual-machine-scale-sets/virtual-machine-scale-sets-dsc.md).

Puede encontrar más información en el artículo sobre [administración segura de credenciales de DSC](extensions-dsc-credentials.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Para más información sobre el controlador de extensiones DSC de Azure, consulte [Introducción al controlador de extensiones de configuración de estado deseado de Azure](extensions-dsc-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Para más información sobre DSC de PowerShell, [visite el centro de documentación de PowerShell](https://msdn.microsoft.com/powershell/dsc/overview).
