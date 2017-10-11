---
title: "Implementación de un dispositivo de la serie StorSimple 8000 en el Portal de Government | Microsoft Docs"
description: Describe los pasos y procedimientos recomendados para implementar el dispositivo de la serie StorSimple 8000 con Update 3 o posterior y el servicio en el Portal de Azure Government.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/22/2017
ms.author: alkohli
ms.openlocfilehash: 5a622eb5ae14a6c6b0c2dd4eceb6ffdb9733dcff
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/29/2017
---
# <a name="deploy-your-on-premises-storsimple-device-in-the-government-portal"></a>Implementación del dispositivo StorSimple local en el Portal de Government

## <a name="overview"></a>Información general
Bienvenido a la implementación del dispositivo StorSimple de Microsoft Azure. Estos tutoriales de implementación se aplican a la serie StorSimple 8000 que ejecuta el software Update 3 o posterior en del Portal de Azure Government. En esta serie de tutoriales se describe se incluye una lista de comprobación de configuración, una lista los requisitos previos de configuración y los pasos de configuración detallados para su dispositivo StorSimple.

La información de estos tutoriales da por supuesto que revisó las precauciones de seguridad, y que desempaquetó, montó y cableó el dispositivo StorSimple. Si todavía necesita realizar dichas tareas, empiece por revisar las [precauciones de seguridad](storsimple-safety.md). Siga las instrucciones específicas del dispositivo para desempaquetar, montar en bastidor y cablear el dispositivo.

* [Desempaquetado, montaje en bastidor y cableado del 8100](storsimple-8100-hardware-installation.md)
* [Desempaquetado, montaje en bastidor y cableado del 8600](storsimple-8600-hardware-installation.md)

Necesitará privilegios de administrador para completar el proceso de instalación y configuración. Se recomienda que revise la lista de comprobación de configuración antes de comenzar. El proceso de implementación y configuración puede tardar algún tiempo en completarse.

> [!NOTE]
> La información de implementación de StorSimple publicada en el sitio web de Microsoft Azure se aplica solo a los dispositivos StorSimple de la serie 8000. Para obtener información completa sobre los dispositivos de la serie 7000, vaya a: [http://onlinehelp.storsimple.com/](http://onlinehelp.storsimple.com). Para obtener información sobre la implementación de la serie 7000, vea la [Guía de inicio rápido del sistema StorSimple](http://onlinehelp.storsimple.com/111_Appliance/).


## <a name="deployment-steps"></a>Pasos de implementación
Siga estos pasos obligatorios para configurar el dispositivo StorSimple y conectarlo al servicio Administrador de dispositivos de StorSimple. Además de los pasos obligatorios, hay pasos y procedimientos opcionales que puede que necesite completar durante la implementación. Las instrucciones detalladas de implementación indican cuándo debe realizar cada uno de estos pasos opcionales.

| Paso | Description |
| --- | --- |
| **REQUISITOS PREVIOS** |Se deben llevar a cabo como preparación para la próxima implementación. |
| [Lista de comprobación de la configuración de implementación](#deployment-configuration-checklist) |Use esta lista de comprobación para recopilar y registrar información antes y durante la implementación. |
| [Requisitos previos de implementación](#deployment-prerequisites) |Validan que el entorno está listo para la implementación. |
|  | |
| **IMPLEMENTACIÓN PASO A PASO** |Estos pasos son obligatorios para implementar el dispositivo StorSimple en producción. |
| [Paso 1: Crear un nuevo servicio](#step-1-create-a-new-service) |Configure la administración y el almacenamiento en la nube para el dispositivo StorSimple. *Omita este paso si tiene un servicio existente para otros dispositivos StorSimple*. |
| [Paso 2: Obtener la clave de registro del servicio](#step-2-get-the-service-registration-key) |Esta clave se usa para registrar y conectar el dispositivo StorSimple con el servicio de administración. |
| [Paso 3: Configurar y registrar el dispositivo a través de Windows PowerShell para StorSimple](#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple) |Conecte el dispositivo a la red y regístrelo con Azure para completar la instalación mediante el servicio de administración. |
| [Paso 4: Para completar la configuración mínima del dispositivo](#step-4-complete-minimum-device-setup) </br>Opcional: actualización del dispositivo StorSimple. |Use el servicio de administración para realizar la instalación del dispositivo y habilitarlo para proporcionar almacenamiento. |
| [Paso 5: Crear un contenedor de volúmenes](#step-5-create-a-volume-container) |Cree un contenedor para aprovisionar los volúmenes. Un contenedor de volúmenes tiene la configuración de la cuenta de almacenamiento, el ancho de banda y el cifrado de todos los volúmenes que contiene. |
| [Paso 6: Crear un volumen](#step-6-create-a-volume) |Aprovisione volúmenes de almacenamiento en el dispositivo StorSimple para los servidores. |
| [Paso 7: Montar, inicializar y formatear un volumen](#step-7-mount-initialize-and-format-a-volume) </br>Opcional: configurar MPIO. |Conecte los servidores al almacenamiento iSCSI proporcionado por el dispositivo. De forma opcional, puede configurar MPIO para asegurarse de que los servidores pueden tolerar errores de vínculo, red e interfaz. |
| [Paso 8: Realizar una copia de seguridad](#step-8-take-a-backup) |Configure la directiva de copia de seguridad para proteger los datos. |
|  | |
| **OTROS PROCEDIMIENTOS** |Puede que necesite hacer referencia a estos procedimientos mientras implementa la solución. |
| [Configurar una nueva cuenta de almacenamiento para el servicio](#configure-a-new-storage-account-for-the-service) | |
| [Usar PuTTY para conectarse a la consola serie del dispositivo](#use-putty-to-connect-to-the-device-serial-console) | |
| [Búsqueda y aplicación de actualizaciones](#scan-for-and-apply-updates) | |
| [Obtener el IQN de un host de Windows Server](#get-the-iqn-of-a-windows-server-host) | |
| [Crear una copia de seguridad manual](#create-a-manual-backup) | |


## <a name="deployment-configuration-checklist"></a>Lista de comprobación de la configuración de implementación
Antes de implementar el dispositivo StorSimple, deberá recopilar información para configurar el software en el dispositivo. Si prepara alguna de esta información con antelación, simplificará el proceso de implementar el dispositivo StorSimple en el entorno. Descargue y use esta lista de comprobación para anotar los detalles de configuración cuando implemente el dispositivo.

[Descargar la lista de comprobación de la configuración de implementación de StorSimple](http://www.microsoft.com/download/details.aspx?id=49159)

## <a name="deployment-prerequisites"></a>Requisitos previos de implementación
Las siguientes secciones explican los requisitos previos de configuración para el servicio de Administrador de dispositivos de StorSimple y el dispositivo StorSimple.

### <a name="for-the-storsimple-device-manager-service"></a>Para el servicio StorSimple Device Manager
Antes de comenzar, asegúrese de que:

* Tiene una cuenta Microsoft con credenciales de acceso.
* Tiene una cuenta de almacenamiento de Microsoft Azure con credenciales de acceso.
* Su suscripción de Microsoft Azure está habilitada para el servicio de Administrador de dispositivos de StorSimple. Debe adquirir la suscripción a través del [Contrato Enterprise](https://azure.microsoft.com/pricing/enterprise-agreement/).
* Tiene acceso a software de emulación de terminales, como PuTTY.

### <a name="for-the-device-in-the-datacenter"></a>Para el dispositivo en el centro de datos
Antes de configurar el dispositivo, asegúrese de que:

* El dispositivo viene completamente desempaquetado, montado en un bastidor y totalmente cableado para alimentación, red y acceso serie, tal como se describe en:
  
  * [Desempaquetado, montaje en bastidor y cableado del dispositivo 8100](storsimple-8100-hardware-installation.md)
  * [Desempaquetado, montaje en bastidor y cableado del dispositivo 8600](storsimple-8600-hardware-installation.md)

### <a name="for-the-network-in-the-datacenter"></a>Para la red del centro de datos
Antes de comenzar, asegúrese de que:

* Se abren los puertos del firewall del centro de datos para permitir el tráfico de iSCSI y de la nube, como se describe en [Requisitos de red para el dispositivo StorSimple](storsimple-8000-system-requirements.md#networking-requirements-for-your-storsimple-device).

## <a name="step-by-step-deployment"></a>IMPLEMENTACIÓN PASO A PASO
Use las siguientes instrucciones paso a paso para implementar el dispositivo StorSimple en el centro de datos.

## <a name="step-1-create-a-new-service"></a>Paso 1: Crear un nuevo servicio
El servicio de Administrador de dispositivos de StorSimple puede administrar varios dispositivos StorSimple. Siga estos pasos para crear una instancia del servicio StorSimple Device Manager.

[!INCLUDE [storsimple-8000-create-new-service-gov](../../includes/storsimple-8000-create-new-service-gov.md)]

> [!IMPORTANT]
> Si no habilitó la creación automática de una cuenta de almacenamiento con el servicio, debe crear al menos una cuenta de almacenamiento después de crear correctamente un servicio. Dicha cuenta de almacenamiento se usará al crear un contenedor de volúmenes.
> 
> * Si no creó automáticamente una cuenta de almacenamiento, vaya a [Configurar una nueva cuenta de almacenamiento para el servicio](#configure-a-new-storage-account-for-the-service) para obtener instrucciones detalladas.
> * Si habilitó la creación automática de una cuenta de almacenamiento, vaya al [Paso 2: Obtener la clave de registro del servicio](#step-2-get-the-service-registration-key).


## <a name="step-2-get-the-service-registration-key"></a>Paso 2: Obtener la clave de registro del servicio
Una vez que el servicio StorSimple Device Manager esté en funcionamiento, necesitará obtener la clave de registro del servicio. Esta clave se usa para registrar y conectar el dispositivo StorSimple al servicio.

Siga estos pasos en el Portal de Government.

[!INCLUDE [storsimple-8000-get-service-registration-key](../../includes/storsimple-8000-get-service-registration-key.md)]

## <a name="step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple"></a>Paso 3: Configurar y registrar el dispositivo a través de Windows PowerShell para StorSimple
Use Windows PowerShell para StorSimple para completar la configuración inicial del dispositivo StorSimple, tal como se explica en el procedimiento siguiente. Deberá usar software de emulación de terminales para completar este paso. Para obtener más información, consulte [Uso de PuTTY para conectarse a la consola serie del dispositivo](#use-putty-to-connect-to-the-device-serial-console).

[!INCLUDE [storsimple-8000-configure-and-register-device-gov](../../includes/storsimple-8000-configure-and-register-device-gov-u2.md)]

## <a name="step-4-complete-minimum-device-setup"></a>Paso 4: Completar el programa de instalación mínima del dispositivo 
Para proceder a la configuración mínima del dispositivo StorSimple, debe hacer lo siguiente:

* Proporcione un nombre descriptivo para el dispositivo.
* Establezca la zona horaria del dispositivo.
* Asignar direcciones IP fijas a ambos controladores.

Siga estos pasos en el Portal de Azure Government para completar la configuración mínima del dispositivo.

[!INCLUDE [storsimple-8000-complete-minimum-device-setup-u2](../../includes/storsimple-8000-complete-minimum-device-setup-u2.md)]

## <a name="step-5-create-a-volume-container"></a>Paso 5: Crear un contenedor de volúmenes
Un contenedor de volúmenes tiene la configuración de la cuenta de almacenamiento, el ancho de banda y el cifrado de todos los volúmenes que contiene. Deberá crear un contenedor de volúmenes para poder empezar a aprovisionar volúmenes en el dispositivo StorSimple.

Para crear un contenedor de volúmenes, siga estos pasos en el Portal de Government.

[!INCLUDE [storsimple-8000-create-volume-container](../../includes/storsimple-8000-create-volume-container.md)]

## <a name="step-6-create-a-volume"></a>Paso 6: Crear un volumen
Después de crear un contenedor de volúmenes, puede aprovisionar un volumen de almacenamiento en el dispositivo StorSimple para los servidores. Para crear un volumen, siga estos pasos en el Portal de Government.

> [!IMPORTANT]
> StorSimple Device Manager solo puede crear volúmenes con aprovisionamiento fino.  Sin embargo, no se pueden crear volúmenes aprovisionados parcialmente.

[!INCLUDE [storsimple-8000-create-volume](../../includes/storsimple-8000-create-volume-u2.md)]

## <a name="step-7-mount-initialize-and-format-a-volume"></a>Paso 7: Montar, inicializar y formatear un volumen
Siga estos pasos en el host de Windows Server.

> [!IMPORTANT]
> * Para obtener la alta disponibilidad de la solución de StorSimple, se recomienda que configure MPIO en los servidores host (opcional) antes de configurar iSCSI. La configuración MPIO en los servidores host garantizará que los servidores pueden tolerar errores de vínculos, redes o interfaces.
> * Para instrucciones de instalación y configuración de MPIO e iSCSI en el host Windows Server, vaya a [Configurar MPIO para el dispositivo StorSimple](storsimple-configure-mpio-windows-server.md). También se incluyen los pasos para montar, inicializar y formatear volúmenes StorSimple.
> * Para instrucciones de instalación y configuración de MPIO e iSCSI en un host Linux, vaya a [Configurar MPIO para el host Linux de StorSimple](storsimple-configure-mpio-on-linux.md)

Si decide no configurar MPIO, realice los pasos siguientes para montar, inicializar y formatear los volúmenes StorSimple en un host Windows Server.

[!INCLUDE [storsimple-mount-initialize-format-volume](../../includes/storsimple-mount-initialize-format-volume.md)]

## <a name="step-8-take-a-backup"></a>Paso 8: Realizar una copia de seguridad
Las copias de seguridad proporcionan seguridad para los volúmenes a partir de un momento específico y mejoran la capacidad de recuperación al mismo tiempo que reducen los tiempos de restauración. Puede realizar dos tipos de copia de seguridad en el dispositivo StorSimple: instantáneas locales e instantáneas en la nube. Cada uno de estos tipos de copia de seguridad puede ser **Programada** o **Manual**.

Para crear una copia de seguridad programada, siga estos pasos en el Portal de Government.

[!INCLUDE [storsimple-8000-take-backup](../../includes/storsimple-8000-take-backup.md)]

Puede realizar una copia de seguridad manual en cualquier momento. Para conocer los procedimientos, vaya a [Crear una copia de seguridad manual](#create-a-manual-backup).

## <a name="configure-a-new-storage-account-for-the-service"></a>Configurar una nueva cuenta de almacenamiento para el servicio
Se trata de un paso opcional que debe llevar a cabo únicamente si no habilitó la creación automática de una cuenta de almacenamiento con su servicio. Se requiere una cuenta de almacenamiento de Microsoft Azure para crear un contenedor de volúmenes de StorSimple.

Si necesita crear una cuenta de almacenamiento de Azure en una región distinta, vea [Acerca de las cuentas de almacenamiento de Azure](../storage/common/storage-create-storage-account.md) para obtener instrucciones paso a paso.

Siga estos pasos en el Portal de Government, en la página **Servicio StorSimple Device Manager**.

[!INCLUDE [storsimple-configure-new-storage-account-u1](../../includes/storsimple-8000-configure-new-storage-account-u2.md)]

## <a name="use-putty-to-connect-to-the-device-serial-console"></a>Uso de PuTTY para conectarse a la consola serie del dispositivo
Para conectarse a Windows PowerShell para StorSimple, deberá usar software de emulación de terminales, como PuTTY. Puede usar PuTTY cuando acceda al dispositivo directamente a través de la consola serie o abriendo una sesión de Telnet desde un equipo remoto.

[!INCLUDE [Use PuTTY to connect to the device serial console](../../includes/storsimple-use-putty.md)]

## <a name="scan-for-and-apply-updates"></a>Búsqueda y aplicación de actualizaciones
La actualización del dispositivo puede tardar entre varias horas. Para obtener instrucciones detalladas sobre cómo instalar la actualización más reciente, vaya a [Instalación de Update 4](storsimple-8000-install-update-4.md).

## <a name="get-the-iqn-of-a-windows-server-host"></a>Obtener el IQN de un host de Windows Server
Siga estos pasos para obtener el nombre completo del iSCSI (IQN) de un host de Windows que ejecute Windows Server® 2012.

[!INCLUDE [Get IQN of your Windows Server host](../../includes/storsimple-get-iqn.md)]

## <a name="create-a-manual-backup"></a>Crear una copia de seguridad manual
Siga estos pasos en el Portal de Government para crear una copia de seguridad manual a petición para un único volumen de su dispositivo StorSimple.

[!INCLUDE [Create a manual backup](../../includes/storsimple-8000-create-manual-backup.md)]

## <a name="next-steps"></a>Pasos siguientes
* Configure un [dispositivo virtual](storsimple-8000-cloud-appliance-u2.md).
* Use el [servicio de Administrador de dispositivos de StorSimple](storsimple-8000-manager-service-administration.md) para administrar el dispositivo StorSimple.

