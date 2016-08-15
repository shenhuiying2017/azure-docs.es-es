<properties
   pageTitle="Introducción al DSC de Automatización de Azure:"
   description="Explicación y ejemplos de las tareas más comunes de la configuración de estado deseado (DSC) de Automatización de Azure"
   services="automation" 
   documentationCenter="na" 
   authors="eslesar" 
   manager="dongill" 
   editor="tysonn"/>

<tags
   ms.service="automation"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="powershell"
   ms.workload="na" 
   ms.date="06/06/2016"
   ms.author="magoedte;eslesar"/>
   

# Introducción al DSC de Automatización de Azure:

En este tema se explica cómo realizar las mayoría de las tareas comunes con configuración de estado deseado (DSC) de Automatización de Azure, como crear, importar y compilar configuraciones, incorporar máquinas para administrarlas y ver informes. Para obtener información general sobre qué es DSC de Automatización de Azure es, consulte [Información general de DSC de Automatización de Azure](automation-dsc-overview.md). Para obtener documentación de DSC, consulte [Información general sobre la configuración de estado deseado de Windows PowerShell](https://msdn.microsoft.com/PowerShell/dsc/overview).

Este tema proporciona a una guía paso a paso para usar DSC de Automatización de Azure. Si busca un entorno de ejemplo que ya se haya configurado sin seguir los pasos descritos en este tema, puede usar [la siguiente plantilla de ARM](https://github.com/azureautomation/automation-packs/tree/master/102-sample-automation-setup). Esta plantilla configura un entorno de DSC de Automatización de Azure completado, incluida una máquina virtual de Azure administrada por DSC de Automatización de Azure.
 
## Requisitos previos

Para completar los ejemplos de este tema, se requiere lo siguiente:

- Una cuenta de Automatización de Azure Para obtener instrucciones sobre cómo crear una cuenta de ejecución de Automatización de Azure, consulte el artículo sobre las [cuentas de ejecución de Azure](automation-sec-configure-azure-runas-account.md).
- Una máquina virtual de Azure Resource Manager (no clásico) ejecuta Windows Server 2008 R2, o cualquier versión posterior. Para obtener instrucciones sobre la creación de una máquina virtual, consulte [Creación de la primera máquina virtual de Windows en el Portal de Azure](../virtual-machines/virtual-machines-windows-hero-tutorial.md).

## Creación de una configuración de DSC

Crearemos una [configuración DSC](https://msdn.microsoft.com/powershell/dsc/configurations) simple que garantice la presencia o ausencia de la característica de Windows **Web-Server** (IIS), en función de cómo se asignen los nodos.

1. Inicie Windows PowerShell ISE (o cualquier editor de texto).

2. Escriba el siguiente texto:

    ```powershell
    configuration TestConfig
    {
        Node WebServer
        {
            WindowsFeature IIS
            {
                Ensure               = 'Present'
                Name                 = 'Web-Server'
                IncludeAllSubFeature = $true

            }
        }

        Node NotWebServer
        {
            WindowsFeature IIS
            {
                Ensure               = 'Absent'
                Name                 = 'Web-Server'

            }
        }
        }
    ```
3. Guarde el archivo como `TestConfig.ps1`.

Esta configuración llama a un recurso de cada bloque del nodo, el recurso [WindowsFeature](https://msdn.microsoft.com/powershell/dsc/windowsfeatureresource), que garantiza la presencia o ausencia de la característica **Web-Server**.

## Importación de una configuración en Automatización de Azure

A continuación, importaremos la configuración en la cuenta de Automatización.

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com).

2. En el menú del concentrador, haga clic en **Todos los recursos** y, luego, en el nombre de la cuenta de Automatización.

3. En la hoja **Cuenta de Automatización**, haga clic en **Configuraciones DSC**.

4. En la hoja **Configuraciones DSC**, haga clic en **Agregar una configuración**.

5. En la hoja **Importar configuración**, vaya al archivo `TestConfig.ps1` del equipo.
    
    ![Captura de pantalla de la hoja **Importar configuración**](./media/automation-dsc-getting-started/AddConfig.png)
    

6. Haga clic en **Aceptar**.

## Visualización de una configuración en Automatización de Azure

Después de importar una configuración, puede verla en el Portal de Azure.

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com).

2. En el menú del concentrador, haga clic en **Todos los recursos** y, luego, en el nombre de la cuenta de Automatización.

3. En la hoja **Cuenta de Automatización**, haga clic en **Configuraciones DSC**.

4. En la hoja **Configuraciones DSC**, haga clic en **TestConfig** (es el nombre de la configuración que importó en el procedimiento anterior).

5. En la hoja **TestConfig Configuration** (Configuración de TestConfig), haga clic en **Ver el origen de configuración**.

    ![Captura de pantalla de la hoja TestConfig configuration (Configuración de TestConfig)](./media/automation-dsc-getting-started/ViewConfigSource.png)
    
    Se abre una hoja de **origen de Configuración de TestConfig**, que muestra el código de PowerShell de la configuración.
    
## Compilación de una configuración en Automatización de Azure

Para poder aplicar un estado deseado a un nodo, se debe compilar una configuración de DSC que defina dicho estado en una o varias configuraciones de nodo (documento de MOF) y se debe colocar en el servidor de extracción de DSC de Automatización. Para obtener una descripción más detallada de la compilación de configuraciones en DSC de Automatización de Azure, consulte [Compilación de configuraciones en DSC de Automatización de Azure](automation-dsc-compile.md). Para más información sobre la compilación de configuraciones, consulte [Configuraciones DSC](https://msdn.microsoft.com/PowerShell/DSC/configurations).

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com).

2. En el menú del concentrador, haga clic en **Todos los recursos** y, luego, en el nombre de la cuenta de Automatización.

3. En la hoja **Cuenta de Automatización**, haga clic en **Configuraciones DSC**.

4. En la hoja **Configuraciones DSC**, haga clic en **TestConfig** (el nombre de la configuración importada previamente).

5. En la hoja **TestConfig Configuration** (Configuración de TestConfig), haga clic en **Compilar** y luego en **Sí**. Así se inicia un trabajo de compilación.
    
    ![Captura de pantalla de la hoja TestConfig configuration (Configuración de TestConfig) en la que se resalta el botón Compilar](./media/automation-dsc-getting-started/CompileConfig.png)
    
> [AZURE.NOTE] Cuando se compila una configuración en Automatización de Azure, esta implementa todos los archivos MOF de configuración de nodo creados en el servidor de extracción.

## Visualización de un trabajo de compilación

Después de iniciar una compilación, puede verla en el icono **Trabajos de compilación** de la hoja **Configuración**. El icono **Trabajos de compilación** muestra los trabajos con errores, completados y en ejecución. Cuando abra la hoja de un trabajo de compilación, mostrará información acerca del trabajo, incluidos los errores o advertencias encontrados, los parámetros de entrada usados en la configuración y los registros de compilación.

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com).

2. En el menú del concentrador, haga clic en **Todos los recursos** y, luego, en el nombre de la cuenta de Automatización.

3. En la hoja **Cuenta de Automatización**, haga clic en **Configuraciones DSC**.

4. En la hoja **Configuraciones DSC**, haga clic en **TestConfig** (el nombre de la configuración importada previamente).

5. En el icono **Trabajos de compilación** de la hoja **TestConfig Configuration** (Configuración de TestConfig), haga clic en cualquiera de los trabajos enumerados. Se abre la hoja de un **trabajo de compilación**, etiquetada con la fecha en que se inició el trabajo de compilación.

    ![Captura de pantalla de la hoja Trabajo de compilación](./media/automation-dsc-getting-started/CompilationJob.png)
  
6. Haga clic en cualquiera de los iconos de la hoja **Trabajo de compilación** para ver detalles adicionales acerca del trabajo.

## Visualización de configuraciones de nodo

La finalización correcta de un trabajo de compilación crea una o varias configuraciones de nodo nuevas. Una configuración de nodo es un documento MOF que se implementa en el servidor de extracción y está listo ser extraído y que lo apliquen uno o varios nodos. Las configuraciones de nodo de su cuenta de Automatización las puede ver en la hoja **Configuraciones del nodo de DSC**. Los nombres de las configuraciones de nodo tienen el formato *ConfigurationName*.*NodeName*.

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com).

2. En el menú del concentrador, haga clic en **Todos los recursos** y, luego, en el nombre de la cuenta de Automatización.

3. En la hoja **Cuenta de Automatización**, haga clic en **Configuraciones del nodo de DSC**.

    ![Captura de pantalla de la hoja Configuraciones del nodo de DSC](./media/automation-dsc-getting-started/NodeConfigs.png)
    
## Incorporación de una máquina virtual de Azure para la administración con DSC de Automatización de Azure

DSC de automatización de Azure se puede usar para administrar máquinas virtuales de Azure (tanto el enfoque clásico como el de Resource Manager), máquinas virtuales locales, máquinas de Linux, máquinas virtuales de AWS y máquinas físicas locales. En este tema, se explicará cómo incorporar solo máquinas virtuales de Azure Resource Manager. Para más información sobre la incorporación de otros tipos de máquinas, consulte [Incorporación de máquinas para administrarlas con DSC de Automatización de Azure](automation-dsc-onboarding.md).

### Para incorporar una máquina virtual de Azure Resource Manager para que DSC de Automatización de Azure la administre

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com).

2. En el menú del concentrador, haga clic en **Todos los recursos** y, luego, en el nombre de la cuenta de Automatización.

3. En la hoja **Cuenta de Automatización**, haga clic en **Nodos DSC**.

4. En la hoja **Nodos DSC**, haga clic en **Agregar máquina virtual de Azure**.

    ![Captura de pantalla de la hoja Nodos DSC en la que se resalta el botón Agregar máquina virtual de Azure](./media/automation-dsc-getting-started/OnboardVM.png)

5. En la hoja **Agregar máquinas virtuales de Azure**, haga clic en **Seleccionar máquinas virtuales para incorporar**.

6. En la hoja **Seleccionar máquinas virtuales**, seleccione la máquina virtual que desea incorporar y haga clic en **Aceptar**.

    >[AZURE.IMPORTANT] Debe ser una máquina virtual de Azure Resource Manager que ejecute Windows Server 2008 R2, o cualquier versión posterior.
    
7. En la hoja **Agregar máquinas virtuales de Azure**, haga clic en **Configurar datos de registro**.

8. En la hoja **Registro**, escriba el nombre de la configuración del nodo que quiere aplicar a la máquina virtual en el cuadro **Nombre de la configuración del nodo**. Debe coincidir exactamente con el nombre de una configuración de nodo de la cuenta de Automatización. Especificar un nombre en este momento es opcional. Puede cambiar la configuración de nodo asignada después de la incorporación del nodo. Marque **Reiniciar el nodo si es necesario** y haga clic en **Aceptar**.
    
    ![Captura de pantalla de la hoja Registro](./media/automation-dsc-getting-started/RegisterVM.png)
    
    La configuración de nodo que especificó se aplicará a la máquina virtual según los intervalos especificados por el valor de **Frecuencia del modo de configuración** y la máquina virtual buscará actualizaciones en la configuración del nodo según los intervalos especificados por el valor de **Frecuencia de actualización**. Para más información sobre cómo se utilizan estos valores, consulte [Configuración del administrador de configuración local](https://msdn.microsoft.com/PowerShell/DSC/metaConfig).
    
9. En la hoja **Agregar máquinas virtuales de Azure**, haga clic en **Crear**.

Azure iniciará el proceso de incorporación de la máquina virtual. Cuando se complete, la máquina virtual se mostrará en la hoja **Nodos DSC** en la cuenta de Automatización.

## Visualización de la lista de nodos DSC

Puede ver la lista de todas las máquinas que han sido incorporados para la administración de la cuenta de Automatización en la hoja **Nodos DSC**.

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com).

2. En el menú del concentrador, haga clic en **Todos los recursos** y, luego, en el nombre de la cuenta de Automatización.

3. En la hoja **Cuenta de Automatización**, haga clic en **Nodos DSC**.

## Visualización de informes de los nodos DSC

Cada vez que DSC de Automatización de Azure realiza una comprobación de coherencia en un nodos administrado, el nodo envía un informe de estado al servidor de extracción. Estos informes se pueden ver en la hoja de dicho nodo.

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com).

2. En el menú del concentrador, haga clic en **Todos los recursos** y, luego, en el nombre de la cuenta de Automatización.

3. En la hoja **Cuenta de Automatización**, haga clic en **Nodos DSC**.

4. En el icono **Informes**, haga clic en cualquiera de los informes de la lista.

    ![Captura de pantalla de la hoja Informe](./media/automation-dsc-getting-started/NodeReport.png)

En la hoja de un informe individual puede ver la siguiente información de estado de la comprobación de coherencia correspondiente:

- El estado del informe, si el nodo es "Compatible", la configuración "Error" o el nodo es "No compatible" (cuando el nodo está en modo **applyandmonitor** y la máquina no está en el estado deseado).
- La hora de inicio de la comprobación de coherencia.
- El tiempo de ejecución total de la comprobación de coherencia.
- El tipo de comprobación de coherencia.
- Todos los errores, incluidos el código de error y el mensaje de error.
- Los recursos de DSC utilizados en la configuración y el estado de cada recurso (si el nodo está en el estado deseado para dicho recurso): puede hacer clic en cada recurso para más información.
- El nombre, la dirección IP y el modo de configuración del nodo.

También puede hacer clic en **Ver informe sin formato** para ver los datos reales que el nodo envía al servidor. Para más información sobre el uso de esos datos, consulte [Uso de un servidor de informes de DSC](https://msdn.microsoft.com/powershell/dsc/reportserver).

Después de que se incorpora un nodo puede pasar un tiempo antes de que el primer informe esté disponible. Después de incorporar un nodo es posible que tenga que esperar hasta 30 minutos para que aparezca el primer informe.

## Reasignación de un nodo a una configuración de nodo diferente

Puede asignar un nodo para que use una configuración de nodo diferente a la que le asignó inicialmente.

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com).

2. En el menú del concentrador, haga clic en **Todos los recursos** y, luego, en el nombre de la cuenta de Automatización.

3. En la hoja **Cuenta de Automatización**, haga clic en **Nodos DSC**.

4. En la hoja **Nodos DSC**, haga clic en el nombre del nodo que quiere reasignar.

5. En la hoja de dicho nodo, haga clic en **Asignar nodo**.

    ![Captura de pantalla de la hoja Nodo en la que se resalta el botón Asignar nodo](./media/automation-dsc-getting-started/AssignNode.png)

6. En la hoja **Asignar configuración de nodo**, seleccione la configuración de nodo a la que desea asignar el nodo y luego haga clic en **Aceptar**.

    ![Captura de pantalla de la hoja Asignar configuración de nodo](./media/automation-dsc-getting-started/AssignNodeConfig.png)
    
## Anulación del registro de un nodo

Si ya no desea que DSC de Automatización de Azure administre un nodo, puede anular su registro.

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com).

2. En el menú del concentrador, haga clic en **Todos los recursos** y, luego, en el nombre de la cuenta de Automatización.

3. En la hoja **Cuenta de Automatización**, haga clic en **Nodos DSC**.

4. En la hoja **Nodos DSC**, haga clic en el nombre del nodo que quiere reasignar.

5. En la hoja de dicho nodo, haga clic en **Anular registro**.

    ![Captura de pantalla de la hoja Nodo en la que se resalta el botón Anular registro](./media/automation-dsc-getting-started/UnregisterNode.png)

## Artículos relacionados
* [Información general de DSC de Automatización de Azure](automation-dsc-overview.md)
* [Incorporación de máquinas para administrarlas con DSC de Automatización de Azure](automation-dsc-onboarding.md)
* [Información general de la configuración de estado deseado de Windows Powershell](https://msdn.microsoft.com/powershell/dsc/overview)
* [Cmdlets de DSC de Automatización de Azure](https://msdn.microsoft.com/library/mt244122.aspx)
* [Precios de DSC de Automatización de Azure](https://azure.microsoft.com/pricing/details/automation/)

<!---HONumber=AcomDC_0803_2016-->