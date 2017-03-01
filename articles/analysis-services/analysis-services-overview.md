---
title: "Qué es Azure Analysis Services | Microsoft Docs"
description: "Obtenga una visión general de Analysis Services en Azure."
services: analysis-services
documentationcenter: 
author: minewiskan
manager: erikre
editor: 
tags: 
ms.assetid: 83d7a29c-57ae-4aa0-8327-72dd8f00247d
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 02/27/2017
ms.author: owend
translationtype: Human Translation
ms.sourcegitcommit: 653c9a04016c0157b2107710e170ba63b3a499f7
ms.openlocfilehash: 38f68d6df19d24492a234933e10d5d5cf34d1b23
ms.lasthandoff: 01/05/2017


---
# <a name="what-is-azure-analysis-services"></a>¿Qué es Azure Analysis Services?
![Azure Analysis Services](./media/analysis-services-overview/aas-overview-aas-icon.png)

Azure Analysis Services, que se basa en el motor analítico de eficacia probada de Microsoft SQL Server Analysis Services, ofrece un modelado de datos de categoría empresarial en la nube. 

Vea este vídeo para obtener más información sobre cómo Azure Analysis Services se complementa con las funciones globales de BI de Microsoft y cómo puede beneficiarse de tener sus modelos semánticos en la nube.

>[!VIDEO https://channel9.msdn.com/series/Azure-Analysis-Services/AzureAnalysisServicesGettingStarted/player]
>
>

> [!IMPORTANT]
> Azure Analysis Services está en **versión preliminar**. Hay algunas cosas que aún no funcionan. Asegúrese de consultar [Expectativas de la versión preliminar](#preview-expectations) más adelante en este artículo. Y no olvide mirar nuestro [blog de Azure Analysis Services](https://go.microsoft.com/fwlink/?linkid=830920) para obtener la información más reciente.
> 
> 

## <a name="built-on-sql-server-analysis-services"></a>Basado en SQL Server Analysis Services
Azure Analysis Services es compatible con la misma edición SQL Server 2016 Analysis Services Enterprise Edition que ya conoce. Azure Analysis Services admite modelos tabulares en el nivel de compatibilidad 1200. Se admiten todas las traducciones, particiones, seguridad de nivel de fila, relaciones bidireccionales y DirectQuery.

## <a name="use-the-tools-you-already-know"></a>Use las herramientas que ya conoce
![Herramientas para desarrolladores de BI](./media/analysis-services-overview/aas-overview-dev-tools.png)

Para crear modelos de datos para Azure Analysis Services, use las mismas herramientas que para SQL Server Analysis Services. Puede crear e implementar modelos tabulares con las versiones más recientes de [SQL Server Data Tools (SSDT)](https://msdn.microsoft.com/library/mt204009.aspx) o con [Azure Powershell](/powershell/azureps-cmdlets-docs) y las plantillas de [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) en [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx).

## <a name="connect-to-data-sources"></a>Conexión a orígenes de datos
Los modelos de datos implementados en servidores de Azure permiten conectarse a datos orígenes locales en su organización o en la nube. Combine datos de orígenes de datos tanto locales como en la nube para crear una solución de BI híbrida.

![Orígenes de datos](./media/analysis-services-overview/aas-overview-data-sources.png)

Como el servidor está en la nube, la conexión a los orígenes de datos en la nube se realiza sin problemas. Al conectarse a orígenes de datos locales, la [puerta de enlace de datos local](analysis-services-gateway.md) garantiza conexiones rápidas y seguras con el servidor de Analysis Services en la nube.  

 \* La versión preliminar aún no admite algunos orígenes de datos. Para más información, consulte [Expectativas de la versión preliminar](#preview-expectations) más adelante en este artículo.

## <a name="explore-your-data-from-anywhere"></a>Exploración de los datos desde cualquier lugar
Conéctese a sus servidores y [obtenga los datos](analysis-services-connect.md) desde cualquier lugar. Azure Analysis Services permite conectarse desde Power BI Desktop, Excel, aplicaciones personalizadas y herramientas basadas en explorador.

![Visualizaciones de datos](./media/analysis-services-overview/aas-overview-visualization.png)

 \* La versión preliminar aún no admite Power BI Embedded.

## <a name="secure"></a>Seguro
#### <a name="user-authentication"></a>Autenticación de usuarios
La autenticación de usuarios para Azure Analysis Services se realiza mediante [Azure Active Directory (AD)](../active-directory/active-directory-whatis.md). Al intentar iniciar sesión en una base de datos de Azure Analysis Services, los usuarios utilizan una identidad de cuenta de la organización con acceso a la base de datos a la que se están intentando acceder. Estas identidades de usuario deben ser miembros del directorio predeterminado de Azure Active Directory para la suscripción donde se encuentra el servidor de Azure Analysis Services. La [integración de directorios](https://technet.microsoft.com/library/jj573653.aspx) entre AAD y una instancia local de Active Directory es una excelente manera de que los usuarios locales tengan acceso a una base de datos de Azure Analysis Services, pero no es necesario para todos los escenarios.

Los usuarios inician sesión con el nombre principal de usuario (UPN) de su cuenta y su contraseña. Cuando se sincroniza con una instancia local de Active Directory, el UPN del usuario suele ser su dirección de correo electrónico profesional.

Para controlar los permisos para administrar el recurso de servidor de Azure Analysis Services, se asignan usuarios a roles dentro de su suscripción de Azure. De forma predeterminada, los administradores de suscripciones tienen permisos de propietario en el recurso de servidor en Azure. Se puede usar Azure Resource Manager para agregar más usuarios.

#### <a name="data-security"></a>Seguridad de los datos
Azure Analysis Services usa Azure Blob Storage para conservar el almacenamiento y los metadatos de las bases de datos de Analysis Services. Los archivos de datos en Blob se cifran mediante cifrado de lado servidor (SSE) de Azure Blob. Cuando se usa el modo DirectQuery, se almacenan solo metadatos; a los datos reales se accede desde el origen de datos en tiempo de consulta.

#### <a name="on-premises-data-sources"></a>Orígenes de datos locales
Para proteger el acceso a los datos que residen localmente en su organización, puede instalar y configurar una [puerta de enlace de datos local](analysis-services-gateway.md). Las puertas de enlace de proporcionan acceso a datos tanto para el modo DirectQuery como para el modo en memoria. Cuando un modelo Azure Analysis Services se conecta a un origen de datos local, se crea una consulta junto con las credenciales cifradas para el origen de datos local. El servicio en la nube de puerta de enlace analiza la consulta e inserta la solicitud en una instancia de Azure Service Bus. La puerta de enlace local sondea Azure Service Bus en busca de solicitudes pendientes. La puerta de enlace obtiene la consulta, descifra las credenciales y se conecta a los orígenes de datos para la ejecución. Los resultados se devuelven desde el origen de datos a la puerta de enlace, y luego se envían a la base de datos Azure Analysis Services.

Azure Analysis Services se rige por los [términos de Microsoft Online Services](http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31) y la [declaración de privacidad de Microsoft Online Services](https://www.microsoft.com/privacystatement/OnlineServices/Default.aspx).
Para más información sobre la seguridad de Azure, consulte [Microsoft Trust Center](https://www.microsoft.com/trustcenter/Security/AzureSecurity).

## <a name="get-help"></a>Obtener ayuda
Azure Analysis Services es fácil de configurar y administrar. Puede encontrar toda la información que necesita para crear y administrar un servidor aquí. Crear un modelo de datos para implementar en el servidor es similar a crear un modelo de datos que se implementa en un servidor local. Hay una amplia biblioteca de artículos sobre conceptos, procedimientos, tutoriales y referencia en [Analysis Services en MSDN](https://msdn.microsoft.com/library/bb522607.aspx).

También contamos con una serie de vídeos útiles en [Azure Analysis Services en Channel 9](https://channel9.msdn.com/series/Azure-Analysis-Services).

Las cosas cambian rápidamente. Siempre puede obtener la información más reciente en el [blog de Azure Analysis Services](https://go.microsoft.com/fwlink/?linkid=830920).

## <a name="community"></a>Comunidad
Analysis Services cuenta con una dinámica comunidad de usuarios. Únase a la conversación en el [foro de Azure Analysis Services](https://aka.ms/azureanalysisservicesforum).

## <a name="feedback"></a>Comentarios
¿Tiene sugerencias o solicitudes de características? No olvide dejar sus comentarios en la página de [comentarios de Azure Analysis Services](https://aka.ms/azureanalysisservicesfeedback).

¿Tiene sugerencias sobre la documentación? Puede agregar comentarios mediante Disqus en la parte inferior de cada artículo.

## <a name="preview-expectations"></a>Expectativas de la versión preliminar
Actualmente, Azure Analysis Services está en versión preliminar. Hay algunas cosas que debe tener en cuenta.

##### <a name="server-modes"></a>Modos de servidor
Azure Analysis Services admite el modo tabular para modelos tabulares en el nivel de compatibilidad 1200. No se admiten el modo multidimensional y minería de datos, ni Power Pivot for SharePoint.

##### <a name="data-sources"></a>Orígenes de datos
En la versión preliminar, se admiten los siguientes orígenes de datos en los modelos tabulares 1200 implementados en un servidor de Azure Analysis Services.

| **Nube** | **Local** |
| --- | --- |
| Base de datos SQL |SQL Server |
| Almacenamiento de datos SQL |APS |
| Oracle | Oracle |
| Teradata | |

### <a name="data-source-providers"></a>Proveedores de orígenes de datos
Para conectarse a los orígenes de datos, los modelos de datos de Azure Analysis Services podrían requerir distintos proveedores de datos que los modelos de datos de SQL Server Analysis Services. Los requisitos de proveedores de datos dependen de si el origen de datos está en la nube o es local, y del tipo de modelo de datos (en memoria o DirectQuery). Para más información, consulte [Datasource connections](analysis-services-datasource.md) (Conexiones de orígenes de datos).

### <a name="client-connections"></a>Conexiones de cliente
La versión preliminar aún no admite Power BI Embedded.

No se admiten libros de Excel con conexiones dinámicas con un servidor de Azure Analysis Services y guardado en OneDrive o SharePoint Online.

## <a name="next-steps"></a>Pasos siguientes
Ahora que sabe más acerca de Azure Analysis Services, es momento de empezar a trabajar. Obtenga información acerca de cómo [crear un servidor](analysis-services-create-server.md) en Azure y cómo [implementar un modelo tabular](analysis-services-deploy.md) en él.


