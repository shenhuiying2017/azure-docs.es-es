<properties title="How to create a custom template image for RemoteApp" pageTitle="How to create a custom template nimage for RemoteApp" description="Learn how to create a custom template image for RemoteApp. You can use this template with either a hybrid or cloud deployment." metaKeywords="" services="" solutions="" documentationCenter="" authors="elizapo" manager="kathyw" />

<tags ms.service="remoteapp" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/12/2014" ms.author="elizapo" ms.manager="kathyw" />

# Creación de una imagen de plantilla personalizada para RemoteApp

RemoteApp de Azure usa una imagen de plantilla de Windows Server 2012 R2 para hospedar todos los programas que desea compartir con sus usuarios. Para crear una imagen de plantilla de RemoteApp, puede comenzar a usar una imagen existente o crear una nueva. Los requisitos para la imagen que se pueden cargar para usarse con RemoteApp de Azure son:

-   El tamaño de la imagen debe ser un múltiplo de MB. Si se intenta cargar una imagen que no es un múltiplo exacto, la carga no se realizará.
-   El tamaño de imagen debe ser de 127 GB o menos.
-   Debe estar en un archivo VHD (los archivos VHDX no se admiten actualmente).
-   El archivo VHD puede ser de tamaño fijo o expandirse dinámicamente. Se recomienda un archivo VHD que se expanda dinámicamente porque tarda menos tiempo en cargarse en Azure que un archivo VHD de tamaño fijo.
-   El disco se debe inicializar usando el estilo de particiones Registro de arranque maestro (MBR, Master Boot Record). El estilo de particiones de tabla de particiones GUID (GPT) no se admite.
-   El archivo VHD debe contener una sola instalación de Windows Server 2012 R2. Puede contener varios volúmenes, pero uno de ellos con una instalación de Windows.
-   El rol Host de sesión de Escritorio remoto (RDSH, Remote Desktop Session Host) y la característica Experiencia de escritorio deben estar instalados.
-   El Sistema de cifrado de archivos (EFS, Encrypting File System) debe estar instalado.
-   Se debe aplicar la herramienta SYSPREP a la imagen usando los parámetros **/oobe /generalize /shutdown** (NO USE el parámetro **/mode:vm**).

**Antes de empezar**

Necesita llevar a cabo los pasos siguientes antes de crear el servicio:

-   Suscríbase a la vista previa de RemoteApp. Puede hacerlo en la dirección [][]<http://azure.microsoft.com/es-es/services/remoteapp/></a>.
-   Cree una cuenta de usuario en Active Directory para usar la cuenta de servicio RemoteApp. Restrinja los permisos para esta cuenta de forma que solamente pueda unir máquinas al dominio.
-   Recopile información sobre la red local: información de direcciones IP y detalles de dispositivos VPN.
-   Instale el módulo [Azure PowerShell][Azure PowerShell].
-   Recopile información sobre los usuarios y grupos a los que desee conceder acceso. Esta información puede ser información de cuenta de Microsoft o información de cuenta de organización de Active Directory de usuarios o grupos.

## **Creación de una imagen de plantilla**

Para crear una nueva imagen de plantilla desde cero:

1.  Busque un DVD o una imagen ISO de Windows Server 2012 R2.
2.  Cree un archivo VHD.
3.  Instale Windows Server 2012 R2.
4.  Instale el rol Host de sesión de Escritorio remoto (RDSH, Remote Desktop Session Host) y la característica Experiencia de escritorio.
5.  Instale las características adicionales que necesitan sus aplicaciones.
6.  Instale y configure sus aplicaciones.
7.  Realice cualquier configuración adicional de Windows que requieran sus aplicaciones.
8.  Deshabilite el Sistema de cifrado de archivos (EFS).
9.  Aplique la herramienta SYSPREP a la imagen.

A continuación se indican los pasos detallados para crear una nueva imagen:

1.  Busque un DVD o una imagen ISO de Windows Server 2012 R2.
2.  Cree un archivo VHD usando Administración de discos.

    1.  Inicie Administración de discos (diskmgmt.msc).
    2.  Cree un archivo VHD que se expanda dinámicamente con un tamaño mínimo de 40 GB. (Calcule la cantidad de espacio necesario para Windows, sus aplicaciones y personalizaciones. Windows Server con el rol RDSH y la característica Experiencia de escritorio instalados requerirán 10 GB de espacio aproximadamente).

        1.  Haga clic en **Acción \> Crear VHD**.
        2.  Especifique la ubicación, el tamaño y el formato VHD. Seleccione **Expansión dinámica** y haga clic en **Aceptar**.

            Esto se ejecutará durante varios segundos. Cuando la creación del archivo VHD se complete, debe ver un nuevo disco sin ninguna letra de unidad y en el estado “No inicializado" en la consola Administración de discos.

        -   Haga clic con el botón secundario en el disco (no en el espacio sin asignar) y después haga clic en **Inicializar disco**. Seleccione **MBR** (Master Boot Record, es decir, registro de arranque maestro) como el estilo de partición y después haga clic en **Aceptar**.
        -   Cree un nuevo volumen: haga clic con el botón secundario en el espacio sin asignar y después en **Nuevo volumen simple**. Puede aceptar los valores predeterminados del asistente pero asegúrese de asignar una letra de unidad para evitar problemas potenciales cuando cargue la imagen de plantilla.
        -   Haga clic con el botón secundario en el disco y después en **Ocultar VHD**.

3.  Instale Windows Server 2012 R2:

    1.  Cree una nueva máquina virtual. Use el Asistente para nueva máquina virtual de Administrador de Hyper-V o Cliente Hyper-V.

        1.  En la página Especificar generación, elija **Generación 1**.
        2.  En la página Conectar disco duro virtual, seleccione **Usar un disco duro virtual existente** y busque su VHD que creó en el paso anterior.
        3.  En la página Opciones de instalación, seleccione **Instalar un sistema operativo desde un CD/DVD-ROM de arranque** y después seleccione la ubicación del disco de instalación de Windows Server 2012 R2.
        4.  Elija otras opciones del asistente necesarias para instalar Windows y sus aplicaciones. Finalice el asistente.

    2.  Después de finalizar el asistente, edite la configuración de la máquina virtual y realice cualquier otro cambio necesario para instalar Windows y sus programas —por ejemplo, el número de procesadores virtuales— y, por último, haga clic en **Aceptar**.
    3.  Conéctese a la máquina virtual e instale Windows Server 2012 R2.

4.  Instale el rol Host de sesión de Escritorio remoto (RDSH, Remote Desktop Session Host) y la característica Experiencia de escritorio:
    1.  Inicie Administrador de servidores.
    2.  Haga clic en **Agregar roles y características** en la pantalla de bienvenida o desde el menú **Administrar**.
    3.  Haga clic en **Siguiente** en la página Antes de empezar.
    4.  Seleccione **Instalación basada en características o en roles** y haga clic en **Siguiente**.
    5.  Seleccione la máquina local en la lista y haga clic en **Siguiente**.
    6.  Seleccione **Servicios de Escritorio remoto** y haga clic en **Siguiente**.
    7.  Expanda **Infraestructura e interfaces de usuario** y seleccione **Experiencia de escritorio**.
    8.  Haga clic en **Agregar características** y después en **Siguiente**.
    9.  En la página Servicios de Escritorio remoto, haga clic en **Siguiente**.
    10. Haga clic en **Host de sesión de Escritorio remoto**.
    11. Haga clic en **Agregar características** y después en **Siguiente**.
    12. En la página Confirmar selecciones de instalación, seleccione **Reiniciar automáticamente el servidor de destino en caso necesario** y haga clic en **Sí** en la advertencia de reinicio.
    13. Haga clic en **Instalar**. El equipo se reiniciará.

5.  Instale las características adicionales que necesiten sus aplicaciones, como por ejemplo .NET Framework 3.5. Para instalar las características, ejecute el Asistente para agregar roles y características.
6.  Instale y configure los programas y aplicaciones que desee publicar a través de RemoteApp.

    **Importante:** Microsoft recomienda instalar el rol RDSH antes de instalar aplicaciones para garantizar que se detecta cualquier problema con la compatibilidad de aplicaciones antes de que la imagen se cargue en RemoteApp.

7.  Realice cualquier configuración adicional de Windows que requieran sus aplicaciones.
8.  Deshabilite el Sistema de cifrado de archivos (EFS). Ejecute el comando siguiente en una ventana de comandos con privilegios elevados:

        Fsutil behavior set disableencryption 1

    Alternativamente, puede establecer o agregar el siguiente valor DWORD en el Registro:

        HKLM\System\CurrentControlSet\Control\FileSystem\NtfsDisableEncryption = 1

9.  Si está creando la imagen dentro de una máquina virtual de Azure, cambie el nombre del archivo **\\%windir%\\Panther\\Unattend.xml**, ya que bloqueará el script de carga usado posteriormente desde el trabajo. Cambie el nombre de este archivo por Unattend.old; de este modo, seguirá teniendo el archivo en caso de que necesite revertir la implementación.
10. Aplique la herramienta SYSPREP a la imagen. En un símbolo del sistema con privilegios elevados, ejecute el siguiente comando:

    **C:\\Windows\\System32\\sysprep\\sysprep.exe /generalize /oobe /shutdown**

    **Nota:** No use el modificador **/mode:vm** del comando SYSPREP aunque esta sea una máquina virtual.

## Pasos siguientes

Ahora que ya tiene su imagen de plantilla personalizada, cárguela en su implementación de RemoteApp. Para obtener información sobre cómo crear la implementación, lea los artículos siguientes:

-   [Creación de una implementación híbrida de RemoteApp][Creación de una implementación híbrida de RemoteApp]
-   [Creación de una implementación de RemoteApp en la nube][Creación de una implementación de RemoteApp en la nube]

  []: http://azure.microsoft.com/es-es/services/remoteapp/
  [Azure PowerShell]: http://azure.microsoft.com/es-es/documentation/articles/install-configure-powershell/
  [Creación de una implementación híbrida de RemoteApp]: http://azure.microsoft.com/es-es/documentation/articles/remoteapp-create-hybrid-deployment/
  [Creación de una implementación de RemoteApp en la nube]: http://azure.microsoft.com/es-es/documentation/articles/remoteapp-create-cloud-deployment/
