---
title: Creación de informes de instantánea de Cloud App Discovery en Azure Active Directory | Microsoft Docs
description: Ofrece información sobre la búsqueda y la administración de aplicaciones con Cloud App Discovery, sobre cuáles son los beneficios y sobre cómo funciona.
services: active-directory
keywords: cloud app discovery, administrar aplicaciones
documentationcenter: ''
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.topic: article
ms.date: 09/22/2017
ms.author: barbkess
ms.reviewer: nigu
ms.custom: it-pro
ms.openlocfilehash: 68585edad6e7d1b6b21a48f1cf4242875cea538a
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/11/2018
---
# <a name="create-cloud-app-discovery-snapshot-reports"></a>Creación de informes de instantánea de Cloud App Discovery

Antes de configurar el recopilador de registros automático, cargue manualmente un registro y deje que Cloud App Security lo analice. Si aún no tiene un registro y desea ver un ejemplo del aspecto de su registro, utilice el procedimiento siguiente para descargar un archivo de registro de ejemplo para ver cómo puede ser.

## <a name="to-create-a-snapshot-report"></a>Para crear un informe de instantánea

1. Recopile los archivos de registro del servidor proxy y firewall a través de los cuales los usuarios de su organización acceden a Internet. Recopile los registros durante períodos de tráfico pico que sean representativos de la actividad del usuario en su organización.
2. En [la barra de menús de Cloud App Security](https://portal.cloudappsecurity.com), seleccione **Detectar** y, a continuación, **Crear informe de instantánea**.
  
  ![Creación de un informe de instantánea](./media/cloud-app-discovery-create-snapshot-reports/create-snapshot-command.png)
3. Escriba un **nombre de informe** y una **descripción**.
    
  ![Nuevo informe de instantánea](./media/cloud-app-discovery-create-snapshot-reports/create-snapshot-form.png)
4. Seleccione el **origen de datos** desde el que desea cargar los archivos de registro.
5. Compruebe el formato del registro para asegurarse de que está formateado correctamente según el ejemplo que puede descargar. Haga clic en **View and verify** (Ver y comprobar) y, después, haga clic en **Descargar registro de ejemplo**. A continuación, compare el registro con el ejemplo que se proporciona para asegurarse de que es compatible.
  
  ![Comprobación del formato del registro](./media/cloud-app-discovery-create-snapshot-reports/create-snapshot-verify.png)
  >  [!NOTE]
  > Se admite el formato de ejemplo FTP en las instantáneas y en la carga automatizada mientras syslog es compatible únicamente con la carga automatizada. Si se descarga un registro de ejemplo, se descarga un registro FTP de ejemplo.
6. **Elija los registros de tráfico** que desea cargar. Puede cargar hasta 20 archivos a la vez. También se admiten archivos comprimidos y zip.
  
7. Haga clic en **Create**(Crear). Una vez finalizada la carga, puede tardar algún tiempo en analizarse. Si este es el caso, Cloud App Discovery envía una notificación por correo electrónico cuando haya terminado.

8. Seleccione **Administrar los informes de instantáneas** y seleccione su informe de instantánea.
  
  ![Administración de informes de instantánea](./media/cloud-app-discovery-create-snapshot-reports/create-snapshot-manage.png)

## <a name="next-steps"></a>Pasos siguientes

* [Introducción al uso de Cloud App Discovery en Azure AD](cloud-app-discovery.md)
* [Configuración de la carga de registro automático para la creación de informes continuos](https://docs.microsoft.com/cloud-app-security/discovery-docker)
* [Uso de un analizador de registros personalizado](https://docs.microsoft.com/cloud-app-security/custom-log-parser)
