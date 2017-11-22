---
title: Requisitos del sistema de la matriz virtual de Microsoft Azure StorSimple | Microsoft Docs
description: "Obtenga más información sobre los requisitos de software y red de la matriz virtual de StorSimple"
services: storsimple
documentationcenter: NA
author: alkohli
manager: jeconnoc
editor: 
ms.assetid: ea1d3bca-e71b-453d-aa82-440d2638f5e3
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/14/2017
ms.author: alkohli
ms.openlocfilehash: 5d01523f326bd7e2518bff06e62ae62db8f318d3
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/15/2017
---
# <a name="storsimple-virtual-array-system-requirements"></a>Requisitos del sistema de la matriz virtual de StorSimple
## <a name="overview"></a>Información general
En este artículo se describen los requisitos del sistema importantes de la matriz virtual de Microsoft Azure StorSimple y de los clientes de almacenamiento que acceden a dicha matriz. Es recomendable que revise cuidadosamente la siguiente información antes de implementar el sistema StorSimple y que luego la consulte según sea necesario durante la implementación y el funcionamiento posterior.

Los requisitos del sistema incluyen:

* **Requisitos de software para clientes de almacenamiento** : describe las plataformas de virtualización compatibles, los exploradores web, los iniciadores iSCSI, los clientes SMB, los requisitos mínimos del dispositivo virtual y cualquier otro requisito adicional de esos sistemas operativos.
* **Requisitos de red para el dispositivo StorSimple** : proporciona información acerca de los puertos que deben estar abiertos en el firewall para permitir el tráfico iSCSI, de nube o de administración.

La información acerca de los requisitos de sistema de StorSimple publicada en este artículo solo se aplica a las matrices virtuales de StorSimple.

* En cuanto a los dispositivos de la serie 8000, consulte [Requisitos de sistema de los dispositivos de la serie 8000 de StorSimple](storsimple-system-requirements.md).
* Para los dispositivos de la serie 7000, consulte [Requisitos de sistema de los dispositivos de la serie 5000-7000 de StorSimple](http://onlinehelp.storsimple.com/1_StorSimple_System_Requirements).

## <a name="software-requirements"></a>Requisitos de software
Los requisitos de software incluyen información sobre los exploradores web compatibles, las versiones SMB, las plataformas de virtualización y los requisitos mínimos de los dispositivos virtuales.

### <a name="supported-virtualization-platforms"></a>Plataformas de virtualización admitidas
| **Hipervisor** | **Versión** |
| --- | --- |
| Hyper-V |Windows Server 2008 R2 SP1 y posterior |
| VMware ESXi |5.0, 5.5 y 6.0 <br> (6.5 no es compatible). |

> [!IMPORTANT]
> No instale las herramientas de VMware en StorSimple Virtual Array; de lo contrario, esta configuración será incompatible.

### <a name="virtual-device-requirements"></a>Requisitos de los dispositivos virtuales
| **Componente** | **Requisito** |
| --- | --- |
| Número mínimo de procesadores virtuales (núcleos) |4 |
| Cantidad mínima de memoria (RAM) |8 GB <br> Para un servidor de archivos, 8 GB para menos de 2 millones de archivos y 16 GB para 2-4 millones de archivos|
| Espacio en disco<sup>1</sup> |Disco de sistema operativo: 80 GB  <br></br>Disco de datos: de 500 GB a 8 TB |
| Número mínimo de interfaces de red |1 |
| Ancho de banda de Internet<sup>2</sup> |Ancho de banda mínimo requerido: 5 Mbps <br> Ancho de banda recomendado: 100 Mbps <br> La velocidad de transferencia de datos se amplía con el ancho de banda de Internet. Por ejemplo, 100 GB de datos tardan dos días en transferirse a 5 Mbps, lo que generaría errores de copia de seguridad por el hecho de que las copias de seguridad no se completarían en un día. Con un ancho de banda de 100 Mbps, 100 GB de datos pueden transferirse en dos horas y media.   |

<sup>1</sup> : con aprovisionamiento fino

<sup>2</sup>: los requisitos de red pueden variar según la tasa de cambio diaria de datos. Por ejemplo, si un dispositivo necesita realizar copias de seguridad de 10 GB o más de los cambios realizados durante un día, la copia de seguridad diaria que se lleva a cabo a través de una conexión a 5 Mbps se puede demorar hasta 4,25 horas (eso si no se pudieran comprimir o desduplicar los datos).

### <a name="supported-web-browsers"></a>Exploradores web compatibles
| **Componente** | **Versión** | **Requisitos/notas adicionales** |
| --- | --- | --- |
| Microsoft Edge |La versión más reciente | |
| Internet Explorer |La versión más reciente |Probado con Internet Explorer 11 |
| Google Chrome |La versión más reciente |Probado con Chrome 46 |

### <a name="supported-storage-clients"></a>Clientes de almacenamiento compatibles
Los siguientes requisitos de software son para los iniciadores de iSCSI con acceso a su matriz virtual de StorSimple (configurada como servidor iSCSI).

| **Sistemas operativos compatibles** | **Versión requerida** | **Requisitos/notas adicionales** |
| --- | --- | --- |
| Windows Server |2008R2 SP1, 2012, 2012R2 |StorSimple puede crear volúmenes con aprovisionamiento fino y totalmente aprovisionados. No puede crear volúmenes aprovisionados parcialmente. Solo se admiten volúmenes de iSCSI de StorSimple para:  <ul><li>Volúmenes simples en discos básicos de Windows.</li><li>NTFS de Windows para dar formato a un volumen.</li> |

Los siguientes requisitos de software son para los iniciadores de SMB con acceso a su matriz virtual de StorSimple (configurada como servidor de archivos).

| **Versión de SMB** |
| --- |
| SMB 2.x |
| SMB 3.0 |
| SMB 3.02 |

> [!IMPORTANT]
> No copie ni almacene archivos protegidos por el Sistema de archivos de cifrado de Windows (EFS) en el servidor de archivos de la matriz virtual de StorSimple; esto provocará una configuración no admitida.


### <a name="supported-storage-format"></a>Formato de almacenamiento compatible.
Se admite solo Blob Storage en bloques de Azure. No se admiten los blobs en páginas. Más información sobre [blobs en bloques y blobs en páginas](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs).

## <a name="networking-requirements"></a>Requisitos de red
La siguiente tabla enumera los puertos que deben abrirse en el firewall para permitir el tráfico de administración, de nube, de SMB o de iSCSI. En esta tabla, *dentro* o *entrante* hace referencia a la dirección desde la que el cliente entrante solicita acceso al dispositivo. *Fuera* o *saliente* hace referencia a la dirección en la que el dispositivo StorSimple envía datos externamente, más allá de la implementación: por ejemplo, saliente a Internet.

| **Nº de puerto<sup>1</sup>** | **Dentro o fuera** | **Ámbito de puerto** | **Obligatorio** | **Notas** |
| --- | --- | --- | --- | --- |
| TCP 80 (HTTP) |Fuera |WAN |No |El puerto de salida se usa para obtener acceso a Internet para así recuperar las actualizaciones. <br></br>El usuario puede configurar el proxy web de salida. |
| TCP 443 (HTTPS) |Fuera |WAN |Sí |El puerto de salida se usa para tener acceso a los datos en la nube. <br></br>El usuario puede configurar el proxy web de salida. |
| UDP 53 (DNS) |Fuera |WAN |En algunos casos; consulte las notas. |Este puerto es necesario solo si está utilizando un servidor DNS basado en Internet. <br></br> Tenga en cuenta que si implementa un servidor de archivos, le recomendamos que use el servidor DNS local. |
| UDP 123 (NTP) |Fuera |WAN |En algunos casos; consulte las notas. |Este puerto solo es necesario si está utilizando un servidor DNS basado en Internet.<br></br> Tenga en cuenta que si implementa un servidor de archivos, le recomendamos que sincronice la hora con los controladores de dominio de Active Directory. |
| TCP 80 (HTTP) |En el |LAN |Sí |Este es el puerto de entrada de la interfaz de usuario local para el dispositivo StorSimple de la administración local. <br></br> Tenga en cuenta que si obtiene acceso a la interfaz de usuario local mediante HTTP, se le redireccionará automáticamente a HTTPS. |
| TCP 443 (HTTPS) |En el |LAN |Sí |Este es el puerto de entrada de la interfaz de usuario local para el dispositivo StorSimple de la administración local. |
| TCP 3260 (iSCSI) |En el |LAN |No |Este puerto se utiliza para tener acceso a datos a través de iSCSI. |

<sup>1</sup> Ningún puerto de entrada debe estar abierto en la red Internet pública.

> [!IMPORTANT]
> Asegúrese de que el firewall no modifica ni descifra ningún tráfico SSL entre el dispositivo de StorSimple y Azure.
> 
> 

### <a name="url-patterns-for-firewall-rules"></a>Patrones de URL para reglas de firewall
Con frecuencia, los administradores de red pueden configurar reglas avanzadas de firewall de acuerdo con los patrones de URL para filtrar el tráfico saliente y entrante. La matriz virtual y el servicio de administrador de dispositivos de StorSimple dependen de otras aplicaciones de Microsoft, como Azure Service Bus, el servicio de control de acceso de Microsoft Azure Active Directory, las cuentas de almacenamiento y los servidores de Microsoft Update. Es posible usar los patrones de URL asociados a estas aplicaciones para configurar las reglas de firewall. Es importante entender que los patrones de URL asociados a estas aplicaciones pueden cambiar. Esta realidad, a su vez, requiere que el administrador de red supervise y actualice las reglas de firewall de su StorSimple de forma pertinente y oportuna. 

Se recomienda que establezca las reglas de firewall para el tráfico saliente, basándose en las direcciones IP fijas de StorSimple, de forma generosa en la mayoría de los casos. Sin embargo, puede utilizar la información siguiente con el objetivo de establecer las reglas avanzadas de firewall que se necesitan para crear entornos seguros.

> [!NOTE]
> 
> * Las direcciones IP del dispositivo (origen) siempre se deben establecer en todas las interfaces de red habilitadas para la nube. 
> * Las IP de destino, por su parte, se deben establecer en los [intervalos de direcciones IP del centro de datos de Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653).
> 
> 

| Patrón de URL | Componente o funcionalidad |
| --- | --- |
| `https://*.storsimple.windowsazure.com/*`<br>`https://*.accesscontrol.windows.net/*`<br>`https://*.servicebus.windows.net/*` <br>`https://login.windows.net`|Servicio de administrador de dispositivos de StorSimple<br>Access Control Service<br>Azure Service Bus<br>Servicio de autenticación|
| `http://*.backup.windowsazure.com` |Registro de dispositivos |
| `http://crl.microsoft.com/pki/*`<br>`http://www.microsoft.com/pki/*` |Revocación de certificados |
| `https://*.core.windows.net/*`<br>`https://*.data.microsoft.com`<br>`http://*.msftncsi.com` |Supervisión y cuentas de Almacenamiento de Azure |
| `http://*.windowsupdate.microsoft.com`<br>`https://*.windowsupdate.microsoft.com`<br>`http://*.update.microsoft.com`<br> `https://*.update.microsoft.com`<br>`http://*.windowsupdate.com`<br>`http://download.microsoft.com`<br>`http://wustat.windows.com`<br>`http://ntservicepack.microsoft.com` |Servidores de Microsoft Update<br> |
| `http://*.deploy.akamaitechnologies.com` |CDN de Akamai |
| `https://*.partners.extranet.microsoft.com/*` |Paquete de soporte |
| `http://*.data.microsoft.com ` |Para información sobre el servicio de telemetría en Windows, consulte la [actualización para la experiencia del usuario y la telemetría de diagnóstico](https://support.microsoft.com/en-us/kb/3068708). |

## <a name="next-steps"></a>Pasos siguientes
* [Prepare el portal para implementar la matriz virtual de StorSimple](storsimple-virtual-array-deploy1-portal-prep.md)
