---
title: Conexión del Explorador de Storage a una suscripción de Azure Stack
description: Aprenda a conectar el Explorador de Storage a una suscripción de Azure Stack
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/28/2018
ms.author: xiaofmao
ms.openlocfilehash: 9cbfb7dbf5272b3213d3791c0a7b6fb57109798f
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/18/2018
---
# <a name="connect-storage-explorer-to-an-azure-stack-subscription"></a>Conexión del Explorador de Storage a una suscripción de Azure Stack

*Se aplica a: sistemas integrados de Azure Stack y Kit de desarrollo de Azure Stack*

El Explorador de Azure Storage es una aplicación independiente que permite trabajar fácilmente con datos de almacenamiento de Azure Stack en Windows, macOS y Linux. Hay varias herramientas disponibles para mover datos hacia el almacenamiento de Azure Stack Storage y desde este. Para más información, consulte [Herramientas de transferencia de datos de Azure Stack Storage](azure-stack-storage-transfer.md).

En este artículo, aprenderá a conectarse a sus cuentas de Azure Stack Storage mediante el Explorador de Storage. 

Si no ha instalado todavía el Explorador de Storage, [descárguelo](http://www.storageexplorer.com/) y hágalo.

Después de conectarse a su suscripción de Azure Stack, puede usar los [artículos sobre el Explorador de Azure Storage](../../vs-azure-tools-storage-manage-with-storage-explorer.md) para trabajar con los datos de Azure Stack. 

## <a name="prepare-an-azure-stack-subscription"></a>Preparación de una suscripción de Azure Stack

Necesita acceder al escritorio de la máquina del host de Azure Stack o una conexión VPN para que el Explorador de Storage pueda acceder de forma remota a la suscripción de Azure Stack. Para más información sobre cómo configurar una conexión VPN a Azure Stack, consulte [Conexión a Azure Stack con VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn).

Para el Kit de desarrollo de Azure Stack, debe exportar el certificado raíz de la entidad de certificación de Azure Stack.

## <a name="export-and-import-azure-stack-certificates"></a>Exportación e importación de certificados de Azure Stack

1. Abra `mmc.exe` en una máquina host de Azure Stack o una máquina local con una conexión VPN a Azure Stack. 

2. En **Archivo**, seleccione **Agregar o quitar complemento** y luego agregue **certificados** para administrar la **Mi cuenta de usuario**.

3. En **Console Root\Certificated (Local Computer)\Trusted Root Certification Authorities\Certificates**, busque **AzureStackSelfSignedRootCert**.

    ![Carga del certificado raíz de Azure Stack a través de mmc.exe][25]

4. Haga clic con el botón derecho en el certificado, seleccione **Todas las tareas** > **Exportar** y luego siga las instrucciones para exportar el certificado con **X.509 (.CER) codificado en base 64**.  

    Se usará el certificado exportado en el paso siguiente.
5. Inicie el Explorador de Storage y, si ve el cuadro de diálogo **Connect to Azure Storage** (Conectar a Azure Storage), cancélelo.

6. En el menú **Editar**, seleccione **Certificados SSL** y, luego, haga clic en **Importar certificados**. Use el cuadro de diálogo del selector de archivos para buscar y abrir el certificado que exportó en el paso anterior.

    Después de la importación, se le pedirá que reinicie el Explorador de Storage.

    ![Importación del certificado en el Explorador de Storage][27]

Ahora ya está listo para conectar el Explorador de Storage a una suscripción de Azure Stack.

## <a name="enable-target-azure-stack"></a>Habilitación de la instancia de Azure Stack de destino

Después de que se reinicie el Explorador de Storage, seleccione el menú **Edit** (Editar) y luego asegúrese de que esté seleccionada la opción **Target Azure Stack** (Azure Stack de destino). Si no está seleccionada, selecciónela y luego reinicie el Explorador de Storage para que el cambio surta efecto. Esta configuración es necesaria para obtener compatibilidad con el entorno de Azure Stack.

![Comprobación de que Azure Stack de destino está activada][28]

## <a name="sign-in-to-your-account"></a>Inicio de sesión en su cuenta

### <a name="aad-backed-azure-stack"></a>Azure Stack respaldado por AAD

1. En el panel izquierdo, seleccione **Administrar cuentas**. Se muestran todas las cuentas de Microsoft en las que haya iniciado sesión.
2. Para conectarse a la cuenta de Azure Stack, seleccione **Agregar una cuenta**.

    ![Adición de una cuenta de Azure Stack][29]
3. En el cuadro de diálogo **Connect to Azure Storage** (Conectar a Azure Storage), en **Azure environment** (Entorno de Azure), seleccione el entorno de Azure que respalda su instancia de Azure Stack y, a continuación, haga clic en **Sign in** (Iniciar sesión).
4. Especificación de las credenciales en la ventana de inicio de sesión
5. Después de iniciar sesión correctamente con una cuenta de Azure Stack, el panel izquierdo se llena con las suscripciones normales y las de Azure Stack. Elija las suscripciones con las que desea trabajar y luego seleccione **Apply** (Aplicar). (Al activar o desactivar la casilla **Todas las suscripciones**, se alterna entre seleccionar todas o ninguna de las suscripciones de Azure Stack que aparecen).

    ![Selección de las suscripciones de Azure Stack después de rellenar el cuadro de diálogo Custom Cloud Environment (Entorno personalizado en la nube)][30]  
    El panel izquierdo muestra ahora las cuentas de almacenamiento asociadas a las suscripciones de Azure Stack seleccionadas.

    ![Lista de cuentas de almacenamiento, incluidas las cuentas de suscripción de Azure Stack][31]

### <a name="adfs-backed-azure-stack"></a>Azure Stack respaldado por ADFS

1. En el panel izquierdo, seleccione **Administrar cuentas**. Se muestran todas las cuentas de Microsoft en las que haya iniciado sesión.
2. Para conectarse a la cuenta de Azure Stack, seleccione **Agregar una cuenta**.

    ![Adición de una cuenta de Azure Stack][29]
3. En el cuadro de diálogo **Connect to Azure Storage** (Conectar a Azure Storage), en **Azure environment** (Entorno de Azure), seleccione **Create Custom Environment** (Crear entorno personalizado) y luego haga clic en **Next** (Siguiente).
4. Para iniciar sesión con la cuenta de Azure Stack que está asociada a una suscripción de Azure Stack activa por lo menos, rellene el cuadro de diálogo **Sign in to Azure Stack Environment** (Iniciar sesión en un entorno de Azure Stack).  

    Los detalles de cada campo son los siguientes:

    * **Environment name** (Nombre del entorno): el usuario puede personalizar este campo.
    * **Azure Resource Manager resource endpoint** (Punto de conexión de recursos de Azure Resource Manager): los ejemplos de puntos de conexión de recursos de Azure Resource Manager:
        * Para operadores en la nube:<br> https://adminmanagement.local.azurestack.external   
        * Para el inquilino:<br> https://management.local.azurestack.external
5. Después de iniciar sesión correctamente con una cuenta de Azure Stack, el panel izquierdo se llena con las suscripciones de Azure Stack asociadas a dicha cuenta. Seleccione las suscripciones de Azure Stack con la que quiere trabajar y luego seleccione **Aplicar**. (Al activar o desactivar la casilla **Todas las suscripciones**, se alterna entre seleccionar todas o ninguna de las suscripciones de Azure Stack que aparecen).

    ![Selección de las suscripciones de Azure Stack después de rellenar el cuadro de diálogo Custom Cloud Environment (Entorno personalizado en la nube)][30]  
    El panel izquierdo muestra ahora las cuentas de almacenamiento asociadas a las suscripciones de Azure Stack seleccionadas.

    ![Lista de cuentas de almacenamiento, incluidas las cuentas de suscripción de Azure Stack][31]

## <a name="next-steps"></a>Pasos siguientes
* [Introducción al Explorador de Storage](../../vs-azure-tools-storage-manage-with-storage-explorer.md)
* [Azure Stack Storage: Diferencias y consideraciones](azure-stack-acs-differences.md)


* Para más información sobre Azure Storage, consulte [Introducción a Microsoft Azure Storage](../../storage/common/storage-introduction.md).

[25]: ./media/azure-stack-storage-connect-se/add-certificate-azure-stack.png
[26]: ./media/azure-stack-storage-connect-se/export-root-cert-azure-stack.png
[27]: ./media/azure-stack-storage-connect-se/import-azure-stack-cert-storage-explorer.png
[28]: ./media/azure-stack-storage-connect-se/select-target-azure-stack.png
[29]: ./media/azure-stack-storage-connect-se/add-azure-stack-account.png
[30]: ./media/azure-stack-storage-connect-se/select-accounts-azure-stack.png
[31]: ./media/azure-stack-storage-connect-se/azure-stack-storage-account-list.png
