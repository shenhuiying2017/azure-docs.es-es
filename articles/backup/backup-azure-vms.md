<properties
	pageTitle="Copia de seguridad de máquinas virtuales de Azure: copia de seguridad | Microsoft Azure"
	description="Aprenda cómo hacer una copia de seguridad de una máquina virtual de Azure después del registro"
	services="backup"
	documentationCenter=""
	authors="trinadhk"
	manager="shreeshd"
	editor=""/>

<tags ms.service="backup" ms.workload="storage-backup-recovery" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="hero-article" ms.date="09/24/2015" ms.author="trinadhk"; "aashishr"; "jimpark"/>


# Copia de seguridad de máquinas virtuales de Azure
Este artículo es la guía esencial para realizar copias de seguridad de máquinas virtuales de Azure. Antes de continuar, asegúrese de que todos los [requisitos previos](backup-azure-vms-introduction.md#prerequisites) se han cumplido.

La realización de copias de seguridad de máquinas virtuales de Azure consta tres pasos principales:

![Tres pasos para hacer una copia de seguridad de una máquina virtual de Azure](./media/backup-azure-vms/3-steps-for-backup.png)

>[AZURE.NOTE]La copia de seguridad de la máquina virtual es local. El almacén de copia de seguridad de una región de Azure especificada no permitirá realizar copias de seguridad de las máquinas virtuales de otra región de Azure. Por lo tanto, para cada región de Azure que tiene máquinas virtuales que necesiten una copia de seguridad, debe crearse al menos 1 almacén de copia de seguridad de esa región.

## 1\. Detección de máquinas virtuales de Azure
El proceso de detección envía consultas a Azure para obtener la lista de máquinas virtuales incluidas en la suscripción, junto con información adicional, por ejemplo, el nombre del servicio en la nube y la región. Siempre se debe ejecutar el proceso de detección como el primer paso. De este modo se asegura de que se identifican las máquinas virtuales nuevas que se agregan a la suscripción.

### Para desencadenar el proceso de detección

1. Vaya al almacén de copia de seguridad, que puede encontrarse en **Servicios de recuperación** en el Portal de Azure y haga clic en la pestaña **Elementos registrados**.

2. Elija el tipo de carga de trabajo en el menú desplegable como **Máquina virtual de Azure** y haga clic en el botón **Seleccionar**.

    ![seleccionar carga de trabajo](./media/backup-azure-vms/discovery-select-workload.png)

3. Haga clic en el botón **DETECTAR** que se encuentra en la parte inferior de la página. ![botón Detectar](./media/backup-azure-vms/discover-button-only.png)

4. El proceso de detección puede ejecutarse durante varios minutos mientras se tabulan las máquinas virtuales. Aparece una notificación del sistema en la parte inferior de la pantalla mientras se ejecuta el proceso de detección.

    ![detectar máquinas virtuales](./media/backup-azure-vms/discovering-vms.png)

5. Una vez completado el proceso de detección, aparece una notificación del sistema.

    ![detectar-completado](./media/backup-azure-vms/discovery-complete.png)

##  2\. Registro de máquinas virtuales de Azure
Para que se pueda proteger una máquina virtual, esta debe estar registrada en el servicio Copia de seguridad de Azure. El objetivo principal del proceso de registro es asociar la máquina virtual al servicio Copia de seguridad de Azure. El registro suele ser una actividad de una vez.

>[AZURE.NOTE]La extensión de copia de seguridad no se instala durante el paso de registro. La instalación y actualización del agente de copia de seguridad ahora forma parte de la tarea de copia de seguridad programada.

### Para registrar las máquinas virtuales

1. Vaya al almacén de copia de seguridad, que puede encontrarse en **Servicios de recuperación** en el Portal de Azure y haga clic en la pestaña **Elementos registrados**.

2. Elija el tipo de carga de trabajo en el menú desplegable como **Máquina virtual de Azure** y haga clic en el botón Seleccionar.

    ![seleccionar carga de trabajo](./media/backup-azure-vms/discovery-select-workload.png)

3. Haga clic en el botón **REGISTRAR** que se encuentra en la parte inferior de la página.

    ![botón Registrar](./media/backup-azure-vms/register-button-only.png)

4. En el menú contextual **Elementos registrados**, elija las máquinas virtuales que desea registrar. Si hay dos o más máquinas virtuales con el mismo nombre, use el servicio en la nube para distinguir entre las máquinas virtuales.

    La operación **Registrar** puede realizarse a escala, lo que significa que se pueden seleccionar a la vez varias máquinas virtuales para registrar. Esto reduce en gran medida el esfuerzo único realizado para preparar la máquina virtual para la copia de seguridad.

5. Se crea un trabajo para cada máquina virtual que se debe registrar. La notificación del sistema muestra el estado de esta actividad. Haga clic en **Ver trabajo** para ir a la página **Trabajos**.

    ![registrar trabajo](./media/backup-azure-vms/register-create-job.png)

6. La máquina virtual también aparece en la lista de elementos registrados y se muestra el estado de la operación de registro

    ![Registrando estado 1](./media/backup-azure-vms/register-status01.png)

7. Una vez completada la operación, se cambiará el estado en el portal para reflejar el estado registrado.

    ![Registrando estado 2](./media/backup-azure-vms/register-status02.png)

## 3\. Protección: copia de seguridad de máquinas virtuales de Azure
Este paso implica configurar una directiva de copia de seguridad y retención para la máquina virtual. Se pueden proteger varias máquinas virtuales a escala con una sola acción de protección.

>[AZURE.NOTE]Los almacenes de copia de seguridad de Azure creados después de mayo de 2015 incluyen una directiva predeterminada integrada en el almacén. Esta directiva predeterminada viene con un tiempo de retención predeterminado de 30 días y una programación de ejecución de copia de seguridad una vez al día.

Para proteger una máquina virtual, siga estos pasos:

1. Vaya al almacén de copia de seguridad, que puede encontrarse en **Servicios de recuperación** en el Portal de Azure y haga clic en la pestaña **Elementos registrados**.
2. Elija el tipo de carga de trabajo en el menú desplegable como **Máquina virtual de Azure** y haga clic en **Seleccionar**.

    ![Seleccionar carga de trabajo en el portal](./media/backup-azure-vms/select-workload.png)

3. Haga clic en **PROTEGER**, en la parte inferior de la página. Se abrirá el asistente **Proteger elementos**. Este asistente solo muestra las máquinas virtuales que están registradas y no protegidas.

4. En el asistente **Proteger elementos** puede seleccionar las máquinas virtuales que se protegerán. Si hay dos o más máquinas virtuales con el mismo nombre, use el servicio en la nube para distinguir entre las máquinas virtuales.

    La operación **Proteger** puede realizarse a escala, lo que significa que se pueden seleccionar a la vez varias máquinas virtuales para protegerlas. Esto reduce en gran medida el esfuerzo único realizado para proteger la máquina virtual.

    ![Configuración de protección a escala](./media/backup-azure-vms/protect-at-scale.png)

5. En la segunda pantalla del asistente **Proteger elementos**, elija una programación de copia de seguridad para hacer una copia de seguridad de las máquinas virtuales seleccionadas. Elija una directa del conjunto de directivas existentes o defina una nueva.

    En cada almacén de copia de seguridad, puede tener varias directivas de copia de seguridad. Las directivas reflejan los detalles acerca de cómo se debe programar y retener la copia de seguridad. Por ejemplo, una directiva de copia de seguridad podría ser para una copia de seguridad diaria a las 10:00 p.m., mientras que otra directiva de copia de seguridad podría ser para una copia de seguridad semanal cada sábado a las 6:00 a.m. Varias directivas de copia de seguridad proporcionan flexibilidad en la programación de copias de seguridad de su infraestructura de máquinas virtuales.

    Cada directiva de copia de seguridad puede tener varias máquinas virtuales asociadas. La máquina virtual puede asociarse solo con una directiva en cualquier momento.

    ![Protección mediante nueva directiva](./media/backup-azure-vms/policy-schedule.png)

6. En la tercera pantalla del asistente **Proteger elementos**, seleccione un intervalo de retención que se asociará a las copias de seguridad realizadas. Esta pantalla admite un esquema de retención basado en el estándar del sector GFS (Grandfather-Father-Son). Más información sobre [retención a largo plazo](#long-term-retention).

    Una directiva de copia de seguridad también implica un esquema de retención de las copias de seguridad programadas. La selección de una directiva de copia de seguridad existente en la pantalla anterior deshabilita la modificación del esquema de retención y las copias de seguridad siguen la directiva de retención tal como se define en la directiva.

    ![Protección con retención flexible](./media/backup-azure-vms/policy-retention.png)

7. Se crea un trabajo para cada máquina virtual para configurar la directiva de protección y asociar las máquinas virtuales a la directiva. Haga clic en la pestaña **Trabajos** y elija el filtro adecuado para ver la lista de trabajos de **Configurar protección**.

    ![Configurar trabajo de protección](./media/backup-azure-vms/protect-configureprotection.png)


## Actividades posteriores a la protección

### Instalación de la extensión de copia de seguridad
El servicio Copia de seguridad de Azure controla perfectamente la actualización y revisión de la extensión de copia de seguridad sin necesidad de intervención incómoda del usuario. Esto libera al usuario de la "sobrecarga de administración de agente" que está normalmente asociada a productos de copia de seguridad.

#### Máquinas virtuales sin conexión
Si se ejecuta la máquina virtual, se instala la extensión de copia de seguridad. Una máquina virtual en ejecución también proporciona las mayores posibilidades de obtener un punto coherente de la aplicación. Sin embargo, el servicio Copia de seguridad de Azure continuará realizando una copia de seguridad de la máquina virtual, incluso si se desactiva la máquina virtual y la extensión no se pudo instalar (también conocido como máquina virtual sin conexión). El impacto se ve en la coherencia: en este caso, el punto de recuperación será *coherente con el bloqueo*.

### Copia de seguridad inicial
Una vez que la máquina virtual está protegida con una directiva, aparecerá en la pestaña **Elementos protegidos** y tendrá un estado *Protegido (copia de seguridad inicial pendiente)*. De forma predeterminada, la primera copia de seguridad programada es la copia de seguridad inicial. Para activar la copia de seguridad inicial inmediatamente después de configurar la protección, use el botón **Copia de seguridad ahora**, situado en la parte inferior de la página **Elementos protegidos**.

El servicio Copia de seguridad de Azure crea un trabajo de copia de seguridad para la operación de copia de seguridad inicial. Haga clic en la pestaña **Trabajos** para ver la lista de los trabajos. Como parte de la operación de copia de seguridad, el servicio Copia de seguridad de Azure emite un comando a la extensión de copia de seguridad en cada máquina virtual para vaciar toda la escritura y tomar una instantánea coherente.

![Copia de seguridad en curso](./media/backup-azure-vms/protect-inprogress.png)

Una vez completada la copia de seguridad inicial, el *Estado de protección* de la máquina virtual en la pestaña **Elementos protegidos** se mostrará como *Protegido*.

![Se realiza una copia de seguridad de la máquina virtual con punto de recuperación](./media/backup-azure-vms/protect-backedupvm.png)

### Visualización de los detalles y el estado de la copia de seguridad
Una vez protegidas, el recuento de máquinas virtuales también aumenta en el resumen de la página **Panel**. Además, la página **Panel** muestra el número de trabajos de las últimas 24 horas que se realizaron correctamente, que produjeron un error y que siguen en curso. Al hacer clic en una categoría, esta se desglosará en la página **Trabajos**.

![Estado de la copia de seguridad en la página Panel](./media/backup-azure-vms/dashboard-protectedvms.png)

>[AZURE.NOTE]Los valores indicados en el panel se actualizan cada 24 horas.

### Retención a largo plazo
La directiva de retención especifica el tiempo durante el que debe almacenarse la copia de seguridad. En vez de especificar solo una "retención plana" para todos los puntos de copia de seguridad, los clientes pueden especificar directivas de retención diferentes en función de cuándo se realiza la copia de seguridad. Por ejemplo, puede que el punto de copia de seguridad que se toma al final de cada trimestre se tenga que conservar durante más tiempo con fines de auditoría, mientras que el punto de copia de seguridad diario (que sirve como punto de recuperación operativo) debe mantenerse durante 90 días.

![Se realiza una copia de seguridad de la máquina virtual con punto de recuperación](./media/backup-azure-vms/long-term-retention.png)

1. **Directiva de retención diaria**: las copias de seguridad realizadas diariamente se almacenan durante 30 días.
2. **Directiva de retención semanal**: las copias de seguridad realizadas cada domingo se conservarán durante 104 semanas.
3. **Directiva de retención mensual**: las copias de seguridad realizadas el último domingo de cada mes se conservarán durante 120 meses.
4. **Directiva de retención anual**: las copias de seguridad realizadas el primer domingo de cada mes de enero se conservarán durante 99 años.

## Coherencia de los puntos de recuperación
Cuando se trabaja con datos de copia de seguridad, los clientes se preocupan por el comportamiento de la máquina virtual después de haberla restaurado. Las preguntas típicas que plantean los clientes son:

- ¿Arrancará la máquina virtual?
- ¿Los datos estarán disponibles en el disco o se producirá una pérdida de datos?
- ¿La aplicación podrá leer los datos o se dañarán los datos?
- ¿Los datos tendrán sentido para la aplicación o serán coherentes cuando la aplicación los lea?

La siguiente tabla explica los tipos de coherencia que se detectan durante la restauración y la copia de seguridad de máquinas virtuales de Azure.

| Coherencia | Con base en VSS | Explicación y detalles |
|-------------|-----------|---------|
| Coherencia de las aplicaciones | Sí | Este es el lugar ideal para las cargas de trabajo de Microsoft, ya que garantiza que:<ol><li> La máquina virtual *arranca*. <li>No hay *ningún daño*. <li>No hay *ninguna pérdida de datos*.<li> Los datos son coherentes con la aplicación que usa los datos, gracias a la implicación de la aplicación en el momento de realizar la copia de seguridad, mediante VSS.</ol> El servicio de instantáneas de volumen (VSS) garantiza que los datos se escriban correctamente en el almacenamiento. La mayoría de cargas de trabajo de Microsoft tienen escritores VSS que realizan acciones específicas de carga de trabajo relacionadas con la coherencia de los datos. Por ejemplo, Microsoft SQL Server tiene un escritor VSS que garantiza que las escrituras en el archivo de registro de transacciones y en la base de datos se realizan correctamente.<br><br> En la copia de seguridad de máquina virtual de Azure, obtener un punto de recuperación coherente con la aplicación significa que la extensión de copia de seguridad pudo invocar el flujo de trabajo VSS y completarlo *correctamente* antes de que se tomase la instantánea de la máquina virtual. Naturalmente, esto significa que los escritores VSS de todas las aplicaciones de la máquina virtual de Azure también se han invocado.<br><br>Obtenga [Aprenda los conceptos básicos de VSS](http://blogs.technet.com/b/josebda/archive/2007/10/10/the-basics-of-the-volume-shadow-copy-service-vss.aspx) y profundice en los detalles de su [funcionamiento](https://technet.microsoft.com/library/cc785914%28v=ws.10%29.aspx). |
| Coherencia del sistema de archivos | Sí, para máquinas de Windows | Hay dos escenarios donde el punto de recuperación puede ser coherente con el sistema de archivos:<ul><li>copia de seguridad de máquinas virtuales Linux en Azure, ya que Linux no tiene una plataforma equivalente a VSS.<li>Error de VSS durante la copia de seguridad de máquinas virtuales de Windows en Azure.</li></ul> En ambos casos, lo mejor que se puede hacer es asegurarse de que: <ol><li> La máquina virtual *arranca*. <li>No hay *ningún daño*.<li>No hay *ninguna pérdida de datos*.</ol> Las aplicaciones deben implementar su propio mecanismo de "reparación" en los datos restaurados.|
| Coherencia de bloqueos | No | Esta situación es equivalente a aquellos casos en que una máquina experimenta un "bloqueo" (a través de un restablecimiento parcial o completo). Esto suele ocurrir cuando la máquina virtual de Azure se apaga en el momento de realizar la copia de seguridad. Para la copia de seguridad de la máquina virtual de Azure, obtener un punto de recuperación coherente con el bloqueo significa que Copia de seguridad de Azure no ofrece ninguna garantía sobre la coherencia de los datos en el medio de almacenamiento, ni desde la perspectiva del sistema operativo ni desde la perspectiva de la aplicación. Solamente se capturan y se hace una copia de seguridad de los datos que ya existen en el disco en el momento de la copia de seguridad. <br/> <br/> Aunque no hay ninguna garantía, en la mayoría de los casos se iniciará el sistema operativo. Normalmente, esto va seguido de un procedimiento de comprobación de disco como chkdsk para corregir los errores por daños. Se perderán los datos o las escrituras en memoria que no se hayan vaciado completamente en el disco. Normalmente, la aplicación sigue con su propio mecanismo de comprobación en caso de que se deba realizar una reversión de datos. En la copia de seguridad de una máquina virtual de Azure, obtener un punto de recuperación coherente con el bloqueo significa que Copia de seguridad de Azure no ofrece ninguna garantía sobre la coherencia de los datos en el almacenamiento, ya sea desde el punto de vista del sistema operativo o desde el punto de vista de la aplicación. Esto ocurre normalmente cuando la máquina virtual de Azure se apaga en el momento de la copia de seguridad.<br><br>Por ejemplo, si el registro de transacciones tiene entradas que no están presentes en la base de datos, el software de la base de datos realiza una reversión hasta que los datos sean coherentes. Cuando se trabaja con datos repartidos en varios discos virtuales (por ejemplo, los volúmenes distribuidos), un punto de recuperación consistente para fallas proporciona garantías para la corrección de los datos.|


## Rendimiento y uso de recursos
Al igual que el software de copia de seguridad que se implementa de forma local, la copia de seguridad de máquinas virtuales en Azure también debe planearse para el uso de recursos y capacidad. Los [límites de Almacenamiento de Azure](azure-subscription-service-limits.md#storage-limits) definirán cómo se estructuran las implementaciones de máquinas virtuales para obtener un rendimiento máximo con un impacto mínimo en las cargas de trabajo en ejecución. Hay dos límites de Almacenamiento de Azure principales que afectan al rendimiento de la copia de seguridad:

- Salida máxima por cuenta de almacenamiento
- Tasa de solicitud total por cuenta de almacenamiento

Cuando se copian los datos de copia de seguridad de la cuenta de almacenamiento del cliente, cuenta las métricas IOPS y Salida (rendimiento de almacenamiento) de la cuenta de almacenamiento. Al mismo tiempo, las máquinas virtuales también ejecutan y consumen IOPS y el rendimiento. El objetivo es asegurarse de que el tráfico total, copia de seguridad y máquina virtual, no supera los límites de la cuenta de almacenamiento.

La copia de seguridad es expansiva e intenta consumir tantos recursos como sea posible, con el fin de completar la copia de seguridad tan pronto como sea posible. Sin embargo, todas las operaciones de E/S están limitadas por el *Rendimiento de destino de un blob*, que tiene un límite de *60 MB por segundo*. Para acelerar el proceso de copia de seguridad, se intenta realizar la copia de seguridad de cada disco de la máquina virtual *en paralelo*. Por lo tanto, si una máquina virtual tiene 4 discos, Copia de seguridad de Azure intentará hacer copia de seguridad de los 4 discos en paralelo. Por lo tanto, el factor más importante que determina el tráfico de copia de seguridad que sale de una cuenta de almacenamiento de cliente es el **número de discos** cuya copia de seguridad se realiza desde la cuenta de almacenamiento.

Un factor secundario que afecta al rendimiento es la **programación de copia de seguridad**. Si configura todas las máquinas virtuales para realizar la copia de seguridad al mismo tiempo, el número de discos cuya copia de seguridad se realiza *en paralelo* aumentará, ya que Copia de seguridad de Azure intentará realizar la copia de seguridad de tantos discos como sea posible. Por tanto, una forma de reducir el tráfico de copia de seguridad de una cuenta de almacenamiento es asegurarse de que se realizan copias de seguridad de diferentes máquinas virtuales en distintos momentos del día, sin superponerse.

### Planificación de capacidad
Reunir todos estos factores significa que el uso de cuentas de almacenamiento debe planearse correctamente. Descargue la [hoja de Excel de planeación de la capacidad de copia de seguridad de máquinas virtuales](https://gallery.technet.microsoft.com/Azure-Backup-Storage-a46d7e33) para ver el impacto de las opciones de programación de las copias de seguridad y los discos.

### Rendimiento de la copia de seguridad
Para cada disco cuya copia de seguridad se realiza, Copia de seguridad de Azure lee los bloques en el disco y solo almacena los datos cambiados (copia de seguridad incremental). La tabla siguiente muestra los valores de rendimiento medios que se pueden esperar de Copia de seguridad de Azure:

| Operación de copia de seguridad | Mejor rendimiento |
| ---------------- | ---------- |
| Copia de seguridad inicial | 160 Mbps |
| Copia de seguridad incremental (DR) | 640 Mbps <br><br> Este rendimiento puede reducirse considerablemente si hay mucha renovación de código dispersa en el disco del que es necesario hacer una copia de seguridad |

Con ello puede calcular la cantidad de tiempo que tarda en realizarse una copia de seguridad de un disco de un tamaño determinado.

### Tiempo total de copia de seguridad de máquinas virtuales
Aunque la mayoría del tiempo se dedica a leer y copiar los datos, hay otras operaciones que contribuyen al tiempo total necesario para la copia de seguridad de una máquina virtual:

1. Tiempo necesario para [instalar o actualizar la extensión de copia de seguridad](backup-azure-vms.md#offline-vms)
2. Tiempo de espera de cola: puesto que el servicio de copia de seguridad está procesando las copias de seguridad de varios clientes, la operación de copia de seguridad podría no iniciarse inmediatamente. En los momentos de carga máxima, los tiempos de espera pueden ampliarse hasta 8 horas debido al número de copias de seguridad que se procesan. Sin embargo, el tiempo total de la copia de seguridad de máquina virtual será de menos de 24 horas para las directivas de copia de seguridad diarias.

## Solución de errores
Obtenga una lista exhaustiva de soluciones para los errores que pueden producirse durante la copia de seguridad de la máquina virtual:

- [Solución de problemas de copia de seguridad de máquinas virtuales](backup-azure-vms-troubleshoot.md)

## Pasos siguientes
Para obtener más información acerca de cómo empezar a usar Copia de seguridad de Azure, consulte:

- [Restauración de máquinas virtuales](backup-azure-restore-vms.md)
- [Administración de máquinas virtuales](backup-azure-manage-vms.md)

<!---HONumber=Oct15_HO3-->