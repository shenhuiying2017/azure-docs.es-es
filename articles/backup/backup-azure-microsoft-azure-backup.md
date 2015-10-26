<properties
  pageTitle="Preparación para la copia de seguridad de cargas de trabajo en Microsoft Azure | Microsoft Azure"
  description="En este artículo, se proporciona información general sobre las capacidades de carga de trabajo de aplicaciones en Copia de seguridad de Microsoft Azure"
  services="backup"
  documentationCenter=""
  authors="SamirMehta"
  manager="shreeshd"
  editor=""/>

<tags
  ms.service="backup"
  ms.workload="storage-backup-recovery"
  ms.tgt_pltfrm="na"
  ms.devlang="na"
  ms.topic="article"
  ms.date="10/07/2015"
  ms.author="sammehta; jimpark"/>

# Preparación para la copia de seguridad de cargas de trabajo en Microsoft Azure

> [AZURE.SELECTOR]
- [SCDPM](backup-azure-dpm-introduction.md)
- [Azure Backup](backup-azure-microsoft-azure-backup.md)

En este artículo, se trata cómo preparar el entorno para hacer copias de seguridad de las cargas de trabajo, para que pueda realizar copias de seguridad de sus datos en Azure. Con el servidor de Copia de seguridad de Microsoft Azure, puede proteger cargas de trabajo de aplicaciones como máquinas virtuales de Hyper-V, Microsoft SQL Server, SharePoint Server, Microsoft Exchange y clientes Windows en:

- **Disco** (D2D), que proporciona RTO elevados para cargas de trabajo de nivel 1
- **Azure** (D2D2C) para la retención a largo plazo

Además, puede administrar la protección de diversas entidades protegidas (servidores y clientes) desde una única interfaz de usuario local.

>[AZURE.NOTE]La Copia de seguridad de Microsoft Azure hereda la funcionalidad de Data Protection Manager (DPM) para copias de seguridad de cargas de trabajo. Encontrará punteros para la documentación de DPM para algunas de estas capacidades. Sin embargo, el servidor de copias de seguridad de Microsoft Azure no ofrece protección en cinta o integración con System Center.

Puede implementar el servidor de Copia de seguridad de Microsoft Azure como:

- Un servidor físico independiente.
- Una máquina virtual de Hyper-V: puede ejecutar el servidor de copias de seguridad de Microsoft Azure como una máquina virtual hospedada en un servidor host de Hyper-V local para hacer copias de seguridad de los datos locales. Para ver una lista de consideraciones en este entorno, consulte [Instalación de DPM como una máquina virtual en un servidor Hyper-V local](https://technet.microsoft.com/library/dn440941.aspx).
- Una máquina virtual de Windows en VMWare: puede implementar el servidor de copias de seguridad de Microsoft Azure para ofrecer protección para las cargas de trabajo de Microsoft que se ejecutan en máquinas virtuales de Windows en VMWare. En este escenario, el servidor de copias de seguridad de Microsoft Azure se puede implementar como servidor físico independiente, como máquina virtual de Hyper-V o como máquina virtual de Windows en VMWare.
- Una máquina virtual de Azure: puede ejecutar el servidor de copias de seguridad de Microsoft Azure como máquina virtual en Azure para hacer copias de seguridad de las cargas de trabajo de nube que se ejecutan como máquinas virtuales de Azure. Para obtener información sobre esta implementación, consulte [Instalar DPM como máquina virtual de Azure](https://technet.microsoft.com/library/hh758176.aspx).

Además,

- Copia de seguridad de Microsoft Azure puede instalarse en Windows Server 2008 R2 SP1, 2012 y 2012 R2.
- El servidor de Copia de seguridad de Microsoft Azure no puede instalarse en un equipo que tenga instalado SCDPM ni el agente RA de SCDPM.
- El servidor de Copia de seguridad de Microsoft Azure no puede instalarse en un equipo que tenga el agente de Copia de seguridad de Microsoft Azure instalado y registrado con un almacén de Copia de seguridad de Azure.



Una vez seleccionado el servidor en el que se va a instalar Copia de seguridad de Microsoft Azure, necesitará:

- Asegurarse de que se cumplan los requisitos previos del servidor de Copia de seguridad de Microsoft Azure;
- Crear un almacén de copia de seguridad;
- Descargar las credenciales del almacén y
- Descargar los archivos de instalación del servidor de Copia de seguridad de Microsoft Azure.

Cada uno de estos pasos se explica con detalle a continuación:

## Antes de comenzar

En la actualidad, el servidor de Copia de seguridad de Microsoft Azure está disponible solamente para la configuración regional en inglés. Actualmente, Servicios de recuperación de Microsoft Azure está disponible en todas las regiones en que Azure está disponible, a excepción del centro de datos de Microsoft Azure Government y Microsoft Azure en China.

Para realizar la instalación sin problemas, asegúrese de que se cumplen los siguientes requisitos previos antes de instalar el servidor de Copia de seguridad de Microsoft Azure.

- El servidor debe tener conectividad a Internet. El servidor debe tener acceso a Microsoft Azure.
- Los requisitos del servidor para instalar Copia de seguridad de Microsoft Azure son los mismos que para DPM. Consulte estos [requisitos de hardware](https://technet.microsoft.com/library/dn726764.aspx) para obtener información adicional.
- El servidor de Copia de seguridad de Microsoft Azure debe estar unido a un dominio.
- El servidor de Copia de seguridad de Microsoft Azure debe tener instaladas las características de .NET 3.5, .NET 4.0 y .NET 3.5 SP1. Consulte [más información acerca de cómo habilitar .NET Framework](https://technet.microsoft.com/library/dn482071.aspx) para obtener más detalles.
- El servidor de Copia de seguridad de Microsoft Azure debe tener instalado Windows Management Framework 4.0. Puede descargarlo [aquí](http://www.microsoft.com/download/details.aspx?id=40855).
- Para el disco usado como espacio dedicado para el almacenamiento de datos del servidor de copias de seguridad de Microsoft Azure, el tamaño recomendado para el grupo de almacenamiento es 1,5 veces el tamaño de los datos protegidos. Para obtener más información, consulte la sección Discos y almacenamiento de [este tema](https://technet.microsoft.com/library/hh758176.aspx#DPM servidor).

Haga lo siguiente para preparar el servidor de Copia de seguridad de Microsoft Azure para realizar copias de seguridad de los datos:

1. **Crear un almacén de copia de seguridad**: cree un almacén en la consola de Copia de seguridad de Azure.
2. **Descargar las credenciales del almacén**: en Copia de seguridad de Azure, cargue el certificado de administración que creó en el almacén.
3. **Descargar el servidor de Copia de seguridad de Microsoft Azure**: puede descargar el servidor de Copia de seguridad de Microsoft Azure para cargas de trabajo de aplicaciones desde la página de inicio rápido de un almacén de copia de seguridad.

[AZURE.INCLUDE [backup-create-vault](../../includes/backup-create-vault.md)]

[AZURE.INCLUDE [backup-download-credentials](../../includes/backup-download-credentials.md)]

## Descarga del servidor de Copia de seguridad de Microsoft Azure
De forma similar a las credenciales del almacén, puede descargar Copia de seguridad de Microsoft Azure para cargas de trabajo de aplicaciones desde la **página de inicio rápido** del almacén de copia de seguridad.

1. Haga clic en **For Application Workloads (Disk to Disk to Cloud)** (Para cargas de trabajo de aplicaciones (de disco a disco y a nube)).

    ![Pantalla de bienvenida de Copia de seguridad de Microsoft Azure](./media/backup-azure-microsoft-azure-backup/dpm-venus1.png)

2. Haga clic en **Descargar**.

    ![Centro de descarga 1](./media/backup-azure-microsoft-azure-backup/downloadcenter1.png)

3. Seleccione todos los archivos y haga clic en **Next** (Siguiente). ![Centro de descarga 1](./media/backup-azure-microsoft-azure-backup/downloadcenter.png)

    >[AZURE.NOTE]Puesto que el tamaño de descarga de todos los archivos juntos es de más de 3 GB, con un vínculo de descarga a 10 Mbps, se puede tardar hasta 60 minutos en completarla.

4. Coloque todos los archivos en la misma carpeta.

5. Descargue todos los archivos procedentes de la página de descarga de Copia de seguridad de Microsoft Azure.

## Instalación del servidor de Copia de seguridad de Microsoft Azure
Asegúrese de que se cumplen los requisitos previos mencionados en la sección anterior antes de iniciar el programa de instalación.

1. Después de descargar todos los archivos, haga clic en **MicrosoftAzureBackupInstaller.exe**. Aparecerá el **Microsoft Azure Backup Setup Wizard** (Asistente para instalación de Copia de seguridad de Microsoft Azure).

    ![Instalador de Copia de seguridad de Microsoft Azure](./media/backup-azure-microsoft-azure-backup/installer-click.png)

2. Proporcione la ubicación de este servidor a la que se extraerán los archivos y haga clic en **Next** (Siguiente).

    ![Asistente para instalación de Copia de seguridad de Microsoft Azure](./media/backup-azure-microsoft-azure-backup/extract-to.png)

3. Haga clic en **Extract** (Extraer) para comenzar a extraer los archivos de instalación.

    ![Asistente para instalación de Copia de seguridad de Microsoft Azure](./media/backup-azure-microsoft-azure-backup/ready-to-extract.png)

4. Active la casilla para iniciar setup.exe, para empezar a instalar el servidor de Copia de seguridad de Microsoft Azure, y haga clic en **Finish** (Finalizar).

    ![Asistente para instalación de Copia de seguridad de Microsoft Azure](./media/backup-azure-microsoft-azure-backup/launch-setup.png)

5. Haga clic en **Copia de seguridad de Microsoft Azure** para iniciar el asistente para la instalación.

    ![Asistente para instalación de Copia de seguridad de Microsoft Azure](./media/backup-azure-microsoft-azure-backup/launch-screen2.png)

6. En la pantalla de bienvenida, haga clic en **Next** (Siguiente).

    ![Pantalla de bienvenida de Copia de seguridad de Microsoft Azure](./media/backup-azure-microsoft-azure-backup/welcome-screen.png)

7. Haga clic en **Check** (Comprobar) para determinar si se cumplieron los requisitos previos de hardware y software para el servidor de Copia de seguridad de Microsoft Azure.

    ![Requisitos previos de Copia de seguridad de Microsoft Azure1](./media/backup-azure-microsoft-azure-backup/prereq-screen1.png)

8. Si se cumplieron todos los requisitos previos, verá un mensaje que indica que el equipo los cumple. Haga clic en **Siguiente**.

    ![Requisitos previos de Copia de seguridad de Microsoft Azure2](./media/backup-azure-microsoft-azure-backup/prereq-screen2.png)

9. El servidor de Copia de seguridad de Microsoft Azure requiere SQL Server Standard. Seleccione una instancia local de SQL Server Standard 2014 existente o deje que el asistente lo instale. Haga clic en **Check and Install** (Comprobar e instalar) para asegurarse de que el servidor tenga instalados los requisitos previos para SQL.

    ![Copia de seguridad de Microsoft Azure - SQL1](./media/backup-azure-microsoft-azure-backup/sql-setup1.png)

    Si se produce un error con una recomendación para reiniciar el equipo, proceda a reiniciar y haga clic en **Check Again** (Comprobar de nuevo).

    ![Error de filtro SiS](./media/backup-azure-microsoft-azure-backup/sis-filter.png)

10. Una vez que los requisitos previos se instalen correctamente, haga clic en **Next** (Siguiente).

    ![Requisitos previos de Copia de seguridad de Microsoft Azure2](./media/backup-azure-microsoft-azure-backup/sql-setup3.png)

11. Proporcione una ubicación donde instalar los archivos del servidor de Copia de seguridad de Microsoft Azure y haga clic en **Next** (Siguiente).

    ![Requisitos previos de Copia de seguridad de Microsoft Azure2](./media/backup-azure-microsoft-azure-backup/space-screen.png)

    >[AZURE.NOTE]La ubicación temporal es un requisito para hacer copias de seguridad en Azure. Asegúrese de que la ubicación temporal sea al menos el 5% de los datos cuya copia de seguridad se planea hacer en la nube. Para la protección de disco, deben configurarse discos independientes una vez completada la instalación. Para obtener más información acerca de los grupos de almacenamiento, consulte [Configuración de bloques de almacenamiento y almacenamiento en disco](https://technet.microsoft.com/library/hh758075.aspx).

12. Proporcione una contraseña segura para las cuentas de usuario locales con permisos restringidos y haga clic en **Next** (Siguiente).

    ![Requisitos previos de Copia de seguridad de Microsoft Azure2](./media/backup-azure-microsoft-azure-backup/security-screen.png)

    >[AZURE.NOTE]Establecer contraseñas seguras es esencial para la seguridad del sistema. Una contraseña segura es aquella con al menos seis caracteres que no contiene todo el nombre de la cuenta del usuario ni parte de él y que contiene como mínimo tres de las cuatro categorías de caracteres siguientes: mayúsculas, minúsculas, dígitos de base 10 y símbolos (como !, @, #).

13. Seleccione si desea usar *Microsoft Update* para comprobar si hay actualizaciones y haga clic en **Next** (Siguiente).

    >[AZURE.NOTE]Se recomienda que Windows Update se redirija a Microsoft Update, que ofrece actualizaciones importantes y de seguridad para Windows y otros productos como el servidor de Copia de seguridad de Microsoft Azure.

    ![Requisitos previos de Copia de seguridad de Microsoft Azure2](./media/backup-azure-microsoft-azure-backup/update-opt-screen2.png)

14. Revise *Summary of Settings* (Resumen de la configuración) y haga clic en **Install** (Instalar).

    ![Requisitos previos de Copia de seguridad de Microsoft Azure2](./media/backup-azure-microsoft-azure-backup/summary-screen.png)

    La instalación se realiza en fases. En la primera fase, se instala un agente en el servidor y debe registrar el servidor con las credenciales de almacén de Copia de seguridad de Azure válidas que descargó con este producto. Después, el asistente comprueba la conectividad directa a Internet. Si la conectividad a Internet está disponible, puede continuar con la instalación; de lo contrario, debe proporcionar los detalles del proxy para conectarse a Internet.

15. Haga clic en **Next** (Siguiente) para iniciar la fase de instalación de Agente de Servicios de recuperación de Microsoft Azure.

    ![Requisitos previos de Copia de seguridad de Microsoft Azure2](./media/backup-azure-microsoft-azure-backup/proxy.png)

    El asistente comprueba los requisitos previos e instala los que faltan.

16. Haga clic en **Instalar**.

    ![Requisitos previos de Copia de seguridad de Microsoft Azure2](./media/backup-azure-microsoft-azure-backup/mars-prereq.png)

17. Cuando se complete la instalación del agente, haga clic en **Next** (Siguiente) para registrar este servidor con el almacén de Copia de seguridad de Azure.

    ![Requisitos previos de Copia de seguridad de Microsoft Azure2](./media/backup-azure-microsoft-azure-backup/mars-successful.png)

18. Proporcione sus credenciales de almacén de Copia de seguridad de Azure para registrar el servidor y haga clic en **Next** (Siguiente).

    ![Requisitos previos de Copia de seguridad de Microsoft Azure2](./media/backup-azure-microsoft-azure-backup/vault-reg-screen.png)

19. Proporcione una frase de contraseña que se usa para cifrar y descifrar los datos enviados a Azure, así como una ubicación para almacenar esta frase de contraseña. Puede generar una frase de contraseña automáticamente o proporcionar la suya propia, con un mínimo de 16 caracteres. Haga clic en **Siguiente**.

    ![Requisitos previos de Copia de seguridad de Microsoft Azure2](./media/backup-azure-microsoft-azure-backup/encrypt1.png)

    >[AZURE.NOTE]Para mantener la confidencialidad, el servidor de Copia de seguridad de Microsoft Azure no envía esta frase de contraseña junto con los datos a Azure. Necesita almacenar esta frase de contraseña, que será necesaria para restaurar datos de Azure, en una ubicación segura.

    Una vez que se complete correctamente el registro del servidor de Copia de seguridad de Microsoft Azure, se prosigue con la instalación y configuración de SQL Server Standard 2014 (si se selecciona).

    ![Requisitos previos de Copia de seguridad de Microsoft Azure2](./media/backup-azure-microsoft-azure-backup/sql-install-start-screen.png)

    Una vez completada la instalación de SQL, se instalan los componentes del servidor de Copia de seguridad de Microsoft Azure.

    ![Requisitos previos de Copia de seguridad de Microsoft Azure2](./media/backup-azure-microsoft-azure-backup/venus-installation-screen.png)

20. Cuando el producto está instalado y se hayan creado los iconos del escritorio, haga doble clic en el icono para iniciar el producto.

    ![Iconos](./media/backup-azure-microsoft-azure-backup/icons.png)

## Adición de un disco al grupo de almacenamiento

Para realizar copias de seguridad de cargas de trabajo en disco y en Azure, primero deberá agregar un disco al grupo de almacenamiento. Para obtener más información acerca de los discos, consulte [Configuración de bloques de almacenamiento y almacenamiento en disco](https://technet.microsoft.com/library/hh758075.aspx).

Una vez configurados los grupos de almacenamiento, se pueden realizar copias de seguridad de las cargas de trabajo en disco y en Azure.

## Solución de problemas

Si el servidor de Copia de seguridad de Microsoft Azure produce un error durante la fase de instalación (o copia de seguridad o restauración), consulte este [documento de códigos de error](https://support.microsoft.com/kb/3041338) para obtener más información. También puede consultar [Copia de seguridad de Azure - Preguntas más frecuentes](backup-azure-backup-faq.md).

## Preguntas más frecuentes

### Instalación e implementación

P: ¿es compatible la compresión NTFS en los discos y volúmenes del servidor de Copia de seguridad de Microsoft Azure que se usan para las copias de seguridad en disco? <br>R: NTFS **no** es compatible con los discos y volúmenes conectados al servidor de Copia de seguridad de Microsoft Azure.

P: ¿puedo mover el servidor de Copia de seguridad de Microsoft Azure a un nuevo dominio después de la implementación? <br>R: **no** se admite el traslado del servidor de Copia de seguridad de Microsoft Azure a un nuevo dominio después de la implementación.

P: ¿qué recursos puede proteger el servidor de Copia de seguridad de Microsoft Azure si se instala en un controlador de dominio? <br>R: el servidor de Copia de seguridad de Microsoft Azure solo puede proteger los orígenes de datos locales si se instala en el mismo servidor como controlador de dominio. Para que el servidor de Copia de seguridad de Microsoft Azure proteja las cargas de trabajo en otros servidores, no lo instale en el mismo equipo como controlador de dominio.

P: ¿puede el servidor de Copia de seguridad de Microsoft Azure usar una instancia remota de SQL Server? <br>: no, el servidor de Copia de seguridad de Microsoft Azure solo puede usar una instancia local de SQL Server.

### Grupo de almacenamiento del servidor de Copia de seguridad de Microsoft Azure

P: ¿puedo proporcionar un disco virtual (VHD/HDx) en el grupo de almacenamiento del servidor de Copia de seguridad de Microsoft Azure? <br>R: sí.

P: ¿puedo disponer de desduplicación en el grupo de almacenamiento del servidor de Copia de seguridad de Microsoft Azure? <br>R: sí, la desduplicación está disponible en el grupo de almacenamiento de un servidor de Copia de seguridad de Microsoft Azure. La experiencia del usuario con el servidor de Copia de seguridad de Microsoft Azure es exactamente la que se explica en esta [entrada de blog sobre DPM](http://blogs.technet.com/b/dpm/archive/2015/01/06/deduplication-of-dpm-storage-reduce-dpm-storage-consumption.aspx).

P: ¿se pueden usar unidades USB o extraíbles para el grupo de almacenamiento de un servidor de Copia de seguridad de Microsoft Azure? <br>R: no.

### Conectividad del servidor de Copia de seguridad de Microsoft Azure al servicio de Azure

P: ¿cómo afectan la conectividad del servidor de Copia de seguridad de Microsoft Azure al servicio de Azure y el estado de suscripción de Azure a las copias de seguridad y la restauración? <br>R: el servidor de Copia de seguridad de Microsoft Azure requiere conectividad al servicio de Azure para que el producto funcione correctamente. Además, la suscripción de Azure debe encontrarse en un estado correcto.

En la tabla siguiente, se explica la funcionalidad del servidor de Copia de seguridad de Microsoft Azure en función del estado de conectividad y de suscripción de Azure.

| Estado de conectividad | Suscripción de Azure | Copia de seguridad en Azure| Copia de seguridad en disco | Restauración desde Azure | Restauración desde disco |
| -------- | ------- | --------------------- | ------------------- | --------------------------- | ----------------------- |
| Conectado | Active | Permitida | Permitida | Permitida | Permitida |
| Conectado | Expirada | Stopped | Stopped | Permitida | Permitida |
| Normal | Desaprovisionada | Stopped | Stopped | Detenida y puntos de recuperación de Azure eliminados | Stopped |
| Pérdida de conectividad > 15 días | Active | Stopped | Stopped | Permitida | Permitida |
| Pérdida de conectividad > 15 días | Expirada | Stopped | Stopped | Permitida | Permitida |
| Pérdida de conectividad > 15 días | Desaprovisionada | Stopped | Stopped | Detenida y puntos de recuperación de Azure eliminados | Stopped |

P: suponiendo que el estado de suscripción siga siendo *Activa*, ¿qué sucede si se restaura la conectividad del servidor de Copia de seguridad de Microsoft Azure? <br>R: una vez que se reanuda la conectividad del servidor de Copia de seguridad de Microsoft Azure al servicio de Azure y el estado de suscripción es *Activa*, se permiten todas las operaciones del servidor de Copia de seguridad de Microsoft Azure. Las copias de seguridad tanto en disco como en Azure se realizarán de la forma habitual.

P: ¿qué ocurre si el estado *Expirado* de la suscripción se restaura a *Activo*? <br>R: siempre que la conectividad del servidor de Copia de seguridad de Microsoft Azure al servicio de Azure sea normal, una vez restaurado el estado de suscripción del servidor de Copia de seguridad de Microsoft Azure a *Activa*, se permiten todas las operaciones del servidor de Copia de seguridad de Microsoft Azure. Las copias de seguridad tanto en disco como en Azure se realizarán ahora de la forma habitual.

P: ¿qué ocurre si el estado *Desaprovisionada* de la suscripción se restaura a *Activa*? <br>R: siempre que la conectividad del servidor de Copia de seguridad de Microsoft Azure al servicio de Azure sea normal, una vez restaurado el estado de suscripción del servidor de Copia de seguridad de Microsoft Azure a *Activa*, se permiten todas las operaciones del servidor de Copia de seguridad de Microsoft Azure. Sin embargo, los puntos de recuperación de Azure se eliminan y no se pueden restaurar. Se pueden restaurar los puntos de recuperación de disco si las copias de seguridad de disco se encuentran en un intervalo de retención válido.

P: ¿qué excepciones es necesario crear para permitir la conectividad del servidor de Copia de seguridad de Microsoft Azure a un servicio de Azure público? <br>R: debe permitir las siguientes direcciones de dominio en el perfil del firewall: www.msftncsi.com, *.Microsoft.com, *.WindowsAzure.com, *.microsoftonline.com, *.windows.net.

P: ¿Cómo puedo confirmar si el servidor de Copia de seguridad de Microsoft Azure tiene conectividad al servicio de Azure? <br>R: ejecute el cmdlet de PowerShell siguiente en la consola del servidor de Copia de seguridad de Microsoft Azure:

```
Get-DPMCloudConnection
Output:
TRUE - Connected to Azure service
FALSE - Not connected to Azure service
```

P: ¿por qué mi suscripción no tiene el estado *Activa*? ¿Cómo lo cambio a *Activa*? <br>R: inicie sesión en el [portal de suscripción](https://account.windowsazure.com/Subscriptions) y administre su suscripción.

### Facturación

P: ¿cuál es el modelo de facturación para el servidor de Copia de seguridad de Microsoft Azure? <br>R: se facturará a los usuarios según un modelo de instancia protegida. Para obtener más información, consulte las preguntas más frecuentes en la página de [precios](http://azure.microsoft.com/pricing/details/backup/).

P: ¿cuál es el modelo de facturación si solo protejo los datos en disco? <br>R: el modelo de facturación es el mismo que el modelo de instancia protegida. Puesto que estos datos se protegen en el almacenamiento local, no se cobra el almacenamiento de Azure para las copias de seguridad basadas únicamente en discos. En este caso, solo se facturará al cliente por la tarifa de instancia protegida. Para obtener más información acerca de la definición de instancia y cuánto se cobra por instancia, consulte las preguntas más frecuentes en la página de [precios](http://azure.microsoft.com/pricing/details/backup/).

P: ¿qué es el cargo por instancia protegida? <br>R: consulte la página de [precios](http://azure.microsoft.com/pricing/details/backup/).

P: ¿dónde puedo encontrar ejemplos que resalten las diferentes cargas de trabajo de aplicaciones y la tarifa de instancia protegida para ellas? <br>R: consulte la sección "Ejemplos" de la página de [precios](http://azure.microsoft.com/pricing/details/backup/).

P: ¿cómo facturaría el servidor de Copia de seguridad de Microsoft Azure por un origen de datos que esté protegido tanto en el disco como en la nube? <br>R: independientemente de si se hace la copia de seguridad de los datos en disco o en la nube, el servidor de Copia de seguridad de Microsoft Azure cobraría en función de las instancias protegidas. El tamaño de la instancia protegida se calcula en función del tamaño del front-end del origen de datos. Para los datos de copia de seguridad en el almacenamiento de Azure, también se aplican los costos de almacenamiento de Azure.

## Pasos siguientes

Puede usar estos artículos para mejorar la comprensión sobre la protección de cargas de trabajo mediante el servidor de Copia de seguridad de Microsoft Azure.

- [Copia de seguridad de SQL Server](backup-azure-backup-sql.md)
- [Copia de seguridad de una granja de SharePoint](backup-azure-backup-sharepoint.md)
- [Copia de seguridad de otro servidor](backup-azure-alternate-dpm-server.md)

<!---HONumber=Oct15_HO3-->