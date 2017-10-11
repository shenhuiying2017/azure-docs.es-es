---
title: "Incorporación de runbooks de Azure Automation a los planes de recuperación en Azure Site Recovery | Microsoft Docs"
description: "Obtenga información sobre cómo Azure Site Recovery puede ayudarle a ampliar los planes de recuperación mediante Azure Automation. Aprenda a realizar tareas complejas durante la recuperación en Azure."
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
ms.workload: storage-backup-recovery
ms.date: 06/23/2017
ms.author: ruturajd@microsoft.com
ms.openlocfilehash: 064a6782970b950543f93c24800998c1f104c8df
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/29/2017
---
# <a name="add-azure-automation-runbooks-to-recovery-plans"></a>Incorporación de runbooks de Azure Automation a los planes de recuperación
En este artículo se explica cómo se integra Azure Site Recovery con Azure Automation para ayudarle a ampliar los planes de recuperación. Los planes de recuperación pueden organizar la recuperación de máquinas virtuales protegidas con Site Recovery. Los planes de recuperación funcionan para la replicación en una nube secundaria y para la replicación en Azure. Los planes de recuperación además ayudan a que la recuperación sea **coherente y precisa**, **repetible** y **automatizada**. Si conmuta por error las máquinas virtuales en Azure, la integración con Azure Automation amplía los planes de recuperación. Se puede usar para ejecutar runbooks, que ofrecen eficaces tareas de automatización.

Si no está familiarizado con Azure Automation, puede [registrarse](https://azure.microsoft.com/services/automation/) y [descargar scripts de muestra](https://azure.microsoft.com/documentation/scripts/). Para más información y para aprender a organizar la recuperación en Azure mediante [planes de recuperación](https://azure.microsoft.com/blog/?p=166264), vea [Azure Site Recovery](https://azure.microsoft.com/services/site-recovery/).

En este artículo se explica cómo integrar runbooks de Azure Automation en los planes de recuperación. Se usan ejemplos para automatizar tareas básicas que anteriormente requerían intervención manual. También se explica cómo convertir una recuperación de varios pasos en una acción de recuperación de un solo clic.

## <a name="customize-the-recovery-plan"></a>Personalización de planes de recuperación
1. Vaya a la hoja de recursos de planes de recuperación de **Site Recovery**. En este ejemplo, el plan de recuperación tiene agregadas dos máquinas virtuales para la recuperación. Para empezar a agregar un runbook, haga clic en la pestaña **Personalizar**.

    ![Clic en la pestaña Personalizar](media/site-recovery-runbook-automation-new/essentials-rp.png)


2. Haga clic con el botón derecho en **Grupo 1: Iniciar** y luego seleccione **Agregar acción posterior**.

    ![Clic con el botón derecho en Grupo 1: Iniciar y adición de acción posterior](media/site-recovery-runbook-automation-new/customize-rp.png)

3. Haga clic en **Elegir un script**.

4. En la hoja **Actualizar acción**, ponga el nombre **Hello World** al script.

    ![Hoja Actualizar acción](media/site-recovery-runbook-automation-new/update-rp.png)

5. Escriba un nombre de cuenta de Automation.
    >[!NOTE]
    > La cuenta de Automation puede estar en cualquier región de Azure. La cuenta de Automation debe estar en la misma suscripción que el almacén de Azure Site Recovery.

6. En la cuenta de Automation, seleccione un runbook. Este runbook es el script que se ejecuta durante la ejecución del plan de recuperación, después de la recuperación del primer grupo.

7. Para guardar el script, haga clic en **Aceptar**. El script se agrega a **Grupo 1: Pasos posteriores**.

    ![Acción posterior Grupo 1: Iniciar](media/site-recovery-runbook-automation-new/addedscript-rp.PNG)


## <a name="considerations-for-adding-a-script"></a>Consideraciones para agregar un script

* Para obtener opciones para **eliminar un paso** o **actualizar el script**, haga clic con el botón derecho en el script.
* Un script se puede ejecutar en Azure durante la conmutación por error desde un equipo local en Azure. También puede ejecutarse en Azure como un script de sitio principal antes de apagar, durante la conmutación por recuperación desde Azure en un equipo local.
* Cuando se ejecuta un script, inserta un contexto del plan de recuperación. El ejemplo siguiente muestra una variable de contexto:

    ```
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
    ```

    La tabla siguiente indica el nombre y la descripción de cada una de las variables del contexto.

    | **Nombre de la variable** | **Descripción** |
    | --- | --- |
    | RecoveryPlanName |Nombre del plan que se va a ejecutar. Esta variable le ayuda a realizar diferentes acciones según el nombre del plan de recuperación. También puede volver a usar el script. |
    | FailoverType |Especifica si la conmutación por error es una prueba, planeada o no. |
    | FailoverDirection |Especifica si la recuperación es en un sitio principal o secundario. |
    | GroupID |Identifica el número de grupo del plan de recuperación cuando este se está ejecutando. |
    | VmMap |Matriz de todas las máquinas virtuales del grupo. |
    | Clave de VMMap |Clave única (GUID) para cada máquina virtual. Es igual que el identificador de Azure Virtual Machine Manager (VMM) de la máquina virtual, si procede. |
    | SubscriptionId |Identificador de la suscripción de Azure en la que se ha creado la máquina virtual. |
    | RoleName |Nombre de la máquina virtual de Azure que se está recuperando. |
    | CloudServiceName |Nombre del servicio en la nube de Azure en el que se ha creado la máquina virtual. |
    | ResourceGroupName|Nombre del grupo de recursos de Azure en el que se ha creado la máquina virtual. |
    | RecoveryPointId|Marca de tiempo de cuando se recupera la máquina virtual. |

* Asegúrese de que la cuenta de Automation tenga los siguientes módulos:
    * AzureRM.profile
    * AzureRM.Resources
    * AzureRM.Automation
    * AzureRM.Network
    * AzureRM.Compute

Todos los módulos deben ser de versiones compatibles. Una manera sencilla de garantizar que todos los módulos sean compatibles es usar las versiones más recientes de todos los módulos.

### <a name="access-all-vms-of-the-vmmap-in-a-loop"></a>Acceso a todas las máquinas virtuales de VMMap en un bucle
Use el siguiente código para crear un bucle con todas las máquinas virtuales de Microsoft VMMap:

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
> Los valores de nombre de grupo de recursos y nombre de rol están vacíos cuando el script es una acción anterior a un grupo de arranque. Los valores se rellenan solo si se realiza correctamente la conmutación por error de la máquina virtual de ese grupo. El script es una acción posterior del grupo de arranque.

## <a name="use-the-same-automation-runbook-in-multiple-recovery-plans"></a>Uso del mismo runbook de Automation en varios planes de recuperación

Puede usar un solo script en varios planes de recuperación gracias a las variables externas. Las [variables de Azure Automation](../automation/automation-variables.md) se pueden usar para almacenar los parámetros que se pueden pasar para la ejecución de un plan de recuperación. Al agregar el nombre del plan de recuperación como prefijo a la variable, puede crear variables individuales para cada plan de recuperación. Luego, use las variables como parámetros. Puede cambiar un parámetro sin cambiar el script y aun así cambiar la forma en que el script funciona.

### <a name="use-a-simple-string-variable-in-a-runbook-script"></a>Uso de una variable de cadena simple en un script de runbook

En este ejemplo, un script toma la entrada de un grupo de seguridad de red (NSG) y la aplica a las máquinas virtuales de un plan de recuperación.

Para que el script detecte qué plan de recuperación se está ejecutando, use el contexto del plan de recuperación:

```
workflow AddPublicIPAndNSG {
    param (
          [parameter(Mandatory=$false)]
          [Object]$RecoveryPlanContext
    )

    $RPName = $RecoveryPlanContext.RecoveryPlanName
```

Para aplicar un NSG existente, debe conocer el nombre del NSG y el nombre del grupo de recursos del NSG. Use estas variables como entradas para los scripts del plan de recuperación. Para ello, cree dos variables en los activos de la cuenta de Automation. Agregue el nombre del plan de recuperación para el que va a crear los parámetros como prefijo al nombre de la variable.

1. Cree una variable para almacenar el nombre del NSG. Agregue un prefijo al nombre de la variable mediante el nombre del plan de recuperación.

    ![Creación de una variable de nombre de NSG](media/site-recovery-runbook-automation-new/var1.png)

2. Cree una variable para almacenar el nombre del grupo de recursos del NSG. Agregue un prefijo al nombre de la variable mediante el nombre del plan de recuperación.

    ![Creación de un nombre de grupo de recursos de NSG](media/site-recovery-runbook-automation-new/var2.png)


3.  En el script, use el siguiente código de referencia para obtener los valores de variable:

    ```
    $NSGValue = $RecoveryPlanContext.RecoveryPlanName + "-NSG"
    $NSGRGValue = $RecoveryPlanContext.RecoveryPlanName + "-NSGRG"

    $NSGnameVar = Get-AutomationVariable -Name $NSGValue
    $RGnameVar = Get-AutomationVariable -Name $NSGRGValue
    ```

4.  Use las variables del runbook para aplicar el NSG a la interfaz de red de la máquina virtual conmutada por error:

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

Para cada plan de recuperación, cree variables independientes para poder volver a usar el script. Agregue un prefijo mediante el nombre del plan de recuperación. Para obtener un script completo de un extremo a otro para este escenario, vea [Add a public IP and NSG to VMs during test failover of a Site Recovery recovery plan (Agregar una dirección IP pública y un NSG a las máquinas virtuales durante la conmutación por error de prueba de un plan de recuperación de Site Recovery)](https://gallery.technet.microsoft.com/Add-Public-IP-and-NSG-to-a6bb8fee).


### <a name="use-a-complex-variable-to-store-more-information"></a>Uso de una variable compleja para almacenar más información

Considere un escenario en el que quiera que un único script active una dirección IP pública en máquinas virtuales concretas. En otro escenario, podría querer aplicar diferentes NSG en diferentes máquinas virtuales (no en todas las máquinas virtuales). Puede crear un script que se pueda volver a usar para cualquier plan de recuperación. Cada plan de recuperación puede tener un número variable de máquinas virtuales. Por ejemplo, una recuperación de SharePoint tiene dos servidores front-end. Una aplicación básica de línea de negocio (LOB) tiene solo un servidor front-end. No puede crear variables independientes para cada plan de recuperación. 

En el siguiente ejemplo se usa una técnica nueva y se crea una [variable compleja](https://msdn.microsoft.com/library/dn913767.aspx?f=255&MSPPError=-2147217396) en los activos de la cuenta de Azure Automation. Para ello se especifican varios valores. Debe usar Azure PowerShell para realizar los siguientes pasos:

1. En PowerShell, inicie sesión en la suscripción de Azure:

    ```
    login-azurermaccount
    $sub = Get-AzureRmSubscription -Name <SubscriptionName>
    $sub | Select-AzureRmSubscription
    ```

2. Para almacenar los parámetros, cree la variable compleja mediante el plan de recuperación:

    ```
    $VMDetails = @{"VMGUID"=@{"ResourceGroupName"="RGNameOfNSG";"NSGName"="NameOfNSG"};"VMGUID2"=@{"ResourceGroupName"="RGNameOfNSG";"NSGName"="NameOfNSG"}}
        New-AzureRmAutomationVariable -ResourceGroupName <RG of Automation Account> -AutomationAccountName <AA Name> -Name <RecoveryPlanName> -Value $VMDetails -Encrypted $false
    ```

3. En esta variable compleja, **VMDetails** es el identificador de la máquina virtual protegida. Para obtener el identificador de la máquina virtual, en Azure Portal, vea las propiedades de la máquina virtual. En la captura de pantalla siguiente se muestra una variable que almacena los detalles de dos máquinas virtuales:

    ![Empleo del identificador de la máquina virtual como GUID](media/site-recovery-runbook-automation-new/vmguid.png)

4. Use esta variable en el runbook. Si el GUID indicado de la máquina virtual se encuentra en el contexto del plan de recuperación, aplique el NSG en la máquina virtual:

    ```
    $VMDetailsObj = Get-AutomationVariable -Name $RecoveryPlanContext.RecoveryPlanName
    ```

4. En el runbook, cree un bucle con las máquinas virtuales del contexto del plan de recuperación. Compruebe si la máquina virtual existe en **$VMDetailsObj**. En caso afirmativo, acceda a las propiedades de la variable para aplicar el NSG:

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

Puede usar el mismo script para distintos planes de recuperación. Escriba distintos parámetros al almacenar el valor que corresponde a un plan de recuperación en variables diferentes.

## <a name="sample-scripts"></a>Scripts de ejemplo

Para implementar scripts de ejemplo en la cuenta de Automation, haga clic en el botón **Implementar en Azure**.

[![Implementación en Azure](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/c4803408-340e-49e3-9a1f-0ed3f689813d.png)](https://aka.ms/asr-automationrunbooks-deploy)

Para obtener otro ejemplo, vea el siguiente vídeo. En él se muestra cómo recuperar una aplicación de WordPress de dos niveles en Azure:


> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/One-click-failover-of-a-2-tier-WordPress-application-using-Azure-Site-Recovery/player]



## <a name="additional-resources"></a>Recursos adicionales
* [Cuenta de ejecución del servicio Azure Automation](../automation/automation-sec-configure-azure-runas-account.md)
* [Información general sobre Azure Automation](http://msdn.microsoft.com/library/azure/dn643629.aspx "Información general sobre Azure Automation")
* [Scripts de ejemplo de Azure Automation](http://gallery.technet.microsoft.com/scriptcenter/site/search?f\[0\].Type=User&f\[0\].Value=SC%20Automation%20Product%20Team&f\[0\].Text=SC%20Automation%20Product%20Team "Scripts de ejemplo de Azure Automation")
