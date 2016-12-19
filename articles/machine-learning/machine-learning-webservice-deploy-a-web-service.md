---
title: "Implementación de servicios web nuevos"
description: "Flujo de trabajo de la implementación de un servicio web basado en ARM"
services: machine-learning
documentationcenter: 
author: vDonGlover
manager: raymondl
editor: 
ms.assetid: a358b04f-0d08-4d50-820e-eeac971854cf
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/04/2016
ms.author: v-donglo
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 7941bdbf2b6a00b14ad589bd90c7169cebd296c6


---
# <a name="deploy-a-new-web-service"></a>Implementación de servicios web nuevos
Ahora, Microsoft Azure Machine Learning proporciona servicios web basados en [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) , con lo que se pueden usar nuevas opciones de planes de facturación e implementar el servicio web en varias regiones.

El flujo de trabajo general para implementar un servicio web mediante el portal de servicios web de Aprendizaje automático de Microsoft Azure Machine es el siguiente:

* Crear un experimento predictivo
* Implementarlo
* Configurar su nombre
* plan de facturación
* Probarlo
* Utilizarlo

En el siguiente gráfico se ilustra el flujo de trabajo.

![Flujo de trabajo de implementación de servicios web][1]

## <a name="deploy-web-service-from-studio"></a>Implementación de servicios web desde Studio
Para implementar un experimento como servicio web nuevo, inicie sesión en Estudio de aprendizaje automático de Microsoft Azure y cree un nuevo servicio web predictivo. 

**Nota**: Si ya ha implementado un experimento como servicio web clásico, no se podrá implementar como nuevo.

Haga clic en la opción **Ejecutar** de la parte inferior del lienzo del experimento y luego en **Deploy Web Service** (Implementar servicio web) y en **Deploy Web Service [New]** (Implementar servicio web [nuevo]). Se abrirá la página de implementación del administrador de servicios web de Aprendizaje automático.

## <a name="machine-learning-web-service-manager-deploy-experiment-page"></a>Página de implementación de experimentos del administrador de servicios web de Aprendizaje automático
En la página de implementación de experimentos, escriba un nombre para el servicio web.
Seleccione un plan de tarifa. Si ya tiene uno, puede seleccionarlo; si no, debe crear uno nuevo para el servicio. 

1. En el menú desplegable **Price Plan** (Plan de precios), seleccione un plan existente o elija la opción **Select new plan** (Seleccionar nuevo plan).
2. En **Nombre del plan**, escriba un nombre que identifique el plan en la factura.
3. Seleccione uno de los **niveles de planes mensuales**. Tenga en cuenta que los niveles de los planes predeterminados son los de la región predeterminada y que el servicio web se implementa en dicha región.

Haga clic en las páginas **Implementar** e Inicio rápido del servicio web que se abre.

## <a name="quickstart-page"></a>Página Inicio rápido
A través de la página Inicio rápido del servicio web podrá acceder a las tareas más comunes que se realizarán después de crear un servicio web nuevo, así como instrucciones sobre cómo hacerlo. Desde aquí, también puede acceder fácilmente a las páginas **Prueba** y **Consume** (Consumo).

## <a name="testing-your-web-service"></a>Pruebas del servicio web
En la página Inicio rápido, haga clic en la opción Test web service (Probar servicio web) de las tareas comunes.   

Para probar el servicio web como servicio de solicitud-respuesta (RRS), siga estos pasos:

* Haga clic en **Probar** en la barra de menús.
* Haga clic en **Request-Response**(Solicitud-respuesta).
* Escriba los valores adecuados para las columnas de entrada del experimento.
* Haga clic en **Test Request-Response**(Probar solicitud-respuesta).

Los resultados se mostrarán en el lado derecho de la página.

Para probar un servicio web de servicio de ejecución de lotes (BES), hay que utilizar un archivo CSV:

* Haga clic en **Probar** en la barra de menús.
* Haga clic en **Lotes**.
* En la entrada, haga clic en Examinar y vaya hasta al archivo de datos de ejemplo.
* Haga clic en **Probar**.

El estado de la prueba se muestra en **Test Batch Jobs**(Trabajos de pruebas por lotes).

## <a name="consuming-your-web-service"></a>Uso de servicios web
Cuando se implementa como servicio web, los experimentos de Aprendizaje automático de Azure proporcionan una API de REST que puede ser consumida por una amplia gama de dispositivos y plataformas. Esto es porque la sencilla API de REST acepta y responde con mensajes de formato JSON. El portal de Aprendizaje automático de Azure proporciona código que se puede utilizar para llamar al servicio web en R, C# y Python.

En la página Consuming (Consumo), puede encontrar la siguiente información:

* La clave de API y los URI para utilizar servicios web en aplicaciones
* Plantillas de aplicaciones web y de Excel para iniciar rápidamente el proceso de consumo.
* El código de ejemplo en C#, Python y R para poder comenzar.

Para obtener más información sobre cómo utilizar servicios web , consulte [Cómo consumir un servicio web de Aprendizaje automático de Azure implementado en un experimento de Aprendizaje automático](machine-learning-consume-web-services.md).

## <a name="next-steps"></a>Pasos siguientes
Para más información sobre el consumo de servicios web, consulte:

[Cómo consumir un servicio web de Aprendizaje automático de Azure implementado en un experimento de Aprendizaje automático](machine-learning-consume-web-services.md)

[Servicios web Azure Machine Learning: Implementación y consumo](machine-learning-deploy-consume-web-service-guide.md)

<!--Image references-->
[1]: ./media/machine-learning-webservice-deploy-a-web-service/armdeploymentworkflow.png


<!--links-->



<!--HONumber=Nov16_HO3-->


