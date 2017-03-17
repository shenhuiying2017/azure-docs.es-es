---
title: "Implementación de SAP S/4 HANA o BW/4 HANA en una máquina virtual de Azure | Microsoft Docs"
description: "Implementación de SAP S/4HANA o BW/4HANA en una máquina virtual de Azure"
services: virtual-machines-linux
documentationcenter: 
author: hermanndms
manager: timlt
editor: 
tags: azure-resource-manager
keywords: 
ms.assetid: 44bbd2b6-a376-4b5c-b824-e76917117fa9
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 09/15/2016
ms.author: hermannd
translationtype: Human Translation
ms.sourcegitcommit: 6f345bb816a3fd6f6fb8672b9a43a0d075bd94eb
ms.openlocfilehash: 939c051cad98590acffb6e550ca45bf5fec90d7e
ms.lasthandoff: 03/02/2017


---
# <a name="deploy-sap-s4hana-or-bw4hana-on-microsoft-azure"></a>Implementación de SAP S/4HANA o BW/4HANA en Microsoft Azure
En este artículo se describe cómo implementar S/4HANA en Microsoft Azure por medio de SAP Cloud Appliance Library (SAP CAL) 3.0. La implementación de otras soluciones basadas en SAP Hana, como BW/4 Hana, funciona del mismo modo desde la perspectiva del proceso. Solo se tiene que seleccionar otra solución.

> [!NOTE]
Para obtener más información sobre SAP Cloud Appliance Library, consulte la [página principal de su sitio](https://cal.sap.com/). Hay un blog de SAP sobre [SAP Cloud Appliance Library 3.0](http://scn.sap.com/community/cloud-appliance-library/blog/2016/05/27/sap-cloud-appliance-library-30-came-with-a-new-user-experience).

## <a name="step-by-step-process-to-deploy-the-solution"></a>Proceso paso a paso para implementar la solución

Las capturas de pantalla siguientes muestran cómo implementar S/4HANA en Azure. El proceso funciona del mismo modo para otras soluciones como BW/4 Hana.

La primera captura de pantalla muestra todas las soluciones basadas en SAP CAL HANA disponibles en Azure. Observe la **edición local SAP S/4HANA** en la parte inferior.

![Captura de pantalla de la ventana Soluciones de SAP Cloud Appliance Library](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic-1b.jpg)

Primero, cree una nueva cuenta de SAP CAL. En **Cuentas**, verá dos opciones para Azure: Microsoft Azure y una opción de Azure administrada por 21Vianet. En este ejemplo, elija **Microsoft Azure**.

![Captura de pantalla de la ventana Cuentas de SAP Cloud Appliance Library](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic-2.jpg)

Después, escriba el identificador de suscripción de Azure que puede encontrarse en Azure Portal. Luego, descargue un certificado de administración de Azure.

![Captura de pantalla de la ventana Cuentas de SAP Cloud Appliance Library](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic3b.jpg)

> [!NOTE]
Para buscar el identificador de suscripción de Azure, debe usar el Portal de Azure clásico, no la versión más reciente. Esto se debe a que SAP CAL aún no se ha adaptado a este nuevo modelo y sigue requiriendo el modelo "clásico" para usar certificados de administración.

En la captura de pantalla siguiente se muestra el portal clásico. En **CONFIGURACIÓN**, seleccione la pestaña **SUSCRIPCIONES** para encontrar el identificador de suscripción que debe especificar en la ventana Cuentas de SAP CAL.

![Captura de pantalla del Portal de Azure clásico](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic4b.jpg)

En **CONFIGURACIÓN**, cambie a la pestaña **CERTIFICADOS DE ADMINISTRACIÓN**. Cargue un certificado de administración para conceder a SAP CAL los permisos para crear máquinas virtuales dentro de una suscripción de cliente (va a cargar el certificado de administración que se descargó antes de SAP CAL).

![Captura de pantalla de la pestaña Certificados de administración del Portal de Azure clásico](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic5.jpg)

Aparece un cuadro de diálogo para seleccionar el archivo de certificado descargado.

![Captura de pantalla del cuadro de diálogo Cargar un certificado de administración](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic8.jpg)

Una vez que se cargue el certificado, se puede probar la conexión entre SAP CAL y la suscripción de Azure dentro de SAP CAL. Debería aparecer un mensaje que indica que la conexión es válida.

![Captura de pantalla de la ventana Cuentas de SAP Cloud Appliance Library](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic9.jpg)

Después, seleccione una solución que vaya a implementarse y cree una instancia.
Escriba un nombre de instancia, elija una región de Azure y defina la contraseña maestra para la solución.

![Captura de pantalla de la ventana Soluciones de SAP Cloud Appliance Library](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic10.jpg)

Después de un tiempo –cuánto depende del tamaño y la complejidad de la solución (SAP CAL ofrece una estimación)–, se muestra como "activa" y está lista para usarse.

![Captura de pantalla de la ventana Instancias de SAP Cloud Appliance Library](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic11.jpg)

Puede ver algunos detalles de la solución, por ejemplo, qué tipo de máquinas virtuales se implementaron. En este caso, se crearon tres máquinas virtuales de Azure de diferentes tamaños y finalidades.

![Captura de pantalla de la ventana Instancias de SAP Cloud Appliance Library](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic12.jpg)

En el Portal de Azure clásico, las máquinas virtuales pueden encontrarse empezando por el mismo nombre de instancia que se proporcionó en SAP CAL.

![Captura de pantalla que muestra tres máquinas virtuales en el Portal de Azure clásico](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic13.jpg)

Ahora es posible conectarse a la solución mediante el botón correspondiente en el portal de SAP CAL. El cuadro de diálogo contiene un vínculo a una guía de usuario donde se describen todas las credenciales predeterminadas para trabajar con la solución.

![Captura de pantalla del cuadro de diálogo Conectar con instancia](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic14b.jpg)

Otra opción consiste en iniciar sesión en la máquina virtual Windows cliente e iniciar la instancia de SAP GUI configurada previamente.

![Captura de pantalla de la GUI de SAP configuradas previamente](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic15.jpg)

