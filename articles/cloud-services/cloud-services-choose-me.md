---
title: Opciones de proceso de Azure (Cloud Services) | Microsoft Docs
description: "Obtenga información sobre cómo Azure hospeda las opciones y cómo funcionan: Servicio de aplicaciones, servicios en la nube y máquinas virtuales"
services: cloud-services
documentationcenter: 
author: Thraka
manager: timlt
ms.assetid: ed7ad348-6018-41bb-a27d-523accd90305
ms.service: multiple
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2017
ms.author: adegeo
ms.openlocfilehash: e8053b74e0e4d721523f49bcbb9e33b08bb7a1dc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="should-i-choose-cloud-services-or-something-else"></a>¿Debo elegir los servicios en la nube o alguna otra opción?
¿Son los servicios en la nube de Azure la opción que mejor se adapta a sus necesidades? Azure proporciona distintos modelos de hospedaje para ejecutar aplicaciones. Cada una de ellas proporciona un conjunto diferente de servicios; por tanto, lo que elija dependerá exactamente lo intenta hacer.

[!INCLUDE [compute-table](../../includes/compute-options-table.md)]

<a name="tellmecs"></a>

## <a name="tell-me-about-cloud-services"></a>Información sobre los servicios en la nube
Los servicios en la nube son un ejemplo de [plataforma como servicio](https://azure.microsoft.com/overview/what-is-paas/) (PaaS). Al igual que [Servicio de aplicaciones](../app-service/app-service-web-overview.md), esta tecnología está diseñada para ser compatible con aplicaciones escalables, confiables y de funcionamiento asequible. Al igual que un Servicio de aplicaciones, se hospedan en máquinas virtuales, así que también son servicios en la nube; sin embargo, se tiene más control sobre las máquinas virtuales. Puede instalar su propio software en máquinas virtuales de Servicio en la nube y tener acceso remoto a ellas.

![cs_diagram](./media/cloud-services-choose-me/diagram.png)

Más control también significa menos facilidad de uso. A menos que necesite opciones de control adicionales, por lo general es más rápido y fácil poner en marcha una aplicación web y que funcione en Web Apps en App Service que en Cloud Services.

Existen dos tipos de roles de servicio en la nube. La única diferencia entre los dos es cómo se hospeda el rol en la máquina virtual.

* **Rol web**  
Implementa y hospeda automáticamente la aplicación a través de IIS.

* **Rol de trabajo**  
No usa IIS y ejecuta la aplicación independiente.

Por ejemplo, una aplicación simple podría utilizar solo un rol web, para dar servicio a un sitio web. Una aplicación más compleja podría utilizar un rol web para manejar solicitudes entrantes provenientes de los usuarios y luego transmitir esas solicitudes a un rol de trabajo para su procesamiento. (esta comunicación podría utilizar [Service Bus](../service-bus-messaging/service-bus-fundamentals-hybrid-solutions.md) o [Azure Queues](../storage/common/storage-introduction.md)).

Como sugiere la ilustración anterior, todas las VM de una sola aplicación se ejecutan en el mismo servicio en la nube. Los usuarios acceden a la aplicación a través de una sola dirección IP pública y el equilibrio de carga de las solicitudes se realiza automáticamente en todas las VM de la aplicación. La plataforma [escala e implementa](cloud-services-how-to-scale.md) las máquinas virtuales en una aplicación de Cloud Services para así evitar un único punto de errores de hardware.

A pesar de que las aplicaciones se ejecutan en máquinas virtuales, resulta importante comprender que Servicios en la nube proporciona PaaS y no IaaS. Piénselo así: con IaaS, como en Azure Virtual Machines, primero crea y configura el entorno en el que se ejecuta la aplicación, para luego implementar la aplicación en este entorno. Es responsabilidad suya administrar gran parte de este mundo, como por ejemplo, implementar nuevas versiones revisadas del sistema operativo en cada máquina virtual. Por el contrario, en PaaS ocurre como si el entorno ya existiese. Todo lo que tiene que hacer es implementar la aplicación. Usted maneja la administración de la plataforma en que se ejecuta, incluida la implementación de versiones nuevas del sistema operativo.

## <a name="scaling-and-management"></a>Escalado y administración
Con Servicios en la nube no se crean máquinas virtuales. En lugar de eso, se proporciona un archivo de configuración que le indica a Azure qué cantidad de cada una le gustaría tener, como por ejemplo, **tres instancias de rol web** y **dos instancias de rol de trabajo**, y la plataforma las crea por usted.  Sigue siendo necesario que elija [qué tamaño](cloud-services-sizes-specs.md) deben tener esas máquinas virtuales (las opciones son las mismas que con las máquinas virtuales de Azure), pero no tiene que crearlas por sí mismo explícitamente. Si la aplicación necesita manejar una carga mayor, puede pedir más VM y Azure creará esas instancias. Si la carga disminuye, puede apagar esas instancias y así dejar de pagar por ellas.

Una aplicación de Servicios en la nube normalmente está a disposición de los usuarios a través de un proceso de dos pasos. Un desarrollador primero [carga la aplicación](cloud-services-how-to-create-deploy.md) en el área de ensayo de la plataforma. Cuando el desarrollador está preparado para activar definitivamente la aplicación, utiliza Azure Portal para intercambiar el ensayo por la producción. Este [intercambio entre el área de ensayo y la producción](cloud-services-nodejs-stage-application.md) se puede llevar a cabo sin tiempo de inactividad, lo que permite actualizar una aplicación en ejecución a una versión nueva sin interrumpir ni molestar a sus usuarios.

## <a name="monitoring"></a>Supervisión
Servicios en la nube también brinda supervisión. Al igual que con Azure Virtual Machines, detecta un servidor físico con errores y reinicia las VM que se ejecutaban en ese servidor en una máquina nueva. Pero Servicios en la nube también detecta máquinas virtuales y aplicaciones con errores, no solo errores de hardware. A diferencia de Máquinas virtuales, Servicios en la nube tiene un agente dentro de cada rol web o rol de trabajo, por lo que puede iniciar nuevas instancias de aplicaciones y máquinas virtuales cuando se produce un error.

La naturaleza de PaaS de Servicios en la nube tiene también otras implicaciones. Una de las más importantes es que las aplicaciones creadas sobre la base de esta tecnología deben estar escritas para ejecutarse correctamente en caso de cualquier error en instancia de rol web o de trabajo. Para lograrlo, una aplicación de Servicios en la nube no debiera mantener estado en el sistema de archivos de sus propias máquinas virtuales. A diferencia de las máquinas virtuales creadas con Máquinas virtuales de Azure, las escrituras que se realizan en las máquinas virtuales de Servicios en la nube no son permanentes; no hay nada parecido a un disco de datos de Máquinas virtuales. En lugar de eso, una aplicación de Servicios en la nube debe escribir explícitamente todo estado en la base de datos de SQL, blogs, tablas u algún otro tipo de almacenamiento externo. Cuando se crean aplicaciones de esta manera, estas resultan más fáciles de escalar y son más resistentes ante los errores, ambos objetivos importantes de Servicios en la nube.

## <a name="next-steps"></a>Pasos siguientes
[Crear una aplicación de servicio en la nube en .NET](cloud-services-dotnet-get-started.md)  
[Crear una aplicación de servicio en la nube en Node.js](cloud-services-nodejs-develop-deploy-app.md)  
[Crear una aplicación de servicio en la nube en PHP](../cloud-services-php-create-web-role.md)  
[Creación de una aplicación de servicio en la nube en Python](cloud-services-python-ptvs.md)

