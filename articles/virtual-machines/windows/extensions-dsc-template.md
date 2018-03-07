---
title: "Extensión Desired State Configuration con plantillas de Azure Resource Manager | Microsoft Docs"
description: "Obtenga información sobre la definición de la plantilla de Resource Manager para la extensión Desired State Configuration (DSC) en Azure."
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
ms.openlocfilehash: 0f1c53c9eafcd96e49232b75d46ef34537a1160f
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/24/2018
---
# <a name="desired-state-configuration-extension-with-azure-resource-manager-templates"></a>Extensión Desired State Configuration con plantillas de Azure Resource Manager

En este artículo se describe la plantilla de Azure Resource Manager para el [controlador de la extensión Desired State Configuration (DSC)](extensions-dsc-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

> [!NOTE]
> Podría encontrar ejemplos de esquema levemente distintos. El cambio en el esquema ocurrió en la versión de octubre de 2016. Para detalles, consulte [Actualización desde el formato anterior](#update-from-the-previous-format).

## <a name="template-example-for-a-windows-vm"></a>Ejemplo de plantilla para una máquina virtual de Windows

El fragmento de código siguiente corresponde a la sección **Resource** de la plantilla. La extensión DSC hereda las propiedades de extensión predeterminadas. Para más información, consulte la [clase VirtualMachineExtension](https://docs.microsoft.com/en-us/dotnet/api/microsoft.azure.management.compute.models.virtualmachineextension?view=azure-dotnet.).

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

## <a name="template-example-for-windows-virtual-machine-scale-sets"></a>Ejemplo de plantilla para conjuntos de escalado de máquinas virtuales Windows

Un nodo del conjunto de escalado de máquinas virtuales tiene una sección **properties** que tiene un atributo **VirtualMachineProfile, extensionProfile**. En **extensions**, agregue DSC.

La extensión DSC hereda las propiedades de extensión predeterminadas. Para más información, consulte la [clase VirtualMachineScaleSetExtension](https://docs.microsoft.com/en-us/dotnet/api/microsoft.azure.management.compute.models.virtualmachinescalesetextension?view=azure-dotnet).

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

Use el esquema siguiente en la sección **settings** de la extensión DSC de Azure en una plantilla de Resource Manager.

Para una lista de los argumentos disponibles para el script de configuración predeterminada, consulte [Script de configuración predeterminada](#default-configuration-script).

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
| settings.wmfVersion |string |Especifica la versión de Windows Management Framework (WMF) que debe instalarse en la máquina virtual. Si se establece esta propiedad en **latest**, se instala la versión más reciente de WMF. Actualmente, los únicos valores posibles para esta propiedad son **4.0**, **5.0**, **5.0PP** y **latest**. Estos valores posibles están sujetos a actualizaciones. El valor predeterminado es **latest**. |
| settings.configuration.url |string |Especifica la ubicación de la dirección URL desde la que descargar el archivo .zip de la configuración de DSC. Si la dirección URL proporcionada requiere un token de SAS para el acceso, establezca la propiedad **protectedSettings.configurationUrlSasToken** en el valor de su token de SAS. Esta propiedad es necesaria si se definen **settings.configuration.script** o **settings.configuration.function**. Si no se especifica ningún valor para estas propiedades, la extensión llamará al script de configuración predeterminada para establecer los metadatos del administrador de configuración de ubicación (LCM) y se deben proporcionar los argumentos. |
| settings.configuration.script |string |Especifica el nombre de archivo del script que contiene la definición de la configuración de DSC. Este script debe estar en la carpeta raíz del archivo .zip descargado de la dirección URL especificada en la propiedad **configuration.url**. Esta propiedad es necesaria si se definen **settings.configuration.url** o **settings.configuration.script**. Si no se especifica ningún valor para estas propiedades, la extensión llama al script de configuración predeterminada para establecer los metadatos de LCM, y deben proporcionarse los argumentos. |
| settings.configuration.function |string |Especifica el nombre de la configuración de DSC. La configuración con nombre se debe incluir en el script que **configuration.script** define. Esta propiedad es necesaria si se definen **settings.configuration.url** o **settings.configuration.function**. Si no se especifica ningún valor para estas propiedades, la extensión llama al script de configuración predeterminada para establecer los metadatos de LCM, y deben proporcionarse los argumentos. |
| settings.configurationArguments |Colección |Define los parámetros que desea pasar a la configuración de DSC. Esta propiedad no está cifrada. |
| settings.configurationData.url |string |Especifica la dirección URL desde la que descargar el archivo de datos de configuración (.psd1) que se usará como entrada para la configuración de DSC. Si la dirección URL proporcionada requiere un token de SAS para el acceso, establezca la propiedad **protectedSettings.configurationDataUrlSasToken** en el valor de su token de SAS. |
| settings.privacy.dataEnabled |string |Habilita o deshabilita la recopilación de telemetría. Los únicos valores posibles para esta propiedad son **Enable**, **Disable**, **''** o **$null**. Si se deja esta propiedad en blanco o como null, se habilita la telemetría. El valor predeterminado es **''**. Para más información, consulte [Azure DSC extension data collection](https://blogs.msdn.microsoft.com/powershell/2016/02/02/azure-dsc-extension-data-collection-2/) (Colección de datos de la extensión DSC de Azure). |
| settings.advancedOptions.downloadMappings |Colección |Define las ubicaciones alternativas desde las que descargar WMF. Para más información, consulte el artículo sobre la [extensión DSC 2.8 de Azure y cómo asignar las descargas de las dependencias de la extensión a su propia ubicación](http://blogs.msdn.com/b/powershell/archive/2015/10/21/azure-dsc-extension-2-2-amp-how-to-map-downloads-of-the-extension-dependencies-to-your-own-location.aspx). |
| protectedSettings.configurationArguments |Colección |Define los parámetros que desea pasar a la configuración de DSC. Esta propiedad no está cifrada. |
| protectedSettings.configurationUrlSasToken |string |Especifica el token de SAS que se usará para acceder a la dirección URL que **configuration.url** define. Esta propiedad no está cifrada. |
| protectedSettings.configurationDataUrlSasToken |string |Especifica el token de SAS que se usará para acceder a la dirección URL que **configurationData.url** define. Esta propiedad no está cifrada. |

## <a name="default-configuration-script"></a>Script de configuración predeterminada

Para más información sobre los valores siguientes, consulte [Configuración básica del administración de configuración local](https://docs.microsoft.com/en-us/powershell/dsc/metaconfig#basic-settings). Puede usar el script de configuración predeterminada de la extensión DSC para configurar solo las propiedades de LCM que aparecen en la tabla siguiente.

| Nombre de propiedad | type | DESCRIPCIÓN |
| --- | --- | --- |
| settings.configurationArguments.RegistrationKey |securestring |Propiedad obligatoria. Especifica la clave que se usa para que un nodo se registre en el servicio de Azure Automation, como la contraseña de un objeto de credencial de PowerShell. Este valor se puede detectar automáticamente a través del método **listkeys** con la cuenta de Automation. El valor se debe proteger como una configuración protegida. |
| settings.configurationArguments.RegistrationUrl |string |Propiedad obligatoria. Especifique la dirección URL del punto de conexión de Automation donde el nodo intenta registrarse. Este valor se puede detectar automáticamente a través del método **reference** con la cuenta de Automation. |
| settings.configurationArguments.NodeConfigurationName |string |Propiedad obligatoria. Especifica la configuración del nodo en la cuenta de Automation que se asignará al nodo. |
| settings.configurationArguments.ConfigurationMode |string |Especifica el modo de LCM. Entre las opciones válidas se incluyen **ApplyOnly**, **ApplyandMonitor** y **ApplyandAutoCorrect**.  El valor predeterminado es **ApplyandMonitor**. |
| settings.configurationArguments.RefreshFrequencyMins | uint32 | Especifica la frecuencia con que LCM intenta consultar a la cuenta de Automation para comprobar si hay actualizaciones.  El valor predeterminado es **30**.  El valor mínimo es **15**. |
| settings.configurationArguments.ConfigurationModeFrequencyMins | uint32 | Especifica la frecuencia con que el LCM valida la configuración actual. El valor predeterminado es **15**. El valor mínimo es **15**. |
| settings.configurationArguments.RebootNodeIfNeeded | boolean | Especifica si un nodo puede reiniciarse automáticamente si una operación de DSC lo solicita. El valor predeterminado es **false**. |
| settings.configurationArguments.ActionAfterReboot | string | Especifica lo que ocurre tras un reinicio al aplicar una configuración. Las opciones válidas son **ContinueConfiguration** y **StopConfiguration**. El valor predeterminado es **ContinueConfiguration**. |
| settings.configurationArguments.AllowModuleOverwrite | boolean | Especifica si el LCM sobrescribe los módulos existentes en el nodo. El valor predeterminado es **false**. |

## <a name="settings-vs-protectedsettings"></a>Settings frente a ProtectedSettings

Todas las configuraciones se guardan en un archivo de texto de configuración en la máquina virtual. Las propiedades que aparece en **settings** son propiedades públicas. Las propiedades públicas no están cifradas en el archivo de texto de configuración. Las propiedades que aparecen en **protectedSettings** se cifran con un certificado y no aparecen en texto sin formato en el archivo de configuración de la máquina virtual.

Si la configuración necesita credenciales, puede incluirlas en **protectedSettings**:

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

## <a name="example-configuration-script"></a>Script de configuración de ejemplo

En el ejemplo siguiente se muestra el comportamiento predeterminado de la extensión DSC, que consiste en proporcionar la configuración de los metadatos al LCM y registrarse en el servicio de DSC de Automatización. Los argumentos de configuración son obligatorios.  Los argumentos de configuración se transfieren al script de configuración predeterminada para establecer los metadatos del LCM.

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

## <a name="example-using-the-configuration-script-in-azure-storage"></a>Ejemplo con el script de configuración en Azure Storage

El ejemplo siguiente proviene de la [información general del controlador de la extensión DSC](extensions-dsc-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Este ejemplo usa plantillas de Resource Manager en lugar de cmdlets para implementar la extensión. Guarde la configuración IisInstall.ps1, colóquela en un archivo .zip y cargue el archivo en una dirección URL accesible. Este ejemplo usa Azure Blob Storage, pero puede descargar los archivos .zip desde cualquier ubicación arbitraria.

En la plantilla de Resource Manager, el código siguiente indica a la máquina virtual que descargue el archivo correcto y, luego, ejecute la función de PowerShell adecuada:

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

## <a name="update-from-a-previous-format"></a>Actualización desde un formato anterior

Cualquier configuración en el formato anterior de la extensión (y que tenga las propiedades públicas **ModulesUrl**, **ConfigurationFunction**, **SasToken** o **Properties**) se adapta automáticamente al formato actual de la extensión. Se ejecuta tal como lo hacía antes.

El esquema siguiente muestra el aspecto que tenía el esquema de configuración anterior:

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
| settings.configuration.script |Primera parte de settings.ConfigurationFunction (antes de \\\\) |
| settings.configuration.function |Segunda parte de settings.ConfigurationFunction (después de \\\\) |
| settings.configurationArguments |settings.Properties |
| settings.configurationData.url |protectedSettings.DataBlobUri (sin token de SAS) |
| settings.privacy.dataEnabled |settings.privacy.dataEnabled |
| settings.advancedOptions.downloadMappings |settings.advancedOptions.downloadMappings |
| protectedSettings.configurationArguments |protectedSettings.Properties |
| protectedSettings.configurationUrlSasToken |settings.SasToken |
| protectedSettings.configurationDataUrlSasToken |Token de SAS de protectedSettings.DataBlobUri |

## <a name="troubleshooting---error-code-1100"></a>Solución de problemas: código de error 1100

El código de error 1100 indica un problema con la entrada del usuario a la extensión DSC. El texto de estos errores varía y puede cambiar. Esto son algunos de los errores que pueden surgir y cómo puede corregirlos.

### <a name="invalid-values"></a>Valores no válidos

"Privacy.dataCollection is '{0}'.
The only possible values are '', 'Enable', and 'Disable'" (Privacy.dataCollection es '{0}'. Los únicos valores posibles son ", "Enable" y "Disable").
"WmfVersion es '{0}'.
Los únicos valores posibles son: and "latest" (WmfVersion es '{0}'. Los únicos valores posibles son... y "latest").

**Problema**: Se proporcionó un valor no permitido.

**Solución**: Cambie el valor no válido a un valor válido. Para más información, consulte la tabla que aparece en [Detalles](#details).

### <a name="invalid-url"></a>Dirección URL no válida

"ConfigurationData.url is '{0}'. This is not a valid URL" (ConfigurationData.url es '{0}'. No es una dirección URL válida) "DataBlobUri is '{0}'. This is not a valid URL" (DataBlobUri es '{0}'. No es una dirección URL válida) "Configuration.url is '{0}'. This is not a valid URL" This is not a valid URL" (Configuration.url es '{0}'. No es una dirección URL válida)

**Problema**: Se proporcionó una dirección URL no válida.

**Solución**: Revise todas las direcciones URL proporcionadas. Asegúrese de que todas las direcciones URL se resuelvan en ubicaciones válidas a las que la extensión pueda acceder en la máquina remota.

### <a name="invalid-configurationargument-type"></a>Tipo de ConfigurationArgument no válido

"Invalid configurationArguments type {0}" (Tipo de configurationArguments no válido {0})

**Problema**: La propiedad *ConfigurationArguments* no se puede resolver en un objeto **Hashtable**.

**Solución**: Convierta la propiedad *ConfigurationArguments* en **Hashtable**. Siga el formato proporcionado en el ejemplo anterior. Esté atento a las comillas, comas y llaves.

### <a name="duplicate-configurationarguments"></a>ConfigurationArguments duplicadas

"Found duplicate arguments '{0}' in both public and protected configurationArguments" (Se encontraron argumentos duplicados '{0}' en propiedades configurationArguments públicas y privadas)

**Problema**: La propiedad *ConfigurationArguments* en la configuración pública y la propiedad *ConfigurationArguments* en la configuración protegida contienen propiedades con el mismo nombre.

**Solución**: Quite una de las propiedades duplicadas.

### <a name="missing-properties"></a>Propiedades que faltan
"Configuration.function requires that configuration.url or configuration.module is specified" (Configuration.function requiere que se especifiquen configuration.url o configuration.module)

"Configuration.url requires that configuration.script is specified" (Configuration.url requiere que se especifique configuration.script)

"Configuration.script requires that configuration.url is specified" (Configuration.script requiere que se especifique configuration.url)

"Configuration.url requires that configuration.function is specified" (Configuration.url requiere que se especifique configuration.function)

"ConfigurationUrlSasToken requires that configuration.url is specified" (ConfigurationUrlSasToken requiere que se especifique configuration.url)

"ConfigurationDataUrlSasToken requires that configurationData.url is specified" (ConfigurationDataUrlSasToken requiere que se especifique configurationData.url)

**Problema**: Una propiedad definida requiere otra propiedad que falta.

**Soluciones**:

- Proporcione la propiedad que falta.
- Quite la propiedad que necesita la propiedad que falta.

## <a name="next-steps"></a>pasos siguientes

* Obtenga información sobre el [uso de los conjuntos de escalado de máquinas virtuales con la extensión DSC de Azure](../../virtual-machine-scale-sets/virtual-machine-scale-sets-dsc.md).
* Encuentre más detalles sobre la [administración segura de credenciales de DSC](extensions-dsc-credentials.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Obtenga una [introducción al controlador de la extensión DSC de Azure](extensions-dsc-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Para obtener más información sobre DSC de PowerShell, vaya al [centro de documentación de PowerShell](https://msdn.microsoft.com/powershell/dsc/overview).
