<properties
   pageTitle="La implementación de Resource Manager y la implementación clásica | Microsoft Azure"
   description="Describe las diferencias entre el modelo de implementación del Administrador de recursos y el modelo de implementación clásica (o de administración del servicio)."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/29/2016"
   ms.author="tomfitz"/>

# La implementación de Azure Resource Manager frente a la implementación clásica: los modelos de implementación y el estado de los recursos

En este tema, hablaremos sobre el modelo de implementación de Azure Resource Manager y el modelo de implementación clásica, el estado de los recursos y por qué los recursos se han implementado con uno u otro. El modelo de implementación de Resource Manager difiere en varios aspectos del modelo de implementación clásica, y los dos modelos no son totalmente compatibles entre sí. Para simplificar la implementación y administración de recursos, Microsoft recomienda que utilice el Administrador de recursos para los nuevos recursos y, si es posible, que vuelva a implementar los recursos existentes a través del Administrador de recursos.

Si no está familiarizado con Resource Manager, quizás quiera revisar primero la terminología definida en la [Información general de Azure Resource Manager](resource-group-overview.md).

## Historia de los modelos de implementación

Originalmente, Azure solo proporcionaba el modelo de implementación clásica. En este modelo, cada recurso existía de forma independiente; no había manera de agrupar los recursos relacionados. En su lugar, debía realizarse un seguimiento manual de los recursos que componían la solución o aplicación, y acordarse de administrarlos de manera coordinada. Para implementar una solución, tenía que crear cada recurso individualmente mediante el portal clásico o crear un script que implementara todos los recursos en el orden correcto. Para eliminar una solución, tenía que eliminar cada recurso individualmente. No se podía aplicar ni actualizar fácilmente las directivas de control de acceso para los recursos relacionados. Por último, no podía aplicar etiquetas a los recursos para etiquetarlos con términos que le ayudaran a supervisar los recursos y administrar la facturación.

En 2014, Azure presentó Resource Manager, que incorpora el concepto de un grupo de recursos. Un grupo de recursos es un contenedor para los recursos que comparten un ciclo de vida común. El modelo de implementación de Resource Manager ofrece varias ventajas:

- Puede implementar, administrar y supervisar todos los servicios para su solución como grupo, en lugar de controlar estos servicios individualmente.
- Puede implementar la solución repetidamente a lo largo del ciclo de vida y tener la seguridad de que los recursos se implementan de forma coherente.
- Puede aplicar control de acceso a todos los recursos del grupo de recursos y las directivas se aplican automáticamente cuando se agregan nuevos recursos al grupo de recursos.
- Puede aplicar etiquetas a los recursos para organizar de manera lógica todos los recursos en su suscripción.
- Puede usar la notación de objetos JavaScript (JSON) para definir la infraestructura de la solución. El archivo JSON se conoce como una plantilla de Resource Manager.
- Puede definir las dependencias entre recursos de modo que se implementen en el orden correcto.

Al agregarse el Administrador de recursos, todos los recursos se agregaron retroactivamente a los grupos de recursos predeterminados. Si ahora crea un recurso a través de la implementación clásica, el recurso se crea automáticamente dentro de un grupo de recursos predeterminado para el servicio, aunque no se especifique dicho grupo de recursos durante la implementación. Sin embargo, solo el hecho de existir dentro de un grupo de recursos no significa que el recurso se haya convertido al modelo del Administrador de recursos. En la sección siguiente veremos cómo cada servicio controla los dos modelos de implementación.

## Descripción de la compatibilidad para los modelos 

Al decidir qué modelo de implementación se usará para los recursos, hay tres escenarios a tener en cuenta:

1. El servicio admite Resource Manager y proporciona un solo tipo.
2. El servicio admite Resource Manager pero proporciona dos tipos: uno para Resource Manager y otro para el modelo clásico. Esto se aplica solo a las máquinas virtuales, las cuentas de almacenamiento y las redes virtuales.
3. El servicio no admite Resource Manager.

Para averiguar si un servicio admite Resource Manager o no, consulte [Proveedores, regiones, versiones de API y esquemas de Resource Manager](resource-manager-supported-services.md).

Si el servicio que desea usar no admite Resource Manager, debe continuar usando la implementación clásica.

Si el servicio admite Resource Manager y **no es** una máquina virtual, cuenta de almacenamiento o red virtual, puede usar Resource Manager sin problemas.

En el caso de las máquinas virtuales, las cuentas de almacenamiento y las redes virtuales, si el recurso se creó mediante la implementación clásica, debe continuar trabajando en él mediante las operaciones clásicas. Si la máquina virtual, la cuenta de almacenamiento o la red virtual se crearon con la implementación de Resource Manager, debe continuar usando operaciones de Resource Manager. Esta distinción puede resultar confusa, especialmente cuando la suscripción contiene una combinación de los recursos creados mediante Resource Manager y la implementación clásica. Esta combinación de recursos puede crear resultados inesperados porque los recursos no son compatibles con las mismas operaciones.

En algunos casos, un comando de Resource Manager puede recuperar información sobre un recurso creado mediante la implementación clásica, o puede realizar tareas administrativas tales como mover un recurso clásico a otro grupo de recursos, pero estos casos no deben dar la impresión de que el tipo es compatible con las operaciones de Resource Manager. Por ejemplo, supongamos que tiene un grupo de recursos que contiene una máquina virtual creada con la implementación clásica. Si ejecuta el siguiente comando de PowerShell para Resource Manager:

    Get-AzureRmResource -ResourceGroupName ExampleGroup -ResourceType Microsoft.ClassicCompute/virtualMachines

Devolverá la máquina virtual:
    
    Name              : ExampleClassicVM
    ResourceId        : /subscriptions/{guid}/resourceGroups/ExampleGroup/providers/Microsoft.ClassicCompute/virtualMachines/ExampleClassicVM
    ResourceName      : ExampleClassicVM
    ResourceType      : Microsoft.ClassicCompute/virtualMachines
    ResourceGroupName : ExampleGroup
    Location          : westus
    SubscriptionId    : {guid}

Sin embargo, el cmdlet **Get-AzureRmVM** de Resource Manager solo devuelve las máquinas virtuales implementadas mediante Resource Manager. El siguiente comando no devuelve la máquina virtual creada mediante la implementación clásica.

    Get-AzureRmVM -ResourceGroupName ExampleGroup

Hay otras consideraciones importantes a la hora de trabajar con máquinas virtuales.

- Las máquinas virtuales implementadas con el modelo de implementación clásica no pueden incluirse en una red virtual implementada con el Administrador de recursos.
- Las máquinas virtuales implementadas con el modelo de implementación del Administrador de recursos deben incluirse en una red virtual.
- Las máquinas virtuales implementadas con el modelo de implementación clásica no deben incluirse en una red virtual.

Para obtener información sobre cómo conectar redes virtuales de diferentes modelos de implementación, consulte [Conexión de redes virtuales clásicas a redes virtuales nuevas](./virtual-network/virtual-networks-arm-asm-s2s.md).

Solo los recursos creados a través del Administrador de recursos son compatibles con las etiquetas. No puede aplicar etiquetas a los recursos clásicos. Para obtener más información sobre el uso de las etiquetas en el Administrador de recursos, consulte [Uso de etiquetas para organizar los recursos de Azure](resource-group-using-tags.md).

## Características del Administrador de recursos

Para ayudarle a comprender los dos modelos, revisemos las características de los tipos de Resource Manager:

- Creado mediante el [Portal de Azure](https://portal.azure.com/).

     ![Portal de Azure](./media/resource-manager-deployment-model/portal.png)

     Para los recursos de Proceso, Almacenamiento y redes, puede usar el Administrador de recursos o la implementación clásica. Select **Administrador de recursos**.

     ![Implementación de Resource Manager](./media/resource-manager-deployment-model/select-resource-manager.png)

- Creado con la versión para Resource Manager de los cmdlets de Azure PowerShell. Estos comandos tienen el formato *Verb-AzureRmNoun*, tal y como se muestra a continuación.

        New-AzureRmResourceGroupDeployment

- Creado mediante la [API de REST de Azure Resource Manager](https://msdn.microsoft.com/library/azure/dn790568.aspx) para las operaciones REST.

- Creado mediante los comandos de la CLI de Azure ejecutados en modo **arm**.

        azure config mode arm
        azure group deployment create 

- El tipo de recurso no incluye **(clásico)** en el nombre. La imagen siguiente muestra el tipo como **cuenta de almacenamiento**.

    ![aplicación web](./media/resource-manager-deployment-model/resource-manager-type.png)

La aplicación que se muestra en el diagrama siguiente muestra cómo los recursos implementados a través del Administrador de recursos están incluidos en un único grupo de recursos.

  ![Arquitectura de Resource Manager](./media/virtual-machines-azure-resource-manager-architecture/arm_arch3.png)

Además, hay relaciones entre los recursos de los proveedores de recursos:

- Una máquina virtual depende de una cuenta de almacenamiento específica definida en el SRP para almacenar sus discos en el almacenamiento de blobs (obligatorio).
- Una máquina virtual hace referencia a una NIC específica definida en el NRP (obligatorio) y un conjunto de disponibilidad definido en el CRP (opcional).
- Una NIC hace referencia a la dirección IP asignada a la máquina virtual (necesaria), la subred de la red virtual para la máquina virtual (necesaria) y a un grupo de seguridad de red (opcional).
- Una subred dentro de una red virtual hace referencia a un grupo de seguridad de red (opcional).
- Una instancia de equilibrador de carga hace referencia al grupo de backend de direcciones IP que incluye la NIC de una máquina virtual (opcional) y hace referencia a una dirección IP pública o privada del equilibrador de carga (opcional).

## Características de implementación clásica

También puede conocer el modelo de implementación clásica como modelo de administración de servicios.

Los recursos creados en el modelo de implementación clásica comparten las siguientes características:

- Creado mediante el [Portal de Azure](https://manage.windowsazure.com).

     ![Portal clásico](./media/resource-manager-deployment-model/classic-portal.png)

     O bien, el Portal de Azure y el usuario deben especificar la implementación **clásica** (para Proceso, Almacenamiento y Redes).

     ![Implementación clásica](./media/resource-manager-deployment-model/select-classic.png)

- Creado mediante la versión para Service Management de los cdmlets de Azure PowerShell. Estos nombres de comandos tienen el formato *Verb-AzureNoun*, tal y como se muestra a continuación.

        New-AzureVM 

- Creado mediante la [API de REST de Service Management](https://msdn.microsoft.com/library/azure/ee460799.aspx) para las operaciones REST.
- Creado mediante los comandos de la CLI de Azure ejecutados en modo **asm**.

        azure config mode asm
        azure vm create 

- El tipo de recurso incluye **(clásico)** en el nombre. La imagen siguiente muestra el tipo como **cuenta de almacenamiento (clásica)**.

    ![tipo clásico](./media/resource-manager-deployment-model/classic-type.png)

Todavía puede usar el Portal de Azure para administrar los recursos creados a través de la implementación clásica.

En Administración de servicios de Azure, los recurss de cálculo, almacenamiento o para hospedar máquinas virtuales son proporcionados por:

- Un servicio de nube requerido que actúa como contenedor para hospedar máquinas virtuales (cálculo). Las máquinas virtuales se proporcionan automáticamente con una tarjeta de interfaz de red (NIC) y una dirección IP asignada por Azure. Además, el servicio de nube contiene una instancia de equilibrador de carga externa, una dirección IP pública y extremos predeterminados para permitir un escritorio remoto y tráfico de PowerShell remoto para máquinas virtuales basadas en Windows y tráfico de Secure Shell (SSH) para máquinas virtuales basadas en Linux.
- Una cuenta de almacenamiento necesaria que almacena discos duros virtuales para una máquina virtual, incluido el sistema operativo, los discos de datos temporales y adicionales (almacenamiento).
- Una red virtual opcional que actúa como un contenedor adicional, en el que se puede crear una estructura de subredes y designar la subred en la que se encuentra la máquina virtual (red).

Aquí se encuentran los componentes y sus relaciones para la administración de servicios de Azure.

  ![arquitectura clásica](./media/virtual-machines-azure-resource-manager-architecture/arm_arch1.png)

## Migración del modelo clásico a Resource Manager

Si está listo para migrar los recursos de la implementación clásica a la implementación de Resource Manager, consulte:

1. [Profundización técnica en la migración compatible con la plataforma de la implementación clásica a la de Azure Resource Manager](./virtual-machines/virtual-machines-windows-migration-classic-resource-manager-deep-dive.md)
2. [Migración compatible con la plataforma de recursos de IaaS del modelo clásico a Azure Resource Manager](./virtual-machines/virtual-machines-windows-migration-classic-resource-manager.md)
3. [Migración de recursos de IaaS de la implementación clásica a la de Resource Manager con Azure PowerShell](./virtual-machines/virtual-machines-windows-ps-migration-classic-resource-manager.md)
4. [Migración de recursos de IaaS de la implementación clásica a Azure Resource Manager con la CLI de Azure](./virtual-machines/virtual-machines-linux-cli-migration-classic-resource-manager.md)

## Pasos siguientes

- Para ver un tutorial sobre la creación de la plantilla que define una máquina virtual, una cuenta de almacenamiento y una red virtual, consulte [Tutorial de la plantilla de Resource Manager](resource-manager-template-walkthrough.md).
- Para obtener información acerca de la estructura de las plantillas de Resource Manager, consulte [Creación de plantillas de Azure Resource Manager](resource-group-authoring-templates.md).
- Para ver los comandos para implementar una plantilla, consulte [Implementación de una aplicación con la plantilla del Administrador de recursos de Azure](resource-group-template-deploy.md).

<!---HONumber=AcomDC_0803_2016-->