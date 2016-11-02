<properties 
   pageTitle="Notas de la versión de la matriz virtual de StorSimple | Microsoft Azure"
   description="Describe los problemas críticos y las soluciones de la matriz virtual de StorSimple."
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="05/13/2016"
   ms.author="alkohli" />

# Notas de la versión de la matriz virtual de StorSimple

## Información general

Las siguientes notas de la versión identifican los problemas críticos de la versión de disponibilidad general (GA) de marzo de 2016 de la matriz virtual de Microsoft Azure StorSimple (también conocida como dispositivo virtual local de StorSimple o dispositivo virtual de StorSimple). Esta versión se corresponde con la versión de software 10.0.10271.0.

Las notas de la versión se actualizan continuamente y se van agregando a medida que se descubren problemas críticos que requieren una solución alternativa. Antes de implementar el dispositivo virtual de StorSimple, le recomendamos que lea detenidamente la información que encontrará en las notas de la versión.

En la tabla siguiente se proporciona un resumen de los problemas conocidos de esta versión.


| N.º | Característica | Problema | Soluciones alternativas o comentarios |
|-----|--------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **1.** | Actualizaciones | Los dispositivos virtuales que se crean en la versión preliminar no se pueden actualizar a una versión que sea compatible con la versión de disponibilidad general. | Debe conmutar por error estos dispositivos virtuales a la versión de disponibilidad general mediante un flujo de trabajo de recuperación ante desastres (DR). |
| **2.** | Disco de datos aprovisionado | Una vez se haya aprovisionado un disco de datos de un determinado tamaño y cree el correspondiente dispositivo virtual de StorSimple, no debe expandir o reducir el disco de datos. Si intentar hacer esto, es posible que pierda todos los datos de las capas locales del dispositivo. | |
| **3.** | Directiva de grupo | Cuando un dispositivo está unido al dominio, aplicar una directiva de grupo puede afectar negativamente al funcionamiento de dispositivo. | Asegúrese de que su matriz virtual está en su propia unidad organizativa (UO) de Active Directory y de que no se le aplica ningún objeto de directiva de grupo (GPO).|
| **4.** | Interfaz de usuario web local. | Si tiene las características de seguridad mejorada habilitadas en Internet Explorer (IE ESC), es posible que algunas páginas de la interfaz de usuario web local, tales como Solución de problemas o Mantenimiento, no funcionen correctamente. Asimismo, cabe la posibilidad de que los botones de estas páginas tampoco funcionen. | Desactive las características de seguridad mejorada de Internet Explorer.|
| **5.** | Interfaz de usuario web local. | En una máquina virtual de Hyper-V, las interfaces de red que se encuentran en la interfaz de usuario web se muestran como interfaces de 10 Gbps. | Este comportamiento es un reflejo de Hyper-V. Hyper-V siempre muestra los adaptadores de red virtual a 10 Gbps. |
| **6.** | Volúmenes o recursos compartidos en niveles | El bloqueo del intervalo de bytes de las aplicaciones que funcionan con volúmenes de StorSimple no se admite. Si tiene habilitado un bloqueo de intervalo de bytes, la organización en niveles de StorSimple no funcionará. | Entre las medidas recomendadas se incluyen: <br></br>Desactive el bloqueo del intervalo de bytes en la lógica de la aplicación.<br></br>Guarde los datos de esta aplicación en volúmenes que estén anclados localmente en lugar de volúmenes organizados en niveles.<br></br>*Advertencia*: si usa volúmenes anclados localmente y tiene el bloqueo del intervalo de bytes habilitado, tenga en cuenta que el volumen anclado localmente puede estar en línea incluso antes de que se complete la restauración. En tal caso, si hay una restauración en curso, debe esperar a que esta se complete. |
| **7.** | Recursos compartidos organizados en niveles | Si trabaja con archivos de gran tamaño, estos podrían ocasionar que la organización en niveles se desarrolle lentamente. | Cuando trabaje con esta clase de archivos, es recomendable que el archivo de mayor tamaño no ocupe más del 3 % del tamaño recurso compartido. |
| **8.** | Capacidad de recursos compartidos usada | Si no hay datos en el recurso compartido, es posible que vea cierto consumo del recurso compartido. Esto ocurre porque la capacidad que se usa para los recursos compartidos incluye los metadatos. | |
| **9.** | Recuperación ante desastres | Solo puede realizar la recuperación ante desastres de un servidor de archivos en el mismo dominio que el del dispositivo de origen. Con esta versión no se puede realizar la recuperación ante desastres en el dispositivo de destino de otro dominio. | Esta característica se implementará en una versión posterior. |
| **10.** | Azure PowerShell | En esta versión no se pueden administrar los dispositivos virtuales de StorSimple a través de Azure PowerShell. | Toda la administración de los dispositivos virtuales debe realizarse mediante el portal de Azure clásico y la interfaz de usuario web local. |
| **11.** | Cambio de contraseña | La consola del dispositivo de matriz virtual solo acepta entradas con el formato de teclado es-ES. | |
| **12.** | CHAP | Las credenciales CHAP no se pueden quitar una vez creadas. Además, si modifica las credenciales CHAP, deberá desconectar los volúmenes y volver a ponerlos en línea para que el cambio surta efecto. | Esta situación se abordará en una versión posterior. |
| **13.** | Servidor iSCSI | El "almacenamiento usado" que aparece para un volumen iSCSI puede ser diferente en el servicio StorSimple Manager y en el host iSCSI. | El host iSCSI tiene la vista del sistema de archivos.<br></br>El dispositivo ve los bloques asignados cuando el volumen estaba en el tamaño máximo.|

<!---HONumber=AcomDC_0518_2016-->