---
title: "Migración de contenido de Colección de áreas de trabajo de Power BI a Power BI Embedded | Microsoft Docs"
description: "Aprenda a migrar de las colecciones de áreas de trabajo de Power BI a Power BI Embedded y aproveche los avances para insertar aplicaciones."
services: power-bi-embedded
documentationcenter: 
author: guyinacube
manager: erikre
editor: 
tags: 
ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 09/28/2017
ms.author: asaxton
ms.openlocfilehash: 069f31c8213bd0d8586f7ca50e543acfdad8a2b3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-migrate-power-bi-workspace-collection-content-to-power-bi-embedded"></a>Migración de contenido de Colección de áreas de trabajo de Power BI a Power BI Embedded

Aprenda cómo migrar de las colecciones de áreas de trabajo de Power BI a Power BI Embedded. En este artículo se ofrece una guía para migrar de las colecciones de áreas de trabajo de Azure Power BI a Power BI Embedded. También veremos qué se puede esperar durante los cambios de la aplicación.

El recurso de las colecciones de área de trabajo de Power BI sigue estando disponible durante un tiempo limitado después de la disponibilidad general de la versión de Power BI Premium. Los clientes con un Contrato Enterprise acceden a sus colecciones de áreas de trabajo existentes hasta la expiración de sus contratos. Los clientes que adquieren las colecciones de áreas de trabajo de Power BI a través de canales Direct o CSP disfrutan de acceso durante un año a partir de la disponibilidad general de Power BI Premium.

> [!IMPORTANT]
> Aunque la migración depende del servicio Power BI, no hay ninguna dependencia en Power BI para los usuarios de la aplicación cuando se usa un **token insertado**. No tienen que registrarse en Power BI ver el contenido insertado en la aplicación. Puede usar este enfoque para insertar Power BI a los clientes.

![Flujo de Power BI Embedded](media/migrate-from-power-bi-workspace-collections/powerbi-embed-flow.png)

## <a name="prepare-for-the-migration"></a>Preparación para la migración

Hay algunas cosas que deben hacerse para preparar la migración del servicio Colección de áreas de trabajo de Power BI a Power BI Embedded. Necesita un inquilino disponible, junto con un usuario que tenga una licencia de Power BI Pro.

1. Asegúrese de que accede a un inquilino de Azure Active Directory (Azure AD).

    ¿Qué inquilino hay que usar?

    * ¿Hay que usar el inquilino de Power BI corporativo existente?
    * ¿Hay que usar un inquilino independiente para la aplicación?
    * ¿Hay que usar un inquilino independiente para cada cliente?

    Si decide crear a un nuevo inquilino para la aplicación, o para cada cliente, consulte una de las opciones siguientes:

    * [Creación de un inquilino de Azure Active Directory](https://powerbi.microsoft.com/documentation/powerbi-developer-create-an-azure-active-directory-tenant/)
    * [Obtención de un inquilino de Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-howto-tenant)

2. Cree un usuario en este nuevo inquilino que actúe como la cuenta "maestra" de la aplicación. Dicha cuenta tiene que registrarse en Power BI y hay que tener una licencia de Power BI Pro asignada a ella.

## <a name="accounts-within-azure-ad"></a>Cuentas en Azure AD

Las cuentas siguientes deben existir dentro del inquilino.

> [!NOTE]
> Estas cuentas necesitan tener licencias de Power BI Pro para que puedan usar las áreas de trabajo de la aplicación.

1. Un usuario administrador de inquilinos.

    Se recomienda que el área de trabajo de la aplicación insertada tenga el administrador de inquilinos enumerado como miembro.

2. Cuentas para analistas que crean contenido.

    Estos usuarios deben asignarse a áreas de trabajo de la aplicación según sea necesario.

3. Una cuenta de usuario *maestra* de aplicación o cuenta de servicio.

    El back-end de las aplicaciones almacena las credenciales para esta cuenta. Utilice la cuenta *maestra* para adquirir un token de Azure AD para su uso con las API de REST de Power BI. Esta cuenta se usa para generar el token de inserción de la aplicación. La cuenta *maestra* debe ser un administrador de las áreas de trabajo de la aplicación que se crearon para la inserción.

    **Esta cuenta es una cuenta de usuario normal de su organización que se usa con fines de inserción.**

## <a name="app-registration-and-permissions"></a>Permisos y registro de aplicaciones

Para realizar llamadas a la API de REST, registre una aplicación con Azure AD. Se aplica la configuración adicional en Microsoft Azure Portal además de la página de registro de aplicación de Power BI. Para más información, consulte [Registro de una aplicación de Azure AD para insertar contenido de Power BI](https://powerbi.microsoft.com/documentation/powerbi-developer-register-app/).

La recomendación es registrar la aplicación con la cuenta **maestra** de la aplicación.

## <a name="create-app-workspaces-required"></a>Creación de áreas de trabajo de la aplicación (obligatorio)

Si la aplicación está dando servicio a varios clientes, puede aprovechar las ventajas de las áreas de trabajo de la aplicación para proporcionar un mejor aislamiento. Los paneles e informes deben estar aislados entre sus clientes. A continuación, podría utilizar una cuenta de Power BI por área de trabajo de la aplicación para aislar aún más las experiencias de aplicación entre los clientes, pero solo se puede usar una cuenta por simplicidad.

> [!IMPORTANT]
> No se puede usar un área de trabajo personal ("Mi área de trabajo") para aprovechar las ventajas de inserción a sus clientes.

Necesita un usuario que tenga una licencia Pro con el fin de crear un área de trabajo de la aplicación en Power BI. De forma predeterminada, el usuario de Power BI que crea el área de trabajo de la aplicación es un administrador de esa área de trabajo. **La cuenta *maestra* de la aplicación debe ser un administrador del área de trabajo.**

## <a name="content-migration"></a>Migración de contenido

El contenido de las colecciones de áreas de trabajo Power BI Embedded se puede migrar en paralelo con la solución que tenga implementada, sin que se produzcan tiempos de inactividad.

Hay disponible una **herramienta de migración** que le ayudará a copiar contenido de las colecciones de áreas de trabajo de Power BI en Power BI Embedded. Especialmente si tiene muchos informes. Para más información, consulte [Herramienta de migración de Power BI Embedded](migrate-tool.md).

La migración de contenido se basa principalmente en dos API.

1. Download PBIX: esta API puede descargar archivos PBIX cargados en Power BI a desde octubre de 2016.
2. Import PBIX: esta API carga cualquier PBIX en Power BI.

Para ver algunos fragmentos de código relacionados, consulte [Fragmentos de código para migrar contenido de Power BI Embedded](migrate-code-snippets.md).

### <a name="report-types"></a>Tipos de informes

Hay varios tipos de informes, cada uno con un flujo de migración diferente.

#### <a name="cached-dataset-and-report"></a>Conjuntos de datos e informes en caché

Los conjuntos de datos almacenados en caché hacen referencia a archivos PBIX que habían importado datos en lugar de a una conexión dinámica o a la conexión de DirectQuery.

**Flujo**

1. Llame a la API Download PBIX para descargar archivos PBIX del área de trabajo de Colección de áreas de trabajo de Power BI.
2. Guarde PBIX.
3. Llame a Import PBIX para importar los archivos PBIX al área de trabajo de Power BI Embedded.

#### <a name="directquery-dataset-and-report"></a>Conjuntos de datos e informes de DirectQuery

**Flujo**

1. Llame a GET https://api.powerbi.com/v1.0/collections/{collection_id}/workspaces/{wid}/datasets/{dataset_id}/Default.GetBoundGatewayDataSources y guarde la cadena de conexión recibida.
2. Llame a la API Download PBIX para descargar archivos PBIX del área de trabajo de Colección de áreas de trabajo de Power BI.
3. Guarde PBIX.
4. Llame a Import PBIX para importar los archivos PBIX al área de trabajo de Power BI Embedded.
5. Actualice la cadena de conexión mediante una llamada a - POST https://api.powerbi.com/v1.0/myorg/datasets/{dataset_id}/Default.SetAllConnections.
6. Obtenga el identificador de GW y el identificador del origen de datos mediante una llamada a GET https://api.powerbi.com/v1.0/myorg/datasets/{dataset_id}/Default.GetBoundGatewayDataSources
7. Actualice las credenciales del usuario mediante una llamada - https://api.powerbi.com/v1.0/myorg/gateways/{gateway_id}/datasources/{datasource_id}

#### <a name="old-dataset-and-reports"></a>Informes y conjunto de datos antiguos

Los informes cargados antes de octubre de 2016 no son compatibles con la característica Download PBIX.

**Flujo**

1. Obtenga PBIX desde el entorno de desarrollo (su control de código fuente interno).
2. Llame a Import PBIX para importar los archivos PBIX al área de trabajo de Power BI Embedded.

#### <a name="push-dataset-and-report"></a>Inserción de conjuntos de datos e informes

La API Download PBIX no es compatible con los conjuntos de datos de *Push API*. Los datos del conjunto de datos de Push API no se pueden llevar de las colecciones de áreas de trabajo de Power BI a Power BI Embedded.

**Flujo**

1. Llame a la API "Crear conjunto de datos" con el conjunto de datos Json para crear el conjunto de datos para el área de trabajo de Power BI Embedded.
2. Vuelva a crear el informe para el conjunto de datos creado*.

Es posible usar algunas soluciones alternativas para migrar el informe de Push API desde las colecciones de áreas de trabajo de Power BI a Power BI Embedded realizando lo siguiente:

1. Llame a la API Download PBIX para importar los archivos PBIX del área de trabajo de Colección de áreas de trabajo de Power BI.
2. Clone el informe de Push API y enlácelo al archivo PBIX ficticio desde el paso 1.
3. Descargue el informe de Push API con el archivo PBIX ficticio.
4. Cargue el archivo PBIX ficticio para el área de trabajo de Power BI Embedded.
5. Cree un conjunto de datos de inserción en el área de trabajo de Power BI Embedded.
6. Vuelva a enlazar el informe con el conjunto de datos de Push API.

## <a name="create-and-upload-new-reports"></a>Creación y carga de nuevos informes

Además del contenido que se ha migrado desde las colecciones de áreas de trabajo de Power BI, puede crear sus propios informes y conjuntos de datos mediante Power BI Desktop y, después, publicar estos informes en un área de trabajo de la aplicación. El usuario final que publica los informes debe tener una licencia de Power BI Pro para publicarlos en un área de trabajo de la aplicación.

## <a name="rebuild-your-application"></a>Vuelva a compilar la aplicación.

1. Modifique la aplicación para usar las API de REST de Power BI y la ubicación del informe en powerbi.com.

2. Vuelva a crear la autenticación AuthN/AuthZ mediante la cuenta *maestra* para la aplicación. Puede sacar partido de usar un [token insertado](https://msdn.microsoft.com/library/mt784614.aspx) para permitir que este usuario actúe en nombre de otros usuarios.

3. Inserte los informes de Power BI Embedded en la aplicación. Para más información, consulte [Inserción de paneles, informes e iconos de Power BI](https://powerbi.microsoft.com/documentation/powerbi-developer-embedding-content/).

## <a name="map-your-users-to-a-power-bi-user"></a>Asignación de usuarios a un usuario de Power BI

Dentro de la aplicación, se pueden asignar usuarios para administrarlos dentro de la aplicación a una credencial de Power BI *maestra* para la aplicación. Las credenciales de esta cuenta *maestra* de Power BI se almacenan dentro de la aplicación y se pueden usar para crear tokens insertados.

## <a name="what-to-do-when-you-are-ready-for-production"></a>Qué hacer cuando se está listo para producción

Cuando ya esté listo para pasarse a producción, debe hacer lo siguiente:

- Si usa un inquilino independiente para el desarrollo, debe asegurarse de que las áreas de trabajo de la aplicación, junto con los paneles e informes, están disponibles en el entorno de producción. Asegúrese de que creó la aplicación en Azure AD para el inquilino de producción y asigne los permisos de aplicación adecuados, tal como se indica en el paso 1.

- Adquiera una capacidad que se adapte a sus necesidades. Puede usar el [documento sobre el planeamiento de la capacidad de análisis insertada](https://aka.ms/pbiewhitepaper) para ayudar a entender lo puede necesitar. Cuando esté listo para realizar la compra, adquiera un recurso de Power BI Embedded en Azure Portal.

- Edite el área de trabajo de la aplicación y asígnela a una capacidad en Avanzadas.

    ![Asigne el área de trabajo de la aplicación con una capacidad en powerbi.com](media/migrate-from-power-bi-workspace-collections/embedded-capacity.png)

- Implemente la aplicación actualizada en producción y empiece a insertar informes desde Power BI Embedded.

## <a name="after-migration"></a>Después de la migración

Se necesitan algunas limpiezas en las colecciones de áreas de trabajo de Power BI.

- Quite todas las áreas de trabajo de la solución implementada en el servicio de Azure Colección de áreas de trabajo de Power BI.
- Elimine las colecciones de áreas de trabajo que hay en Azure.

## <a name="next-steps"></a>Pasos siguientes

¡Enhorabuena! La aplicación ahora se migra a Power BI Embedded. Para más información acerca de cómo insertar los paneles, informes y conjuntos de datos de Power B, consulte [Inserción de paneles, informes e iconos de Power BI](https://powerbi.microsoft.com/documentation/powerbi-developer-embedding-content/).

¿Tiene más preguntas? [Pruebe a plantearlas en la Comunidad de Power BI](http://community.powerbi.com/)