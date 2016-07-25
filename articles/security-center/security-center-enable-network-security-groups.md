<properties
   pageTitle="Habilitación de grupos de seguridad de red en Azure Security Center | Microsoft Azure"
   description="En este documento, mostramos cómo implementar la recomendación de Azure Security Center **Habilitar los grupos de seguridad de red**."
   services="security-center"
   documentationCenter="na"
   authors="TerryLanfear"
   manager="MBaldwin"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/11/2016"
   ms.author="terrylan"/>

# Habilitación de los grupos de seguridad de red en Azure Security Center

Azure Security Center recomienda habilitar un grupo de seguridad de red (NSG) si no hay ninguno habilitado. Los NSG contienen una lista de reglas de la lista de control de acceso (ACL) que permiten o deniegan el tráfico de red a sus instancias de máquina virtual en una red virtual. Los NSG se pueden asociar con las subredes o las instancias individuales de máquina virtual dentro de esa subred. Cuando un NSG está asociado a una subred, las reglas de ACL se aplican a todas las instancias de VM de esa subred. Además, se puede restringir más el tráfico a una máquina virtual individual asociando un NSG directamente a esa máquina virtual. Para más información, consulte [¿Qué es un grupo de seguridad de red?](../virtual-network/virtual-networks-nsg.md).

Si no tiene habilitados los NSG, Security Center presentará dos recomendaciones: habilitar grupos de seguridad de red en subredes y habilitar grupos de seguridad de red de máquinas virtuales. Elija el nivel, subred o máquina virtual donde aplicar el NSG.


> [AZURE.NOTE] La información de este documento se aplica a la versión preliminar del Centro de seguridad de Azure. En este documento se presenta el servicio mediante una implementación de ejemplo. No se trata de una guía paso a paso.

## Implementación de la recomendación

1. En la hoja **Recomendaciones**, seleccione **Habilitar los grupos de seguridad de red** en subredes o en máquinas virtuales. ![Habilitar los grupos de seguridad de red][1]

2. Se abrirá la hoja **Configurar los grupos de seguridad de red que faltan** para las subredes o para las máquinas virtuales, según la recomendación que seleccionó. Seleccione una subred o una máquina virtual en donde configurar el NSG.

  ![Configurar NSG para subred][2]

  ![Configurar NSG para máquina virtual][3]
3. En la hoja **Elegir grupo de seguridad de red** seleccione un NSG existente o seleccione la opción de crear uno nuevo.

  ![Elegir grupo de seguridad de red][4]

Si crea un nuevo NSG, siga los pasos de [Cómo administrar grupos de seguridad de red con el Portal de Azure](../virtual-network/virtual-networks-create-nsg-arm-pportal.md) para crear un NSG y establecer las reglas de seguridad.

## Pasos siguientes

En este artículo se ha mostrado cómo implementar la recomendación de Security Center "Habilitar los grupos de seguridad de red" para las subredes o las máquinas virtuales. Para más información sobre la habilitación de NSG, consulte los siguientes recursos:

- [¿Qué es un grupo de seguridad de red?](../virtual-network/virtual-networks-nsg.md)
- [Cómo administrar grupos de seguridad de red con el Portal de Azure](../virtual-network/virtual-networks-create-nsg-arm-pportal.md)

Para más información sobre el Centro de seguridad, consulte los siguientes recursos:

- [Establecimiento de directivas de seguridad en Azure Security Center](security-center-policies.md): aprenda a configurar directivas de seguridad para las suscripciones y los grupos de recursos de Azure.
- [Administración de recomendaciones de seguridad en Azure Security Center](security-center-recommendations.md): recomendaciones que le ayudan a proteger los recursos de Azure.
- [Supervisión del estado de seguridad en Azure Security Center](security-center-monitoring.md): obtenga información sobre cómo supervisar el mantenimiento de los recursos de Azure.
- [Administración y respuesta a las alertas de seguridad en Azure Security Center](security-center-managing-and-responding-alerts.md): obtenga información sobre cómo administrar y responder a alertas de seguridad.
- [Supervisión de las soluciones de asociados con Azure Security Center](security-center-partner-solutions.md): aprenda a supervisar el estado de mantenimiento de las soluciones de asociados.
- [Preguntas más frecuentes sobre Azure Security Center](security-center-faq.md): busque las preguntas más frecuentes sobre cómo usar el servicio.
- [Blog de seguridad de Azure](http://blogs.msdn.com/b/azuresecurity/): obtenga las últimas noticias e información sobre la seguridad en Azure.

<!--Image references-->
[1]: ./media/security-center-enable-nsg/enable-nsg.png
[2]: ./media/security-center-enable-nsg/configure-nsg-for-subnet.png
[3]: ./media/security-center-enable-nsg/configure-nsg-for-vm.png
[4]: ./media/security-center-enable-nsg/choose-nsg.png

<!---HONumber=AcomDC_0713_2016-->