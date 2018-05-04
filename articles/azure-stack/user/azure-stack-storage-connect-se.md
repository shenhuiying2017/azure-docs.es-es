---
title: Conexión del Explorador de Storage a una suscripción de Azure Stack o una cuenta de almacenamiento | Microsoft Docs
description: Aprenda a conectar el Explorador de Storage a una suscripción de Azure Stack
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 04/20/2018
ms.author: mattbriggs
ms.reviewer: xiaofmao
ms.openlocfilehash: 8b670ec7040aab7eca26d411c9e31a934052be19
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2018
---
# <a name="connect-storage-explorer-to-an-azure-stack-subscription-or-a-storage-account"></a>Conexión del Explorador de Storage a una suscripción de Azure Stack o una cuenta de almacenamiento

*Se aplica a: sistemas integrados de Azure Stack y Kit de desarrollo de Azure Stack*

El Explorador de Azure Storage es una aplicación independiente que permite trabajar fácilmente con datos de almacenamiento de Azure Stack en Windows, macOS y Linux. Hay varias herramientas disponibles para mover datos hacia el almacenamiento de Azure Stack Storage y desde este. Para más información, consulte [Herramientas de transferencia de datos de Azure Stack Storage](azure-stack-storage-transfer.md).

En este artículo, aprenderá a conectarse a sus suscripciones de Azure Stack y cuentas de almacenamiento mediante el Explorador de Storage. 

Si no ha instalado todavía el Explorador de Storage, [descárguelo](http://www.storageexplorer.com/) y hágalo.

Después de conectarse a una suscripción de Azure Stack o una cuenta de almacenamiento, puede usar los [artículos sobre el Explorador de Azure Storage](../../vs-azure-tools-storage-manage-with-storage-explorer.md) para trabajar con los datos de Azure Stack. 

## <a name="prepare-for-connecting-to-azure-stack"></a>Prepárese para conectarse a Azure Stack

Necesita acceso directo a Azure Stack o una conexión VPN para que el Explorador de Storage pueda obtener acceso a la suscripción de Azure Stack. Para más información sobre cómo configurar una conexión VPN a Azure Stack, consulte [Conexión a Azure Stack con VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn).

Para el Kit de desarrollo de Azure Stack, debe exportar el certificado raíz de la entidad de certificación de Azure Stack.

### <a name="export-and-then-import-the-azure-stack-certificate"></a>Exportación y posterior importación del certificado de Azure Stack

1. Abra `mmc.exe` en una máquina host de Azure Stack o una máquina local con una conexión VPN a Azure Stack. 

2. En **Archivo**, seleccione **Agregar o quitar complemento** y luego agregue **certificados** para administrar la **Mi cuenta de usuario**.

3. En **Console Root\Certificated (Local Computer)\Trusted Root Certification Authorities\Certificates** busque **AzureStackSelfSignedRootCert**.

    ![Carga del certificado raíz de Azure Stack a través de mmc.exe](./media/azure-stack-storage-connect-se/add-certificate-azure-stack.png)

4. Haga clic con el botón derecho en el certificado, seleccione **Todas las tareas** > **Exportar** y luego siga las instrucciones para exportar el certificado con **X.509 (.CER) codificado en base 64**.  

    Se usará el certificado exportado en el paso siguiente.

5. Inicie el Explorador de Storage y, si ve el cuadro de diálogo **Connect to Azure Storage** (Conectar a Azure Storage), cancélelo.

6. En el menú **Editar**, seleccione **Certificados SSL** y, luego, seleccione **Importar certificados**. Use el cuadro de diálogo del selector de archivos para buscar y abrir el certificado que exportó en el paso anterior.

    Después de la importación, se le pedirá que reinicie el Explorador de Storage.

    ![Importación del certificado en el Explorador de Storage](./media/azure-stack-storage-connect-se/import-azure-stack-cert-storage-explorer.png)

7. Después de que se reinicie el Explorador de Storage, seleccione el menú **Edit** (Editar) y luego asegúrese de que esté seleccionada la opción **Target Azure Stack** (Azure Stack de destino). Si no está seleccionada, selecciónela y luego reinicie el Explorador de Storage para que el cambio surta efecto. Esta configuración es necesaria para obtener compatibilidad con el entorno de Azure Stack.

    ![Comprobación de que Azure Stack de destino está activada](./media/azure-stack-storage-connect-se/target-azure-stack.png)

## <a name="connect-to-an-azure-stack-subscription"></a>Conexión a una suscripción de Azure Stack

Ahora ya está listo para conectar el Explorador de Storage a una suscripción de Azure Stack.

1. En el panel izquierdo del Explorador de Storage, seleccione **Administrar cuentas**.  
    Se muestran todas las suscripciones de Microsoft en las que inició sesión.

2. Para conectarse a la suscripción de Azure Stack, seleccione **Agregar una cuenta**.

    ![Adición de una cuenta de Azure Stack](./media/azure-stack-storage-connect-se/add-azure-stack-account.png)

3. En el cuadro de diálogo Conectar a Azure Storage, en el **entorno de Azure**, seleccione **Azure** o **Azure China**, que depende de la cuenta de Azure Stack utilizada y, a continuación, seleccione **Iniciar sesión**. Para iniciar sesión con la cuenta de Azure Stack que está asociada a una suscripción de Azure Stack activa por lo menos.

    ![Conectar a Azure Storage](./media/azure-stack-storage-connect-se/azure-stack-connect-to-storage.png)

4. Después de iniciar sesión correctamente con una cuenta de Azure Stack, el panel izquierdo se llena con las suscripciones de Azure Stack asociadas a dicha cuenta. Seleccione las suscripciones de Azure Stack con las que quiere trabajar y luego seleccione Aplicar (al activar o desactivar la casilla Todas las suscripciones, se alterna entre seleccionar todas o ninguna de las suscripciones de Azure Stack que aparecen).

    ![Selección de las suscripciones de Azure Stack después de rellenar el cuadro de diálogo Custom Cloud Environment (Entorno personalizado en la nube)](./media/azure-stack-storage-connect-se/select-accounts-azure-stack.png)  

    El panel izquierdo muestra ahora las cuentas de almacenamiento asociadas a las suscripciones de Azure Stack seleccionadas.

    ![Lista de cuentas de almacenamiento, incluidas las cuentas de suscripción de Azure Stack](./media/azure-stack-storage-connect-se/azure-stack-storage-account-list.png)

## <a name="connect-to-an-azure-stack-storage-account"></a>Conexión a una cuenta de almacenamiento de Azure Stack

También puede conectarse a una cuenta de almacenamiento de Azure Stack mediante el par de claves y el nombre de la cuenta de almacenamiento.

1.  En el panel izquierdo del Explorador de Storage, seleccione Administrar cuentas. Se muestran todas las cuentas de Microsoft en las que inició sesión.

    ![Agregar una cuenta](./media/azure-stack-storage-connect-se/azure-stack-sub-add-an-account.png)

2.  Para conectarse a la suscripción de Azure Stack, seleccione **Agregar una cuenta**.
 
    ![Agregar una cuenta](./media/azure-stack-storage-connect-se/azure-stack-use-a-storage-and-key.png)

3.  En el cuadro de diálogo Conectar a Azure Storage, seleccione **Use a storage account name and key** (Usar un nombre y una clave de cuenta de almacenamiento).

4. Escriba el nombre de su cuenta en **Nombre de cuenta**, pegue la clave de cuenta en el cuadro de texto **Clave de cuenta**, seleccione **Otros (escribir a continuación)** en **Dominio de puntos de conexión de Storage** y escriba el punto de conexión de Azure Stack.  

    Un punto de conexión de Azure Stack incluye dos partes: el nombre de una región y el dominio de Azure Stack. En el Kit de desarrollo de Azure Stack, el punto de conexión predeterminado es **local.azurestack.external**. Si no está seguro de cuál es su punto de conexión, póngase en contacto con el administrador de la nube.

    ![Asociar nombre y clave](./media/azure-stack-storage-connect-se/azure-stack-attach-name-and-key.png)

5.  Seleccione **Conectar**.
6.  Cuando la cuenta de almacenamiento se haya asociado correctamente, se muestra con (**External, Other**) (Externa, Otros) anexado a su nombre.

    ![VMWINDISK](./media/azure-stack-storage-connect-se/azure-stack-vmwindisk.png)

## <a name="next-steps"></a>Pasos siguientes
* [Introducción al Explorador de Storage](../../vs-azure-tools-storage-manage-with-storage-explorer.md)
* [Azure Stack Storage: Diferencias y consideraciones](azure-stack-acs-differences.md)
* Para más información sobre Azure Storage, consulte [Introducción a Microsoft Azure Storage](../../storage/common/storage-introduction.md).
