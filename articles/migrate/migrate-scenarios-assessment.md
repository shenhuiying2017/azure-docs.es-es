---
title: Evaluación de cargas de trabajo locales para la migración a Azure con DMA y Azure Migrate | Microsoft Docs
description: Aprenda a preparar Azure para la migración de máquinas locales mediante Data Migration Assistant (DMA) y el servicio Azure Migrate.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: tutorial
ms.date: 04/16/2018
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 33e31c47a6125ac363410a9a78e9c9310c74d51e
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2018
---
# <a name="scenario-1-assess-on-premises-workloads-for-migration-to-azure"></a>Escenario 1: Evaluación de las cargas de trabajo locales para la migración a Azure

Al plantearse la migración a Azure, la empresa Contoso desea realizar una evaluación técnica y financiera para averiguar si sus cargas de trabajo locales son adecuadas para la migración a la nube. En concreto, desea evaluar la compatibilidad de las máquinas y bases de datos para la migración y calcular la capacidad y los costos de ejecutar sus recursos en Azure.

Para dar los primeros pasos y conocer mejor las tecnologías implicadas, evalúan y migran una pequeña aplicación de viajes local. Es una aplicación de dos niveles en la que una aplicación web se ejecutan en una máquina virtual y una base de datos de SQL Server en la segunda máquina virtual. La aplicación se implementa en VMware, y el entorno lo controla vCenter Server. La evaluación se realiza mediante Data Migration Assistant (DMA) y el servicio Azure Migrate.

**Technology** | **Descripción** | **Costee
--- | --- | ---
[DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=ssdt-18vs2017) | DMA evalúa y detecta problemas de compatibilidad que pueden afectar a la funcionalidad de la base de datos en Azure. Además, evalúa y presenta la paridad entre el origen y el destino de SQL Server, y ofrece recomendaciones para mejorar el rendimiento y la confiabilidad en su entorno de destino. | Es una herramienta que se puede descargar de forma gratuita. 
[Azure Migrate](https://docs.microsoft.com/azure/migrate/migrate-overview) | Este servicio le ayuda a evaluar equipos locales para su migración a Azure. Evalúa la idoneidad de la migración de los equipos y proporciona cálculos de tamaño y costo de la ejecución en Azure. En la actualidad, el servicio Azure Migrate puede evaluar las máquinas virtuales de VMware locales para la migración a Azure. | Actualmente (abril de 2018) el uso de este servicio no tiene costo alguno.
[Mapa de servicio](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-service-map) | Azure Migrate usa Service Map para mostrar las dependencias entre los equipos que se desea migrar. |  Service Map forma parte de Azure Log Analytics. Actualmente se puede utilizar durante 180 días sin ningún costo. 

En este escenario, descargaremos y ejecutaremos DMA para evaluar la base de datos local de SQL Server de nuestra aplicación de viajes. Usaremos Azure Migrate con la asignación de dependencias para evaluar las máquinas virtuales de la aplicación antes de migrarlas a Azure.

> [!NOTE]
> Para este escenario el destino de la evaluación de la base de datos será "SQL Server en una máquina virtual de Azure". Sin embargo, en el siguiente artículo, ejecutaremos la migración a una Instancia administrada de Azure SQL Database. Usamos este enfoque porque DMA no admite actualmente la evaluación a un destino de una instancia administrada de SQL Database.

## <a name="architecture"></a>Architecture

En este escenario, vamos a configurar 

 ![Arquitectura de evaluación de migraciones](./media/migrate-scenarios-assessment/migration-assessment-architecture.png)

En este escenario:
- Contoso tiene un centro de datos local (**contoso-datacenter**), con un controlador de dominio local (**contosodc1**).
- La aplicación de viajes interna se organiza en niveles en dos máquinas virtuales, **WEBVM** y **SQLVM**, y se encuentra en el host de VMware ESXi **contosohost1.contoso.com**.
- El entorno de VMware lo administra vCenter Server (**vcenter.contoso.com**), que se ejecuta en una máquina virtual.




## <a name="prerequisites"></a>requisitos previos

Para implementar este escenario, necesitará lo siguiente:

- Una instancia local de vCenter Server que ejecuta las versiones 5.5, 6.0 ó 6.5.
- Una cuenta de solo lectura de vCenter Server, o permisos para crearla. 
- Permisos para crear una máquina virtual en vCenter Server mediante una plantilla .OVA.
- Al menos un host ESXi en el que se ejecute la versión 5.0, o cualquier versión superior.
- Al menos dos máquinas virtuales VMware locales, y en una de ellas debe ejecutarse una base de datos de SQL Server.
- Debe tener permisos para instalar agentes de Azure Migrate en todas las máquinas virtuales.
- Las máquinas virtuales deben tener conectividad directa a Internet.
        - Puede restringir el acceso a Internet a las [direcciones URL requeridas](https://docs.microsoft.com/azure/migrate/concepts-collector#collector-pre-requisites).
        - Si tiene equipos sin conectividad a Internet, debe descargar la [puerta de enlace de OMS](../log-analytics/log-analytics-oms-gateway.md) e instalarla en ellos.
- El FQDN de la máquina virtual que ejecuta la instancia de SQL Server, para la evaluación de la base de datos.
- La instancia de Firewall de Windows que se ejecuta en la máquina virtual de SQL Server debe permitir conexiones externas en el puerto TCP 1433 (valor predeterminado) para que DMA pueda conectarse.


## <a name="scenario-overview"></a>Información general de escenario

Esto es lo que vamos a hacer:


> [!div class="checklist"]
> * **Paso 1: Preparar Azure**. Solo se necesita una suscripción de Azure.
> * **Paso 2: Descargar e instalar DMA**: preparación de DMA para la evaluación de la base de datos de SQL Server local.
> * **Paso 3: Evaluar la base de datos**: ejecución y análisis de la evaluación de la base de datos.
> * **Paso 4: Preparar la evaluación de la máquina virtual con Azure Migrate**: configuración de cuentas locales y cambio de la configuración de VMware.
> * **Paso 5: Detectar máquinas virtuales locales**: creación de una máquina virtual del recopilador de Azure Migrate. Después, ejecute el recopilador para detectar máquinas virtuales para su evaluación.
> * **Paso 6: Preparar el análisis de dependencias**: instalación de agentes de Azure Migrate en las máquinas virtuales, con el fin de que podamos ver la asignación de dependencias entre las máquinas virtuales.
> * **Paso 7: Evaluar las máquinas virtuales**: comprobación de las dependencias, agrupación de las máquinas virtuales y ejecución de la evaluación. Una vez que la evaluación esté lista, analícela para preparar la migración.


## <a name="step-1-prepare-azure"></a>Paso 1: Preparar Azure

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/pricing/free-trial/).

- Si crea una cuenta gratuita, será el administrador de su suscripción y podrá realizar todas las acciones.
- Si usa una suscripción existente y no es el administrador, tendrá que solicitar al administrador que le asigne permisos de propietario o colaborador en la suscripción o en el grupo de recursos que use en este escenario.


## <a name="step-2-download-and-install-the-dma"></a>Paso 2: Descargar e instalar DMA

1. Descargue DMA del [Centro de descarga de Microsoft](https://www.microsoft.com/download/details.aspx?id=53595).
    - DMA se puede instalar en cualquier equipo que pueda conectarse a la instancia de SQL. No es preciso ejecutarlo en el equipo de SQL Server.
    - No debe ejecutarlo en el equipo host de SQL Server.
2. Para iniciar la instalación, haga doble clic en el archivo de instalación que ha descargado (DownloadMigrationAssistant.msi).
3. En la página **Finish** (Finalizar), asegúrese de que está seleccionado **Launch Microsoft Data Migration Assistant** (Iniciar Microsoft Data Migration Assistant) y haga clic en **Finish** (Finalizar).

## <a name="step-3-run-and-analyze-the-database-assessment"></a>Paso 3: Ejecutar y analizar la evaluación de la base de datos

Ejecute una evaluación para analizar la instancia de SQL Server de origen con un destino especificado.

1. En **New** (Nuevo), seleccione **Assessment** (Evaluación) y asigne un nombre de proyecto a la evaluación.
2. En **Source server type** (Tipo de servidor de origen), seleccione **SQL Server**. En **Target server type** (Tipo de servidor de destino), seleccione **SQL Server on Azure Virtual Machines** (SQL Server en Azure Virtual Machines).

    ![Seleccionar origen](./media/migrate-scenarios-assessment/dma-assessment-1.png)

    > [!NOTE]
      Actualmente, DMA no admite la migración para migrar a una instancia administrada de SQL Database. Como alternativa, vamos a usar SQL Server en una máquina virtual de Azure como objetivo supuesto para la evaluación.

1.  En **Select Target Version** (Seleccionar versión de destino), especifique la versión de destino de SQL Server que desea ejecutar en Azure y lo que desea detectar en la evaluación:
    - **Compatibility Issues** (Problemas de compatibilidad) indica los cambios que pueden interrumpir la migración o que requieren un ajuste menor antes de la migración. También indica las características que se usan actualmente y que han quedado en desuso. Los problemas se organizan por nivel de compatibilidad. 
    - **New features' recommendation** (Recomendación de nuevas características) le permite conocer las nuevas características de la plataforma de SQL Server de destino que se puede usar para la base de datos después de la migración. Se organizan por rendimiento, seguridad y almacenamiento. 

    ![Seleccionar destino](./media/migrate-scenarios-assessment/dma-assessment-2.png)

2. En **Connect to a server** (Conexión a un servidor), especifique el nombre del equipo que ejecuta la instancia de SQL Server, el tipo de autenticación y los detalles de la conexión. Haga clic en **Conectar**.

    ![Seleccionar destino](./media/migrate-scenarios-assessment/dma-assessment-3.png)
    
3. En **Add source** (Agregar origen), seleccione la base de datos que desea evaluar y haga clic en **Add** (Agregar).
4. Se crea una evaluación con el nombre especificado.

    ![Crear evaluación](./media/migrate-scenarios-assessment/dma-assessment-4.png)

5.  Haga clic en **Next** (Siguiente) para iniciar la evaluación.
6. En **Review Results** (Examinar resultados), verá los resultados de las pruebas de evaluación que ha habilitado.


### <a name="analyze-the-database-assessment"></a>Análisis de la evaluación de la base de datos

Los resultados se muestran en DMA en cuanto están disponibles. 

1. En el informe **Compatibility Issues** (Problemas de compatibilidad), compruebe si la base de datos tiene problemas en los distintos niveles de compatibilidad y, si es así, cómo corregirlos. Los niveles de compatibilidad se asignan a las versiones de SQL Server como sigue:
    - 100: SQL Server 2008/Azure SQL Database
    - 110: SQL Server 2012/Azure SQL Database
    - 120: SQL Server 2014/Azure SQL Database
    - 130: SQL Server 2016/Azure SQL Database
    - 140: SQL Server 2017/Azure SQL Database

    ![Problemas de compatibilidad](./media/migrate-scenarios-assessment/dma-assessment-5.png)

2. En el informe **Feature recommendations** (Características recomendadas), vea las características de rendimiento, seguridad y almacenamiento que la evaluación recomienda que debe configurar después de la migración. Se recomiendan varias características, entre las que se incluyen OLTP en memoria y Almacén de columnas, Stretch Database, Always Encrypted, Enmascaramiento dinámico de datos y Cifrado de datos transparente.

    ![Recomendaciones de características](./media/migrate-scenarios-assessment/dma-assessment-6.png)

3. Si corrige algún problema, haga clic en **Restart Assessment** (Reiniciar evaluación) para volver a ejecutarlo. 
4. Haga clic en **Export report** (Exportar informe) para obtener el informe de evaluación en formato JSON o CSV.

Si ejecuta una evaluación a mayor escala:

- Para ejecutar varias evaluaciones simultáneamente y ver su estado, abra la página **All Assessments** (Todas las evaluaciones).
- Puede [consolidar las evaluaciones en una base de datos de SQL Server](https://docs.microsoft.com/sql/dma/dma-consolidatereports?view=ssdt-18vs2017#import-assessment-results-into-a-sql-server-database).
- Puede [consolidar las evaluaciones en un informe de PowerBI](https://docs.microsoft.com/sql/dma/dma-powerbiassesreport?view=ssdt-18vs2017).


## <a name="step-4-prepare-for-vm-assessment-with-azure-migrate"></a>Paso 4: Preparar la evaluación de la máquina virtual con Azure Migrate

Cree una cuenta de VMware que Azure Migrate usará para detectar automáticamente las máquinas virtuales que se van a evaluar, compruebe que dispone de permisos para crear una máquina virtual, anote los puertos que deben estar abiertos y establezca el nivel de configuración de las estadísticas.

### <a name="set-up-a-vmware-account"></a>Configuración de una cuenta de VMware

 Necesita una cuenta de solo lectura en vCenter. Si no la tiene, cree una cuenta de VMware con las siguientes propiedades:

- Tipo de usuario: al menos un usuario de solo lectura.
- Permisos: Data Center -> Propagate to Child Object, role=Read-only (Objeto de Centro de datos -> Propagar a objeto secundario, rol = Solo lectura).
- Detalles: el usuario se asigna en el nivel de centro de datos y tiene acceso a todos los objetos de este.
- Para restringir el acceso, asigne el rol **Sin acceso** con **Propagate to child object** (Propagar a objeto secundario) a los objetos secundarios (hosts de vSphere, almacenes de datos, máquinas virtuales y redes).

### <a name="verify-permissions-to-create-a-vm"></a>Comprobación de los permisos para crear una máquina virtual

Para comprobar que tiene los permisos necesarios para crear una máquina virtual, importe un archivo en formato OVA. [Más información](https://kb.vmware.com/s/article/1023189?other.KM_Utility.getArticleLanguage=1&r=2&other.KM_Utility.getArticleData=1&other.KM_Utility.getArticle=1&ui-comm-runtime-components-aura-components-siteforce-qb.Quarterback.validateRoute=1&other.KM_Utility.getGUser=1).

### <a name="verify-ports"></a>Comprobación de puertos

En este escenario, vamos a configurar la asignación de dependencias. Esta característica requiere que haya un agente instalado en las máquinas virtuales que se van a evaluar. Es preciso que dicho agente se pueda conectar a Azure desde el puerto TCP 443 de todas las máquinas virtuales. [Más información](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid) acerca de los requisitos de la conexión.


### <a name="set-statistics-settings"></a>Establecimiento de la configuración de las estadísticas

Las estadísticas de vCenter Server se debe establecer en el nivel 3 antes de iniciar la implementación. Observe lo siguiente:
- Después de establecer el nivel, debe esperar al menos un día antes de ejecutar la evaluación. Si no lo hace así, es posible que no funcione según lo previsto.
- Si el nivel es mayor que 3, la evaluación funcionará, pero:
    - No se recopilarán los datos de rendimiento de los discos ni de la red.
    - Para el almacenamiento, Azure Migrate recomienda un disco estándar en Azure con el mismo tamaño que el disco local.
    - Para redes, para cada adaptador de red local, se recomendará un adaptador de red en Azure.
    - Para el proceso, Azure Migrate examinará los núcleos y el tamaño de la memoria de la máquina virtual y recomendará una máquina virtual de Azure con la misma configuración. Si hay varios tamaños de máquina virtual de Azure adecuados, se recomienda el que tiene el costo más bajo.
   
    
[Más información](https://docs.microsoft.com/azure/migrate/concepts-assessment-calculation#sizing) acerca del ajuste de tamaño con el nivel 3.

Establezca el nivel de la manera siguiente:

1. En el vSphere Web Client, abra la instancia de vCenter Server.
2. Seleccione la pestaña **Manage** (Administrar) y en **Settings** (Configuración), haga clic en **General**.
3. Haga clic en **Edit** (Editar) y en **Statistics** (Estadísticas), establezca la configuración de nivel de la estadística en **Level 3** (Nivel 3).

    ![Nivel de estadísticas de vCenter](./media/migrate-scenarios-assessment/vcenter-statistics-level.png)



## <a name="step-5-discover-vms"></a>Paso 5: Detectar máquinas virtuales

Cree un proyecto de Azure Migrate, descargue la máquina virtual del recopilador y configúrela. Luego, ejecute el recopilador para detectar las máquinas virtuales.

### <a name="create-a-project"></a>Crear un proyecto

1. Inicie sesión en [Azure Portal](https://portal.azure.com) y haga clic en **Crear un recurso**.
2. Busque **Azure Migrate**. Seleccione **Azure Migrate** en los resultados de la búsqueda y haga clic en **Crear**.
3. Especifique un nombre de proyecto y la suscripción de Azure.
4. Cree un nuevo grupo de recursos.
5. Especifique la ubicación del proyecto y haga clic en **Crear**.

    - Los proyectos de Azure Migrate solo se pueden crear en la región Centro occidental de EE. UU. o Este de EE. UU.
    - Puede planear una migración a cualquier ubicación de destino.
    - La ubicación del proyecto solo se utiliza para almacenar los metadatos que se recopilan de las máquinas virtuales locales.

    ![Azure Migrate](./media/migrate-scenarios-assessment/project-1.png)


    

### <a name="download-the-collector-appliance"></a>Descarga del dispositivo de recopilador

Azure Migrate crea una VM local conocida como el dispositivo de recopilador. Esta VM detecta VM de VMware locales y envía los metadatos sobre ellas para al servicio Azure Migrate. Para configurar el dispositivo de recopilador, descargue un archivo .OVA e impórtelo en el servidor vCenter local para crear la VM.

1. En el proyecto de Azure Migrate, haga clic en **Introducción** > **Detectar y evaluar** > **Detectar máquinas**.
2. En **Detectar máquinas**, haga clic en **Descargar**, para descargar el archivo .OVA.
3. En **Copiar las credenciales del proyecto**, copie la clave y el identificador del proyecto. Los necesitará cuando configure el recopilador.

    ![Descarga del archivo .ova](./media/migrate-scenarios-assessment/download-ova.png) 

### <a name="verify-the-collector-appliance"></a>Comprobación del dispositivo de recopilador

Compruebe que el archivo .OVA es seguro, antes de implementarlo.

1. En la máquina en la que descargó el archivo, abra una ventana de comandos de administrador.
2. Ejecute el siguiente comando para generar el código hash para el archivo OVA:
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Ejemplo de uso: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```
3. El código hash generado debe coincidir con esta configuración (versión 1.0.9.7)
    
    **Algoritmo** | **Valor del código hash**
    --- | ---
    MD5 | d5b6a03701203ff556fa78694d6d7c35
    SHA1 | f039feaa10dccd811c3d22d9a59fb83d0b01151e
    SHA256 | e5e997c003e29036f62bf3fdce96acd4a271799211a84b34b35dfd290e9bea9c
    

### <a name="create-the-collector-appliance"></a>Creación del dispositivo de recopilador

Importe el archivo descargado en el servidor vCenter Server.

1. En la consola de cliente de vSphere, haga clic en **File** (Archivo) > **Deploy OVF Template** (Implementar plantilla de OVF).

    ![Implementación de OVF](./media/migrate-scenarios-assessment/vcenter-wizard.png) 

2. En el Deploy OVF Template Wizard (Asistente para implementar la plantilla de OVF) > **Source** (Origen), especifique la ubicación del archivo OVA y haga clic en **Next** (Siguiente).
3. En **OVF Template Details** (Detalles de plantilla de OVF), haga clic en **Next** (Siguiente). En **End User License Agreement** (Contrato de licencia para el usuario final), haga clic en **Accept** (Aceptar) para aceptar el contrato y después en **Next** (Siguiente).
4. En **Name and Location** (Nombre y ubicación), especifique un nombre descriptivo para la máquina virtual del recopilador y la ubicación del inventario en el que se hospedará la máquina virtual, y haga clic en **Next** (Siguiente). Especifique el host o clúster donde se ejecutará el dispositivo recopilador.
5. En **Storage** (Almacenamiento), especifique el lugar en que desea almacenar los archivos del dispositivo y haga clic en **Next** (Siguiente).
6. En **Disk Format** (Formato de disco), especifique cómo desea aprovisionar el almacenamiento.
7. En **Network Mapping** (Asignación de red), especifique la red a la que se conectará la VM de recopilador. La red necesita conectividad a Internet, para enviar metadatos a Azure. 
8. En **Ready to Complete** (Listo para completar), revise la configuración, seleccione **Power on after deployment** (Encender después de la implementación) y, después, haga clic en **Finish** (Finalizar).

Una vez creado el dispositivo, se genera un mensaje que confirma la finalización correcta.

### <a name="run-the-collector-to-discover-vms"></a>Ejecución del recopilador para detectar VM

Antes de empezar, tenga en cuenta que actualmente el recopilador solo admite "Inglés (Estados Unidos)" como idioma del sistema operativo e idioma de la interfaz del recopilador.

1. En la consola del cliente de vSphere, haga clic con el botón derecho en VM > **Open Console** (Abrir consola).
2. Proporcione el idioma, la zona horaria y las preferencias de contraseña para el dispositivo.
3. En el escritorio, haga clic en el acceso directo **Run collector** (Ejecutar recopilador).

    ![Acceso directo al recopilador](./media/migrate-scenarios-assessment/collector-shortcut.png) 
    
4. En Azure Migrate Collector, abra **Set Up Prerequisites** (Configurar requisitos previos).
    - Acepte los términos de licencia y lea la información de terceros.
    - El recopilador comprueba que la máquina virtual tiene acceso a internet, que la hora está sincronizada y que se ejecuta el servicio de recopilador (se instala de forma predeterminada en la máquina virtual). También comprueba que VMWare PowerCLI esté instalado. 
    
    > [!NOTE]
    > Suponemos que la máquina virtual tiene acceso directo a Internet, sin un proxy.

    ![Verificar los requisitos previos](./media/migrate-scenarios-assessment/collector-verify-prereqs.png)
    

5. En **Specify vCenter Server details** (Especificar detalles de vCenter Server), haga lo siguiente:
    - Especifique el nombre (FQDN) o la dirección IP del servidor vCenter.
    - En **Username** (Nombre de usuario) y **Password** (Contraseña), especifique las credenciales de la cuenta de solo lectura que utilizará el recopilador para detectar las máquinas virtuales en vCenter Server.
    - En **Seleccionar ámbito**, especifique un ámbito para la detección de máquinas virtuales. El recopilador solo puede detectar VM dentro del ámbito especificado. El ámbito se puede establecer en una carpeta, centro de datos o clúster específico. No debe contener más de 1500 máquinas virtuales. 

    ![Conectarse a vCenter](./media/migrate-scenarios-assessment/collector-connect-vcenter.png)

6. En **Specify migration project** (Especificar proyecto de migración), especifique la clave y el identificador de proyecto de Azure Migrate que copió del portal. Si no los copió, abra Azure Portal desde la VM de recopilador. En la página **Introducción** del proyecto, haga clic en **Detectar máquinas** y copie los valores.  

    ![Conexión a Azure](./media/migrate-scenarios-assessment/collector-connect-azure.png)

7. En **View collection progress** (Ver progreso de recopilación), supervise la detección y compruebe que los metadatos recopilados de las máquinas virtuales se encuentran dentro del ámbito. El recopilador proporciona un tiempo de detección aproximado.

    ![Colección en curso](./media/migrate-scenarios-assessment/collector-collection-process.png)
   


### <a name="verify-vms-in-the-portal"></a>Comprobación de VM en el portal

Una vez que se completa la colección, compruebe que las máquinas virtuales aparecen en el portal.

1. En el proyecto de Azure Migrate, haga clic en **Manage** > **Machines** (Administrar > Máquinas).
2. Compruebe que las máquinas virtuales que desea detectar aparecen en la página.

    ![Máquinas detectadas](./media/migrate-scenarios-assessment/discovery-complete.png)

3. Tenga en cuenta que las máquinas actualmente no tienen instalados los agentes de Azure Migrate. Para poder ver las dependencias, es preciso instalarlos.
    
    ![Máquinas detectadas](./media/migrate-scenarios-assessment/machines-no-agent.png)



## <a name="step-6-prepare-for-dependency-analysis"></a>Paso 6: Preparar el análisis de dependencias

Para ver las dependencias existentes entre las máquinas virtuales que deseamos evaluar, descargamos e instalamos los agentes en las máquinas virtuales de la aplicación web: WEBVM y SQLVM.

### <a name="take-a-snapshot"></a>Realización de una instantánea

Si desea tener una copia de la máquina virtual antes de modificarla, tome una instantánea antes de instalar a los agentes.

![Instantánea de la máquina](./media/migrate-scenarios-assessment/snapshot-vm.png) 


### <a name="download-and-install-the-vm-agents"></a>Descarga e instalación de los agentes en máquinas virtuales

1.  En la página Azure Migrate project (Proyecto de Azure Migrate) > **Machines** (Máquinas), seleccione la máquina y haga clic en **Requires installation** (Requiere instalación) en la columna **Dependencies** (Dependencias).
2.  En la página **Discover Machines** (Detectar máquinas), descargue e instale Microsoft Monitoring Agent (MMA) y Dependency Agent.
3.  Copie la clave y el identificador de área de trabajo. Se necesitan al instalar el MMA.

    ![Descarga de agente](./media/migrate-scenarios-assessment/download-agents.png) 



#### <a name="install-the-mma"></a>Instalación de MMA

1. Haga doble clic en el agente descargado.
2. En la página **principal**, haga clic en **Siguiente**. En la página **Términos de licencia**, haga clic en **Acepto** para aceptar la licencia.
3. En **Destination Folder** (Carpeta de destino), mantenga la carpeta de instalación predeterminada y haga clic en **Next** (Siguiente). 
4. En **Agent Setup Options** (Opciones de instalación del agente), seleccione **Connect the agent to Azure Log Analytics** > **Next** (Conectar el agente a Azure Log Analytics > Siguiente). 

    ![Instalación de MMA](./media/migrate-scenarios-assessment/mma-install.png) 
5. En **Azure Log Analytics**, pegue la clave y el identificador del área de trabajo que copió del portal. Haga clic en **Next**.
    ![Instalación de MMA](./media/migrate-scenarios-assessment/mma-install2.png) 

6. En **Ready to Install** (Listo para instalar), instale el MMA.



#### <a name="install-the-dependency-agent"></a>Instalación del agente de dependencia

1.  Haga doble clic en la instancia de Dependency Agent descargada.
2.  En la página **License Terms** (Términos de licencia), haga clic en **I Agree to accept the license** (Acepto para aceptar la licencia).
3.  En **Installing** (Instalando), espere a que la instalación finalice. A continuación, haga clic en **Siguiente**.

    ![Dependency Agent](./media/migrate-scenarios-assessment/dependency-agent.png) 


       
## <a name="step-7-run-and-analyze-the-vm-assessment"></a>Paso 7: Ejecutar y analizar la evaluación de la máquina virtual

Compruebe las dependencias de la máquina y cree un grupo. Después, realice la evaluación.

### <a name="verify-dependencies-and-create-a-group"></a>Compruebe las dependencias y cree un grupo.

1.  En la página **Machines** (Máquinas), para las máquinas virtuales que desea analizar, haga clic en **View Dependencies** (Ver dependencias).

    ![Ver las dependencias de máquinas](./media/migrate-scenarios-assessment/view-machine-dependencies.png) 

2. Para SQLVM, el mapa de dependencias muestra los siguientes detalles:

    - Los grupos de procesos o los procesos con conexiones de red activas que se ejecutan en SQLVM, durante el período especificado (una hora de forma predeterminada)
    - Conexiones TCP de entrada (cliente) y de salida (servidor) a y desde todas las máquinas dependientes.
    - Las máquinas dependientes con los agentes de Azure Migrate se muestran como cuadros independientes
    - Las máquinas los agentes instalados muestran la información del puerto y dirección IP.
    
 3. En las máquinas con el agente instalado (WEBVM), haga clic en el cuadro de la máquina para ver más información, incluidos el FQDN, el sistema operativo y la dirección MAC. 

    ![Visualización de las dependencias del grupo](./media/migrate-scenarios-assessment/sqlvm-dependencies.png)

4. Ahora, seleccione las máquinas virtuales que desea agregar al grupo (SQLVM y WEBVM).  Utilice CTRL + clic para seleccionar varias máquinas virtuales.
5. Haga clic en **Create Group** (Crear grupo) y especifique un nombre (smarthotelapp).

> [!NOTE]
    > Para ver dependencias más pormenorizadas, puede expandir el intervalo de tiempo. Puede seleccionar una duración concreta, o las fechas de inicio y finalización. 


### <a name="run-an-assessment"></a>Ejecución de una evaluación


1. En la página **Groups**  (Grupos), abra el grupo (smarthotelapp).
2. Haga clic en **Create assessment** (Crear evaluación).

    ![Crear una evaluación](./media/migrate-scenarios-assessment/run-vm-assessment.png)

3. La evaluación aparece en la página **Manage** > **Assessments** (Administrar > Evaluaciones).


### <a name="modify-assessment-settings"></a>Modificación de la configuración de evaluaciones

Para este tutorial hemos usado la configuración de evaluación predeterminada, pero se puede personalizar como se indica a continuación:

1. En la página **Evaluaciones** del proyecto de migración, seleccione la evaluación y haga clic en **Editar propiedades**.
2. Modifique las propiedades de acuerdo a la siguiente tabla:

    **Configuración** | **Detalles** | **Valor predeterminado**
    --- | --- | ---
    **Ubicación de destino** | La ubicación de Azure a la que desea realizar la migración. | No hay valor predeterminado.
    **Redundancia de almacenamiento** | El tipo de redundancia de almacenamiento que las máquinas virtuales de Azure usarán después de la migración. | [El almacenamiento con redundancia local (LRS)](../storage/common/storage-redundancy-lrs.md) es el valor predeterminado. Azure Migrate solo admite las evaluaciones basadas en discos administrados y los discos administrados solo son compatibles con LRS, de ahí la opción de LRS. 
    **Criterio de ajuste de tamaño** | El criterio que debe utilizar Azure Migrate para ajustar el tamaño de las máquinas virtuales para Azure. Puede hacer bien un ajuste de tamaño *basado en el rendimiento* o puede ajustar el tamaño de las máquinas virtuales *como en el entorno local*, sin tener en cuenta el historial de rendimiento. | El ajuste de tamaño basado en el rendimiento es el valor predeterminado.
    **Historial de rendimiento** | La duración a tener en cuenta para evaluar el rendimiento de las máquinas virtuales. Esta propiedad solo es aplicable cuando el criterio es el *ajuste de tamaño basado en el rendimiento*. | El valor predeterminado es un día.
    **Uso de percentil** | El valor de percentil del ejemplo de rendimiento establecido para determinar el tamaño adecuado. Esta propiedad solo es aplicable cuando el criterio es el *ajuste de tamaño basado en el rendimiento*.  | Valor predeterminado es percentil 95.
    **Plan de tarifa** | Puede especificar el [plan de tarifa (Básico o Estándar)](../virtual-machines/windows/sizes-general.md) de las máquinas virtuales de Azure de destino. Por ejemplo, si va a migrar un entorno de producción, debería tener en cuenta el plan Estándar, que proporciona máquinas virtuales con una latencia baja aunque con un costo más alto. Por otro lado, si tiene un entorno de desarrollo y pruebas, quizá debería considerar el plan Básico que tiene máquinas virtuales con una latencia mayor y un costo más bajo. | De forma predeterminada se usa el plan [Estándar](../virtual-machines/windows/sizes-general.md).
    **Factor de confort** | Azure Migrate tiene en cuenta un búfer (factor de confort) durante la evaluación. Dicho búfer se aplica además de los datos de uso de la máquina en las máquinas virtuales (CPU, memoria, disco y red). El factor de confort se tiene en cuenta en problemas como el uso estacional, un historial de rendimiento corto y los posibles aumentos en el uso futuro.<br/><br/> Por ejemplo, una máquina virtual de 10 núcleos con un uso del 20 % normalmente genera una máquina virtual de 2 núcleos. Sin embargo, con un factor de confort de 2.0 x, el resultado es una máquina virtual de 4 núcleos. | La configuración predeterminada es 1.3x.
    **Oferta** | [Oferta de Azure](https://azure.microsoft.com/support/legal/offer-details/) en la que esté inscrito. | [Pago por uso](https://azure.microsoft.com/offers/ms-azr-0003p/) es el valor predeterminado.
    **Moneda** | Divisa de facturación. | El valor predeterminado es el dólar estadounidense.
    **Descuento (%)** | Cualquier descuento específico de la suscripción que reciba además de la oferta de Azure. | La configuración predeterminada es 0 %.
    **Ventaja híbrida de Azure** | Especifique si dispone de Software Assurance y tiene derecho a [Ventaja híbrida de Azure](https://azure.microsoft.com/pricing/hybrid-use-benefit/). Si se establece en Sí, los precios de Azure que no son de Windows se toman en cuenta para las máquinas virtuales de Windows. | El valor predeterminado es Yes.

3. Haga clic en **Guardar** para actualizar la configuración de la evaluación.


### <a name="analyze-the-vm-assessment"></a>Análisis de la evaluación de la máquina virtual

La valoración de Azure Migrate incluye información acerca de si las máquinas virtuales locales son compatibles con Azure, una sugerencia acerca del tamaño correcto de la máquina virtual de Azure y los costos mensuales estimados de Azure. 

![Informe de evaluación](./media/migrate-scenarios-assessment/assessment-overview.png)

#### <a name="review-confidence-rating"></a>Examen de la clasificación de confianza

![Presentación de la evaluación](./media/migrate-scenarios-assessment/assessment-display.png)

La evaluación obtiene una clasificación de confianza de estrella de 1 a 5 estrellas (siendo 1 estrella la menor confianza y 5 la mayor).
- La clasificación de confianza se asigna a una valoración que se basa en la disponibilidad de puntos de datos necesarios para calcular tal valoración.
- Esta clasificación le ayuda a calcular la confiabilidad de las recomendaciones de tamaño que proporciona Azure Migrate.
- La clasificación de confianza es útil cuando se está realizando un *ajuste del tamaño basado en el rendimiento* porque Azure Migrate puede no tener suficientes puntos de datos para realizar el ajuste de tamaño en función del uso. Para el *ajuste de tamaño como local*, la clasificación de confianza siempre es de 5 estrellas, puesto que Azure Migrate tiene todos los datos que necesita para ajustar el tamaño de la máquina virtual.
- Según el porcentaje de puntos de datos disponibles, se proporciona la clasificación de confianza para la valoración:

   **Disponibilidad de puntos de datos** | **Clasificación de confianza**
   --- | ---
   0 % - 20 % | 1 estrella
   21 % - 40 % | 2 estrellas
   41 % - 60 % | 3 estrellas
   61 % - 80 % | 4 estrellas
   81 % - 100 % | 5 estrellas

#### <a name="verify-readiness"></a>Comprobación de la preparación

![Preparación para Azure](./media/migrate-scenarios-assessment/azure-readiness.png)  

El informe de la evaluación muestra la información que se resume en la tabla. Tenga en cuenta que para mostrar el ajuste de tamaño basado en el rendimiento, Azure Migrate necesita la siguiente información. Si dicha información no se pueden recopilar, es posible que la evaluación del ajuste de tamaño no sea precisa.

- Datos de uso de la CPU y la memoria.
- IOPS de lectura o escritura, y capacidad de proceso de cada disco conectado a la máquina virtual.
- Información de entrada y salida de la red de todos y cada uno de los adaptadores de red conectados a la máquina virtual.


**Configuración** | **Indicación** | **Detalles**
--- | --- | ---
**Preparación para VM de Azure** | Indica si la máquina virtual está preparada para la migración | Los posibles estados son:</br><br/>- Preparado para Azure<br/><br/>- Preparado con condiciones <br/><br/>- No está preparada para Azure<br/><br/>- Preparación desconocida<br/><br/> Si alguna máquina virtual no está lista, mostraremos varios pasos para solucionarlo.
**Tamaño de la máquina virtual de Azure** | Para las máquinas virtuales listas, se recomienda un tamaño de máquina virtual de Azure. | El tamaño recomendado depende de las propiedades de la evaluación:<br/><br/>- Si utilizó un ajuste de tamaño basado en el rendimiento, dicho ajuste tiene en cuenta el historial de rendimiento de las máquinas virtuales.<br/><br/>- Si utilizó "como local", el ajuste de tamaño se basa en el tamaño de la máquina virtual local y no se usan datos de uso.
**Herramienta sugerida** | Dado que las máquinas ejecutan los agentes, Azure Migrate examina los procesos que se ejecutan en la máquina e identifica si es una máquina de base de datos o no.
**Información de máquina virtual** | El informe muestra la configuración de la máquina virtual local, lo que incluye información acerca del sistema operativo, del tipo de arranque, de los discos y del almacenamiento.




#### <a name="review-monthly-cost-estimates"></a>Examen de cálculos del costo mensual

Esta vista muestra el cálculo total y el costo de almacenamiento que supone ejecutar las máquinas virtuales en ejecución en Azure, además de los detalles de cada máquina. 

![Preparación para Azure](./media/migrate-scenarios-assessment/azure-costs.png) 

- Las estimaciones de los costos se calculan mediante las recomendaciones de tamaño de una máquina.
- Los costos mensuales estimados para el proceso y almacenamiento se agregan para todas las VM del grupo. 


## <a name="conclusion"></a>Conclusión

En este escenario, hemos:

> [!div class="checklist"]
> * Evaluado la base de datos local con la herramienta DMA.
> * Evaluado las máquinas virtuales locales, mediante la asignación de dependencias, con el servicio Azure Migrate.
> * Examinado las evaluaciones para asegurarnos de que los recursos locales están listos para la migración a Azure.

## <a name="next-steps"></a>Pasos siguientes

Pasemos al siguiente escenario, donde realizaremos una migración mediante lift-and-shift de las máquinas virtuales locales a Azure.



