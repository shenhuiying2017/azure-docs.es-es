---
title: "Incorporación de runbooks de Azure Automation a los planes de recuperación en Site Recovery | Microsoft Docs"
description: "Este artículo describe cómo Azure Site Recovery ahora le permite ampliar los planes de recuperación mediante Automatización de Azure para completar tareas complejas durante la recuperación en Azure"
services: site-recovery
documentationcenter: 
author: ruturaj
manager: gauravd
editor: 
ms.assetid: ecece14d-5f92-4596-bbaf-5204addb95c2
ms.service: site-recovery
ms.devlang: powershell
ms.tgt_pltfrm: na
ms.topic: article
ms.workload: required
ms.date: 02/22/2017
ms.author: ruturajd@microsoft.com
translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: 198caeea693fbc48b6e0eb1c9c8ee559e0553261
ms.lasthandoff: 03/31/2017


---
# <a name="add-azure-automation-runbooks-to-recovery-plans"></a>Incorporación de runbooks de automatización de Azure a los planes de recuperación
Este tutorial describe cómo se integra Azure Site Recovery con Automatización de Azure para proporcionar extensibilidad a los planes de recuperación. Los planes de recuperación pueden coordinar la recuperación de las máquinas virtuales protegidas mediante Azure Site Recovery para escenarios de replicación en la nube secundaria y replicación en Azure. También ayudan a realizar la recuperación **coherente y precisa**, **repetible** y **automatizada**. Si conmuta por error las máquinas virtuales en Azure, la integración con Automatización de Azure amplía los planes de recuperación y le ofrece la capacidad de ejecutar runbooks, lo que proporciona tareas de automatización eficaces.

Si aún no ha oído hablar de Azure Automation, suscríbase [aquí](https://azure.microsoft.com/services/automation/) y descargue sus scripts de ejemplo [aquí](https://azure.microsoft.com/documentation/scripts/). Obtenga más información sobre [Azure Site Recovery](https://azure.microsoft.com/services/site-recovery/) y cómo llevar a cabo la recuperación en Azure con los planes de recuperación [aquí](https://azure.microsoft.com/blog/?p=166264).

En este tutorial, se explica cómo integrar runbooks de Azure Automation en planes de recuperación. Se automatizarán tareas sencillas que anteriormente requerían intervención manual y se enseñará a convertir una recuperación de varios pasos en una acción de recuperación con un solo clic.

## <a name="customize-the-recovery-plan"></a>Personalización de planes de recuperación
1. Comenzaremos abriendo la hoja de recursos del plan de recuperación. Puede ver que el plan de recuperación tiene agregadas dos máquinas virtuales para realizar el proceso de recuperación.

    ![](media/site-recovery-runbook-automation-new/essentials-rp.PNG)
- - -
1. Haga clic en el botón Personalizar para empezar a agregar runbooks. 

    ![](media/site-recovery-runbook-automation-new/customize-rp.PNG)


1. Haga clic con el botón derecho en el grupo de inicio 1 y seleccione "Agregar acción posterior".
2. Elija un script en la nueva hoja.
3. Denomine al script "Hello World".
4. Elija un nombre para la cuenta de automatización 
    >[!NOTE]
    > La cuenta de Automation puede estar en cualquier ubicación geográfica de Azure, pero tiene que residir en la misma suscripción que el almacén de Site Recovery.
    
5. Seleccione un runbook de la cuenta de automatización. Dicho runbook es el script que se ejecutará durante la ejecución del plan de recuperación después de la recuperación del primer grupo.

    ![](media/site-recovery-runbook-automation-new/update-rp.PNG)
6. Haga clic en Aceptar para guardar el script. El script se agrega al grupo de acción posterior de Grupo 1: Inicio.

    ![](media/site-recovery-runbook-automation-new/addedscript-rp.PNG)


## <a name="salient-points-of-adding-a-script"></a>Aspectos destacados del proceso de agregar un script
1. Puede hacer clic con el botón derecho en el script y elegir "eliminar paso " o "actualizar script ".
2. Un script puede ejecutarse en Azure durante la conmutación por error de un entorno local a Azure. Asimismo, puede hacerlo como script del primario antes del apagado: durante la conmutación por recuperación de Azure a un entorno local.
3. Cuando se ejecuta un script, inserta un contexto del plan de recuperación.

A continuación se muestra un ejemplo de la variable de contexto.

        {"RecoveryPlanName":"hrweb-recovery",

        "FailoverType":"Test",

        "FailoverDirection":"PrimaryToSecondary",

        "GroupId":"1",

        "VmMap":{"7a1069c6-c1d6-49c5-8c5d-33bfce8dd183":

                { "SubscriptionId":"7a1111111-c1d6-49c5-8c5d-111ce8dd183",
                
                "ResourceGroupName":"ContosoRG",
                
                "CloudServiceName":"pod02hrweb-Chicago-test",

                "RoleName":"Fabrikam-Hrweb-frontend-test",
                
                "RecoveryPointId":"TimeStamp"}

                }

        }


La tabla siguiente contiene el nombre y la descripción de cada una de las variables del contexto.

| **Nombre de la variable** | **Descripción** |
| --- | --- |
| RecoveryPlanName |Nombre del plan que se va a ejecutar. Esta variable le ayuda a realizar diferentes acciones basadas en el nombre y en si se reutiliza el script |
| FailoverType |Especifica si la conmutación por error se probó y si se planeó o no. |
| FailoverDirection |Especifica si la recuperación es al principal o secundario. |
| GroupID |Identifica el número de grupo dentro del plan de recuperación cuando se ejecuta el plan. |
| VmMap |Tabla con todas las máquinas virtuales del grupo. |
| Clave de VMMap |Clave única (GUID) para cada máquina virtual. Es el mismo que el identificador de VMM de la máquina virtual, si procede. |
| SubscriptionId |Identificador de la suscripción de Azure en la que se crea la máquina virtual. |
| RoleName |Nombre de la máquina virtual de Azure que se está recuperando. |
| CloudServiceName |Nombre del servicio en la nube de Azure con el que se crea la máquina virtual. |
| ResourceGroupName|El nombre del grupo de recursos de Azure con el que se crea la máquina virtual. |
| RecoveryPointId|Marca de tiempo en la que se recupera la máquina virtual. |

También debe asegurarse de que se han agregado los siguientes módulos a la cuenta de Automation. Todos los módulos deben ser de versiones compatibles. Una manera sencilla de que sean compatibles es asegurarse de que todos los módulos tienen la versión más reciente disponible.
* AzureRM.profile
* AzureRM.Resources
* AzureRM.Automation
* AzureRM.Network
* AzureRM.Compute


### <a name="accessing-all-vms-of-the-vmmap-in-a-loop"></a>Acceso a todas las máquinas virtuales de VmMap en un bucle
Utilice el siguiente fragmento de código para crear un bucle con todas las máquinas virtuales de VmMap.

```
    $VMinfo = $RecoveryPlanContext.VmMap | Get-Member | Where-Object MemberType -EQ NoteProperty | select -ExpandProperty Name
    $vmMap = $RecoveryPlanContext.VmMap
     foreach($VMID in $VMinfo)
     {
         $VM = $vmMap.$VMID                
             if( !(($VM -eq $Null) -Or ($VM.ResourceGroupName -eq $Null) -Or ($VM.RoleName -eq $Null))) {
             #this check is to ensure that we skip when some data is not available else it will fail
         Write-output "Resource group name ", $VM.ResourceGroupName
         Write-output "Rolename " = $VM.RoleName
         }
     }

```

> [!NOTE]
> Los valores Resource Group name y Rolename están vacíos cuando la secuencia de comandos es una acción anterior a un grupo de arranque. Los valores solo se llenan si la máquina virtual del grupo realiza correctamente la conmutación por error y el script es una acción posterior del grupo de arranque.

## <a name="using-the-same-automation-runbook-with-multiple-recovery-plans"></a>Uso del mismo runbook de Automation con varios planes de recuperación

Un solo script se puede usar en varios planes de recuperación gracias a las variables externas. Las [variables de Azure Automation](../automation/automation-variables.md) se pueden usar para almacenar los parámetros que se pueden pasar en la ejecución de un plan de recuperación. Al usar como prefijo de la variable el nombre del plan de recuperación, se pueden crear variables individual para cada plan de recuperación y usarlas como parámetro. El parámetro se puede cambiar sin cambiar el script y hacer que el script funcione de otra manera.

### <a name="using-simple-string-variables-in-runbook-script"></a>Uso de variables de cadena simples en un script de runbook

Considere un script que tome la entrada de un NSG y la aplique a las máquinas virtuales de un plan de recuperación.

Para que el script "sepa" qué plan de recuperación ejecuta, se puede utilizar el contexto del plan de recuperación.

```
    workflow AddPublicIPAndNSG {
        param (
              [parameter(Mandatory=$false)]
              [Object]$RecoveryPlanContext
        )

        $RPName = $RecoveryPlanContext.RecoveryPlanName
```

Para aplicar un NSG existente, se necesitan el nombre y el grupo de recursos del NSG. Estas variables se proporcionan como entrada a los scripts del plan de recuperación. Para ello, cree dos variables en los recursos de las cuentas de Automation y utilice como prefijo el nombre del plan de recuperación para el que va a crear los parámetros.

1. Cree una variable para almacenar el nombre del NSG. Use el nombre del plan de recuperación como prefijo.
    ![Crear la variable del nombre de NSG](media/site-recovery-runbook-automation-new/var1.png)

2. Cree una variable para almacenar el nombre del grupo de recursos de NSG. Use el nombre del plan de recuperación como prefijo.
    ![Crear nombre del grupo de recursos de NSG](media/site-recovery-runbook-automation-new/var2.png)


En el script, adquiera los valores de las variables mediante el siguiente código de referencia:

```
    $NSGValue = $RecoveryPlanContext.RecoveryPlanName + "-NSG"
    $NSGRGValue = $RecoveryPlanContext.RecoveryPlanName + "-NSGRG"

    $NSGnameVar = Get-AutomationVariable -Name $NSGValue 
    $RGnameVar = Get-AutomationVariable -Name $NSGRGValue
```

A continuación puede utilizar las variables en el runbook y aplicar el NSG a la interfaz de red de la máquina virtual conmutada por error.

```
     InlineScript { 
         if (($Using:NSGname -ne $Null) -And ($Using:NSGRGname -ne $Null)) {
            $NSG = Get-AzureRmNetworkSecurityGroup -Name $Using:NSGname -ResourceGroupName $Using:NSGRGname
            Write-output $NSG.Id
            #Apply the NSG to a network interface
            #$vnet = Get-AzureRmVirtualNetwork -ResourceGroupName TestRG -Name TestVNet
            #Set-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name FrontEnd `
            #  -AddressPrefix 192.168.1.0/24 -NetworkSecurityGroup $NSG
        }
    }
```

Para cada plan de recuperación, cree variables independientes para poder volver a usar el script y agregue como prefijo el nombre del plan de recuperación. [Aquí se proporciona](https://gallery.technet.microsoft.com/Add-Public-IP-and-NSG-to-a6bb8fee) un script completo de un extremo a otro para este ejemplo.


### <a name="using-complex-variable-to-store-more-information"></a>Uso de una variable compleja para almacenar más información

Considere un escenario en el que solo desea un script que active una IP pública en determinadas máquinas virtuales. Otro ejemplo sería aplicar diferentes NSG en distintas máquinas virtuales (no todas). Este script se podría volver a usar en cualquier otro plan de recuperación. Cada plan de recuperación puede tener un número variable de máquinas virtuales (por ejemplo, una recuperación de Sharepoint tiene dos servidores front-end, mientras que una aplicación de LOB simple tiene solo un front-end). Para lograr este resultado, es imposible crear variables independientes por cada plan de recuperación. Aquí se usa una técnica nueva y se crea una [variable compleja](https://msdn.microsoft.com/library/dn913767.aspx?f=255&MSPPError=-2147217396) en los recursos de la cuenta de Azure Automation mediante la especificación de varios valores. Necesita Azure PowerShell para ejecutar los siguientes pasos.

1. En Azure PowerShell, inicie sesión en su suscripción.

    ```
        login-azurermaccount
        $sub = Get-AzureRmSubscription -Name <SubscriptionName>
        $sub | Select-AzureRmSubscription
    ```

2. Para almacenar los parámetros, cree la variable complejo y asígnele el nombre del plan de recuperación.

    ```
        $VMDetails = @{"VMGUID"=@{"ResourceGroupName"="RGNameOfNSG";"NSGName"="NameOfNSG"};"VMGUID2"=@{"ResourceGroupName"="RGNameOfNSG";"NSGName"="NameOfNSG"}}
        New-AzureRmAutomationVariable -ResourceGroupName <RG of Automation Account> -AutomationAccountName <AA Name> -Name <RecoveryPlanName> -Value $VMDetails -Encrypted $false
    ```

    En esta variable compleja, **VMDetails* es el identificador de la máquina virtual protegida. Esto se puede encontrar en las propiedades de la máquina virtual en el portal. Aquí hemos creado una variable que almacene los detalles de las dos máquinas virtuales.

    ![El identificador de la máquina virtual que se va a usar como GUID](media/site-recovery-runbook-automation-new/vmguid.png)

3. Si se encuentra alguna instancia del VMGUID dado en el contexto del plan virtual, utilice esta variable en el runbook y aplique el NSG en la máquina virtual.

    ```
        $VMDetailsObj = Get-AutomationVariable -Name $RecoveryPlanContext.RecoveryPlanName 
    ```

4. En el runbook, recorra en bucle las máquinas virtuales del contexto del plan de recuperación para comprobar si la máquina virtual también existe en **$VMDetailsObj**. En caso afirmativo, aplique el NSG, para lo que debe acceder a las propiedades de la variable.
    ```
        $VMinfo = $RecoveryPlanContext.VmMap | Get-Member | Where-Object MemberType -EQ NoteProperty | select -ExpandProperty Name
        $vmMap = $RecoveryPlanContext.VmMap
           
        foreach($VMID in $VMinfo) {
            Write-output $VMDetailsObj.value.$VMID
            
            if ($VMDetailsObj.value.$VMID -ne $Null) { #If the VM exists in the context, this will not b Null
                $VM = $vmMap.$VMID
                # Access the properties of the variable
                $NSGname = $VMDetailsObj.value.$VMID.'NSGName'
                $NSGRGname = $VMDetailsObj.value.$VMID.'NSGResourceGroupName'

                # Add code to apply the NSG properties to the VM
            }
        }
    ```

El mismo script se puede utilizar con planes de recuperación diferentes y proporcionar parámetros diferentes mediante el almacenamiento del valor correspondiente a planes de recuperación diferentes en otra variable.

## <a name="sample-scripts"></a>Scripts de ejemplo
Implemente scripts de ejemplo en su cuenta de Automation mediante el siguiente botón Implementar en Azure.

[![Implementación en Azure](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/c4803408-340e-49e3-9a1f-0ed3f689813d.png)](https://aka.ms/asr-automationrunbooks-deploy)

También puede ver un breve vídeo sobre la recuperación de una aplicación de WordPress de dos niveles en Azure.

> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/One-click-failover-of-a-2-tier-WordPress-application-using-Azure-Site-Recovery/player]



## <a name="additional-resources"></a>Recursos adicionales
[Cuenta de ejecución del servicio Azure Automation](../automation/automation-sec-configure-azure-runas-account.md)

[Información general sobre Automatización de Azure](http://msdn.microsoft.com/library/azure/dn643629.aspx "Información general sobre Automatización de Azure")

[Scripts de Automatización de Azure de ejemplo](http://gallery.technet.microsoft.com/scriptcenter/site/search?f\[0\].Type=User&f\[0\].Value=SC%20Automation%20Product%20Team&f\[0\].Text=SC%20Automation%20Product%20Team "Scripts de Automatización de Azure de ejemplo")

