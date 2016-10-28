<properties
   pageTitle="P+F de Copia de seguridad de Azure | Microsoft Azure"
   description="Respuestas a las preguntas más frecuentes sobre el servicio de Copia de seguridad, el agente de Copia de seguridad, copia de seguridad y retención, recuperación, seguridad y otras preguntas comunes sobre la copia de seguridad y recuperación ante desastres."
   services="backup"
   documentationCenter=""
   authors="markgalioto"
   manager="jwhit"
   editor=""
   keywords="copia de seguridad y recuperación ante desastres; servicio de copia de seguridad"/>

<tags
   ms.service="backup"
   ms.workload="storage-backup-recovery"
	 ms.tgt_pltfrm="na"
	 ms.devlang="na"
	 ms.topic="get-started-article"
	 ms.date="08/29/2016"
	 ms.author="trinadhk; giridham; arunak; markgal; jimpark;"/>

# P+F de servicio de Copia de seguridad de Azure

> [AZURE.SELECTOR]
- [Preguntas más frecuentes acerca del servicio Copia de seguridad para el modo clásico](backup-azure-backup-faq.md)
- [Preguntas más frecuentes acerca del servicio Copia de seguridad para el modo de Resource Manager](backup-azure-backup-ibiza-faq.md)

En este artículo encontrará una lista de las preguntas más frecuentes (y sus correspondientes respuestas) acerca del servicio Copia de seguridad de Azure. Nuestra comunidad responde rápidamente, y si una pregunta se repite a menudo, la agregamos a este artículo. Normalmente, las respuestas proporcionan referencia o información de soporte técnico. Las preguntas acerca de Copia de seguridad de Azure se pueden realizar en la sección Disqus de este artículo o de otros artículos relacionados. También se pueden publicar preguntas sobre el servicio Copia de seguridad de Azure en el [foro de debate](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazureonlinebackup).


## ¿Cuál es la lista de sistemas operativos compatibles desde los que se pueden realizar copias de seguridad en Azure mediante Copia de seguridad de Azure? <br/>
Copia de seguridad de Azure es compatible con los sistemas operativos siguientes para la copia de seguridad de la carpeta de archivos, la copia de seguridad de aplicación con el servidor de Copia de seguridad de Azure y SCDPM.

| Sistema operativo | Plataforma | SKU |
| :------------- |-------------| :-----|
| Windows 8 y SP más recientes | 64 bits | Enterprise, Pro |
| Windows 7 y SP más recientes | 64 bits | Ultimate, Enterprise, Professional, Home Premium, Home Basic, Starter |
| Windows 8.1 y SP más recientes | 64 bits | Enterprise, Pro |
| Windows 10 | 64 bits | Enterprise, Pro, Home |
|Windows Server 2012 R2 y SP más recientes|	64 bits|	Standard, Datacenter, Foundation|
|Windows Server 2012 y SP más recientes|	64 bits|	Datacenter, Foundation, Standard|
|Windows Storage Server 2012 R2 y SP más recientes |64 bits|	Standard, Workgroup|
|Windows Storage Server 2012 y SP más recientes |64 bits |Standard, Workgroup
|Windows Server 2012 R2 y SP más recientes |64 bits|	Essential|
|Windows Server 2008 R2 SP1 |64 bits|	Standard, Enterprise, Datacenter, Foundation|
|Windows Server 2008 SP2 |64 bits|	Standard, Enterprise, Datacenter, Foundation|

Para la copia de seguridad de máquina virtual de Azure,

- **Linux**: Copia de seguridad de Azure admite [una lista de distribuciones aprobadas por Azure](../virtual-machines/virtual-machines-linux-endorsed-distros.md), con la excepción de CoreOS Linux. Otras distribuciones con la iniciativa "traiga su propio Linux" también podrían funcionar, siempre que el agente de máquina virtual esté disponible en la máquina virtual y haya compatibilidad con Python.
- **Windows Server**: no se admiten las versiones anteriores a Windows Server 2008 R2.

## ¿Dónde puedo descargar el agente de Copia de seguridad de Azure más reciente? <br/>
El agente más reciente para realizar copias de seguridad de Windows Server, System Center DPM o el cliente de Windows se puede descargar de [aquí](http://aka.ms/azurebackup_agent). Si desea hacer una copia de seguridad de una máquina virtual, utilice el agente de máquina virtual (que instala automáticamente la extensión adecuada). El agente de la máquina virtual ya está presente en las máquinas virtuales que se crean desde la Galería de Azure.

## ¿Qué versión de servidor SCDPM es compatible? <br/>
Se recomienda que instale el agente de Copia de seguridad de Azure [más reciente](http://aka.ms/azurebackup_agent) en el paquete acumulativo de actualizaciones de SCDPM más reciente (UR11 desde agosto de 2016).

## Al configurar al agente de Copia de seguridad de Azure, se me solicita que especifique las credenciales de almacén. ¿Expiran las credenciales de almacén?
Sí, las credenciales de almacén expiran a las 48 horas. Si el archivo caduca, inicie sesión en el Portal de Azure y descargue los archivos de credenciales de almacén desde el almacén.

## ¿Hay algún límite del número de almacenes que se pueden crear en cada suscripción de Azure? <br/>
Sí. Desde agosto de 2016, puede crear 25 almacenes (uno para cada almacén de copia de seguridad y un almacén de Servicios de recuperación) por suscripción. Si necesita más almacenes, cree una nueva suscripción.

## ¿Hay algún límite en el número de servidores o máquinas que se puede registrar en cada almacén? <br/>
Sí, se pueden registrar un máximo de 50 máquinas por almacén. Para las máquinas virtuales de IaaS de Azure, el límite es de 200 máquinas virtuales por almacén. Si necesita registrar más máquinas, cree un nuevo almacén.

## ¿Cómo registro mi servidor en otro centro de datos?<br/>
Los datos de copia de seguridad se envían al centro de datos del almacén en el que está registrado. La forma más sencilla de cambiar el centro de datos es desinstalar el agente y volver a instalarlo y registrarlo en un nuevo almacén que pertenece al centro de datos deseado.

## ¿Qué ocurre si cambio el nombre de un servidor de Windows de cuyos datos se está realizando una copia de seguridad en Azure?<br/>
Al cambiar el nombre de un servidor, se detienen todas las copias de seguridad configuradas actualmente. Debe registrar el nuevo nombre del servidor con el almacén de copia de seguridad. Cuando se crea un nuevo registro, la primera operación de copia de seguridad es una copia de seguridad completa y no una copia de seguridad incremental. Si necesita recuperar datos cuya copia de seguridad se realizó anteriormente en el almacén con el nombre del servidor antiguo, puede hacerlo mediante la opción [**Otro servidor**](backup-azure-restore-windows-server.md#recover-to-an-alternate-machine) del Asistente para **recuperar datos**.

## ¿Desde qué tipos de unidades puedo realizar copias de seguridad de archivos y carpetas? <br/>
La copia de seguridad no se puede realizar del siguiente conjunto de unidades/volúmenes de disco:

- Medios extraíbles: la unidad debe ser fija para poder usarse como origen de copia de seguridad.
- Volúmenes de solo lectura: el volumen debe ser grabable para que el servicio de copia de instantáneas de volumen (VSS) funcione.
- Volúmenes sin conexión: el volumen debe estar en línea para que VSS funcione.
- Recurso compartido de red: el volumen debe ser local en el servidor para que la copia de seguridad se realice en línea.
- Volúmenes protegidos por BitLocker: el volumen debe desbloquearse antes de que se pueda crear la copia de seguridad.
- Identificación del sistema de archivos: NTFS es el único sistema de archivos admitido por esta versión del servicio de copia de seguridad en línea.

## ¿De qué tipos de archivos y carpetas puedo hacer copias de seguridad desde mi servidor?<br/>
Se admiten los siguientes tipos:

- Cifrados
- Comprimidos
- Dispersos
- Comprimidos + dispersos
- Vínculos físicos: no compatibles, se omiten
- Puntos de repetición: no compatibles, se omiten
- Cifrados + comprimidos: no compatibles, se omiten
- Cifrados + dispersos: no compatibles, se omiten
- Secuencias comprimidas: no compatibles, se omiten
- Secuencias dispersas: no compatibles, se omiten

## ¿Cuál es el requisito de tamaño mínimo para la carpeta de caché? <br/>
El tamaño de la carpeta de caché determina la cantidad de datos de los que se realiza la copia de seguridad. La carpeta de caché debe tener un 5% del espacio requerido para el almacenamiento de datos.

## Si la organización tiene un almacén, ¿cómo se pueden aislar los datos de un servidor desde otro servidor al restaurar los datos?<br/>
Los servidores registrados en el mismo almacén podrán recuperar los datos cuya copia de seguridad hayan realizado otros servidores *que usen la misma frase de contraseña*. Si tiene servidores cuyos datos de copia de seguridad desee aislar de otros servidores de la organización, utilice una frase de contraseña específica para dichos servidores. Por ejemplo, los servidores de recursos humanos podrían usar una frase de contraseña de cifrado, los servidores de contabilidad, otra y los servidores de almacenamiento, otra distinta.

## ¿Puedo "migrar" mi almacén o datos de copia de seguridad de una suscripción a otra? <br/>
No. El almacén se crea en un nivel de suscripción y no se puede reasignar a otra suscripción una vez que se crea.

## ¿Funciona el agente de Copia de seguridad de Azure en un servidor que usa la desduplicación de Windows Server 2012? <br/>
Sí. El servicio del agente convierte los datos desduplicados en datos normales cuando prepara la operación de copia de seguridad. Luego optimiza los datos para la copia de seguridad, los cifra y los envía al servicio de copia de seguridad en línea.

## Si se cancela un trabajo de copia de seguridad una vez que se ha iniciado, ¿se eliminan los datos de copia de seguridad transferidos? <br/>
No. El almacén de copia de seguridad almacena los datos de los que se ha realizado la copia de seguridad transferidos hasta el momento de la cancelación. Copia de seguridad de Azure usa un mecanismo para agregar ocasionalmente agregar puntos de control a los datos de copia de seguridad durante la copia de seguridad. Debido a que hay puntos de control en los datos de copia de seguridad, el siguiente proceso de copia de seguridad puede validar la integridad de los archivos. La siguiente copia de seguridad desencadenada sería incremental respecto a los datos de los que se haya creado una copia de seguridad anteriormente. Las copias de seguridad incrementales permiten usar mejor el ancho de banda, por lo que no es necesario transferir los mismos datos una y otra vez.

En caso de la copia de seguridad de la máquina virtual de Azure, cuando se cancela el trabajo, los datos transferidos se ignoran y una nueva copia de seguridad transfiere los datos incrementales desde el trabajo de copia de seguridad realizado correctamente.

## ¿Por qué aparece la advertencia "Las copias de seguridad de Azure no se han configurado para este servidor" aunque programé previamente copias de seguridad periódicas? <br/>
Esta advertencia se produce cuando la configuración de programación de la copia de seguridad almacenada en el servidor local no es la misma que la configuración del almacén de copia de seguridad. Cuando el servidor o la configuración se recuperan a un estado válido conocido, las programaciones de copia de seguridad pueden perder la sincronización. Si recibe esta advertencia, [vuelva a configurar la directiva de copia de seguridad](backup-azure-manage-windows-server.md) y elija **Ejecutar copia de seguridad ahora** para volver a sincronizar el servidor local con Azure.

## ¿Qué reglas de firewall que hay que configurar para Copia de seguridad de Azure? <br/>
Para una protección completa tanto de los datos locales que se transfieren a Azure como de los datos de la carga de trabajo que se transfieren a Azure, se recomienda permitir que el firewall se comunique con las direcciones URL siguientes:

- www.msftncsi.com
- *.Microsoft.com
- *.WindowsAzure.com
- *.microsoftonline.com
- *.windows.net

##¿Se puede instalar el agente de Copia de seguridad de Azure en una máquina virtual de Azure de la que el servicio Copia de seguridad de Azure ya ha realizado una copia de seguridad mediante la extensión de máquina virtual? <br/>
Totalmente. Copia de seguridad de Azure proporciona copia de seguridad de nivel de máquina virtual para las máquinas virtuales de Azure que usen la extensión de máquina virtual. El agente de Copia de seguridad de Azure se puede instalar en un SO invitado de Windows para proteger los archivos y carpetas de dicho SO.

## ¿Puedo instalar el agente de Copia de seguridad de Azure en una máquina virtual de Azure para realizar la copia de seguridad de los archivos y carpetas que hay en un almacenamiento temporal proporcionado por la máquina virtual de Azure? <br/>
Puede instalar al agente de Copia de seguridad de Azure en el SO invitado de Windows y realizar una copia de seguridad de los archivos y carpetas en un almacenamiento temporal. Sin embargo, tenga en cuenta que las copias de seguridad dejarán de funcionar cuando se borren los datos del almacenamiento temporal. Además, si se han eliminado los datos del almacenamiento temporal, la restauración solo se podrá realizar en un almacenamiento no volátil.

## He instalado el agente de Copia de seguridad de Azure para proteger archivos y carpetas. ¿Puedo instalar ahora SCDPM para que funcione con el agente de Copia de seguridad de Azure con el fin de proteger las cargas de trabajo locales de aplicaciones o de máquinas virtuales para Azure? <br/>
Para usar Copia de seguridad de Azure con SCDPM, se recomienda instalar SCDPM primero y a continuación instalar el agente de Copia de seguridad de Azure. Esto garantiza una integración sin problemas del agente de Copia de seguridad de Azure con SCDPM. Además, permite proteger archivos y carpetas, cargas de trabajo de aplicaciones y máquinas virtuales para Azure, directamente desde la consola de administración de SCDPM. No se recomienda ni admite instalar SCDPM después de instalar el agente de Copia de seguridad de Azure para los fines mencionados antes.

## ¿Qué longitud tiene la ruta de acceso de archivo que se puede especificar como parte de la directiva de Copia de seguridad de Azure mediante el agente de Copia de seguridad de Azure? <br/>  
El agente de Copia de seguridad de Azure usa NTFS. La [API de Windows limita la especificación de la longitud de la ruta de acceso de archivo](https://msdn.microsoft.com/library/aa365247.aspx#fully_qualified_vs._relative_paths). En caso de copia de seguridad de archivos cuyas rutas de acceso tengan una longitud mayor que las especificadas por la API de Windows, los clientes pueden elegir hacer una copia de seguridad de la carpeta primaria o de la unidad de disco de los archivos de la copia de seguridad.

## ¿Qué caracteres se permiten en la ruta de acceso de archivo de la directiva de Copia de seguridad de Azure cuando se usa el agente de Copia de seguridad de Azure? <br>  
 El agente de Copia de seguridad de Azure usa NTFS. Permite [caracteres compatibles con NTFS](https://msdn.microsoft.com/library/aa365247.aspx#naming_conventions) como parte de la especificación de los archivos.

## ¿Puedo usar el servidor de Copia de seguridad de Azure para crear una copia de seguridad de reconstrucción completa (BMR) para un servidor físico? <br/>
Sí.

## ¿Puedo configurar el servicio Copia de seguridad para enviar mensajes de correo electrónico si se produce un error en un trabajo de copia de seguridad? <br/>
Sí, el servicio de copia de seguridad tiene varias alertas basadas en eventos que se pueden usar con un script de PowerShell. Para obtener una descripción completa, consulte [Notificaciones de alerta](backup-azure-manage-vms.md#alert-notifications).

## ¿Hay algún límite en el tamaño de cada origen de datos del que se realiza una copia de seguridad? <br/>
Aunque no hay ningún límite de almacén en relación a la cantidad de datos de los que se puede hacer una copia de seguridad, Copia de seguridad de Azure impone una restricción (en la práctica, estos límites son muy elevados) sobre el tamaño máximo del origen de datos. A partir de agosto de 2015, el origen de datos de tamaño máximo del sistemas operativos compatible es:

|S.No |	Sistema operativos |	Tamaño máximo del origen de datos |
| :-------------: |:-------------| :-----|
|1| Windows Server 2012 o superior| 54400 GB|
|2| Windows 8 o superior| 54400 GB|
|3| Windows Server 2008, Windows Server 2008 R2 | 1700 GB|
|4| Windows 7 | 1700 GB|

La tabla siguiente explica cómo se determina el tamaño de cada origen de datos.

|	Origen de datos |	Detalles |
| :-------------: |:-------------|
|Volumen |La cantidad de datos de los que se realiza copia de seguridad de un volumen individual de un equipo servidor o cliente.|
|Máquina virtual de Hyper-V | Suma de los datos de todos los discos duros virtuales de la máquina virtual de los que se hace copia de seguridad|
|Base de datos de Microsoft SQL Server | Tamaño de una sola base de datos SQL de la que se hace copia de seguridad |
|Microsoft SharePoint |Suma de las bases de datos de contenido y configuración de la granja de SharePoint de las que se hace copia de seguridad|
|Microsoft Exchange |Suma de todas las bases de datos de un servidor de Exchange de las que se hace copia de seguridad|
|Estado del sistema y BMR |Cada copia individual del estado del sistema o BMR del equipo del que se hace copia de seguridad|

## ¿Hay algún límite en el número de veces por día que se puede programar un trabajo de copia de seguridad?<br/>
Sí, en Windows Server o en un cliente Windows los trabajos de copia de seguridad se pueden ejecutar un máximo de tres veces al día. Sin embargo, en System Center DPM, los trabajos de copia de seguridad se pueden ejecutar un máximo de dos veces al día. Por último, en las máquinas virtuales de IaaS solo se puede ejecutar un trabajo de copia de seguridad al día.

## ¿Hay alguna diferencia entre las directivas de programación de DPM y de Windows Server (es decir, en Windows Server sin DPM)? <br/>
Sí. Con DPM se pueden especificar programaciones diarias, semanales, mensuales y anuales. Windows Server (sin DPM) solo permite especificar programaciones diarias y semanales.

## ¿Hay alguna diferencia entre las directivas de retención de DPM y de Windows Server o cliente de Windows (es decir, en Windows Server sin DPM)?<br/>
No, tanto DPM como Windows Server o el cliente Windows tienen directivas de retención diarias, semanales, mensuales y anuales.

## ¿Puedo configurar de forma selectiva mis directivas de retención (es decir, configurar semanal y diariamente, pero no anual y mensualmente)?<br/>
Sí, la estructura de retención de la Copia de seguridad de Azure permite tener una flexibilidad completa en la definición de la directiva de retención según sus requisitos.

## ¿Puedo "programar una copia de seguridad" a las 6 p.m. y especificar "directivas de retención" en un momento diferente?<br/>
No. Las directivas de retención pueden aplicarse solo en puntos de copia de seguridad. En la siguiente imagen, la directiva de retención se especifica para las copias de seguridad realizadas a las 12 a.m. y a las 6 p.m. <br/>

![Programar copia de seguridad y retención](./media/backup-azure-backup-faq/Schedule.png) <br/>

## ¿Se transfiere una copia incremental para las directivas de retención programadas? <br/>
No, la copia incremental se envía en el momento mencionado en la página de programación de copia de seguridad. Los puntos que se pueden retener se determinan según la directiva de retención.

## Si se conserva una copia de seguridad durante un período prolongado, ¿se tarda más tiempo en recuperar un punto de datos más antiguo? <br/>
 No: el tiempo de recuperación del punto de datos más antiguo o más reciente es el mismo. Cada punto de recuperación se comporta como un punto completo.

## Si cada punto de recuperación es como un punto completo, ¿afecta esto al almacenamiento de copia de seguridad facturable total?<br/>
Los productos con un punto de retención a largo plazo típicos almacenan los datos de copia de seguridad como puntos completos. Los puntos completos *no son eficientes* para el almacenamiento, pero resultan más fáciles y rápidos de restaurar. Las copias incrementales son *eficientes* para el almacenamiento, pero requieren que se restaure una cadena de datos, lo que afecta al tiempo de recuperación. La arquitectura de almacenamiento de Copia de seguridad de Azure le ofrece lo mejor de ambos mundos ya que permite almacenar de forma óptima datos para conseguir restauraciones más rápidas e incurrir en pocos costos de almacenamiento. Este enfoque del almacenamiento de datos garantiza que el ancho de banda de entrada y salida se utiliza de manera eficiente. Tanto la cantidad de almacenamiento de datos como el tiempo necesario para recuperar los datos se reducen al mínimo. Obtenga más información sobre la eficacia del almacenamiento de las [copias de seguridad incrementales](https://azure.microsoft.com/blog/microsoft-azure-backup-save-on-long-term-storage/).

## ¿Hay un límite en el número de puntos de recuperación que se pueden crear?<br/>
No. Hemos eliminado los límites a los puntos de recuperación. Puede crear tantos puntos de recuperación como desee.

## ¿Por qué la cantidad de datos transferida en la copia de seguridad es distinta a la cantidad de datos de los que realizo la copia de seguridad?<br/>
 Todos los datos de los que se realiza una copia de seguridad desde el agente de Copia de seguridad de Azure o SCDPM o del servidor de Copia de seguridad de Azure se comprimen y se cifran antes de ser transferidos. Una vez que se aplica la compresión y el cifrado, el tamaño de los datos del almacén de copia de seguridad se reduce entre un 30% y un 40%.

## ¿Hay alguna forma de ajustar la cantidad de ancho de banda que utiliza el servicio Copia de seguridad?<br/>
 Sí, use la opción **Cambiar propiedades** del agente de Copia de seguridad para ajustar el ancho de banda. Ajuste la cantidad de ancho de banda y los momentos en que se usa dicho ancho de banda. Para más información, consulte [Habilitación de la velocidad moderada de la red (opcional)](../backup-configure-vault.md#enable-network-throttling).

## El ancho de banda de Internet está limitado por la cantidad de datos de los que tengo que realizar una copia de seguridad. ¿Hay alguna manera para pasar datos a una determinada ubicación con una canalización de red de gran tamaño e insertar dichos datos en Azure? <br/>
Puede hacer una copia datos en Azure a través del proceso de copia de seguridad en línea estándar o puede usar el servicio de importación y exportación de Azure para transferir los datos al Almacenamiento de blobs en Azure. No hay ninguna otra forma de introducir datos para copia de seguridad en el Almacenamiento de Azure. Para más información acerca de cómo usar el servicio Importación/Exportación de Azure con Copia de seguridad de Azure, consulte el artículo [Flujo de trabajo de copia de seguridad sin conexión en Copia de seguridad de Azure](backup-azure-backup-import-export.md).

## ¿Cuántas recuperaciones puedo realizar en los datos cuya copia de seguridad se crea en Azure?<br/>
No hay ningún límite en cuanto al número de recuperaciones de la Copia de seguridad de Azure.

## ¿Tengo que pagar por el tráfico de salida desde el centro de datos de Azure durante las recuperaciones?<br/>
 No. Sus recuperaciones son gratuitas y no se cobra por el tráfico de salida.

## ¿Se cifran los datos que se envían a Azure? <br/>
Sí. Los datos se cifran en la máquina cliente/servidor/SCDPM local mediante AES256 y se envían a través de un vínculo HTTPS seguro.

## ¿También se cifran los datos de copia de seguridad en Azure?<br/>
 Sí. Los datos que se envían a Azure permanecen cifrados (en reposo). Microsoft no descifra los datos de copia de seguridad en ningún momento. Para la copia de seguridad de la máquina virtual de Azure, Copia de seguridad de Azure se basa en el cifrado de la máquina virtual, es decir, si la máquina virtual está cifrada con Cifrado de discos de Azure o con alguna otra tecnología de cifrado, Copia de seguridad de Azure utiliza ese cifrado para proteger los datos.

## ¿Cuál es la longitud mínima de la clave de cifrado utilizada para cifrar los datos de copia de seguridad? <br/>
 La clave de cifrado debe tener al menos 16 caracteres.

## ¿Qué sucede si pierdo la clave de cifrado? ¿Puedo recuperar los datos (o) puede Microsoft recuperar los datos? <br/>
La clave utilizada para cifrar los datos de copia de seguridad está presente en las instalaciones del cliente. Microsoft no mantiene una copia en Azure y no tiene acceso a la clave. Si el cliente pierde la clave, Microsoft no puede recuperar los datos de copia de seguridad.

## ¿Cómo se cambia la ubicación de la memoria caché especificada para el agente de Copia de seguridad de Azure?<br/>
 Vaya secuencialmente a través de la lista de viñetas siguiente para cambiar la ubicación de la caché.
- Detenga el módulo de copia de seguridad, para lo que debe ejecutar el siguiente comando en un símbolo del sistema con privilegios elevados:

  ```PS C:\> Net stop obengine```

- No mueva los archivos. En su lugar, copie la carpeta de espacio en caché en otra unidad con espacio suficiente. El espacio en caché original se puede eliminar después de confirmar que las copias de seguridad funcionan con el espacio en caché nuevo.

- Actualice las siguientes entradas del Registro con la ruta de acceso a la nueva carpeta del espacio en caché.<br/>

|Ruta de acceso del Registro | Clave del Registro | Valor |
| ------ | ------- | ------|
| `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config` | ScratchLocation | *Nueva ubicación de la carpeta de la memoria caché* |
| `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config\CloudBackupProvider` | ScratchLocation | *Nueva ubicación de la carpeta de la memoria caché* |

- Reinicie el módulo de copia de seguridad, para lo que debe ejecutar el siguiente comando en un símbolo del sistema con privilegios elevados:

  ```PS C:\> Net start obengine```

  Una vez que la creación de la copia de seguridad se haya completado correctamente en la nueva ubicación de la memoria caché, puede quitar la carpeta de la memoria caché original.

## ¿Dónde se puede colocar la carpeta de caché del agente de Copia de seguridad de Azure para que funcione según lo previsto?<br/>
No se recomiendan las siguientes ubicaciones para la carpeta de caché:

- Recursos compartidos de red o medios extraíbles: la carpeta de caché debe ser local para el servidor que necesita realizar copias de seguridad mediante la copia de seguridad en línea. No se admiten ubicaciones de red ni medios extraíbles como unidades USB.
- Volúmenes sin conexión: la carpeta de caché debe estar en línea para la copia de seguridad esperada con el agente de Copia de seguridad de Azure.

## ¿Hay algún atributo de la carpeta de caché que no se admita?<br/>
 No se admiten los siguientes atributos ni sus combinaciones para la carpeta de caché:

- Cifrados
- Desduplicados
- Comprimidos
- Dispersos
- Punto de reanálisis

Se recomienda que ni la carpeta de caché ni el disco duro virtual de metadatos tengan los atributos anteriores para que el agente de Copia de seguridad de Azure funcione de la forma esperada.

<!---HONumber=AcomDC_0831_2016-->