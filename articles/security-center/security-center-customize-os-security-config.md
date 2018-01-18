---
title: "Personalización de las configuraciones de seguridad del sistema operativo en Azure Security Center [versión preliminar] | Microsoft Docs"
description: "En este artículo se muestra cómo personalizar las evaluaciones del centro de seguridad."
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/08/2018
ms.author: terrylan
ms.openlocfilehash: 2fa63515d290e6700fbe4a90ae509f4635b19f29
ms.sourcegitcommit: 719dd33d18cc25c719572cd67e4e6bce29b1d6e7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/08/2018
---
# <a name="customizing-os-security-configurations-in-azure-security-center-preview"></a>Personalización de las configuraciones de seguridad del sistema operativo en Azure Security Center [versión preliminar]

Aprenda a personalizar las evaluaciones de configuración de seguridad del sistema operativo en Azure Security Center mediante este tutorial.

## <a name="what-are-os-security-configurations"></a>¿Cuáles son las configuraciones de seguridad del sistema operativo?

Azure Security Center supervisa las configuraciones de seguridad mediante un conjunto de más de 150 reglas recomendadas para proteger el sistema operativo, incluidas, entre otras, las relacionadas con firewalls, auditoría y directivas de contraseña. Si se detecta que la configuración de una maquina es vulnerable, se genera una recomendación de seguridad.

La personalización de las reglas puede ayudar a las organizaciones a controlar qué opciones de configuración son las más adecuadas para su entorno. Esta característica permite a los usuarios establecer una directiva personalizada de evaluación y aplicarla a todas las máquinas aplicables de la suscripción.

> [!NOTE]
> - En este momento, la personalización de la configuración de seguridad del sistema operativo está disponible únicamente para Windows Server 2008, 2008R2, 2012 y 2012R2.
- La configuración se aplica a todas las máquinas virtuales y equipos conectados a todas las áreas de trabajo en la suscripción seleccionada.
- La personalización de la configuración de seguridad del sistema operativo está disponible únicamente en el nivel estándar de Security Center.
>
>

¿Cómo personalizar las reglas de configuración de seguridad del sistema operativo?

Para personalizar las reglas de configuración de seguridad del sistema operativo, puede habilitar y deshabilitar una regla determinada, cambiar el valor deseado en una regla existente y agregar nuevas reglas basadas en los tipos de reglas admitidos (Registro, directiva de auditoría y directiva de seguridad). Actualmente, el valor deseado debe ser un valor exacto.

Las nuevas reglas deben tener el mismo formato y estructura que otras reglas existentes del mismo tipo.

> [!NOTE]
> Para personalizar las configuraciones de seguridad del sistema operativo, debe tener asignado el rol de propietario de la suscripción, colaborador de la suscripción o administrador de seguridad.
>
>

## <a name="customize-security-configuration"></a>Personalización de la configuración de seguridad

Para personalizar la configuración de seguridad predeterminada del sistema operativo en Security Center, siga estos pasos:

1.  Abra el panel **Security Center**.

2.  En el menú principal de Security Center, seleccione **Directiva de seguridad**.  Se abre la página **Security Center - Directiva de seguridad**.

3.  Seleccione la suscripción para la que desea realizar la personalización.

    ![](media/security-center-customize-os-security-config/open-security-policy.png)

4. En **Componentes de la directiva**, seleccione **Edit security configurations** (Editar configuraciones de seguridad).

6.  Se abre la página **Edit security configurations** (Editar configuraciones de seguridad). Siga los pasos que se resaltan en la pantalla para descargar, editar y cargar el archivo modificado.

    ![](media/security-center-customize-os-security-config/blade.png)

  > [!NOTE]
  > De forma predeterminada, el archivo de configuración que descarga está en formato *json*. Para obtener instrucciones sobre cómo modificar este archivo, vaya a [Personalización del archivo de configuración](#customize-the-configuration-file).
  >

7. Después de guardar correctamente el archivo, la configuración se aplica a todas las máquinas virtuales y equipos conectados a todas las áreas de trabajo en la suscripción seleccionada. Este proceso puede tardar algún tiempo, normalmente unos minutos, pero puede prolongarse ya que depende del tamaño de la infraestructura. Seleccione **Guardar** para confirmar el cambio, ya que, si no, la directiva no se almacena.

    ![](media/security-center-customize-os-security-config/save-successfully.png)

En cualquier momento, puede restablecer la configuración actual de la directiva al estado predeterminado; para ello, seleccione la opción **Restablecer** en **Edición de reglas de configuración de la seguridad del SO**. Si elige esta opción, recibirá la siguiente alerta emergente. Seleccione **Sí** para confirmar la acción.

![](media/security-center-customize-os-security-config/edit-alert.png)

## <a name="customize-the-configuration-file"></a>Personalización del archivo de configuración

En el archivo de personalización, cada versión de sistema operativo admitida tiene un conjunto de reglas. Cada conjunto de reglas tiene su propio nombre e identificador únicos, como se muestra en el ejemplo siguiente:

![](media/security-center-customize-os-security-config/custom-file.png)

> [!NOTE]
> Este archivo se ha editado con Visual Studio, pero también puede usar Bloc de notas siempre que tenga instalado el complemento de visor de JSON.
>
>

Al editar este archivo, puede modificar una regla o todas ellas. Cada conjunto de reglas incluye una sección de *reglas* que contiene las reglas, divididas en tres categorías: Registro, Directiva de auditoría y Directiva de seguridad, como se muestra a continuación:

![](media/security-center-customize-os-security-config/rules-section.png)

Cada categoría tiene su propio conjunto de atributos. En las reglas existentes, puede realizar cambios en los siguientes atributos:

- expectedValue: el tipo de datos de campo de este atributo debe coincidir con los valores admitidos por cada tipo de regla, por ejemplo:

  - baselineRegistryRules: el valor debe coincidir con el valor de [regValueType](https://msdn.microsoft.com/library/windows/desktop/ms724884(v=vs.85) definido en esa regla.

  - baselineAuditPolicyRules: el valor debe ser una cadena, una de las siguientes:

    - Aciertos y errores

    - Correcto

  - baselineSecurityPolicyRules: el valor debe ser un valor de cadena, uno de los siguientes:

    - "No one"

    - Lista de grupos de usuario admitidos, por ejemplo: “Administrators, Backup Operators”

-   state: cadena que puede contener las opciones “Disabled” o “Enabled”. En esta versión preliminar privada, la cadena distingue mayúsculas y minúsculas.

Estos son los únicos campos que se pueden configurar. Si se infringe el formato o el tamaño de archivo, no se podrá guardar el cambio. El siguiente mensaje de error se produce cuando no se puede procesar el archivo:

![](media/security-center-customize-os-security-config/invalid-json.png)

Consulte [Códigos de error](#error-codes) para obtener la lista de posibles errores.

A continuación, encontrará ejemplos de estas reglas y los atributos (en negrita) que se pueden cambiar:

**Sección de reglas:** baselineRegistryRules
```
{

    "hive": "LocalMachine",
    "regValueType": "Int",
    "keyPath":
    "System\\\\CurrentControlSet\\\\Services\\\\LanManServer\\\\Parameters",
    "valueName": "restrictnullsessaccess",
    "ruleId": "f9020046-6340-451d-9548-3c45d765d06d",
    "originalId": "0f319931-aa36-4313-9320-86311c0fa623",
    "cceId": "CCE-10940-5",
    "ruleName": "Network access: Restrict anonymous access to Named Pipes and
    Shares",
    "ruleType": "Registry",
    "**expectedValue**": "1",
    "severity": "Warning",
    "analyzeOperation": "Equals",
    "source": "Microsoft",
    "**state**": "Disabled"

}
```

**Sección de reglas:** baselineAuditPolicyRules
```
{
"auditPolicyId": "0cce923a-69ae-11d9-bed3-505054503030",
"ruleId": "37745508-95fb-44ec-ab0f-644ec0b16995",
"originalId": "2ea0de1a-c71d-46c8-8350-a7dd4d447895",
"cceId": "CCE-11001-5",
"ruleName": "Audit Policy: Account Management: Other Account Management Events",
"ruleType": "AuditPolicy",
"**expectedValue**": "Success and Failure",
"severity": "Critical",
"analyzeOperation": "Equals",
"source": "Microsoft",
"**state**": "Enabled"
},
```

**Secciones de reglas:** baselineSecurityPolicyRules
```
{
"sectionName": "Privilege Rights",
"settingName": "SeIncreaseWorkingSetPrivilege",
"ruleId": "b0ec9d5e-916f-4356-83aa-c23522102b33",
"originalId": "b61bd492-74b0-40f3-909d-36b9bf54e94c",
"cceId": "CCE-10548-6",
"ruleName": "Increase a process working set",
"ruleType": "SecurityPolicy",
"**expectedValue**": "Administrators, Local Service",
"severity": "Warning",
"analyzeOperation": "Equals",
"source": "Microsoft", "**state**": "Enabled"
},
```

Hay algunas reglas que se repiten para los distintos tipos de sistema operativo. Las reglas duplicadas tienen el mismo valor de "originalId".

## <a name="adding-a-new-custom-rule"></a>Adición de una nueva regla personalizada

También puede crear una nueva regla. Antes de hacerlo, tenga en cuenta las siguientes restricciones:

-   La versión de esquema, *baselineId* y *baselineName* no se puede cambiar.

-   No se puede quitar el conjunto de reglas.

-   No se puede agregar el conjunto de reglas.

-   El número máximo de reglas permitidas (incluidas las reglas predeterminadas) es 1000 reglas.

Las nuevas reglas personalizadas se marcan con un nuevo origen personalizado (! = "Microsoft"). El campo *ruleId* puede ser nulo o estar vacío. Si está vacío, Microsoft genera uno. Si no está vacío, debe tener un GUID válido único entre todas las reglas (predeterminado y personalizado). Revise a continuación las restricciones con respecto a los campos principales:

-   *originalId*: puede ser nulo o estar vacío. Si *originalId* no está vacío, debe ser un GUID válido.

-   *cceId*: puede ser nulo o estar vacío. Si *cceId* es no está vacío, debe ser único.

-   *ruleType*: Registry, AuditPolicy o SecurityPolicy.

-   *Severity*: Unknown, Critical, Warning o Informational.

-   *analyzeOperation*: debe ser Equals.

-   *auditPolicyId*: debe ser un GUID válido.

-   *regValueType*: debe ser Int, Long, String o MultipleString.

> [!NOTE]
> Hive debe ser *LocalMachine*.
>
>

Ejemplo de una nueva regla personalizada:

**Registro**:
```
    {
    "hive": "LocalMachine",
    "regValueType": "Int",
    "keyPath":
    "System\\\\CurrentControlSet\\\\Services\\\\Netlogon\\\\MyKeyName",
    "valueName": "MyValueName",
    "originalId": "",
    "cceId": "",
    "ruleName": "My new registry rule”, "baselineRuleType": "Registry",
    "expectedValue": "123", "severity": "Critical",
    "analyzeOperation": "Equals",
    "source": "MyCustomSource",
    "state": "Enabled"
   }
```
**Directiva de seguridad**:
```
{

   "sectionName": "Privilege Rights",
   "settingName": "SeDenyBatchLogonRight",
   "originalId": "",
   "cceId": "",
   "ruleName": "My new security policy rule", "baselineRuleType":
   "SecurityPolicy",
   "expectedValue": "Guests",
   "severity": "Critical",
   "analyzeOperation": "Equals", "source": " MyCustomSource ",
   "state": "Enabled"
   }
```
**Directiva de auditoría**
```
   {
   "auditPolicyId": "0cce923a-69ae-11d9-bed3-505054503030",
   "originalId": "",
   "cceId": "",
   "ruleName": " My new audit policy rule ", "baselineRuleType": "AuditPolicy",
   "expectedValue": " Failure",
   "severity": "Critical",
   "analyzeOperation": "Equals", "source": " MyCustomSource ",
   "state": "Enabled"
   }
```

## <a name="file-upload-failures"></a>Errores de carga de archivos

Si el archivo de configuración enviado no es válido debido a errores en los valores o el formato, se muestra un error. Puede descargar un informe detallado de errores en formato csv para solucionar y corregir los errores antes de volver a enviar un archivo de configuración corregido.

![](media/security-center-customize-os-security-config/invalid-configuration.png)

Ejemplo del archivo de errores:

![](media/security-center-customize-os-security-config/errors-file.png)

## <a name="error-codes"></a>Códigos de error

La lista siguiente tiene todos los errores posibles:

| **Error**                                | **Descripción**                                                                                                                              |
|------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------|
| BaselineConfiguratiohSchemaVersionError  | Se encontró que la propiedad "schemaVersion" no era válida o estaba vacía. El valor se debe establecer en "{0}".                                                         |
| BaselineInvalidStringError               | La propiedad "{0}" no puede contener '\\n'.                                                                                                         |
| BaselineNullRuleError                    | La lista de reglas de configuración de línea de base contiene una regla con el valor "null".                                                                         |
| BaselineRuleCceIdNotUniqueError          | CCE-ID "{0}" no es único.                                                                                                                  |
| BaselineRuleEmptyProperty                | La propiedad: "{0}" falta o no es válida.                                                                                                       |
| BaselineRuleIdNotInDefault               | La regla tiene una propiedad de origen "Microsoft" pero no se encontró en el conjunto de reglas predeterminado de Microsoft.                                                   |
| BaselineRuleIdNotUniqueError             | El identificador de regla no es único.                                                                                                                       |
| BaselineRuleInvalidGuid                  | Se encontró que la propiedad "{0}" no era válida. El valor' no es un GUID válido.                                                                             |
| BaselineRuleInvalidHive                  | Hive debe ser LocalMachine.                                                                                                                   |
| BaselineRuleNameNotUniqueError           | El nombre de la regla no es único.                                                                                                                 |
| BaselineRuleNotExistInConfigError        | No se encontró la regla en la nueva configuración. No se puede eliminar la regla.                                                                     |
| BaselineRuleNotFoundError                | La regla no se encontró en conjunto de reglas de línea de base predeterminado.                                                                                        |
| BaselineRuleNotInPlace                   | La regla coincide con una regla predeterminada con el tipo {0} y aparece en la lista de {1}.                                                                       |
| BaselineRulePropertyTooLong              | La propiedad "{0}" es demasiado larga. Longitud máxima permitida: {1}.                                                                                        |
| BaselineRuleRegTypeInvalidError          | El valor esperado "{0}" no coincide con el tipo de valor del Registro que se define.                                                              |
| BaselineRulesetAdded                     | Se encontró un conjunto de reglas con el identificador "{0}" en la configuración predeterminada. No se puede agregar el conjunto de reglas.                                               |
| BaselineRulesetIdMustBeUnique            | El conjunto de reglas de línea de base especificado "{0}" debe ser único.                                                                                           |
| BaselineRulesetNotFound                  | No se encontró el conjunto de reglas con el identificador "{0}" y el nombre "{1}" en la configuración dada. No se puede eliminar el conjunto de reglas.                                |
| BaselineRuleSourceNotMatch               | Ya hay una regla definida con el identificador "{0}".                                                                                                       |
| BaselineRuleTypeDoesntMatch              | El tipo de regla predeterminado es "{0}".                                                                                                              |
| BaselineRuleTypeDoesntMatchError         | El tipo real de la regla es: {0}, pero la propiedad ruleType es: {1}.                                                                          |
| BaselineRuleUnpermittedChangesError      | Solo se permite cambiar las propiedades "expectedValue" y "state".                                                                       |
| BaselineTooManyRules                     | El número máximo permitido de reglas personalizadas es {0} reglas. La configuración dada contiene reglas de {1}. ({2} reglas predeterminadas + {3} reglas personalizadas. |
| ErrorNoConfigurationStatus               | No se encontró ningún estado de configuración. Defina el estado de configuración deseado: "Default" o "Custom".                                    |
| ErrorNonEmptyRulesetOnDefault            | El estado de configuración está establecido como predeterminado. La lista BaselineRulesets debe ser null o estar vacía.                                                          |
| ErrorNullRulesetsPropertyOnCustom        | El estado de configuración proporcionado es "Custom", pero la propiedad "baselineRulesets" es null o está vacía.                                             |
| ErrorParsingBaselineConfig               | La configuración dada no es válida. Uno o varios de los valores definidos tienen un valor null o un tipo no válido.                                  |
| ErrorParsingIsDefaultProperty            | El valor dado de "configurationStatus" "{0}" no es válido. El valor puede ser solo "Default" o "Custom".                                         |
| InCompatibleViewVersion                  | Versión de visualización: {0} no se admite en este tipo de área de trabajo.                                                                                   |
| InvalidBaselineConfigurationGeneralError | Se encontró la configuración de línea de base especificada con uno o varios errores de validación de tipo.                                                          |
| ViewConversionError                      | La vista es de una versión más antigua que la que admite esa área de trabajo. Error de conversión de vista: {0}                                                                 |

Si no tiene permisos suficientes, puede que reciba un error general:

![](media/security-center-customize-os-security-config/general-failure-error.png)

## <a name="next-steps"></a>pasos siguientes
En este artículo se ha mostrado cómo personalizar las evaluaciones de configuración de seguridad del sistema operativo en Security Center. Para más información sobre las reglas de configuración y la corrección, consulte:

- [Security Center Common Configuration Identifiers and Baseline Rules](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335) (Identificadores y reglas de línea de base comunes de configuración de Security Center).
- Azure Security Center utiliza Common Configuration Enumeration (CCE) con el fin de asignar identificadores únicos para las reglas de configuración. Visite el sitio de [CCE](https://nvd.nist.gov/config/cce/index) para obtener más información.
- En [Corrección de configuración de seguridad](security-center-remediate-os-vulnerabilities.md) se muestra cómo resolver las vulnerabilidades cuando la configuración del sistema operativo no coincide con las reglas de configuración de seguridad recomendadas.
