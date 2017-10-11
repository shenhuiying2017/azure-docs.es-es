---
title: "Esquemas para la validación de XML: Azure Logic Apps | Microsoft Docs"
description: "Validación de documentos XML con esquemas para Azure Logic Apps y Enterprise Integration Pack"
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: anneta
editor: cgronlun
ms.assetid: 56c5846c-5d8c-4ad4-9652-60b07aa8fc3b
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/29/2016
ms.author: LADocs; padmavc
ms.openlocfilehash: 4f58a587c1f10aea1cee89e46fa9ec340e0d21c6
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="validate-xml-with-schemas-for-azure-logic-apps-and-the-enterprise-integration-pack"></a>Validación de documentos XML con esquemas para Azure Logic Apps y Enterprise Integration Pack

Los esquemas permiten confirmar que los documentos XML recibidos son válidos y que contienen los datos esperados en un formato predefinido. Los esquemas también ayudan a validar los mensajes que se intercambian en un escenario B2B.

## <a name="add-a-schema"></a>Agregar un esquema

1. En Azure Portal, seleccione **Más servicios**.

    ![Azure Portal, "Más servicios"](media/logic-apps-enterprise-integration-schemas/overview-11.png)

2. En el cuadro de búsqueda, especifique **integración** y seleccione **Integration Accounts** (Cuentas de integración) en la lista de resultados.

    ![Cuadro de búsqueda](media/logic-apps-enterprise-integration-schemas/overview-21.png)

3. Seleccione la **cuenta integración** en la que vaya a agregar el esquema.

    ![Lista de cuentas de integración](media/logic-apps-enterprise-integration-schemas/overview-31.png)

4. Seleccione el icono de **Esquemas**.

    ![Cuenta de integración de ejemplo, "Esquemas"](media/logic-apps-enterprise-integration-schemas/schema-11.png)

### <a name="add-a-schema-file-smaller-than-2-mb"></a>Incorporación de un archivo de esquema inferior a 2 MB

1. En la hoja **Esquemas** que se abre (de los pasos anteriores), elija **Agregar**.

    ![Hoja de esquemas, "Agregar"](media/logic-apps-enterprise-integration-schemas/schema-21.png)

2. Escriba un nombre para el esquema. Para cargar el archivo de esquema, seleccione el icono de carpeta junto al cuadro de texto **Esquema**. Cuando termine el proceso de carga, seleccione **Aceptar**.

    ![Captura de pantalla de "Agregar esquema" con "Archivo pequeño" resaltado](media/logic-apps-enterprise-integration-schemas/schema-31.png)

### <a name="add-a-schema-file-larger-than-2-mb-up-to-8-mb-maximum"></a>Incorporación de un archivo de esquema superior a 2 MB (hasta un máximo de 8 MB)

Estos pasos difieren en función del nivel de acceso del contenedor de blob: **Público** o **Sin acceso anónimo**.

**Para determinar este nivel de acceso**

1.  Abra el **Explorador de Azure Storage**. 

2.  En **Contenedores de blob**, seleccione el contenedor de blob que desee. 

3.  Seleccione **Seguridad**, **Nivel de acceso**.

Si el nivel de acceso de seguridad de blob es **Público**, siga estos pasos.

![Explorador de Azure Storage, con "Contenedores de blob", "Seguridad" y "Público" resaltados](media/logic-apps-enterprise-integration-schemas/blob-public.png)

1. Cargue el esquema en la cuenta de almacenamiento y copie el URI.

    ![Cuenta de almacenamiento con URI resaltado](media/logic-apps-enterprise-integration-schemas/schema-blob.png)

2. En **Agregar esquema**, seleccione **Archivo grande** y proporcione el URI en el cuadro de texto **URI de contenido**.

    ![Esquemas con el botón "Agregar" y "Archivo grande" resaltados](media/logic-apps-enterprise-integration-schemas/schema-largefile.png)

Si el nivel de acceso de seguridad de blob es **Sin acceso anónimo**, siga estos pasos.

![Explorador de Azure Storage, con "Contenedores de blob", "Seguridad" y "Sin acceso anónimo" resaltados](media/logic-apps-enterprise-integration-schemas/blob-1.png)

1. Cargue el esquema en la cuenta de almacenamiento.

    ![Cuenta de almacenamiento](media/logic-apps-enterprise-integration-schemas/blob-3.png)

2. Genere una firma de acceso compartido para el esquema.

    ![Cuenta de almacenamiento con la ficha firmas de acceso compartido resaltada](media/logic-apps-enterprise-integration-schemas/blob-2.png)

3. En **Agregar esquema**, seleccione **Archivo grande** y proporcione el URI de firma de acceso compartido en el cuadro de texto **URI de contenido**.

    ![Esquemas con el botón "Agregar" y "Archivo grande" resaltados](media/logic-apps-enterprise-integration-schemas/schema-largefile.png)

4. En la hoja **Esquemas** de la cuenta de integración debería ver ahora el esquema recién agregado.

    ![Cuenta de integración con "Esquemas" y el nuevo esquema resaltados](media/logic-apps-enterprise-integration-schemas/schema-41.png)

## <a name="edit-schemas"></a>Editar esquemas

1. Seleccione el icono de **Esquemas**.

2. Cuando se abra la hoja **Esquemas**, seleccione el esquema que desea editar.

3. En la hoja **Esquemas**, seleccione **Editar**.

    ![Hoja de esquemas](media/logic-apps-enterprise-integration-schemas/edit-12.png)

4. Seleccione el archivo de esquemas que desea editar y, a continuación, seleccione **Abrir**.

    ![Abrir archivo de esquema que desea editar](media/logic-apps-enterprise-integration-schemas/edit-31.png)

Azure muestra un mensaje que indica que el esquema se ha cargado correctamente.

## <a name="delete-schemas"></a>Eliminar esquemas

1. Seleccione el icono de **Esquemas**.

2. Cuando se abra la hoja **Esquemas**, seleccione el esquema que desea eliminar.

3. En la hoja **Esquemas**, seleccione **Editar**.

    ![Hoja de esquemas](media/logic-apps-enterprise-integration-schemas/delete-12.png)

4. Para confirmar que desea eliminar el contrato seleccionado, elija **Sí**.

    ![Mensaje de confirmación para "Eliminar esquema"](media/logic-apps-enterprise-integration-schemas/delete-21.png)

    En la hoja **Esquemas**, la lista de esquemas se actualiza y ya no incluye el esquema que ha eliminado.

    ![La cuenta de integración con "Esquemas" resaltado](media/logic-apps-enterprise-integration-schemas/delete-31.png)

## <a name="next-steps"></a>Pasos siguientes
* [Más información sobre Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md "Información sobre Enterprise Integration Pack").  

