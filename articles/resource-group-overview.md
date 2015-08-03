<properties
   pageTitle="Información general del Administrador de recursos de Azure"
   description="Describe cómo utilizar Administrador de recursos de Azure para la implementación, la administración y el control de acceso de los recursos en Azure."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/15/2015"
   ms.author="tomfitz"/>

# Información general del Administrador de recursos de Azure

Las aplicaciones normalmente se componen de muchos componentes: quizá una aplicación web, base de datos, servidor de base de datos, almacenamiento y servicios de terceros. Estos componentes no se ven como entidades independientes, sino como partes de una sola entidad relacionadas e interdependientes. Desea implementarlas, administrarlas y supervisarlas como grupo. Administrador de recursos de Azure permite trabajar con los recursos de la aplicación como un grupo. Puede implementar, actualizar o eliminar todos los recursos de la aplicación en una operación única y coordinada. Para la implementación se utiliza una plantilla, y esta plantilla puede trabajar en diferentes entornos, como pruebas, ensayo y producción. Puede aclarar la facturación de la organización consultando los costes acumulados de todo el grupo.

Administrador de recursos de Azure integra de forma nativa el control de acceso en la plataforma de administración, para que pueda especificar las acciones que puede realizar un usuario de la organización en un grupo de recursos.

> [AZURE.NOTE]En este tema se describen los recursos, grupos y plantillas y se utiliza el Portal de vista previa para mostrar los conceptos. Sin embargo, también se pueden crear, administrar y eliminar recursos de Azure con la [CLI de Azure para Mac, Linux y Windows](virtual-machines/xplat-cli-azure-resource-manager.md), así como con [PowerShell](powershell-azure-resource-manager.md).

## Grupos de recursos

Un grupo de recursos es un contenedor que incluye los recursos relacionados de una aplicación. El grupo de recursos puede incluir todos los recursos de una aplicación o solo aquellos que se agrupan juntos lógicamente. Puede decidir cómo desea asignar los recursos a los grupos de recursos en función de lo que más convenga a su organización.

Hay algunos factores importantes que se deben tener en cuenta al definir el grupo de recursos:

1. Todos los recursos del grupo deben compartir el mismo ciclo de vida. Se implementarán, actualizarán y eliminarán de forma conjunta. Si un recurso, como un servidor de base de datos, debe existir en un ciclo de implementación diferente, debe estar en otro grupo de recursos.
2. Cada recurso solo puede existir en un grupo de recursos.
3. Puede agregar o quitar un recurso de un grupo de recursos en cualquier momento.
4. Un grupo de recursos puede contener recursos que residen en diferentes regiones.
5. Un grupo de recursos puede utilizarse para definir el ámbito de control de acceso para las acciones administrativas.

En el Portal de vista previa de Azure, todos los recursos nuevos se crean en un grupo de recursos. Incluso si crea simplemente un único recurso como un sitio web, debe decidir si desea agregar ese recurso a un grupo existente o bien crear un nuevo grupo para él.

La siguiente imagen muestra un grupo de recursos con un sitio web, una base de datos y Application Insights.

![resumen del grupo de recursos](./media/resource-group-overview/resourcegroupsummary.png)

Un grupo de recursos también se puede vincular a un recurso de otro grupo de recursos. Se considera que un recurso está vinculado cuando existe una dependencia de implementación entre recursos de distintos grupos de recursos. Por ejemplo, si una aplicación web de un grupo de recursos se conecta a la base de datos dn otro grupo de recursos, estos recursos están vinculados.

![recurso vinculado](./media/resource-group-overview/linkedresource.png)

Desde el Portal de vista previa, puede ver fácilmente los costos, supervisar eventos y administrar alertas. La siguiente imagen muestra la facturación consolidada de un grupo.

![facturación](./media/resource-group-overview/billing.png)

## Implementación de plantilla

Con Administrador de recursos de Azure, puede crear una plantilla sencilla (en formato JSON) que define la implementación y configuración de la aplicación. Esta plantilla se conoce como una plantilla de Azure y proporciona una manera declarativa para definir la implementación. El uso de una plantilla permite implementar la aplicación repetidamente a lo largo del ciclo de vida de esta y tener la seguridad de que los recursos se implementan de forma coherente.

Dentro de la plantilla puede definir la infraestructura de la aplicación, cómo configurar dicha infraestructura y cómo publicar el código de aplicación en ella. No necesita preocuparse por el orden de implementación porque Administrador de recursos de Azure analiza las dependencias para asegurarse de que los recursos se crean en el orden correcto.

También puede utilizar la plantilla para las actualizaciones de la infraestructura. Por ejemplo, puede agregar un nuevo recurso a la aplicación y agregar reglas de configuración para los recursos que ya están implementados. Si la plantilla especifica la creación de un nuevo recurso pero ese recurso ya existe, Administrador de recursos de Azure realiza una actualización en lugar de crear un nuevo recurso. Administrador de recursos de Azure actualiza el activo existente al mismo estado que tendría si fuese nuevo.

Puede especificar parámetros en la plantilla para permitir la personalización y conseguir flexibilidad en la implementación. Por ejemplo, puede pasar valores de parámetro que adapten la implementación al entorno de prueba. Mediante la especificación de parámetros, puede utilizar la misma plantilla para la implementación en todos los entornos de la aplicación.

Puede realizar todos los pasos necesarios para la implementación y la configuración a través de la plantilla y no deberían quedar pasos restantes manuales. Administrador de recursos de Azure proporciona extensiones para escenarios en los que se necesitan operaciones adicionales, como la instalación de un software determinado que no está incluido en el programa de instalación. Si ya utiliza un servicio de administración de configuración, como DSC, Chef o Puppet, puede seguir trabajando con ese servicio mediante las extensiones.

Cuando crea una solución desde Marketplace, la solución incluye automáticamente una plantilla de implementación. No tiene que crear la plantilla desde cero, puede empezar con la plantilla para la solución y personalizarla para satisfacer sus necesidades específicas.

Por último, la plantilla se convierte en parte del código fuente de la aplicación. Puede protegerla en el repositorio de código fuente y actualizarla a medida que evoluciona la aplicación. Puede editar la plantilla mediante Visual Studio.

Para obtener más información sobre la creación de plantillas, consulte [Crear plantillas del Administrador de recursos de Azure](./resource-group-authoring-templates.md).

Para los esquemas de plantilla, consulte [Esquemas del Administrador de recursos de Azure](https://github.com/Azure/azure-resource-manager-schemas).

Para obtener información acerca del uso de una plantilla para la implementación, consulte [Implementación de una aplicación con la plantilla del Administrador de recursos de Azure](azure-portal/resource-group-template-deploy.md) e [Implementación predecible de una aplicación compleja en Azure](app-service-web/app-service-deploy-complex-application-predictably.md).

## Etiquetas

Administrador de recursos de Azure proporciona una característica de etiquetado que permite clasificar los recursos de acuerdo con los requisitos de administración o facturación. Es recomendable usar etiquetas cuando se tiene un conjunto complejo de grupos de recursos y de recursos, y se necesitan visualizar estos activos de la manera más conveniente. Por ejemplo, puede etiquetar recursos que cumplen una función similar en la organización o que pertenecen al mismo departamento.

En el portal de vista previa puede comenzar a trabajar con etiquetas haciendo clic en el icono de etiqueta.

![etiquetas](./media/resource-group-overview/tags.png)

Los recursos no tienen que residir en el mismo grupo de recursos para compartir una etiqueta. Puede crear su propia taxonomía de etiquetas para asegurarse de que todos los usuarios de la organización utilizan etiquetas comunes y no aplican accidentalmente etiquetas ligeramente diferentes (por ejemplo, "dept" en lugar de "departamento").

Para obtener más información sobre las etiquetas, consulte [Uso de etiquetas para organizar los recursos de Azure](./resource-group-using-tags.md).

## Control de acceso

Administrador de recursos de Azure permite controlar quién tiene acceso a acciones específicas para la organización. Integra de forma nativa OAuth y Control de acceso basado en rol (RBAC) en la plataforma de administración y aplica este control de acceso a todos los servicios del grupo de recursos. Puede agregar usuarios a roles específicos de plataforma y recurso predefinidos, y aplicar estos roles a una suscripción, un grupo de recursos o un recurso para limitar el acceso. Por ejemplo, puede aprovechar el rol predefinido denominado Colaborador de Base de datos de SQL que permite a los usuarios administrar bases de datos pero no servidores de base de datos ni directivas de seguridad. Puede agregar usuarios de la organización que necesitan este tipo de acceso al rol Colaborador de Base de datos de SQL y aplicar el rol a la suscripción, al grupo de recursos o al recurso.

En el portal de vista previa puede definir el control de acceso haciendo clic en el botón de acceso.

![control de acceso de usuario](./media/resource-group-overview/access.png)

Administrador de recursos de Azure registra automáticamente las acciones del usuario para la auditoría.

También puede bloquear explícitamente recursos críticos para impedir que los usuarios los eliminen o modifiquen.

Para obtener más información acerca del control de acceso basado en rol, consulte [Control de acceso basado en rol en el Portal de vista previa de Azure](./role-based-access-control-configure.md).

Para obtener ejemplos de configuración de directivas de acceso, consulte [Administración y auditoría del acceso a los recursos](azure-portal/resource-group-rbac.md).

## Capa de administración coherente

Administrador de recursos de Azure proporciona operaciones totalmente compatibles a través de Azure PowerShell, CLI de Azure para Mac, Linux y Windows, Portal de Azure o la API de REST. Puede utilizar la interfaz que más le convenga y moverse rápidamente entre las interfaces sin confusión. El portal incluso muestra notificación sobre las acciones realizadas fuera de él.

Para obtener información sobre PowerShell, consulte [Uso de Azure PowerShell con Administrador de recursos](./powershell-azure-resource-manager.md) y [Cmdlets de Administrador de recursos de Azure](https://msdn.microsoft.com/library/azure/dn757692.aspx).

Para obtener información sobre CLI de Azure, consulte [Uso de la CLI de Azure para Mac, Linux y Windows con administración de recursos de Azure](./virtual-machines/xplat-cli-azure-resource-manager.md)).

Para obtener información acerca de la API de REST, consulte [Referencia de la API de REST del Administrador de recursos de Azure](https://msdn.microsoft.com/library/azure/dn790568.aspx).

## Pasos siguientes
Introducción

- [Uso de Azure PowerShell con el Administrador de recursos](./powershell-azure-resource-manager.md)
- [Uso de la CLI de Azure para Mac, Linux y Windows con administración de recursos de Azure](./virtual-machines/xplat-cli-azure-resource-manager.md)
- [Uso del Portal de Azure para administrar los recursos de Azure](azure-portal/resource-group-portal.md)

Creación e implementación de aplicaciones

- [Creación de plantillas](./resource-group-authoring-templates.md)
- [Implementación de plantillas](azure-portal/resource-group-template-deploy.md)
- [Solución de problemas de implementaciones de grupo de recursos en Azure](virtual-machines/resource-group-deploy-debug.md)
- [Implementación predecible de una aplicación compleja en Azure](app-service-web/app-service-deploy-complex-application-predictably.md)
- [Implementación de recursos de Azure mediante bibliotecas de .NET y una plantilla](virtual-machines/arm-template-deployment.md)
- [Funciones de plantillas](./resource-group-template-functions.md)
- [Operaciones avanzadas de plantilla](./resource-group-advanced-template.md)
- [Esquemas de plantilla](https://github.com/Azure/azure-resource-manager-schemas)

Organización de los recursos

- [Uso de etiquetas para organizar los recursos de Azure](./resource-group-using-tags.md)

Administración y auditoría del acceso

- [Administración y auditoría del acceso a los recursos](azure-portal/resource-group-rbac.md)
- [Control de acceso basado en rol en el Portal de vista previa de Azure](./role-based-access-control-configure.md)
- [Autenticación de una entidad de servicio con el Administrador de recursos de Azure](./resource-group-authenticate-service-principal.md)
- [Creación de una nueva entidad de servicio de Azure mediante el portal de Azure](./resource-group-create-service-principal-portal.md)

<!---HONumber=July15_HO4-->