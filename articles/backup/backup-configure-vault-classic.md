---
title: "Realización de copias de seguridad de servidores o estaciones de trabajo Windows en Azure (modelo clásico) | Microsoft Docs"
description: "Realice copias de seguridad de servidores o clientes Windows en un almacén de copia de seguridad en Azure. Vaya a la sección de aspectos básicos para proteger archivos y carpetas en un almacén de copia de seguridad mediante el Agente de Azure Backup."
services: backup
documentationcenter: 
author: markgalioto
manager: carmonm
editor: 
keywords: "almacén de copia de seguridad; copia de seguridad de un equipo de Windows Server; ventanas de copia de seguridad;"
ms.assetid: 3b543bfd-8978-4f11-816a-0498fe14a8ba
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/11/2017
ms.author: markgal;trinadhk;
ms.openlocfilehash: a8daa6a4655b72936b6299c0fa5b80459ffa5da3
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/18/2017
---
# <a name="back-up-a-windows-server-or-workstation-to-azure-using-the-classic-portal"></a>Realización de una copia de seguridad en Azure de un servidor o estación de trabajo Windows con el Portal clásico
> [!div class="op_single_selector"]
> * [Portal clásico](backup-configure-vault-classic.md)
> * [Portal de Azure](backup-configure-vault.md)
>
>

En este artículo, se tratan los procedimientos necesarios para preparar el entorno y crear una copia de seguridad de un servidor (o estación de trabajo) de Windows Server en Azure. También se describen los aspectos que se deben tener en cuenta al implementar la solución de copia de seguridad. Si está interesado en probar el servicio Copia de seguridad de Azure por primera vez, este artículo le guiará rápidamente en ese proceso.

Azure tiene dos modelos de implementación diferentes para crear y trabajar con recursos: el de Resource Manager y el clásico. Este artículo trata del modelo de implementación clásico. Microsoft recomienda que las implementaciones más recientes usen el modelo del Administrador de recursos.

## <a name="before-you-start"></a>Antes de comenzar
Si desea crear una copia de seguridad de un servidor o cliente en Azure, necesita una cuenta de Azure. En caso de no tener ninguna, puede crear una [cuenta gratuita](https://azure.microsoft.com/free/) en tan solo unos minutos.

## <a name="create-a-backup-vault"></a>Creación de un almacén de copia de seguridad
Para hacer una copia de seguridad de los archivos y las carpetas de un servidor o cliente, debe crear un almacén de copia de seguridad en la región geográfica donde desea almacenar los datos.

> [!IMPORTANT]
> A partir de marzo de 2017, ya no podrá usar el portal clásico para crear almacenes de Backup.
>
> Ahora puede actualizar los almacenes de Backup a almacenes de Recovery Services. Para más información, consulte el artículo [Actualización de un almacén de Backup a un almacén de Recovery Services](backup-azure-upgrade-backup-to-recovery-services.md). Microsoft anima a actualizar los almacenes de Backup a almacenes de Recovery Services.<br/> **15 de octubre de 2017**, ya no podrá usar PowerShell para crear almacenes de Backup. <br/> **A partir del 1 de noviembre de 2017**:
>- Los almacenes de Backup restantes se actualizarán automáticamente a almacenes de Recovery Services.
>- No podrá acceder a los datos de copia de seguridad en el portal clásico. En su lugar, utilice Azure Portal para tener acceso a los datos de copia de seguridad en los almacenes de Recovery Services.
>


## <a name="download-the-vault-credential-file"></a>Descargar el archivo de credenciales de almacén
La máquina local se debe autenticar con un almacén de copia de seguridad antes de poder crear una copia de seguridad de los datos en Azure. La autenticación se realiza mediante las *credenciales de almacén*. El archivo de credenciales de almacén se descarga a través de un canal seguro desde el portal clásico. La clave privada de certificado no se conserva en el portal ni en el servicio.

### <a name="to-download-the-vault-credential-file-to-a-local-machine"></a>Para descargar el archivo de credenciales de almacén en una máquina local:
1. En el panel de navegación de la izquierda, haga clic en **Servicios de recuperación**y, luego, seleccione el almacén de copia de seguridad que creó.

    ![IR completado](./media/backup-configure-vault-classic/rs-left-nav.png)
2. En la página de Inicio rápido, haga clic en **Descargar credenciales de almacén**.

   El portal clásico genera una credencial de almacén mediante una combinación del nombre del almacén y la fecha actual. El archivo de credenciales de almacén se utiliza solo durante el flujo de trabajo de registro y expira tras 48 horas.

   El archivo de credenciales de almacén se puede descargar desde el portal.
3. Haga clic en **Guardar** para descargar el archivo de credenciales de almacén en la carpeta Descargas de la cuenta local. También puede seleccionar **Guardar como** en el menú **Guardar** para especificar una ubicación para el archivo de credenciales de almacén.

   > [!NOTE]
   > Asegúrese de que las credenciales de almacén se guardan en una ubicación a la que se pueda acceder desde la máquina. Si se almacena en un recurso compartido de archivos o en un bloque de mensajes del servidor, compruebe que tiene permiso de acceso a él.
   >
   >

## <a name="download-install-and-register-the-backup-agent"></a>Descarga, instalación y registro del agente de Backup
Después de crear el almacén de copia de seguridad y de descargar el archivo de credenciales de almacén, se debe instalar un agente en cada una de sus máquinas con Windows.

### <a name="to-download-install-and-register-the-agent"></a>Para descargar, instalar y registrar el agente:
1. Haga clic en **Servicios de recuperación**y, luego, seleccione el almacén de copia de seguridad que desea registrar en un servidor.
2. En la página Inicio rápido, haga clic en el **Agente para Windows Server, System Center Data Protection Manager o cliente de Windows**. A continuación, haga clic en **Guardar**.

    ![Guardar agente](./media/backup-configure-vault-classic/agent.png)
3. Una vez que se descargue el archivo MARSagentinstaller.exe, haga clic en **Ejecutar** (o haga doble clic en **MARSAgentInstaller.exe** en la ubicación guardada).
4. Elija la carpeta de instalación y la carpeta de caché que se requieren para el agente y, luego, haga clic en **Siguiente**. La ubicación de caché que especifique debe tener un espacio libre de, como mínimo, el 5% de los datos de copia de seguridad.
5. Puede continuar para conectarse a Internet a través de la configuración predeterminada de proxy.             Si usa un servidor proxy para conectarse a Internet, en la página Configuración de proxy, active la casilla **Utilice una configuración de proxy personalizada** y, luego, escriba los detalles del servidor proxy. Si usa un proxy autenticado, escriba los detalles de nombre y contraseña del usuario y, luego, haga clic en **Siguiente**.
6. Haga clic en **Instalar** para comenzar la instalación del agente. El agente de Copia de seguridad instalar .NET Framework 4.5 y Windows PowerShell (si todavía no está instalado) para completar la instalación.
7. Una vez que se instale el agente, haga clic en **Proceder al registro** para continuar con el flujo de trabajo.
8. En la página Identificación del almacén, busque y seleccione el archivo de credenciales de almacén que descargó anteriormente.

    El archivo de credenciales de almacén es válido solo durante 48 horas una vez que se lo descarga del portal. Si encuentra un error en esta página (como "El archivo de credenciales de almacén especificado expiró"), inicie sesión en el portal y vuelva a descargar el archivo de credenciales de almacén.

    Asegúrese de que el archivo de credenciales de almacén está disponible en una ubicación a la que puede tener acceso la aplicación de instalación. Si encuentra errores relacionados con el acceso, copie el archivo de credenciales de almacén en una ubicación temporal en la misma máquina y vuelva a intentar la operación.

    Si encuentra un error de credenciales de almacén como "Las credenciales del almacén no son válidas", el archivo está dañado o no tiene asociadas las credenciales más recientes con el Servicio de recuperación. Vuelva a intentar la operación después de descargar un nuevo archivo de credenciales de almacén desde el portal. Este error también se puede producir si un usuario hace clic varias veces en la opción **Descargar credenciales de almacén** en sucesión rápida. En este caso, solo es válido el último archivo de credenciales de almacén.
9. En la página Configuración de cifrado, puede generar una frase de contraseña o proporcionarla (con un mínimo de 16 caracteres). Recuerde guardar la frase de contraseña en una ubicación segura.
10. Haga clic en **Finalizar** El Asistente para registrar servidor registra el servidor con el servicio Copia de seguridad.

    > [!WARNING]
    > Si pierde u olvida la frase de contraseña, Microsoft no puede ayudarle a recuperar los datos de copia de seguridad. El usuario posee la frase de contraseña de cifrado y Microsoft no puede verla. Guarde el archivo en una ubicación protegida, ya que será necesario durante una operación de recuperación.
    >
    >

11. Una vez que se establezca la clave de cifrado, deje activada la casilla **Inicio del agente de Microsoft Azure Recovery Services** y luego haga clic en **Cerrar**.

## <a name="complete-the-initial-backup"></a>Realización de la copia de seguridad inicial
La copia de seguridad inicial incluye dos tareas clave:

* Creación de la programación de la copia de seguridad.
* Creación de copias de seguridad de archivos y carpetas por primera vez.

Una vez que la directiva de copia de seguridad completa la copia de seguridad inicial, crea puntos de copia de seguridad que puede usar si necesita recuperar los datos. Para ello, la directiva de copia de seguridad se basa en la programación que define.

### <a name="to-schedule-the-backup"></a>Para programar la copia de seguridad
1. Abra el agente de Copia de seguridad de Microsoft Azure. (Se abrirá automáticamente si dejó activada la casilla **Inicio del agente de Microsoft Azure Recovery Services** cuando cerró el Asistente para registrar servidor). Para encontrarlo, busque **Copia de seguridad de Microsoft Azure**en la máquina.

    ![Lanzamiento del agente de Copia de seguridad de Azure](./media/backup-configure-vault-classic/snap-in-search.png)
2. En el Agente de Copia de seguridad, haga clic en **Programar copia de seguridad**.

    ![Programar una copia de seguridad de Windows Server](./media/backup-configure-vault-classic/schedule-backup-close.png)
3. En la página de introducción del Asistente para programar copias de seguridad, haga clic en **Siguiente**.
4. En la página Seleccionar elementos de los que realizar copia de seguridad, haga clic en **Agregar elementos**.
5. Seleccione los archivos y las carpetas de los que desea crear la copia de seguridad y, luego, haga clic en **Aceptar**.
6. Haga clic en **Siguiente**.
7. En la página **Especifique la programación de copia de seguridad**, indique la **programación de copia de seguridad** y haga clic en **Siguiente**.

    Puede programar copias de seguridad diarias (con una frecuencia máxima de tres veces al día) o semanales.

    ![Elementos para la copia de seguridad de Windows Server](./media/backup-configure-vault-classic/specify-backup-schedule-close.png)

   > [!NOTE]
   > Si desea más información sobre cómo especificar la programación de las copias de seguridad, consulte el artículo [Usar la copia de seguridad de Azure para cambiar su infraestructura de cintas](backup-azure-backup-cloud-as-tape.md).
   >
   >

8. En la página **Seleccione la directiva de retención**, elija la **directiva de retención** para la copia de seguridad.

    La directiva de retención especifica el tiempo durante el que se almacenará la copia de seguridad. En vez de especificar solo una directiva para todos los puntos de copia de seguridad, puede especificar directivas de retención distintas en función de cuándo se realice la copia de seguridad. Puede modificar las directivas de retención diarias, semanales, mensuales y anuales según sus necesidades.
9. En la página Elija el tipo de copia de seguridad inicial, elija el tipo de copia de seguridad inicial. Deje activada la opción **Automáticamente a través de la red** y, luego, haga clic en **Siguiente**.

    Puede hacer una copia de seguridad automáticamente en la red, o puede realizar una copia sin conexión. En el resto de este artículo, se describe el proceso para crear automáticamente una copia de seguridad. Si prefiere crear una copia de seguridad sin conexión, consulte el artículo [Flujo de trabajo de copia de seguridad sin conexión en Copia de seguridad de Azure](backup-azure-backup-import-export.md) para más información.
10. En la página Confirmación, revise la información y, luego, haga clic en **Finalizar**.
11. Cuando el asistente termine de crear la programación de copia de seguridad, haga clic en **Cerrar**.

### <a name="enable-network-throttling-optional"></a>Habilitación de la velocidad moderada de la red (opcional)
El agente de Copia de seguridad brinda limitación de la red. Esta limitación controla cómo se utiliza el ancho de banda de red durante la transferencia de datos. Este control puede resultar útil si necesita realizar una copia de seguridad durante las horas de trabajo, pero no quiere que el proceso interfiera con otro tráfico de Internet. La limitación se aplica a las actividades de copia de seguridad y restauración.

**Para habilitar la limitación de red**

1. En el agente de Copia de seguridad, haga clic en **Cambiar propiedades**.

    ![Cambiar propiedades](./media/backup-configure-vault-classic/change-properties.png)
2. En la pestaña **Limitación**, active la casilla **Habilitar el límite de uso del ancho de banda de Internet para operaciones de copia de seguridad**.

    ![Limitación de la red](./media/backup-configure-vault-classic/throttling-dialog.png)
3. Una vez que se ha habilitado la limitación, especifique el ancho de banda permitido para la transferencia de datos de copia de seguridad durante la **jornada laboral** y las **horas de descanso**.

    Los valores de ancho de banda comienzan en 512 kilobytes por segundo (Kbps) y pueden subir hasta 1023 megabytes por segundo (Mbps). También puede designar el inicio y el final de la **jornada laboral**, así como qué días de la semana se consideran laborables. Las horas que se encuentran fuera de las horas laborables designadas se consideran como no laborables.
4. Haga clic en **Aceptar**.

### <a name="to-back-up-now"></a>Para crear ahora mismo una copia de seguridad
1. En el agente de Copia de seguridad, haga clic en **Iniciar copia de seguridad** para completar la propagación inicial a través de la red.

    ![Realizar copia de seguridad de Windows Server ahora](./media/backup-configure-vault-classic/backup-now.png)
2. En la página Confirmación, revise la configuración que el asistente para iniciar copia de seguridad usará para crear la copia de seguridad de la máquina. Luego, haga clic en **Crear copia de seguridad**.
3. Haga clic en **Cerrar** para cerrar el asistente. Si lo hace antes de que finalice el proceso de copia de seguridad, el asistente se sigue ejecutando en segundo plano.

Una vez que finalice la copia de seguridad inicial, el estado **Trabajo completado** se refleja en la consola de Copia de seguridad.

![IR completado](./media/backup-configure-vault-classic/ircomplete.png)

## <a name="next-steps"></a>Pasos siguientes
* Regístrese para obtener una [cuenta de Azure gratuita](https://azure.microsoft.com/free/).

Para más información sobre la copia de seguridad de máquinas virtuales u otras cargas de trabajo, consulte:

* [Preparación del entorno de copia de seguridad de máquinas virtuales de Azure](backup-azure-vms-prepare.md)
* [Preparación para la copia de seguridad de cargas de trabajo en Microsoft Azure](backup-azure-microsoft-azure-backup.md)
* [Preparación para la copia de seguridad de cargas de trabajo en Azure con DPM](backup-azure-dpm-introduction.md)
