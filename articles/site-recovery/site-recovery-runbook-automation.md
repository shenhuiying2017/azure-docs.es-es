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
ms.date: 02/06/2017
ms.author: ruturajd@microsoft.com
translationtype: Human Translation
ms.sourcegitcommit: 44b6ff6e588d529fd833a4a7fdd61df7e933ddd8
ms.openlocfilehash: b88974ef713211a40b52aafab1b079ed8dbfec49


---
# <a name="add-azure-automation-runbooks-to-recovery-plans"></a>Incorporación de runbooks de automatización de Azure a los planes de recuperación
Este tutorial describe cómo se integra Azure Site Recovery con Automatización de Azure para proporcionar extensibilidad a los planes de recuperación. Los planes de recuperación pueden coordinar la recuperación de las máquinas virtuales protegidas mediante Azure Site Recovery para escenarios de replicación en la nube secundaria y replicación en Azure. También ayudan a realizar la recuperación **coherente y precisa**, **repetible** y **automatizada**. Si conmuta por error las máquinas virtuales en Azure, la integración con Automatización de Azure amplía los planes de recuperación y le ofrece la capacidad de ejecutar runbooks, lo que proporciona tareas de automatización eficaces.

Si aún no ha oído hablar de Azure Automation, suscríbase [aquí](https://azure.microsoft.com/services/automation/) y descargue sus scripts de ejemplo [aquí](https://azure.microsoft.com/documentation/scripts/). Obtenga más información sobre [Azure Site Recovery](https://azure.microsoft.com/services/site-recovery/) y cómo llevar a cabo la recuperación en Azure con los planes de recuperación [aquí](https://azure.microsoft.com/blog/?p=166264).

En este tutorial, veremos cómo se pueden integrar runbooks de Automatización de Azure en planes de recuperación. Automatizaremos tareas sencillas que anteriormente requerían una intervención manual y veremos cómo convertir una recuperación de varios paso en una acción de recuperación de un solo clic. También veremos cómo puede solucionar problemas de un script sencillo si algo va mal.

## <a name="customize-the-recovery-plan"></a>Personalización de planes de recuperación
1. Comenzaremos abriendo la hoja de recursos del plan de recuperación. Puede ver que el plan de recuperación tiene agregadas dos máquinas virtuales para realizar el proceso de recuperación.

    ![](media/site-recovery-runbook-automation-new/essentials-rp.PNG)
- - -
1. Haga clic en el botón Personalizar para empezar a agregar runbooks. Esta acción abrirá la hoja de personalización de planes de recuperación.

    ![](media/site-recovery-runbook-automation-new/customize-rp.PNG)


1. Haga clic con el botón derecho en el grupo de inicio 1 y seleccione Agregar acción posterior.
2. Elija un script en la nueva hoja.
3. Denomine al script "Hello World".
4. Elija un nombre para la cuenta de automatización (será la cuenta de Azure Automation). Recuerde que esta cuenta puede estar en cualquier ubicación geográfica de Azure, pero tiene que residir en la misma suscripción que el almacén de Site Recovery.
5. Seleccione un runbook de la cuenta de automatización. Se tratará del script que se ejecutará durante la ejecución del plan de recuperación después de la recuperación del primer grupo.

    ![](media/site-recovery-runbook-automation-new/update-rp.PNG)
6. Haga clic en Aceptar para guardar el script. Esta acción agregará el script al grupo de acción posterior del grupo de inicio 1.

    ![](media/site-recovery-runbook-automation-new/addedscript-rp.PNG)


## <a name="salient-points-of-adding-a-script"></a>Aspectos destacados del proceso de agregar un script
1. Puede haga clic con el botón derecho en el script y seleccionar Eliminar paso o Actualizar script.
2. Un script puede ejecutarse en Azure durante la conmutación por error de un entorno local a Azure. Asimismo, puede hacerlo como script del primario antes del apagado: durante la conmutación por recuperación de Azure a un entorno local.
3. Cuando se ejecuta un script, se insertará un contexto de plan de recuperación.

A continuación se muestra un ejemplo del aspecto de la variable de contexto.

        {"RecoveryPlanName":"hrweb-recovery",

        "FailoverType":"Test",

        "FailoverDirection":"PrimaryToSecondary",

        "GroupId":"1",

        "VmMap":{"7a1069c6-c1d6-49c5-8c5d-33bfce8dd183":

                {"CloudServiceName":"pod02hrweb-Chicago-test",

                "RoleName":"Fabrikam-Hrweb-frontend-test"}

                }

        }


La tabla siguiente contiene el nombre y la descripción de cada variable en el contexto.

| **Nombre de la variable** | **Descripción** |
| --- | --- |
| RecoveryPlanName |Nombre del plan que se va a ejecutar. Permite realizar acciones según el nombre mediante el mismo script. |
| FailoverType |Especifica si la conmutación por error se probó y si se planeó o no. |
| FailoverDirection |Especifica si la recuperación es al principal o secundario. |
| GroupID |Identifica el número de grupo dentro del plan de recuperación cuando se ejecuta el plan. |
| VmMap |Tabla con todas las máquinas virtuales del grupo. |
| Clave de VMMap |Clave única (GUID) para cada máquina virtual. Es el mismo que el identificador de VMM de la máquina virtual, si procede. |
| RoleName |Nombre de la máquina virtual de Azure que se está recuperando. |
| CloudServiceName |Nombre del servicio en la nube de Azure con el que se crea la máquina virtual. |
| CloudServiceName (en el modelo de implementación de Resource Manager) |El nombre del grupo de recursos de Azure con el que se crea la máquina virtual. |

## <a name="using-complex-variables-per-recovery-plan"></a>Uso de variables complejas por plan de recuperación
A veces, un runbook necesita más información que el parámetro RecoveryPlanContext. No hay ningún mecanismo de primera clase para pasar un parámetro a un runbook. Sin embargo, si quiere utilizar el mismo script a través de varios planes de recuperación, puede emplear la variable RecoveryPlanContext y la siguiente técnica experimental para usar una variable compleja de Azure Automation en un runbook. En el ejemplo siguiente se muestra cómo puede crear tres recursos de variables complejas diferentes y usarlos en el runbook basado en el nombre del plan de recuperación.

Supongamos que quiere usar 3 parámetros más en un runbook. Vamos a codificarlos en un formato JSON {"Var1":"testautomation","Var2":"Unplanned","Var3":"PrimaryToSecondary"}.

Use la [variable compleja AA](../automation/automation-variables.md#variable-types) para crear un nuevo recurso de Automation.
Denomine a la variable "<RecoveryPlanName>- params".
Aquí puede utilizar la referencia para crear una [variable compleja](https://msdn.microsoft.com/library/dn913767.aspx?f=255&MSPPError=-2147217396).

Para planes de recuperación diferentes, asigne a la variable los siguientes nombres:

1. recoveryPlanName1 >-params
2. recoveryPlanName2 >-params
3. recoveryPlanName3 >-params

Ahora, en el script, haga referencia a los parámetros cuando realice los siguientes pasos:

1. Obtenga el nombre de RP de la variable $rpname = $Recoveryplancontex.
2. Obtenga el recurso de $paramValue = "$($rpname)-params".
3. Úselo como una variable compleja para el plan de recuperación llamando a Get-AzureAutomationVariable [-AutomationAccountName] <String> -Name $paramValue.

Por ejemplo, para obtener el parámetro o la variable complejos para el plan de recuperación SharepointApp, cree una variable compleja de Azure Automation denominada "SharepointApp-params".

Utilícela en el plan de recuperación extrayendo la variable del recurso mediante la instrucción Get-AzureAutomationVariable [-AutomationAccountName] <String> [-Name] $paramValue. [Consulte este artículo para obtener más información](https://msdn.microsoft.com/library/dn913772.aspx).

De este modo, puede utilizarse el mismo script para planes de recuperación diferentes almacenando la variable compleja concreta de los recursos.

## <a name="sample-scripts"></a>Scripts de ejemplo
Para ver un repositorio de scripts que puede importar directamente en su cuenta de automatización, consulte el [repositorio OMS de Kristian Nese de scripts](https://github.com/krnese/AzureDeploy/tree/master/OMS/MSOMS/Solutions/asrautomation).

Aquí, el script es una plantilla de Azure Resource Manager que implementará todos los siguientes scripts:

* Grupo de seguridad de red

El runbook NSG asignará direcciones IP públicas a cada máquina virtual del plan de recuperación y conectará sus adaptadores de red virtual a un grupo de seguridad de red que permitirá la comunicación predeterminada.

* PublicIP

El runbook PublicIP asignará direcciones IP públicas a cada máquina virtual del plan de recuperación. El acceso a las máquinas y las aplicaciones dependerá de la configuración de firewall de cada invitado.

* CustomScript

El runbook CustomScript asignará direcciones IP públicas a cada máquina virtual del plan de recuperación. Asimismo, instalará una extensión de script personalizado que extraerá el script al que hizo referencia durante la implementación de la plantilla.

* NSGwithCustomScript

El runbook NSGwithCustomScript asignará direcciones IP públicas a cada máquina virtual del plan de recuperación. Asimismo, instalará una extensión de script personalizado y conectará los adaptadores de red virtual a un NSG, de modo que se permitirán las comunicaciones entrante y saliente predeterminadas para habilitar el acceso remoto.

## <a name="additional-resources"></a>Recursos adicionales
[Cuenta de ejecución del servicio Azure Automation](../automation/automation-sec-configure-azure-runas-account.md)

[Información general sobre Automatización de Azure](http://msdn.microsoft.com/library/azure/dn643629.aspx "Información general sobre Automatización de Azure")

[Scripts de Automatización de Azure de ejemplo](http://gallery.technet.microsoft.com/scriptcenter/site/search?f\[0\].Type=User&f\[0\].Value=SC%20Automation%20Product%20Team&f\[0\].Text=SC%20Automation%20Product%20Team "Scripts de Automatización de Azure de ejemplo")



<!--HONumber=Jan17_HO5-->


