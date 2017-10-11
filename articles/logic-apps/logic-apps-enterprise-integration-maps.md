---
title: "Transformación de XML con mapas XSLT: Azure Logic Apps | Microsoft Docs"
description: "Incorporación de mapas XSLT para transformar datos XML con Azure Logic Apps y Enterprise Integration Pack"
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: anneta
editor: cgronlun
ms.assetid: 90f5cfc4-46b2-4ef7-8ac4-486bb0e3f289
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2016
ms.author: LADocs; padmavc
ms.openlocfilehash: 4445a84a6c6425110e7d705019a28b5cc5447046
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="add-maps-for-xml-data-transform"></a>Incorporación de mapas para la transformación de datos XML

Enterprise Integration utiliza mapas para transformar datos XML de un formato a otro. Un mapa es un documento XML que define qué datos de un documento deben transformarse en otro formato. 

## <a name="why-use-maps"></a>¿Por qué se utilizan las asignaciones?

Suponga que recibe periódicamente pedidos o facturas B2B de clientes que usen el formato de fecha AAAMMDD. Sin embargo, en su organización, las fechas se almacenan en el formato MMDDAAA. Puede usar una asignación para *transformar* el formato de fecha AAAMMDD en MMDDAAA antes de almacenar los detalles de los pedidos o las facturas en la base de datos de actividades de clientes.

## <a name="how-do-i-create-a-map"></a>¿Cómo se crea un mapa?

Puede crear proyectos de integración de BizTalk con [Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md "Información sobre Enterprise Integration Pack") para Visual Studio 2015. Posteriormente, puede crear un archivo de mapa de Integration que le permite mostrar en un mapa los elementos entre dos archivos de esquema XML. Después de compilar este proyecto, obtendrá un documento XSLT.

## <a name="how-do-i-add-a-map"></a>¿Cómo se puede agregar un mapa?

1. En Azure Portal, seleccione **Examinar**.

    ![](./media/logic-apps-enterprise-integration-overview/overview-1.png)

2. En el cuadro de búsqueda, especifique **integración** y seleccione **Integration Accounts** (Cuentas de integración) en la lista de resultados.

    ![](./media/logic-apps-enterprise-integration-overview/overview-2.png)

3. Seleccione la cuenta integración en la que vaya a agregar el mapa.

    ![](./media/logic-apps-enterprise-integration-overview/overview-3.png)

4. Seleccione el icono de **Mapas**.

    ![](./media/logic-apps-enterprise-integration-maps/map-1.png)

5. Una vez que se abre la hoja de mapas, elija **Agregar**.

    ![](./media/logic-apps-enterprise-integration-maps/map-2.png)  

6. Escriba un **nombre** para el mapa. Para cargar el archivo de mapa, seleccione el icono de carpeta de la derecha del cuadro de texto **Mapa**. Cuando termine el proceso de carga, seleccione **Aceptar**.

    ![](./media/logic-apps-enterprise-integration-maps/map-3.png)

7. Después de que Azure agregue el mapa a la cuenta de integración, obtendrá un mensaje en la pantalla que muestra si se ha agregado el archivo de mapa o no. Después de obtener este mensaje, elija el icono de **mapas** para que pueda ver el mapa recién agregado.

    ![](./media/logic-apps-enterprise-integration-maps/map-4.png)

## <a name="how-do-i-edit-a-map"></a>¿Cómo se puede editar un mapa?

Debe cargar un nuevo archivo de mapa con los cambios que desee. En primer lugar, puede descargar el mapa para editarlo.

Siga estos pasos para cargar un nuevo mapa que reemplace el que ya existe.

1. Seleccione el icono de **Mapas**.

2. Una vez que se abra la hoja de mapas, seleccione el mapa que desea editar.

3. En la hoja de **mapas**, elija **Actualizar**.

    ![](./media/logic-apps-enterprise-integration-maps/edit-1.png)

4. En el selector de archivos, seleccione el archivo de mapa que desea cargar y haga clic en **Abrir**.

    ![](./media/logic-apps-enterprise-integration-maps/edit-2.png)

## <a name="how-to-delete-a-map"></a>¿Cómo se eliminan asignaciones?

1. Seleccione el icono de **Mapas**.

2. Una vez que se abra la hoja de mapas, seleccione el mapa que desea eliminar.

3. Elija **Eliminar**.

    ![](./media/logic-apps-enterprise-integration-maps/delete.png)

4. Confirme que desea eliminar el mapa.

    ![](./media/logic-apps-enterprise-integration-maps/delete-confirmation-1.png)

## <a name="next-steps"></a>Pasos siguientes
* [Más información sobre Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md "Información sobre Enterprise Integration Pack")  
* [Más información sobre los contratos](../logic-apps/logic-apps-enterprise-integration-agreements.md "Información sobre los contratos de integración de empresas")  
* [Más información sobre las transformaciones](logic-apps-enterprise-integration-transform.md "Información sobre las transformaciones de integración empresarial")  

