---
title: "Requisitos previos técnicos para la creación de un servicio de datos para Marketplace | Microsoft Docs"
description: "Información de los requisitos para crear un Servicio de datos para implementar y vender en Azure Marketplace"
services: marketplace-publishing
documentationcenter: 
author: HannibalSII
manager: hascipio
editor: 
ms.assetid: aaff609a-1cd1-4146-98f4-d04166b0fce0
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/26/2016
ms.author: hascipio; avikova
ms.openlocfilehash: 52827723477677bc292c645e2390c435fbad3ee4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="technical-pre-requisites-for-creating-a-data-service-offer-for-the-azure-marketplace"></a>Requisitos previos técnicos para la creación de una oferta de servicio de datos para Azure Marketplace
> [!IMPORTANT]
> **En este momento, ya no se pueden incorporar nuevos editores del Servicio de datos. No se aprobarán nuevos servicios de datos para mostrarse en lista.** Si tiene una aplicación de negocio de SaaS que desea publicar en AppSource, puede encontrar más información [aquí](https://appsource.microsoft.com/partners). Si tiene aplicaciones IaaS o un servicio de desarrollo que quiera publicar en Azure Marketplace, puede encontrar más información [aquí](https://azure.microsoft.com/marketplace/programs/certified/).
> 
> 

Lea el proceso minuciosamente antes de empezar y comprenda dónde y por qué se realiza cada paso. Tanto como sea posible, debe preparar la información de su compañía y otros datos, descargar las herramientas necesarias o crear componentes técnicos antes de comenzar el proceso de creación de la oferta.

Debe tener listos los elementos siguientes antes de comenzar el proceso:

## <a name="make-a-decision-on-what-technology-will-be-used-to-publish-your-data-service-offer"></a>Tomar una decisión sobre qué tecnología se usará para publicar su oferta de servicio de datos
Un editor puede decidir entre varias tecnologías al publicar un servicio de datos en Azure Marketplace. Las principales tecnologías admitidas se describen a continuación. Independientemente de qué tecnología se use para publicar el servicio de datos, el usuario final consume los datos a través de la **fuente de OData** expuesta por el servicio de Azure Marketplace. Para encontrar toda la información acerca del servicio de OData, consulte la página [http://www.odata.org/](http://www.odata.org/)

## <a name="sql-azure-database"></a>Base de datos de SQL Azure
Tener el conjunto de datos listo en SQL Azure es responsabilidad del publicador. Necesitará suscribirse a Azure, aprovisionar el tamaño adecuado de la base de datos y cargar los datos en la base de datos de SQL Azure. El publicador también es responsable de mantener sus datos siempre actualizados. Para obtener más información sobre la suscripción a Servicios de Azure, consulte la página [https://azure.microsoft.com/services/sql-database/](https://azure.microsoft.com/services/sql-database/)

Al mover los datos a SQL Azure, Azure Marketplace puede exponer las tablas y vistas. El publicador puede especificar qué tablas/vistas y columnas se exponen al usuario final. Además, el proveedor de contenido también puede especificar qué columnas puede consultar el usuario final y las que solo se devuelven en la carga. Esto proporciona un alto grado de flexibilidad sobre qué datos de la base de datos se deben exponer. Las columnas que se pueden consultar deben estar respaldadas por uno o más índices de la base de datos.

## <a name="rest-based-web-service"></a>Servicio web basado en REST
Protocolo admitido: **HTTPS solo**

Los servicios basados en REST existentes pueden exponerse a través de Azure Marketplace. Dado que el conjunto de datos siempre se expone al usuario final como una fuente de OData, el servicio de Azure Marketplace necesita poder asignar el servicio a un servicio basado en OData. Para ello, los puntos de conexión basados en REST deben exponer todos los parámetros como parámetros HTTP.

La carga debe estar en un formulario que se puede asignar a una respuesta ATOM. Por lo tanto, la respuesta de los servicios debe estar en formato XML y solo puede contener un elemento de repetición que contiene los valores de carga (como el conjunto de registros). El servicio de Azure Marketplace asignará el nodo repetido al nodo de entrada en ATOM y los valores de carga en los nodos de propiedad dentro del nodo de entrada.

Información de autorización (por ejemplo, clave de API, token de autenticación, etc.) debe proporcionarse como un parámetro HTTP o en el encabezado HTTP (pares de clave-valor); también se admite la autenticación básica. Debe proporcionarse una clave válida y se están realizando todas las solicitudes a través de Azure Marketplace mediante esa clave. La supervisión y facturación de usuarios se realiza en la capa de Azure Marketplace.

Los errores devueltos por el servicio deben asignarse a códigos de estado HTTP. En caso de que el servicio devuelva un archivo XML que contenga el error, estos van a ser asignados por el servicio de Azure Marketplace a códigos de estado HTTP.

## <a name="soap-based-web-services"></a>Servicios web basados en SOAP
Protocolo: **HTTPS solo**

Los requisitos son los mismos que en la sección de servicio basada en REST. La única diferencia es que el parámetro también se puede proporcionar en un cuerpo XML que se está publicando en el servicio del publicador con cada solicitud realizada a través de Azure Marketplace. Esto significa que los parámetros HTTP proporcionados por el usuario en el front-end se están convirtiendo en elementos XML de un documento XML que se está publicando con la solicitud al servicio de web del proveedor de contenido.

## <a name="odata-based-web-services"></a>Servicios web basados en OData
Protocolo: **HTTPS solo**

Los datos pueden exponerse como un servicio de OData a Azure Marketplace. El sistema atravesará el servicio y reemplaza la raíz del servicio con la raíz del servicio de Azure Marketplace: para asegurarse de que todas las llamadas posteriores atraviesan Azure Marketplace.

Los servicios de OData no solo necesitan oponerse a una base de datos en el back-end. OData admite cualquier tipo de almacenamiento o lógica empresarial para controlar el servicio.

## <a name="next-steps"></a>Pasos siguientes
Ahora que ha revisado los requisitos previos y completado las tareas necesarias, puede continuar con la creación de su oferta de Servicio de datos como se detalla en la [Guía de publicación de Servicios de datos](marketplace-publishing-data-service-creation.md).

O bien, si desea revisar el proceso general y los artículos correspondientes para cada una de las fases de la publicación, visite el artículo [Introducción: Publicación de una oferta en Azure Marketplace](marketplace-publishing-getting-started.md).

[link-acct]:marketplace-publishing-accounts-creation-registration.md
