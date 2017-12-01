---
title: "Administración de costos con Azure Cost Management | Microsoft Docs"
description: "Administre los costos utilizando informes de asignación, visualización y contracargo de costos."
services: cost-management
keywords: 
author: bandersmsft
ms.author: banders
ms.date: 11/21/2017
ms.topic: tutorial
ms.service: cost-management
ms.custom: mvc
manager: carmonm
ms.openlocfilehash: bfbcded98814500a03b2b79b0248c84f8f043dc0
ms.sourcegitcommit: 8aa014454fc7947f1ed54d380c63423500123b4a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/23/2017
---
# <a name="manage-costs-by-using-azure-cost-management"></a>Administración de costos con Azure Cost Management

Administre los costos y genere informes de visualización de costos con Azure Cost Management de Cloudyn mediante la asignación de costos basados en etiquetas. El proceso de asignación de costos asigna los costos a los recursos consumidos en la nube. Los costos se asignan completamente cuando todos los recursos se clasifican con etiquetas. Una vez asignados los costos, puede mostrar o cargar los costos a los usuarios mediante paneles e informes. Pero puede ser que muchos recursos no estén etiquetados o no se puedan etiquetar al empezar a usar Cost Management.

Por ejemplo, puede ser que quiera obtener el reembolso de los costos de ingeniería. Debe poder demostrar al equipo de ingeniería que necesita un importe concreto, en función de los costos de los recursos. Puede mostrarles un informe de todos los recursos consumidos y etiquetados con el término *ingeniería*.

En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Usar etiquetas personalizadas para asignar costos.
> * Crear informes de visualización y contracargo de gastos.

## <a name="use-custom-tags-to-allocate-costs"></a>Uso de etiquetas personalizadas para asignar costos

Al iniciar la asignación de costos, lo primero que hay que hacer es definir el alcance mediante el uso de un modelo de costos. El modelo de costos no cambia los costos, sino que los distribuye. Al crear un modelo de costos, segmenta los datos por la entidad de costo, la cuenta o la suscripción y con varias etiquetas. Algunas de las etiquetas habituales son el código de facturación, el centro de costos o el nombre del grupo. Las etiquetas también permiten ofrecer una visualización o aplicar un contracargo de gastos a otras partes de su organización.

Para crear un modelo de asignación de costos personalizado, en el menú del informe seleccione **Cost** (Costo) &gt; **Cost Management** (Administración de costos) &gt; **Cost Allocation 360°** (Asignación de costos 360°).

![Selección de Cost Allocation 360° (Asignación de costos 360°)](./media/tutorial-manage-costs/cost-allocation-360.png)

En la página **Cost Allocation 360°** (Asignación de costos 360°), seleccione **Agregar** y después escriba un nombre y una descripción para el modelo de costos. Seleccione todas las cuentas o cuentas individuales. Si desea usar cuentas individuales, puede seleccionar varias cuentas de varios proveedores de servicios en la nube. A continuación, haga clic en **Categorization** (Clasificación) para elegir las etiquetas detectadas que sirvan para clasificar los datos de costos. Elija las etiquetas (categorías) que se van a incluir en el modelo. En el ejemplo siguiente, la etiqueta **Unit** (Unidad) está seleccionada.

![Ejemplo de clasificación de modelos de costos](./media/tutorial-manage-costs/cost-model01.png)



En el ejemplo, 14.444 $ aparece sin clasificar (sin etiquetas).

A continuación, seleccione **Uncategorized Resources** (Recursos sin categoría) y seleccione los servicios que tengan costos sin asignar. A continuación, defina las reglas para asignar costos.

Por ejemplo, puede ser que quiera tomar los costos de almacenamiento de Azure y distribuirlos de forma equitativa entre las máquinas virtuales de Azure. Para ello, seleccione el servicio **Azure/Almacenamiento**, seleccione **Proportional to Categorized** (De proporcional a clasificado) y después seleccione **Azure/VM**. Seleccione **Crear**.

![Ejemplo de regla de asignación de modelos de costos para una distribución equitativa](./media/tutorial-manage-costs/cost-model02.png)



En otro ejemplo, puede ser que le interese asignar todos los costos de red de Azure a una unidad de negocio específica de su organización. Para ello, seleccione el servicio **Azure/Red** y después seleccione **Explicit Distribution** (Distribución explícita). A continuación, establezca el porcentaje de distribución en 100 y seleccione la unidad de negocio: **G&amp;A** en la siguiente imagen:

![Ejemplo de regla de asignación de modelos de costos para una unidad de negocio específica](./media/tutorial-manage-costs/cost-model03.png)



Para todos los recursos que queden sin clasificar, cree reglas de asignación adicionales.

Si dispone de instancias reservadas de Amazon Web Services (AWS) sin asignar, puede asignarlas a las categorías etiquetadas con **Reserved Instances** (Instancias reservadas).

Para ver información sobre las elecciones que ha hecho para asignar costos, seleccione **Summary** (Resumen). Para guardar la información y seguir trabajando en otras reglas, seleccione **Save As Draft** (Guardar como borrador). O bien, para guardar la información y hacer que Cloudyn empiece a procesar su modelo de asignación de costos, seleccione **Guardar y activar**.

En la lista de modelos de costos se muestra su nuevo modelo de costos con un **Processing status** (Estado de procesamiento). La base de datos de Cloudyn puede tardar un poco en incluir su modelo de costos. Cuando el procesamiento haya finalizado, el estado se actualizará a **Completed** (Completado). A continuación, puede ver los datos de su modelo de costos en el informe de Cost Analysis en **Extended Filters** (Filtros ampliados) &gt; **Cost Model** (Modelo de costos).

### <a name="category-manager"></a>Administrador de categoría

El administrador de categorías es una herramienta de limpieza de datos que le permite combinar los valores de varias categorías (etiquetas) para crear otras nuevas. Es una herramienta sencilla basada en reglas en la que usted selecciona una categoría y crea reglas para combinar los valores existentes. Por ejemplo, podría tener categorías para **R&amp;D** y **dev**, en que ambas representan el grupo de desarrollo.

En el portal de Cloudyn, haga clic en el símbolo de engranaje situado en la esquina superior derecha y seleccione **Category Management** (Administración de categorías). Para crear una categoría nueva, seleccione el signo más (**+**). Escriba un nombre para la categoría y después en **Keys** (Claves), introduzca las claves de categoría que quiera incluir en la categoría nueva.

Al definir una regla, puede agregar varios valores con una condición OR. También puede hacer algunas operaciones de cadenas básicas. En cualquiera de los casos, haga clic en el símbolo de puntos suspensivos (**…**) situado a la derecha de **Rule** (Regla).

Para definir una nueva regla, en el área **Rules** (Reglas), cree una nueva regla. Por ejemplo, escriba **dev** en **Rules** (Reglas) y escriba **R&amp;D** en **Actions** (Acciones). Cuando haya terminado, guarde los cambios.

En la siguiente imagen se muestra un ejemplo de reglas creadas para una nueva categoría denominada **Work-Load** (Carga de trabajo):

![Categoría de ejemplo](./media/tutorial-manage-costs/category01.png)

### <a name="tag-sources-and-reports"></a>Informes y orígenes de etiqueta

Los datos de etiqueta que se ven en los informes de Cloudyn se originan en tres lugares:

- API de recursos del proveedor en la nube
- API de facturación del proveedor en la nube
- Etiquetas creadas manualmente desde los orígenes siguientes:
    - Etiquetas de entidad de Cloudyn: metadatos definidos por el usuario aplicados a las entidades de Cloudyn
    - Category Manager: una herramienta de limpieza de datos que crea nuevas etiquetas basadas en reglas que se aplican a las etiquetas existentes

Para ver las etiquetas del proveedor en la nube en informes de costos de Cloudyn, debe crear un modelo de asignación de costos personalizado con Cost Allocation 360°. Para ello, vaya a **Cost** (Costo) > **Cost Management** (Administración de costos) > **Cost Allocation 360°**, seleccione las etiquetas deseadas y, a continuación, defina las reglas para controlar los costos no etiquetados. A continuación, cree un nuevo modelo de costos. Luego, podrá ver informes en Cost Allocation Analysis (Análisis de asignación de costos) para ver, filtrar y ordenar las etiquetas de recursos de Azure.

Las etiquetas de recursos de Azure solo aparecen en los informes **Cost Allocation Analysis** (Análisis de asignación de costos).

Las etiquetas de facturación del proveedor en la nube aparecen en todos los informes de costos.

Las etiquetas de entidad de Cloudyn y las que crea manualmente aparecen en todos los informes de costos.


## <a name="create-showback-and-chargeback-reports"></a>Creación de informes de visualización y contracargo de gastos

Los métodos que las organizaciones usan para mostrar los gastos y hacer contracargos varían mucho. Se puede utilizar cualquiera de los paneles e informes del portal de Cloudyn como base para cualquier finalidad. Puede proporcionar acceso de usuario a cualquier persona de su organización para que pueda ver los paneles e informes que necesite. Todos los informes de análisis de costos admiten una visualización de gastos, puesto que muestran a los usuarios los recursos que han consumido. También permiten a los usuarios profundizar en los datos de uso o de costos específicos de su grupo dentro de la organización.

Para ver los resultados de la asignación de costos, abra el informe de análisis de costos y seleccione el modelo de costos que ha creado. A continuación, agregue una agrupación por una o varias de las etiquetas seleccionadas en el modelo de costos.

![Informe de análisis de costos](./media/tutorial-manage-costs/cost-analysis.png)

Puede crear y guardar fácilmente los informes que se centran en servicios concretos consumidos por grupos específicos. Por ejemplo, podría tener un departamento que hiciera un uso exhaustivo de máquinas virtuales de Azure. Puede crear un informe que se filtre en máquinas virtuales de Azure para mostrar el consumo y los costos.

Si tiene que proporcionar datos de instantáneas a otros equipos, puede exportar los informes en formato PDF o CSV.


## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha aprendido cómo:

> [!div class="checklist"]
> * Usar etiquetas personalizadas para asignar costos.
> * Crear informes de visualización y contracargo de gastos.



Para obtener más información sobre cómo empezar a usar Cloudyn y sus características, pase a la documentación de Cloudyn.

> [!div class="nextstepaction"]
> [Documentación de Cloudyn](https://support.cloudyn.com/hc/)
