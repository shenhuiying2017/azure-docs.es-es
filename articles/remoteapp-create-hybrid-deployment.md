<properties title="How to create a hybrid deployment of RemoteApp" pageTitle="How to create a hybrid deployment of RemoteApp" description="Learn how to create a deployment of RemoteApp that connects to your internal network." metaKeywords="" services="" solutions="" documentationCenter="" authors="elizapo"  />

<tags ms.service="remoteapp" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="elizapo"></tags>

# Creación de una implementación híbrida de RemoteApp

Hay dos tipos de implementación de RemoteApp:

-   Nube: reside completamente en Azure y se crea con la opción **Creación rápida** en el Portal de administración de Azure.
-   Híbrida: incluye una red virtual para acceso local y se crea usando la opción **Crear con VPN** del Portal de administración.

En este tutorial se realizará un recorrido por el proceso de creación de una implementación híbrida. Son siete pasos:

1.  Crear una imagen de plantilla de RemoteApp.
2.  Crear un servicio RemoteApp.
3.  Vincular una red virtual.
4.  Vincular una imagen de plantilla.
5.  Configurar la sincronización de directorios. RemoteApp lo requiere para sincronizar los usuarios, grupos, contactos y contraseñas de su Active Directory local con su inquilino de Azure Active Directory.
6.  Publicar programas RemoteApp.
7.  Configurar el acceso del usuario.

**Antes de empezar**

Necesita llevar a cabo los pasos siguientes antes de crear el servicio:

-   Instale las [actualizaciones necesarias][] para mejorar el rendimiento de RemoteApp de Azure.
-   Suscribirse a la [versión de vista previa de RemoteApp][].
-   Cree una cuenta de usuario en Active Directory para usar la cuenta de servicio RemoteApp. Restrinja los permisos para esta cuenta de forma que solamente pueda unir máquinas al dominio.
-   Recopile información sobre la red local: información de direcciones IP y detalles de dispositivos VPN.
-   Instale el módulo [Azure PowerShell][].
-   Recopile información sobre los usuarios y grupos a los que desee conceder acceso. Esta información puede ser información de cuenta de Microsoft o información de cuenta de organización de Active Directory de usuarios o grupos.

## **Paso 1: Crear una imagen de plantilla**

RemoteApp de Azure usa una imagen de plantilla de Windows Server 2012 R2 para hospedar todos los programas que desea compartir con sus usuarios. Para crear una imagen de plantilla de RemoteApp, puede comenzar a usar una imagen existente o crear una nueva. Los requisitos para la imagen que se pueden cargar para usarse con RemoteApp de Azure son:

-   Debe estar en un archivo VHD (los archivos VHDX no se admiten actualmente).
-   El archivo VHD puede ser de tamaño fijo o expandirse dinámicamente. Se recomienda un archivo VHD que se expanda dinámicamente porque tarda menos tiempo en cargarse en Azure que un archivo VHD de tamaño fijo.
-   El disco se debe inicializar usando el estilo de particiones Registro de arranque maestro (MBR, Master Boot Record). El estilo de particiones de tabla de particiones GUID (GPT) no se admite.
-   El archivo VHD debe contener una sola instalación de Windows Server 2012 R2. Puede contener varios volúmenes, pero uno de ellos con una instalación de Windows.
-   El rol Host de sesión de Escritorio remoto (RDSH, Remote Desktop Session Host) y la característica Experiencia de escritorio deben estar instalados.
-   El Sistema de cifrado de archivos (EFS, Encrypting File System) debe estar instalado.
-   Se debe aplicar la herramienta SYSPREP a la imagen usando los parámetros **/oobe /generalize /shutdown** (NO USE el parámetro **/mode:vm**).

Para crear un nueva imagen de plantilla desde cero:

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
        1.  En la página Conectar disco duro virtual, seleccione **Usar un disco duro virtual existente** y busque su VHD que creó en el paso anterior.
        2.  En la página Opciones de instalación, seleccione **Instalar un sistema operativo desde un CD/DVD-ROM de arranque** y después seleccione la ubicación del disco de instalación de Windows Server 2012 R2.
        3.  Elija otras opciones del asistente necesarias para instalar Windows y sus aplicaciones. Finalice el asistente.
    2.  Después de finalizar el asistente, edite la configuración de la máquina virtual y realice cualquier otro cambio necesario para instalar Windows y sus programas, como por ejemplo el número de procesadores virtuales, y, por último, haga clic en **Aceptar**.
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
    12. En la página Confirmar selecciones de instalación seleccione **Reiniciar automáticamente el servidor de destino en caso necesario** y haga clic en **Sí** en la advertencia de reinicio.
    13. Haga clic en **Instalar**. El equipo se reiniciará.

5.  Instale las características adicionales que necesiten sus aplicaciones, como por ejemplo .NET Framework 3.5. Para instalar las características, ejecute el Asistente para agregar roles y características.
6.  Instale y configure los programas y aplicaciones que desee publicar a través de RemoteApp.

    **Importante:** Microsoft recomienda instalar el rol RDSH antes de instalar aplicaciones para garantizar que se detecta cualquier problema con la compatibilidad de aplicaciones antes de que la imagen se cargue en RemoteApp.

7.  Realice cualquier configuración adicional de Windows que requieran sus aplicaciones.
8.  Deshabilite el Sistema de cifrado de archivos (EFS). Ejecute el comando siguiente en una ventana de comandos con privilegios elevados:

        Fsutil behavior set disableencryption 1

    Alternativamente, puede establecer o agregar el siguiente valor DWORD en el Registro:

        HKLM\System\CurrentControlSet\Control\FileSystem\NtfsDisableEncryption = 1

9.  Si está creando la imagen dentro de una **Máquina virtual de Azure**, necesitará eliminar el archivo **\\Windows\\Panther\\Unattend.xml** o cambiar su nombre, ya que bloqueará el script de carga usado posteriormente desde el trabajo.
10. Aplique la herramienta SYSPREP a la imagen. En un símbolo del sistema con privilegios elevados, ejecute el siguiente comando:

    **C:\\Windows\\System32\\sysprep\\sysprep.exe /generalize /oobe /shutdown**

    **Nota:** no use el modificador **/mode:vm** del comando SYSPREP aunque esta sea una máquina virtual.

## **Paso 2: Creación de un servicio RemoteApp**

1.  En el [Portal de administración de Azure][], vaya a la página RemoteApp.
2.  Haga clic en **Nuevo \> Crear con VPN**.
3.  Escriba un nombre para el servicio y haga clic en **Crear servicio de RemoteApp**.

Después de crear el servicio RemoteApp, vaya a la página **Inicio rápido** de RemoteApp para continuar con los pasos de configuración.

## **Paso 3: Vincular una red virtual**

Una red virtual permite a los usuarios acceder a los datos de la red local a través de recursos remotos de RemoteApp. Debe llevar a cabo cuatro pasos para configurar el vínculo de red virtual:

1.  En la página Inicio rápido, haga clic en **Vincular una red virtual RemoteApp**.
2.  Elija si desea crear una nueva red virtual o usar una existente. Para este tutorial, crearemos una red nueva.
3.  Proporcione la siguiente información para la nueva red:

    -   Nombre
    -   Espacio de dirección de red virtual
    -   Espacio de dirección local
    -   Dirección IP del servidor DNS
    -   Dirección IP VPN

    Consulte [Configurar una VPN de sitio a sitio en el Portal de administración][] para obtener más información.

4.  A continuación, vuelva a la página Inicio rápido, haga clic en **obtener script** para descargar un script para configurar el dispositivo VPN para conectarse a la red virtual que acaba de crear. Necesitará la siguiente información acerca del dispositivo VPN:

    -   Proveedor
    -   Plataforma
    -   Sistema operativos

    Guarde el script y ejecútelo en el dispositivo VPN.

    **Nota:** después de ejecutar este script, la red virtual pasará al estado de preparada, lo que puede tardar entre 5 y 7 minutos. Hasta que la red esté preparada, no podrá usarla.

5.  Por último, de nuevo en la página Inicio rápido, haga clic en **unirse al dominio local**. Agregue la cuenta de servicio RemoteApp al dominio de Active Directory local. Necesitará el nombre de dominio, la unidad organizativa, el nombre del usuario de la cuenta de servicio y la contraseña.

## **Paso 4: Vincular una imagen de plantilla de RemoteApp**

Una imagen de plantilla de RemoteApp contiene los programas que desea compartir con los usuarios. Puede cargar la nueva imagen de plantilla creada en el paso uno o vincular una imagen existente (una ya cargada en Azure).

Si está cargando la nueva imagen, puede que necesite escribir el nombre y elegir la ubicación para dicha imagen. En la página siguiente del asistente, verá un conjunto de cmdlets de PowerShell. Copie y ejecute estos cmdlets desde un símbolo de Azure PowerShell con privilegios elevados para cargar la imagen especificada.

Si vincula una imagen de plantilla existente, simplemente especifique el nombre, la ubicación y la suscripción de Azure asociada de la imagen.

## **Paso 5: Configurar la sincronización de directorios de Active Directory**

RemoteApp requiere la sincronización de directorios entre Azure Active Directory y su Active Directory local para sincronizar usuarios, grupos, contactos y contraseñas con su inquilino de Azure Active Directory. Consulte [Guía de sincronización de directorios][] para obtener información sobre planeación y pasos detallados.

## **Paso 6: Publicación de programas RemoteApp**

Un programa RemoteApp es la aplicación o el programa que proporciona a los usuarios. Se encuentra en la imagen de plantilla que cargó para el servicio. Cuando un usuario accede a un programa RemoteApp, da la sensación de que este se ejecuta en el entorno local, pero realmente se está ejecutando en Azure.

Para que los usuarios puedan obtener acceso a programas RemoteApp, debe publicarlos en la fuente de usuarios finales (lista de programas disponibles a los que los usuarios acceden desde el portal de Azure).

Puede publicar varios programas en su servicio RemoteApp. Desde la página de programas de RemoteApp, haga clic en **Publicar** para agregar un programa. Puede publicar desde el menú Inicio de la imagen de plantilla o especificando la ruta de acceso en la imagen de plantilla para el programa. Si opta por agregar desde el menú Inicio, elija el programa para publicar. Si opta por proporcionar la ruta de acceso al programa, proporcione un nombre para el programa y la ruta de acceso en la que se instaló el programa en la imagen de la plantilla.

## **Paso 7: Configuración del acceso de usuarios**

Ahora que ha creado el servicio RemoteApp, necesita agregar los usuarios y grupos que desea que puedan usar los recursos remotos. Los usuarios y grupos a los que proporciona acceso necesitan existir en el inquilino de Active Directory asociado a la suscripción usada para crear este servicio RemoteApp.

1.  Desde la página Inicio rápido, haga clic en **Configurar acceso de usuario**.
2.  Escriba la cuenta organizativa o nombre de grupo (desde Active Directory) o la cuenta de Microsoft para la que desea conceder acceso.

    Para los usuarios, asegúrese de que usa el formato “[usuario@dominio.com][]”. Para los grupos, escriba el nombre de grupo.

3.  Una vez que los usuarios o grupos se validen, haga clic en **Guardar**.

## Pasos siguientes

Esto es todo; ha creado he implementado correctamente su implementación híbrida de RemoteApp. El paso siguiente es que los usuarios descarguen e instalen el cliente Escritorio remoto. Puede encontrar la dirección URL del cliente en la página Inicio rápido de RemoteApp. Después, indique a los usuarios que inicien sesión en Azure y accedan a los programas de RemoteApp publicados.

  [actualizaciones necesarias]: http://support.microsoft.com/kb/2977219
  [versión de vista previa de RemoteApp]: http://azure.microsoft.com/en-us/services/remoteapp/
  [Azure PowerShell]: http://azure.microsoft.com/en-us/documentation/articles/install-configure-powershell/
  [Portal de administración de Azure]: http://manage.windowsazure.com
  [Configurar una VPN de sitio a sitio en el Portal de administración]: http://msdn.microsoft.com/library/azure/dn133795.aspx
  [Guía de sincronización de directorios]: http://msdn.microsoft.com//library/azure/hh967642.aspx
  [usuario@dominio.com]: mailto:user@domain.com
