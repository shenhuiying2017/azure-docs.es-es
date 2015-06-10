<properties 
   pageTitle="Implementar el dispositivo StorSimple local"
   description="Pasos y procedimientos recomendados para implementar el servicio y el dispositivo de la actualización 1 de StorSimple en el portal de Azure Government."
   services="storsimple"
   documentationCenter="NA"
   authors="SharS"
   manager="adinah"
   editor="tysonn" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="05/27/2015"
   ms.author="v-sharos" />

# Implementar el dispositivo StorSimple local

[AZURE.INCLUDE [storsimple-version-selector](../includes/storsimple-version-selector.md)]

## Información general

Bienvenido a la implementación del dispositivo StorSimple de Microsoft Azure.

Estos tutoriales de implementación se aplican a la serie StorSimple 8000 del Portal de Azure Government.

En esta serie de tutoriales describe cómo configurar un dispositivo de StorSimple y se incluyen una lista de comprobación previa a la instalación, requisitos previos de configuración y pasos de configuración detallados.

> [AZURE.NOTE]La información de implementación de StorSimple publicada en el sitio web de Microsoft Azure y en MSDN Library se aplica solo a los dispositivos de la serie 8000 de StorSimple. Para obtener información completa sobre los dispositivos de la serie 7000, vaya a: [http://onlinehelp.storsimple.com/](http://onlinehelp.storsimple.com). Para obtener información sobre la implementación de la serie 7000, vea la [Guía de inicio rápido del sistema StorSimple](http://onlinehelp.storsimple.com/111_Appliance/).

La información de estos tutoriales da por supuesto que revisó las precauciones de seguridad y la lista de comprobación de configuración, y que desempaquetó, montó y cableó el dispositivo StorSimple. Si todavía debe realizar estas tareas, vaya a [Precauciones de seguridad](https://msdn.microsoft.com/library/azure/dn772366.aspx), [Lista de comprobación de configuración](https://msdn.microsoft.com/library/azure/dn757787.aspx) e [Instalación de hardware del dispositivo](https://msdn.microsoft.com/library/azure/dn772375.aspx), según corresponda.

Necesitará privilegios de administrador para completar el proceso de instalación y configuración. Se recomienda que revise la lista de comprobación previa a la instalación antes de comenzar. El proceso de implementación y configuración puede tardar algún tiempo en completarse.

## Lista de comprobación previa a la instalación

La siguiente lista de comprobación previa a la instalación describe la información que necesita recopilar antes de configurar el software en el dispositivo StorSimple. Si prepara esta información con antelación, simplificará el proceso de implementar el dispositivo StorSimple en el entorno.

| | Requisitos | Detalles | Valores |
|---| --------------------- | ---------------------- | ------------- |
| 1 | Configuración de red <ol><li>Dirección IP del dispositivo</li><li>Interfaces de red, 4x1 GbE, 2x10 GbE</li><li>IP fija del controlador</li><li>Máscaras de subred</li><li>Puerta de enlace</li></ol> | Número total de IP requeridas: 8 <ol><li>Una por dispositivo</li><li>Una por interfaz de red habilitada, total 6</li><li>Una por controlador, total 2, requerido para conectarse a Internet para atender las actualizaciones</li><li>Una para cada dirección IP</li><li>Una por dispositivo</li></ol> | |
| 2 | Acceso serie | Configuración inicial del dispositivo | Sí/No |
| 3 | Direcciones IP del servidor DNS | Necesarias para conectarse a Microsoft Azure: necesarias un total de 2 para alta disponibilidad | |
| 4 | Direcciones IP del servidor NTP | Necesarias para sincronizar la hora con Azure: 1 necesaria, 1 opcional | |
| 5 | Servidor proxy (opcional) | Dirección IP/nombre de dominio completo del servidor proxy, puerto que se usará | |
| 6 | Cuenta de almacenamiento de Azure | Credenciales de acceso, como el nombre de cuenta y la clave acceso, por cuenta de almacenamiento | |
| 7 | Clave de cifrado de almacenamiento en la nube (recomendado) | Por contenedor de volúmenes | |
| 8 | IQN del host | Por host | |

## Requisitos previos de implementación

Las siguientes secciones explican los requisitos previos de configuración para el servicio de Administrador de StorSimple y el dispositivo StorSimple.

### Para el servicio de Administrador de StorSimple

Antes de comenzar, asegúrese de que:

- Tiene una cuenta Microsoft con credenciales de acceso.

- Tiene una cuenta de almacenamiento de Microsoft Azure con credenciales de acceso.

- Su suscripción de Microsoft Azure está habilitada para el servicio de Administrador de StorSimple. Debe adquirir la suscripción a través del [Contrato Enterprise](http://azure.microsoft.com/pricing/enterprise-agreement/).

- Tiene acceso a software de emulación de terminales, como PuTTY.

### Para el dispositivo en el centro de datos

Antes de configurar el dispositivo, asegúrese de que:

- El dispositivo está desempaquetado por completo, tal como se describe en [Desempaquetar el dispositivo 8100](https://msdn.microsoft.com/library/azure/dn772327.aspx) o [Desempaquetar el dispositivo 8600](https://msdn.microsoft.com/library/azure/dn772418.aspx).

- El dispositivo está montado en un bastidor, tal como se describe en [Montar en el bastidor el dispositivo 8100](https://msdn.microsoft.com/library/azure/dn757749.aspx) o [Montar en el bastidor el dispositivo 8600](https://msdn.microsoft.com/library/azure/dn757745.aspx).

- El dispositivo dispone de todos los cables de alimentación, red y acceso serie, tal como se describe en [Cablear el dispositivo 8100](https://msdn.microsoft.com/library/azure/dn757738.aspx) o [Cablear el dispositivo 8600](https://msdn.microsoft.com/library/azure/dn757762.aspx).

- Se abren los puertos del firewall del centro de datos para permitir el tráfico de iSCSI y de la nube, como se describe en [Requisitos de red para el dispositivo StorSimple](https://msdn.microsoft.com/library/dn772371.aspx).

## Pasos de implementación

Siga estos pasos imprescindibles para configurar el dispositivo StorSimple y conectarlo al servicio de Administrador de StorSimple:

- Paso 1: Crear un nuevo servicio 
- Paso 2: Obtener la clave de registro del servicio
- Paso 3: Configurar y registrar el dispositivo a través de Windows PowerShell para StorSimple 
- Paso 4: Completar la instalación mínima del dispositivo
- Paso 5: Crear un contenedor de volúmenes 
- Paso 6: Crear un volumen
- Paso 7: Montar, inicializar y formatear un volumen 
- Paso 8: Realizar una copia de seguridad

Además de los pasos imprescindibles, hay una serie de pasos opcionales que podría tener que seguir al implementar la solución. Estos pasos opcionales explican cómo:

- Configurar una nueva cuenta de almacenamiento para el servicio
- Usar PuTTY para conectarse a la consola serie del dispositivo
- Obtener el IQN de un host de Windows Server
- Crear una copia de seguridad manual
- Configurar MPIO

Las instrucciones detalladas de implementación indican cuándo debe realizar cada uno de estos pasos opcionales.

## Paso 1: Crear un nuevo servicio

El servicio de Administrador de StorSimple puede administrar varios dispositivos de StorSimple. Siga estos pasos para crear una nueva instancia del servicio de Administrador de StorSimple.

[AZURE.INCLUDE [storsimple-create-new-service-gov](../includes/storsimple-create-new-service-gov.md)]

> [AZURE.IMPORTANT]Si no habilitó la creación automática de una cuenta de almacenamiento con el servicio, debe crear al menos una cuenta de almacenamiento después de crear correctamente un servicio. Dicha cuenta de almacenamiento se usará al crear un contenedor de volúmenes.
>
> * Si no creó automáticamente una cuenta de almacenamiento, vaya a [Configurar una nueva cuenta de almacenamiento para el servicio](#configure-a-new-storage-account-for-the-service) para obtener instrucciones detalladas. 
> * Si habilitó la creación automática de una cuenta de almacenamiento, vaya al [Paso 2: Obtener la clave de registro del servicio](#step-2:-get-the-service-registration-key).

## Paso 2: Obtener la clave de registro del servicio

Una vez esté en funcionamiento el servicio de Administrador de StorSimple, necesitará la clave de registro del servicio. Esta clave se usa para registrar y conectar el dispositivo StorSimple con el servicio.

Siga estos pasos en el Portal de Government.

[AZURE.INCLUDE [storsimple-get-service-registration-key-gov](../includes/storsimple-get-service-registration-key-gov.md)]


## Paso 3: Configurar y registrar el dispositivo a través de Windows PowerShell para StorSimple

Use Windows PowerShell para StorSimple para completar la configuración inicial del dispositivo StorSimple, tal como se explica en el procedimiento siguiente. Deberá usar software de emulación de terminales para completar este paso. Para obtener más información, consulte [Uso de PuTTY para conectarse a la consola serie del dispositivo](#use-putty-to-connect-to-the-device-serial-console).

[AZURE.INCLUDE [storsimple-configure-and-register-device-gov](../includes/storsimple-configure-and-register-device-gov.md)]

## Paso 4: Completar la instalación mínima del dispositivo

Para proceder a la configuración mínima del dispositivo StorSimple, debe hacer lo siguiente:

- Configurar el servidor DNS secundario.
- Habilitar iSCSI en al menos una interfaz de red.
- Asignar direcciones IP fijas a ambos controladores.

Siga estos pasos en el Portal de Government para completar la instalación mínima del dispositivo.

[AZURE.INCLUDE [storsimple-complete-minimum-device-setup](../includes/storsimple-complete-minimum-device-setup-u1.md)]

## Paso 5: Crear un contenedor de volúmenes

Un contenedor de volúmenes tiene la configuración de la cuenta de almacenamiento, el ancho de banda y el cifrado de todos los volúmenes que contiene. Deberá crear un contenedor de volúmenes para poder empezar a aprovisionar volúmenes en el dispositivo StorSimple.

Para crear un contenedor de volúmenes, siga estos pasos en el Portal de Government.

[AZURE.INCLUDE [storsimple-create-volume-container](../includes/storsimple-create-volume-container.md)]

## Paso 6: Crear un volumen

Después de crear un contenedor de volúmenes, puede aprovisionar un volumen de almacenamiento en el dispositivo StorSimple para los servidores. Para crear un volumen, siga estos pasos en el Portal de Government.

> [AZURE.IMPORTANT]Azure StorSimple solo puede crear volúmenes con aprovisionamiento fino. No es posible crear volúmenes total o parcialmente aprovisionados en un sistema de Azure StorSimple.

[AZURE.INCLUDE [storsimple-create-volume](../includes/storsimple-create-volume.md)]

## Paso 7: Montar, inicializar y formatear un volumen

Siga estos pasos en el host de Windows Server.

[AZURE.INCLUDE [storsimple-mount-initialize-format-volume](../includes/storsimple-mount-initialize-format-volume.md)]

## Paso 8: Realizar una copia de seguridad

Las copias de seguridad proporcionan seguridad para los volúmenes a partir de un momento específico y mejoran la capacidad de recuperación al mismo tiempo que reducen los tiempos de restauración. Puede realizar dos tipos de copia de seguridad en el dispositivo StorSimple: instantáneas locales e instantáneas en la nube. Cada uno de estos tipos de copia de seguridad puede ser **Programada** o **Manual**.

Para crear una copia de seguridad programada, siga estos pasos en el Portal de Government.

[AZURE.INCLUDE [storsimple-take-backup](../includes/storsimple-take-backup.md)]

Puede realizar una copia de seguridad manual en cualquier momento. Para conocer los procedimientos, vaya a [Crear una copia de seguridad manual](#create-a-manual-backup).

## Configurar una nueva cuenta de almacenamiento para el servicio

Se trata de un paso opcional que debe llevar a cabo únicamente si no habilitó la creación automática de una cuenta de almacenamiento con su servicio. Se requiere una cuenta de almacenamiento de Microsoft Azure para crear un contenedor de volúmenes de StorSimple.

Si necesita crear una cuenta de almacenamiento de Azure en una región distinta, vea [Acerca de las cuentas de almacenamiento de Azure](storage-create-storage-account.md) para obtener instrucciones paso a paso.

Siga estos pasos en el Portal de Government, en la página **Servicio de Administrador de StorSimple**.

[AZURE.INCLUDE [storsimple-configure-new-storage-account-u1](../includes/storsimple-configure-new-storage-account-u1.md)]


## Usar PuTTY para conectarse a la consola serie del dispositivo

Para conectarse a Windows PowerShell para StorSimple, deberá usar software de emulación de terminales, como PuTTY. Puede usar PuTTY cuando acceda al dispositivo directamente a través de la consola serie o abriendo una sesión de Telnet desde un equipo remoto.

[AZURE.INCLUDE [Usar PuTTY para conectarse a la consola serie del dispositivo](../includes/storsimple-use-putty.md)]

## Obtener el IQN de un host de Windows Server

Siga estos pasos para obtener el nombre completo del iSCSI (IQN) de un host de Windows que ejecute Windows Server® 2012.

[AZURE.INCLUDE [Crear una copia de seguridad manual](../includes/storsimple-get-iqn.md)]

## Creación de una copia de seguridad manual

Siga estos pasos en el Portal de Government para crear una copia de seguridad a petición para un único volumen de su dispositivo StorSimple.

[AZURE.INCLUDE [Crear una copia de seguridad manual](../includes/storsimple-create-manual-backup-gov.md)]

## Configurar MPIO

E/S de múltiples rutas (MPIO) es una característica opcional y no está instalada de forma predeterminada en Windows Server. Se debe instalar como característica a través del Administrador de servidores.

> [AZURE.NOTE]No se admite MPIO en un dispositivo virtual de StorSimple.

Para obtener instrucciones de instalación de MPIO, vaya a [Configurar MPIO para el dispositivo StorSimple](storsimple-configure-mpio-windows-server.md).

## Pasos siguientes

Configure un [dispositivo virtual](storsimple-virtual-device.md).

Use el [servicio de Administrador de StorSimple](https://msdn.microsoft.com/library/azure/dn772396.aspx) para administrar el dispositivo StorSimple.

<!---HONumber=58-->