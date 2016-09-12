<properties
	pageTitle="Preguntas más frecuentes sobre DevTest Labs | Microsoft Azure"
	description="Encuentre respuestas a preguntas comunes sobre DevTest Labs"
	services="devtest-lab,virtual-machines"
	documentationCenter="na"
	authors="tomarcher"
	manager="douge"
	editor=""/>

<tags
	ms.service="devtest-lab"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/29/2016"
	ms.author="tarcher"/>

# Preguntas más frecuentes sobre DevTest Labs

En este artículo se ofrecen respuestas a algunas de las preguntas más comunes sobre DevTest Labs.

## Mi pregunta no está respondida aquí. ¿Qué debo hacer?
Si su pregunta no aparece aquí, háganoslo saber y lo ayudaremos a encontrar una respuesta.

- Publique una pregunta en el [hilo de Disqus](#comments) al final de estas preguntas más frecuentes y ponerse en contacto con el equipo de Caché de Microsoft Azure y otros miembros de la comunidad con cualquier tema que tenga relación con este artículo.
- Para llegar a más público, puede publicar una pregunta en el [foro de MSDN de Azure DevTest Labs](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureDevTestLabs) y ponerse en contacto con el equipo de Azure DevTest Labs y otros miembros de la Comunidad.
- Para realizar una solicitud de característica, envíe las solicitudes y las ideas al sitio [User Voice de Azure DevTest Labs](https://feedback.azure.com/forums/320373-azure-devtest-labs).

## Primeros pasos
- [¿Cómo puedo comenzar a utilizar Azure DevTest Labs?](http://go.microsoft.com/fwlink/?LinkID=627034&clcid=0x409)

## General
- [¿Por qué debería usar DevTest Labs?](#why-should-i-use-devtest-labs)
- [¿Qué significa "sin preocupaciones, autoservicio"?](#what-does-quotworry-free-self-servicequot-mean)
- [¿Cómo se usa DevTest Labs?](#how-can-i-use-devtest-labs)
- [¿Cómo se paga DevTest Labs?](#how-will-i-be-charged-for-devtest-labs)
 
## Seguridad 
- [¿Cuáles son los diferentes niveles de seguridad en DevTest Labs?](#what-are-the-different-security-levels-in-devtest-labs)
- [¿Cómo se crea un rol específico para permitir que los usuarios realicen una única tarea?](#how-do-i-create-a-specific-role-to-allow-users-to-perform-only-a-single-task)
 
## Automatización e integración de CI/CD 
 
- [¿Se integra DevTest Labs con mi cadena de herramientas de CI/CD?](#does-devtest-labs-integrate-with-my-cicd-toolchain)
 
## Máquinas virtuales 
 
- [¿Por qué no puedo ver determinadas máquinas virtuales en la hoja Máquinas virtuales de Azure que sí veo en DevTest Labs?](#why-cant-i-see-certain-vms-in-the-azure-virtual-machines-blade-that-i-see-within-devtest-labs)
- [¿Cuál es la diferencia entre imágenes personalizadas y fórmulas?](#what-is-the-difference-between-custom-images-and-formulas)
- [¿Cómo se crean varias máquinas virtuales desde la misma plantilla a la vez?](#how-do-i-create-multiple-vms-from-the-same-template-at-once)
- [¿Cómo se pueden mover las máquinas virtuales de Azure existentes a mi laboratorio de DevTest Labs?](#how-do-i-move-my-existing-azure-vms-into-my-devtest-labs-lab)
- [¿Se pueden conectar varios discos a las máquinas virtuales?](#can-i-attach-multiple-disks-to-my-vms)
- [¿Cómo se puede automatizar el proceso de carga de archivos VHD para crear imágenes personalizadas?](#how-do-i-automate-the-process-of-uploading-vhd-files-to-create-custom-images)
 
## Artefactos 
 
- [¿Qué son los artefactos?](#what-are-artifacts)
 
## Configuración del laboratorio 
 
- [¿Cómo se crea un laboratorio a partir de una plantilla de Azure Resource Manager?](#how-do-i-create-a-lab-from-an-arm-template)
- [¿Por qué todas las máquinas virtuales creadas están en distintos grupos de recursos con nombres arbitrarios? ¿Se pueden modificar estos grupos de recursos o cambiar su nombre?](#why-are-all-of-my-vms-created-in-different-resource-groups-with-arbitrary-names-can-i-rename-or-modify-these-resource-groups)
- [¿Cuántos laboratorios se pueden crear con una misma suscripción?](#how-many-labs-can-i-create-under-the-same-subscription)
- [¿Cuántas máquinas virtuales se pueden crear por laboratorio?](#how-many-vms-can-i-create-per-lab)
 
## Solución de problemas 
 
- [Mi artefacto produjo errores durante la creación de la máquina virtual. ¿Cómo se soluciona este problema?](#my-artifact-failed-during-vm-creation-how-do-i-troubleshoot-it)
- [¿Por qué mi máquina virtual existente no se guarda correctamente?](#why-isnt-my-existing-virtual-network-saving-properly)

### ¿Por qué debería usar DevTest Labs? 
DevTest Labs puede ahorrarle a su equipo tiempo y dinero. Los desarrolladores pueden crear sus propios entornos mediante una serie de bases diferentes, y utilizar artefactos para implementar y configurar rápidamente las aplicaciones. Mediante imágenes personalizadas y fórmulas, las máquinas virtuales se pueden guardar como plantillas y reproducirse fácilmente. Además, los laboratorios ofrecen varias directivas configurables (como apagado automático, umbral de costo, máquinas virtuales máximas por usuario y tamaños máximos de máquina virtual), que permiten a los administradores reducir los desechos y administrar los entornos de un equipo. Para ver una explicación más detallada de DevTest Labs, lea la [información general](devtest-lab-overview.md) o consulte el [vídeo de introducción](/documentation/videos/videos/what-is-azure-devtest-labs).

### ¿Qué significa "sin preocupaciones, autoservicio"?
Con estos términos queremos indicar que los desarrolladores y evaluadores pueden crear sus propios entornos acordes a sus necesidades y que los administradores tienen la seguridad de saber que DevTest Labs ayudará a reducir los desechos y el control de los costos. Los administradores pueden especificar qué tamaños de máquina virtual se permiten, el número máximo de máquinas virtuales y cuándo iniciar y apagar las máquinas virtuales. DevTest Labs también permite supervisar fácilmente los costos y definir alertas para estar al tanto del número de recursos que se utilizan en el laboratorio.

### ¿Cómo se usa DevTest Labs? 
DevTest Labs es útil siempre que necesite desarrollar o probar entornos y quiera reproducirlos rápidamente o administrarlos con directivas de ahorro de costos.

Estos son algunos escenarios en los que nuestros clientes usan DevTest Labs:

- Administración de entornos de desarrollo y prueba, mediante directivas para reducir costos y personalizar imágenes para compartir compilaciones entre los miembros del equipo.
- Desarrollo de aplicaciones con imágenes personalizadas para guardar el estado del disco en todas las etapas de desarrollo.
- Seguimiento del costo en relación con el progreso.
- Creación de entornos de prueba masivos para pruebas de garantía de calidad.
- Uso de artefactos y fórmulas para configurar y reproducir fácilmente una aplicación en diversos entornos.
- Distribución de máquinas virtuales para encuentros de programadores (hackathons) (trabajo de desarrollo o prueba en colaboración) y desaprovisionamiento fácil de estas máquinas cuando el evento finaliza.

### ¿Cómo se paga DevTest Labs? 
DevTest Labs es un servicio gratuito, lo que significa que crear laboratorios y configurar las directivas, las plantillas y los artefactos no tiene ningún costo. Solo se paga por los recursos de Azure usados en los laboratorios, como máquinas virtuales, cuentas de almacenamiento y redes virtuales. Para más información sobre el costo de los recursos de laboratorio, lea acerca de los [precios de Azure DevTest Labs](https://azure.microsoft.com/pricing/details/devtest-lab/).

### ¿Cuáles son los diferentes niveles de seguridad en DevTest Labs?  
La seguridad del acceso viene determinada por el [Control de acceso basado en roles (RBAC) de Azure](../active-directory/role-based-access-built-in-roles.md). Para entender cómo funciona el acceso, es importante comprender las diferencias entre un permiso, un rol y un ámbito según se define en RBAC. Un permiso es un acceso definido para una acción específica (por ejemplo, acceso de lectura a todas las máquinas virtuales). Un rol es un conjunto de permisos que se pueden agrupar y asignar a un usuario (por ejemplo, el "propietario de una suscripción" tiene acceso a todos los recursos dentro de una suscripción). Un ámbito es un nivel dentro de la jerarquía de recursos de Azure (por ejemplo, un grupo de recursos, un único laboratorio o toda la suscripción).
 
Dentro del ámbito de DevTest Labs, hay dos tipos de roles para definir los permisos de usuario: usuario de laboratorio y propietario de laboratorio.

- Propietario de laboratorio: un propietario de laboratorio tiene acceso a los recursos que hay dentro del laboratorio; así, puede modificar las directivas, leer y escribir en todas las máquinas virtuales, cambiar la red virtual, etc.
- Usuario de laboratorio: un usuario de laboratorio puede ver todos los recursos de laboratorio, como máquinas virtuales, directivas y redes virtuales, pero no puede modificar las directivas ni las máquinas virtuales creadas por otros usuarios. En DevTest Labs también puede crear roles personalizados, y en este artículo puede averiguar cómo: [Concesión de permisos de usuario a directivas específicas de laboratorio](devtest-lab-grant-user-permissions-to-specific-lab-policies.md).
 
Puesto que los ámbitos son jerárquicos, cuando un usuario tiene permisos en un ámbito determinado, también se le conceden automáticamente en cada ámbito de nivel inferior que engloba. Por ejemplo, si un usuario está asignado al rol de propietario de la suscripción, tiene acceso a todos los recursos de una suscripción, lo que incluye todas las máquinas virtuales, todas las redes virtuales y todos los laboratorios. Por lo tanto, el propietario de una suscripción hereda automáticamente el rol de propietario de laboratorio. Sin embargo, lo contrario no es cierto. El propietario de un laboratorio tiene acceso a un laboratorio, que es un ámbito inferior al del nivel de suscripción. Por lo tanto, el propietario de un laboratorio no podrá ver las máquinas virtuales, las redes virtuales o los recursos que se encuentren fuera del laboratorio.

### ¿Cómo se crea un rol para permitir que los usuarios realicen una tarea específica?
Aquí encontrará un artículo completo sobre cómo crear roles personalizados y asignar permisos a ese rol. Este es un ejemplo de un script que crea el rol "Usuario avanzado de DevTest Labs", que tiene permiso para iniciar y detener todas las máquinas virtuales del laboratorio:
 
	$policyRoleDef = (Get-AzureRmRoleDefinition "DevTest Labs User") 
	$policyRoleDef.Actions.Remove('Microsoft.DevTestLab/Environments/*') 
	$policyRoleDef.Id = $null 
	$policyRoleDef.Name = "DevTest Labs Advance User" 
	$policyRoleDef.IsCustom = $true 
	$policyRoleDef.AssignableScopes.Clear() 
	$policyRoleDef.AssignableScopes.Add("subscriptions/<subscription Id>") 
	$policyRoleDef.Actions.Add("Microsoft.DevTestLab/labs/virtualMachines/Start/action") 
	$policyRoleDef.Actions.Add("Microsoft.DevTestLab/labs/virtualMachines/Stop/action") 
	$policyRoleDef = (New-AzureRmRoleDefinition -Role $policyRoleDef)  
 
### ¿Se integra DevTest Labs con mi cadena de herramientas de CI/CD? 
Si va a utilizar VSTS, hay una [extensión de Tareas de Azure DevTest Labs](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks) que le permite automatizar la canalización de entrega de versiones de DevTest Labs. Algunos de los usos de esta extensión incluyen:

- Crear e implementar una máquina virtual automáticamente y configurarla con la última compilación mediante Copia de archivos de Azure o Tareas de VSTS de PowerShell.
- Capturar automáticamente el estado de una máquina virtual después de intentar reproducir un error en la misma máquina virtual para investigarlo con más detalle.
- Eliminar la máquina virtual al final de la canalización de entrega de versiones cuando ya no sea necesaria.

Las siguientes entradas de blog proporcionan orientación e información sobre el uso de la extensión de VSTS:
 
- [Azure DevTest Labs: extensión de VSTS](https://blogs.msdn.microsoft.com/devtestlab/2016/06/15/azure-devtest-labs-vsts-extension/)
- [Deploying a new VM in an existing AzureDevTestLab from VSTS (Implementación de una nueva máquina virtual en un laboratorio de AzureDevTestLab existente desde VSTS)](http://www.visualstudiogeeks.com/blog/DevOps/Deploy-New-VM-To-Existing-AzureDevTestLab-From-VSTS)
- [Using VSTS Release Management for Continuous Deployments to AzureDevTestLabs (Uso de la administración de versiones de VSTS para implementaciones continuas en AzureDevTestLabs)](http://www.visualstudiogeeks.com/blog/DevOps/Use-VSTS-ReleaseManagement-to-Deploy-and-Test-in-AzureDevTestLabs)
 
Para otras cadenas de herramientas de CI/CD, todos los escenarios mencionados anteriormente que pueden obtenerse mediante la extensión de Tareas de VSTS se pueden conseguir igualmente mediante la implementación de [plantillas de Azure Resource Manager](https://github.com/Azure/azure-devtestlab/tree/master/ARMTemplates) con [cmdlets de Azure PowerShell](../resource-group-template-deploy.md) y los [SDK de .NET](https://www.nuget.org/packages/Microsoft.Azure.Management.DevTestLabs/). También puede usar las [API de REST para DevTest Labs](http://aka.ms/dtlrestapis) para integrarlas con su cadena de herramientas.

### ¿Por qué no puedo ver determinadas máquinas virtuales en la hoja Máquinas virtuales de Azure que sí veo en DevTest Labs?
Cuando se crea una máquina virtual en DevTest Labs, se concede permiso de acceso a esa máquina virtual y puede verla tanto en la hoja Laboratorios como en la hoja **Máquinas virtuales**. Si es un usuario de DevTest Labs, esto le permite ver todas las máquinas virtuales que se crearon en el laboratorio mediante la hoja **Todas las máquinas virtuales**. Sin embargo, como usuario de DevTest Labs, no se le concede automáticamente acceso de lectura a los recursos de máquina virtual que otros han creado, por lo que no se mostrarán en la hoja **Máquinas virtuales**.

### ¿Cuál es la diferencia entre imágenes personalizadas y fórmulas? 
Una imagen personalizada es un disco duro virtual (VHD), mientras que una fórmula es una imagen que se puede configurar con opciones adicionales que se pueden guardar y reproducir. Si lo que quiere es crear rápidamente varios entornos con la misma imagen básica inmutable, puede ser preferible una imagen personalizada. En cambio, si quiere reproducir la configuración de la máquina virtual con los últimos bits, una red o subred virtual o un tamaño específico, es mejor una fórmula. Para una explicación más detallada, consulte el artículo, [Comparación de imágenes personalizadas y fórmulas en DevTest Labs](devtest-lab-comparing-vm-base-image-types.md).
 
### ¿Cómo se crean varias máquinas virtuales desde la misma plantilla a la vez? 
Puede usar la [extensión de Tareas de VSTS](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks) o [generar una plantilla de Azure Resource Manager](devtest-lab-add-vm-with-artifacts.md#save-arm-template) mientras crea una máquina virtual e [implementar la plantilla de Azure Resource Manager desde Windows PowerShell](../resource-group-template-deploy.md).
 
### ¿Cómo se pueden mover las máquinas virtuales de Azure existentes a mi laboratorio de DevTest Labs? 
Estamos diseñando una solución para mover directamente las máquinas virtuales a DevTest. Mientras tanto, puede copiar las máquinas virtuales existentes en DevTest Labs de la manera siguiente:

1. Copie el archivo VHD de la máquina virtual existente con este [script de Windows PowerShell](https://github.com/Azure/azure-devtestlab/blob/master/Scripts/CopyVHDFromVMToLab.ps1).
1. [Cree la imagen personalizada](devtest-lab-create-template.md) dentro del laboratorio de DevTest Labs.
1. Creación de una máquina virtual en el laboratorio a partir de la imagen personalizada
 
### ¿Se pueden conectar varios discos a las máquinas virtuales? 
Se admite la conexión de varios discos a las máquinas virtuales.
 
### ¿Cómo se puede automatizar el proceso de carga de archivos VHD para crear imágenes personalizadas? 
Hay dos opciones:

- Se puede usar [Azure AzCopy](../storage/storage-use-azcopy.md#blob-upload) para copiar o cargar archivos VHD en la cuenta de almacenamiento asociada con el laboratorio.
- El [Explorador de almacenamiento de Microsoft Azure](../vs-azure-tools-storage-manage-with-storage-explorer.md) es una aplicación independiente que se ejecuta en Windows, OSX y Linux.
 
Para buscar la cuenta de almacenamiento de destino asociada con el laboratorio:

1. Inicie sesión en el [Portal de Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Seleccione **Grupos de recursos** en el panel izquierdo.
1. Busque y seleccione el grupo de recursos asociado con el laboratorio.
1. En la hoja **Información general**, seleccione una de las cuentas de almacenamiento.
1. Seleccione **Blobs**.
1. Busque cargas en la lista. Si no existe ninguna, vuelva al paso 4 y pruebe con otra cuenta de almacenamiento.
1. Use la **dirección URL** como destino del comando AzCopy.

### ¿Qué son los artefactos? 
Los artefactos son elementos personalizables que se pueden usar para implementar los bits más recientes o las herramientas de desarrollo en una máquina virtual. Se asocian a la máquina virtual durante su creación con unos pocos clics y una vez que se ha aprovisionado la máquina virtual, los artefactos implementan y configurar la máquina virtual. Hay una serie de artefactos ya existentes en nuestro [repositorio público de Github](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts), pero también puede [crear fácilmente sus propios artefactos](devtest-lab-artifact-author.md).

### ¿Cómo se crea un laboratorio a partir de una plantilla de Azure Resource Manager? 
Tenemos un [repositorio de Github de plantillas de Azure Resource Manager de laboratorios](https://github.com/Azure/azure-devtestlab/tree/master/ARMTemplates). Cada una de estas plantillas tiene un vínculo en el que puede hacer clic para implementar el laboratorio de DevTest Labs en su propia suscripción de Azure.
 
### ¿Por qué todas las máquinas virtuales creadas están en distintos grupos de recursos con nombres arbitrarios? ¿Se pueden modificar estos grupos de recursos o cambiar su nombre? 
Los grupos de recursos se crean de esta manera para que DevTest Labs administre los permisos de usuario y el acceso a las máquinas virtuales. Aunque puede mover la máquina virtual a otro grupo de recursos con el nombre que desee, no se recomienda. Estamos trabajando en mejorar esta experiencia para permitir más flexibilidad.
 
### ¿Cuántos laboratorios se pueden crear con una misma suscripción? 
No hay ningún límite en el número de laboratorios que se pueden crear por suscripción específica, pero los recursos usados están limitados por cada suscripción. Puede leer sobre los [límites y las cuotas impuestos en las suscripciones de Azure](../azure-subscription-service-limits.md) y [cómo aumentar estos límites](https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests).
 
### ¿Cuántas máquinas virtuales se pueden crear por laboratorio? 
No hay ningún límite específico en el número de máquinas virtuales que se pueden crear por laboratorio, pero actualmente el laboratorio admite únicamente la ejecución de 40 máquinas virtuales al mismo tiempo en almacenamiento estándar y 25 máquinas virtuales de forma simultánea en almacenamiento premium. En estos momentos estamos trabajando para aumentar estas limitaciones.
 
### Mi artefacto produjo errores durante la creación de la máquina virtual. ¿Cómo se soluciona este problema? 
Consulte la entrada de blog [How to troubleshoot failing Artifacts in AzureDevTestLabs](http://www.visualstudiogeeks.com/blog/DevOps/How-to-troubleshoot-failing-artifacts-in-AzureDevTestLabs) (Solución de problemas de artefactos con errores en AzureDevTestLabs), escrito por uno de nuestros MVP, para más información sobre cómo obtener los registros respecto a su artefacto con errores.
 
### ¿Por qué mi máquina virtual existente no se guarda correctamente?  
Una posibilidad es que el nombre de la red virtual contenga puntos. Si es así, pruebe a quitar los puntos, o reemplácelos por guiones, y luego intente guardar de nuevo la máquina virtual.

<!---HONumber=AcomDC_0831_2016-->