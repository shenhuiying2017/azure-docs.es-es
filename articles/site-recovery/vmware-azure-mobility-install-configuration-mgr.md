---
title: Automatización de la instalación de Mobility Service para Azure Site Recovery mediante System Center Configuration Manager | Microsoft Docs
description: La información de este artículo le ayuda a automatizar la instalación de Mobility Service mediante System Center Configuration Manager.
services: site-recovery
author: AnoopVasudavan
manager: gauravd
ms.service: site-recovery
ms.topic: article
ms.date: 03/05/2018
ms.author: anoopkv
ms.openlocfilehash: 50328fc3b594a9162ffb9f82f699f43f9106640f
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/05/2018
ms.locfileid: "33942520"
---
# <a name="automate-mobility-service-installation-with-system-center-configuration-manager"></a>Automatización de la instalación de Mobility Service con System Center Configuration Manager

Mobility Service se instala en máquina virtuales de VMware y servidores físicos que quiera replicar en Azure mediante [Azure Site Recovery](site-recovery-overview.md).

En este artículo se proporciona un ejemplo de cómo puede usar System Center Configuration Manager para implementar Mobility Service de Azure Site Recovery en una máquina virtual de VMware. Usar una herramienta de implementación de software como Configuration Manager ofrece las ventajas siguientes:

* Programación de nuevas instalaciones y actualizaciones durante la ventana de mantenimiento planeado para actualizaciones de software
* Implementación a escala en cientos de servidores de forma simultánea

En este artículo se usa System Center Configuration Manager 2012 R2 para demostrar la actividad de implementación. Se supone que usa la versión **9.9.4510.1** o posterior de Mobility Service.

Como alternativa, puede automatizar la instalación de Mobility Service con [DSC de Azure Automation ](vmware-azure-mobility-deploy-automation-dsc.md).

## <a name="prerequisites"></a>requisitos previos

1. Una herramienta de implementación de software, como Configuration Manager, que ya esté implementada en el entorno.
2. Debe crear dos [colecciones de dispositivos](https://technet.microsoft.com/library/gg682169.aspx), una para todos los **servidores Windows** y otra para todos los **servidores Linux** que quiera proteger con Site Recovery.
3. Un servidor de configuración que ya esté registrado en el almacén de Recovery Services.
4. Un recurso compartido de archivos de red seguro (recurso compartido de SMB) al que pueda acceder mediante la máquina de Configuration Manager.

## <a name="deploy-on-windows-machines"></a>Implementación en máquinas Windows
> [!NOTE]
> En este artículo se supone que la dirección IP del servidor de configuración es 192.168.3.121 y que el recurso compartido de archivos de red segura es \\\ContosoSecureFS\MobilityServiceInstallers.

### <a name="prepare-for-deployment"></a>Preparación de la implementación
1. Cree una carpeta en el recurso compartido de red y asígnele el nombre **MobSvcWindows**.
2. Inicie sesión en el servidor de configuración y abra un símbolo del sistema con privilegios de administrador.
3. Ejecute los comandos siguientes para generar un archivo de frase de contraseña:

    `cd %ProgramData%\ASR\home\svsystems\bin`

    `genpassphrase.exe -v > MobSvc.passphrase`
4. Copie el archivo **MobSvc.passphrase** en la carpeta **MobSvcWindows** del recurso compartido de red.
5. Examine el repositorio del instalador en el servidor de configuración mediante la ejecución del comando siguiente:

   `cd %ProgramData%\ASR\home\svsystems\pushinstallsvc\repository`

6. Copie el archivo **Microsoft-ASR\_UA\_*versión*\_Windows\_GA\_*fecha*\_Release.exe** en la carpeta **MobSvcWindows** del recurso compartido de red.
7. Copie el código siguiente y guárdelo como **install.bat** en la carpeta **MobSvcWindows**.

   > [!NOTE]
   > Reemplace los marcadores de posición [CSIP] de este script por los valores reales de la dirección IP del servidor de configuración.

```DOS
Time /t >> C:\Temp\logfile.log
REM ==================================================
REM ==== Clean up the folders ========================
RMDIR /S /q %temp%\MobSvc
MKDIR %Temp%\MobSvc
MKDIR C:\Temp
REM ==================================================

REM ==== Copy new files ==============================
COPY M*.* %Temp%\MobSvc
CD %Temp%\MobSvc
REN Micro*.exe MobSvcInstaller.exe
REM ==================================================

REM ==== Extract the installer =======================
MobSvcInstaller.exe /q /x:%Temp%\MobSvc\Extracted
REM ==== Wait 10s for extraction to complete =========
TIMEOUT /t 10
REM =================================================

REM ==== Perform installation =======================
REM =================================================

CD %Temp%\MobSvc\Extracted
whoami >> C:\Temp\logfile.log
SET PRODKEY=HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Uninstall
REG QUERY %PRODKEY%\{275197FC-14FD-4560-A5EB-38217F80CBD1}
IF NOT %ERRORLEVEL% EQU 0 (
    echo "Product is not installed. Goto INSTALL." >> C:\Temp\logfile.log
    GOTO :INSTALL
) ELSE (
    echo "Product is installed." >> C:\Temp\logfile.log

    echo "Checking for Post-install action status." >> C:\Temp\logfile.log
    GOTO :POSTINSTALLCHECK
)

:POSTINSTALLCHECK
    REG QUERY "HKLM\SOFTWARE\Wow6432Node\InMage Systems\Installed Products\5" /v "PostInstallActions" | Find "Succeeded"
    If %ERRORLEVEL% EQU 0 (
        echo "Post-install actions succeeded. Checking for Configuration status." >> C:\Temp\logfile.log
        GOTO :CONFIGURATIONCHECK
    ) ELSE (
        echo "Post-install actions didn't succeed. Goto INSTALL." >> C:\Temp\logfile.log
        GOTO :INSTALL
    )

:CONFIGURATIONCHECK
    REG QUERY "HKLM\SOFTWARE\Wow6432Node\InMage Systems\Installed Products\5" /v "AgentConfigurationStatus" | Find "Succeeded"
    If %ERRORLEVEL% EQU 0 (
        echo "Configuration has succeeded. Goto UPGRADE." >> C:\Temp\logfile.log
        GOTO :UPGRADE
    ) ELSE (
        echo "Configuration didn't succeed. Goto CONFIGURE." >> C:\Temp\logfile.log
        GOTO :CONFIGURE
    )


:INSTALL
    echo "Perform installation." >> C:\Temp\logfile.log
    UnifiedAgent.exe /Role MS /InstallLocation "C:\Program Files (x86)\Microsoft Azure Site Recovery" /Platform "VmWare" /Silent
    IF %ERRORLEVEL% EQU 0 (
        echo "Installation has succeeded." >> C:\Temp\logfile.log
        (GOTO :CONFIGURE)
    ) ELSE (
        echo "Installation has failed." >> C:\Temp\logfile.log
        GOTO :ENDSCRIPT
    )

:CONFIGURE
    echo "Perform configuration." >> C:\Temp\logfile.log
    cd "C:\Program Files (x86)\Microsoft Azure Site Recovery\agent"
    UnifiedAgentConfigurator.exe  /CSEndPoint "[CSIP]" /PassphraseFilePath %Temp%\MobSvc\MobSvc.passphrase
    IF %ERRORLEVEL% EQU 0 (
        echo "Configuration has succeeded." >> C:\Temp\logfile.log
    ) ELSE (
        echo "Configuration has failed." >> C:\Temp\logfile.log
    )
    GOTO :ENDSCRIPT

:UPGRADE
    echo "Perform upgrade." >> C:\Temp\logfile.log
    UnifiedAgent.exe /Platform "VmWare" /Silent
    IF %ERRORLEVEL% EQU 0 (
        echo "Upgrade has succeeded." >> C:\Temp\logfile.log
    ) ELSE (
        echo "Upgrade has failed." >> C:\Temp\logfile.log
    )
    GOTO :ENDSCRIPT

:ENDSCRIPT
    echo "End of script." >> C:\Temp\logfile.log


```

### <a name="create-a-package"></a>Creación de un paquete

1. Inicie sesión en la consola de Configuration Manager.
2. Vaya a **Biblioteca de software** > **Administración de aplicaciones** > **Paquetes**.
3. Haga clic con el botón derecho en **Paquetes** y seleccione **Crear paquete**.
4. Escriba los valores para nombre, descripción, fabricante, lenguaje y versión.
5. Seleccione la casilla **Este paquete contiene archivos de origen**.
6. Haga clic en **Examinar** y seleccione el recurso compartido de red donde está almacenado el instalador (\\\ContosoSecureFS\MobilityServiceInstaller\MobSvcWindows).

  ![Captura de pantalla del Asistente para crear paquetes y programas](./media/vmware-azure-mobility-install-configuration-mgr/create_sccm_package.png)

7. En la página **Elija el tipo de programa que desea crear**, seleccione **Programa estándar** y haga clic en **Siguiente**.

  ![Captura de pantalla del Asistente para crear paquetes y programas](./media/vmware-azure-mobility-install-configuration-mgr/sccm-standard-program.png)

8. En la página **Especifique la información sobre este programa estándar**, proporcione las siguientes entradas y haga clic en **Siguiente**. (Las demás entradas pueden usar sus valores predeterminados).

  | **Nombre de parámetro** | **Valor** |
  |--|--|
  | NOMBRE | Instalación del Servicio de movilidad de Microsoft Azure (Windows) |
  | Línea de comandos | install.bat |
  | El programa se puede ejecutar | Tanto si un usuario inició sesión como si no |

  ![Captura de pantalla del Asistente para crear paquetes y programas](./media/vmware-azure-mobility-install-configuration-mgr/sccm-program-properties.png)

9. En la página siguiente, seleccione los sistemas operativos de destino. Mobility Service se puede instalar solo en Windows Server 2012 R2, Windows Server 2012 y Windows Server 2008 R2.

  ![Captura de pantalla del Asistente para crear paquetes y programas](./media/vmware-azure-mobility-install-configuration-mgr/sccm-program-properties-page2.png)

10. Para completar el asistente, haga clic en **Siguiente** dos veces.


> [!NOTE]
> El script es compatible con las nuevas instalaciones de los agentes de Mobility Service y las actualizaciones de agentes ya instalados.

### <a name="deploy-the-package"></a>Implementación del paquete
1. En la consola de Configuration Manager, haga clic con el botón derecho en el paquete y seleccione **Distribuir contenido**.
  ![Captura de pantalla de la consola de Configuration Manager](./media/vmware-azure-mobility-install-configuration-mgr/sccm_distribute.png)
2. Seleccione los **[puntos de distribución](https://technet.microsoft.com/library/gg712321.aspx#BKMK_PlanForDistributionPoints)** en los que se deben copiar los paquetes.
3. Realice los pasos del asistente. El paquete comienza a replicarse en los puntos de distribución especificados.
4. Cuando la distribución del paquete finalice, haga clic con el botón derecho en el paquete y seleccione **Implementar**.
  ![Captura de pantalla de la consola de Configuration Manager](./media/vmware-azure-mobility-install-configuration-mgr/sccm_deploy.png)
5. Seleccione la recopilación de dispositivos de Windows Server que creó en la sección de requisitos previos como la recopilación de destino para la implementación.

  ![Captura de pantalla del Asistente para implementar software](./media/vmware-azure-mobility-install-configuration-mgr/sccm-select-target-collection.png)

6. En la página **Especifique el destino del contenido**, seleccione los **puntos de distribución**.
7. En la página **Especificar configuración para controlar cómo se implementará este software**, asegúrese de que el propósito está seleccionado como **requerido**.

  ![Captura de pantalla del Asistente para implementar software](./media/vmware-azure-mobility-install-configuration-mgr/sccm-deploy-select-purpose.png)

8. En la página **Especifique la programación de esta implementación**, especifique una programación. Para más información, consulte [Programación de paquetes](https://technet.microsoft.com/library/gg682178.aspx).
9. En la página **Puntos de distribución**, configure las propiedades en función de las necesidades del centro de datos. Luego realice los pasos del asistente.

> [!TIP]
> Para evitar los reinicios innecesarios, programe la instalación del paquete durante la ventana de mantenimiento mensual o la ventana de actualizaciones de software.

Puede supervisar el progreso de la implementación en la consola de Configuration Manager. Vaya a **Supervisión** > **Implementaciones** > *[nombre del paquete]*.

  ![Captura de pantalla de la opción de Configuration Manager para supervisar implementaciones](./media/vmware-azure-mobility-install-configuration-mgr/report.PNG)

## <a name="deploy-on-linux-machines"></a>Implementación en máquinas Linux
> [!NOTE]
> En este artículo se supone que la dirección IP del servidor de configuración es 192.168.3.121 y que el recurso compartido de archivos de red segura es \\\ContosoSecureFS\MobilityServiceInstallers.

### <a name="prepare-for-deployment"></a>Preparación de la implementación
1. Cree una carpeta en el recurso compartido de red y asígnele el nombre **MobSvcLinux**.
2. Inicie sesión en el servidor de configuración y abra un símbolo del sistema con privilegios de administrador.
3. Ejecute los comandos siguientes para generar un archivo de frase de contraseña:

    `cd %ProgramData%\ASR\home\svsystems\bin`

    `genpassphrase.exe -v > MobSvc.passphrase`
4. Copie el archivo **MobSvc.passphrase** en la carpeta **MobSvcLinux** del recurso compartido de red.
5. Examine el repositorio del instalador en el servidor de configuración mediante la ejecución del comando:

   `cd %ProgramData%\ASR\home\svsystems\pushinstallsvc\repository`

6. Copie los archivos siguientes en la carpeta **MobSvcLinux** del recurso compartido de red:
   * Microsoft-ASR\_UA\*RHEL6-64*release.tar.gz
   * Microsoft-ASR\_UA\*RHEL7-64\*release.tar.gz
   * Microsoft-ASR\_UA\*SLES11-SP3-64\*release.tar.gz
   * Microsoft-ASR\_UA\*SLES11-SP4-64\*release.tar.gz
   * Microsoft-ASR\_UA\*OL6-64\*release.tar.gz
   * Microsoft-ASR\_UA\*UBUNTU-14.04-64\*release.tar.gz


7. Copie el código siguiente y guárdelo como **install_linux.sh** en la carpeta **MobSvcLinux**.
   > [!NOTE]
   > Reemplace los marcadores de posición [CSIP] de este script por los valores reales de la dirección IP del servidor de configuración.

```Bash
#!/usr/bin/env bash

rm -rf /tmp/MobSvc
mkdir -p /tmp/MobSvc
INSTALL_DIR='/usr/local/ASR'
VX_VERSION_FILE='/usr/local/.vx_version'

echo "=============================" >> /tmp/MobSvc/sccm.log
echo `date` >> /tmp/MobSvc/sccm.log
echo "=============================" >> /tmp/MobSvc/sccm.log

if [ -f /etc/oracle-release ] && [ -f /etc/redhat-release ]; then
    if grep -q 'Oracle Linux Server release 6.*' /etc/oracle-release; then
        if uname -a | grep -q x86_64; then
            OS="OL6-64"
            echo $OS >> /tmp/MobSvc/sccm.log
            cp *OL6*.tar.gz /tmp/MobSvc
        fi
    fi
elif [ -f /etc/redhat-release ]; then
    if grep -q 'Red Hat Enterprise Linux Server release 6.* (Santiago)' /etc/redhat-release || \
        grep -q 'CentOS Linux release 6.* (Final)' /etc/redhat-release || \
        grep -q 'CentOS release 6.* (Final)' /etc/redhat-release; then
        if uname -a | grep -q x86_64; then
            OS="RHEL6-64"
            echo $OS >> /tmp/MobSvc/sccm.log
            cp *RHEL6*.tar.gz /tmp/MobSvc
        fi
    elif grep -q 'Red Hat Enterprise Linux Server release 7.* (Maipo)' /etc/redhat-release || \
        grep -q 'CentOS Linux release 7.* (Core)' /etc/redhat-release; then
        if uname -a | grep -q x86_64; then
            OS="RHEL7-64"
            echo $OS >> /tmp/MobSvc/sccm.log
            cp *RHEL7*.tar.gz /tmp/MobSvc
                fi
    fi
elif [ -f /etc/SuSE-release ] && grep -q 'VERSION = 11' /etc/SuSE-release; then
    if grep -q "SUSE Linux Enterprise Server 11" /etc/SuSE-release && grep -q 'PATCHLEVEL = 3' /etc/SuSE-release; then
        if uname -a | grep -q x86_64; then
            OS="SLES11-SP3-64"
            echo $OS >> /tmp/MobSvc/sccm.log
            cp *SLES11-SP3*.tar.gz /tmp/MobSvc
        fi
    elif grep -q "SUSE Linux Enterprise Server 11" /etc/SuSE-release && grep -q 'PATCHLEVEL = 4' /etc/SuSE-release; then
        if uname -a | grep -q x86_64; then
            OS="SLES11-SP4-64"
            echo $OS >> /tmp/MobSvc/sccm.log
            cp *SLES11-SP4*.tar.gz /tmp/MobSvc
        fi
    fi
elif [ -f /etc/lsb-release ] ; then
    if grep -q 'DISTRIB_RELEASE=14.04' /etc/lsb-release ; then
       if uname -a | grep -q x86_64; then
           OS="UBUNTU-14.04-64"
           echo $OS >> /tmp/MobSvc/sccm.log
           cp *UBUNTU-14*.tar.gz /tmp/MobSvc
       fi
    fi
else
    exit 1
fi

if [ -z "$OS" ]; then
    exit 1
fi

Install()
{
    echo "Perform Installation." >> /tmp/MobSvc/sccm.log
    ./install -q -d ${INSTALL_DIR} -r MS -v VmWare
    RET_VAL=$?
    echo "Installation Returncode: $RET_VAL" >> /tmp/MobSvc/sccm.log
    if [ $RET_VAL -eq 0 ]; then
        echo "Installation has succeeded. Proceed to configuration." >> /tmp/MobSvc/sccm.log
        Configure
    else
        echo "Installation has failed." >> /tmp/MobSvc/sccm.log
        exit $RET_VAL
    fi
}

Configure()
{
    echo "Perform configuration." >> /tmp/MobSvc/sccm.log
    ${INSTALL_DIR}/Vx/bin/UnifiedAgentConfigurator.sh -i [CSIP] -P MobSvc.passphrase
    RET_VAL=$?
    echo "Configuration Returncode: $RET_VAL" >> /tmp/MobSvc/sccm.log
    if [ $RET_VAL -eq 0 ]; then
        echo "Configuration has succeeded." >> /tmp/MobSvc/sccm.log
    else
        echo "Configuration has failed." >> /tmp/MobSvc/sccm.log
        exit $RET_VAL
    fi
}

Upgrade()
{
    echo "Perform Upgrade." >> /tmp/MobSvc/sccm.log
    ./install -q -v VmWare
    RET_VAL=$?
    echo "Upgrade Returncode: $RET_VAL" >> /tmp/MobSvc/sccm.log
    if [ $RET_VAL -eq 0 ]; then
        echo "Upgrade has succeeded." >> /tmp/MobSvc/sccm.log
    else
        echo "Upgrade has failed." >> /tmp/MobSvc/sccm.log
        exit $RET_VAL
    fi
}

cp MobSvc.passphrase /tmp/MobSvc
cd /tmp/MobSvc

tar -zxvf *.tar.gz

if [ -e ${VX_VERSION_FILE} ]; then
    echo "${VX_VERSION_FILE} exists. Checking for configuration status." >> /tmp/MobSvc/sccm.log
    agent_configuration=$(grep ^AGENT_CONFIGURATION_STATUS "${VX_VERSION_FILE}" | cut -d"=" -f2 | tr -d " ")
    echo "agent_configuration=$agent_configuration" >> /tmp/MobSvc/sccm.log
     if [ "$agent_configuration" == "Succeeded" ]; then
        echo "Agent is already configured. Proceed to Upgrade." >> /tmp/MobSvc/sccm.log
        Upgrade
    else
        echo "Agent is not configured. Proceed to Configure." >> /tmp/MobSvc/sccm.log
        Configure
    fi
else
    Install
fi

cd /tmp

```

### <a name="create-a-package"></a>Creación de un paquete

1. Inicie sesión en la consola de Configuration Manager.
2. Vaya a **Biblioteca de software** > **Administración de aplicaciones** > **Paquetes**.
3. Haga clic con el botón derecho en **Paquetes** y seleccione **Crear paquete**.
4. Escriba los valores para nombre, descripción, fabricante, lenguaje y versión.
5. Seleccione la casilla **Este paquete contiene archivos de origen**.
6. Haga clic en **Examinar** y seleccione el recurso compartido de red donde está almacenado el instalador (\\\ContosoSecureFS\MobilityServiceInstaller\MobSvcLinux).

  ![Captura de pantalla del Asistente para crear paquetes y programas](./media/vmware-azure-mobility-install-configuration-mgr/create_sccm_package-linux.png)

7. En la página **Elija el tipo de programa que desea crear**, seleccione **Programa estándar** y haga clic en **Siguiente**.

  ![Captura de pantalla del Asistente para crear paquetes y programas](./media/vmware-azure-mobility-install-configuration-mgr/sccm-standard-program.png)

8. En la página **Especifique la información sobre este programa estándar**, proporcione las siguientes entradas y haga clic en **Siguiente**. (Las demás entradas pueden usar sus valores predeterminados).

    | **Nombre de parámetro** | **Valor** |
  |--|--|
  | NOMBRE | Instalación del Servicio de movilidad de Microsoft Azure (Linux) |
  | Línea de comandos | ./install_linux.sh |
  | El programa se puede ejecutar | Tanto si un usuario inició sesión como si no |

  ![Captura de pantalla del Asistente para crear paquetes y programas](./media/vmware-azure-mobility-install-configuration-mgr/sccm-program-properties-linux.png)

9. En la página siguiente, seleccione **Este programa puede ejecutarse en cualquier plataforma**.
  ![Captura de pantalla del Asistente para crear paquetes y programas](./media/vmware-azure-mobility-install-configuration-mgr/sccm-program-properties-page2-linux.png)

10. Para completar el asistente, haga clic en **Siguiente** dos veces.

> [!NOTE]
> El script es compatible con las nuevas instalaciones de los agentes de Mobility Service y las actualizaciones de agentes ya instalados.

### <a name="deploy-the-package"></a>Implementación del paquete
1. En la consola de Configuration Manager, haga clic con el botón derecho en el paquete y seleccione **Distribuir contenido**.
  ![Captura de pantalla de la consola de Configuration Manager](./media/vmware-azure-mobility-install-configuration-mgr/sccm_distribute.png)
2. Seleccione los **[puntos de distribución](https://technet.microsoft.com/library/gg712321.aspx#BKMK_PlanForDistributionPoints)** en los que se deben copiar los paquetes.
3. Realice los pasos del asistente. El paquete comienza a replicarse en los puntos de distribución especificados.
4. Cuando la distribución del paquete finalice, haga clic con el botón derecho en el paquete y seleccione **Implementar**.
  ![Captura de pantalla de la consola de Configuration Manager](./media/vmware-azure-mobility-install-configuration-mgr/sccm_deploy.png)
5. Seleccione la recopilación de dispositivos de Linux Server que creó en la sección de requisitos previos como la recopilación de destino para la implementación.

  ![Captura de pantalla del Asistente para implementar software](./media/vmware-azure-mobility-install-configuration-mgr/sccm-select-target-collection-linux.png)

6. En la página **Especifique el destino del contenido**, seleccione los **puntos de distribución**.
7. En la página **Especificar configuración para controlar cómo se implementará este software**, asegúrese de que el propósito está seleccionado como **requerido**.

  ![Captura de pantalla del Asistente para implementar software](./media/vmware-azure-mobility-install-configuration-mgr/sccm-deploy-select-purpose.png)

8. En la página **Especifique la programación de esta implementación**, especifique una programación. Para más información, consulte [Programación de paquetes](https://technet.microsoft.com/library/gg682178.aspx).
9. En la página **Puntos de distribución**, configure las propiedades en función de las necesidades del centro de datos. Luego realice los pasos del asistente.

Mobility Service se instala en la recopilación de dispositivos de Linux Server según la programación configurada.


## <a name="uninstall-the-mobility-service"></a>Desinstalación de Mobility Service
Como en el caso de la instalación, puede crear paquetes de Configuration Manager para desinstalar Mobility Service. Para ello, use el script siguiente:

```
Time /t >> C:\logfile.log
REM ==================================================
REM ==== Check if Mob Svc is already installed =======
REM ==== If not installed no operation required ========
REM ==== Else run uninstall command =====================
REM ==== {275197FC-14FD-4560-A5EB-38217F80CBD1} is ====
REM ==== guid for Mob Svc Installer ====================
whoami >> C:\logfile.log
NET START | FIND "InMage Scout Application Service"
IF  %ERRORLEVEL% EQU 1 (GOTO :INSTALL) ELSE GOTO :UNINSTALL
:NOOPERATION
                echo "No Operation Required." >> c:\logfile.log
                GOTO :ENDSCRIPT
:UNINSTALL
                echo "Uninstall" >> C:\logfile.log
                MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1} /L+*V "C:\ProgramData\ASRSetupLogs\UnifiedAgentMSIUninstall.log"
:ENDSCRIPT

```

## <a name="next-steps"></a>Pasos siguientes
Ahora está listo para [habilitar la protección](vmware-azure-enable-replication.md) de las máquinas virtuales.
