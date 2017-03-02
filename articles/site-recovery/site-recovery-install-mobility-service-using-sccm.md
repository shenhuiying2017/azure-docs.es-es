---
title: "Automatización de la instalación de Servicio de movilidad para Azure Site Recovery mediante herramientas de implementación de software | Microsoft Docs."
description: "Este artículo le ayuda a automatizar la instalación de Servicio de movilidad mediante herramientas de implementación de software como System Center Configuration Manager"
services: site-recovery
documentationcenter: 
author: AnoopVasudavan
manager: gauravd
editor: 
ms.assetid: 
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 1/10/2017
ms.author: anoopkv
translationtype: Human Translation
ms.sourcegitcommit: 93ac6629df82b1a5b8d14a1ad289e1e462b49b17
ms.openlocfilehash: 8883b2d15592ea5e0c286bd6e6fc7c38134326a7
ms.lasthandoff: 02/22/2017

---
# <a name="automate-mobility-service-installation-using-software-deployment-tools"></a>Automatización de la instalación de Servicio de movilidad mediante herramientas de implementación de software

En este artículo se proporciona un ejemplo de cómo puede usar System Center Configuration Manager (SCCM) para implementar el Servicio de movilidad de Azure Site Recovery en el centro de datos. Usar una herramienta de implementación de software como SCCM ofrece las ventajas siguientes
* Programación de la instalación, ya sea de instalaciones nuevas o actualizaciones, durante la ventana de mantenimiento planeada para las actualizaciones de software.
* Implementación a escala en cientos de servidores de forma simultánea


> [!NOTE]
> En este artículo se usa System Center Configuration Manager 2012 R2 para demostrar la actividad de implementación. También puede automatizar la instalación del Servicio de movilidad mediante [Azure Automation y la configuración de estado deseado](site-recovery-automate-mobility-service-install.md).

## <a name="prerequisites"></a>Requisitos previos
1. Una herramienta de implementación de software, como System Center Configuration Manager (SCCM), que ya esté implementada en el entorno.
  * Cree dos [recopilaciones de dispositivos](https://technet.microsoft.com/library/gg682169.aspx), una para todos los **servidores Windows** y otra para todos los **servidores Linux** que desea proteger con Azure Site Recovery.
3. Un servidor de configuración que ya esté registrado con Azure Site Recovery.
4. Un recurso compartido de archivos de red seguro (recurso compartido SMB) al que pueda tener acceso el servidor SCCM.

## <a name="deploy-mobility-service-on-computers-running-microsoft-windows-operating-systems"></a>Implementación del Servicio de movilidad en equipos que ejecutan sistemas operativos de Microsoft Windows
> [!NOTE]
> En este artículo se da por hecho lo siguiente:
> 1. La dirección IP el servidor de configuración es 192.168.3.121
> 2. El recurso compartido de archivos de red seguro es \\\ContosoSecureFS\MobilityServiceInstallers

### <a name="step-1-prepare-for-deployment"></a>Paso 1: Preparación de la implementación
1. Cree una carpeta en el recurso compartido de red y asígnele el nombre **MobSvcWindows**.
2. Inicie sesión en el servidor de configuración y abra un símbolo del sistema con privilegios de administrador.
3. Ejecute los comandos siguientes para generar un archivo de frase de contraseña.

    `cd %ProgramData%\ASR\home\svsystems\bin`

    `genpassphrase.exe -v > MobSvc.passphrase`
6. Copie el archivo MobSvc.passphrase en la carpeta MobSvcWindows del recurso compartido de red.
5. A continuación, ejecute el comando para ir al repositorio del instalador en el servidor de configuración.

  `cd %ProgramData%\ASR\home\svsystems\puhsinstallsvc\repository`
6. Copie el archivo **Microsoft-ASR\_UA\_*versión*\_Windows\_GA\_*fecha*\_Release.exe** en la carpeta **MobSvcWindows** del recurso compartido de red.
7. Copie el código que aparece a continuación y guárdelo como **install.bat** en la carpeta **MobSvcWindows**.
> [!NOTE]
> Recuerde reemplazar los marcadores de posición [CSIP] del script que aparece a continuación por los valores reales de la dirección IP del servidor de configuración.

  [!INCLUDE [site-recovery-sccm-windows-script](../../includes/site-recovery-sccm-windows-script.md)]

### <a name="step-2-create-a-package"></a>Paso 2: Creación de un paquete

1. Inicie sesión en la consola de System Center Configuration Manager.
2. Vaya a **Biblioteca de software** > **Administración de aplicaciones** > **Paquetes**.
3. Haga clic con el botón derecho en **Paquetes** y seleccione **Crear paquete**.
4. Escriba los valores para nombre, descripción, fabricante, lenguaje y versión.
5. Active la casilla **Este paquete contiene archivos de origen**.
6. Haga clic en el botón **Examinar** y seleccione el recurso compartido de red donde está almacenado el instalador (\\\ContosoSecureFS\MobilityServiceInstaller\MobSvcWindows)

  ![create-sccm-package](./media/site-recovery-install-mobility-service-using-sccm/create_sccm_package.png)

7. En la página **Elija el tipo de programa que desea crear**, seleccione **Programa estándar** y haga clic en **Siguiente**.

  ![create-sccm-package](./media/site-recovery-install-mobility-service-using-sccm/sccm-standard-program.png)
8. En la página **Especifique la información sobre este programa estándar**, proporcione las siguientes entradas y haga clic en **Siguiente**. (Las demás entradas pueden quedar con sus valores predeterminados).

  ![sccm-package-properties](./media/site-recovery-install-mobility-service-using-sccm/sccm-program-properties.png)   
| **Nombre de parámetro** | **Valor** |
|--|--|
| Nombre | Instalación del Servicio de movilidad de Microsoft Azure (Windows) |
| Línea de comandos | install.bat |
| El programa se puede ejecutar | Tanto si un usuario inició sesión como si no |
9. En la página siguiente, seleccione los sistemas operativos de destino. El Servicio de movilidad se puede instalar solo en Windows Server 2012 R2, Windows Server 2012 y Windows Server 2008 R2.

  ![sccm-package-properties-page2](./media/site-recovery-install-mobility-service-using-sccm/sccm-program-properties-page2.png)   
10. Haga clic en Siguiente dos veces para completar el asistente.

> [!NOTE]
> El script es compatible con ambas instalaciones de los agentes del Servicio de movilidad y la actualización de agentes ya instalados.

### <a name="step-3-deploy-the-package"></a>Paso 3: Implementación del paquete
1. En la consola de SCCM, haga clic con el botón derecho en el paquete y seleccione **Distribuir contenido**
  ![distribute-sccm-package](./media/site-recovery-install-mobility-service-using-sccm/sccm_distribute.png).
2. Seleccione los **[puntos de distribución](https://technet.microsoft.com/library/gg712321.aspx#BKMK_PlanForDistributionPoints)** en los que se deben copiar los paquetes.
3. Una vez que complete el asistente, el paquete comenzará a replicarse en los puntos de distribución especificados.
4. Cuando la distribución de paquete finalice, haga clic con el botón derecho en el paquete y seleccione **Implementar**
  ![deploy-sccm-package](./media/site-recovery-install-mobility-service-using-sccm/sccm_deploy.png).
5. Seleccione la recopilación de dispositivos de Windows Server que creó en la sección de requisitos previos como la recopilación de destino para la implementación.

  ![sccm-select-target-collection](./media/site-recovery-install-mobility-service-using-sccm/sccm-select-target-collection.png)
6. En la página **Especifique el destino del contenido**, seleccione los **puntos de distribución**.
7. En la página **Especifique la configuración para controlar cómo se implementa este software**, asegúrese de que el propósito está seleccionado como requerido.

  ![sccm-deploy-select-purpose](./media/site-recovery-install-mobility-service-using-sccm/sccm-deploy-select-purpose.png)
8. Especifique una programación en **Especifique la programación de esta implementación**. Más información sobre cómo [programar los paquetes](https://technet.microsoft.com/library/gg682178.aspx)
9. Configure las propiedades en la página **Puntos de distribución** según las necesidades del centro de datos y complete el asistente.

> [!TIP]
> Para evitar los reinicios innecesarios, programe la instalación del paquete durante la ventana de mantenimiento mensual o la ventana de actualizaciones de software.

Puede supervisar el progreso de la implementación con la consola de SCCM; para ello, vaya a **Supervisión** > **Implementaciones** > *[el nombre del paquete]*
  ![monitor-sccm](./media/site-recovery-install-mobility-service-using-sccm/report.PNG).

## <a name="deploy-mobility-service-on-computers-running-linux-operating-systems"></a>Implementación del Servicio de movilidad en equipos que ejecutan sistemas operativos de Linux
> [!NOTE]
> En este artículo se da por hecho lo siguiente:
> 1. La dirección IP el servidor de configuración es 192.168.3.121
> 2. El recurso compartido de archivos de red seguro es \\\ContosoSecureFS\MobilityServiceInstallers

### <a name="step-1-prepare-for-deployment"></a>Paso 1: Preparación de la implementación
1. Cree una carpeta en el recurso compartido de red y asígnele el nombre **MobSvcLinux**.
2. Inicie sesión en el servidor de configuración y abra un símbolo del sistema con privilegios de administrador.
3. Ejecute los comandos siguientes para generar un archivo de frase de contraseña.

    `cd %ProgramData%\ASR\home\svsystems\bin`

    `genpassphrase.exe -v > MobSvc.passphrase`
6. Copie el archivo MobSvc.passphrase en la carpeta MobSvcWindows del recurso compartido de red.
5. A continuación, ejecute el comando para ir al repositorio del instalador en el servidor de configuración.

  `cd %ProgramData%\ASR\home\svsystems\puhsinstallsvc\repository`
6. Copie los archivos siguientes en la carpeta **MobSvcLinux** del recurso compartido de red.
  * Microsoft-ASR\_UA\_*versión*\_OEL-64\_GA\_*fecha*\_Release.tar.gz
  * Microsoft-ASR\_UA\_*versión*\_RHEL6-64\_GA\_*fecha*\_Release.tar.gz
  * Microsoft-ASR\_UA\_*versión*\_RHEL7-64\_GA\_*fecha*\_Release.tar.gz
  * Microsoft-ASR\_UA\_*versión*\_SLES11-SP3-64\_GA\_*fecha*\_Release.tar.gz

7. Copie el código que aparece a continuación y guárdelo como **install_linux.sh** en la carpeta **MobSvcLinux**.
> [!NOTE]
> Recuerde reemplazar los marcadores de posición [CSIP] del script que aparece a continuación por los valores reales de la dirección IP del servidor de configuración.

  [!INCLUDE [site-recovery-sccm-linux-script](../../includes/site-recovery-sccm-linux-script.md)]

### <a name="step-2-create-a-package"></a>Paso 2: Creación de un paquete

1. Inicie sesión en la consola de System Center Configuration Manager.
2. Vaya a **Biblioteca de software** > **Administración de aplicaciones** > **Paquetes**.
3. Haga clic con el botón derecho en **Paquetes** y seleccione **Crear paquete**.
4. Escriba los valores para nombre, descripción, fabricante, lenguaje y versión.
5. Active la casilla **Este paquete contiene archivos de origen**.
6. Haga clic en el botón **Examinar** y seleccione el recurso compartido de red donde está almacenado el instalador (\\\ContosoSecureFS\MobilityServiceInstaller\MobSvcLinux).

  ![create-sccm-package](./media/site-recovery-install-mobility-service-using-sccm/create_sccm_package-linux.png)

7. En la página **Elija el tipo de programa que desea crear**, seleccione **Programa estándar** y haga clic en **Siguiente**.

  ![create-sccm-package](./media/site-recovery-install-mobility-service-using-sccm/sccm-standard-program.png)
8. En la página **Especifique la información sobre este programa estándar**, proporcione las siguientes entradas y haga clic en **Siguiente**. (Las demás entradas pueden quedar con sus valores predeterminados).

  ![sccm-package-properties](./media/site-recovery-install-mobility-service-using-sccm/sccm-program-properties-linux.png)   
| **Nombre de parámetro** | **Valor** |
|--|--|
| Nombre | Instalación del Servicio de movilidad de Microsoft Azure (Linux) |
| Línea de comandos | ./install_linux.sh |
| El programa se puede ejecutar | Tanto si un usuario inició sesión como si no |

9. En la página siguiente, seleccione **Este programa puede ejecutarse en cualquier plataforma**
  ![sccm-package-properties-page2](./media/site-recovery-install-mobility-service-using-sccm/sccm-program-properties-page2-linux.png).   

10. Haga clic en **Siguiente** dos veces para completar el asistente.
> [!NOTE]
> El script es compatible con ambas instalaciones de los agentes del Servicio de movilidad y la actualización de agentes ya instalados.

### <a name="step-3-deploy-the-package"></a>Paso 3: Implementación del paquete
1. En la consola de SCCM, haga clic con el botón derecho en el paquete y seleccione **Distribuir contenido**
  ![distribute-sccm-package](./media/site-recovery-install-mobility-service-using-sccm/sccm_distribute.png).
2. Seleccione los **[puntos de distribución](https://technet.microsoft.com/library/gg712321.aspx#BKMK_PlanForDistributionPoints)** en los que se deben copiar los paquetes.
3. Una vez que complete el asistente, el paquete comenzará a replicarse en los puntos de distribución especificados.
4. Cuando la distribución de paquete finalice, haga clic con el botón derecho en el paquete y seleccione **Implementar**
  ![deploy-sccm-package](./media/site-recovery-install-mobility-service-using-sccm/sccm_deploy.png).
5. Seleccione la recopilación de dispositivos de Linux Server que creó en la sección de requisitos previos como la recopilación de destino para la implementación.

  ![sccm-select-target-collection](./media/site-recovery-install-mobility-service-using-sccm/sccm-select-target-collection-linux.png)
6. En la página **Especifique el destino del contenido**, seleccione los **puntos de distribución**.
7. En la página **Especifique la configuración para controlar cómo se implementa este software**, asegúrese de que el propósito está seleccionado como requerido.

  ![sccm-deploy-select-purpose](./media/site-recovery-install-mobility-service-using-sccm/sccm-deploy-select-purpose.png)
8. Especifique una programación en **Especifique la programación de esta implementación**. Más información sobre cómo [programar los paquetes](https://technet.microsoft.com/library/gg682178.aspx)
9. Configure las propiedades en la página **Puntos de distribución** según las necesidades del centro de datos y complete el asistente.

El Servicio de movilidad se instala en la recopilación de dispositivos de Linux Server según la programación que configuró.

## <a name="other-methods-to-install-mobility-services"></a>Otros métodos para instalar los servicios de movilidad
Más información sobre otras formas de instalar los servicios de movilidad.
* [Instalación manual mediante la GUI](http://aka.ms/mobsvcmanualinstall)
* [Instalación manual mediante la línea de comandos](http://aka.ms/mobsvcmanualinstallcli)
* [Instalación de inserción mediante el servidor de configuración](http://aka.ms/pushinstall)
* [Instalación automatizada mediante Azure Automation y la configuración de estado deseado](http://aka.ms/mobsvcdscinstall)

## <a name="next-steps"></a>Pasos siguientes
Ahora está listo para [habilitar la protección](https://docs.microsoft.com/en-us/azure/site-recovery/site-recovery-vmware-to-azure#step-6-replicate-applications) de las máquinas virtuales.

