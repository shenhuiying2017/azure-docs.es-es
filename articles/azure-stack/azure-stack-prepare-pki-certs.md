---
title: Preparación de certificados de infraestructura de clave pública de Azure Stack para la implementación de sistemas integrados de Azure Stack | Microsoft Docs
description: Describe cómo preparar los certificados PKI de Azure Stack para sus sistemas integrados.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/22/2018
ms.author: mabrigg
ms.reviewer: ppacent
ms.openlocfilehash: 934585082e2832c41885874c82ab43d64a1fa361
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/04/2018
ms.locfileid: "33203483"
---
# <a name="prepare-azure-stack-pki-certificates-for-deployment"></a>Preparación de certificados PKI de Azure Stack para la implementación
Los archivos de certificado [obtenidos de la entidad de certificación que eligió](azure-stack-get-pki-certs.md) deben importarse y exportarse con propiedades que coincidan con los requisitos de certificado de Azure Stack.


## <a name="prepare-certificates-for-deployment"></a>Preparación de los certificados para la implementación
Siga estos pasos para preparar y validar los certificados PKI de Azure Stack: 

### <a name="import-the-certificate"></a>Importación del certificado

1.  Copie las versiones de los certificados originales [obtenidas de la entidad de certificación de elección](azure-stack-get-pki-certs.md) en un directorio del host de implementación. 
  > [!WARNING]
  > No copie los archivos que ya se han importado, exportado o modificado de alguna forma de los archivos proporcionados directamente por la entidad de certificación.

2.  Haga clic con el botón derecho en el certificado y seleccione **Instalar certificado** o **Instalar PFX**, dependiendo de cómo se entrega el certificado desde la entidad de certificación.

3. En el **Asistente para importar certificados**, seleccione **Máquina local** como la ubicación de importación. Seleccione **Next** (Siguiente). En la siguiente pantalla, vuelva a hacer clic en Siguiente.

    ![Ubicación de importación de la máquina local](.\media\prepare-pki-certs\1.png)

4.  Elija **Colocar todos los certificados en el siguiente almacén** y, luego, seleccione **Confianza empresarial** como ubicación. Haga clic en **Aceptar** para cerrar el cuadro de diálogo de selección de almacén de certificados y, luego, haga clic en **Siguiente**.

    ![Configurar el almacén de certificados](.\media\prepare-pki-certs\3.png)

    a. Si va a importar un PFX, aparecerá un cuadro de diálogo adicional. En la página **Protección de clave privada**, escriba la contraseña de los archivos de certificado y, luego, habilite **Marcar esta clave como exportable. Esto permite realizar copias de seguridad o transportar las claves en un momento posterior**. Seleccione **Next** (Siguiente).

    ![Marcar clave como exportable](.\media\prepare-pki-certs\2.png)

5. Haga clic en Finalizar para completar la importación.

### <a name="export-the-certificate"></a>Exportación de certificados

Abra la consola MMC del administrador de certificados y conéctese al almacén de certificados de la máquina local.

1. Abra Microsoft Management Console, en Windows 10 haga clic con el botón derecho en el menú Inicio y, después, haga clic en Ejecutar. Escriba **mmc** y haga clic en Aceptar.

2. Haga clic en Archivo, Agregar o quitar complemento y, después, seleccione certificados y haga clic en Agregar.

    ![Complemento Agregar certificados](.\media\prepare-pki-certs\mmc-2.png)
 
3. Seleccione la cuenta Equipo, haga clic en Siguiente, seleccione Equipo local y, después, haga clic en Finalizar. Haga clic en Aceptar para cerrar la página Agregar o quitar complemento.

    ![Complemento Agregar certificados](.\media\prepare-pki-certs\mmc-3.png)

4. Vaya a Certificados > Confianza empresarial > Ubicación de certificado. Compruebe que ve el certificado a la derecha.

5. En la barra de tareas de la consola del administrador de certificados, seleccione **Acciones** > **Todas las tareas** > **Exportar**. Seleccione **Next** (Siguiente).

  > [!NOTE]
  > Dependiendo del número de certificados de Azure Stack que tenga, es posible que necesite completar este proceso más de una vez.

4. Seleccione **Exportar la clave privada** y, después, haga clic en **Siguiente**.

5. En la sección Formato de archivo de exportación, seleccione **Exportar todas las propiedades extendidas** y, luego, haga clic en **Siguiente**.

6. Seleccione **Contraseña** y proporcione una contraseña para los certificados. Recuerde esta contraseña, ya que se utiliza como un parámetro de implementación. Seleccione **Next** (Siguiente).

7. Elija un nombre de archivo y la ubicación del archivo PFX que se va a exportar. Seleccione **Next** (Siguiente).

8. Seleccione **Finalizar**.

## <a name="next-steps"></a>Pasos siguientes
[Validar los certificados PKI](azure-stack-validate-pki-certs.md)