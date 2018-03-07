---
title: "Preparación de certificados de infraestructura de clave pública de Azure Stack para la implementación de sistemas integrados de Azure Stack | Microsoft Docs"
description: "Describe cómo preparar los certificados PKI de Azure Stack para sus sistemas integrados."
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2018
ms.author: jeffgilb
ms.reviewer: ppacent
ms.openlocfilehash: b2f1e9bf62773dd7124678552f23dd9262530031
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/24/2018
---
# <a name="prepare-azure-stack-pki-certificates-for-deployment"></a>Preparación de certificados PKI de Azure Stack para la implementación
Los archivos de certificado [obtenidos de la entidad de certificación que eligió](azure-stack-get-pki-certs.md) deben importarse y exportarse con propiedades que coincidan con los requisitos de certificado de Azure Stack.


## <a name="prepare-certificates-for-deployment"></a>Preparación de los certificados para la implementación
Siga estos pasos para preparar y validar los certificados PKI de Azure Stack: 

1.  Copie las versiones de los certificados originales [obtenidas de la entidad de certificación de elección](azure-stack-get-pki-certs.md) en un directorio del host de implementación. 
  > [!WARNING]
  > No copie los archivos que ya se han importado, exportado o modificado de alguna forma de los archivos proporcionados directamente por la entidad de certificación.

2.  Importe los certificados en el almacén de certificados de la máquina local:

    a.  Haga clic con el botón derecho en el certificado y seleccione **Instalar PFX**.

    b.  En el **Asistente para importar certificados**, seleccione **Máquina local** como la ubicación de importación. Seleccione **Siguiente**.

    ![Ubicación de importación de la máquina local](.\media\prepare-pki-certs\1.png)

    c.  Seleccione **Siguiente** en la página **Elija un archivo para importar**.

    d.  En la página **Protección de clave privada**, escriba la contraseña de los archivos de certificado y, luego, habilite **Marcar esta clave como exportable. Esto permite realizar copias de seguridad o transportar las claves en un momento posterior**. Seleccione **Siguiente**.

    ![Marcar clave como exportable](.\media\prepare-pki-certs\2.png)

    e.  Elija **Colocar todos los certificados en el siguiente almacén** y, luego, seleccione **Confianza empresarial** como ubicación. Haga clic en **Aceptar** para cerrar el cuadro de diálogo de selección de almacén de certificados y, luego, haga clic en **Siguiente**.

    ![Configurar el almacén de certificados](.\media\prepare-pki-certs\3.png)

  f.    Haga clic en **Finalizar** para completar el Asistente para importar certificados.

  g.    Repita el proceso para todos los certificados que se van a proporcionar para la implementación.

3. Exporte el certificado en formato de archivo PFX con los requisitos de Azure Stack:

  a.    Abra la consola MMC del administrador de certificados y conéctese al almacén de certificados de la máquina local.

  b.    Vaya al directorio **Confianza empresarial**.

  c.    Seleccione uno de los certificados importados en el paso 2 anterior.

  d.    En la barra de tareas de la consola del administrador de certificados, seleccione **Acciones** > **Todas las tareas** > **Exportar**.

  e.    Seleccione **Siguiente**.

  f.    Seleccione **Exportar la clave privada** y, después, haga clic en **Siguiente**.

  g.    En la sección Formato de archivo de exportación, seleccione **Exportar todas las propiedades extendidas** y, luego, haga clic en **Siguiente**.

  h.    Seleccione **Contraseña** y proporcione una contraseña para los certificados. Recuerde esta contraseña, ya que se utiliza como un parámetro de implementación. Seleccione **Siguiente**.

  i.    Elija un nombre de archivo y la ubicación del archivo PFX que se va a exportar. Seleccione **Siguiente**.

  j.    Seleccione **Finalizar**.

  k.    Repita este proceso para todos los certificados importados para su implementación en el paso 2 anterior.

## <a name="next-steps"></a>pasos siguientes
[Validar los certificados PKI](validate-pki-certs.md)