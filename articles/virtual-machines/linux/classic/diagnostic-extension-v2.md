---
title: "Supervisión de una máquina virtual de Linux con una extensión de máquina virtual | Microsoft Docs"
description: "Obtenga información sobre cómo usar la extensión de diagnóstico de Linux para supervisar los datos de rendimiento y diagnóstico de una máquina virtual Linux de Azure."
services: virtual-machines-linux
author: NingKuang
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: f54a11c5-5a0e-40ff-af6c-e60bd464058b
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 12/15/2015
ms.author: Ning
ms.openlocfilehash: b8c6e2e22d8478b6e92e7b7942f15d37a840fed3
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/29/2017
---
# <a name="use-the-linux-diagnostic-extension-to-monitor-the-performance-and-diagnostic-data-of-a-linux-vm"></a>Uso de la extensión de diagnóstico de Linux para supervisar los datos de rendimiento y diagnóstico de una máquina virtual Linux

En este documento se describe la versión 2.3 de la extensión Diagnostics de Linux.

> [!IMPORTANT]
> Esta versión está en desuso y puede eliminarse en cualquier momento a partir del 30 de junio de 2018. La sustituye la versión 3.0. Para obtener más información, consulte los [documentos de la versión 3.0 de la extensión Diagnostics de Linux](../diagnostic-extension.md).

## <a name="introduction"></a>Introducción

(**Nota**: La extensión de diagnóstico de Linux se ofrece como código abierto en [GitHub](https://github.com/Azure/azure-linux-extensions/tree/master/Diagnostic), donde se publica primero la información más actualizada sobre la extensión. Es posible que quiera comprobar antes la [página de GitHub](https://github.com/Azure/azure-linux-extensions/tree/master/Diagnostic)).

La extensión de diagnóstico de Linux ayuda a los usuarios a supervisar las máquinas virtuales Linux que se ejecutan en Microsoft Azure. Ofrece la siguiente funcionalidad:

* Recopila y carga la información de rendimiento del sistema de la máquina virtual Linux en la tabla de almacenamiento del usuario, incluidos los datos de diagnóstico y Syslog.
* Permite a los usuarios personalizar las métricas de datos que se recopilan y se cargan.
* Permite a los usuarios cargar los archivos de registro especificados en la tabla de almacenamiento designada.

En la versión 2.3 actual, los datos incluyen la siguiente información:

* Todos los registros de Linux Rsyslog, incluidos los relacionados con el sistema, la seguridad y las aplicaciones.
* Todos los datos del sistema que se especifican en el [sitio de soluciones multiplataforma de System Center](https://scx.codeplex.com/wikipage?title=xplatproviders).
* Archivos de registro especificados por el usuario.

Esta extensión funciona tanto con el modelo de implementación clásico como con el de Azure Resource Manager.

### <a name="current-version-of-the-extension-and-deprecation-of-old-versions"></a>La versión actual de la extensión y el desuso de las versiones anteriores

La versión más reciente de la extensión es la **2.3** y **cualquier versión anterior (2.0, 2.1 y 2.2) quedará en desuso y se retirará a finales de este año (2017)**. Si ha instalado la extensión de diagnóstico de Linux con la actualización automática de versión secundaria deshabilitada, es muy recomendable que la desinstale y la vuelva a instalar con la actualización automática de versión secundaria habilitada. En máquinas virtuales clásicas (ASM), puede hacerlo especificando '2.*' como versión, si va a instalar la extensión mediante la CLI XPLAT de Azure o PowerShell. En máquinas virtuales de ARM, puede realizarlo mediante la inclusión de ' "autoUpgradeMinorVersion": true' en la plantilla de implementación de máquina virtual. Además, cualquier instalación nueva de la extensión debe tener la opción de actualización automática de versión secundaria activada.

## <a name="enable-the-extension"></a>Habilitación de la extensión

Puede habilitar esta extensión a través del [Portal de Azure](https://portal.azure.com/#), Azure PowerShell o scripts de la CLI de Azure.

Para ver y configurar los datos de rendimiento y el sistema directamente desde Azure Portal, siga [estos pasos del blog de Azure](https://azure.microsoft.com/en-us/blog/windows-azure-virtual-machine-monitoring-with-wad-extension/).

En este artículo nos centramos en cómo habilitar y configurar la extensión mediante comandos de la CLI de Azure. De esta forma, podrá leer y ver los datos directamente desde la tabla de almacenamiento.

Tenga en cuenta que los métodos de configuración que se describen a continuación no funcionarán en el Portal de Azure. Para ver y configurar los datos de rendimiento y del sistema directamente desde el Portal de Azure, se debe habilitar esta extensión a través de dicho portal.

## <a name="prerequisites"></a>Requisitos previos

* **Versión 2.0.6 o posterior del agente Linux de Azure**.

  Tenga en cuenta que la mayoría de las imágenes de la galería de máquina virtual Linux de Azure incluyen la versión 2.0.6 o posterior. Puede ejecutar **WAAgent -version** para confirmar la versión instalada en la máquina virtual. Si la máquina virtual está ejecutando una versión anterior a la 2.0.6, puede seguir [estas instrucciones de GitHub](https://github.com/Azure/WALinuxAgent "instrucciones") para actualizarla.
* **Azure CLI**. Siga [esta guía de instalación de la CLI](../../../cli-install-nodejs.md) para configurar el entorno de la CLI de Azure en su máquina. Cuando se haya instalado la CLI de Azure, puede usar el comando **azure** desde la interfaz de la línea de comandos (Bash, Terminal o símbolo del sistema) para obtener acceso a los comandos de la CLI de Azure. Por ejemplo:

  * Ejecute **azure vm extension set --help** para obtener información de ayuda detallada.
  * Ejecute **azure login** para iniciar sesión en Azure.
  * Ejecute **azure vm list** para enumerar todas las máquinas virtuales que tiene en Azure.
* Una cuenta de almacenamiento para almacenar los datos. Necesitará que el nombre de la cuenta de almacenamiento se haya creado anteriormente, además de una clave de acceso para cargar los datos en el almacenamiento.

## <a name="use-the-azure-cli-command-to-enable-the-linux-diagnostic-extension"></a>Uso del comando de la CLI de Azure para habilitar la extensión de diagnóstico de Linux

### <a name="scenario-1-enable-the-extension-with-the-default-data-set"></a>Escenario 1. Habilitación de la extensión con el conjunto de datos predeterminado

En la versión 2.3 o posterior, los datos predeterminados que se recopilarán incluyen la siguiente información:

* Todas la información de Rsyslog, incluidos los registros del sistema, seguridad y aplicaciones.  
* Un conjunto de datos del sistema base. Tenga en cuenta que el conjunto de datos completo aparece descrito en el [sitio de soluciones multiplataforma de System Center](https://scx.codeplex.com/wikipage?title=xplatproviders).
  Si desea habilitar más datos, continúe con los pasos que figuran en los escenarios 2 y 3.

Paso 1. Cree un archivo llamado "PrivateConfig.json" con el siguiente contenido:

    {
        "storageAccountName" : "the storage account to receive data",
        "storageAccountKey" : "the key of the account"
    }

Paso 2: Ejecute **azure vm extension set vm_name LinuxDiagnostic Microsoft.OSTCExtensions 2.* --private-config-path PrivateConfig.json**.

### <a name="scenario-2-customize-the-performance-monitor-metrics"></a>Escenario 2. Personalización de las métricas del monitor de rendimiento

En esta sección se describe cómo personalizar la tabla de datos de rendimiento y diagnóstico.

Paso 1. Cree un archivo denominado "PrivateConfig.json" con el contenido descrito en el escenario 1. Cree también un archivo llamado "PublicConfig.json". Especificar los datos concretos que quiere recopilar.

Para ver todos los proveedores y variables que se admiten, visite el [sitio de soluciones multiplataforma de System Center](https://scx.codeplex.com/wikipage?title=xplatproviders). Puede tener varias consultas y almacenarlas en varias tablas anexando más consultas al script.

De forma predeterminada, siempre se recopilan los datos de Rsyslog.

    {
          "perfCfg":
          [
              {
                  "query" : "SELECT PercentAvailableMemory, AvailableMemory, UsedMemory ,PercentUsedSwap FROM SCX_MemoryStatisticalInformation",
                  "table" : "LinuxMemory"
              }
          ]
    }


Paso 2: Ejecute **azure vm extension set vm_name LinuxDiagnostic Microsoft.OSTCExtensions '2.*' --private-config-path PrivateConfig.json --public-config-path PublicConfig.json**.

### <a name="scenario-3-upload-your-own-log-files"></a>Escenario 3. Carga de sus propios archivos de registro

En esta sección se describe cómo recopilar y cargar archivos de registro concretos en la cuenta de almacenamiento. Deberá especificar la ruta de acceso al archivo de registro y el nombre de la tabla en la que se almacenará el registro. Para tener varios archivos de registro puede agregar varias entradas de archivo o tabla al script.

Paso 1. Cree un archivo denominado "PrivateConfig.json" con el contenido descrito en el escenario 1. Luego, cree otro archivo llamado "PublicConfig.json" con el siguiente contenido:

```json
{
    "fileCfg" :
    [
        {
            "file" : "/var/log/mysql.err",
            "table" : "mysqlerr"
            }
    ]
}
```

Paso 2: Ejecute `azure vm extension set vm_name LinuxDiagnostic Microsoft.OSTCExtensions '2.*' --private-config-path PrivateConfig.json --public-config-path PublicConfig.json`.

Tenga en cuenta que, con esta configuración de las versiones de extensión anteriores a 2.3, todos los registros que se escriban en `/var/log/mysql.err` también podrían duplicarse en `/var/log/syslog` (o `/var/log/messages` según la distribución de Linux). Si desea evitar este registro duplicado, puede excluir el registro de los registros de instalación `local6` en la configuración de rsyslog. Depende de la distribución de Linux pero en un sistema Ubuntu 14.04 el archivo que se debe modificar es `/etc/rsyslog.d/50-default.conf` y puede reemplazar la línea `*.*;auth,authpriv.none -/var/log/syslog` por `*.*;auth,authpriv,local6.none -/var/log/syslog`. Este problema se ha corregido en la versión de revisión de 2.3 (2.3.9007) más reciente, de modo que si tiene la versión de extensión 2.3, no debería producirse más. Si sigue produciéndose incluso después de reiniciar la máquina virtual, póngase en contacto con nosotros y ayúdenos a averiguar por qué la versión de revisión más reciente no se ha instalado automáticamente.

### <a name="scenario-4-stop-the-extension-from-collecting-any-logs"></a>Escenario 4. Detención del proceso de recopilación de registros por parte de la extensión

En esta sección se describe cómo hacer que la extensión deje de recopilar registros. Tenga en cuenta que el proceso del agente de supervisión seguirá activo y en ejecución incluso con esta reconfiguración. Si desea detener completamente el proceso de agente de supervisión, puede hacerlo deshabilitando la extensión. El comando para deshabilitar la extensión es `azure vm extension set --disable <vm_name> LinuxDiagnostic Microsoft.OSTCExtensions '2.*'`.

Paso 1. Cree un archivo denominado "PrivateConfig.json" con el contenido descrito en el escenario 1. Cree otro archivo llamado "PublicConfig.json" con el siguiente contenido:

    {
        "perfCfg" : [],
        "enableSyslog" : "false"
    }


Paso 2: Ejecute **azure vm extension set vm_name LinuxDiagnostic Microsoft.OSTCExtensions '2.*' --private-config-path PrivateConfig.json --public-config-path PublicConfig.json**.

## <a name="review-your-data"></a>Revisión de los datos

Los datos de diagnóstico y rendimiento y se almacenan en una tabla de almacenamiento de Azure. Consulte [Uso del Almacenamiento de tablas de Azure con Ruby](../../../cosmos-db/table-storage-how-to-use-ruby.md) para obtener información sobre cómo acceder a los datos de la tabla de almacenamiento utilizando scripts de la CLI de Azure.

Además, puede utilizar las siguientes herramientas de la interfaz de usuario para tener acceso a los datos:

1. Explorador de servidores de Visual Studio. Vaya a la cuenta de almacenamiento. Cuando la máquina virtual se ejecute aproximadamente durante 5 minutos, debe ver las cuatro tablas predeterminadas: "LinuxCpu", "LinuxDisk", "LinuxMemory" y "Linuxsyslog". Haga doble clic en el nombre de la tabla para ver los datos.
1. [Explorador de almacenamiento de Azure](https://azurestorageexplorer.codeplex.com/ "Explorador de almacenamiento de Azure").

![imagen](./media/diagnostic-extension/no1.png)

Si ha habilitado el archivo fileCfg o perfCfg (tal y como se describe en los escenarios 2 y 3), podrá utilizar el Explorador de servidores de Visual Studio y el Explorador de almacenamiento de Azure para ver los datos no predeterminados.

## <a name="known-issues"></a>Problemas conocidos

* Solo puede accederse a la información de Rsyslog y el archivo de registro del cliente especificado a través de scripts.
