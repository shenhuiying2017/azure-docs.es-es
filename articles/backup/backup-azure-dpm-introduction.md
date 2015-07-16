<properties
	pageTitle="Introducción a Copia de seguridad de Azure DPM | Microsoft Azure"
	description="Una introducción a la copia de seguridad de servidores DPM mediante el servicio Copia de seguridad de Azure"
	services="backup"
	documentationCenter=""
	authors="Jim-Parker"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/12/2015"
	ms.author="jimpark"/>

# Introducción a Copia de seguridad de DPM de Azure

En este artículo se proporciona una introducción al uso de Copia de seguridad de Microsoft Azure con el objeto de proteger los servidores y las cargas de trabajo de System Center Data Protection Manager (DPM). Cuando lo lea, comprenderá:

- Cómo funciona la copia de seguridad de servidor DPM de Azure
- Los requisitos previos para lograr una experiencia fluida de la copia de seguridad
- Los errores típicos y cómo resolverlos
- Escenarios admitidos

Datos de aplicación y archivo de copia de seguridad de System Center DMP. Los datos con copia de seguridad en DPM se pueden almacenar en una cinta o en un disco, o se puede crear una copia de seguridad de ellos en Azure con Copia de seguridad de Microsoft Azure. DPM interactúa con Copia de seguridad de Azure de la manera siguiente:

- **DPM implementado como un servidor físico o una máquina virtual local**: si DPM se implementa como un servidor físico o una máquina virtual de Hyper-V local, además de en disco y cinta, puede crear una copia de seguridad de los datos en un almacén de Copia de seguridad de Azure.
- **DPM implementado como una máquina virtual de Azure**: desde System Center 2012 R2 con Update 3, DPM puede implementarse como una máquina virtual de Azure. Si DPM se implementa como una máquina virtual de Azure, puede crear una copia de seguridad de los datos en discos de Azure conectados a la máquina virtual de Azure DPM, o puede descargar el almacenamiento de datos creando una copia de seguridad de ellos en un almacén de Copia de seguridad de Azure.

## ¿Por qué crear una copia de seguridad de los servidores DPM?

Algunas de las ventajas empresariales del uso de Copia de seguridad de Azure para la copia de seguridad de servidores DMP son las siguientes:

- Para la implementación de DPM local, puede usar Copia de seguridad de Azure como alternativa a la implementación a largo plazo en una cinta.
- Para las implementaciones de DPM en Azure, Copia de seguridad de Azure le permite descargar el almacenamiento del disco de Azure, de forma que es posible escalar verticalmente gracias a que los datos más antiguos se almacenan en Copia de seguridad de Azure y los nuevos en el disco.

## Cómo funciona la copia de seguridad de servidor DPM
Para hacer una copia de seguridad de una máquina virtual, primero se necesita una instantánea de los datos en un momento específico. El servicio de Copia de seguridad de Azure inicia el trabajo de copia de seguridad a la hora programada y desencadena la extensión para tomar una instantánea de copia de seguridad. La extensión de copia de seguridad se coordina con el servicio VSS en el invitado para conseguir coherencia y llama a la API de instantánea de blob del servicio de almacenamiento de Azure cuando se alcanza la coherencia. Esto se hace para obtener una instantánea coherente de los discos de la máquina virtual, sin tener que apagarla.

Una vez tomada la instantánea, los datos se transfieren por el servicio de Copia de seguridad de Azure al almacén de copia de seguridad. El servicio se encarga de identificar y transferir solo los bloques que han cambiado desde la última copia de seguridad, lo que hace que el almacenamiento de copias de seguridad y la red sean eficaces. Una vez completada la transferencia de datos, se elimina la instantánea y se crea un punto de recuperación. Este punto de recuperación se puede ver en el Portal de administración de Azure.

>[AZURE.NOTE]Para las máquinas virtuales de Linux, solo es posible la copia de seguridad coherente con archivos.

## Requisitos previos
Prepare Copia de seguridad de Azure para crear copias de seguridad de los datos de DPM de la manera siguiente:

1. **Crear un almacén de copia de seguridad**: cree un almacén en la consola de Copia de seguridad de Azure.
2. **Descargar las credenciales de almacén**: en Copia de seguridad de Azure, cargue el certificado de administración que creó en el almacén.
3. **Instalar Azure Backup Agent y registrar el servidor**: en Copia de seguridad de Azure, instale el agente en cada servidor DPM y registre cada servidor en el almacén de copia de seguridad.

### Creación de un almacén de copia de seguridad
Para iniciar la copia de seguridad de sus máquinas virtuales de Azure, deberá crear primero un almacén de copia de seguridad. El almacén es una entidad que almacena todas las copias de seguridad y los puntos de recuperación que se han creado con el tiempo. El almacén contiene también las directivas de copia de seguridad que se aplicarán a las máquinas virtuales de una copia de seguridad.

1. Inicie sesión en el [Portal de administración](http://manage.windowsazure.com/).
2. Haga clic en **Nuevo** > **Servicios de datos** > **Servicios de recuperación** > **Almacén de copia de seguridad** > **Creación rápida**. Si tiene varias suscripciones asociadas a su cuenta profesional, elija la suscripción adecuada que se asociará al almacén de copia de seguridad. En cada suscripción de Azure puede tener varios almacenes de copia de seguridad para organizar los datos que se van a proteger.
3. En **Nombre**, escriba un nombre descriptivo para identificar el almacén. Esto debe ser único para cada suscripción.
4. En **Región**, seleccione la región geográfica del almacén. Tenga en cuenta que el almacén debe estar en la misma región que las máquinas virtuales que desea proteger. Si tiene máquinas virtuales en diferentes regiones, cree un almacén en cada uno de ellos. No es necesario especificar cuentas de almacenamiento para almacenar datos de copia de seguridad. El almacén de copia de seguridad y el servicio Copia de seguridad de Azure lo controlan automáticamente.
    > [AZURE.NOTE] La copia de seguridad de máquinas virtuales con el servicio de Copia de seguridad de Azure solo se admite en [determinadas regiones](http://azure.microsoft.com/regions/#services). Consulte la lista de regiones en que se admite. Si la región que buscando no la admite actualmente, no aparecerá en la lista desplegable durante la creación del almacén.

5. En **Suscripción**, escriba la suscripción de Azure con la que desea usar el almacén de copia de seguridad.
6. Haga clic en **Crear almacén**.![Crear un almacén de copia de seguridad](./media/backup-azure-dpm-introduction/backup_vaultcreate.png)

    La creación del almacén de credenciales de copia de seguridad puede tardar unos minutos. Supervise las notificaciones de estado en la parte inferior del portal. ![Crear la notificación del sistema del almacén](./media/backup-azure-dpm-introduction/creating-vault.png)

    Un mensaje confirma que el almacén se ha creado correctamente y se mostrará en la página Servicios de recuperación como **Activo**.

    ![Lista de copias de seguridad](./media/backup-azure-dpm-introduction/backup_vaultslist.png)

7. Haga clic en el almacén de copia de seguridad para ir a la página **Inicio rápido**, donde se muestran las instrucciones para la copia de seguridad de los servidores DPM. ![Instrucciones de copia de seguridad de máquina virtual en la página del panel](./media/backup-azure-dpm-introduction/vmbackup-instructions.png)

    > [AZURE.NOTE]Asegúrese de que se ha elegido la opción de redundancia de almacenamiento apropiada justo después de que se ha creado el almacén. Lea más sobre cómo [establecer la opción de redundancia de almacenamiento en el almacén de copia de seguridad](http://azure.microsoft.com/documentation/articles/backup-azure-backup-create-vault/#azure-backup---storage-redundancy-options).

### Descarga de las credenciales de almacén
1. Haga clic en **Servicios de recuperación** y en el almacén de copia de seguridad. En la página **Inicio rápido**, haga clic en **Descargar credenciales de almacén** para descargar el archivo de credenciales y guardarlo en una ubicación segura. No puede editar las credenciales, por lo que no es necesario abrir la ubicación. Por motivos de seguridad, la clave del archivo caduca transcurridas 48 horas.

2. Copie el archivo en una ubicación segura a la que puedan acceder fácilmente los servidores DPM que desee registrar en el almacén de Copia de seguridad de Azure. Deberá seleccionar el archivo cuando instale Azure Backup Agent.

### Instalación de Azure Backup Agent y registro del servidor
Descargue el archivo de instalación del agente y ejecútelo en cada servidor DPM que contenga los datos de los que desea crear una copia de seguridad. Los agentes se almacenan en el **Centro de descarga de Azure** y tienen su propio proceso de instalación. Al ejecutar el programa de instalación, se instala el agente y el servidor DPM se registra en el almacén. Observe lo siguiente:

- Necesitará permisos administrativos en el servidor DPM para instalar al agente.
- Para realizar la instalación en varios servidores DPM, puede colocar el archivo de instalación en un recurso compartido de red, o usar la directiva de grupo o productos de administración como System Center Configuration Manager para instalarlo.
- No es necesario reiniciar el servidor DPM después de la instalación.

#### Para instalar Azure Backup Agent y registrar el servidor

1. En la página **Inicio rápido** del almacén de Copia de seguridad de Azure, en **Descargar Azure Backup Agent**, seleccione **Para Windows Server, System Center Data Protection Manager o cliente de Windows**. Descargue la aplicación en el servidor DPM en el que desea ejecutarla.
2. Ejecute el archivo de instalación **MARSAgentInstaller.exe**. Acepte los términos del servicio y seleccione instalar el software necesario que falta.
3. En la página **Configuración de la instalación**, seleccione la **Carpeta de instalación** y **Ubicación en caché**.

    La carpeta de ubicación en caché predeterminada es <system drive>:\Program Files\Azure Backup Agent. En la ubicación en caché, el proceso de instalación crea una carpeta denominada **Scratch** en la carpeta **Azure Backup Agent**. La ubicación en caché debe tener al menos 2,5 gigabytes (GB) (o el 10 % del tamaño de los datos de los que se creará una copia de seguridad en Azure) de espacio libre. Solo los administradores del sistema local y los miembros del grupo Administradores tienen acceso al directorio de caché para evitar ataques de denegación de servicio.

4. En la página **Configuración de proxy**, establezca la configuración del proxy personalizada para que el agente se conecte a Azure. Si no realiza ninguna configuración, se usará la configuración predeterminada de acceso a Internet en el servidor DPM. Tenga en cuenta que si usa un servidor proxy que requiere autenticación, debe especificar los detalles en esta página.
5. En la página **Alta en actualizaciones de Microsoft** se recomienda habilitar las actualizaciones. Si el servidor ya está habilitado para las actualizaciones automáticas, se omitirá este paso. Tenga en cuenta que la configuración de actualizaciones de Microsoft es para todas las actualizaciones de productos de Microsoft, y no son exclusivas de Azure Backup Agent.
6. Se muestra la página **Instalación**. En la instalación se comprueba que el software necesario esté instalado y se completa el proceso de instalación. Cuando termine, recibirá un mensaje para indicar que Azure Backup Agent se instaló correctamente. En este momento, puede elegir comprobar si hay actualizaciones. Se recomienda que permita la acción de comprobar si hay actualizaciones.
7. Haga clic en **Proseguir con el registro** para registrar el servidor en el almacén.
8. En la página **Identificación del almacén**, seleccione el archivo de registro de almacén que generó en el almacén de Copia de seguridad de Azure.
9. En la página **Configuración de cifrado**, especifique detalles de la frase de contraseña o genere automáticamente una frase de contraseña.
10. Haga clic en Generar frase de contraseña seguido de Copiar al Portapapeles. Recibirá un mensaje para indicar que la frase de contraseña se ha copiado en el Portapapeles. Ahora resulta muy conveniente abrir el Bloc de notas y pegar la frase de contraseña desde el Portapapeles y guardar el archivo. Imprima también el archivo y guárdelo en un lugar seguro. Haga clic en Registrar para registrar el servidor DPM en el almacén de copia de seguridad.

    > [AZURE.TIP] En el paso Configuración de cifrado, recuerde copiar la frase de contraseña en el Portapapeles.
11. Haga clic en **Registrar**.

    Una vez completado el registro, la consola de DPM muestra la disponibilidad de Copia de seguridad de Azure.

    Copia de seguridad de Azure siempre cifrará los datos en el origen con la frase de contraseña (cadena alfanumérica) que especifique o que se genere automáticamente.
    >[AZURE.NOTE]Copia de seguridad de Azure no mantiene nunca la frase de contraseña y, si la pierde, los datos no se podrán restaurar ni recuperar. Se recomienda encarecidamente que guarde la clave en una ubicación externa.

Si especifica una frase de contraseña y hace clic en **Finalizar**, el agente tarda unos segundos en registrar el servidor de producción en el almacén de copia de seguridad. Tan pronto como el registro en el almacén finaliza, aparece una página de resumen **Registro del servidor**.

## Requisitos y limitaciones

- DPM se puede ejecutar como un servidor físico o como una máquina virtual de Hyper-V, instalado en System Center 2012 SP1 o System Center 2012 R2. También se puede ejecutar como una máquina virtual de Azure que ejecuta System Center 2012 R2 con al menos el paquete acumulativo de actualizaciones 3 para DPM 2012 R2, o como una máquina virtual de Windows en VMWare que se ejecuta en System Center 2012 R2 con al menos el paquete acumulativo de actualizaciones 5.
- Si ejecuta DPM con System Center 2012 SP1, debe instalar el paquete acumulativo de actualizaciones 2 para System Center Data Protection Manager SP1. Esto es necesario para poder instalar Azure Backup Agent.
- El servidor DPM debe tener instalado Windows PowerShell y .Net Framework 4.5.
- DPM puede realizar una copia de seguridad de la mayoría de las cargas de trabajo en Copia de seguridad de Azure. Para obtener una lista completa de compatibilidad, vea a continuación los elementos admitidos en Copia de seguridad de Azure.
- Los datos almacenados en Copia de seguridad de Azure no se pueden recuperar con la opción "copiar en cinta".
- Necesitará una cuenta de Azure con la característica Copia de seguridad de Azure habilitada. En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Lea acerca de los [Precios de Backup](https://azure.microsoft.com/pricing/details/backup/).
- El uso de Copia de seguridad de Azure requiere la instalación de Azure Backup Agent en los servidores de los que desee realizar una copia de seguridad. Cada servidor debe tener al menos 2,5 GB de espacio de almacenamiento local disponible para la ubicación en caché, si bien lo recomendable son 15 GB.
- Los datos se almacenarán en el almacenamiento de almacén de Azure. No hay ningún límite en la cantidad de datos de los que puede realizar una copia de seguridad en el almacén de Copia de seguridad de Azure; sin embargo, el tamaño de un origen de datos (por ejemplo, una máquina virtual o una base de datos) no debe superar 1,65 TB.

Los siguientes tipos de archivo se admiten para la copia de seguridad en Azure:

- Cifrados (solo copias de seguridad completas)
- Comprimidos (copias de seguridad incrementales compatibles)
- Dispersos (copias de seguridad incrementales compatibles)
- Comprimidos y dispersos (tratados como dispersos)

No se admiten los siguientes:

- Servidores de sistemas de archivos que distinguen entre mayúsculas y minúsculas.
- Vínculos físicos (omitidos)
- Puntos de reanálisis (omitidos)
- Cifrados y comprimidos (omitidos)
- Cifrados y dispersos (omitidos)
- Flujo comprimido
- Flujo disperso

>[AZURE.NOTE]A partir de System Center 2012 DPM con SP1 en adelante, puede realizar una copia de seguridad de las cargas protegidas por DPM en Azure con Copia de seguridad de Microsoft Azure.

<!---HONumber=62-->