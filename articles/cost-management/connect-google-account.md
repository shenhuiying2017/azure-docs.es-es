---
title: Conectar una cuenta de Google Cloud Platform a Azure Cost Management | Microsoft Docs
description: Conecte una cuenta de Google Cloud Platform para ver los datos de uso y el costo en los informes de Cost Management.
services: cost-management
keywords: 
author: bandersmsft
ms.author: banders
ms.date: 02/05/2018
ms.topic: article
ms.service: cost-management
manager: carmonm
ms.custom: 
ms.openlocfilehash: 81a38f471ee1f2f8064a956eca121fd0e6feb235
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/09/2018
---
# <a name="connect-a-google-cloud-platform-account"></a>Conectar una cuenta de Google Cloud Platform

Puede conectar una cuenta existente de Google Cloud Platform a Azure Cost Management. Después de conectar su cuenta a Cost Management, los datos de uso y de costo estarán disponibles en los informes de Cost Management. En este artículo se le ayuda a configurar y a conectar su cuenta de Google con Cost Management.

## <a name="collect-project-information"></a>Recopilación de información del proyecto

Debe comenzar por recopilar información acerca de su proyecto.

1. Inicie sesión en la consola de Google Cloud Platform en [https://console.cloud.google.com](https://console.cloud.google.com).
2. Revise la información del proyecto que desea incorporar a Cost Management y anote el **Nombre del proyecto** y el **Id. del proyecto**. Mantenga esta información a mano para los pasos posteriores.  
    ![Consola de Google Cloud Platform](./media/connect-google-account/gcp-console01.png)
3. Si la facturación no está habilitada ni vinculada a su proyecto, cree una cuenta de facturación. Para más información, consulte [Create a new billing account](https://cloud.google.com/billing/docs/how-to/manage-billing-account#create\_a\_new\_billing\_account) (Crear una nueva cuenta de facturación).

## <a name="enable-storage-bucket-billing-export"></a>Habilitar la exportación de facturación de depósitos de almacenamiento

Cost Management recupera los datos de facturación de Google de un depósito de almacenamiento. Mantenga la información de **Nombre del depósito** y **Prefijo del informe** a mano para usarla más tarde durante el registro de Cost Management.

Las tarifas del uso de Google Cloud Storage para almacenar informes de uso son muy reducidas. Para más información, consulte [Precios de Cloud Storage](https://cloud.google.com/storage/pricing).

1. Si no ha habilitado la exportación de facturas a un archivo, siga las instrucciones de [Habilitar la exportación de facturación a un archivo](https://cloud.google.com/billing/docs/how-to/export-data-file#how_to_enable_billing_export_to_a_file). Puede usar el formato de exportación de facturas JSON o CSV.
2. En caso contrario, en la consola de Google Cloud Platform, vaya a **Facturación** > **Exportación de la facturación**. Anote el **Nombre del depósito** y el **Prefijo del informe** de la facturación.  
    ![Exportación de facturación](./media/connect-google-account/billing-export.png)

## <a name="enable-google-cloud-platform-apis"></a>Habilite las API de Google Cloud Platform

Para recopilar información de uso y de recursos, Cost Management necesita que las siguientes API de Google Cloud Platform estén habilitadas:

- BigQuery API
- Google Cloud SQL
- Google Cloud Datastore API
- Google Cloud Storage
- Google Cloud Storage JSON API
- Google Compute Engine API

### <a name="enable-or-verify-apis"></a>Habilitar o comprobar API

1. En la consola de Google Cloud Platform, seleccione el proyecto que desee registrar con Cost Management.
2. Vaya a **API y servicios** > **Biblioteca**.
3. Use la búsqueda para encontrar todas las API enumeradas anteriormente.
4. Para cada API, compruebe que se muestra **API habilitada**. De lo contrario, haga clic en **HABILITAR**.

## <a name="add-a-google-cloud-account-to-cost-management"></a>Adición de una cuenta de Google Cloud para Cost Management

1. Abra el portal de Cloudyn desde Azure Portal o vaya a [https://azure.cloudyn.com](https://azure.cloudyn.com/) e inicie sesión.
2. Haga clic en **Configuración** (símbolo de engranaje) y, después, seleccione **Cuentas de Cloud**.
3. En **Administración de cuentas**, seleccione la pestaña **Cuentas de Google** y, después, haga clic en **Agregar nuevo +**.
4. En **Nombre de la cuenta de Google**, escriba la dirección de correo electrónico de la cuenta de facturación y, después, haga clic en **Siguiente**.
5. En el cuadro de diálogo de autenticación de Google, seleccione o escriba una cuenta de Google y, después, haga clic en **PERMITIR** para que cloudyn.com pueda acceder a su cuenta.
6. Agregue la información del proyecto que anotó antes. Incluido el **Id. del proyecto**, el nombre del **Proyecto**, el nombre del depósito de **facturación** y el prefijo del informe del **archivo facturación** y, después, haga clic en  **Guardar**.  
    ![Adición de un proyecto de Google](./media/connect-google-account/add-project.png)

Su cuenta de Google aparece en la lista de cuentas y debería aparecer como **Autenticada**. Debajo de esta, el nombre del proyecto de Google y el identificador deberían aparecer y tener un símbolo de marca de verificación verde. El estado de la cuenta debe aparecer como **Completado**.

En unas horas, los informes de Cost Management muestran información acerca del costo y del uso de Google.

## <a name="next-steps"></a>pasos siguientes

- Para obtener más información acerca de Azure Cost Management de Cloudyn, consulte el tutorial [Revisión del uso y los costos](./tutorial-review-usage.md) de Cost Management.
