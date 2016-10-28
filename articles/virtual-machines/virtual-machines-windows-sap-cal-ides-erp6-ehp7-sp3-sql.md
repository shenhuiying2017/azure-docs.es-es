<properties 
pageTitle="Implementación de SAP IDE EHP7 SP3 para SAP ERP 6.0 en Microsoft Azure | Microsoft Azure" 
description="Implementación de SAP IDE EHP7 SP3 para SAP ERP 6.0 en Microsoft Azure" 
services="virtual-machines-windows" 
documentationCenter="" 
authors="hermanndms" 
manager="timlt" 
editor="" 
tags="azure-resource-manager" 
keywords=""/>
<tags  
ms.service="virtual-machines-windows" 
ms.devlang="na" 
ms.topic="article" 
ms.tgt_pltfrm="vm-windows" 
ms.workload="infrastructure-services" 
ms.date="09/16/2016" 
ms.author="hermannd"/>


# Implementación de SAP IDE EHP7 SP3 para SAP ERP 6.0 en Microsoft Azure 

En este artículo se describe cómo implementar SAP IDES con SQL Server y el SO Windows en Microsoft Azure por medio de SAP Cloud Appliance Library 3.0. Las capturas de pantalla muestran el proceso paso a paso. La implementación de otras soluciones de la lista funciona del mismo modo desde la perspectiva del proceso. Solo se tiene que seleccionar una solución diferente.

Para empezar con SAP Cloud Appliance Library (SAP CAL), vaya [aquí](https://cal.sap.com/). Hay un blog de SAP sobre la nueva versión de [SAP Cloud Appliance Library 3.0](http://scn.sap.com/community/cloud-appliance-library/blog/2016/05/27/sap-cloud-appliance-library-30-came-with-a-new-user-experience).


Las capturas de pantalla siguientes muestran paso a paso cómo implementar SAP IDES en Microsoft Azure. El proceso funciona del mismo modo para otras soluciones.


![](./media/virtual-machines-windows-sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic1.jpg)

La primera imagen muestra todas las soluciones que están disponibles en Microsoft Azure. Se eligió la solución SAP IDES basada en Windows que solo está disponible en Azure para demostrar el proceso.

![](./media/virtual-machines-windows-sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic2.jpg)

En primer lugar, debe crear una cuenta de SAP CAL. Actualmente hay dos opciones para Azure: Azure estándar y Azure para China continental, operado por el asociado 21Vianet.

![](./media/virtual-machines-windows-sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic3.jpg)

A continuación, debe escribir el identificador de suscripción de Azure que puede encontrarse en el Portal de Azure (consulte más abajo cómo obtenerlo). Después, debe descargar un certificado de administración de Azure.

![](./media/virtual-machines-windows-sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic6.jpg)

En el nuevo portal de Azure, busque el elemento "Suscripciones" en el lado izquierdo. Haga clic en él para mostrar todas las suscripciones activas para el usuario.

![](./media/virtual-machines-windows-sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic7.jpg)

Si selecciona una de las suscripciones y después "Certificados de administración", verá una explicación sobre el concepto nuevo de "entidades de servicio" usado para el nuevo modelo de Azure Resource Manager. SAP CAL aún no se ha adaptado a este nuevo modelo y sigue requiriendo el modelo "clásico" y el Portal de Azure anterior para trabajar con certificados de administración.

![](./media/virtual-machines-windows-sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic4.jpg)

Aquí se ve el Portal de Azure anterior. Al cargarse un certificado de administración, se conceden a SAP CAL los permisos para crear máquinas virtuales dentro de una suscripción de cliente. En la pestaña "SUSCRIPCIONES", se encuentra el identificador de suscripción que debe especificar en el portal de SAP CAL.

![](./media/virtual-machines-windows-sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic5.jpg)

En la segunda pestaña, es posible cargar el certificado de administración que se descargó antes de SAP CAL.

![](./media/virtual-machines-windows-sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic8.jpg)

Aparece un pequeño cuadro de diálogo para seleccionar el archivo de certificado descargado.

![](./media/virtual-machines-windows-sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic9.jpg)

Una vez que se cargue el certificado, se puede probar la conexión entre SAP CAL y la suscripción de Azure del cliente dentro de SAP CAL. Debería aparecer un pequeño mensaje que indica que la conexión es válida.

![](./media/virtual-machines-windows-sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic10.jpg)

Después de configurar una cuenta, debe seleccionar una solución que vaya a implementarse y crear una instancia. Con el modo "básico", es realmente sencillo. Escriba un nombre de instancia, elija una región de Azure y defina la contraseña maestra para la solución.

![](./media/virtual-machines-windows-sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic11.jpg)

Después de un tiempo –cuánto depende del tamaño y la complejidad de la solución (SAP CAL ofrece una estimación)–, se muestra como "activa" y está lista para usarse. Es muy sencillo.

![](./media/virtual-machines-windows-sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic12.jpg)

Mirando algunos detalles de la solución, se ve qué tipo de máquinas virtuales se implementaron. En este caso, hay una única máquina virtual de Azure de tamaño D12 creada mediante SAP CAL.

![](./media/virtual-machines-windows-sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic13.jpg)

En Azure Portal, las máquinas virtuales se encuentran empezando por el mismo nombre de instancia que se proporcionó en SAP CAL.

![](./media/virtual-machines-windows-sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic14.jpg)

Ahora es posible conectarse a la solución mediante el botón correspondiente en el portal de SAP CAL. El pequeño cuadro de diálogo contiene un vínculo a una guía de usuario donde se describen todas las credenciales predeterminadas para trabajar con la solución. [Aquí](https://caldocs.hana.ondemand.com/caldocs/help/Getting_Started_Guide_IDES607MSSQL.pdf) se encuentra el vínculo a la guía de la solución IDES.

![](./media/virtual-machines-windows-sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic15.jpg)

Otra opción consiste en iniciar sesión en la máquina virtual Windows e iniciar, por ejemplo, la instancia de SAP GUI configurada previamente.

<!---HONumber=AcomDC_0921_2016-->