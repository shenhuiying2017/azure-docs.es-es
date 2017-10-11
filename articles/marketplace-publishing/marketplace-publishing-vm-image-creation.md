---
title: "Creación de una imagen de máquina virtual para Azure Marketplace | Microsoft Docs"
description: "Instrucciones detalladas sobre cómo crear una imagen de máquina virtual para Azure Marketplace para que otros usuarios la compren."
services: Azure Marketplace
documentationcenter: 
author: HannibalSII
manager: hascipio
editor: 
ms.assetid: 5c937b8e-e28d-4007-9fef-624046bca2ae
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: Azure
ms.workload: na
ms.date: 01/05/2017
ms.author: hascipio; v-divte
ms.openlocfilehash: 046ce7af40301014746c6aef07d08d81ab4adcc2
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/29/2017
---
# <a name="guide-to-create-a-virtual-machine-image-for-the-azure-marketplace"></a>Guía para la creación de una imagen de máquina virtual para Azure Marketplace
En este artículo, **paso 2**, se explica cómo puede preparar los discos duros virtuales (VHD) que va a implementar en Azure Marketplace. Los VHD constituyen el fundamento de la SKU. El proceso difiere en función de si la SKU que va a proporcionar está basada en Linux o en Windows. En este artículo se tratan ambos escenarios. Este proceso puede realizarse en paralelo con la [creación y registro de cuentas][link-acct-creation].

## <a name="1-define-offers-and-skus"></a>1. Definición de ofertas y SKU
En esta sección aprenderá a definir las ofertas y sus SKU.

Una oferta es un "elemento primario" de todas sus SKU. Puede tener varias ofertas. Usted elige cómo desea estructurarlas. Cuando se inserta una oferta en un entorno de ensayo, se inserta con todas sus SKU. Sea cauteloso a la hora de elegir los identificadores de sus SKU, ya que serán visibles en la dirección URL.

* Azure.com: http://azure.microsoft.com/marketplace/partners/{PartnerNamespace}/{OfferIdentifier}-{SKUidentifier}
* Portal de vista previa de Azure: https://portal.azure.com/#gallery/{PublisherNamespace}.{OfferIdentifier}{SKUIDdentifier}  

Una SKU es el nombre comercial de una imagen de máquina virtual. Una imagen de máquina virtual contiene un disco del sistema operativo y cero o más discos de datos. Se trata, básicamente, del perfil de almacenamiento completo de una máquina virtual. Es necesario un VHD por disco. Incluso los discos de datos vacíos requieren la creación de un VHD.

Independientemente del sistema operativo que use, agregue solo el número mínimo de discos de datos necesarios para la SKU. Los usuarios no pueden quitar discos que formen parte de una imagen durante la implementación pero siempre pueden agregar discos durante o después de la implementación si los necesitan.

> [!IMPORTANT]
> **No cambie el número de discos en una nueva versión de imagen.** Si necesita volver a configurar los discos de datos de la imagen, defina una nueva SKU. Si publica una nueva versión de la imagen con un número de discos distinto, las nuevas implementaciones que se realicen con arreglo a la nueva versión pueden registrar problemas con la autoescala, con la implementación automática de soluciones mediante plantillas de ARM y con otros escenarios.
>
>

### <a name="11-add-an-offer"></a>1.1 Agregar una oferta
1. Inicie sesión en el [Portal de publicación][link-pubportal] con su cuenta de vendedor.
2. Seleccione la pestaña **Máquinas virtuales** del Portal de publicación. En el campo de entrada, escriba el nombre de la oferta. El nombre de la oferta es normalmente el nombre del producto o servicio que vaya a vender en Azure Marketplace.
3. Seleccione **Crear**.

### <a name="12-define-a-sku"></a>1.2 Definir una SKU
Una vez agregada una oferta, tiene que definir e identificar sus SKU. Puede tener varias ofertas y cada oferta puede tener varias SKU. Cuando se inserta una oferta en un entorno de ensayo, se inserta con todas sus SKU.

1. **Agregue una SKU.** La SKU requiere un identificador que se usa en la dirección URL. El identificador tiene que ser único dentro de su perfil de publicación, pero no existe riesgo de colisión de identificadores con otros anunciantes.

   > [!NOTE]
   > La oferta y los identificadores de SKU se muestran en la dirección URL de la oferta en Marketplace.
   >
   >
2. **Agregue una descripción resumida de la SKU.** Las descripciones resumidas serán visibles para los clientes, así que es recomendable que sean fáciles de leer. No es necesario que esta información esté bloqueada hasta que se inserte en la fase de entorno de ensayo. Hasta entonces, puede editarla como desee.
3. Si usa SKU basadas en Windows, siga los vínculos sugeridos para adquirir las versiones aprobadas de Windows Server.

## <a name="2-create-an-azure-compatible-vhd-linux-based"></a>2. Creación de un VHD compatible con Azure (basado en Linux)
Esta sección se centra en los procedimientos recomendados para crear una imagen de máquina virtual basada en Linux para Azure Marketplace. Vea un tutorial detallado en la documentación siguiente: [Creación y carga de un disco duro virtual que contiene el sistema operativo Linux](../virtual-machines/linux/classic/create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json).

## <a name="3-create-an-azure-compatible-vhd-windows-based"></a>3. Creación de un VHD compatible con Azure (basado en Windows)
En esta sección se indican los pasos necesarios para crear una SKU basada en Windows Server para Azure Marketplace.

### <a name="31-ensure-that-you-are-using-the-correct-base-vhds"></a>3.1 Asegurarse de que se usan los VHD base correctos
Los VHD del sistema operativo para su imagen de máquina virtual deben basarse en una imagen aprobada para Azure que contenga Windows Server o SQL Server.

Para comenzar, cree una máquina virtual a partir de alguna de las siguientes imágenes, que encontrará en el [Portal de Microsoft Azure][link-azure-portal]:

* Windows Server ([2012 R2 Datacenter][link-datactr-2012-r2], [2012 Datacenter][link-datactr-2012], [2008 R2 SP1][link-datactr-2008-r2])
* SQL Server 2014 ([Enterprise][link-sql-2014-ent], [Standard][link-sql-2014-std], [Web][link-sql-2014-web])
* SQL Server 2012 SP2 ([Enterprise][link-sql-2012-ent], [Standard][link-sql-2012-std], [Web][link-sql-2012-web])

Estos vínculos se encuentran también en el portal de publicación, en la página de la SKU.

> [!TIP]
> Si usa el Portal de Azure actual o PowerShell, las imágenes de Windows Server publicadas a partir del 8 de septiembre de 2014 están aprobadas.
>
>

### <a name="32-create-your-windows-based-vm"></a>3.2 Crear su propia máquina virtual basada en Windows
Desde el Portal de Microsoft Azure, puede crear su máquina virtual basada en una imagen base aprobada con solo algunos pasos sencillos. A continuación se ofrece información general del proceso:

1. En la página de la imagen base, seleccione **Crear máquina virtual** para ir al nuevo [Portal de Microsoft Azure][link-azure-portal].

    ![dibujo][img-acom-1]
2. Inicie sesión en el portal con la cuenta Microsoft y la contraseña de la suscripción de Azure que desee usar.
3. Siga las indicaciones para crear una máquina virtual con la imagen base que seleccionó. Tendrá que proporcionar un nombre de host (nombre del equipo), un nombre de usuario (registrado como administrador) y una contraseña para la máquina virtual.

    ![dibujo][img-portal-vm-create]
4. Seleccione el tamaño de la máquina virtual que desea implementar:

    a.    Si planea desarrollar el VHD en modo local, el tamaño no importa. Considere usar una de las máquinas virtuales de menor tamaño.

    b.    Si planea desarrollar la imagen en Azure, considere usar uno de los tamaños de máquina virtual recomendados para la imagen seleccionada.

    c.    Para obtener información de precios, consulte el selector de **plan de tarifa recomendada** que se muestra en el portal. Mostrará los tres precios recomendados proporcionados por el anunciante. (En este caso, el anunciante es Microsoft.)

    ![dibujo][img-portal-vm-size]
5. Establezca las propiedades:

    a.    Para conseguir una implementación rápida, puede dejar los valores predeterminados de las propiedades en **Configuración opcional** y **Grupo de recursos**.

    b.    En **Cuenta de almacenamiento**, puede seleccionar opcionalmente la cuenta de almacenamiento donde se almacenará el VHD del sistema operativo.

    c.    En **Grupo de recursos**, puede seleccionar opcionalmente el grupo lógico donde ubicar la máquina virtual.
6. Seleccione la **Ubicación** para la implementación:

    a.    Si planea desarrollar el VHD en modo local, la ubicación no importa, ya que cargará la imagen más tarde en Azure.

    b.    Si planea desarrollar la imagen en Azure, considere usar una de las regiones de Microsoft Azure en Estados Unidos desde el principio. Esto agiliza el proceso de copia del VHD que Microsoft realiza por usted cuando envía la imagen para certificarla.

    ![dibujo][img-portal-vm-location]
7. Haga clic en **Crear**. La máquina virtual empieza a implementarse. En cuestión de minutos, dispondrá de una implementación correcta y podrá comenzar a crear la imagen para su SKU.

### <a name="33-develop-your-vhd-in-the-cloud"></a>3.3 Desarrollar el VHD en la nube
Se recomienda encarecidamente desarrollar el VHD en la nube mediante el Protocolo de escritorio remoto (RDP). La conexión a RDP se realiza con el nombre de usuario y la contraseña que especificó durante el aprovisionamiento.

> [!IMPORTANT]
> Si está desarrollando su VHD de forma local (aunque no se recomienda), consulte [Creación de una imagen de máquina virtual de forma local](marketplace-publishing-vm-image-creation-on-premise.md). Si desarrolla el VHD en la nube, no es necesario descargarlo.
>
>

**Conectarse mediante RDP usando el [Portal de Microsoft Azure][link-azure-portal]**

1. Seleccione **Examinar** > **Máquinas virtuales**.
2. Se abre la hoja Máquinas virtuales. Compruebe que la máquina virtual con la que desea conectarse está ejecutándose y selecciónela en la lista de máquinas virtuales implementadas.
3. Se abre una hoja en la que se describe la máquina virtual seleccionada. En la parte superior, haga clic en **Conectar**.
4. Se le pide que proporcione el nombre de usuario y la contraseña que especificó durante el aprovisionamiento.

**Conectarse mediante RDP con PowerShell**

Para descargar un archivo de escritorio remoto en una máquina local, use el [cmdlet Get-AzureRemoteDesktopFile][link-technet-2]. Para usar este cmdlet, tiene que conocer el nombre del servicio y el de la máquina virtual. Si creó la máquina virtual desde el [Portal de Microsoft Azure][link-azure-portal], puede ver esta información en las propiedades de la máquina virtual:

1. En Microsoft Azure Portal, seleccione **Examinar** > **Máquinas virtuales**.
2. Se abre la hoja Máquinas virtuales. Seleccione la máquina virtual que ha implementado.
3. Se abre una hoja en la que se describe la máquina virtual seleccionada.
4. Haga clic en **Propiedades**.
5. La primera parte del nombre del dominio es el nombre del servicio. El nombre de host es el nombre de la máquina virtual.

    ![dibujo][img-portal-vm-rdp]
6. El cmdlet para descargar el archivo RDP de la máquina virtual creada en el escritorio local del administrador es el siguiente.

        Get‐AzureRemoteDesktopFile ‐ServiceName “baseimagevm‐6820cq00” ‐Name “BaseImageVM” –LocalPath “C:\Users\Administrator\Desktop\BaseImageVM.rdp”

Encontrará más información acerca de RDP en el artículo de MSDN [Conectarse a una máquina virtual de Azure con RDP o SSH](http://msdn.microsoft.com/library/azure/dn535788.aspx).

**Configurar una máquina virtual y crear la SKU**

Una vez descargado el VHD del sistema operativo, use Hyper-V y configure una máquina virtual para comenzar a crear la SKU. Encontrará los pasos detallados en el siguiente vínculo de TechNet: [Instalar Hyper-V y crear una máquina virtual](http://technet.microsoft.com/library/hh846766.aspx).

### <a name="34-choose-the-correct-vhd-size"></a>3.4 Elegir el tamaño de VHD correcto
El VHD del sistema operativo Windows en la imagen de máquina virtual debe crearse como VHD de 128 GB con formato fijo.  

Si el tamaño físico es inferior a 128 GB, el VHD debe ser disperso. Las imágenes base de Windows y SQL Server proporcionadas cumplen ya estos requisitos, por lo que no es necesario que cambie el formato ni el tamaño del VHD obtenido.  

Los discos de datos pueden tener un tamaño de hasta 1 TB. A la hora de decidir el tamaño de disco, tenga en cuenta que los clientes no pueden cambiar el tamaño de los VHD de una imagen en el momento de la implementación. Los VHD para discos de datos deben crearse como VHD de formato fijo. También deben ser dispersos. Los discos de datos pueden estar vacíos o contener datos.

### <a name="35-install-the-latest-windows-patches"></a>3.5 Instalar las últimas revisiones de Windows
Las imágenes base contienen las últimas revisiones hasta su fecha de publicación. Antes de publicar el VHD del sistema operativo que ha creado, asegúrese de que se ejecutó Windows Update y se instalaron todas las revisiones de seguridad Críticas e Importantes.

### <a name="36-perform-additional-configuration-and-schedule-tasks-as-necessary"></a>3.6 Configurar opciones adicionales y programar tareas según sea necesario
Si es necesaria alguna configuración adicional, considere la posibilidad de usar una tarea programada que se ejecute al inicio para realizar posibles cambios finales en la máquina virtual una vez implementada.

* Se recomienda que la propia tarea se elimine después de ejecutarse correctamente.
* Ninguna configuración debe depender de unidades que no sean C o D, ya que estas son las dos únicas unidades cuya existencia está garantizada. La unidad C es el disco del sistema operativo y unidad D es el disco local temporal.

### <a name="37-generalize-the-image"></a>3.7 Generalizar la imagen
Todas las imágenes de Azure Marketplace deben ser reutilizables de forma genérica. Dicho de otro modo, el VHD del sistema operativo debe generalizarse:

* Para Windows, la imagen debe estar preparada con sysprep y no deben realizarse configuraciones que no admitan el comando **sysprep** .
* Puede ejecutar el siguiente comando desde el directorio % windir%\System32\Sysprep.

        sysprep.exe /generalize /oobe /shutdown

  En uno de los pasos del artículo de MSDN [Crear y cargar un VHD de Windows Server a Azure](../virtual-machines/windows/classic/createupload-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json), se explica cómo preparar el sistema operativo con Sysprep.

## <a name="4-deploy-a-vm-from-your-vhds"></a>4. Implementación de una máquina virtual a partir de VHD
Una vez cargados en una cuenta de almacenamiento de Azure sus VHD (el VHD del sistema operativo generalizado y cero o más VHD de discos de datos), puede registrarlos como imagen de máquina virtual de usuario. A continuación, puede probar esa imagen. Tenga en cuenta que, como el VHD del sistema operativo está generalizado, no puede implementar la máquina virtual directamente proporcionando la dirección URL del VHD.

Para obtener más información sobre las imágenes de máquina virtual consulte las siguientes entradas de blog:

* [Imagen de máquina virtual](https://azure.microsoft.com/blog/vm-image-blog-post/)
* [Procedimientos para trabajar con imágenes de máquina virtual en PowerShell](https://azure.microsoft.com/blog/vm-image-powershell-how-to-blog-post/)
* [Acerca de las imágenes de máquina virtual en Azure](https://msdn.microsoft.com/library/azure/dn790290.aspx)

### <a name="set-up-the-necessary-tools-powershell-and-azure-cli"></a>Configurar las herramientas necesarias, PowerShell y la CLI de Azure
* [Cómo configurar PowerShell](/powershell/azure/overview)
* [Cómo configurar la CLI de Azure](../cli-install-nodejs.md)

### <a name="41-create-a-user-vm-image"></a>4.1 Crear una imagen de máquina virtual de usuario
#### <a name="capture-vm"></a>Captura de máquina virtual
Vea los vínculos siguientes para obtener instrucciones sobre cómo capturar la máquina virtual con API/PowerShell/CLI de Azure.

* [API](https://msdn.microsoft.com/library/mt163560.aspx)
* [PowerShell](../virtual-machines/windows/capture-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [CLI de Azure](../virtual-machines/linux/capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

### <a name="generalize-image"></a>Generalización de una imagen
Vea los vínculos siguientes para obtener instrucciones sobre cómo capturar la máquina virtual con API/PowerShell/CLI de Azure.

* [API](https://msdn.microsoft.com/library/mt269439.aspx)
* [PowerShell](../virtual-machines/windows/capture-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [CLI de Azure](../virtual-machines/linux/capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

### <a name="42-deploy-a-vm-from-a-user-vm-image"></a>4.2 Implementar una máquina virtual a partir de una imagen de máquina virtual de usuario
Para implementar una máquina virtual a partir de una imagen de máquina virtual de usuario, puede usar el [Portal de Azure](https://manage.windowsazure.com) actual o PowerShell.

**Implementar una máquina virtual desde el Portal de Azure actual**

1. Vaya a **Nuevo** > **Proceso** > **Máquina virtual** > **De la galería**.

    ![dibujo][img-manage-vm-new]
2. Vaya a **Mis imágenes**y seleccione la imagen de máquina virtual con la que quiere implementar una máquina virtual:

   1. Preste mucha atención a la imagen que selecciona, porque la vista **Mis imágenes** enumera tanto imágenes del sistema operativo como imágenes de máquina virtual.
   2. Comprobar el número de discos puede ayudar a determinar el tipo de imagen que va a implementar, ya que la mayoría de las imágenes de máquina virtual tienen más de un disco. No obstante, es posible tener una imagen de máquina virtual con un solo disco del sistema operativo, es decir, tendría **Número de discos** establecido en un 1.

      ![dibujo][img-manage-vm-select]
3. Siga el asistente para crear máquinas virtuales y especifique el nombre de la máquina virtual, el tamaño, la ubicación, el nombre de usuario y la contraseña.

**Implementación de una máquina virtual a partir de PowerShell**

Para implementar una máquina virtual de gran tamaño, puede usar los siguientes cmdlets desde la imagen de máquina virtual generalizada que acaba de crear.

    $img = Get-AzureVMImage -ImageName "myVMImage"
    $user = "user123"
    $pass = "adminPassword123"
    $myVM = New-AzureVMConfig -Name "VMImageVM" -InstanceSize "Large" -ImageName $img.ImageName | Add-AzureProvisioningConfig -Windows -AdminUsername $user -Password $pass
    New-AzureVM -ServiceName "VMImageCloudService" -VMs $myVM -Location "West US" -WaitForBoot

> [!IMPORTANT]
> Vea [Solución de problemas comunes que se encuentran durante la creación de VHD] para obtener más ayuda.
>
>

## <a name="5-obtain-certification-for-your-vm-image"></a>5. Obtención de la certificación para su imagen de máquina virtual
El siguiente paso para preparar su imagen de máquina virtual para Azure Marketplace es certificarla.

Este proceso incluye ejecutar una herramienta de certificación especial, cargar los resultados de la comprobación en el contenedor de Azure donde residen sus VHD, agregar una oferta, definir su SKU y enviar su imagen de máquina virtual para que la certifiquen.

### <a name="51-download-and-run-the-certification-test-tool-for-azure-certified"></a>5.1 Descargar e instalar “Certification Test Tool for Azure Certified”
La herramienta de certificación se ejecuta en una máquina virtual en ejecución, aprovisionada desde su imagen de máquina virtual de usuario, para comprobar que la imagen de máquina virtual es compatible con Microsoft Azure. Comprueba que se han cumplido las instrucciones y los requisitos para preparar su VHD. El resultado de la herramienta es un informe de compatibilidad que se debe cargar en el Portal de publicación al solicitar la certificación.

La herramienta de certificación se puede usar con máquinas virtuales basadas tanto en Windows como en Linux. Se conecta con las máquinas virtuales basadas en Windows a través de PowerShell y, con las máquinas virtuales basadas en Linux, mediante SSH.Net:

1. En primer lugar, descargue la herramienta de certificación en el [sitio de descargas de Microsoft][link-msft-download].
2. Abra la herramienta de certificación y haga clic en el botón **Iniciar nueva prueba** .
3. En la pantalla **Información de la prueba** , escriba un nombre para la serie de pruebas.
4. Especifique si la máquina virtual está basada en Linux o Windows. En función de lo que especifique, seleccione las demás opciones.

### <a name="connect-the-certification-tool-to-a-linux-vm-image"></a>**Conexión de la herramienta de certificación a una imagen de máquina virtual basada en Linux**
1. Seleccione el modo de autenticación con SSH: contraseña o archivo de clave.
2. Si usa autenticación por contraseña, escriba el Sistema de nombres de dominio (DNS), el nombre de usuario y la contraseña.
3. Si usa autenticación con archivo de clave, escriba el nombre DNS, el nombre de usuario y la ubicación de la clave privada.

   ![Autenticación de contraseña de imagen de máquina virtual de Linux][img-cert-vm-pswd-lnx]

   ![Autenticación de archivo de clave de máquina virtual de Linux][img-cert-vm-key-lnx]

### <a name="connect-the-certification-tool-to-a-windows-based-vm-image"></a>**Conexión de la herramienta de certificación a una imagen de máquina virtual basada en Windows**
1. Escriba el nombre DNS completo de la máquina virtual (por ejemplo, MyVMName.ClOudapp.net).
2. Escriba el nombre de usuario y la contraseña.

   ![Autenticación de contraseña de imagen de máquina virtual de Windows][img-cert-vm-pswd-win]

Después de seleccionar las opciones correctas para la imagen de máquina virtual basada en Linux o Windows, elija **Probar conexión** para comprobar que SSH.Net o PowerShell tienen una conexión válida con fines de prueba. Una vez establecida la conexión, seleccione **Siguiente** para iniciar la prueba.

Cuando termine la prueba, recibirá los resultados (sin errores/error/con advertencia) para cada elemento de la prueba.

![Casos de prueba para la imagen de máquina virtual de Linux][img-cert-vm-test-lnx]

![Casos de prueba para la imagen de máquina virtual de Windows][img-cert-vm-test-win]

Si se produce un error en alguna de las pruebas, la imagen no se certificará. Si ocurre esto, revise los requisitos y realice los cambios necesarios.

Después de la prueba automatizada, se le pedirá que proporcione información adicional en la imagen de máquina virtual a través de una pantalla de cuestionario.  Complete las preguntas y seleccione **Siguiente**

![Cuestionario de la herramienta de certificación][img-cert-vm-questionnaire]

![Cuestionario de la herramienta de certificación][img-cert-vm-questionnaire-2]

Una vez haya completado el cuestionario, puede proporcionar información adicional, como información de acceso SSH para la imagen de máquina virtual de Linux y una explicación sobre las evaluaciones que han dado errores. Puede descargar los resultados de la prueba y los archivos de registro de las pruebas ejecutadas, además de sus respuestas al cuestionario. Guarde los resultados en el mismo contenedor que los VHD.

![Guardar resultados de la prueba de certificación][img-cert-vm-results]

### <a name="52-get-the-shared-access-signature-uri-for-your-vm-images"></a>5.2 Obtener el identificador URI de firma de acceso compartido para sus imágenes de máquina virtual
Durante el proceso de publicación, tiene que especificar los identificadores uniformes de recursos (URI) que llevan a cada uno de los VHD que creó para su SKU. Microsoft necesita tener acceso a estos VHD durante el proceso de certificación. Por lo tanto, debe crear un URI de firma de acceso compartido para cada VHD. Este es el URI que debe proporcionar en la pestaña **Imágenes** del portal de publicación.

El URI de firma de acceso compartido creado debe cumplir los siguientes requisitos:

* Para generar los URI de firma de acceso compartido para sus VHD, son suficientes los permisos de lista y lectura. No proporcione acceso de escritura o eliminación.
* La duración del acceso debe ser un mínimo de tres (3) semanas a partir de la creación del URI de firma de acceso compartido.
* Para proteger la hora UTC, seleccione el día anterior a la fecha actual. Por ejemplo, si la fecha actual es el 6 de octubre de 2014, seleccione 5/10/2014.

La dirección URL de SAS puede generarse de varias maneras a fin de compartir el disco duro virtual para Azure Marketplace.
Estos son las 3 herramientas recomendadas:

1.  Explorador de Azure Storage
2.  Explorador de almacenamiento de Microsoft
3.  CLI de Azure

**Explorador de Azure Storage (recomendado para los usuarios de Windows)**

A continuación se enumeran los pasos para generar la dirección URL de SAS mediante el Explorador de Azure Storage.

1. Descargue [Explorador de Azure Storage 6 versión preliminar 3](https://azurestorageexplorer.codeplex.com/) desde CodePlex. Vaya a [Azure Storage Explorer 6 Preview](https://azurestorageexplorer.codeplex.com/) (Versión preliminar del explorador de Azure Storage 6) y haga clic en **"Downloads"** (Descargas).

    ![dibujo](media/marketplace-publishing-vm-image-creation/img5.2_01.png)

2. Descargue [AzureStorageExplorer6Preview3.zip](https://azurestorageexplorer.codeplex.com/downloads/get/891668) e instálelo después de la descompresión.

    ![dibujo](media/marketplace-publishing-vm-image-creation/img5.2_02.png)

3. Una vez instalado, abra la aplicación.
4. Haga clic en **Agregar cuenta**.

    ![dibujo](media/marketplace-publishing-vm-image-creation/img5.2_03.png)

5. Especifique el nombre de cuenta de almacenamiento, la clave de la cuenta de almacenamiento y el dominio de los puntos de conexión de almacenamiento. Esta es la cuenta de almacenamiento de su suscripción de Azure en la que guardó el disco duro virtual en Azure Portal.

    ![dibujo](media/marketplace-publishing-vm-image-creation/img5.2_04.png)

6. Una vez que el Explorador de Azure Storage se conecte a su cuenta de almacenamiento específica, empezará a mostrar todos los contenedores de la cuenta de almacenamiento. Seleccione el contenedor en el que copió el archivo VHD del disco del sistema operativo (también los discos de datos si son aplicables en su caso).

    ![dibujo](media/marketplace-publishing-vm-image-creation/img5.2_05.png)

7. Después de seleccionar el contenedor de blobs, se iniciará la aplicación Explorador de almacenamiento de Azure con los archivos dentro del contenedor. Seleccione el archivo de imagen (.vhd) que tiene que enviarse.

    ![dibujo](media/marketplace-publishing-vm-image-creation/img5.2_06.png)

8.  Después de seleccionar el archivo (.vhd) en el contenedor, haga clic en la pestaña **Seguridad** .

    ![dibujo](media/marketplace-publishing-vm-image-creation/img5.2_07.png)

9.  En el cuadro de diálogo **Seguridad del contenedor de blobs**, deje los valores predeterminados de la pestaña **Nivel de acceso** y, después, haga clic en la pestaña **Firmas de acceso compartido**.

    ![dibujo](media/marketplace-publishing-vm-image-creation/img5.2_08.png)

10. Siga estos pasos a fin de generar un URI de firma de acceso compartido para la imagen .vhd:

    ![dibujo](media/marketplace-publishing-vm-image-creation/img5.2_09.png)

    a. **Acceso permitido desde**: para proteger la hora UTC, seleccione el día anterior a la fecha actual. Por ejemplo, si la fecha actual es el 6 de octubre de 2014, seleccione 5/10/2014.

    b. **Acceso permitido hasta**: seleccione una fecha que sea al menos 3 semanas después de la fecha de **Acceso permitido desde**.

    c. **Acciones permitidas**: seleccione los permisos **Lista** y **Lectura**.

    d. Si seleccionó correctamente el archivo .vhd, en **Nombre de blob para acceder** aparece el archivo con la extensión .vhd

    e. Haga clic en **Generar firma**.

    f. En **URI de firma de acceso compartido generado de este contenedor**, compruebe lo siguiente tal como se ha resaltado anteriormente:

       - Compruebe que el nombre de archivo de imagen y **".vhd"** están en el URI.
       - Asegúrese de que **"=rl"** aparece al final de la firma. Esto demuestra que el acceso de lectura y lista se ha proporcionado correctamente.
       - En el centro de la firma, asegúrese de que aparece **"sr=c"**. Esto demuestra que tiene acceso de nivel de contenedor.

11. Para asegurarse de que el URI de firma de acceso compartido generado funciona correctamente, haga clic en **Probar en el explorador**. El proceso de descarga debería comenzar.

12. Copie el URI de firma de acceso compartido. Este es el URI que debe pegar en el portal de publicación.

13. Repita los pasos de 6 a 10 para cada disco duro virtual en la SKU.

**Explorador de Microsoft Azure Storage (Windows/MAC/Linux)**

A continuación se muestran los pasos para generar la dirección URL de SAS mediante el Explorador de Microsoft Azure Storage.

1.  Descargue el Explorador de Microsoft Azure Storage desde el sitio web [http://storageexplorer.com/](http://storageexplorer.com/). Vaya a [Microsoft Azure Storage Explorer](http://storageexplorer.com/releasenotes.html) (Explorador de Microsoft Azure Storage) y haga clic en **"Download for Windows"** (Descargar para Windows).

    ![dibujo](media/marketplace-publishing-vm-image-creation/img5.2_10.png)

2.  Una vez instalado, abra la aplicación.

3.  Haga clic en **Agregar cuenta**.

4.  Configure el Explorador de Microsoft Azure Storage con su suscripción iniciando sesión en su cuenta.

    ![dibujo](media/marketplace-publishing-vm-image-creation/img5.2_11.png)

5.  Vaya a la cuenta de almacenamiento y seleccione el contenedor.

6.  Seleccione **"Get Share Access Signature"** (Obtener la firma de acceso compartido) haciendo clic derecho en el **contenedor**.

    ![dibujo](media/marketplace-publishing-vm-image-creation/img5.2_12.png)

7.  Actualice la hora de inicio, la hora de expiración y los permisos según se especifica a continuación:

    ![dibujo](media/marketplace-publishing-vm-image-creation/img5.2_13.png)

    a.  **Hora de inicio**: para proteger la hora UTC, seleccione el día anterior a la fecha actual. Por ejemplo, si la fecha actual es el 6 de octubre de 2014, seleccione 5/10/2014.

    b.  **Hora de caducidad:** seleccione una fecha que sea al menos 3 semanas después de la **Hora de inicio**.

    c.  **Permisos**: seleccione los permisos **Lista** y **Lectura**.

8.  Copie el URI de la firma de acceso compartido del contenedor.

    ![dibujo](media/marketplace-publishing-vm-image-creation/img5.2_14.png)

    La dirección URL de SAS generada es de nivel de contenedor, y ahora tenemos que agregarle el nombre del disco duro virtual.

    Formato de la dirección URL de SAS de nivel de contenedor:`https://testrg009.blob.core.windows.net/vhds?st=2016-04-22T23%3A05%3A00Z&se=2016-04-30T23%3A05%3A00Z&sp=rl&sv=2015-04-05&sr=c&sig=J3twCQZv4L4EurvugRW2klE2l2EFB9XyM6K9FkuVB58%3D`

    Inserte el nombre del disco duro virtual después del nombre del contenedor en la dirección URL de SAS como se indica a continuación:`https://testrg009.blob.core.windows.net/vhds/<VHD NAME>?st=2016-04-22T23%3A05%3A00Z&se=2016-04-30T23%3A05%3A00Z&sp=rl&sv=2015-04-05&sr=c&sig=J3twCQZv4L4EurvugRW2klE2l2EFB9XyM6K9FkuVB58%3D`

    Ejemplo:

    ![dibujo](media/marketplace-publishing-vm-image-creation/img5.2_15.png)

    TestRGVM201631920152.vhd es el nombre de disco duro virtual, así que la dirección URL de SAS del disco duro virtual será `https://testrg009.blob.core.windows.net/vhds/TestRGVM201631920152.vhd?st=2016-04-22T23%3A05%3A00Z&se=2016-04-30T23%3A05%3A00Z&sp=rl&sv=2015-04-05&sr=c&sig=J3twCQZv4L4EurvugRW2klE2l2EFB9XyM6K9FkuVB58%3D`

    - Compruebe que el nombre de archivo de imagen y **".vhd"** están en el URI.
    - Asegúrese de que **"sp=rl"** aparece en el medio de la firma. Esto demuestra que el acceso de lectura y lista se ha proporcionado correctamente.
    - En el centro de la firma, asegúrese de que aparece **"sr=c"**. Esto demuestra que tiene acceso de nivel de contenedor.

9.  Para asegurarse de que el URI de firma de acceso compartido generado funciona correctamente, pruébelo en el explorador. El proceso de descarga debería comenzar.

10. Copie el URI de firma de acceso compartido. Este es el URI que debe pegar en el portal de publicación.

11. Repita estos pasos para cada VHD de la SKU.

**CLI de Azure (recomendado para la integración continua y distinta de Windows)**

A continuación se enumeran los pasos para generar la dirección URL de SAS mediante la CLI de Azure.

1.  Descargue la CLI de Microsoft Azure desde [aquí](https://azure.microsoft.com/en-in/documentation/articles/xplat-cli-install/). También puede encontrar vínculos diferentes para **[Windows](http://aka.ms/webpi-azure-cli)** y **[MAC OS](http://aka.ms/mac-azure-cli)**.

2.  Una vez completada la descarga, instálela.

3.  Cree un archivo de PowerShell con el código siguiente y guárdelo en local.

          $conn="DefaultEndpointsProtocol=https;AccountName=<StorageAccountName>;AccountKey=<Storage Account Key>"
          azure storage container list vhds -c $conn
          azure storage container sas create vhds rl <Permission End Date> -c $conn --start <Permission Start Date>  

    Actualice los siguientes parámetros especificados anteriormente.

    a. **`<StorageAccountName>`**: indique el nombre de la cuenta de almacenamiento

    b. **`<Storage Account Key>`**: indique la clave de la cuenta de almacenamiento

    c. **`<Permission Start Date>`**: para proteger la hora UTC, seleccione el día anterior a la fecha actual. Por ejemplo, si la fecha actual es el 26 de octubre de 2016, el valor debe ser 10/25/2016.

    d. **`<Permission End Date>`**: seleccione una fecha que sea al menos 3 semanas después de la **Fecha de inicio**. Así, el valor debería ser **11/02/2016**.

    A continuación se muestra el código de ejemplo después de actualizar los parámetros adecuados.

          $conn="DefaultEndpointsProtocol=https;AccountName=st20151;AccountKey=TIQE5QWMKHpT5q2VnF1bb+NUV7NVMY2xmzVx1rdgIVsw7h0pcI5nMM6+DVFO65i4bQevx21dmrflA91r0Vh2Yw=="
          azure storage container list vhds -c $conn
          azure storage container sas create vhds rl 11/02/2016 -c $conn --start 10/25/2016  

4.  Abra el editor de Powershell con el modo de "Ejecutar como administrador" y abra el archivo del paso 3.

5.  Ejecute el script para conseguir la dirección URL de SAS para el acceso de nivel de contenedor.

    A continuación se muestra el resultado de la firma de SAS; copie la parte resaltada en el Bloc de notas.

    ![dibujo](media/marketplace-publishing-vm-image-creation/img5.2_16.png)

6.  Ahora tendrá la dirección URL de SAS de nivel de contenedor y tendrá que agregar el nombre del disco duro virtual en ella.

    N.º de URL de SAS de nivel de contenedor

    `https://st20151.blob.core.windows.net/vhds?st=2016-10-25T07%3A00%3A00Z&se=2016-11-02T07%3A00%3A00Z&sp=rl&sv=2015-12-11&sr=c&sig=wnEw9RfVKeSmVgqDfsDvC9IHhis4x0fc9Hu%2FW4yvBxk%3D`

7.  Inserte el nombre del disco duro virtual después del nombre del contenedor en la dirección URL de SAS como se indica a continuación:`https://st20151.blob.core.windows.net/vhds/<VHDName>?st=2016-10-25T07%3A00%3A00Z&se=2016-11-02T07%3A00%3A00Z&sp=rl&sv=2015-12-11&sr=c&sig=wnEw9RfVKeSmVgqDfsDvC9IHhis4x0fc9Hu%2FW4yvBxk%3D`

    Ejemplo:

    TestRGVM201631920152.vhd es el nombre de disco duro virtual, así que la dirección URL de SAS del disco duro virtual será

    `https://st20151.blob.core.windows.net/vhds/ TestRGVM201631920152.vhd?st=2016-10-25T07%3A00%3A00Z&se=2016-11-02T07%3A00%3A00Z&sp=rl&sv=2015-12-11&sr=c&sig=wnEw9RfVKeSmVgqDfsDvC9IHhis4x0fc9Hu%2FW4yvBxk%3D`

    - Compruebe que el nombre de archivo de imagen y ".vhd" están en el URI.
    -   Asegúrese de que "sp=rl" aparece en el medio de la firma. Esto demuestra que el acceso de lectura y lista se ha proporcionado correctamente.
    -   Asegúrese de que "sr=c" aparece en el medio de la firma. Esto demuestra que tiene acceso de nivel de contenedor.

8.  Para asegurarse de que el URI de firma de acceso compartido generado funciona correctamente, pruébelo en el explorador. El proceso de descarga debería comenzar.

9.  Copie el URI de firma de acceso compartido. Este es el URI que debe pegar en el portal de publicación.

10. Repita estos pasos para cada VHD de la SKU.


### <a name="53-provide-information-about-the-vm-image-and-request-certification-in-the-publishing-portal"></a>5.3 Proporcionar información acerca de la imagen de máquina virtual y solicitar su certificación en el portal de publicación
Una vez que haya creado su oferta y SKU, debe proporcionar los datos de la imagen asociados con esa SKU.

1. Vaya al [Portal de publicación][link-pubportal] e inicie sesión con su cuenta de vendedor.
2. Seleccione la pestaña **Imágenes de VM** .
3. El identificador que aparece en la parte superior de la página es realmente el identificador de la oferta, no de la SKU.
4. Rellene las propiedades de la sección **SKUs** .
5. En **Familia del sistema operativo**, haga clic en el tipo de sistema operativo asociado con el VHD del sistema operativo.
6. En el cuadro **Sistema operativo** , describa el sistema operativo. Considere un formato como familia de sistemas operativos, tipo, versión y actualizaciones. Por ejemplo, "Windows Server Datacenter 2014 R2".
7. Seleccione hasta seis tamaños de máquina virtual recomendados. Estas recomendaciones se muestran al cliente en la hoja del plan de tarifa del Portal de Azure cuando este decide comprar e implementar su imagen. **Estas son solo recomendaciones. El cliente puede seleccionar cualquier tamaño de máquina virtual que admita los discos especificados en su imagen.**
8. Especifique la versión. El campo de versión encapsula una versión semántica para identificar el producto y sus actualizaciones:
   * Las versiones deben ser del tipo X.Y.Z, donde X, Y y Z son números enteros.
   * Imágenes de SKU diferentes pueden tener distintas versiones principales y secundarias.
   * Las versiones dentro de una SKU solo deberían ser los cambios incrementales que aumenten la versión de revisión (Z de X.Y.Z).
9. En el cuadro **Dirección URL del VHD del sistema operativo** , escriba el URI de firma de acceso compartido creado para el VHD del sistema operativo.
10. Si hay discos de datos asociados con esta SKU, seleccione el número de unidad lógica (LUN) donde desea que se monte este disco de datos durante la implementación.
11. En el cuadro **Dirección URL del VHD de LUN X** , escriba el URI de firma de acceso compartido creado para el primer VHD de datos.

    ![dibujo](media/marketplace-publishing-vm-image-creation/vm-image-pubportal-skus-3.png)


## <a name="common-sas-url-issues--fixes"></a>Problemas y soluciones comunes de la dirección URL de SAS

|Problema|Mensaje de error|Solución|Vínculo a documentación|
|---|---|---|---|
|Error al copiar imágenes: "?" no se encuentra en la dirección URL de SAS|Error al copiar imágenes No se puede descargar el blob mediante la dirección URL de SAS indicada.|Actualización de la dirección URL de SAS mediante las herramientas recomendadas|[https://azure.microsoft.com/es-es/documentation/articles/storage-dotnet-shared-access-signature-part-1/](https://azure.microsoft.com/en-us/documentation/articles/storage-dotnet-shared-access-signature-part-1/)|
|Error al copiar imágenes: los parámetros "st" y "se" no están en la dirección URL de SAS|Error al copiar imágenes No se puede descargar el blob mediante la dirección URL de SAS indicada.|Actualización de la dirección URL de SAS incluyendo las fechas de inicio y finalización|[https://azure.microsoft.com/es-es/documentation/articles/storage-dotnet-shared-access-signature-part-1/](https://azure.microsoft.com/en-us/documentation/articles/storage-dotnet-shared-access-signature-part-1/)|
|Error al copiar imágenes: "sp=rl" no está en la dirección URL de SAS|Error al copiar imágenes No se puede descargar el blob mediante la dirección URL de SAS indicada.|Actualización de la dirección Url de SAS con los permisos establecidos como "Lectura" y "Lista"|[https://azure.microsoft.com/es-es/documentation/articles/storage-dotnet-shared-access-signature-part-1/](https://azure.microsoft.com/en-us/documentation/articles/storage-dotnet-shared-access-signature-part-1/)|
|Error al copiar imágenes: la dirección URL de SAS tiene espacios en blanco en el nombre de disco duro virtual.|Error al copiar imágenes No se puede descargar el blob mediante la dirección URL de SAS indicada.|Actualización de la dirección URL de SAS sin espacios en blanco|[https://azure.microsoft.com/es-es/documentation/articles/storage-dotnet-shared-access-signature-part-1/](https://azure.microsoft.com/en-us/documentation/articles/storage-dotnet-shared-access-signature-part-1/)|
|Error al copiar imágenes: error de autorización de dirección Url de SAS|Error al copiar imágenes No se puede descargar blob debido a un error de autorización.|Regeneración de la dirección URL de SAS|[https://azure.microsoft.com/es-es/documentation/articles/storage-dotnet-shared-access-signature-part-1/](https://azure.microsoft.com/en-us/documentation/articles/storage-dotnet-shared-access-signature-part-1/)|


## <a name="next-step"></a>Paso siguiente
Cuando termine con los detalles de SKU, podrá continuar con la [guía de contenido de marketing de Azure Marketplace][link-pushstaging]. En este paso del proceso de publicación, proporciona el contenido de marketing, precios y demás información necesaria antes del **Paso 3: Prueba de la oferta de máquina virtual en ensayo**, donde prueba varios escenarios de caso de uso antes de implementar la oferta en Azure Marketplace para la compra y visibilidad pública.  

## <a name="see-also"></a>Consulte también
* [Introducción: Publicación de una oferta en Azure Marketplace](marketplace-publishing-getting-started.md)

[img-acom-1]:media/marketplace-publishing-vm-image-creation/vm-image-acom-datacenter.png
[img-portal-vm-size]:media/marketplace-publishing-vm-image-creation/vm-image-portal-size.png
[img-portal-vm-create]:media/marketplace-publishing-vm-image-creation/vm-image-portal-create-vm.png
[img-portal-vm-location]:media/marketplace-publishing-vm-image-creation/vm-image-portal-location.png
[img-portal-vm-rdp]:media/marketplace-publishing-vm-image-creation/vm-image-portal-rdp.png
[img-azstg-add]:media/marketplace-publishing-vm-image-creation/vm-image-storage-add.png
[img-manage-vm-new]:media/marketplace-publishing-vm-image-creation/vm-image-manage-new.png
[img-manage-vm-select]:media/marketplace-publishing-vm-image-creation/vm-image-manage-select.png
[img-cert-vm-key-lnx]:media/marketplace-publishing-vm-image-creation/vm-image-certification-keyfile-linux.png
[img-cert-vm-pswd-lnx]:media/marketplace-publishing-vm-image-creation/vm-image-certification-password-linux.png
[img-cert-vm-pswd-win]:media/marketplace-publishing-vm-image-creation/vm-image-certification-password-win.png
[img-cert-vm-test-lnx]:media/marketplace-publishing-vm-image-creation/vm-image-certification-test-sample-linux.png
[img-cert-vm-test-win]:media/marketplace-publishing-vm-image-creation/vm-image-certification-test-sample-win.png
[img-cert-vm-results]:media/marketplace-publishing-vm-image-creation/vm-image-certification-results.png
[img-cert-vm-questionnaire]:media/marketplace-publishing-vm-image-creation/vm-image-certification-questionnaire.png
[img-cert-vm-questionnaire-2]:media/marketplace-publishing-vm-image-creation/vm-image-certification-questionnaire-2.png
[img-pubportal-vm-skus]:media/marketplace-publishing-vm-image-creation/vm-image-pubportal-skus.png
[img-pubportal-vm-skus-2]:media/marketplace-publishing-vm-image-creation/vm-image-pubportal-skus-2.png

[link-pushstaging]:marketplace-publishing-push-to-staging.md
[link-github-waagent]:https://github.com/Azure/WALinuxAgent
[link-azure-codeplex]:https://azurestorageexplorer.codeplex.com/
[link-azure-2]:../storage/blobs/storage-dotnet-shared-access-signature-part-2.md
[link-azure-1]:../storage/common/storage-dotnet-shared-access-signature-part-1.md
[link-msft-download]:http://www.microsoft.com/download/details.aspx?id=44299
[link-technet-3]:https://technet.microsoft.com/library/hh846766.aspx
[link-technet-2]:https://msdn.microsoft.com/library/dn495261.aspx
[link-azure-portal]:https://portal.azure.com
[link-pubportal]:https://publish.windowsazure.com
[link-sql-2014-ent]:http://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2014enterprisewindowsserver2012r2/
[link-sql-2014-std]:http://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2014standardwindowsserver2012r2/
[link-sql-2014-web]:http://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2014webwindowsserver2012r2/
[link-sql-2012-ent]:http://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2012sp2enterprisewindowsserver2012/
[link-sql-2012-std]:http://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2012sp2standardwindowsserver2012/
[link-sql-2012-web]:http://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2012sp2webwindowsserver2012/
[link-datactr-2012-r2]:http://azure.microsoft.com/marketplace/partners/microsoft/windowsserver2012r2datacenter/
[link-datactr-2012]:http://azure.microsoft.com/marketplace/partners/microsoft/windowsserver2012datacenter/
[link-datactr-2008-r2]:http://azure.microsoft.com/marketplace/partners/microsoft/windowsserver2008r2sp1/
[link-acct-creation]:marketplace-publishing-accounts-creation-registration.md
[link-technet-1]:https://technet.microsoft.com/library/hh848454.aspx
[link-azure-vm-2]:./virtual-machines-linux-agent-user-guide/
[link-openssl]:https://www.openssl.org/
[link-intsvc]:http://www.microsoft.com/download/details.aspx?id=41554
[link-python]:https://www.python.org/
