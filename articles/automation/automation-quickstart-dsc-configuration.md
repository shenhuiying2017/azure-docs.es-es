---
title: "Inicio rápido de Azure: Configuración de una máquina virtual con DSC | Microsoft Docs"
description: "Configurar una pila de LAMP en una máquina virtual Linux con Desired State Configuration"
services: automation
keywords: "dsc, configuración, automation"
author: KrisBash
ms.author: krbash
ms.date: 12/17/2017
ms.topic: quickstart
ms.custom: mvc
manager: carmonm
ms.openlocfilehash: 7f35acc0d8d553a3f5d1576ae18cbafd1dcbb225
ms.sourcegitcommit: 48fce90a4ec357d2fb89183141610789003993d2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/12/2018
---
# <a name="configure-a-linux-virtual-machine-with-desired-state-configuration"></a>Configurar una máquina virtual Linux con Desired State Configuration

Al habilitar Desired State Configuration (DSC), puede administrar y supervisar las configuraciones de los servidores Windows y Linux. Las configuraciones que se desvían de la configuración deseada se pueden identificar o corregir automáticamente. Esta guía de inicio rápido le guía por la incorporación de una máquina virtual Linux y la implementación de una pila de LAMP con DSC.

## <a name="prerequisites"></a>requisitos previos

Para completar este inicio rápido necesita instalar:

* Una suscripción de Azure. Si no tiene una suscripción a Azure, [cree una cuenta gratuita](https://azure.microsoft.com/free/).
* Una cuenta de Azure Automation Para obtener instrucciones sobre cómo crear una cuenta de ejecución de Azure Automation, consulte el artículo sobre las [cuentas de ejecución de Azure](automation-sec-configure-azure-runas-account.md).
* Una máquina virtual de Azure Resource Manager (no clásica) que ejecute Red Hat Enterprise Linux, CentOS u Oracle Linux. Para obtener instrucciones sobre la creación de una máquina virtual, consulte [Creación de máquinas virtuales Linux con Azure Portal](../virtual-machines/linux/quick-create-portal.md)

## <a name="log-in-to-azure"></a>Inicie sesión en Azure.
Inicie sesión en Azure en https://portal.azure.com.

## <a name="onboard-a-virtual-machine"></a>Incorporar una máquina virtual
Hay muchos métodos diferentes para incorporar una máquina y habilitar Desired State Configuration. Esta guía de inicio rápido trata la incorporación a través de una cuenta de Automation. Para obtener más información sobre los distintos métodos de incorporación de máquinas a Desired State Configuration, lea el artículo sobre la [incorporación](https://docs.microsoft.com/azure/automation/automation-dsc-onboarding).

1. En el panel izquierdo de Azure Portal, seleccione **Cuentas de Automation**. Si la opción no está visible en el panel izquierdo, haga clic en **Todos los servicios** y búsquela en la vista que se muestra.
1. Seleccione una cuenta de Automation de la lista.
1. En el panel izquierdo de la cuenta de Automation, seleccione **Nodos DSC**.
1. Haga clic en la opción de menú **Agregar máquina virtual de Azure**.
1. Busque la máquina virtual para la que quiere habilitar DSC. Puede usar el campo de búsqueda y las opciones de filtro para buscar una máquina virtual específica.
1. Haga clic en la máquina virtual y, a continuación, seleccione **Conectar**.
1. Seleccione la configuración de DSC adecuada para la máquina virtual. Si ya tiene una configuración preparada, la puede especificar como *Nombre de la configuración del nodo*. Puede establecer el [modo de configuración](https://docs.microsoft.com/powershell/dsc/metaconfig) para controlar el comportamiento de la configuración de la máquina.
1. Haga clic en **Aceptar**

![Incorporación de una máquina virtual de Azure a DSC](./media/automation-quickstart-dsc-configuration/dsc-onboard-azure-vm.png)

Mientras se implementa la extensión de Desired State Configuration en la máquina virtual, se muestra *Conectando.*

## <a name="import-modules"></a>Importación de módulos

Los módulos contienen recursos de DSC y muchos de ellos se pueden encontrar en la [Galería de PowerShell](https://www.powershellgallery.com). Todos los recursos que se utilizan en las configuraciones se deben importar en la cuenta de Automation antes de la compilación. Para este tutorial, es necesario el módulo denominado **nx**.

1. En el panel izquierdo de la cuenta de Automation, seleccione **Galería de módulos** (en Recursos compartidos).
1. Para buscar el módulo que quiere importar, escriba parte de su nombre: *nx*.
1. Haga clic en el módulo que quiere importar
1. Haga clic en **Importar**

![Importación de un módulo DSC](./media/automation-quickstart-dsc-configuration/dsc-import-module-nx.png)

## <a name="import-the-configuration"></a>Importar la configuración

Esta guía de inicio rápido usa una configuración de DSC que configura el servidor HTTP Apache, MySQL y PHP en la máquina.

Para más información sobre las configuraciones DSC, consulte [Configuraciones DSC](https://docs.microsoft.com/powershell/dsc/configurations).

En un editor de texto, escriba lo siguiente y guárdelo localmente como `LAMPServer.ps1`.

```powershell-interactive
configuration LAMPServer {
   Import-DSCResource -module "nx"

   Node localhost {

        $requiredPackages = @("httpd","mod_ssl","php","php-mysql","mariadb","mariadb-server")
        $enabledServices = @("httpd","mariadb")

        #Ensure packages are installed
        ForEach ($package in $requiredPackages){
            nxPackage $Package{
                Ensure = "Present"
                Name = $Package
                PackageManager = "yum"
            }
        }

        #Ensure daemons are enabled
        ForEach ($service in $enabledServices){
            nxService $service{
                Enabled = $true
                Name = $service
                Controller = "SystemD"
                State = "running"
            }
        }
   }
}
```

Para importar la configuración:

1. En el panel izquierdo de la cuenta de Automation, seleccione **Configuraciones DSC**.
1. Haga clic en la opción de menú **Agregar una configuración**.
1. Seleccione el *archivo de configuración* que guardó en el paso anterior.
1. Haga clic en **Aceptar**

## <a name="compile-a-configuration"></a>Compilar una configuración

Las configuraciones DSC se deben compilar en una configuración de nodo (documento MOF) antes de asignarse a un nodo. La compilación valida la configuración y permite la entrada de valores de parámetro. Para obtener más información sobre la compilación de una configuración, consulte: [Compilación de configuraciones en DSC de Azure Automation](https://docs.microsoft.com/azure/automation/automation-dsc-compile).

Para compilar la configuración:

1. En el panel izquierdo de la cuenta de Automation, seleccione **Configuraciones DSC**.
1. Seleccione la configuración importada en un paso anterior, "LAMPServer".
1. En las opciones del menú, haga clic en **Compilar** y, a continuación, en **Sí**.
1. En la vista Configuración, verá un nuevo *trabajo de compilación* en cola. Cuando el trabajo se complete correctamente, podrá avanzar al paso siguiente. Si se produce algún error, puede hacer clic en el trabajo de compilación para obtener más información.

![Estado del trabajo de compilación](./media/automation-quickstart-dsc-configuration/dsc-compilationjob.png)

## <a name="assign-a-node-configuration"></a>Asignación de una configuración de nodo

Una *configuración de nodo* compilada se puede asignar a nodos DSC. La asignación aplica la configuración a la máquina y supervisa (o corrige automáticamente) cualquier desvío de esa configuración.

1. En el panel izquierdo de la cuenta de Automation, seleccione **Nodos DSC**.
1. Seleccione el nodo al que quiere asignar una configuración
1. Haga clic en **Asignar configuración de nodo**.
1. Seleccione *Configuración del nodo* - **LAMPServer.localhost** para realizar la asignación y haga clic en **Aceptar**.
1. La configuración compilada está asignada ahora al nodo y el estado del nodo cambia a *Pendiente*. En la siguiente comprobación periódica, el nodo recupera la configuración, la aplica y presenta un informe de estado. El nodo puede tardar hasta 30 minutos en recuperar la configuración en función de la configuración del nodo. Para forzar una comprobación inmediata, puede ejecutar el comando siguiente localmente en la máquina virtual Linux: `sudo /opt/microsoft/dsc/Scripts/PerformRequiredConfigurationChecks.py`

![Asignación de una configuración de nodo](./media/automation-quickstart-dsc-configuration/dsc-assign-node-configuration.png)

## <a name="viewing-node-status"></a>Visualización del estado del nodo

El estado de todos los nodos administrados se puede encontrar en la vista **Nodos DSC** de la cuenta de Automation. Puede filtrar la visualización por el estado, la configuración del nodo o la búsqueda de nombre. 

![Estado del nodo DSC](./media/automation-quickstart-dsc-configuration/dsc-node-status.png)

## <a name="next-steps"></a>pasos siguientes

En esta guía de inicio rápido, incorporó una máquina virtual Linux a DSC, creó una configuración para una pila de LAMP y la implementó en la máquina virtual. Para obtener información sobre cómo usar DSC de Automatización para habilitar la implementación continua, consulte el artículo:

> [!div class="nextstepaction"]
> [Implementación continua en máquinas virtuales con DSC y Chocolatey](./automation-dsc-cd-chocolatey.md)

* Para obtener más información sobre Desired State Configuration de PowerShell, consulte [Información general sobre la configuración de estado deseado de Windows PowerShell](https://docs.microsoft.com/powershell/dsc/overview).
* Para obtener más información acerca de cómo administrar DSC de Automatización desde PowerShell, consulte [Azure PowerShell](https://docs.microsoft.com/powershell/module/azurerm.automation/?view=azurermps-5.0.0).
* Para obtener información sobre cómo reenviar los informes de DSC a Log Analytics para la generación de informes y alertas, consulte el tema sobre el [reenvío de informes de DSC a Log Analytics](https://docs.microsoft.com/azure/automation/automation-dsc-diagnostics). 
