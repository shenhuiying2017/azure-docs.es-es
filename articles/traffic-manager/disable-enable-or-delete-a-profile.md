---
title: Deshabilitación, habilitación o eliminación de un perfil del Administrador de tráfico | Microsoft Docs
description: Este artículo le ayudará a trabajar con sus perfiles del Administrador de tráfico.
services: traffic-manager
documentationcenter: na
author: sdwheeler
manager: carmonm
editor: tysonn

ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/10/2016
ms.author: sewhee

---
# Deshabilitación, habilitación o eliminación de un perfil
Puede deshabilitar un perfil de Administrador de tráfico existente de para que no envíe solicitudes de usuario a sus extremos configurados. Cuando deshabilite un perfil de Administrador de tráfico, dicho perfil y la información que contiene se mantendrán intactos y pueden editarse en la interfaz del Administrador de tráfico. Si desea volver a habilitar el perfil, puede hacerlo fácilmente en el portal de Azure y los envíos se reanudarán. Cuando se crea un perfil del Administrador de tráfico en el portal de Azure, este se habilita automáticamente.

## Para deshabilitar un perfil
1. Modifique el registro de recursos DNS en el servidor DNS de Internet para usar el tipo de registro adecuado y un puntero a otro nombre o a la dirección IP de una ubicación específica en Internet. En otras palabras, debe cambiar el registro de recursos DNS del servidor DNS de Internet para que ya no utilice un registro de recursos CNAME que señale al nombre de dominio del perfil del Administrador de tráfico.
2. El tráfico dejará de dirigirse a los extremos a través de la configuración del perfil del Administrador de tráfico.
3. Seleccione el perfil que desea deshabilitar. Para seleccionar el perfil, en la página Administrador de tráfico, resalte el perfil haciendo clic en la columna situada junto al nombre del perfil. No haga clic en el nombre del perfil ni en la flecha que aparece junto al nombre, ya que esto le llevará a la página de configuración del perfil.
4. Después de seleccionar el perfil, haga clic en Deshabilitar en la parte inferior de la página.

## Para habilitar un perfil
1. Seleccione el perfil que desea habilitar. Para seleccionar el perfil, en la página Administrador de tráfico, resalte el perfil haciendo clic en la columna situada junto al nombre del perfil. No haga clic en el nombre del perfil ni en la flecha que aparece junto al nombre, ya que esto le llevará a la página de configuración del perfil.
2. Después de seleccionar el perfil, haga clic en Habilitar en la parte inferior de la página.
3. Modifique el registro de recursos DNS del servidor DNS de Internet para que use el tipo de registro CNAME, que asigna el nombre de dominio de la empresa al nombre de dominio del perfil del Administrador de tráfico. Para obtener más información, consulte [Hacer que un dominio de Internet de la compañía apunte a un dominio del Administrador de tráfico](traffic-manager-point-internet-domain.md).
4. El tráfico comenzará a dirigirse de nuevo a los extremos.

## Eliminación de un perfil
1. Asegúrese de que el registro de recursos DNS del servidor DNS de Internet ya no usa un registro de recursos CNAME que señale al nombre de dominio del perfil del Administrador de tráfico.
2. Seleccione el perfil que desea eliminar. Para seleccionar el perfil, en la página Administrador de tráfico, resalte el perfil
3. haciendo clic en la columna situada junto al perfil. No haga clic en el nombre del perfil ni en la flecha que aparece junto al nombre, ya que esto le llevará a la página de configuración del perfil.
4. Después de seleccionar el perfil, haga clic en Eliminar en la parte inferior de la página.

## Pasos siguientes
[Administrador de tráfico: deshabilitación o habilitación de un extremo](disable-or-enable-an-endpoint.md)

[Configuración del método de enrutamiento de conmutación por error](traffic-manager-configure-failover-routing-method.md)

[Configuración del método de enrutamiento round robin](traffic-manager-configure-round-robin-routing-method.md)

[Configuración del método de enrutamiento de rendimiento](traffic-manager-configure-performance-routing-method.md)

[Solución de problemas de estado degradado del Administrador de tráfico](traffic-manager-troubleshooting-degraded.md)

<!---HONumber=AcomDC_0824_2016-->