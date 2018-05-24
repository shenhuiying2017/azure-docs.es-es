---
title: Hybrid Runbook Worker de Azure Automation en Linux
description: En este artículo encontrará información sobre cómo instalar un Hybrid Runbook Worker de Azure Automation que le permita ejecutar runbooks en equipos con Linux en su centro de datos local o entorno de nube.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 04/25/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: aca68b6e8d0e6b80a1504b16b9b3462f20fdc6c4
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/16/2018
ms.locfileid: "34195665"
---
# <a name="how-to-deploy-a-linux-hybrid-runbook-worker"></a>Cómo implementar un Hybrid Runbook Worker en Linux

La característica Hybrid Runbook Worker de Azure Automation permite ejecutar runbooks directamente en el equipo que hospeda el rol y en los recursos del entorno para administrar dichos recursos locales. Hybrid Runbook Worker de Linux ejecuta runbooks como un usuario especial que se puede elevar para ejecutar comandos que necesitan elevación. Los runbooks se almacenan y administran en Azure Automation y después se entregan a uno o más equipos designados. En este artículo, se describe cómo instalar Hybrid Runbook Worker en un equipo Linux.

## <a name="supported-linux-operating-systems"></a>Sistemas operativos Linux compatibles

La siguiente es una lista de las distribuciones de Linux que se admiten:

* Amazon Linux 2012.09 --> 2015.09 (x86/x64)
* CentOS Linux 5, 6 y 7 (x86/x64)
* Oracle Linux 5, 6 y 7 (x86/x64)
* Red Hat Enterprise Linux Server 5,6 y 7 (x86/x64)
* Debian GNU/Linux 6, 7 y 8 (x86/x64)
* Ubuntu 12.04 LTS, 14.04 LTS, 16.04 LTS (x86/x64)
* SUSE Linux Enterprise Server 11 y 12 (x86/x64)

## <a name="installing-linux-hybrid-runbook-worker"></a>Instalación de Hybrid Runbook Worker en Linux

Para instalar y configurar una instancia de Hybrid Runbook Worker en el equipo con Linux, hay que seguir un procedimiento sencillo para instalar y configurar el rol manualmente. Es necesario habilitar la solución **Automation Hybrid Worker** en el área de trabajo de Log Analytics y, después, ejecutar un conjunto de comandos para registrar el equipo como un trabajo y agregarlo a un grupo nuevo u otro existente.

Estos son los requisitos mínimos de un Hybrid Runbook Worker de Linux:

* Dos núcleos como mínimo
* Un mínimo de 4 GB de RAM
* Puerto 443 (saliente)

### <a name="package-requirements"></a>Requisitos de paquete

| **Paquetes necesarios** | **Descripción** | **Versión mínima**|
|--------------------- | --------------------- | -------------------|
|Glibc |Biblioteca GNU C| 2.5-12 |
|Openssl| Bibliotecas OpenSSL | 0.9.8E o 1.0|
|Curl | Cliente web de cURL | 7.15.5|
|Python ctypes | |
|PAM | Módulos de autenticación conectables|

Antes de continuar, debe anotar el área de trabajo de Log Analytics a la que está vinculada su cuenta de Automation, así como la clave principal de la cuenta de Automation. Encontrará ambos datos en el portal si selecciona la cuenta de Automation y, para el identificador de área de trabajo, selecciona **Área de trabajo** y, para la clave principal, selecciona **Claves**. Para obtener información sobre los puertos y las direcciones que se necesitan para Hybrid Runbook Worker, consulte [Configuring your network](automation-hybrid-runbook-worker.md#network-planning) (Configurar la red).

1. Habilite la solución "Automation Hybrid Worker" en Azure. Esto se puede hacer de la siguiente forma:

   1. Agregue la solución **Automation Hybrid Worker** a la suscripción mediante el procedimiento que se describe en [Adición de soluciones de administración de Azure Log Analytics al área de trabajo](../log-analytics/log-analytics-add-solutions.md).
   1. Ejecute el siguiente cmdlet:

        ```azurepowershell-interactive
         Set-AzureRmOperationalInsightsIntelligencePack -ResourceGroupName  <ResourceGroupName> -WorkspaceName <WorkspaceName> -IntelligencePackName  "AzureAutomation" -Enabled $true
        ```

1. Para instalar el agente OMS para Linux, ejecute el comando siguiente, reemplazando \<WorkspaceID\> y \<WorkspaceKey\> con los valores adecuados de su área de trabajo.

   ```bash
   wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <WorkspaceID> -s <WorkspaceKey>
   ```

1. Ejecute el siguiente comando y cambie los valores de los parámetros *-w*, *-k*, *-g* y *-e*. Para el parámetro *-g*, reemplace el valor con el nombre del grupo de Hybrid Runbook Worker al que se debe unir el nuevo Hybrid Runbook Worker de Linux. Si el nombre todavía no existe en su cuenta de Automation, se crea un nuevo grupo de Hybrid Runbook Worker con ese nombre.

   ```bash
   sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/onboarding.py --register -w <LogAnalyticsworkspaceId> -k <AutomationSharedKey> -g <hybridgroupname> -e <automationendpoint>
   ```

1. Una vez completado el comando, la página Grupos de Hybrid Worker en Azure Portal mostrará el nuevo grupo y el número de miembros o, si es un grupo existente, el número de miembros aumenta. Puede seleccionar el grupo de la lista en la página **Grupos de Hybrid Worker** y seleccionar el icono **Hybrid Workers**. En la página **Hybrid Workers**, verá que aparece cada miembro del grupo.

## <a name="turning-off-signature-validation"></a>Desactivación de la validación de firma

De forma predeterminada, las instancias de Hybrid Runbook Worker de Linux requieren la validación de la firma. Si ejecuta un runbook sin firmar en un trabajo, verá un error que indica que no se ha podido validar la firma. Para desactivar la validación de la firma, ejecute el siguiente comando y sustituya el segundo parámetro por el identificador del área de trabajo de Log Analytics:

 ```bash
 sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/require_runbook_signature.py --false <LogAnalyticsworkspaceId>
 ```

## <a name="supported-runbook-types"></a>Tipos de runbook admitidos

Las instancias de Hybrid Runbook Worker Linux no admiten el conjunto completo de tipos de runbook que se encuentran en Azure Automation.

Los siguientes tipos de runbook funcionan en instancias de Hybrid Worker Linux:

* Python 2
* PowerShell

Los siguientes tipos de runbook no funcionan en instancias de Hybrid Worker Linux:

* Flujo de trabajo de PowerShell
* Gráfico
* Flujo de trabajo gráfico de PowerShell

## <a name="troubleshooting"></a>solución de problemas

Hybrid Runbook Worker de Linux depende del agente de OMS para Linux a fin de comunicarse con su cuenta de Automation para registrar el trabajo, recibir trabajos de runbook e informar del estado. Si se produce un error de registro del trabajo, estas son algunas de las causas posibles:

### <a name="the-oms-agent-for-linux-is-not-running"></a>El agente de OMS para Linux no se está ejecutando

Si el agente de OMS para Linux no se está ejecutando, esto impide que Hybrid Runbook Worker de Linux se comunique con Azure Automation. Escriba el siguiente comando para comprobar si el agente se está ejecutando: `ps -ef | grep python`. Debería ver un resultado similar al siguiente, los procesos de Python con la cuenta de usuario **nxautomation**. Si no están habilitadas las soluciones Update Management o Azure Automation, no se ejecutará ninguno de los siguientes procesos.

```bash
nxautom+   8567      1  0 14:45 ?        00:00:00 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/main.py /var/opt/microsoft/omsagent/state/automationworker/oms.conf rworkspace:<workspaceId> <Linux hybrid worker version>
nxautom+   8593      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/state/automationworker/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
nxautom+   8595      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/<workspaceId>/state/automationworker/diy/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
```

En la siguiente lista, se muestran los procesos que se inician para un Hybrid Runbook Worker de Linux. Se encuentran en el directorio `/var/opt/microsoft/omsagent/state/automationworker/`.

* **oms.conf**: este es el proceso de administrador de trabajos, este se inicia directamente desde la DSC.

* **worker.conf**: este es el proceso de Auto Registered Hybrid Worker, lo inicia el administrador de trabajos. Este proceso lo usa Update Management y es transparente para el usuario. Este proceso no debe estar presente si la solución de Update Management no está habilitada en el equipo.

* **diy/worker.conf**: este es el proceso de DIY Hybrid Worker. El proceso de DIY Hybrid Worker se usa para ejecutar runbooks de usuario en Hybrid Runbook Worker. Difiere del proceso de Auto Registered Hybrid Worker solo en el detalle clave de que usa otra configuración. Este proceso no debe estar presente si la solución Azure Automation no está habilitada y el DIY Hybrid Worker de Linux no está registrado.

Si el agente de OMS para Linux no se está ejecutando, ejecute el siguiente comando para iniciar el servicio: `sudo /opt/microsoft/omsagent/bin/service_control restart`.

### <a name="the-specified-class-does-not-exist"></a>La clase especificada no existe

Si ve el error **La clase especificada no existe...** en `/var/opt/microsoft/omsconfig/omsconfig.log`, el agente de OMS para Linux debe actualizarse. Ejecute el siguiente comando para volver a instalar al agente de OMS:

```bash
wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <WorkspaceID> -s <WorkspaceKey>
```

Para obtener pasos adicionales sobre cómo solucionar problemas relacionados con Update Management, consulte [Update Management: solución de problemas](automation-update-management.md#troubleshooting).

## <a name="next-steps"></a>Pasos siguientes

* Revise la [ejecución de runbooks en Hybrid Runbook Worker](automation-hrw-run-runbooks.md) para más información sobre cómo configurar los runbooks para automatizar los procesos del centro de datos local o en otro entorno de nube.
* Para obtener instrucciones sobre cómo quitar Hybrid Runbook Workers, vea [Quitar Hybrid Runbook Workers de Azure Automation](automation-hybrid-runbook-worker.md#removing-hybrid-runbook-worker).
