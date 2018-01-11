---
title: "Solución de problemas de Azure Migrate | Microsoft Docs"
description: "Se proporciona información general sobre los problemas conocidos del servicio Azure Migrate, además de sugerencias para solucionar los errores comunes."
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: troubleshooting
ms.date: 12/12/2017
ms.author: raynew
ms.openlocfilehash: 1fcc9e12e63eda73d53ae2085bc2a64d31ea2067
ms.sourcegitcommit: aaba209b9cea87cb983e6f498e7a820616a77471
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/12/2017
---
# <a name="troubleshoot-azure-migrate"></a>Solución de problemas de Azure Migrate

## <a name="troubleshoot-common-errors"></a>Solución de errores comunes

[Azure Migrate](migrate-overview.md) evalúa las cargas de trabajo locales para su migración a Azure. Use este artículo para solucionar problemas al implementar y usar Azure Migrate.


**El recopilador no es capaz de conectarse a Internet**

Esto puede ocurrir cuando la máquina que está utilizando está detrás de un proxy. Asegúrese de proporcionar las credenciales de autorización en caso de que el proxy las necesite.
Si va a usar un proxy de firewall basado en direcciones URL para controlar la conectividad saliente, asegúrese de incluir en la lista de permitidos estas direcciones URL necesarias:

**URL** | **Propósito**  
--- | ---
*.portal.azure.com | Se requiere comprobar la conectividad con el servicio de Azure y validar los problemas de sincronización horaria.
*.oneget.org | Se requiere para descargar el módulo PowerCLI de vCenter basado en PowerShell.

**El recopilador no se puede conectar al proyecto con el identificador de proyecto y la clave que copié del portal**

Asegúrese de haber copiado y pegado la información correcta. Para solucionar el problema, instale Microsoft Monitoring Agent (MMA) como sigue:

1. Descargue [MMA](https://go.microsoft.com/fwlink/?LinkId=828603) en la máquina virtual del recopilador.
2. Para iniciar la instalación, haga doble clic en el archivo descargado.
3. En la **principal** que aparece al iniciar la instalación, haga clic en **Siguiente**. En la página **Términos de licencia**, haga clic en **Acepto** para aceptar la licencia.
4. En **Carpeta de destino**, mantenga o modifique la carpeta de instalación predeterminada y después haga clic en **Siguiente**.
5. En **Opciones de instalación del agente**, seleccione **Azure Log Analytics (OMS)** > **Siguiente**.
6. Haga clic en **Agregar** para agregar un área de trabajo nueva de Log Analytics. Pegue el identificador de proyecto y la clave que copió. A continuación, haga clic en **Siguiente**.
7. Verifique que el agente puede conectarse al proyecto. Si no puede, verifique la configuración. Si el agente puede conectarse, pero el recopilador no, póngase en contacto con el servicio de soporte técnico.


**Error 802: obtengo un error de sincronización de fecha y hora**

El reloj del servidor podría estar desincronizado con la hora actual en más de cinco minutos. Cambie la hora del reloj de la máquina virtual del recopilador para que coincida con la hora actual; para ello, siga estos pasos:

1. Abra un símbolo del sistema de administrador en la máquina virtual.
2. Para comprobar la zona horaria, ejecute w32tm /tz.
3. Para sincronizar la hora, ejecute w32tm /resync.

**Mi clave de proyecto tiene los símbolos "==" al final. El recopilador los codifica como caracteres alfanuméricos. ¿Es normal?**

Sí, todas las claves del proyecto termina con "==". El recopilador cifra la clave de proyecto antes de procesarla.

**Los datos de rendimiento de los discos y adaptadores de red aparecen como ceros**

Esto puede ocurrir si el nivel de configuración de las estadísticas de vCenter Server se establece en menos de tres. Si el nivel es tres o superior, vCenter almacena el historial de rendimiento de proceso, almacenamiento y red de la máquina virtual. Si el nivel es inferior a tres, vCenter no almacena datos de almacenamiento y red, sino solamente los datos de CPU y memoria. En este escenario, los datos de rendimiento se muestran como cero en Azure Migrate, y este último ofrece recomendaciones de tamaño de los discos y las redes en función de los metadatos recopilados de los equipos locales.

Para habilitar la recopilación de los datos de rendimiento del disco y la red, cambie el nivel de configuración de estadísticas a tres. A continuación, espere al menos un día para detectar el entorno y evaluarlo. 

**He instalado agentes y he usado la visualización de dependencias para crear grupos. Ahora, después de la conmutación por error, los equipos muestran la acción "Instalar agente" en lugar de "Ver dependencias"**
* Tras una conmutación por error planeada o no planeada, los equipos locales se desactivan y los equipos equivalentes se activan en Azure. Estos equipos adquieren una dirección MAC diferente. Pueden adquirir una dirección IP distinta en función de si el usuario elige conservar o no la dirección IP local. Si las direcciones IP y MAC difieren, Azure Migrate no asocia los equipos locales con ningún dato de dependencia de Service Map y solicita al usuario que instale los agentes en lugar de visualizar las dependencias.
* Después de la conmutación por error de prueba, los equipos locales permanecen encendidos según lo previsto. Los equipos equivalentes que se activan en Azure adquieren una dirección MAC distinta y pueden adquirir una dirección IP diferente. A menos que el usuario bloquee el tráfico saliente de OMS procedente de estos equipos, Azure Migrate no asocia los equipos locales con ningún dato de dependencia de Service Map y solicita al usuario que instale los agentes en lugar de visualizar las dependencias.


## <a name="troubleshoot-readiness-issues"></a>Solución de problemas de preparación

**Problema** | **Revisión**
--- | ---
Tipo de arranque no admitido | Cambie el BIOS antes de ejecutar una migración.
El recuento del disco supera el límite | Quite los discos no utilizados del equipo antes de la migración.
El tamaño del disco supera el límite | Reduzca los discos a un tamaño inferior a 4 TB antes de la migración. 
El disco no está disponible en la ubicación especificada | Asegúrese de que el disco está en la ubicación de destino antes de realizar la migración.
El disco no está disponible para la redundancia especificada | El disco debe usar el tipo de almacenamiento de redundancia definido en la configuración de evaluación (LRS de forma predeterminada).
No se pudo determinar la idoneidad del disco debido a un error interno | Intente crear una evaluación para el grupo. 
No se encontró ninguna máquina virtual con los núcleos y memoria requeridos | Azure no pudo ajustar un tipo de máquina virtual adecuado. Reduzca la memoria y el número de núcleos de la máquina local antes de realizar la migración. 
Uno o varios discos no son adecuados | Asegúrese de que los discos locales tengan un tamaño inferior a 4 TB o menos antes de ejecutar una migración.
Uno o varios adaptadores de red no son adecuados | Quite los adaptadores de red no utilizados de la máquina antes de realizar la migración.
No se pudo determinar la idoneidad de la máquina virtual debido a un error interno | Intente crear una evaluación para el grupo. 
No se pudo determinar la idoneidad de uno o varios discos debido a un error interno | Intente crear una evaluación para el grupo.
No se pudo determinar la idoneidad de uno o varios adaptadores de red debido a un error interno | Intente crear una evaluación para el grupo.
No se encontró ninguna máquina virtual para el rendimiento de almacenamiento requerido | El rendimiento de almacenamiento (IOPS/rendimiento) requerido para la máquina excede el soporte técnico de máquina virtual de Azure. Reduzca los requisitos de almacenamiento de la máquina antes de realizar la migración.
No se encontró ninguna máquina virtual para el rendimiento de red requerido | El rendimiento de red (entrada/salida) requerido para la máquina excede el soporte técnico de máquina virtual de Azure. Reduzca los requisitos de red de la máquina. 
No se encontró ninguna máquina virtual para el plan de tarifa especificado. | Compruebe la configuración del plan de tarifa. 
No se encontró la máquina virtual en la ubicación especificada | Utilice una ubicación de destino diferente antes de la migración.
Problemas de compatibilidad con el SO Linux | Asegúrese de que ejecuta 64 bits con estos [sistemas operativos](../virtual-machines/linux/endorsed-distros.md) compatibles.
Problemas de compatibilidad con el SO Windows | Asegúrese de que ejecuta un sistema operativo compatible. [Más información](concepts-assessment-calculation.md#azure-suitability-analysis)
Sistema operativo desconocido | Compruebe que el sistema operativo especificado en vCenter es correcto y repita el proceso de detección.
Requiere una suscripción a Visual Studio | Los sistemas operativos cliente Windows solo se admiten en suscripciones de Visual Studio (MSDN).


## <a name="collect-logs"></a>Recopilación de registros

**¿Cómo puedo recopilar registros en la máquina virtual del recopilador?**

El registro está habilitado de manera predeterminada. La ubicación de los registros es la siguiente:

- C:\Profiler\ProfilerEngineDB.sqlite
- C:\Profiler\Service.log
- C:\Profiler\WebApp.log

Para recopilar el Seguimiento de eventos para Windows, haga lo siguiente:

1. En la máquina virtual del recopilador, abra una ventana de comandos de PowerShell.
2. Ejecute **Get-EventLog -LogName Application | export-csv eventlog.csv**.

**¿Cómo puedo recopilar registros de tráfico de red del portal?**

1. Abra el explorador, vaya al [portal](https://portal.azure.com) e inicie sesión en él.
2. Presione F12 para iniciar las Herramientas de desarrollo. Si es necesario, desactive la configuración **Clear entries on navigation** (Borrar entradas de navegación).
3. Haga clic en la pestaña **Network** (Red) para empezar a capturar el tráfico de red:
 - En Chrome, seleccione **Preserve log** (Conservar registro). La grabación debe iniciarse automáticamente. Un círculo rojo indica que la captura del tráfico está en curso. Si no aparece, haga clic en el círculo negro para iniciar la captura.
 - En Edge/IE, la grabación debe iniciarse automáticamente. Si no es así, haga clic en el botón de reproducción de color verde.
4. Pruebe a reproducir el error.
5. Una vez haya encontrado el error durante la grabación, detenga la grabación y guarde una copia de la actividad grabada:
 - En Chrome, haga clic con el botón derecho y haga clic en **Save as HAR with content** (Guardar como HAR con contenido). Con esta acción, los registros se comprimen y exportan como un archivo .har.
 - En Edge/IE, haga clic en el icono **Exportar el tráfico capturado**. Con esta acción, el registro se comprime y exporta.
6. Navegue a la pestaña **Console** (Consola) para ver si hay alguna advertencia o error. Para guardar el registro de la consola:
 - En Chrome, haga clic con el botón derecho en cualquier lugar en el registro de la consola. Seleccione **Save as** (Guardar como) para exportar y comprimir el registro.
 - En Edge/IE, haga clic con el botón derecho sobre los errores y seleccione **Copy all** (Copiar todo). 
7. Cierre las Herramientas de desarrollo.
 



