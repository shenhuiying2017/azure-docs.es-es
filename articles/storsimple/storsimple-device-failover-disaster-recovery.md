<properties 
   pageTitle="Conmutación por error y recuperación ante desastres del dispositivo StorSimple | Microsoft Azure"
   description="Aprenda cómo conmutar por error el dispositivo StorSimple a sí mismo, a otro dispositivo físico o a un dispositivo virtual."
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="adinah"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/23/2015"
   ms.author="alkohli" />

# Conmutación por error y recuperación ante desastres para el dispositivo StorSimple

## Información general

En este tutorial se describen los pasos necesarios para conmutar por error un dispositivo StorSimple en caso de desastre. La conmutación por error permite migrar los datos desde un dispositivo de origen en el centro de datos a otro dispositivo físico o incluso virtual situado en la misma ubicación geográfica o en otra diferente. La conmutación por error de un dispositivo se coordina a través de la característica de recuperación ante desastres y se inicia desde la página Dispositivos. Esta página recoge en formato de tabla todos los dispositivos de StorSimple conectados al servicio de Administrador de StorSimple. Para cada dispositivo se muestran el nombre descriptivo, el estado, la capacidad aprovisionada y máxima, el tipo y el modelo.

![Página de dispositivos](./media/storsimple-device-failover-disaster-recovery/IC740972.png)

## Recuperación ante desastres y conmutación por error del dispositivo

En un escenario de recuperación ante desastres, el dispositivo principal deja de funcionar. En esta situación, puede mover los datos en la nube asociados al dispositivo con error a otro dispositivo usando el dispositivo primario como *origen* y especificando otro dispositivo como *destino*. Puede seleccionar uno o varios contenedores de volúmenes para migrar al dispositivo de destino. Este proceso se conoce como *conmutación por error*. Durante la conmutación por error, los contenedores de volúmenes del dispositivo de origen cambian la propiedad y se transfieren al dispositivo de destino.

## Consideraciones para la conmutación por error del dispositivo

En caso de desastre, puede elegir conmutar por error el dispositivo StorSimple:

- A un dispositivo físico 
- A sí mismo
- A un dispositivo virtual

En todos los tipos de conmutación por error del dispositivo, tenga en cuenta lo siguiente:

- Los requisitos previos para la recuperación ante desastres son que todos los volúmenes incluidos en los contenedores de volúmenes estén desconectados y que los contenedores de volúmenes tengan asociada una instantánea en la nube. 
- Los dispositivos de destino disponibles para la recuperación ante desastres son dispositivos con espacio suficiente para alojar los contenedores de volúmenes seleccionados. 
- Los dispositivos que están conectados al servicio pero que no cumplen los criterios de espacio suficiente no estarán disponibles como dispositivos de destino.

## Conmutar por error a otro dispositivo físico

Siga estos pasos para restaurar el dispositivo a un dispositivo físico de destino.

1. Compruebe que el contenedor de volúmenes que desea conmutar por error tiene asociadas instantáneas en la nube.

1. En la página **Dispositivos**, haga clic en la pestaña **Contenedores de volúmenes**.

1. Seleccione un contenedor de volúmenes que desee conmutar por error a otro dispositivo. Haga clic en el contenedor de volúmenes para mostrar la lista de volúmenes incluidos dentro de este contenedor. Seleccione un volumen y haga clic en **Desconectar** para desconectar el volumen. Repita este proceso para todos los volúmenes incluidos en el contenedor de volúmenes.

1. Repita el paso anterior para todos los contenedores de volúmenes que le gustaría conmutar por error a otro dispositivo.

1. En la página **Dispositivos**, haga clic en **Conmutación por error**.

1. En el asistente que se abre, en **Elegir el contenedor de volúmenes para la conmutación por error**:

	1. En la lista de contenedores de volúmenes, seleccione los contenedores de volúmenes que desea que conmuten por error.

		>[AZURE.NOTE]**Solo se muestran los contenedores de volúmenes con volúmenes desconectados e instantáneas de nube asociadas.**

	1. En **Elegir un dispositivo de destino** para los volúmenes de los contenedores seleccionados, elija un dispositivo de destino en la lista desplegable de dispositivos disponibles. En la lista desplegable solo se muestran los dispositivos con capacidad disponible.

	1. Por último, revise toda la configuración de conmutación por error en **Confirmar conmutación por error**. Haga clic en el icono de marca de verificación ![Icono de marca de verificación](./media/storsimple-device-failover-disaster-recovery/IC740895.png).

1. Una vez completada la conmutación por error, vaya a la página **Dispositivos**.

	1. Seleccione el dispositivo que se usó como dispositivo de destino para el proceso de conmutación por error.

	1. Vaya a la página **Contenedores de volúmenes**. Deberían aparecer todos los contenedores de volúmenes, junto con los volúmenes del dispositivo antiguo.

## Conmutación por error usando un solo dispositivo

Siga estos pasos si solo dispone de un dispositivo y necesita realizar una conmutación por error.

1. Tome instantáneas en la nube de todos los volúmenes del dispositivo.

1. Restablezca el dispositivo a los valores predeterminados de fábrica. Siga las instrucciones detalladas de [Cómo restablecer un dispositivo StorSimple a los valores predeterminados de fábrica](https://msdn.microsoft.com/library/dn772373.aspx).

1. Configure el dispositivo y vuelva a registrarlo con el servicio de Administrador de StorSimple.

1. En la página **Dispositivos**, el dispositivo antiguo debe aparecer como **Desconectado**. El dispositivo recién registrado debe aparecer como **Conectado**.

1. Complete primero la configuración mínima del dispositivo en el dispositivo nuevo.
												
	>[AZURE.IMPORTANT]**Si no se completa primero la configuración mínima, se producirá un error en la recuperación ante desastres como resultado de un error en la implementación actual. Este comportamiento se solucionará en una versión posterior.**

1. Seleccione el dispositivo antiguo (estado desconectado) y haga clic en **Conmutación por error**. En el asistente que aparece, conmute por error este dispositivo y especifique el dispositivo de destino como dispositivo recién registrado. Para obtener instrucciones detalladas, consulte [Conmutar por error a otro dispositivo físico](#fail-over-to-another-physical-device).

1. Se creará un trabajo de restauración de dispositivo que puede supervisar desde la página **Trabajos**.

1. Después de que el trabajo se haya completado correctamente, acceda al dispositivo nuevo y navegue hasta la página **Contenedores de volúmenes**. Ahora se deben migrar todos los contenedores de volúmenes del dispositivo antiguo al dispositivo nuevo.

## Conmutar por error a un dispositivo virtual de StorSimple

Debe crear y configurar un dispositivo virtual de StorSimple antes de ejecutar este procedimiento.
 
>[AZURE.NOTE]**En esta versión, la cantidad de almacenamiento que se admite en el dispositivo virtual de StorSimple es 30 TB.**

Siga estos pasos para restaurar el dispositivo a un dispositivo virtual de StorSimple de destino.

1. Compruebe que el contenedor de volúmenes que desea conmutar por error tiene asociadas instantáneas en la nube.

1. En la página **Dispositivos**, haga clic en la pestaña **Contenedores de volúmenes**.

1. Seleccione un contenedor de volúmenes que desee conmutar por error a otro dispositivo. Haga clic en el contenedor de volúmenes para mostrar la lista de volúmenes incluidos dentro de este contenedor. Seleccione un volumen y haga clic en **Desconectar** para desconectar el volumen. Repita este proceso para todos los volúmenes incluidos en el contenedor de volúmenes.

1. Repita el paso anterior para todos los contenedores de volúmenes que le gustaría conmutar por error a otro dispositivo.

1. En la página **Dispositivos**, haga clic en **Conmutación por error**.

1. En el asistente que se abre, en **Elegir el contenedor de volúmenes para la conmutación por error**, siga estos pasos:
													
	a. En la lista de contenedores de volúmenes, seleccione los contenedores de volúmenes que desea que conmuten por error.

	>[AZURE.NOTE]**Solo se muestran los contenedores de volúmenes con volúmenes desconectados e instantáneas de nube asociadas.**

	b. En **Elegir un dispositivo de destino para los volúmenes de los contenedores seleccionados**, seleccione el dispositivo virtual de StorSimple en la lista desplegable de dispositivos disponibles. En la lista desplegable solo se muestran los dispositivos con capacidad suficiente.
	
	>[AZURE.NOTE]**Si el dispositivo físico ejecuta Update 1, solo puede conmutar a un dispositivo virtual que ejecute Update 1. Si el dispositivo virtual de destino está ejecutando una versión anterior del software, verá un error en el sentido de que es necesario actualizar el software del dispositivo de destino.**

1. Por último, revise toda la configuración de conmutación por error en **Confirmar conmutación por error**. Haga clic en el icono de marca de verificación ![Icono de marca de verificación](./media/storsimple-device-failover-disaster-recovery/IC740895.png).

1. Una vez completada la conmutación por error, vaya a la página **Dispositivos**.
													
	a. Seleccione el dispositivo virtual de StorSimple que se usó como dispositivo de destino para el proceso de conmutación por error.
	
	b. Vaya a la página **Contenedores de volúmenes**. Deberían aparecer todos los contenedores de volúmenes, junto con los volúmenes del dispositivo antiguo.

## Recuperación ante desastres y continuidad empresarial (BCDR)

Un escenario de recuperación ante desastres y continuidad empresarial (BCDR) se produce cuando todo el centro de datos de Azure deja de funcionar. Esto puede afectar al servicio de Administrador de StorSimple y a los dispositivos StorSimple asociados.

Si hay dispositivos StorSimple que se registraron justo antes de que ocurra un desastre, es posible que estos dispositivos deban restablecerse a valores de fábrica. Después del desastre, el dispositivo StorSimple se mostrará como desconectado. El dispositivo StorSimple debe eliminarse del portal, restablecerse a valores de fábrica y volver a registrarse.

## Pasos siguientes

Después de realizar la conmutación por error, puede que deba:

- [Desactivar el dispositivo StorSimple](storsimple-deactivate-and-delete-device.md#deactivate-a-device)
- [Eliminar el dispositivo StorSimple](storsimple-deactivate-and-delete-device.md#delete-a-device)

Para obtener información sobre cómo administrar el dispositivo mediante el servicio de Administrador de StorSimple, consulte:

- [Guía del administrador](https://msdn.microsoft.com/library/dn772401.aspx)

 

<!---HONumber=August15_HO6-->