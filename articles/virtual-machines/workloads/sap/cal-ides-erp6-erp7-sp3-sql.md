---
title: "Implementación de SAP IDES EHP7 SP3 para SAP ERP 6.0 en Azure | Microsoft Docs"
description: "Implementación de SAP IDES EHP7 SP3 para SAP ERP 6.0 en Azure"
services: virtual-machines-windows
documentationcenter: 
author: hermanndms
manager: timlt
editor: 
tags: azure-resource-manager
keywords: 
ms.assetid: 626c1523-1026-478f-bd8a-22c83b869231
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 09/16/2016
ms.author: hermannd
ms.openlocfilehash: 91eed294077ff72d0760018b10c98f32db88f3be
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="deploy-sap-ides-ehp7-sp3-for-sap-erp-60-on-azure"></a>Implementación de SAP IDES EHP7 SP3 para SAP ERP 6.0 en Azure
En este artículo se describe cómo implementar un sistema SAP IDES que se ejecuta con SQL Server y el sistema operativo Windows en Azure por medio de SAP Cloud Appliance Library (SAP CAL) 3.0. Las capturas de pantalla muestran el proceso paso a paso. Para implementar otra solución, siga los mismos pasos.

Para empezar con SAP CAL, vaya al sitio web de [SAP Cloud Appliance Library](https://cal.sap.com/). SAP también tiene un blog acerca de [SAP Cloud Appliance Library 3.0](http://scn.sap.com/community/cloud-appliance-library/blog/2016/05/27/sap-cloud-appliance-library-30-came-with-a-new-user-experience). 

> [!NOTE]
A partir del 29 de mayo de 2017 puede usar el modelo de implementación mediante Azure Resource Manager, además del modelo de implementación clásico, que se usa menos, para implementar SAP CAL. Se recomienda usar el nuevo modelo de implementación mediante Resource Manager, en lugar del modelo de implementación clásico.

Si ya ha creado una cuenta de SAP CAL que utiliza el modelo clásico, *debe crear otra*. Dicha cuenta se debe implementar exclusivamente en Azure mediante el modelo de Resource Manager.

Después de iniciar sesión SAP CAL, la primera página normalmente conducir a la página de **soluciones**. El número de soluciones que se ofrecen en SAP CAL aumenta continuamente, por lo que es posible que deba desplazarse un poco para encontrar la solución que desea. La SAP IDES para Windows resaltada que está disponible exclusivamente en Azure muestra el proceso de implementación:

![Soluciones de SAP CAL](./media/cal-ides-erp6-ehp7-sp3-sql/ides-pic1.jpg)

### <a name="create-an-account-in-the-sap-cal"></a>Creación de una cuenta en SAP CAL
1. La primera vez que inicie sesión en SAP CAL, utilice S-User de SAP, o cualquier otro usuario registrado de SAP. A continuación, defina una cuenta de SAP CAL que SAP CAL use para implementar aplicaciones en Azure. En la definición de la cuenta, debe:

    a. Seleccione el modelo de implementación en Azure (Resource Manager o clásico).

    b. Especifique la suscripción a Azure. Una cuenta de SAP CAL no se puede asignar a más de una suscripción. Si necesita más de una suscripción, debe crear otra cuenta de SAP CAL.
    
    c. Conceda el permiso de SAP CAL para realizar la implementación en la suscripción de Azure.

    > [!NOTE]
    Los pasos siguientes muestran cómo crear una cuenta de SAP CAL para las implementaciones mediante Resource Manager. Si ya tiene una cuenta de SAP CAL vinculada al modelo de implementación clásico, *tendrá que* seguir estos pasos para crear una cuenta de SAP CAL nueva. La nueva cuenta de SAP CAL se tiene que implementar mediante el modelo de Resource Manager.

2. Para crear una nueva cuenta de SAP CAL, la página **Accounts** (Cuentas) muestra dos opciones para Azure: 

    a. **Microsoft Azure (classic)** es el modelo de implementación clásico y ya no se suele usar.

    b. **Microsoft Azure** es el nuevo modelo de implementación mediante Resource Manager.

    ![Cuentas de SAP CAL](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic-2a.PNG)

    Para implementar mediante el modelo de Resource Manager, seleccione **Microsoft Azure**.

    ![Cuentas de SAP CAL](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic3c.PNG)

3. En el campo **Suscription ID**, especifique el identificador de suscripción de Azure, que se puede encontrar en Azure Portal. 

    ![Identificador de suscripción de SAP CAL](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic3c.PNG)

4. Para autorizar que SAP CAL se implemente en la suscripción de Azure que ha definido, haga clic en **Authorize** (Autorizar). La siguiente página aparecerá en la pestaña del explorador:

    ![Inicio de sesión de servicios en la nube de Internet Explorer](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic4c.PNG)

5. Si se enumera más de un usuario, elija la cuenta de Microsoft vinculada para ser el coadministrador de la suscripción de Azure que ha seleccionado. La siguiente página aparecerá en la pestaña del explorador:

    ![Confirmación de servicios en la nube de Internet Explorer](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic5a.PNG)

6. Haga clic en **Aceptar**. Si la autorización es correcta, vuelve a mostrarse la definición de la cuenta de SAP CAL. Poco después, un mensaje confirma que el proceso de autorización se ha realizado correctamente.

7. Para asignar la cuenta de SAP CAL recién creada al usuario, escriba en el cuadro de texto de la derecha su **identificador de usuario** y haga clic en **Agregar**. 

    ![Asociación de cuenta a usuario](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic8a.PNG)

8. Para asociar una cuenta con el usuario que utiliza para iniciar sesión en SAP CAL, haga clic en **Review** (Revisar). 

9. Para crear la asociación entre el usuario y la cuenta de SAP CAL recién creada, haga clic en **Create** (Crear).

    ![Asociación de usuario a cuenta](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic9b.PNG)

Ha creado correctamente una cuenta de SAP CAL que puede:

- Usar el modelo de implementación mediante Resource Manager.
- Implementar sistemas de SAP en su suscripción a Azure.

> [!NOTE]
Para poder implementar la solución SAP IDES para Windows y SQL Server, es preciso registrarse para obtener una suscripción de SAP CAL. Si no se hace, la solución podría aparecer como **Locked** (Bloqueado) en la página de información general.

### <a name="deploy-a-solution"></a>Implementación de una solución
1. Después de configurar una cuenta de SAP CAL, seleccione **La solución SAP IDES en Windows y la solución SQL Server**. Haga clic en **Create Instance** (Crear instancia) y confirme las condiciones de uso. 

2. En la página **Basic Mode: Create Instance** (Modo básico: crear instancia), tiene que:

    a. Especificar en **Name** el nombre de la instancia.

    b. Seleccionar una **región** de Azure. Para poder elegir entre varias regiones de Azure, puede que necesite tener una suscripción a SAP CAL.

    c.  Escribir en **Master Password** la contraseña maestra de la solución, como se muestra:

    ![SAP CAL, Modo básico: crear instancia](./media/cal-ides-erp6-ehp7-sp3-sql/ides-pic10a.png)

3. Haga clic en **Create** (Crear). Después de un tiempo, y en función del tamaño y la complejidad de la solución (SAP CAL ofrece una estimación), el estado pasa mostrarse como activo y listo para usarse: 

    ![Instancias de SAP CAL](./media/cal-ides-erp6-ehp7-sp3-sql/ides-pic12a.png)

4. Para buscar el grupo de recursos y todos los objetos que SAP CAL ha creado, vaya Azure Portal. Se puede ver que la máquina virtual se inicia con el mismo nombre de instancia que se proporcionó en SAP CAL.

    ![Objetos del grupo de recursos](./media/cal-ides-erp6-ehp7-sp3-sql/ides_resource_group.PNG)

5. En el portal de SAP CAL, vaya a las instancias implementadas y haga clic en **Connect** (Conectar). Aparece la siguiente ventana emergente: 

    ![Conectarse a la instancia](./media/cal-ides-erp6-ehp7-sp3-sql/ides-pic14a.PNG)

6. Para poder usar una de las opciones para conectarse a los sistemas implementados, haga clic en **Guía de introducción**. La documentación asigna los nombres de los usuarios en cada uno de los métodos de conectividad. Las contraseñas de dichos usuarios se establecen en la contraseña maestra que se definió al comienzo del proceso de implementación. En la documentación, otros usuarios más funcionales se enumeran junto con sus contraseñas, que se pueden usar para iniciar sesión en el sistema implementado.

    ![Documentación de bienvenida de SAP](./media/cal-ides-erp6-ehp7-sp3-sql/ides-pic15.jpg)

En unas pocas horas, un sistema SAP IDES en buen estado se implementa en Azure.

Si ha adquirido una suscripción a SAP CAL, SAP admite totalmente implementaciones a través de SAP CAL en Azure. La cola de compatibilidad es BC-VCM-CAL.

