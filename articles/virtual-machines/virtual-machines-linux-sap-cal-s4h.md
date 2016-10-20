<properties 
pageTitle="Implementación de S/4 HANA o BW/4 HANA en una máquina virtual de Azure | Microsoft Azure" 
description="Implementación de S/4 HANA o BW/4 HANA en una máquina virtual de Azure" 
services="virtual-machines-linux" 
documentationCenter="" 
authors="hermanndms" 
manager="timlt" 
editor="" 
tags="azure-resource-manager" 
  keywords=""/>
<tags  
  ms.service="virtual-machines-linux" 
  ms.devlang="na" 
  ms.topic="article" 
  ms.tgt_pltfrm="vm-linux" 
  ms.workload="infrastructure-services" 
  ms.date="09/15/2016" 
  ms.author="hermannd"/>


# Implementación de S/4 Hana o BW/4 Hana en Microsoft Azure 

En este artículo se describe cómo implementar S/4 Hana en Microsoft Azure por medio de SAP Cloud Appliance Library 3.0. Las capturas de pantalla muestran el proceso paso a paso. La implementación de otras soluciones basadas en SAP Hana, como BW/4 Hana, funciona del mismo modo desde la perspectiva del proceso. Solo se tiene que seleccionar una solución diferente.

Para empezar con SAP Cloud Appliance Library (SAP CAL), vaya [aquí](https://cal.sap.com/). Hay un blog de SAP sobre la nueva versión de [SAP Cloud Appliance Library 3.0](http://scn.sap.com/community/cloud-appliance-library/blog/2016/05/27/sap-cloud-appliance-library-30-came-with-a-new-user-experience).


Las capturas de pantalla siguientes muestran paso a paso cómo implementar S/4 Hana en Microsoft Azure. El proceso funciona del mismo modo para otras soluciones como BW/4 Hana.


![](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic-1b.jpg)

La primera imagen muestra todas las soluciones basadas en SAP CAL Hana que están disponibles en Microsoft Azure. Como ejemplo, se eligió "SAP S/4 HANA on-premises edition" (solución en la parte inferior de la captura de pantalla) para llevar a cabo el proceso.

![](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic-2.jpg)

En primer lugar, debe crear una cuenta de SAP CAL. Actualmente hay dos opciones para Azure: Azure estándar y Azure para China continental, operado por el asociado 21Vianet.

![](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic3b.jpg)

A continuación, debe escribir el identificador de suscripción de Azure que puede encontrarse en el Portal de Azure (consulte más abajo cómo obtenerlo). Después, debe descargar un certificado de administración de Azure.

![](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic6b.jpg)

En el nuevo portal de Azure, busque el elemento "Suscripciones" en el lado izquierdo. Haga clic en él para mostrar todas las suscripciones activas para el usuario.

![](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic7b.jpg)

Si selecciona una de las suscripciones y después "Certificados de administración", verá una explicación sobre el concepto nuevo de "entidades de servicio" usado para el nuevo modelo de Azure Resource Manager. SAP CAL aún no se ha adaptado a este nuevo modelo y sigue requiriendo el modelo "clásico" y el Portal de Azure anterior para trabajar con certificados de administración.

![](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic4b.jpg)

Aquí se ve el Portal de Azure anterior. Al cargarse un certificado de administración, se conceden a SAP CAL los permisos para crear máquinas virtuales dentro de una suscripción de cliente. En la pestaña "SUSCRIPCIONES", se encuentra el identificador de suscripción que debe especificar en el portal de SAP CAL.

![](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic5.jpg)

En la segunda pestaña, es posible cargar el certificado de administración que se descargó antes de SAP CAL.

![](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic8.jpg)

Aparece un pequeño cuadro de diálogo para seleccionar el archivo de certificado descargado.

![](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic9.jpg)

Una vez que se cargue el certificado, se puede probar la conexión entre SAP CAL y la suscripción de Azure del cliente dentro de SAP CAL. Debería aparecer un pequeño mensaje que indica que la conexión es válida.

![](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic10.jpg)

Después de configurar una cuenta, debe seleccionar una solución que vaya a implementarse y crear una instancia. Con el modo "básico", es realmente sencillo. Escriba un nombre de instancia, elija una región de Azure y defina la contraseña maestra para la solución.

![](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic11.jpg)

Después de un tiempo –cuánto depende del tamaño y la complejidad de la solución (SAP CAL ofrece una estimación)–, se muestra como "activa" y está lista para usarse. Es muy sencillo.

![](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic12.jpg)

Mirando algunos detalles de la solución, se ve qué tipo de máquinas virtuales se implementaron. En este caso, se crearon tres máquinas virtuales de Azure de diferentes tamaños y finalidades.

![](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic13.jpg)

En el Portal de Azure, las máquinas virtuales se encuentran empezando por el mismo nombre de instancia que se proporcionó en SAP CAL.

![](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic14b.jpg)

Ahora es posible conectarse a la solución mediante el botón correspondiente en el portal de SAP CAL. El pequeño cuadro de diálogo contiene un vínculo a una guía de usuario donde se describen todas las credenciales predeterminadas para trabajar con la solución.

![](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic15.jpg)

Otra opción consiste en iniciar sesión en la máquina virtual Windows cliente e iniciar, por ejemplo, la instancia de SAP GUI configurada previamente.

<!---HONumber=AcomDC_0928_2016-->