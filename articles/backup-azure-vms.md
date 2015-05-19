<properties
	pageTitle="Copia de seguridad de máquinas virtuales de Azure: copia de seguridad"
	description="Aprenda cómo hacer una copia de seguridad de una máquina virtual de Azure después del registro"
	services="backup"
	documentationCenter=""
	authors="aashishr"
	manager="shreeshd"
	editor=""/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/30/2015"
	ms.author="aashishr"/>


# Copia de seguridad de máquinas virtuales de Azure

Este artículo es la guía esencial para realizar copias de seguridad de máquinas virtuales de Azure. Antes de continuar, asegúrese de que todos los [requisitos previos][prereq] se han cumplido.

La realización de copias de seguridad de máquinas virtuales de Azure consta tres pasos principales:

![Tres pasos para hacer una copia de seguridad de una máquina virtual de Azure][three-steps]

## Detección de máquinas virtuales de Azure
El proceso de detección realiza una consulta a Azure para obtener la lista de máquinas virtuales de la suscripción, junto con información adicional como el nombre del servicio en la nube y la región.

> [AZURE.NOTE]Siempre se debe ejecutar el proceso de detección como el primer paso. De este modo se asegura de que se identifican las máquinas virtuales nuevas que se agregan a la suscripción.

Para desencadenar el proceso de detección, siga estos pasos:

1. Vaya al almacén de copia de seguridad, que se encuentra en **Servicios de recuperación** en el portal de Azure y haga clic en la pestaña **Elementos registrados**.

2. Elija el tipo de carga de trabajo en el menú desplegable como **Máquina virtual de Azure**, y haga clic en el botón **Seleccionar**. ![seleccionar carga de trabajo][select-workload]

3. Haga clic en el botón **Detectar** en la parte inferior de la página. ![botón Detectar][discover-button]

4. El proceso de detección puede ejecutarse durante varios minutos mientras se tabulan las máquinas virtuales. Aparece una notificación del sistema en la parte inferior de la pantalla mientras se ejecuta el proceso de detección. ![detectar máquinas virtuales][discover-vms]

5. Una vez completado el proceso de detección, aparece una notificación del sistema. ![detectar-completado][discover-done]

<br><br>
## Registro de máquinas virtuales de Azure
Antes de poder proteger una máquina virtual, esta debe estar registrada en el servicio Copia de seguridad de Azure. El proceso de registro tiene dos objetivos principales:

1. Conectar la extensión de copia de seguridad en el agente de VM en la máquina virtual

2. Asociar la máquina virtual con el servicio Copia de seguridad de Azure.

El registro suele ser una actividad de una vez. El servicio Copia de seguridad de Azure controla perfectamente la actualización y revisión de la extensión de copia de seguridad sin necesidad de intervención incómoda del usuario. Esto libera al usuario de la "sobrecarga de administración de agente" que normalmente se asocia con productos de copia de seguridad.

### Registro de las máquinas virtuales

1. Vaya al almacén de copia de seguridad, que se encuentra en **Servicios de recuperación** en el portal de Azure y haga clic en la pestaña **Elementos registrados**.

2. Elija el tipo de carga de trabajo en el menú desplegable como **Máquina virtual de Azure** y haga clic en el botón Seleccionar. ![seleccionar carga de trabajo][select-workload] 

3. Haga clic en el botón **Registrar** en la parte inferior de la página. ![botón Registrar][register-button]

4. En la ventana emergente **Elementos registrados**, elija las máquinas virtuales que desea registrar. Si hay dos o más máquinas virtuales con el mismo nombre, use el servicio en la nube para distinguir las máquinas virtuales.

  	La operación **Registrar** puede realizarse a escala, lo que significa que se pueden seleccionar a la vez varias máquinas virtuales para registrar. Esto reduce en gran medida el esfuerzo único realizado para preparar la máquina virtual para la copia de seguridad.

  	> [AZURE.NOTE]Aquí solo se mostrarán las máquinas virtuales que no están registradas y que están en la misma región que el almacén de copia de seguridad.

  	![Lista de máquinas virtuales que se van a registrar][register-vms]

5. Se crea un trabajo para cada máquina virtual que se debe registrar. La notificación del sistema muestra el estado de esta actividad. Haga clic en **Ver trabajo** para ir a la página **Trabajos**. ![registrar trabajo][register-job]

6. La máquina virtual también aparece en la lista de elementos registrados y se muestra el estado de la operación de registro. ![Registrando estado 1][register-status1]

7. Una vez completada la operación, se cambiará el estado en el portal para reflejar el estado registrado. ![Registrando estado 2][register-status2]

<br><br>
## Copia de seguridad de máquinas virtuales de Azure
Este paso implica configurar una directiva de copia de seguridad y retención para la máquina virtual. Para proteger una máquina virtual, siga estos pasos:

1. Vaya al almacén de copia de seguridad, que se encuentra en **Servicios de recuperación** en el portal de Azure y haga clic en la pestaña **Elementos registrados**.

2. Elija el tipo de carga de trabajo en el menú desplegable como **Máquina virtual de Azure** y haga clic en el botón Seleccionar. ![Seleccionar carga de trabajo en el portal][select-workload] 

3. Haga clic en el botón **Proteger** en la parte inferior de la página.

4. Se abrirá el asistente **Proteger elementos**, donde puede seleccionar las máquinas virtuales que se protegerán. Si hay dos o más máquinas virtuales con el mismo nombre, use el servicio en la nube para distinguir las máquinas virtuales. La operación **Proteger** puede realizarse a escala, lo que significa que se pueden seleccionar a la vez varias máquinas virtuales para proteger. Esto reduce en gran medida el esfuerzo único realizado para proteger la máquina virtual.

	> [AZURE.NOTE]Aquí solo se mostrarán las máquinas virtuales que se han registrado correctamente con el servicio Copia de seguridad de Azure y que están en la misma región que el almacén de copia de seguridad.

	![Seleccionar elementos para proteger][protect-wizard1]

5. En la segunda pantalla del asistente **Proteger elementos**, elija una directiva de copia de seguridad y retención para hacer una copia de seguridad de las máquinas virtuales seleccionadas. Elija una directa del conjunto de directivas existentes o defina una nueva.

	> [AZURE.NOTE]Para la vista previa, se admiten hasta 30 días de retención y un máximo de una copia de seguridad una vez al día.

	![Seleccionar directiva de protección][protect-wizard2]

	En cada almacén de copia de seguridad, puede tener varias directivas de copia de seguridad. Las directivas reflejan los detalles acerca de cómo se debe programar y retener la copia de seguridad. Por ejemplo, una directiva de copia de seguridad podría ser para copia de seguridad diaria a las 22:00 h, mientras que otra directiva de copia de seguridad podría ser para copia de seguridad semanal a las 6:00 h. Varias directivas de copia de seguridad proporcionan flexibilidad en la programación de copias de seguridad de su infraestructura de máquinas virtuales. Cada directiva de copia de seguridad puede tener varias máquinas virtuales asociadas. La máquina virtual puede asociarse solo con una directiva en cualquier momento.

6. Se crea un trabajo para cada máquina virtual para configurar la directiva de protección y asociar las máquinas virtuales a la directiva. Haga clic en la pestaña **Trabajos** ficha y elija el filtro adecuado para ver la lista de trabajos de **Configurar protección**. ![Configurar trabajo de protección][protect-configure]

7. Una vez completada la acción, las máquinas virtuales se protegen con una directiva y se debe esperar a que se complete el tiempo de copia de seguridad programado para la copia de seguridad inicial. La máquina virtual aparecerá ahora en la pestaña **Elementos protegidos** y tendrá un estado protegido de *Protegido* \(copia de seguridad inicial pendiente\).

	> [AZURE.NOTE]De momento, el inicio de la copia de seguridad inicial inmediatamente después de configurar la protección no está disponible.

8. En el tiempo programado, el servicio Copia de seguridad de Azure crea un trabajo de copia de seguridad para cada máquina virtual de la que se debe hacer una copia de seguridad. Haga clic en la pestaña **Trabajos** para ver la lista de trabajos de **Copia de seguridad**. Como parte de la operación de copia de seguridad, el servicio Copia de seguridad de Azure emite un comando a la extensión de copia de seguridad en cada máquina virtual para vaciar toda la escritura y tomar una instantánea coherente. ![Copia de seguridad en curso][protect-inprogress]

9. Una vez completada la acción, el estado de protección de la máquina virtual en la pestaña **Elementos protegidos** se mostrará como *Protegido*. ![Se realiza una copia de seguridad de la máquina virtual con punto de recuperación][protect-backedup]


## Visualización del estado y detalles de la copia de seguridad
Una vez protegidas, el recuento de máquinas virtuales también aumenta en el resumen de la página **Panel**. Además, la página Panel muestra el número de trabajos de las últimas 24 horas que se realizaron correctamente, que han producido un error y que siguen en curso. Al hacer clic en una categoría, esta se desglosará en la página **Trabajos**. ![Estado de la copia de seguridad en la página Panel][dashboard]


## Solución de errores
Puede solucionar los problemas de detección y el registro con la información siguiente.

| Operación de copia de seguridad | Detalles del error | Solución alternativa |
| -------- | -------- | -------|
| Detección | Se ha producido un error al detectar elementos nuevos: Copia de seguridad de Microsoft Azure ha detectado un error interno. Espere unos minutos y vuelva a intentar la operación. | Después de 15 minutos, vuelva a intentar el proceso de detección.
| Detección | Se ha producido un error al detectar elementos nuevos: ya hay otra operación de detección en curso. Espere hasta que se haya completado la operación de detección. | Espere a que finalice la detección existente. |
| Registro | El rol de VM de Azure no está en un estado para instalar la extensión: compruebe si la máquina virtual está en estado de ejecución. La extensión de Servicios de recuperación de Azure requiere que la máquina virtual se esté ejecutando. | Inicie la máquina virtual y cuando esté en estado en ejecución, vuelva a intentar la operación de registro.|


## Coherencia de los puntos de recuperación
Al tratar los datos de copia de seguridad, a los clientes les preocupa la máquina virtual después de que se haya restaurado. Las preguntas típicas que plantean los clientes son:

+ ¿Arrancará la máquina virtual?

+ ¿Los datos estarán disponibles en el disco o se producirá una pérdida de datos?

+ ¿La aplicación podrá leer los datos o se dañarán los datos?

+ ¿Los datos tendrán sentido para la aplicación o serán coherentes cuando la aplicación los lea?

La siguiente tabla explica los tipos de coherencia que se detectan durante la restauración y la copia de seguridad de máquinas virtuales de Azure:

| Coherencia | Basada en el VSS | Explicación y detalles |
|-------------|-----------|---------|
| Coherencia de las aplicaciones | Sí | Este es el lugar ideal para las cargas de trabajo de Microsoft, ya que garantiza:<ol><li> que la máquina virtual *arranque*, <li>no *se produzca ningún daño*, <li>no se produzca ninguna *pérdida de datos* y<li> los datos sean coherentes con la aplicación que los usa, implicando la aplicación en el momento de la copia de seguridad mediante el uso del VSS.</ol> El servicio de instantáneas de volumen \(VSS\) garantiza que los datos se escriban correctamente en el almacenamiento. La mayoría de cargas de trabajo de Microsoft tienen escritores VSS que realizan acciones específicas de carga de trabajo relacionadas con la coherencia de los datos. Por ejemplo, Microsoft SQL Server tiene un escritor VSS que garantiza que las escrituras en el archivo de registro de transacciones y en la base de datos se realizan correctamente.<br><br> En la copia de seguridad de máquina virtual de Azure, obtener un punto de recuperación coherente con la aplicación significa que la extensión de copia de seguridad pudo invocar el flujo de trabajo VSS y completarlo *correctamente* antes de que se tomase la instantánea de la máquina virtual. Naturalmente, esto significa que los escritores VSS de todas las aplicaciones de la máquina virtual de Azure también se han invocado.<br><br>Obtenga [Aprenda los conceptos básicos de VSS](http://blogs.technet.com/b/josebda/archive/2007/10/10/the-basics-of-the-volume-shadow-copy-service-vss.aspx) y profundice en los detalles de su [funcionamiento](https://technet.microsoft.com/es-es/library/cc785914%28v=ws.10%29.aspx). |
| Coherencia del sistema de archivos | Sí, para máquinas de Windows | Hay dos escenarios donde el punto de recuperación puede ser coherente con el sistema de archivos:<ul><li>copia de seguridad de máquinas virtuales Linux en Azure, ya que Linux no tiene una plataforma equivalente a VSS.<li>Error de VSS durante la copia de seguridad de máquinas virtuales de Windows en Azure.</li></ul> En ambos casos, lo mejor que puede hacer es asegurarse de que: <ol><li> la máquina virtual *arranque*, <li> *no se produzca ningún daño* y <li>no se produzca *ninguna pérdida de datos*.</ol> Las aplicaciones deben implementar su propio mecanismo de "reparación" en los datos restaurados.|
| Coherencia de bloqueos | No | Esta situación es equivalente a aquellos casos en que una máquina experimenta un "bloqueo" \(a través de un restablecimiento parcial o completo\). No hay ninguna garantía en torno a la coherencia de los datos en el medio de almacenamiento. Solamente se capturan y se hace una copia de seguridad de los datos que ya existen en el disco en el momento de la copia de seguridad. <ol><li>Aunque no hay ninguna garantía, en la mayoría de los casos, se iniciará el SO.<li>Normalmente, esto va seguido de un procedimiento de comprobación de disco como chkdsk para corregir los errores por daños.<li> Se perderán los datos o las escrituras en memoria que no se hayan vaciado completamente en el disco.<li> Normalmente, la aplicación sigue con su propio mecanismo de comprobación en caso de que se deba realizar una reversión de datos. </ol>En la copia de seguridad de una máquina virtual de Azure, obtener un punto de recuperación coherente con el bloqueo significa que Copia de seguridad de Azure no ofrece ninguna garantía en torno a la coherencia de los datos en el almacenamiento, ya sea desde el punto de vista del sistema operativo o desde el punto de vista de la aplicación. Esto ocurre normalmente cuando la máquina virtual de Azure se apaga en el momento de la copia de seguridad.<br><br>Por ejemplo, si el registro de transacciones tiene entradas que no están presentes en la base de datos, el software de la base de datos realiza una reversión hasta que los datos sean coherentes. Cuando se trabaja con datos repartidos en varios discos virtuales \(por ejemplo, los volúmenes distribuidos\), un punto de recuperación consistente para fallas proporciona garantías para la corrección de los datos.|



## Pasos siguientes
Para obtener más información acerca de cómo empezar a usar Copia de seguridad de Azure, consulte:

- [Restauración de máquinas virtuales](backup-azure-restore-vms.md)

[three-steps]: ./media/backup-azure-vms/3-steps-for-backup.png
[select-workload]: ./media/backup-azure-vms/discovery-select-workload.png
[discover-button]: ./media/backup-azure-vms/discover-button.png
[discover-vms]: ./media/backup-azure-vms/discovering-vms.png
[discover-done]: ./media/backup-azure-vms/discovery-complete.png
[register-button]: ./media/backup-azure-vms/register-button.png
[register-job]: ./media/backup-azure-vms/register-create-job.png
[register-vms]: ./media/backup-azure-vms/register-popup.png
[register-status1]: ./media/backup-azure-vms/register-status01.png
[register-status2]: ./media/backup-azure-vms/register-status02.png
[select-workload]: ./media/backup-azure-vms/select-workload.png
[protect-wizard1]: ./media/backup-azure-vms/protect-wizard1.png
[protect-wizard2]: ./media/backup-azure-vms/protect-wizard2.png
[protect-configure]: ./media/backup-azure-vms/protect-configureprotection.png
[protect-inprogress]: ./media/backup-azure-vms/protect-inprogress.png
[protect-backedup]: ./media/backup-azure-vms/protect-backedupvm.png
[dashboard]: ./media/backup-azure-vms/dashboard-protectedvms.png
[prereq]: http://azure.microsoft.com/documentation/articles/backup-azure-vms-introduction/#prerequisites

<!--HONumber=54-->