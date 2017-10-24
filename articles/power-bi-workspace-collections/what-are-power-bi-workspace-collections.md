---
title: "¿Qué son las colecciones de áreas de trabajo de Power BI?"
description: "Power BI Embedded le permite integrar informes de Power BI en las aplicaciones web o móviles, por lo que no necesita crear soluciones personalizadas para compilar soluciones personalizadas."
services: power-bi-embedded
documentationcenter: 
author: guyinacube
manager: erikre
editor: 
tags: 
ROBOTS: NOINDEX
ms.assetid: 03649b72-b7d7-40ca-b077-12356d72d4f3
ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 09/20/2017
ms.author: asaxton
ms.openlocfilehash: 7df172895bb926f1715370b941964e2c29ab393d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="what-are-power-bi-workspace-collections"></a>¿Qué son las colecciones de áreas de trabajo de Power BI?

Con las **colecciones de áreas de trabajo de Power BI**, puede integrar informes de Power BI directamente en las aplicaciones web o móviles.

![Diagrama de aplicaciones](media/what-are-power-bi-workspace-collections/what-is.png)

> [!IMPORTANT]
> Las colecciones de áreas de trabajo de Power BI están en desuso y estarán disponibles hasta junio de 2018 o hasta cuando lo indique su contrato. Se recomienda planear la migración a Power BI Embedded para evitar la interrupción de la aplicación. Para más información sobre cómo migrar los datos a Power BI Embedded, consulte [Migración de contenido de Colección de áreas de trabajo de Power BI a Power BI Embedded](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/).

Las colecciones de áreas de trabajo de Power BI son un **servicio de Azure** que permite a los ISV y a los desarrolladores de software exponer las experiencias de datos de Power BI en sus aplicaciones. Como desarrollador, ha creado aplicaciones y esas aplicaciones tienen sus propios usuarios y un diferente conjunto de características. También podría ocurrir que estas aplicaciones tengan algunos elementos de datos integrados como gráficos e informes que ahora pueden disponer de la tecnología de las colecciones de áreas de trabajo de Microsoft Power BI. No necesita una cuenta de Power BI para utilizar la aplicación. Pueden seguir iniciando sesión en su aplicación como antes, y ver e interactuar con la experiencia de informes de Power BI sin necesitar ninguna licencia adicional.

## <a name="licensing-for-microsoft-power-bi-workspace-collections"></a>Introducción a las colecciones de áreas de trabajo de Microsoft Power BI

En el modelo de uso de las **colecciones de áreas de trabajo de Microsoft Power BI**, la administración de licencias de Power BI no es responsabilidad del usuario final.  En su lugar, el desarrollador de la aplicación que está consumiendo los elementos visuales, adquiere **sesiones** que se cobran a la suscripción que posee esos recursos. 

## <a name="microsoft-power-bi-workspace-collections-conceptual-model"></a>Modelo conceptual de las colecciones de áreas de trabajo de Microsoft Power BI

![Flujo de la aplicación con colecciones de área de trabajo](media/what-are-power-bi-workspace-collections/model.png)

Al igual que cualquier otro servicio en Azure, los recursos de las colecciones de áreas de trabajo de Power BI se aprovisionan mediante las [API de Azure Resource Manager](https://msdn.microsoft.com/library/mt712306.aspx). En este caso, el recurso que aprovisiona es una **Colección de áreas de trabajo de Power BI**.

## <a name="workspace-collection"></a>Colección de áreas de trabajo

Una **colección de áreas de trabajo** es el contenedor de Azure de nivel superior para aquellos recursos que contienen 0 o más **áreas de trabajo**.  Una **colección** de **áreas de trabajo** tiene todas las propiedades estándar de Azure, así como las siguientes:

* **Claves de acceso** : claves que se usan al llamar de forma segura a las API de Power BI (se describen en una sección posterior).
* **Usuarios**: los usuarios de Azure Active Directory (AAD) que tienen derechos de administrador para administrar la Colección de áreas de trabajo de Power BI a través de Azure Portal o de la API de Azure Resource Manager.
* **Región**: como parte del aprovisionamiento de una **colección de áreas de trabajo**, puede seleccionar una región para que se aprovisione. Para más información, consulte [Regiones de Azure](https://azure.microsoft.com/regions/).

## <a name="workspace"></a>Área de trabajo

Un **área de trabajo** es un contenedor de contenido de Power BI, que puede incluir conjuntos de datos e informes. Un **área de trabajo** está vacío cuando se crea por primera vez. El contenido se creará con Power BI Desktop y el PBIX se implementará mediante programa en el área de trabajo con la [API Power BI Import](https://msdn.microsoft.com/library/mt711504.aspx). También puede crear mediante programación un conjunto de datos y, después, crear informes en la aplicación, en lugar de usar Power BI Desktop.

## <a name="using-workspace-collections-and-workspaces"></a>Uso de colecciones de áreas de trabajo y áreas de trabajo

Las **colecciones de áreas de trabajo** y las **áreas de trabajo** son contenedores de contenido que se usan y se organizan de la forma que mejor se adapte al diseño de la aplicación que está creando. Podrá disponer el contenido dentro de ellas de muchas maneras diferentes. Puede decidir colocar todo el contenido en un área de trabajo y después usar los tokens de aplicación para subdividir más el contenido entre sus clientes. También puede decidir colocar todos sus clientes en áreas de trabajo independientes de forma que exista algo de separación entre ellos. O bien, puede elegir organizar los usuarios por región en lugar de por cliente. Este diseño flexible le permite elegir la mejor manera de organizar el contenido.

## <a name="cached-datasets"></a>Conjuntos de datos en caché

Se pueden usar los conjuntos de datos en caché.  Sin embargo, no puede actualizar los datos en caché una vez que se han cargado en las **colecciones de áreas de trabajo de Microsoft Power BI**. Un conjunto de datos en caché significa que ha importado los datos en Power BI Desktop, en lugar de usar DirectQuery.

## <a name="authentication-and-authorization-with-app-tokens"></a>Autenticación y autorización con tokens de aplicación

Las **colecciones de áreas de trabajo de Microsoft Power BI** aplaza a su aplicación para realizar la autenticación y la autorización de usuario necesaria. No hay ningún requisito explícito por el que los usuarios finales tengan que ser clientes de Azure Active Directory (Azure AD).  En su lugar, la aplicación expresará la autorización para representar un informe de Power BI a las **colecciones de áreas de trabajo de Microsoft Power BI** mediante los **Tokens de autenticación de la aplicación (tokens de aplicación)**.  Estos **tokens de aplicación** se crean según sea necesario cuando la aplicación quiere presentar un informe.

![Diagrama de uso del token de aplicación](media/what-are-power-bi-workspace-collections/app-tokens.png)

Los **Tokens de autenticación de la aplicación (tokens de aplicación)** se usan para autenticarse en las **colecciones de áreas de trabajo de Microsoft Power BI**.  Existen tres tipos de **tokens de aplicación**:

1. Aprovisionar tokens: se usa al aprovisionar una nueva **área de trabajo** en una **colección de áreas de trabajo**
2. Desarrollar tokens: se usa al realizar llamadas directamente a las **API de REST de Power BI**
3. Tokens de incrustación: se usan al realizar llamadas para presentar un informe en el iframe incrustado

Estos tokens se usan para las diversas fases de las interacciones con las **colecciones de áreas de trabajo de Microsoft Power BI**.  Los tokens están diseñados para que pueda delegar permisos desde su aplicación a Power BI. Para obtener más información, consulte el artículo sobre el [flujo del token de aplicación](app-token-flow.md).

## <a name="create-or-edit-reports-within-your-application"></a>Creación o modificación de informes en la aplicación

Ahora se pueden editar los informes existente o crear nuevos informes directamente en su aplicación sin tener que usar Power BI Desktop. Esto requiere que exista un conjunto de datos en el área de trabajo.

## <a name="see-also"></a>Otras referencias

[Escenarios comunes de las colecciones de áreas de trabajo de Power BI](scenarios.md)  
[Introducción a las colecciones de áreas de trabajo de Power BI](get-started.md)  
[Get started with Microsoft Power BI Embedded sample (Introducción a Microsoft Power BI Embedded: ejemplo)](get-started-sample.md)  
[Embed a report in Power BI Embedded](embed-report.md) (Inserción de un informe en Power BI Embedded)  
[Authenticating and authorizing in Power BI Workspace Collections](app-token-flow.md) (Autenticación y autorización en las colecciones de áreas de trabajo de Power BI)  
[JavaScript Embed Sample](https://microsoft.github.io/PowerBI-JavaScript/demo/) (Ejemplo de inserción de JavaScript)  
[Repositorio GIT PowerBI-CSharp](https://github.com/Microsoft/PowerBI-CSharp)  
[Repositorio GIT PowerBI-Node](https://github.com/Microsoft/PowerBI-Node)  

¿Tiene más preguntas? [Pruebe la comunidad de Power BI](http://community.powerbi.com/)
