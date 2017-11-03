---
title: "Recomendaciones de seguridad para imágenes de Azure Marketplace | Microsoft Docs"
description: "En este artículo se proporcionan recomendaciones para las imágenes incluidas en Marketplace."
services: security
documentationcenter: na
author: barclayn
manager: MBaldwin
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.date: 10/18/2017
ms.author: barclayn
ms.openlocfilehash: 4ae36f87c29975c82bb99f713893a9dc78a249e6
ms.sourcegitcommit: d6ad3203ecc54ab267f40649d3903584ac4db60b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/19/2017
---
# <a name="security-recommendations-for-azure-marketplace-images"></a>Recomendaciones de seguridad para imágenes de Azure Marketplace

Se recomienda que cada solución cumpla con las siguientes recomendaciones de configuración de seguridad. Esto le ayudará a mantener un alto nivel de seguridad para las imágenes de la solución del asociado de Azure Marketplace.

Estas recomendaciones también pueden ser útiles para las organizaciones que no tienen imágenes en Azure Marketplace. Puede que quiera comprobar las configuraciones de imágenes de Windows y Linux de la empresa frente a las instrucciones que se encuentran en las tablas siguientes.

## <a name="open-source-based-images"></a>Abrir imágenes basadas en origen

|||
|--------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Categoría**                                                 | **Comprobación**                                                                                                                                                                                                                                                                              |
| Seguridad                                                     | Se instalan todas las revisiones de seguridad más recientes de la distribución de Linux.                                                                                                                                                                                                              |
| Seguridad                                                     | Se siguieron las directrices del sector para proteger la imagen de la máquina virtual para la distribución de Linux específica.                                                                                                                                                                                     |
| Seguridad                                                     | Limitar la superficie expuesta a ataques al mantener una superficie mínima con los roles, las características, los servicios y los puertos de red de Windows Server necesarios.                                                                                                                                               |
| Seguridad                                                     | Examinar el código fuente y la imagen de máquina virtual resultante en busca de malware.                                                                                                                                                                                                                                   |
| Seguridad                                                     | La imagen de disco duro virtual solo incluye las cuentas bloqueadas necesarias, que no tienen las contraseñas predeterminadas que permitirían un inicio de sesión interactivo; no hay puertas traseras.                                                                                                                                           |
| Seguridad                                                     | Las reglas de Firewall están deshabilitadas, a menos que la aplicación se base funcionalmente en ellas, como un dispositivo de firewall.                                                                                                                                                                             |
| Seguridad                                                     | Toda la información confidencial se quitó de la imagen de disco duro virtual, como las claves SSH de prueba, los archivo de hosts conocidos, los archivos de registro y los certificados innecesarios.                                                                                                                                       |
| Seguridad                                                     | Se recomienda no usar LVM.                                                                                                                                                                                                                                            |
| Seguridad                                                     | Deben incluirse versiones más recientes de las bibliotecas necesarias: </br> - OpenSSL v1.0 o posterior. </br> - Python 2.5 o posterior (se recomienda Python 2.6+). </br> - Paquete pyans1 de Python, si no está ya instalado. </br> - d.OpenSSL v 1.0 o posterior.                                                                |
| Seguridad                                                     | Deben borrarse las entradas del historial de Shell/Bash.                                                                                                                                                                                                                                             |
| Redes                                                   | De forma predeterminada, se debe incluir un servidor SSH. Establezca la conexión persistente de SSH en la configuración sshd con la siguiente opción: ClientAliveInterval 180.                                                                                                                                                        |
| Redes                                                   | La imagen no debe contener ninguna configuración de red personalizada. Elimine resolv.conf: `rm /etc/resolv.conf`                                                                                                                                                                                |
| Implementación                                                   | Se debe instalar el agente Linux de Azure más reciente. </br> -  El agente debe instalarse mediante el paquete RPM o Deb.  </br> -  También puede usar el proceso de instalación manual, pero se recomienda y se prefiere que se usen los paquetes del instalador. </br> -  Si instala el agente manualmente desde el repositorio de GitHub, copie primero el archivo `waagent` en `/usr/sbin` y ejecute (como raíz): </br>`# chmod 755 /usr/sbin/waagent` </br>`# /usr/sbin/waagent -install` </br>El archivo de configuración del agente se colocará en `/etc/waagent.conf`.    |
| Implementación                                                   | Garantiza que el soporte técnico de Azure puede proporcionar a nuestros asociados la salida de la consola de serie cuando sea necesario y proporcionar el tiempo de espera adecuado para el montaje del disco del sistema operativo desde el almacenamiento en la nube. La imagen debe haber agregado los parámetros siguientes a la línea de arranque de kernel: `console=ttyS0 earlyprintk=ttyS0 rootdelay=300` |
| Implementación                                                   | No hay ninguna partición de intercambio en el disco del SO. El agente de Linux puede solicitar el intercambio para la creación en el disco del recurso local.         |
| Implementación                                                   | Se recomienda que se cree una partición de raíz única para el disco del sistema operativo.      |
| Implementación                                                   | Solo el sistema operativo de 64 bits.                                                                                                                                                                                                                                                          |

## <a name="windows-server-based-images"></a>Imágenes basadas en Windows Server

|||
|-------------| -------------------------|
| **Categoría**                                                     | **Comprobación**                                                                                                                                                                |
| Seguridad                                                         | Usar una imagen base de sistema operativo segura. El disco duro virtual que se usa para el origen de cualquier imagen basada en Windows Server debe pertenecer a las imágenes del sistema operativo de Windows Server que se proporcionan a través de Microsoft Azure. |
| Seguridad                                                         | Instalar todas las actualizaciones de seguridad.                                                                                                                                     |
| Seguridad                                                         | Las aplicaciones no deben depender de nombres de usuario con permisos restringidos, como administrador y raíz.                                                                |
| Seguridad                                                         | No se admite el Cifrado de unidad BitLocker en el disco duro del sistema operativo. BitLocker puede usarse en discos de datos.                                                            |
| Seguridad                                                         | Limitar la superficie expuesta a ataques al mantener una superficie mínima habilitando solo los roles, las características, los servicios y los puertos de red de Windows Server necesarios.                         |
| Seguridad                                                         | Examinar el código fuente y la imagen de máquina virtual resultante en busca de malware.                                                                                                                     |
| Seguridad                                                         | Establecer la actualización de seguridad de las imágenes de Windows Server para que se actualicen automáticamente.                                                                                                                |
| Seguridad                                                         | La imagen de disco duro virtual solo incluye las cuentas bloqueadas necesarias, que no tienen las contraseñas predeterminadas que permitirían un inicio de sesión interactivo; no hay puertas traseras.                             |
| Seguridad                                                         | Las reglas de Firewall están deshabilitadas, a menos que la aplicación se base funcionalmente en ellas, como un dispositivo de firewall.                                                               |
| Seguridad                                                         | Toda la información confidencial se eliminó de la imagen de disco duro virtual. Por ejemplo, se deben eliminar los archivos de registros, el archivo de HOSTS y los certificados innecesarios.                                              |
| Implementación                                                       | Solo el sistema operativo de 64 bits.                            |
