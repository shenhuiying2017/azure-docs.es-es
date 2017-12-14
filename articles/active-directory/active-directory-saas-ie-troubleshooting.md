---
title: "Solución de problemas de la extensión del panel de acceso de Azure para Internet Explorer | Microsoft Docs"
description: "Cómo usar la directiva de grupo para implementar el complemento de Internet Explorer para el portal de Mis aplicaciones."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: mtillman
editor: 
ms.assetid: f56b3230-26fd-42ec-9e3d-2c12daf15479
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/31/2017
ms.author: markvi
ms.reviewer: asteen
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ff6d0b299c45d89ee8d3a79fa98ce4a542174a5b
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="troubleshooting-the-access-panel-extension-for-internet-explorer"></a>Solución de problemas de la extensión del Panel de acceso para Internet Explorer
Este artículo le ayudará a solucionar los siguientes problemas:

* No puede tener acceso a sus aplicaciones a través del portal de Mis aplicaciones con Internet Explorer.
* Verá el mensaje "Instalar software" aunque ya haya instalado el software.

Si es administrador, vea también: [Cómo implementar la extensión del Panel de acceso para Internet Explorer con la Directiva de grupo](active-directory-saas-ie-group-policy.md)

## <a name="run-the-diagnostic-tool"></a>Ejecutar la herramienta de diagnóstico
Puede diagnosticar problemas de instalación con la extensión del Panel de acceso descargando y ejecutando la herramienta de diagnóstico del Panel de acceso:

1. [Haga clic aquí para descargar la herramienta de diagnóstico.](https://account.activedirectory.windowsazure.com/applications/AccessPanelExtensionDiagnosticTool/AccessPanelExtensionDiagnosticTool.zip)
2. Abra el archivo y presione el botón **Extraer todo** .
   
    ![Presionar Extraer todo](./media/active-directory-saas-ie-troubleshooting/extract1.png)
3. A continuación, presione el botón **Extraer** para continuar.
   
    ![Presionar Extraer](./media/active-directory-saas-ie-troubleshooting/extract2.png)
4. Para ejecutar la herramienta, haga clic con el botón derecho en el archivo denominado **AccessPanelExtensionDiagnosticTool** y, después, seleccione **Abrir con > Host de script basado en Microsoft Windows**.
   
    ![Abrir con > Host de script basado en Microsoft Windows](./media/active-directory-saas-ie-troubleshooting/open_tool.png)
5. A continuación verá la siguiente ventana de diagnóstico, en la que se describe qué puede haber incorrecto con la instalación.
   
    ![Un ejemplo de la ventana de diagnóstico](./media/active-directory-saas-ie-troubleshooting/tool_preview.png)
6. Haga clic en "**SÍ**" para permitir que el programa corrija los problemas que se han encontrado.
7. Para guardar estos cambios, cierre cada ventana de Internet Explorer y luego vuelva a abrir Internet Explorer.<br />Si todavía no puede tener acceso a sus aplicaciones, pruebe los pasos siguientes.

## <a name="check-that-the-access-panel-extension-is-enabled"></a>Comprobar que la extensión del Panel de acceso está activada
Para comprobar que la extensión del Panel de acceso está habilitada en Internet Explorer:

1. En Internet Explorer, haga clic en el **icono de engranaje** de la esquina superior derecha de la ventana. Después, seleccione **Opciones de Internet**.<br />(En versiones anteriores de Internet Explorer, encontrará esta opción en **Herramientas > Opciones de Internet**.
   
    ![Ir a Herramientas > Opciones de Internet](./media/active-directory-saas-ie-troubleshooting/internetoptions.png)
2. Haga clic en la pestaña **Programas** y, después, en el botón **Administrar complementos**.
   
    ![Hacer clic en Administrar complementos](./media/active-directory-saas-ie-troubleshooting/internetoptions_programs.png)
3. En este diálogo, seleccione **Extensión del Panel de acceso** y, después, haga clic en el botón **Habilitar**.
   
    ![Hacer clic en Habilitar](./media/active-directory-saas-ie-troubleshooting/enableaddon.png)
4. Para guardar estos cambios, cierre cada ventana de Internet Explorer y luego vuelva a abrir Internet Explorer.

## <a name="enable-extensions-for-inprivate-browsing"></a>Habilitar extensiones para la exploración de InPrivate
Si usa el modo de exploración de InPrivate:

1. En Internet Explorer, haga clic en el **icono de engranaje** de la esquina superior derecha de la ventana. Después, seleccione **Opciones de Internet**.<br />(En versiones anteriores de Internet Explorer, encontrará esta opción en **Herramientas > Opciones de Internet**.
   
    ![Un ejemplo de la ventana de diagnóstico](./media/active-directory-saas-ie-troubleshooting/inprivateoptions.png)
2. Vaya a la pestaña **Privacidad** y **desactive** la casilla **Deshabilitar barras de herramientas y extensiones cuando se inicie la exploración de InPrivate**.</p>
   
    ![Desactivar Deshabilitar barras de herramientas y extensiones cuando se inicie la exploración de InPrivate](./media/active-directory-saas-ie-troubleshooting/enabletoolbars.png)
3. Para guardar estos cambios, cierre cada ventana de Internet Explorer y luego vuelva a abrir Internet Explorer.

## <a name="uninstall-the-access-panel-extension"></a>Desinstalar la extensión del Panel de acceso
Para desinstalar la extensión del Panel de acceso desde el equipo:

1. En el teclado, presione la **clave de Windows** para abrir el menú Inicio. Cuando se abre el menú, puede escribir cualquier cosa para realizar una búsqueda. Escriba "Panel de Control" y luego abra el **Panel de Control** cuando aparezca en los resultados de la búsqueda.
   
    ![Buscar el Panel de control](./media/active-directory-saas-ie-troubleshooting/search_sm.png)
2. En la esquina superior derecha del Panel de control, cambie la opción **Ver** a **Iconos grandes**. Luego busque y haga clic en el botón **Programas y características**.
   
    ![Cambiar la vista para mostrar iconos grandes](./media/active-directory-saas-ie-troubleshooting/control_panel.png)
3. En la lista, seleccione **Access Panel Extension** (Extensión del Panel de acceso) y, después, haga clic en el botón **Desinstalar**.
   
    ![Hacer clic en Desinstalar](./media/active-directory-saas-ie-troubleshooting/uninstall.png)
4. Después, puede intentar instalar la extensión de nuevo para ver si se ha resuelto el problema.

Si encuentra problemas al desinstalar la extensión, también puede quitarla usando la herramienta [Microsoft Fix It](https://go.microsoft.com/?linkid=9779673) .

## <a name="related-articles"></a>Artículos relacionados
* [Índice de artículos sobre la administración de aplicaciones en Azure Active Directory](active-directory-apps-index.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)
* [Implementación de la extensión del Panel de acceso para Internet Explorer mediante la directiva de grupo](active-directory-saas-ie-group-policy.md)

