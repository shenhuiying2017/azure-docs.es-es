<properties 
   pageTitle="Visualización y administración de alertas de StorSimple | Microsoft Azure"
   description="Describe las condiciones de alerta y gravedad de StorSimple, cómo configurar notificaciones de alerta y cómo usar el servicio Administrador de StorSimple para administrar las alertas."
   services="storsimple"
   documentationCenter="NA"
   authors="SharS"
   manager="carolz"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="09/15/2015"
   ms.author="v-sharos" />

# Usar el servicio de Administrador de StorSimple para ver y administrar alertas de StorSimple

## Información general

La pestaña **Alertas** del servicio de Administrador de StorSimple proporciona una forma de revisar y borrar alertas relacionadas con el dispositivo StorSimple en tiempo real. Desde esta pestaña, puede supervisar de forma centralizada el estado de sus dispositivos StorSimple y la solución general Microsoft Azure StorSimple.

En este tutorial se describen las condiciones de alerta comunes, los niveles de gravedad de alerta y cómo configurar las notificaciones de alerta. Además, incluye tablas de referencia rápida de alertas, que le permiten localizar una alerta específica rápidamente y responder de forma adecuada.

![Página de alertas](./media/storsimple-manage-alerts/HCS_AlertsPage.png)

## Condiciones de alerta comunes

El dispositivo StorSimple genera alertas en respuesta a una serie de condiciones. A continuación se detallan los tipos más comunes de condiciones de alerta:

- **Problemas de hardware** – Estas alertas le informan sobre el estado del hardware. Le permiten saber si son necesarias actualizaciones de firmware, si una interfaz de red tiene problemas o si existe un problema con alguno de sus discos de datos.

- **Problemas de conectividad** – Estas alertas se producen cuando hay dificultades para transferir datos entre orígenes de datos. Los problemas de comunicación pueden ocurrir durante la transferencia de datos a y de la cuenta de almacenamiento de Azure o debido a la falta de conectividad entre los dispositivos y el servicio de Administrador de StorSimple. Pueden ocurrir durante los procesos de configuración, copias de seguridad o restauración. Los problemas de comunicación están entre los más difíciles de solucionar porque existen numerosos puntos de error. En primer lugar, siempre debe verificar que estén disponibles la conectividad de la red y el acceso a Internet antes de pasar a soluciones de problemas más avanzados.

- **Problemas de rendimiento** – Estas alertas se producen cuando el sistema no tiene un rendimiento óptimo; por ejemplo, cuando está sobrecargado.

Además, podría ver alertas relacionadas con seguridad, actualizaciones o errores en el trabajo.

## Niveles de gravedad de alerta

Las alertas tienen distintos niveles de gravedad, según el impacto que tendrá la situación de alerta y la necesidad de dar respuesta a la misma. Los niveles de gravedad son:

- **Crítica** – Esta alerta es en respuesta a una condición que está afectando el rendimiento correcto del sistema. Se requiere una acción para asegurar que el servicio de StorSimple no se vea interrumpido.

- **Advertencia** – Esta condición puede convertirse en crítica si no se resuelve. Debe investigar la situación y tomar las acciones necesarias para resolver el problema.

- **Información** – Esta alerta contiene información que puede ser útil para el seguimiento y administración del sistema.

## Configurar alertas

Puede elegir si desea recibir una notificación por correo electrónico de las condiciones de alerta para cada uno de los dispositivos StorSimple. Además, puede identificar otros destinatarios de las notificaciones de alerta escribiendo sus direcciones de correo electrónico en el recuadro **OTROS DESTINATARIOS DE CORREO ELECRTRÓNICO**, separadas por punto y coma.

>[AZURE.NOTE]Puede escribir un máximo de 20 direcciones de correo electrónico por dispositivo.

Después de habilitar la notificación por correo electrónico para un dispositivo, los miembros de la lista de notificación recibirán un mensaje de correo electrónico cada vez que se produzca una alerta crítica. Los mensajes se enviarán desde **storsimple-alerts-noreply@mail.windowsazure.com* y describirán la condición de alerta. Los destinatarios pueden hacer clic en **Eliminar suscripción** para quitarse de la lista de notificación por correo electrónico.

#### Para habilitar la notificación de alertas por correo electrónico de un dispositivo

1. Vaya a **Dispositivos** > **Configurar** en el dispositivo.

2. En **Configuración de alertas**, establezca lo siguiente:

    1. En el campo **ENVIAR NOTIFICACIÓN POR CORREO ELECTRÓNICO**, seleccione **SÍ**.

    2. En el campo **ADMINISTRADORES DE SERVICIOS DE CORREO ELECTRÓNICO**, seleccione **SÍ** si desea que el administrador de servicios y todos los coadministradores reciban las notificaciones de alerta.

    3. En el campo **OTROS DESTINATARIOS DE CORREO ELECTRÓNICO**, introduzca las direcciones de correo electrónico de los demás destinatarios que deben recibir las notificaciones de alerta. Escriba los nombres en el formato **someone@somewhere.com*. Utilice punto y coma para separar las direcciones de correo electrónico. Puede configurar un máximo de 20 direcciones de correo electrónico por dispositivo.

    ![Página de configuración de notificación de alertas](./media/storsimple-manage-alerts/HCS_AlertNotificationConfig.png)

3. Para enviar una notificación de prueba por correo electrónico, haga clic en el icono de flecha junto a **ENVIAR CORREO ELECTRÓNICO DE PRUEBA**. El servicio Administrador de StorSimple mostrará mensajes de estado mientras envía la notificación de prueba.

4. Cuando aparezca el siguiente mensaje, haga clic en **Aceptar**.

    ![Correo electrónico de notificación de alertas de prueba enviado](./media/storsimple-manage-alerts/HCS_AlertNotificationConfig3.png)

    >[AZURE.NOTE]Si el mensaje de notificación de prueba no puede enviarse, el servicio Administrador de StorSimple mostrará un mensaje adecuado. Esto puede ocurrir debido al tráfico u otros problemas de red. Haga clic en **Aceptar** y, después de algunos minutos, intente volver a enviar el mensaje de notificación de prueba.

## Ver y realizar un seguimiento de las alertas

El panel del servicio Administrador de StorSimple le proporciona un rápido vistazo del número de alertas en sus dispositivos, desglosadas por nivel de gravedad.

![Panel de alertas](./media/storsimple-manage-alerts/admin_alerts_dashboard.png)

Al hacer clic en el nivel de gravedad, se abre la pestaña **Alertas**. Los resultados solo incluyen las alertas que coinciden con ese nivel de gravedad.

![Informe de alertas enfocado al tipo de alerta](./media/storsimple-manage-alerts/admin_alerts_scoped.png)

Al hacer clic en la lista, se muestran detalles adicionales de la alerta, incluyendo la última vez que se emitió la alerta, el número de repeticiones de la alerta en el dispositivo y la acción recomendada para resolver la alerta. Si se trata de una alerta de hardware, también identificará el componente de hardware.

![Ejemplo de alerta de hardware](./media/storsimple-manage-alerts/admin_alerts_hardware.png)

Puede copiar los detalles de la alerta en un archivo de texto si desea realizar un seguimiento de la resolución de la misma en un archivo separado o si necesita enviar la información al soporte técnico de Microsoft. Después de haber resuelto la condición de alerta, debe borrar la alerta del dispositivo seleccionando la misma en la pestaña **Alertas** y haciendo clic en **Borrar**. Para borrar varias alertas, presione la tecla Ctrl mientras selecciona las alertas y, a continuación, haga clic en **Borrar**. Tenga en cuenta que algunas alertas se eliminan automáticamente cuando se resuelve el problema o cuando el sistema actualiza la alerta con nueva información.

Al hacer clic en **Borrar**, tendrá la posibilidad de proporcionar comentarios sobre la alerta y los pasos llevados a cabo para resolver el problema. El sistema borrará algunos eventos si se desencadena otro evento con información nueva. En ese caso, verá el siguiente mensaje.

![Borrar mensaje de alerta](./media/storsimple-manage-alerts/admin_alerts_system_clear.png)

## Clasificar y revisar alertas

Quizá le resulte más eficiente ejecutar informes de alertas que le permitan revisar y borrar las mismas en grupos. Además, la pestaña **Alertas** puede mostrar hasta 250 alertas. Si se ha superado ese número de alertas, no todas las alertas se mostrarán en la vista predeterminada. Puede combinar los siguientes campos para personalizar las alertas que se muestran:

- **Estado**: Puede mostrar alertas **Activas** o **Desactivadas**. Las alertas activas se siguen desencadenando en el sistema, mientras que las alertas desactivadas han sido borradas de forma manual por un administrador o se han borrado de forma programada porque el sistema actualizó la condición de alerta con nueva información.

- **Gravedad**: Puede mostrar las alertas de todos los niveles de gravedad (crítica, advertencia, información), o solo las de determinada gravedad, como las alertas críticas únicamente.

- **Origen**: Puede mostrar las alertas de todos los orígenes, o limitar las alertas procedentes del servicio, o de uno o todos los dispositivos.

- **Intervalo de tiempo**: Al especificar las fechas **Desde** y **Hasta** y las marcas de tiempo, puede ver las alertas durante el período de tiempo que le interesa.

## Referencia rápida de alertas

Las siguientes tablas enumeran algunas de las alertas de Microsoft Azure StorSimple que pueden encontrarse, así como información adicional y recomendaciones donde estén disponibles. Las alertas del dispositivo StorSimple se clasifican en alguna de las siguientes categorías:

- [Alertas de conectividad de la nube](#cloud-connectivity-alerts)

- [Alertas de clúster](#cluster-alerts)

- [Alertas de recuperación ante desastres](#disaster-recovery-alerts)

- [Alertas de hardware](#hardware-alerts)

- [Alertas de errores de trabajo](#job-failure-alerts)

- [Alertas de rendimiento](#performance-alerts)

- [Alertas de seguridad](#security-alerts)

- [Alertas de paquetes de soporte](#support-package-alerts)

- [Alertas de prueba](#test-alerts)

- [Alertas de actualización](#update-alerts)

### Alertas de conectividad de la nube

|Texto de la alerta|Evento|Más información / acciones recomendadas|
|:---|:---|:---|
|La conectividad a <*nombre de credencial en la nube*> no puede establecerse.|No es posible conectarse a la cuenta de almacenamiento.|Parece que podría existir un problema de conectividad con su dispositivo. Ejecute el cmdlet `Test-HcsmConnection` desde la interfaz de Windows PowerShell para StorSimple en su dispositivo para identificar y corregir el problema. Si la configuración es correcta, puede que el problema sea con las credenciales de la cuenta de almacenamiento por la que se desencadenó la alarma. En este caso, use el cmdlet `Test-HcsStorageAccountCredential` para determinar si existen problemas que puede resolver.<ul><li>Compruebe la configuración de red.</li><li>Compruebe sus credenciales de cuenta de almacenamiento.</li></ul>|
|No hemos recibido ningún latido desde su dispositivo durante los últimos <*número*> minutos.|No es posible conectarse al dispositivo.|Parece que existe un problema de conectividad con su dispositivo. Use el cmdlet `Test-HcsmConnection` desde la interfaz de Windows PowerShell para StorSimple en su dispositivo para identificar y corregir el problema, o póngase en contacto con su administrador de red.|

### Comportamiento de StorSimple cuando se produce un error de conectividad de la nube

¿Qué sucede si se produce un error de conectividad de la nube para mi dispositivo StorSimple que se ejecuta en producción?

Si se produce un error en la conectividad de la nube en el dispositivo de producción de StorSimple, según el estado del dispositivo, puede ocurrir lo siguiente:

- **Para los datos locales en el dispositivo**: no se producirá ninguna interrupción y las lecturas se seguirán atendiendo. Sin embargo, cuando el número de operaciones de E/S pendientes aumenta y excede el límite, las lecturas pueden comenzar a fallar. 

	Según la cantidad de datos en las capas locales del dispositivo, las escrituras seguirán también produciéndose para las primeras horas después de la interrupción de la conectividad de la nube. Las escrituras se ralentizarán y finalmente comenzarán a fallar si se interrumpe la conectividad de la nube durante varias horas.

 
- **Para los datos en la nube**: en la mayoría de los errores de conectividad de nube, se devuelve un error. Una vez restaurada la conectividad, se reanudarán las operaciones de E/S sin que el usuario tiene que incorporar el volumen en línea. En raras ocasiones, podría ser necesaria la intervención del usuario para recuperar el volumen en línea desde el Portal de Azure.
 
- **Para las instantáneas en la nube en curso**: la operación se reintenta varias veces en 4 o 5 horas y, si no se restaura la conectividad, se producirá un error en las instantáneas en la nube.


### Alertas de clúster

|Texto de la alerta|Evento|Más información / acciones recomendadas|
|:---|:---|:---|
|Conmutación por error de dispositivo a <*nombre de dispositivo*>.|El dispositivo está en modo de mantenimiento.|Conmutación por error de dispositivo debido a entrada o salida del modo de mantenimiento. Esto es normal y no se requiere ninguna acción. Una vez confirmada esta alerta, bórrela de la página de alertas.|
|Conmutación por error de dispositivo a <*nombre de dispositivo*>.|Se actualizó recientemente el firmware o software del dispositivo.|Se produjo una conmutación por error de clúster debido a una actualización. Esto es normal y no se requiere ninguna acción. Una vez confirmada esta alerta, bórrela de la página de alertas.|
|Conmutación por error de dispositivo a <*nombre de dispositivo*>.|Se apagó o reinició el controlador.|Conmutación por error de dispositivo debido al apagado o reinicio del controlador por parte de un administrador. No se requiere ninguna acción. Una vez confirmada esta alerta, bórrela de la página de alertas.|
|Conmutación por error de dispositivo a <*nombre de dispositivo*>.|Conmutación por error planeada.|Verifique que se trató de una conmutación por error planeada. Una vez tomadas las acciones apropiadas, borre esta alerta de la página de alertas.|
|Conmutación por error de dispositivo a <*nombre de dispositivo*>.|Conmutación por error no planeada.|StorSimple está diseñado para recuperarse automáticamente de las conmutaciones por error no planeadas. Si ve un gran número de estas alertas, póngase en contacto con el soporte técnico de Microsoft.|
|Conmutación por error de dispositivo a <*nombre de dispositivo*>.|Otras causas/causas desconocidas.|Si ve un gran número de estas alertas, póngase en contacto con el soporte técnico de Microsoft. Una vez resuelto el problema, borre esta alerta de la página de alertas.|

### Alertas de recuperación ante desastres

|Texto de la alerta|Evento|Más información / acciones recomendadas|
|:---|:---|:---|
|Las operaciones de recuperación no lograron restaurar todos los valores de configuración para este servicio. Los datos de configuración de dispositivo están en un estado incoherente para algunos dispositivos.|Incoherencia de datos detectada después de la recuperación ante desastres.|Los datos cifrados en el servicio no están sincronizados con los del dispositivo. Autorice al dispositivo <*nombre de dispositivo*> desde el Administrador de StorSimple a iniciar el proceso de sincronización. Use la interfaz de Windows PowerShell para StorSimple para ejecutar el cmdlet `Restore-HcsmEncryptedServiceData` en dispositivo <*nombre de dispositivo*>, proporcionando la contraseña anterior como entrada para este cmdlet para restaurar el perfil de seguridad. Después ejecute el cmdlet `Invoke-HcsmServiceDataEncryptionKeyChange` para actualizar la clave de cifrado de datos del servicio. Una vez tomadas las acciones apropiadas, borre esta alerta de la página de alertas.|
|El servicio ha conmutado por error a un centro de datos secundario debido a un error inesperado.|Otras causas/causas desconocidas.|Debe verificar los valores de configuración en el Administrador de StorSimple para continuar. Una vez tomadas las acciones apropiadas, borre esta alerta de la página de alertas. Para obtener más información, sobre el Administrador de StorSimple, consulte [Utilizar el servicio de Administrador de StorSimple para administrar su dispositivo StorSimple](storsimple-manager-service-administration.md).|

### Alertas de hardware

|Texto de la alerta|Evento|Más información / acciones recomendadas|
|:---|:---|:---|
|El componente de hardware <*Id. de componente*> informa el estado como <*estado*>.||A veces las condiciones temporales pueden causar estas alertas. Si es el caso, esta alerta se borrará automáticamente después de un tiempo. Si el problema persiste, póngase en contacto con el servicio de soporte técnico de Microsoft.|
|Funcionamiento incorrecto del controlador pasivo.|El controlador pasivo (secundario) no funciona.|El dispositivo está operativo, pero uno de los controladores no funciona. Intente reiniciar ese controlador. Si el problema persiste, póngase en contacto con el soporte técnico de Microsoft.|

### Alertas de errores de trabajo

|Texto de la alerta|Evento|Más información / acciones recomendadas|
|:---|:---|:---|
|Error de copia de seguridad de <*Id. de grupo de volúmenes de origen*>|Error de trabajo de copia de seguridad.|Podrían existir problemas de conectividad que impiden que la operación de copia de seguridad se complete correctamente. Si no hay problemas de conectividad, podría haber alcanzado el número máximo de copias de seguridad. Elimine las copias de seguridad que ya no sean necesarias y vuelva a intentar la operación. Una vez tomadas las acciones apropiadas, borre esta alerta de la página de alertas.|
|Error de clonación de <*Id. de elemento de copia de seguridad de origen*> a <*números de serie de volúmenes de destino*>.|Error de trabajo de clonación.|Actualice la lista de copias de seguridad para verificar que la copia de seguridad siga siendo válida. Si la copia de seguridad es válida, es posible que existan problemas de conectividad de nube que impidan que la operación de clonación se complete correctamente. Si no hay problemas de conectividad, podría haber alcanzado el límite de almacenamiento. Elimine las copias de seguridad que ya no sean necesarias y vuelva a intentar la operación. Una vez tomadas las acciones apropiadas para resolver el problema, borre esta alerta de la página de alertas.|
|Error de restauración de <*Id. de elemento de copia de seguridad de origen*>.|Error de trabajo de restauración.|Actualice la lista de copias de seguridad para verificar que la copia de seguridad siga siendo válida. Si la copia de seguridad es válida, es posible que existan problemas de conectividad de nube que impidan que la operación de restauración se complete correctamente. Si no hay problemas de conectividad, podría haber alcanzado el límite de almacenamiento. Elimine las copias de seguridad que ya no sean necesarias y vuelva a intentar la operación. Una vez tomadas las acciones apropiadas para resolver el problema, borre esta alerta de la página de alertas.|

### Alertas de rendimiento

|Texto de la alerta|Evento|Más información / acciones recomendadas|
|:---|:---|:---|
|La carga del dispositivo ha superado <*umbral*>.|Tiempos de respuesta más lentos que lo esperado.|El dispositivo indica su utilización con una carga elevada de entrada/salida. Esto podría provocar que el dispositivo no funcione tan bien como debería. Revise las cargas de trabajo que ha conectado al dispositivo y determine si algunas pueden moverse a otro dispositivo o ya no son necesarias.|

### Alertas de seguridad

|Texto de la alerta|Evento|Más información / acciones recomendadas|
|:---|:---|:---|
|Se ha iniciado una sesión de soporte técnico de Microsoft Support.|Un tercero ha accedido a la sesión de soporte técnico.|Confirme que este acceso está autorizado. Una vez tomadas las acciones apropiadas, borre esta alerta de la página de alertas.|
|La contraseña de <*elemento*> caducará en <*período de tiempo*>.||Cambiar la contraseña antes de que caduque.|
|Falta información de configuración de seguridad de <*Id. de elemento*>.||Los volúmenes asociados con este contenedor de volúmenes no pueden utilizarse para replicar la configuración de StorSimple. Para asegurarse de que sus datos se almacenan de forma segura, recomendamos eliminar el contenedor de volúmenes y los volúmenes asociados con el contenedor de volúmenes. Una vez tomadas las acciones apropiadas, borre esta alerta de la página de alertas.|
|<*número*> intentos de inicio de sesión erróneos en <*Id. de elemento*>.|Múltiples intentos de inicio de sesión erróneos.|Su dispositivo puede estar bajo ataque o un usuario autorizado está intentando conectarse con una contraseña incorrecta.<ul><li>Póngase en contacto con los usuarios autorizados y verifique que estos intentos proceden de un origen legítimo. Si continúa viendo un gran número de intentos de inicio de sesión erróneos, considere la posibilidad de deshabilitar la administración remota y de ponerse en contacto con su administrador de red. Una vez tomadas las acciones apropiadas, borre esta alerta de la página de alertas.</li><li>Compruebe que las instancias del Administrador de instantáneas estén configuradas con la contraseña correcta. Una vez tomadas las acciones apropiadas, borre esta alerta de la página de alertas.</li></ul>|
|Se ha producido uno o más errores al cambiar la clave de cifrado de datos del servicio.||Se encontraron errores al cambiar la clave de cifrado de datos del servicio. Cuando haya solucionado las condiciones de error, ejecute el cmdlet `Invoke-HcsmServiceDataEncryptionKeyChange` desde la interfaz de Windows PowerShell para StorSimple en su dispositivo para actualizar el servicio. Si el problema persiste, póngase en contacto con el servicio de soporte técnico de Microsoft. Una vez que resolvió el problema, borre esta alerta de la página de alertas.|

### Alertas de paquetes de soporte

|Texto de la alerta|Evento|Más información / acciones recomendadas|
|:---|:---|:---|
|Error de creación de paquete de soporte técnico.|StorSimple no pudo generar el paquete.|Vuelva a intentarlo. Si el problema persiste, póngase en contacto con el servicio de soporte técnico de Microsoft. Una vez que ha resuelto el problema, borre esta alerta de la página de alertas.|

### Alertas de prueba

|Texto de la alerta|Evento|Más información / acciones recomendadas|
|:---|:---|:---|
|Este es un mensaje de prueba enviado desde su dispositivo StorSimple. Su administrador de StorSimple le ha agregado como destinatario de las notificaciones de alerta para el dispositivo: <*nombre de dispositivo*>.|Correo electrónico de notificación de alerta de prueba.|Si cree que ha recibido este mensaje por error, póngase en contacto con su administrador de StorSimple.|

### Alertas de actualización

|Texto de la alerta|Evento|Más información / acciones recomendadas|
|:---|:---|:---|
|Revisión instalada.|Actualización de software o firmware completada.|La revisión se instaló correctamente en el dispositivo.|
|Actualizaciones manuales disponibles.|Notificación de actualizaciones disponibles.|Utilice la interfaz de Windows PowerShell para StorSimple de su dispositivo para instalar estas actualizaciones.|
|Nuevas actualizaciones disponibles.|Notificación de actualizaciones disponibles.|Puede instalar estas actualizaciones desde la página **Mantenimiento** o mediante la interfaz de Windows PowerShell para StorSimple de su dispositivo.|
|Error al instalar las actualizaciones.|Las actualizaciones no se instalaron correctamente.|El sistema no pudo instalar las actualizaciones. Puede instalar estas actualizaciones desde la página **Mantenimiento** o mediante la interfaz de Windows PowerShell para StorSimple de su dispositivo. Si el problema persiste, póngase en contacto con el servicio de soporte técnico de Microsoft.|
|No se puede comprobar automáticamente si hay nuevas actualizaciones.|Error de comprobación automática.|Puede comprobar manualmente si hay nuevas actualizaciones desde la página **Mantenimiento**.|
|Nuevo agente WUA disponible.|Notificación de actualización disponible.|Descargue la versión más reciente del Agente de Windows Update e instálela desde la interfaz de Windows PowerShell.|
|La versión del componente de firmware <*Id. de componente*> no coincide con el hardware.|Las actualizaciones de firmaware no se instalaron correctamente.|Póngase en contacto con el soporte técnico de Microsoft.|

## Pasos siguientes

- [Obtenga más información sobre los errores de StorSimple](storsimple-troubleshoot-operational-device.md).
- Obtenga más información sobre el [uso del servicio StorSimple Manager para administrar su dispositivo StorSimple](storsimple-manager-service-administration.md).

<!---HONumber=Oct15_HO3-->