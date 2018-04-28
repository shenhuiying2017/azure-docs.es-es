---
title: Solución de problemas de Azure Migrate | Microsoft Docs
description: Se proporciona información general sobre los problemas conocidos del servicio Azure Migrate, además de sugerencias para solucionar los errores comunes.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: troubleshooting
ms.date: 03/19/2018
ms.author: raynew
ms.openlocfilehash: bba52534c534ea33c2939d0d9b76b7b4138e13cd
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/16/2018
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

Asegúrese de haber copiado y pegado la información correcta. Para solucionar problemas, instale Microsoft Monitoring Agent (MMA) y compruebe si el MMA puede conectarse al proyecto como se indica a continuación:

1. Descargue [MMA](https://go.microsoft.com/fwlink/?LinkId=828603) en la máquina virtual del recopilador.
2. Para iniciar la instalación, haga doble clic en el archivo descargado.
3. En la **principal** que aparece al iniciar la instalación, haga clic en **Siguiente**. En la página **Términos de licencia**, haga clic en **Acepto** para aceptar la licencia.
4. En **Carpeta de destino**, mantenga o modifique la carpeta de instalación predeterminada y después haga clic en **Siguiente**.
5. En **Opciones de instalación del agente**, seleccione **Azure Log Analytics** > **Siguiente**.
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
* Después de la conmutación por error de prueba, los equipos locales permanecen encendidos según lo previsto. Los equipos equivalentes que se activan en Azure adquieren una dirección MAC distinta y pueden adquirir una dirección IP diferente. A menos que el usuario bloquee el tráfico saliente de Log Analytics procedente de estos equipos, Azure Migrate no asocia los equipos locales con ningún dato de dependencia de Service Map y solicita al usuario que instale los agentes en lugar de visualizar las dependencias.


## <a name="troubleshoot-readiness-issues"></a>Solución de problemas de preparación

**Problema** | **Revisión**
--- | ---
Tipo de arranque no compatible | Azure no admite máquinas virtuales con el tipo de arranque EFI. Se recomienda convertir el tipo de arranque a BIOS antes de ejecutar una migración. <br/><br/>Puede usar [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/tutorial-migrate-on-premises-to-azure) para realizar la migración de las máquinas virtuales de este tipo, dado que durante la migración el tipo de arranque de la máquina virtual se convertirá a BIOS.
Sistema operativo Windows admitido con condiciones | El sistema operativo alcanzó la fecha de finalización del soporte técnico y necesita un contrato de soporte técnico personalizado (CSA) para [recibir soporte técnico en Azure](https://aka.ms/WSosstatement). Considere la posibilidad de actualizar el sistema operativo antes de migrar a Azure.
Sistema operativo Windows no admitido | Azure solo admite [algunas versiones del sistema operativo Windows](https://aka.ms/WSosstatement). Considere la posibilidad de actualizar el sistema operativo de la máquina antes de migrar a Azure.
Sistema operativo Linux aprobado con condiciones | Azure solo aprueba [algunas versiones del sistema operativo Linux](../virtual-machines/linux/endorsed-distros.md). Considere la posibilidad de actualizar el sistema operativo de la máquina antes de migrar a Azure.
Sistema operativo Linux no aprobado | La máquina se puede arrancar en Azure, pero Azure no proporciona compatibilidad con ningún sistema operativo. Considere la posibilidad de actualizar el sistema operativo a una [versión Linux aprobada](../virtual-machines/linux/endorsed-distros.md) antes de migrar a Azure
Sistema operativo desconocido | El sistema operativo de la máquina virtual se especificó como "Otro" en vCenter Server, debido a que Azure Migrate no puede identificar la preparación para Azure de la máquina virtual. Asegúrese de que el sistema operativo que se ejecuta dentro de la máquina se [admite](https://aka.ms/azureoslist) en Azure antes de migrar la máquina.
Valor de bits de sistema operativo no aprobado | Las máquinas virtuales con sistemas operativos de 32 bits pueden arrancar en Azure; sin embargo, se recomienda actualizar el sistema operativo de la máquina virtual a 64 bits antes de migrar a Azure.
Requiere una suscripción a Visual Studio | Las máquinas tienen un sistema operativo cliente de Windows que se ejecuta en su interior, que solo se admite en suscripciones de Visual Studio.
No se encontró ninguna máquina virtual para el rendimiento de almacenamiento requerido | El rendimiento de almacenamiento (IOPS/rendimiento) requerido para la máquina excede el soporte técnico de máquina virtual de Azure. Reduzca los requisitos de almacenamiento de la máquina antes de realizar la migración.
No se encontró ninguna máquina virtual para el rendimiento de red requerido | El rendimiento de red (entrada/salida) requerido para la máquina excede el soporte técnico de máquina virtual de Azure. Reduzca los requisitos de red de la máquina.
No se encontró ninguna máquina virtual en el plan de tarifa especificado. | Si el plan de tarifa está establecido en Estándar, considere la posibilidad de reducir el tamaño de la máquina virtual antes de migrar a Azure. Si el nivel de ajuste de tamaño es Básico, considere la posibilidad de cambiar el plan de tarifa de la valoración a Estándar.
No se encontró la máquina virtual en la ubicación especificada | Utilice una ubicación de destino diferente antes de la migración.
Uno o varios discos no son adecuados | Uno o varios discos conectados a la VM no cumplen los requisitos de Azure. Para cada disco conectado a la VM, compruebe que el tamaño del disco sea inferior a 4 TB. De lo contrario, redúzcalo antes de migrar a Azure. Asegúrese de que el rendimiento (IOPS/rendimiento) necesario para cada disco sea compatible con los [discos de máquinas virtuales administradas](https://docs.microsoft.com/azure/azure-subscription-service-limits#storage-limits) de Azure.   
Uno o varios adaptadores de red no son adecuados | Quite los adaptadores de red no utilizados de la máquina antes de realizar la migración.
El recuento del disco supera el límite | Quite los discos no utilizados del equipo antes de la migración.
El tamaño del disco supera el límite | Azure admite discos con un tamaño de hasta 4 TB. Reduzca los discos a un tamaño inferior a 4 TB antes de la migración.
El disco no está disponible en la ubicación especificada | Asegúrese de que el disco está en la ubicación de destino antes de realizar la migración.
El disco no está disponible para la redundancia especificada | El disco debe usar el tipo de almacenamiento de redundancia definido en la configuración de evaluación (LRS de forma predeterminada).
No se pudo determinar la idoneidad del disco debido a un error interno | Intente crear una evaluación para el grupo.
No se encontró ninguna máquina virtual con los núcleos y memoria requeridos | Azure no pudo ajustar un tipo de máquina virtual adecuado. Reduzca la memoria y el número de núcleos de la máquina local antes de realizar la migración.
No se pudo determinar la idoneidad de la máquina virtual debido a un error interno | Intente crear una evaluación para el grupo.
No se pudo determinar la idoneidad de uno o varios discos debido a un error interno | Intente crear una evaluación para el grupo.
No se pudo determinar la idoneidad de uno o varios adaptadores de red debido a un error interno | Intente crear una evaluación para el grupo.


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


## <a name="vcenter-errors"></a>Errores de vCenter

### <a name="error-unhandledexception-internal-error-occured-systemiofilenotfoundexception"></a>Error UnhandledException Error interno: System.IO.FileNotFoundException

Se trata de un problema que se observa en las versiones del recopilador anteriores a la 1.0.9.5. Si tiene una versión del recopilador 1.0.9.2 o una de las versiones anteriores a la disponibilidad general, como la 1.0.8.59, se encontrará con este problema. Siga este [enlace a los foros para una respuesta más detallada](https://social.msdn.microsoft.com/Forums/azure/en-US/c1f59456-7ba1-45e7-9d96-bae18112fb52/azure-migrate-connect-to-vcenter-server-error?forum=AzureMigrate).

[Actualice el recopilador para corregir el problema](https://aka.ms/migrate/col/checkforupdates).

### <a name="error-unabletoconnecttoserver"></a>Error UnableToConnectToServer

No se puede conectar con vCenter Server "Servername.com:9443" debido al error: No hay ningún punto de conexión escuchando en https://Servername.com:9443/sdk que pudiera aceptar el mensaje.

Esto sucede cuando la máquina del recopilador no puede resolver el nombre del servidor de vCenter especificado o el puerto especificado es incorrecto. De forma predeterminada, si no se especifica el puerto, el recopilador intentará conectar con el número de puerto 443.

1. Intente hacer ping a Servername.com desde el equipo del recopilador.
2. Si se produce un error en el paso 1, intente conectarse al servidor de vCenter a través de la dirección IP.
3. Identifique el número de puerto correcto para conectarse a vCenter.
4. Por último, compruebe si el servidor de vCenter está en funcionamiento.

## <a name="collector-error-codes-and-recommended-actions"></a>Códigos de error del recopilador y acciones recomendadas

|           |                                |                                                                               |                                                                                                       |                                                                                                                                            | 
|-----------|--------------------------------|-------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------| 
| Código de error | Nombre del error                      | Message                                                                       | Causas posibles                                                                                        | Acción recomendada                                                                                                                          | 
| 601       | CollectorExpired               | El recopilador expiró.                                                        | Recopilador expirado.                                                                                    | Descargue una versión más reciente del recopilador y vuelva a intentarlo.                                                                                      | 
| 751       | UnableToConnectToServer        | No se puede conectar con vCenter Server "%Name;" debido al siguiente error: %ErrorMessage;.     | Para obtener más detalles, consulte el mensaje de error.                                                             | Resuelva el problema y vuelva a intentarlo.                                                                                                           | 
| 752       | InvalidvCenterEndpoint         | El servidor "%Name;" no es ninguna instancia de vCenter Server.                                  | Proporcione los detalles de vCenter Server.                                                                       | Vuelva a intentar la operación con los detalles correctos de vCenter Server.                                                                                   | 
| 753       | InvalidLoginCredentials        | No se puede conectar con vCenter Server "%Name;" debido al siguiente error: %ErrorMessage;. | Se produjo un error de conexión a vCenter Server porque las credenciales de inicio de sesión no son válidas.                             | Asegúrese de que las credenciales de inicio de sesión proporcionadas sean correctas.                                                                                    | 
| 754       | NoPerfDataAvaialable           | Los datos de rendimiento no están disponibles.                                               | Compruebe el nivel de estadísticas en vCenter Server. Se debe establecer en 3 para que los datos de rendimiento estén disponibles. | Cambie el nivel de estadísticas a 3 (para una duración de 5 minutos, 30 minutos y 2 horas) y vuelva a intentarlo después de esperar, al menos, un día.                   | 
| 756       | NullInstanceUUID               | Se encontró una máquina con un valor de InstanceUUID null                                  | Puede que vCenter Server tenga un objeto inadecuado.                                                      | Resuelva el problema y vuelva a intentarlo.                                                                                                           | 
| 757       | VMNotFound                     | No se encuentra la máquina virtual                                                  | La máquina virtual se puede eliminar: %VMID;                                                                | Asegúrese de que las máquinas virtuales seleccionadas al definir el ámbito del inventario de vCenter existan durante la detección.                                      | 
| 758       | GetPerfDataTimeout             | Se agotó el tiempo de espera de la solicitud de VCenter. Mensaje %Message;                                  | Las credenciales de vCenter Server no son correctas.                                                              | Compruebe las credenciales de vCenter Server y asegúrese de que vCenter Server esté accesible. Vuelva a intentar la operación. Si el problema persiste, póngase en contacto con el soporte técnico. | 
| 759       | VmwareDllNotFound              | No se encontró el archivo DLL VMWare.Vim.                                                     | PowerCLI no está instalado correctamente.                                                                   | Compruebe si PowerCLI está instalado correctamente. Vuelva a intentar la operación. Si el problema persiste, póngase en contacto con el soporte técnico.                               | 
| 800       | ServiceError                   | El servicio Azure Migrate Collector no se está ejecutando.                               | El servicio Azure Migrate Collector no se está ejecutando.                                                       | Use services.msc para iniciar el servicio y vuelva a intentar la operación.                                                                             | 
| 801       | PowerCLIError                  | Error en la instalación de PowerCLI de VMware.                                          | Error en la instalación de PowerCLI de VMware.                                                                  | Vuelva a intentar la operación. Si el problema persiste, instálelo manualmente y vuelva a intentar la operación.                                                   | 
| 802       | TimeSyncError                  | La hora no está sincronizada con la del servidor horario de Internet.                            | La hora no está sincronizada con la del servidor horario de Internet.                                                    | Asegúrese de que la hora del reloj esté bien definida para la zona horaria de la máquina y vuelva a intentar la operación.                                 | 
| 702       | OMSInvalidProjectKey           | Se especificó una clave de proyecto no válida.                                                | Se especificó una clave de proyecto no válida.                                                                        | Reintente la operación con la clave de proyecto correcta.                                                                                              | 
| 703       | OMSHttpRequestException        | Error durante el envío de la solicitud. Mensaje %Message;                                | Compruebe el id. y la clave del proyecto para asegurarse de que se puede acceder al punto de conexión.                                       | Vuelva a intentar la operación. Si el problema persiste, póngase en contacto con el Soporte técnico de Microsoft.                                                                     | 
| 704       | OMSHttpRequestTimeoutException | Se agotó el tiempo de espera de la solicitud HTTP. Mensaje %Message;                                     | Compruebe el id. y la clave del proyecto y asegúrese de que se pueda acceder al punto de conexión.                                       | Vuelva a intentar la operación. Si el problema persiste, póngase en contacto con el Soporte técnico de Microsoft.                                                                     | 
