
<properties 
   pageTitle="Notas de la versión de SDK de Azure para .NET 2.7" 
   description="Notas de la versión de SDK de Azure para .NET 2.7" 
   services="app-service\web" 
   documentationCenter=".net" 
   authors="Juliako" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="app-service"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="08/04/2015"
   ms.author="juliako"/>


# Notas de la versión de SDK de Azure para .NET 2.7

Este documento contiene las notas de la versión de SDK de Azure para la versión .NET 2.7.

Azure SDK 2.7 solo es compatible en Visual Studio 2015 y Visual Studio 2013. [Azure SDK 2.6](http://azure.microsoft.com/downloads/) es el último SDK compatible para Visual Studio 2012.

Para obtener información detallada acerca de esta publicación, consulte la [Publicación de anuncio de Azure SDK 2.7](https://azure.microsoft.com/blog/2015/07/20/announcing-the-azure-sdk-2-7-for-net/).

##Mejoras en el inicio de sesión de Visual Studio de 2015

Azure SDK 2.7 para Visual Studio 2015 es compatible con las nuevas características de administración de identidades en Visual Studio 2015. Esto incluye la compatibilidad para las cuentas de acceso a Azure mediante control de acceso basado en rol, proveedores de soluciones en la nube, DreamSpark y otros tipos de cuenta y suscripción.

Estas mejoras en el inicio de sesión solo están disponibles en Visual Studio de 2015. La compatibilidad con Visual Studio 2013 se incluirá en una futura actualización.


##SDK de dispositivos móvil

Plantillas de **Aplicaciones móviles** para reflejar el [paquete de NuGet](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/) y el proceso de configuración más actualizados

##Bus de servicio 

Mejoras y correcciones de errores generales. Para obtener información detallada sobre las actualizaciones y otras características, consulte las notas de la versión del [NuGet de bus de servicio](http://www.nuget.org/packages/WindowsAzure.ServiceBus/) más reciente.

##Herramientas de HDInsight 

En esta versión se han realizado las siguientes actualizaciones. Estas actualizaciones se encuentran en vista previa. Para obtener más información, consulte [este blog](http://go.microsoft.com/fwlink/?LinkId=619108).

- Gráficos de Hive para Hive en trabajos de Tez
- Compatibilidad completa de IntelliSense de DML de Hive
- Compatibilidad con plantillas de Pig
- Plantillas de Storm para servicios de Azure

###Cambios drásticos

- El proyecto de **Storm** antiguo debe actualizarse cuando se usa esta versión de las herramientas. Para obtener más información, consulte [este blog](http://go.microsoft.com/fwlink/?LinkId=619108).
- Visual Studio Web Express ya no es compatible. Para obtener más información, consulte [este blog](http://go.microsoft.com/fwlink/?LinkId=619108).

##Herramientas del Servicio de aplicaciones de Azure

En esta versión se han realizado las siguientes actualizaciones en Web Tools Extensions. Para obtener más información, consulte [este blog](https://azure.microsoft.com/blog/2015/07/20/announcing-the-azure-sdk-2-7-for-net/).

- Compatibilidad agregada para las cuentas de DreamSpark
- Cambio completo en Azure Tools realizado para admitir las nuevas API de Administración de recursos de Azure
- Compatibilidad de Servicio de aplicaciones de Azure agregado a [Cloud Explorer](azure-sdk-dot-net-release-notes-2_7.md#cloud_explorer)

###Problemas conocidos

Los nodos de ranura de implementación de aplicación web no aparecen en el nodo de ranura en Explorador de servidores los nodos secundarios de ranura de implementación de aplicación web no se cargan en Cloud Explorer. Este problema se ha resuelto y preparado en la próxima versión SDK.


##<a id="cloud_explorer"></a>Cloud Explorer de Visual Studio de 2015

Azure SDK 2.7 incluye Cloud Explorer para Visual Studio de 2015, que le permitirá ver los recursos de Azure, inspeccionar sus propiedades y realizar acciones de desarrollador clave desde dentro de Visual Studio.

Cloud Explorer admite lo siguiente:

- Vistas de grupo de recursos y tipo de recurso de los recursos de Azure 
- Búsqueda de recursos por nombre (disponible en la vista de tipo de recurso)
- Compatibilidad con las suscripciones y los recursos que tienen aplicado el control de acceso basado en rol (RBAC) 
- Panel de acción integrado que muestra las acciones orientadas a desarrolladores específicas para los recursos seleccionados. Por ejemplo: adjunte el depurador remoto para las máquinas virtuales creadas con la pila del Administrador de recursos, Ver datos del diagnóstico para máquinas virtuales, etc.
- Panel de propiedades integrado que muestra propiedades orientadas a desarrolladores normalmente necesarias durante el desarrollo y las pruebas 
- Cambio rápido de la cuenta que se utilizará al enumerar los recursos (utilice el comando Configuración de la barra de herramientas) 
- Filtrado de suscripciones que se utilizará al enumerar los recursos (utilice el comando Configuración de la barra de herramientas) 
- Vínculos profundos en el Portal de vista previa de Azure para administración de recursos y grupos de recursos 
 
 
##Herramientas del Administrador de recursos de Azure 

Las herramientas del Administrador de recursos de Azure se han actualizado para trabajar con el control de acceso basado en rol (RBAC) y nuevos tipos de suscripción. Estos cambios incluyen la capacidad de usar nuevas cuentas de almacenamiento, además de almacenamiento clásico para almacenar artefactos durante la implementación.

Si está utilizando un proyecto del grupo de recursos de Azure desde una versión anterior del SDK con SDK 2.7, es necesario un nuevo script de implementación para implementar mediante una nueva cuenta de almacenamiento en lugar de almacenamiento clásico. Se le solicitará antes de realizar cambios en el proyecto que agregue el nuevo script. Se cambiará el nombre del script anterior y tendrá que realizar modificaciones manualmente en el nuevo script.
 
 
##Herramientas del explorador de almacenamiento 

- Compatibilidad con la visualización de los blobs de anexión. Puede obtener más información en [esta entrada de blog](http://blogs.msdn.com/b/windowsazurestorage/archive/2015/04/13/introducing-azure-storage-append-blob.aspx). 
- Soporte técnico para ver las cuentas de almacenamiento premium a través del Explorador de servidores. El Explorador de servidores solo mostrará los blobs de página de cuentas de almacenamiento premium como si fuera el único tipo compatible para cuentas de almacenamiento premium.

##Herramientas de Factoría de datos de Azure para Visual Studio 

Introducción a **Herramientas de Factoría de datos de Azure** para Visual Studio. A continuación se muestran las características habilitadas. Para obtener más información, consulte [este blog](http://go.microsoft.com/fwlink/?LinkId=617530).

- **Creación basada en plantillas**: seleccione plantillas de procesamiento de datos, de movimiento de datos o basadas en casos de uso para implementar una solución de integración de datos completa e iniciar la experiencia práctica rápidamente con Factoría de datos. 
- **Integración con el Explorador de soluciones para la creación e implementación de entidades de Factoría de datos**: cree e implemente canalizaciones y las entidades relacionadas como proyectos de Visual Studio. 
- **Integración con vista de diagrama de interacción visual durante la creación**: cree visualmente las canalizaciones y conjuntos de datos con ayuda de la vista de diagrama. 
- **Integración con el Explorador de servidores para la exploración y la interacción con las entidades ya implementadas**: aproveche el Explorador de servidores para examinar las factorías de datos ya implementadas y las entidades correspondientes. Importe una factoría de datos implementada o cualquier identidad (canalización, servicios vinculados o conjuntos de datos) en el proyecto. 
- **Edición de JSON con Intellisense enriquecido y validación de esquema**: configure y edite de forma eficiente documentos JSON de entidades de Factoría de datos con e Intellisense enriquecido y validación de esquema 
- **Publicación en varios entornos**: publique canalizaciones creadas para desarrollar, probar o producir un entorno mediante la creación de archivos de configuración para cada entorno.
- **Soporte técnico de procesamiento de datos basado en Pig, Hive y .Net**: soporte técnico para scripts de Pig y Hive en el proyecto de Factoría de datos. Soporte técnico para hacer referencia al proyecto de C# para la administración de .Net Activity.

##Consulte también:

[Publicación de anuncio de Azure SDK 2.7](https://azure.microsoft.com/blog/2015/07/20/announcing-the-azure-sdk-2-7-for-net/)

<!---HONumber=August15_HO6-->