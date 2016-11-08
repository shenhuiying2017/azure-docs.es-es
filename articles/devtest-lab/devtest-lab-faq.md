---
title: Preguntas más frecuentes sobre Azure DevTest Labs | Microsoft Docs
description: Encuentre respuestas a preguntas comunes sobre Azure DevTest Labs
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: ''

ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/13/2016
ms.author: tarcher

---
# <a name="azure-devtest-labs-faq"></a>Preguntas más frecuentes sobre Azure DevTest Labs
En este artículo se ofrecen respuestas a algunas de las preguntas más frecuentes sobre Azure DevTest Labs.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="general"></a>General
* [Mi pregunta no está respondida aquí. ¿Qué debo hacer?](#what-if-my-question-isnt-answered-here)
* [¿Por qué debería usar Azure DevTest Labs?](#why-should-i-use-azure-devtest-labs) 
* [¿Qué significa "sin preocupaciones, autoservicio"?](#what-does-quotworry-free-self-servicequot-mean)
* [¿Cómo se usa Azure DevTest Labs?](#how-can-i-use-azure-devtest-labs) 
* [¿Cómo se factura Azure DevTest Labs?](#how-am-i-billed-for-azure-devtest-labs) 

## <a name="security"></a>Seguridad
* [¿Cuáles son los diferentes niveles de seguridad en Azure DevTest Labs?](#what-are-the-different-security-levels-in-azure-devtest-labs) 
* [¿Cómo se crea un rol para permitir que los usuarios realicen una tarea específica?](#how-do-i-create-a-role-to-allow-users-to-perform-a-specific-task) 

## <a name="ci/cd-integration-&-automation"></a>Automatización e integración de CI/CD
* [¿Se integra Azure DevTest Labs con mi cadena de herramientas de CI/CD?](#does-azure-devtest-labs-integrate-with-my-cicd-toolchain) 

## <a name="virtual-machines"></a>Máquinas virtuales
* [¿Por qué no puedo ver determinadas máquinas virtuales en la hoja Azure Virtual Machines que sí veo en Azure DevTest Labs?](#why-cant-i-see-certain-vms-in-the-azure-virtual-machines-blade-that-i-see-within-azure-devtest-labs) 
* [¿Cuál es la diferencia entre imágenes personalizadas y fórmulas?](#what-is-the-difference-between-custom-images-and-formulas) 
* [¿Cómo se crean varias máquinas virtuales desde la misma plantilla a la vez?](#how-do-i-create-multiple-vms-from-the-same-template-at-once) 
* [¿Cómo se pueden mover las máquinas virtuales de Azure existentes a mi laboratorio de Azure DevTest Labs?](#how-do-i-move-my-existing-azure-vms-into-my-azure-devtest-labs-lab) 
* [¿Se pueden conectar varios discos a las máquinas virtuales?](#can-i-attach-multiple-disks-to-my-vms) 
* [¿Cómo se puede automatizar el proceso de carga de archivos VHD para crear imágenes personalizadas?](#how-do-i-automate-the-process-of-uploading-vhd-files-to-create-custom-images) 
* [¿Cómo puedo automatizar el proceso de eliminación de todas las máquinas virtuales en mi laboratorio?](#how-can-i-automate-the-process-of-deleting-all-the-vms-in-my-lab)

## <a name="artifacts"></a>Artefactos
* [¿Qué son los artefactos?](#what-are-artifacts) 

## <a name="lab-configuration"></a>Configuración del laboratorio
* [¿Cómo se crea un laboratorio a partir de una plantilla de Azure Resource Manager?](#how-do-i-create-a-lab-from-an-azure-resource-manager-template) 
* [¿Por qué mis máquinas virtuales se crean en distintos grupos de recursos con nombres arbitrarios? ¿Se pueden modificar estos grupos de recursos o cambiar su nombre?](#why-are-my-vms-created-in-different-resource-groups-with-arbitrary-names-can-i-rename-or-modify-these-resource-groups) 
* [¿Cuántos laboratorios se pueden crear con una misma suscripción?](#how-many-labs-can-i-create-under-the-same-subscription)
* [¿Cuántas máquinas virtuales se pueden crear por laboratorio?](#how-many-vms-can-i-create-per-lab)
* [¿Cómo se puede compartir un vínculo directo a mi laboratorio?](#how-do-i-share-a-direct-link-to-my-lab)
* [¿Qué es una cuenta Microsoft?](#what-is-a-microsoft-account)

## <a name="troubleshooting"></a>Solución de problemas
* [Mi artefacto produjo errores durante la creación de la máquina virtual. ¿Cómo se soluciona este problema?](#my-artifact-failed-during-vm-creation-how-do-i-troubleshoot-it) 
* [¿Por qué mi máquina virtual existente no se guarda correctamente?](#why-isnt-my-existing-virtual-network-saving-properly)  

### <a name="what-if-my-question-isn't-answered-here?"></a>Mi pregunta no está respondida aquí. ¿Qué debo hacer?
Si su pregunta no aparece aquí, háganoslo saber y lo ayudaremos a encontrar una respuesta.

* Publique una pregunta en el [hilo de Disqus](#comments) al final de estas preguntas más frecuentes y ponerse en contacto con el equipo de Caché de Microsoft Azure y otros miembros de la comunidad con cualquier tema que tenga relación con este artículo.
* Para llegar a más público, puede publicar una pregunta en el [foro de MSDN de Azure DevTest Labs](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureDevTestLabs)y ponerse en contacto con el equipo de Azure DevTest Labs y otros miembros de la Comunidad.
* Para realizar una solicitud de característica, envíe las solicitudes y las ideas al sitio [User Voice de Azure DevTest Labs](https://feedback.azure.com/forums/320373-azure-devtest-labs).

### <a name="why-should-i-use-azure-devtest-labs?"></a>¿Por qué debería usar Azure DevTest Labs?
Azure DevTest Labs puede ahorrarle a su equipo tiempo y dinero. Los desarrolladores pueden crear sus propios entornos con distintas bases, y utilizar artefactos para implementar y configurar rápidamente las aplicaciones. Mediante imágenes personalizadas y fórmulas, las máquinas virtuales se pueden guardar como plantillas y reproducirse fácilmente. Además, los laboratorios ofrecen varias directivas configurables que permiten a los administradores de laboratorios reducir los desechos y administrar los entornos de un equipo. Estas directivas incluyen apagado automático, umbral de costos, número máximo de máquinas virtuales por usuario y tamaños máximos de máquinas virtuales. Para ver una explicación más detallada de Azure DevTest Labs, lea la [información general](devtest-lab-overview.md) o consulte el [vídeo de introducción](/documentation/videos/videos/what-is-azure-devtest-labs). 

### <a name="what-does-"worry-free,-self-service"-mean?"></a>¿Qué significa "sin preocupaciones, autoservicio"?
Con estos términos queremos indicar que los desarrolladores y evaluadores crean sus propios entornos acordes a sus necesidades y que los administradores tienen la seguridad de saber que Azure DevTest Labs ayuda a reducir los desechos y el control de los costos. Los administradores pueden especificar qué tamaños de máquina virtual se permiten, el número máximo de máquinas virtuales y cuándo iniciar y apagar las máquinas virtuales. Azure DevTest Labs también permite supervisar fácilmente los costos y definir alertas para estar al tanto del número de recursos que se utilizan en el laboratorio. 

### <a name="how-can-i-use-azure-devtest-labs?"></a>¿Cómo se usa Azure DevTest Labs?
Azure DevTest Labs es útil siempre que necesite desarrollar o probar entornos y quiera reproducirlos rápidamente o administrarlos con directivas de ahorro de costos. 

Estos son algunos escenarios en los que nuestros clientes usan Azure DevTest Labs: 

* Administración de entornos de desarrollo y prueba, mediante directivas para reducir costos y personalizar imágenes para compartir compilaciones entre los miembros del equipo.
* Desarrollo de aplicaciones con imágenes personalizadas para guardar el estado del disco en todas las etapas de desarrollo.
* Seguimiento del costo en relación con el progreso. 
* Creación de entornos de prueba masivos para pruebas de garantía de calidad.
* Uso de artefactos y fórmulas para configurar y reproducir fácilmente una aplicación en diversos entornos. 
* Distribución de máquinas virtuales para encuentros de programadores (hackathons) (trabajo de desarrollo o prueba en colaboración) y desaprovisionamiento fácil de estas máquinas cuando el evento finaliza. 

### <a name="how-am-i-billed-for-azure-devtest-labs?"></a>¿Cómo se factura Azure DevTest Labs?
Azure DevTest Labs es un servicio gratuito, lo que significa que crear laboratorios y configurar las directivas, las plantillas y los artefactos no tiene ningún costo. Solo se paga por los recursos de Azure usados en los laboratorios, como máquinas virtuales, cuentas de almacenamiento y redes virtuales. Para más información sobre el costo de los recursos de laboratorio, lea acerca de los [precios de Azure DevTest Labs](https://azure.microsoft.com/pricing/details/devtest-lab/). 

### <a name="what-are-the-different-security-levels-in-azure-devtest-labs?"></a>¿Cuáles son los diferentes niveles de seguridad en Azure DevTest Labs?
La seguridad del acceso viene determinada por el [Control de acceso basado en roles (RBAC) de Azure](../active-directory/role-based-access-built-in-roles.md). Para entender cómo funciona el acceso, es importante comprender las diferencias entre un permiso, un rol y un ámbito según se define en RBAC.

* **Permiso** : un permiso es un acceso definido para una acción específica. Por ejemplo, un permiso podría ser el acceso de lectura a todas las máquinas virtuales. 
* **Rol** : un rol es un conjunto de permisos que se pueden agrupar y asignar a un usuario. Por ejemplo, un "propietario de la suscripción" tiene acceso a todos los recursos dentro de una suscripción. 
* **Ámbito** : un ámbito es un nivel dentro de la jerarquía de recursos de Azure. Por ejemplo, un ámbito puede ser un grupo de recursos, un laboratorio único o toda la suscripción. 

Dentro del ámbito de Azure DevTest Labs, hay dos tipos de roles para definir los permisos de usuario: usuario de laboratorio y propietario de laboratorio.

* **Propietario de laboratorio** : el propietario de un laboratorio tiene acceso a todos los recursos del laboratorio. Por lo tanto, puede modificar las directivas, leer y escribir en todas las máquinas virtuales, cambiar la red virtual, etc. 
* **Usuario de laboratorio** : un usuario de laboratorio puede ver todos los recursos de laboratorio, como máquinas virtuales, directivas y redes virtuales, pero no puede modificar las directivas ni las máquinas virtuales creadas por otros usuarios. En Azure DevTest Labs también puede crear roles personalizados, y en este artículo puede averiguar cómo: [Concesión de permisos de usuario a directivas específicas de laboratorio](devtest-lab-grant-user-permissions-to-specific-lab-policies.md). 

Puesto que los ámbitos son jerárquicos, cuando un usuario tiene permisos en un ámbito determinado, también se le conceden automáticamente en cada ámbito de nivel inferior que engloba. Por ejemplo, si un usuario está asignado al rol de propietario de la suscripción, tiene acceso a todos los recursos de una suscripción. Estos recursos incluyen todas las máquinas virtuales, todas las redes virtuales y todos los laboratorios. Por lo tanto, el propietario de una suscripción hereda automáticamente el rol de propietario de laboratorio. Sin embargo, lo contrario no es cierto. El propietario de un laboratorio tiene acceso a un laboratorio, que es un ámbito inferior al del nivel de suscripción. Por lo tanto, el propietario de un laboratorio no puede ver las máquinas virtuales, las redes virtuales ni los recursos que se encuentren fuera del laboratorio. 

### <a name="how-do-i-create-a-role-to-allow-users-to-perform-a-specific-task?"></a>¿Cómo se crea un rol para permitir que los usuarios realicen una tarea específica?
Aquí encontrará un artículo completo sobre cómo crear roles personalizados y asignar permisos a ese rol. Este es un ejemplo de un script que crea el rol "Usuario avanzado de DevTest Labs", que tiene permiso para iniciar y detener todas las máquinas virtuales del laboratorio:

    $policyRoleDef = Get-AzureRmRoleDefinition "DevTest Labs User" 
    $policyRoleDef.Actions.Remove('Microsoft.DevTestLab/Environments/*') 
    $policyRoleDef.Id = $null 
    $policyRoleDef.Name = "DevTest Labs Advance User" 
    $policyRoleDef.IsCustom = $true 
    $policyRoleDef.AssignableScopes.Clear() 
    $policyRoleDef.AssignableScopes.Add("subscriptions/<subscription Id>") 
    $policyRoleDef.Actions.Add("Microsoft.DevTestLab/labs/virtualMachines/Start/action") 
    $policyRoleDef.Actions.Add("Microsoft.DevTestLab/labs/virtualMachines/Stop/action") 
    $policyRoleDef = New-AzureRmRoleDefinition -Role $policyRoleDef  

### <a name="does-azure-devtest-labs-integrate-with-my-ci/cd-toolchain?"></a>¿Se integra Azure DevTest Labs con mi cadena de herramientas de CI/CD?
Si va a utilizar VSTS, hay una [extensión de Tareas de Azure DevTest Labs](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks) que le permite automatizar la canalización de entrega de versiones de Azure DevTest Labs. Algunos de los usos de esta extensión incluyen:

* Crear e implementar una máquina virtual automáticamente y configurarla con la última compilación mediante Copia de archivos de Azure o Tareas de VSTS de PowerShell. 
* Capturar automáticamente el estado de una máquina virtual después de intentar reproducir un error en la misma máquina virtual para investigarlo con más detalle. 
* Eliminar la máquina virtual al final de la canalización de entrega de versiones cuando ya no sea necesaria. 

Las siguientes entradas de blog proporcionan orientación e información sobre el uso de la extensión de VSTS:

* [Azure DevTest Labs: extensión de VSTS](https://blogs.msdn.microsoft.com/devtestlab/2016/06/15/azure-devtest-labs-vsts-extension/) 
* [Deploying a new VM in an existing AzureDevTestLab from VSTS (Implementación de una nueva máquina virtual en un laboratorio de AzureDevTestLab existente desde VSTS)](http://www.visualstudiogeeks.com/blog/DevOps/Deploy-New-VM-To-Existing-AzureDevTestLab-From-VSTS) 
* [Using VSTS Release Management for Continuous Deployments to AzureDevTestLabs (Uso de la administración de versiones de VSTS para implementaciones continuas en AzureDevTestLabs)](http://www.visualstudiogeeks.com/blog/DevOps/Use-VSTS-ReleaseManagement-to-Deploy-and-Test-in-AzureDevTestLabs) 

Para otras cadenas de herramientas de CI/CD, todos los escenarios mencionados anteriormente que pueden obtenerse mediante la extensión de Tareas de VSTS se pueden conseguir igualmente mediante la implementación de [plantillas de Azure Resource Manager](https://github.com/Azure/azure-devtestlab/tree/master/ARMTemplates) con [cmdlets de Azure PowerShell](../resource-group-template-deploy.md) y los [SDK de .NET](https://www.nuget.org/packages/Microsoft.Azure.Management.DevTestLabs/). También puede usar las [API de REST para DevTest Labs](http://aka.ms/dtlrestapis) para integrarlas con su cadena de herramientas.  

### <a name="why-can't-i-see-certain-vms-in-the-azure-virtual-machines-blade-that-i-see-within-azure-devtest-labs?"></a>¿Por qué no puedo ver determinadas máquinas virtuales en la hoja Azure Virtual Machines que sí veo en Azure DevTest Labs?
Cuando se crea una máquina virtual en Azure DevTest Labs, se concede permiso para acceder a esa máquina virtual. Puede verla tanto en la hoja de laboratorios con en la hoja **Máquinas virtuales** . Los usuarios del rol de DevTest Labs pueden ver todas las máquinas virtuales creadas en el laboratorio mediante la hoja **Todas las máquinas virtuales** del laboratorio. No obstante, a los usuarios del rol de DevTest Labs no se les concede automáticamente acceso de lectura a los recursos de máquina virtual creados por otros usuarios. Por lo tanto, esas máquinas virtuales no se muestran en la hoja **Máquinas virtuales** . 

### <a name="what-is-the-difference-between-custom-images-and-formulas?"></a>¿Cuál es la diferencia entre imágenes personalizadas y fórmulas?
Una imagen personalizada es un disco duro virtual (VHD), mientras que una fórmula es una imagen que se puede configurar con opciones adicionales que se pueden guardar y reproducir. Si lo que quiere es crear rápidamente varios entornos con la misma imagen básica inmutable, puede ser preferible una imagen personalizada. En cambio, si quiere reproducir la configuración de la máquina virtual con los últimos bits, una red o subred virtual o un tamaño específico, es mejor una fórmula. Para una explicación más detallada, consulte el artículo [Comparación de imágenes personalizadas y fórmulas en DevTest Labs](devtest-lab-comparing-vm-base-image-types.md). 

### <a name="how-do-i-create-multiple-vms-from-the-same-template-at-once?"></a>¿Cómo se crean varias máquinas virtuales desde la misma plantilla a la vez?
Puede usar la [extensión de Tareas de VSTS](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks) o [generar una plantilla de Azure Resource Manager](devtest-lab-add-vm-with-artifacts.md#save-arm-template) mientras crea una máquina virtual e [implementar la plantilla de Azure Resource Manager desde Windows PowerShell](../resource-group-template-deploy.md). 

### <a name="how-do-i-move-my-existing-azure-vms-into-my-azure-devtest-labs-lab?"></a>¿Cómo se pueden mover las máquinas virtuales de Azure existentes a mi laboratorio de Azure DevTest Labs?
Estamos diseñando una solución para mover directamente las máquinas virtuales a Azure DevTest Labs. Mientras tanto, puede copiar las máquinas virtuales existentes en Azure DevTest Labs de la manera siguiente: 

1. Copie el archivo VHD de la máquina virtual existente con este [script de Windows PowerShell](https://github.com/Azure/azure-devtestlab/blob/master/Scripts/CopyVHDFromVMToLab.ps1) 
2. [Cree la imagen personalizada](devtest-lab-create-template.md) dentro del laboratorio de Azure DevTest Labs. 
3. Creación de una máquina virtual en el laboratorio a partir de la imagen personalizada 

### <a name="can-i-attach-multiple-disks-to-my-vms?"></a>¿Se pueden conectar varios discos a las máquinas virtuales?
Se admite la conexión de varios discos a las máquinas virtuales.  

### <a name="how-do-i-automate-the-process-of-uploading-vhd-files-to-create-custom-images?"></a>¿Cómo se puede automatizar el proceso de carga de archivos VHD para crear imágenes personalizadas?
Hay dos opciones:

* [Azure AzCopy](../storage/storage-use-azcopy.md#blob-upload) para copiar o cargar archivos VHD en la cuenta de almacenamiento asociada con el laboratorio.
* [Explorador de almacenamiento de Microsoft Azure](../vs-azure-tools-storage-manage-with-storage-explorer.md) es una aplicación independiente que se ejecuta en Windows, OSX y Linux.   

Para buscar la cuenta de almacenamiento de destino asociada con el laboratorio, siga estos pasos:

1. Inicie sesión en el [Portal de Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040). 
2. Seleccione **Grupos de recursos** en el panel izquierdo. 
3. Busque y seleccione el grupo de recursos asociado con el laboratorio. 
4. En la hoja **Información general** , seleccione una de las cuentas de almacenamiento. 
5. Seleccione **Blobs**.
6. Busque cargas en la lista. Si no existe ninguna, vuelva al paso 4 y pruebe con otra cuenta de almacenamiento.
7. Use la **dirección URL** como destino del comando AzCopy.

### <a name="how-can-i-automate-the-process-of-deleting-all-the-vms-in-my-lab?"></a>¿Cómo puedo automatizar el proceso de eliminación de todas las máquinas virtuales en mi laboratorio?
Además de eliminar las máquinas virtuales del laboratorio en Azure Portal, puede eliminar todas las máquinas virtuales de su laboratorio por medio de un script de PowerShell. En el ejemplo siguiente, basta con modificar los valores de parámetro en el comentario **Values to change** (Valores para cambiar). Puede recuperar los valores `subscriptionId`, `labResourceGroup` y `labName` de la hoja de laboratorio en Azure Portal. 

    # Delete all the VMs in a lab

    # Values to change
    $subscriptionId = "<Enter Azure subscription ID here>"
    $labResourceGroup = "<Enter lab's resource group here>"
    $labName = "<Enter lab name here>"

    # Login to your Azure account
    Login-AzureRmAccount

    # Select the Azure subscription that contains the lab. This step is optional
    # if you have only one subscription.
    Select-AzureRmSubscription -SubscriptionId $subscriptionId

    # Get the lab that contains the VMs to delete.
    $lab = Get-AzureRmResource -ResourceId ('subscriptions/' + $subscriptionId + '/resourceGroups/' + $labResourceGroup + '/providers/Microsoft.DevTestLab/labs/' + $labName)

    # Get the VMs from that lab.
    $labVMs = Get-AzureRmResource | Where-Object { 
              $_.ResourceType -eq 'microsoft.devtestlab/labs/virtualmachines' -and
              $_.ResourceName -like "$($lab.ResourceName)/*"}

    # Delete the VMs.
    foreach($labVM in $labVMs)
    {
        Remove-AzureRmResource -ResourceId $labVM.ResourceId -Force
    }




### <a name="what-are-artifacts?"></a>¿Qué son los artefactos?
Los artefactos son elementos personalizables que se pueden usar para implementar los bits más recientes o las herramientas de desarrollo en una máquina virtual. Se asocian a la máquina virtual durante su creación con unos pocos clics y una vez que se ha aprovisionado la máquina virtual, los artefactos implementan y configurar la máquina virtual. Hay diversos artefactos ya existentes en el [repositorio público de Github](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts) pero también puede [crear fácilmente sus propios artefactos](devtest-lab-artifact-author.md). 

### <a name="how-do-i-create-a-lab-from-an-azure-resource-manager-template?"></a>¿Cómo se crea un laboratorio a partir de una plantilla de Azure Resource Manager?
Hemos proporcionado una [repositorio de Github de plantillas de Azure Resource Manager de laboratorio](https://github.com/Azure/azure-devtestlab/tree/master/ARMTemplates) que se puede implementar tal cual o modificar para crear plantillas personalizadas para los laboratorios. Cada una de estas plantillas tiene un vínculo en el que puede hacer clic para implementar el laboratorio tal cual bajo su propia suscripción de Azure, o puede personalizar la plantilla e [implementar con PowerShell o la CLI de Azure](../resource-group-template-deploy.md).

### <a name="why-are-my-vms-created-in-different-resource-groups-with-arbitrary-names?-can-i-rename-or-modify-these-resource-groups?"></a>¿Por qué mis máquinas virtuales se crean en distintos grupos de recursos con nombres arbitrarios? ¿Se pueden modificar estos grupos de recursos o cambiar su nombre?
Los grupos de recursos se crean de esta manera para que Azure DevTest Labs administre los permisos de usuario y el acceso a las máquinas virtuales. Aunque puede mover la máquina virtual a otro grupo de recursos con el nombre que desee, no se recomienda hacerlo. Estamos trabajando en mejorar esta experiencia para permitir más flexibilidad.   

### <a name="how-many-labs-can-i-create-under-the-same-subscription?"></a>¿Cuántos laboratorios se pueden crear con una misma suscripción?
No hay ningún límite en el número de laboratorios que se pueden crear por suscripción específica. Sin embargo, los recursos usados están limitados por cada suscripción. Puede leer sobre los [límites y las cuotas impuestos en las suscripciones de Azure](../azure-subscription-service-limits.md) y [cómo aumentar estos límites](https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests). 

### <a name="how-many-vms-can-i-create-per-lab?"></a>¿Cuántas máquinas virtuales se pueden crear por laboratorio?
No hay ningún límite específico en el número de máquinas virtuales que se pueden crear por cada laboratorio. No obstante, actualmente el laboratorio admite solo la ejecución de 40 máquinas virtuales al mismo tiempo en almacenamiento estándar y 25 máquinas virtuales de forma simultánea en almacenamiento premium. En estos momentos estamos trabajando para aumentar estos límites. 

### <a name="how-do-i-share-a-direct-link-to-my-lab?"></a>¿Cómo se puede compartir un vínculo directo a mi laboratorio?
Para compartir un vínculo directo a los usuarios de laboratorio, puede realizar el procedimiento siguiente.

1. Vaya al laboratorio en Azure Portal.
2. Copie la dirección URL del laboratorio desde el explorador y compártala con los usuarios del laboratorio. 

> [!NOTE]
> Si los usuarios de laboratorio son usuarios externos con un [cuenta MSA](#what-is-a-microsoft-account) y no pertenecen al directorio Active Directory de su empresa, es posible que reciban un error al navegar al vínculo proporcionado. De ser así, indíqueles que hagan clic en su nombre en la esquina superior derecha de Azure Portal y seleccionen el directorio donde existe el laboratorio en la sección **Directorio** del menú.
> 
> 

### <a name="what-is-a-microsoft-account?"></a>¿Qué es una cuenta Microsoft?
Una cuenta de Microsoft es lo que se utiliza para casi todo lo que hace con servicios y dispositivos de Microsoft. Es una combinación de dirección de correo electrónico y contraseña que utiliza para iniciar sesión en Skype, Outlook.com, OneDrive, Windows Phone y Xbox LIVE, e implica que los archivos, fotografías, contactos y configuraciones pueden seguirle a cualquier dispositivo. 

> [!NOTE]
> La cuenta Microsoft antes se llamaba "Windows Live ID".
> 
> 

### <a name="my-artifact-failed-during-vm-creation.-how-do-i-troubleshoot-it?"></a>Mi artefacto produjo errores durante la creación de la máquina virtual. ¿Cómo se soluciona este problema?
Consulte la entrada de blog [How to troubleshoot failing Artifacts in AzureDevTestLabs](http://www.visualstudiogeeks.com/blog/DevOps/How-to-troubleshoot-failing-artifacts-in-AzureDevTestLabs) (Solución de problemas de artefactos con errores en AzureDevTestLabs), escrito por uno de nuestros MVP, para más información sobre cómo obtener los registros respecto a su artefacto con errores. 

### <a name="why-isn't-my-existing-virtual-network-saving-properly?"></a>¿Por qué mi máquina virtual existente no se guarda correctamente?
Una posibilidad es que el nombre de la red virtual contenga puntos. Si es así, pruebe a quitar los puntos, o reemplácelos por guiones, y luego intente guardar de nuevo la máquina virtual.

<!--HONumber=Oct16_HO2-->


