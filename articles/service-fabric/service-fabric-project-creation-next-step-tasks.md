---
title: "Pasos siguientes de la creación del proyecto de Service Fabric | Microsoft Docs"
description: "Obtenga información sobre el proyecto de aplicación que acaba de crear en Visual Studio.  Obtenga información acerca de cómo crear servicios mediante tutoriales y aprenda a desarrollar servicios para Service Fabric."
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 299d1f97-1ca9-440d-9f81-d1d0dd2bf4df
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/07/2017
ms.author: rwike77
ms.openlocfilehash: 17eb1e7c2184fe9cae19685a47ea80716292b754
ms.sourcegitcommit: 42ee5ea09d9684ed7a71e7974ceb141d525361c9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/09/2017
---
# <a name="your-service-fabric-application-and-next-steps"></a>Su aplicación de Service Fabric y próximos pasos
La aplicación Azure Service Fabric se ha creado. En este artículo se describen algunos tutoriales que puede probar, la composición del proyecto, información adicional que podría interesarle y posibles pasos siguientes.

## <a name="get-started-with-tutorials-walk-throughs-and-samples"></a>Introducción a los tutoriales, recorridos y ejemplos
¿Ya está listo para comenzar?  

Trabajar con el tutorial de aplicación .NET. Obtenga información acerca de cómo [compilar una aplicación](service-fabric-tutorial-create-dotnet-app.md) con un front-end ASP.NET Core y un back-end con estado, [implementar la aplicación](service-fabric-tutorial-deploy-app-to-party-cluster.md) en un clúster, [configurar CI/CD](service-fabric-tutorial-deploy-app-with-cicd-vsts.md) y [configurar la supervisión y el diagnóstico](service-fabric-tutorial-monitoring-aspnet.md).

O bien pruebe uno de los recorridos y cree por primera vez...
- [Servicio C# de Reliable Services en Windows](service-fabric-reliable-services-quick-start.md) 
- [Servicio C# de Reliable Actors en Windows](service-fabric-reliable-actors-get-started.md) 
- [Servicio ejecutable de invitado en Windows](quickstart-guest-app.md) 
- [Aplicación contenedora de Windows](service-fabric-get-started-containers.md) 

Puede que también esté interesado en probar nuestras [aplicaciones de ejemplo](http://aka.ms/servicefabricsamples).

## <a name="have-questions-or-feedback--need-to-report-an-issue"></a>¿Tiene preguntas o comentarios?  ¿Necesita informar de un problema?
Lea las [preguntas más frecuentes](service-fabric-common-questions.md) y busque respuestas sobre lo que puede hacer Service Fabric y cómo se debe usar.

Las [opciones de soporte técnico](service-fabric-support.md) incluyen foros de StackOverflow y MSDN para formular preguntas, así como opciones para informar de problemas, obtener soporte técnico y enviar comentarios sobre el producto.

## <a name="the-application-project"></a>El proyecto de aplicación
Cada nueva aplicación incluye un proyecto de aplicación. Puede haber uno o dos proyectos adicionales según el tipo de servicio elegido.

El proyecto de aplicación consiste en:

* Un conjunto de referencias a los servicios que conforman la aplicación.
* Tres perfiles de publicación (1 nodo local, 5 nodos local y en la nube) que puede usar para conservar las preferencias para trabajar con entornos diferentes, tales como las preferencias relacionadas con un punto de conexión de clúster y si va a realizar implementaciones de actualización de forma predeterminada.
* Tres archivos de parámetro de aplicación (los mismos que se indican anteriormente) que puede usar para mantener configuraciones de aplicaciones específicas del entorno, como el número de particiones para crear un servicio. Aprenda a [configurar la aplicación para varios entornos](service-fabric-manage-multiple-environment-app-configuration.md).
* Un script de implementación que puede usar para implementar la aplicación desde la línea de comandos o como parte de una canalización de integración e implementación continua automatizada. Obtenga más información sobre la [implementación de aplicaciones con PowerShell](service-fabric-deploy-remove-applications.md).
* El manifiesto de aplicación que describe la aplicación. Puede encontrar el manifiesto en la carpeta ApplicationPackageRoot. Más información sobre los [manifiestos de servicio y aplicación](service-fabric-application-model.md).



## <a name="learn-more-about-the-programming-models"></a>Más información sobre los modelos de programación
Service Fabric ofrece varias maneras de escribir y administrar los servicios.  Aquí se ofrece información general y conceptual sobre [Reliable Services con y sin estado](service-fabric-reliable-services-introduction.md), [Reliable Actors](service-fabric-reliable-actors-introduction.md), [contenedores](service-fabric-containers-overview.md), [ejecutables de invitado ](service-fabric-deploy-existing-app.md), [servicios de ASP.NET Core con y sin estado](service-fabric-reliable-services-communication-aspnetcore.md).

## <a name="learn-about-service-communication"></a>Obtenga información acerca de la comunicación de servicio
Una aplicación de Service Fabric se compone de muchos servicios diferentes y cada uno de ellos realiza una tarea especializada. Estos servicios pueden comunicarse entre sí y puede haber aplicaciones de cliente fuera del clúster que se conectan y comunican con los servicios. Más información sobre cómo [configurar la comunicación con los servicios y entre ellos](service-fabric-connect-and-communicate-with-services.md) en Service Fabric. 

## <a name="learn-about-configuring-application-security"></a>Más información sobre cómo configurar la seguridad de la aplicación
Puede proteger las aplicaciones que se ejecutan en el clúster con distintas cuentas de usuario. Service Fabric también protege los recursos que usan las aplicaciones en el momento de la implementación con la cuenta de usuario; por ejemplo, archivos, directorios y certificados. Esto aumenta la seguridad entre aplicaciones en ejecución, incluso en un entorno hospedado compartido.  Más información sobre cómo [configurar directivas de seguridad para la aplicación](service-fabric-application-runas-security.md).

La aplicación puede contener información confidencial, como cadenas de conexión de almacenamiento, contraseñas u otros valores que no se deben administrar en texto sin formato. Más información acerca de cómo [administrar secretos en la aplicación](service-fabric-application-secret-management.md).

## <a name="learn-about-the-application-lifecycle"></a>Más información acerca del ciclo de vida de la aplicación
Al igual que sucede con otras plataformas, una aplicación de Service Fabric, normalmente, pasa las siguientes fases: diseño, desarrollo, prueba, implementación, actualización, mantenimiento y eliminación. [En este artículo](service-fabric-application-lifecycle.md) se proporciona información general de las API y cómo las usan los distintos roles durante todas las fases del ciclo de vida de aplicación de Service Fabric.

## <a name="next-steps"></a>Pasos siguientes
- [Cree un clúster de Windows en Azure](service-fabric-tutorial-create-vnet-and-windows-cluster.md).
- Visualice el clúster, incluidas las aplicaciones implementadas y el diseño físico, con [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md).
- [Control de versiones y actualización de los servicios](service-fabric-application-upgrade-tutorial.md)


