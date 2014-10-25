<properties title="Security offerings for Azure Virtual Machines" pageTitle="Security offerings for Azure Virtual Machines" description="Quick overview of key security features for Azure VMs and links to details" metaKeywords="" services="virtual machines" solutions="" documentationCenter="" authors="kathydav" videoId="" scriptId="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-multiple" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="kathydav"></tags>

# Ofertas de seguridad para máquinas virtuales de Azure

Las tareas de seguridad y la configuración para Máquinas virtuales de Azure son, en su mayoría, las mismas que para cualquier otra máquina virtual o equipo físico. Entre otras se incluyen tareas básicas como el uso de contraseñas seguras, la no utilización de nombres de usuario conocidos y el establecimiento de una directiva y programación para actualizar el sistema operativo y las aplicaciones. Además, le recomendamos las siguientes prácticas y le proporcionamos características que puede usar para implementarlas:

-   Ejecutar antivirus y antimalware

-   Usar listas de control de acceso (ACL) de red en extremos

## Ejecución de antivirus y antimalware

Azure ofrece varias opciones para soluciones antivirus y antimalware, pero la administración depende de usted. Por ejemplo, necesita decidir cuándo ejecutar exámenes e instalar actualizaciones. Puede agregar compatibilidad antivirus cuando cree la máquina virtual o posteriormente. Actualmente se ofrecen tres soluciones como extensiones de seguridad que puede instalar tanto en máquinas virtuales nuevas como existentes:

-   [Instalación y configuración de Endpoint Protection en una máquina virtual de Azure][Instalación y configuración de Endpoint Protection en una máquina virtual de Azure]
-   [Instalación y configuración de Trend Micro Deep Security como servicio en una máquina virtual de Azure][Instalación y configuración de Trend Micro Deep Security como servicio en una máquina virtual de Azure]
-   [Implementación de soluciones antimalware en Máquinas virtuales de Azure][Implementación de soluciones antimalware en Máquinas virtuales de Azure]

## Uso de listas de control de acceso (ACL) de red en extremos de máquina virtual

Las listas de control de acceso (ACL) de red ofrecen la posibilidad de permitir o denegar de forma selectiva el tráfico de entrada a un extremo de máquina virtual. Esta capacidad de filtro de paquetes proporciona un nivel adicional de seguridad. Para obtener detalles sobre cómo funciona esto y vínculos e instrucciones, consulte [Acerca de las listas de control de acceso (ACL) de red][Acerca de las listas de control de acceso (ACL) de red].

  [Instalación y configuración de Endpoint Protection en una máquina virtual de Azure]: http://go.microsoft.com/fwlink/p/?LinkId=404207
  [Instalación y configuración de Trend Micro Deep Security como servicio en una máquina virtual de Azure]: http://go.microsoft.com/fwlink/p/?LinkId=404206
  [Implementación de soluciones antimalware en Máquinas virtuales de Azure]: http://azure.microsoft.com/blog/2014/05/13/deploying-antimalware-solutions-on-azure-virtual-machines/
  [Acerca de las listas de control de acceso (ACL) de red]: http://go.microsoft.com/fwlink/?LinkId=506655
