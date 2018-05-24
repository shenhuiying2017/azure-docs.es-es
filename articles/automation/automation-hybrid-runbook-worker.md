---
title: Hybrid Runbook Workers de Azure Automation
description: Este artículo brinda información sobre cómo instalar y usar Hybrid Runbook Worker, una característica de Azure Automation que le permite ejecutar runbooks en máquinas de su centro de datos local o proveedor de la nube.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 04/25/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: 0af8806cdc55b89a9ab87a8059808e4fcc9a1730
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/14/2018
ms.locfileid: "34159193"
---
# <a name="automate-resources-in-your-data-center-or-cloud-with-hybrid-runbook-worker"></a>Automatización de recursos en los centros de datos o nube con Hybrid Runbook Worker

Es posible que los runbooks de Azure Automation no tengan acceso a los recursos de otras nubes o del entorno local, debido a que se ejecutan en la nube de Azure. La característica Hybrid Runbook Worker de Azure Automation permite ejecutar runbooks directamente en el equipo que hospeda el rol y en los recursos del entorno para administrar dichos recursos locales. Los runbooks se almacenan y administran en Azure Automation y después se entregan a uno o más equipos designados.

Esta funcionalidad se ilustra en la imagen siguiente:

![Información general de Trabajo híbrido de runbook](media/automation-hybrid-runbook-worker/automation.png)

## <a name="hybrid-runbook-worker-groups"></a>Grupos de Trabajos híbridos de runbook

Cada Trabajo híbrido de runbook es miembro de un grupo de Trabajos híbridos de runbook que especifica cuando instala el agente. Un grupo puede incluir solo un agente, pero puede instalar varios agentes en un grupo para contar con alta disponibilidad.

Cuando se inicia un runbook en Hybrid Runbook Worker, se especifica el grupo en el que se ejecuta. Cada rol de trabajo del grupo sondea Azure Automation para ver si hay trabajos disponibles. Si hay un trabajo disponible, el primer rol de trabajo en obtener el trabajo lo toma. No es posible especificar un trabajo determinado.

## <a name="installing-a-hybrid-runbook-worker"></a>Instalación de un Hybrid Runbook Worker

El proceso para instalar una instancia de Hybrid Runbook Worker depende del sistema operativo. En la tabla siguiente se incluyen vínculos a los diferentes métodos que puede usar para instalar una instancia de Hybrid Runbook Worker. Para instalar y configurar Hybrid Runbook Worker en Windows, hay dos métodos disponibles. El método recomendado usa un runbook de Automation a fin de automatizar completamente el proceso necesario para configurar un equipo Windows. El segundo método es un procedimiento paso a paso para instalar y configurar el rol manualmente. En el caso de las máquinas de Linux, debe ejecutar un script de Python para instalar el agente en la máquina.

|SO  |Tipos de implementación  |
|---------|---------|
|Windows     | [PowerShell](automation-windows-hrw-install.md#automated-deployment)<br>[Manual](automation-windows-hrw-install.md#manual-deployment)        |
|Linux     | [Python](automation-linux-hrw-install.md#installing-linux-hybrid-runbook-worker)        |

> [!NOTE]
> Para administrar la configuración de los servidores que admiten el rol de Hybrid Runbook Worker con la configuración de estado deseado (DSC), debe agregarlos como nodos de DSC. Para obtener más información sobre su incorporación para la administración con DSC, consulte [Incorporación de máquinas para administrarlas con DSC de Azure Automation](automation-dsc-onboarding.md).
>
>Si habilita la [solución Update Management](automation-update-management.md), los equipos conectados al área de trabajo de Log Analytics se configurarán automáticamente como Hybrid Runbook Worker para admitir los runbooks que se incluyen en esta solución. Sin embargo, no está registrado en ningún grupo de Hybrid Worker ya definido en la cuenta de Automation. El equipo se puede agregar a un grupo de Hybrid Runbook Worker en la cuenta de Automation para admitir los runbooks de Automation siempre que use la misma cuenta para la solución y la pertenencia a grupos de Hybrid Runbook Worker. Esta funcionalidad se agregó a la versión 7.2.12024.0 de Hybrid Runbook Worker.

Revise la [información para planear la red](#network-planning) antes de empezar a implementar una instancia de Hybrid Runbook Worker. Después de haber implementado correctamente un trabajo de runbook, revise la [ejecución de runbooks en Hybrid Runbook Worker](automation-hrw-run-runbooks.md) para más información sobre cómo configurar los runbooks para automatizar los procesos del centro de datos local o en otro entorno de nube.

## <a name="removing-hybrid-runbook-worker"></a>Eliminación de Hybrid Runbook Worker

Puede quitar uno o más Hybrid Runbook Worker de un grupo o puede quitar el grupo, dependiendo de sus requisitos. Para quitar una instancia de Hybrid Runbook Worker de un equipo local, realice los pasos siguientes:

1. En Azure Portal, vaya a su cuenta de Automation.
2. En la hoja **Configuración**, seleccione **Claves** y anote los valores de **URL** y **Primary Access Key** (Clave de acceso principal). Esta información la necesita para el siguiente paso.

### <a name="windows"></a>Windows

Abra una sesión de PowerShell en modo Administrador y ejecute el comando siguiente. Use el modificador **-Verbose** para ver un registro detallado del proceso de eliminación.

```powershell
Remove-HybridRunbookWorker -url <URL> -key <PrimaryAccessKey>
```

Para eliminar máquinas obsoletas del grupo Hybrid Worker, use el parámetro `machineName` opcional.

```powershell-interactive
Remove-HybridRunbookWorker -url <URL> -key <PrimaryAccessKey> -machineName <ComputerName>
```

### <a name="linux"></a>Linux

```bash
sudo python onboarding.py --deregister --endpoint="<URL>" --key="<PrimaryAccessKey>" --groupname="Example" --workspaceid="<workspaceId>"
```

> [!NOTE]
> Con esto no se quita Microsoft Monitoring Agent del equipo, sino solo la funcionalidad y la configuración del rol Hybrid Runbook Worker.

## <a name="remove-hybrid-worker-groups"></a>Eliminación de grupos de Hybrid Worker

Para quitar un grupo, primero debe quitar el Hybrid Runbook Worker de todos los equipos que sean miembros del grupo mediante el procedimiento mostrado anteriormente y luego realizar los pasos siguientes para quitar el grupo.

1. Abra la cuenta de Automation en Azure Portal.
1. En **Automatización de procesos**, seleccione **Grupos de Hybrid Worker**. Seleccione el grupo que quiere eliminar. Tras seleccionar el grupo específico, se muestra la página de propiedades de **Grupo de Hybrid Worker**.

   ![Página Grupo de Hybrid Runbook Worker](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-group-properties.png)

1. En la página de propiedades del grupo seleccionado, haga clic en **Eliminar**. Aparecerá un mensaje solicitándole que confirme esta acción. Si está seguro de que quiere continuar, seleccione **Sí**.

   ![Cuadro de diálogo de confirmación de eliminación de grupo](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-confirm-delete.png)

   Este proceso puede tardar varios segundos en completarse y puede realizar el seguimiento de su progreso en **Notificaciones** en el menú.

## <a name="network-planning"></a>Configure la red

### <a name="hybrid-worker-role"></a>Rol de Hybrid Worker

Para que Hybrid Runbook Worker se conecte a Log Analytics y se registre en este servicio, debe tener acceso al número de puerto y a las direcciones URL que se describen en esta sección. Y esto aparte de los [puertos y las direcciones URL necesarios para que Microsoft Monitoring Agent](../log-analytics/log-analytics-agent-windows.md) se conecte a Log Analytics.

Si se usa un servidor proxy para realizar la comunicación entre el agente y el servicio Log Analytics, asegúrese de que sea posible acceder a los recursos adecuados. Si usa un firewall para restringir el acceso a Internet, tendrá que configurarlo para que permita el acceso.

Los siguientes puertos y direcciones URL son necesarios para que el rol Hybrid Runbook Worker se comunique con Automation:

* Puerto: solo se requiere el puerto TCP 443 para el acceso a Internet.
* URL global: *.azure-automation.net
* Direcciones URL globales de US Gov Virginia: *.azure-automation.us
* Servicio de agente: https://\<workspaceId\>.agentsvc.azure-automation.net

Si tiene una cuenta de Automation definida para una región específica, puede restringir la comunicación con ese centro de datos regional. En la tabla siguiente se proporciona el registro de DNS para cada región.

| **Región** | **Registro de DNS** |
| --- | --- |
| Centro occidental de EE.UU. | wcus-jobruntimedata-prod-su1.azure-automation.net</br>wcus-agentservice-prod-1.azure-automation.net |
| Centro-Sur de EE. UU |scus-jobruntimedata-prod-su1.azure-automation.net</br>scus-agentservice-prod-1.azure-automation.net |
| Este de EE. UU. 2 |eus2-jobruntimedata-prod-su1.azure-automation.net</br>eus2-agentservice-prod-1.azure-automation.net |
| Centro de Canadá |cc-jobruntimedata-prod-su1.azure-automation.net</br>cc-agentservice-prod-1.azure-automation.net |
| Europa occidental |we-jobruntimedata-prod-su1.azure-automation.net</br>we-agentservice-prod-1.azure-automation.net |
| Europa del Norte |ne-jobruntimedata-prod-su1.azure-automation.net</br>ne-agentservice-prod-1.azure-automation.net |
| Sudeste de Asia |sea-jobruntimedata-prod-su1.azure-automation.net</br>sea-agentservice-prod-1.azure-automation.net|
| India Central |cid-jobruntimedata-prod-su1.azure-automation.net</br>cid-agentservice-prod-1.azure-automation.net |
| Este de Japón |jpe-jobruntimedata-prod-su1.azure-automation.net</br>jpe-agentservice-prod-1.azure-automation.net |
| Sudeste de Australia |ase-jobruntimedata-prod-su1.azure-automation.net</br>ase-agentservice-prod-1.azure-automation.net |
| Sur del Reino Unido 2 | uks-jobruntimedata-prod-su1.azure-automation.net</br>uks-agentservice-prod-1.azure-automation.net |
| Gobierno de EE. UU. - Virginia | usge-jobruntimedata-prod-su1.azure-automation.us<br>usge-agentservice-prod-1.azure-automation.us |

Para obtener una lista de direcciones IP de regiones en lugar de nombres de regiones, descargue el archivo XML [Direcciones IP de los centros de datos de Azure](https://www.microsoft.com/download/details.aspx?id=41653) desde el Centro de descarga de Microsoft.

> [!NOTE]
> El archivo XML de direcciones IP de los centros de datos de Azure enumera los intervalos de direcciones IP que se usan en los centros de datos de Microsoft Azure. En el archivo se incluyen Compute, SQL y los intervalos de almacenamiento.
>
>Semanalmente, se publica un archivo actualizado. El archivo refleja los intervalos implementados actualmente y los próximos cambios en los intervalos IP. Los nuevos intervalos que aparecen en el archivo no se utilizan en los centros de datos durante al menos una semana.
>
> Descargar el archivo XML nuevo cada semana es una buena idea. A continuación, actualice el sitio para identificar correctamente los servicios que se ejecutan en Azure. Los usuarios de Azure ExpressRoute deberían observar que este archivo se usa para actualizar la publicidad de Border Gateway Protocol (BGP) del espacio de Azure la primera semana de cada mes.

### <a name="update-management"></a>Administración de actualizaciones

Además de las direcciones y los puertos estándar que necesita Hybrid Runbook Worker, las direcciones siguientes se requieren específicamente para Update Management. La comunicación con estas direcciones se realiza a través del puerto 443.

|Azure Public  |Azure Government  |
|---------|---------|
|* .ods.opinsights.azure.com     |*.ods.opinsights.azure.us         |
|* .oms.opinsights.azure.com     | *.oms.opinsights.azure.us        |
|* .blob.core.windows.net|*.blob.core.usgovcloudapi.net|

## <a name="troubleshooting"></a>solución de problemas

Hybrid Runbook Worker depende de un agente para comunicarse con su cuenta de Automation para registrar el trabajo, recibir trabajos de runbook e informar del estado. En el caso de Windows, este agente es Microsoft Monitoring Agent. En el caso de Linux, es el agente de OMS para Linux. Si se produce un error de registro del trabajo, estas son algunas de las causas posibles:

### <a name="the-hybrid-worker-is-behind-a-proxy-or-firewall"></a>Hybrid Worker está detrás de un firewall o proxy.

Compruebe que el equipo tenga acceso saliente a *.azure-automation.net en el puerto 443.

### <a name="the-computer-the-hybrid-worker-is-running-on-has-less-than-the-minimum-hardware-requirements"></a>El equipo en el que se está ejecutando Hybrid Worker no cumple los requisitos mínimos de hardware.

Los equipos que ejecutan Hybrid Runbook Worker deben cumplir los requisitos mínimos de hardware antes de indicar que hospede esta característica. De lo contrario, en función del uso de recursos de otros procesos en segundo plano y la contención provocada por runbooks durante la ejecución, el equipo estará sobreutilizado y provocará retrasos o tiempos de espera en los trabajos de runbook.

Confirme que el equipo designado para ejecutar la característica Hybrid Runbook Worker cumple los requisitos mínimos de hardware. Si es así, supervise el uso de la CPU y de memoria para determinar las posibles correlaciones entre el rendimiento de los procesos de Hybrid Runbook Worker y Windows. Si hay presión de memoria o de la CPU, esto puede indicar que se deben actualizar o agregar procesadores adicionales, o aumentar la memoria para resolver el cuello de botella de recursos y el error. También puede seleccionar un recurso de proceso diferente que pueda admitir los requisitos mínimos y escalarse cuando las demandas de trabajo indiquen que es necesario un aumento.

Para obtener información adicional sobre cómo solucionar problemas de un sistema operativo específico, consulte [Hybrid Runbook Worker de Linux](automation-linux-hrw-install.md#troubleshooting) o [Hybrid Runbook Worker de Windows](automation-windows-hrw-install.md#troubleshooting).

## <a name="next-steps"></a>Pasos siguientes

Revise la [ejecución de runbooks en Hybrid Runbook Worker](automation-hrw-run-runbooks.md) para más información sobre cómo configurar los runbooks para automatizar los procesos del centro de datos local o en otro entorno de nube.
