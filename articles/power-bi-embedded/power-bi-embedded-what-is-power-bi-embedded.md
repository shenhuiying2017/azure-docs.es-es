---
title: "¿Qué es Microsoft Power BI Embedded?"
description: "Power BI Embedded le permite integrar informes de Power BI en las aplicaciones web o móviles, por lo que no necesita crear soluciones personalizadas para compilar soluciones personalizadas."
services: power-bi-embedded
documentationcenter: 
author: guyinacube
manager: erikre
editor: 
tags: 
ms.assetid: 03649b72-b7d7-40ca-b077-12356d72d4f3
ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 01/06/2017
ms.author: asaxton
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 81215430b5731a53d4f4700499c1d9af963e712c
ms.lasthandoff: 12/08/2016


---
# <a name="what-is-microsoft-power-bi-embedded"></a>¿Qué es Microsoft Power BI Embedded?
Con **Power BI Embedded**, puede integrar informes de Power BI directamente en las aplicaciones web o móviles.

![](media/powerbi-embedded-whats-is/what-is.png)

Power BI Embedded es un **servicio de Azure** que permitirá a los ISV y a los desarrolladores de software exponer las experiencias de datos de Power BI en sus aplicaciones. Como desarrollador, ha creado aplicaciones y esas aplicaciones tienen sus propios usuarios y un diferente conjunto de características. También podría ocurrir que estas aplicaciones tengan algunos elementos de datos integrados como gráficos e informes que ahora pueden disponer de la tecnología de Microsoft Power BI Embedded. No necesita una cuenta de Power BI para utilizar la aplicación. Pueden seguir iniciando sesión en su aplicación como antes, y ver e interactuar con la experiencia de informes de Power BI sin necesitar ninguna licencia adicional.

## <a name="licensing-for-microsoft-power-bi-embedded"></a>Licencias de Microsoft Power BI Embedded
En el modelo de uso de **Microsoft Power BI Embedded** , la administración de licencias de Power BI no es responsabilidad del usuario final.  En su lugar, el desarrollador de la aplicación que está consumiendo los elementos visuales, adquiere **sesiones** que se cobran a la suscripción que posee esos recursos. Puede encontrar información adicional en la página de precios (https://azure.microsoft.com/es-es/pricing/details/power-bi-embedded/).

## <a name="microsoft-power-bi-embedded-conceptual-model"></a>Modelo conceptual de Microsoft Power BI Embedded
![](media/powerbi-embedded-whats-is/model.png)

Al igual que cualquier otro servicio en Azure, los recursos de Power BI Embedded se aprovisionan mediante las [API de Azure Resource Manager](https://msdn.microsoft.com/library/mt712306.aspx). En este caso, el recurso que aprovisiona es una **Colección de áreas de trabajo de Power BI**.

## <a name="workspace-collection"></a>Colección de área de trabajo
Una **colección de áreas de trabajo** es el contenedor de Azure de nivel superior para aquellos recursos que contienen 0 o más **áreas de trabajo**.  Una **colección** de **áreas de trabajo** tiene todas las propiedades estándar de Azure, así como las siguientes:

* **Claves de acceso** : claves que se usan al llamar de forma segura a las API de Power BI (se describen en una sección posterior).
* **Usuarios**: los usuarios de Azure Active Directory (AAD) que tienen derechos de administrador para administrar la Colección de áreas de trabajo de Power BI a través de Azure Portal o de la API de Azure Resource Manager.
* **Región**: como parte del aprovisionamiento de una **colección de áreas de trabajo**, puede seleccionar una región para que se aprovisione. Para más información, consulte [Regiones de Azure](https://azure.microsoft.com/regions/).

## <a name="workspace"></a>Área de trabajo
Un **área de trabajo** es un contenedor de contenido de Power BI, que puede incluir conjuntos de datos, informes y paneles. Un **área de trabajo** está vacío cuando se crea por primera vez. Durante la versión preliminar, creará todo el contenido con Power BI Desktop e implementará automáticamente PBIX en el área de trabajo mediante las [API de REST de Power BI](https://msdn.microsoft.com/library/mt711504.aspx).

## <a name="using-workspace-collections-and-workspaces"></a>Usar las áreas de trabajo y las colecciones de área de trabajo
Las **colecciones de áreas de trabajo** y las **áreas de trabajo** son contenedores de contenido que se usan y se organizan de la forma que mejor se adapte al diseño de la aplicación que está creando. Podrá disponer el contenido dentro de ellas de muchas maneras diferentes. Puede decidir colocar todo el contenido en un área de trabajo y después usar los tokens de aplicación para subdividir más el contenido entre sus clientes. También puede decidir colocar todos sus clientes en áreas de trabajo independientes de forma que exista algo de separación entre ellos. O bien, puede elegir organizar los usuarios por región en lugar de por cliente. Este diseño flexible le permite elegir la mejor manera de organizar el contenido.

## <a name="cached-datasets"></a>Conjuntos de datos en caché
Los conjuntos de datos en caché pueden usarse en la versión preliminar.  Sin embargo, no puede actualizar los datos en caché una vez que se han cargado en **Microsoft Power BI Embedded**.

## <a name="authentication-and-authorization-with-app-tokens"></a>Autenticación y autorización con tokens de aplicación
**Microsoft Power BI Embedded** aplaza a su aplicación para realizar la autenticación y la autorización de usuario necesaria. No hay ningún requisito explícito por el que los usuarios finales tengan que ser clientes de Azure Active Directory (Azure AD).  En su lugar, la aplicación expresará la autorización para representar un informe de Power BI a **Microsoft Power BI Embedded** mediante los **Tokens de autenticación de la aplicación (tokens de aplicación)**.  Estos **tokens de aplicación** se crean según sea necesario cuando la aplicación quiere presentar un informe.

![](media/powerbi-embedded-whats-is/app-tokens.png)

Los **Tokens de autenticación de la aplicación (tokens de aplicación)** se usan para autenticarse en **Microsoft Power BI Embedded**.  Existen tres tipos de **tokens de aplicación**:

1. Aprovisionar tokens: se usa al aprovisionar una nueva **área de trabajo** en una **colección de áreas de trabajo**
2. Desarrollar tokens: se usa al realizar llamadas directamente a las **API de REST de Power BI**
3. Tokens de incrustación: se usan al realizar llamadas para presentar un informe en el iframe incrustado

Estos tokens se usan para las diversas fases de las interacciones con **Microsoft Power BI Embedded**.  Los tokens están diseñados para que pueda delegar permisos desde su aplicación a Power BI. Para obtener más información, consulte el artículo sobre el [flujo del token de aplicación](power-bi-embedded-app-token-flow.md).

## <a name="see-also"></a>Otras referencias
* [Common Microsoft Power BI Embedded scenarios (Escenarios comunes de Microsoft Power BI Embedded)](power-bi-embedded-scenarios.md)
* [Introducción a Microsoft Power BI Embedded](power-bi-embedded-get-started.md)

