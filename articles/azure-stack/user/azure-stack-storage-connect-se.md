---
title: "Conexión del Explorador de Storage a una suscripción de Azure Stack"
description: "Aprenda a conectar el Explorador de Storage a una suscripción de Azure Stack"
services: azure-stack
documentationcenter: 
author: xiaofmao
manager: 
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 9/25/2017
ms.author: xiaofmao
ms.openlocfilehash: c7e6d70148d39fd74f6409a0a239833f8e9f7614
ms.sourcegitcommit: d03907a25fb7f22bec6a33c9c91b877897e96197
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/12/2017
---
# <a name="connect-storage-explorer-to-an-azure-stack-subscription"></a>Conexión del Explorador de Storage a una suscripción de Azure Stack

*Se aplica a: Sistemas integrados de Azure Stack y Azure Stack Development Kit*

El Explorador de Azure Storage (versión preliminar) es una aplicación independiente que permite trabajar fácilmente con datos de Azure Stack Storage en Windows, macOS y Linux. Hay varias herramientas disponibles para mover datos hacia Azure Stack Storage y desde este. Para más información, consulte [Herramientas de transferencia de datos de Azure Stack Storage](azure-stack-storage-transfer.md).

En este artículo, aprenderá a conectarse a sus cuentas de Azure Stack Storage mediante el Explorador de Storage. 

Si no ha instalado todavía el Explorador de Storage, [descárguelo](http://www.storageexplorer.com/) y hágalo.

Después de conectarse a su suscripción de Azure Stack, puede usar los [artículos sobre el Explorador de Azure Storage](../../vs-azure-tools-storage-manage-with-storage-explorer.md) para trabajar con los datos de Azure Stack. 

## <a name="prepare-an-azure-stack-subscription"></a>Preparación de una suscripción de Azure Stack

Necesita acceder al escritorio de la máquina del host de Azure Stack o una conexión VPN para que el Explorador de Storage pueda acceder de forma remota a la suscripción de Azure Stack. Para más información sobre cómo configurar una conexión VPN a Azure Stack, consulte [Conexión a Azure Stack con VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn).

Para Azure Stack Development Kit, debe exportar el certificado raíz de la entidad de certificación de Azure Stack.

### <a name="to-export-and-then-import-the-azure-stack-certificate"></a>Exportación y posterior importación del certificado de Azure Stack

1. Abra `mmc.exe` en una máquina host de Azure Stack o una máquina local con una conexión VPN a Azure Stack. 

2. En **Archivo**, seleccione **Agregar o quitar complemento** y luego agregue **certificados** para administrar la **Mi cuenta de usuario**.



3. En **Console Root\Certificated (Local Computer)\Trusted Root Certification Authorities\Certificates** busque **AzureStackSelfSignedRootCert**.

    ![Carga del certificado raíz de Azure Stack a través de mmc.exe][25]

4. Haga clic con el botón derecho en el certificado, seleccione **Todas las tareas** > **Exportar** y luego siga las instrucciones para exportar el certificado con **X.509 (.CER) codificado en base 64**.  

    Se usará el certificado exportado en el paso siguiente.
5. Inicie el Explorador de Storage (versión preliminar) y, si ve el cuadro de diálogo **Conectar a Azure Storage**, cancélelo.

6. En el menú **Editar**, seleccione **Certificados SSL** y, luego, haga clic en **Importar certificados**. Use el cuadro de diálogo del selector de archivos para buscar y abrir el certificado que exportó en el paso anterior.

    Después de la importación, se le pedirá que reinicie el Explorador de Storage.

    ![Importe el certificado en el Explorador de Storage (versión preliminar)][27]

Ahora ya está listo para conectar el Explorador de Storage a una suscripción de Azure Stack.

### <a name="to-connect-an-azure-stack-subscription"></a>Conexión a una suscripción de Azure Stack


1. Después de que se reinicie el Explorador de Storage (versión preliminar), seleccione el menú **Editar** y luego asegúrese de que esté seleccionada la opción **Target Azure Stack** (Azure Stack de destino). Si no está seleccionada, selecciónela y luego reinicie el Explorador de Storage para que el cambio surta efecto. Esta configuración es necesaria para obtener compatibilidad con el entorno de Azure Stack.

    ![Comprobación de que Azure Stack de destino está activada][28]

7. En el panel izquierdo, seleccione **Administrar cuentas**.  
    Se muestran todas las cuentas de Microsoft en las que haya iniciado sesión.

8. Para conectarse a la cuenta de Azure Stack, seleccione **Agregar una cuenta**.

    ![Adición de una cuenta de Azure Stack][29]

9. En el cuadro de diálogo **Conectar a Azure Storage**, en el **entorno de Azure**, seleccione **Use Azure Stack Environment** (Usar entorno de Azure Stack) y, a continuación, haga clic en **Siguiente**.

10. Para iniciar sesión con la cuenta de Azure Stack que está asociada a una suscripción de Azure Stack activa por lo menos, rellene el cuadro de diálogo **Sign in to Azure Stack Environment** (Iniciar sesión en un entorno de Azure Stack).  

    Los detalles de cada campo son los siguientes:

    * **Environment name** (Nombre del entorno): el usuario puede personalizar este campo.
    * **ARM resource endpoint** (Punto de conexión de recurso de ARM): los ejemplos de los puntos de conexión de recursos de Azure Resource Manager:

        * Para operadores en la nube:<br> https://adminmanagement.local.azurestack.external   
        * Para el inquilino:<br> https://management.local.azurestack.external
 
    * **Identificador de inquilino**: opcional. Se asigna un valor solo si se debe especificar el directorio.

12. Después de iniciar sesión correctamente con una cuenta de Azure Stack, el panel izquierdo se llena con las suscripciones de Azure Stack asociadas a dicha cuenta. Seleccione las suscripciones de Azure Stack con la que quiere trabajar y luego seleccione **Aplicar**. (Al activar o desactivar la casilla **Todas las suscripciones**, se alterna entre seleccionar todas o ninguna de las suscripciones de Azure Stack que aparecen).

    ![Selección de las suscripciones de Azure Stack después de rellenar el cuadro de diálogo Custom Cloud Environment (Entorno personalizado en la nube)][30]  
    El panel izquierdo muestra ahora las cuentas de almacenamiento asociadas a las suscripciones de Azure Stack seleccionadas.

    ![Lista de cuentas de almacenamiento, incluidas las cuentas de suscripción de Azure Stack][31]

## <a name="next-steps"></a>Pasos siguientes
* [Introducción al Explorador de Storage (versión preliminar)](../../vs-azure-tools-storage-manage-with-storage-explorer.md)
* [Azure Stack Storage: Diferencias y consideraciones](azure-stack-acs-differences.md)


* Para más información sobre Azure Storage, consulte [Introducción a Microsoft Azure Storage](../../storage/common/storage-introduction.md).

[25]: ./media/azure-stack-storage-connect-se/add-certificate-azure-stack.png
[26]: ./media/azure-stack-storage-connect-se/export-root-cert-azure-stack.png
[27]: ./media/azure-stack-storage-connect-se/import-azure-stack-cert-storage-explorer.png
[28]: ./media/azure-stack-storage-connect-se/select-target-azure-stack.png
[29]: ./media/azure-stack-storage-connect-se/add-azure-stack-account.png
[30]: ./media/azure-stack-storage-connect-se/select-accounts-azure-stack.png
[31]: ./media/azure-stack-storage-connect-se/azure-stack-storage-account-list.png
