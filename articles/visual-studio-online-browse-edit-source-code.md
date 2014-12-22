<properties urlDisplayName="Browse and Edit Your Source Code" pageTitle="Examen y edición del código fuente | Azure" metaKeywords="Visual Studio Online, VSO, git, tfvc, editar, código, confirmar" description="Learn how to edit your source code." metaCanonical="" services="visual-studio-online" documentationCenter="" title="Browse and Edit Your Source Code" authors="ehollow" solutions="" manager="kamrani" editor="" />

<tags ms.service="visual-studio-online" ms.workload="tbd" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="ehollow" />

# Búsqueda y edición del código fuente

En la vista previa del Portal de Microsoft Azure es sencillo buscar y editar el código fuente del proyecto de equipo de Visual Studio Online.


Una vez creado un proyecto de equipo en el portal y después de proteger el código (control de versiones de Team Foundation) o confirmarlo (git), puede acceder a dicho código y explorarlo fácilmente a través del portal.


1. En primer lugar, abra el cuadro de proyecto de equipo y busque el modo de código.  Después, en la parte del código fuente, haga clic en el nombre del repositorio que le gustaría examinar (en este caso se llama 'BrowseCode').  Nota: si tiene más de dos repositarios, puede hacer clic en el número de repositorio (1 en este caso) para ver la lista completa.
![Code Lens](./media/visual-studio-online-browse-edit-source-code/Code-Lens.png)
2. Ahora que el cuadro de repositorio está abierto, puede hacer clic en la parte Código para comenzar a examinar el código fuente.
![Repository Blade](./media/visual-studio-online-browse-edit-source-code/Repo-Blade.png)
3. Aparecerá un cuadro de navegación de vista de árbol estándar con todo el proyecto disponible.  Puede hacer clic en los nodos para abrir la carpeta o en un archivo específico (Index.cshtml en este caso) para ver o editar el archivo.
![Tree Navigation](./media/visual-studio-online-browse-edit-source-code/Tree-Nav.png)
4. Para editar un archivo, simplemente haga clic en el botón Editar de la barra de comandos.  El contenido del archivo entrará en el modo de edición y podrá realizar los cambios. Sugerencia: haga clic en el botón Maximizar situado en la parte superior derecha para tener una experiencia de edición a pantalla completa.
![Edit Mode](./media/visual-studio-online-browse-edit-source-code/Edit-Mode.png)
5. Cuando haya terminado de editar el archivo, haga clic en el botón Confirmar o (Proteger) de la barra de comandos.  Esta acción iniciará un nuevo cuadro donde podrá escribir el mensaje de envío.  Cuando haya terminado, haga clic en Aceptar y el archivo se confirmará en el repositorio.
![Commit Code](./media/visual-studio-online-browse-edit-source-code/Commit-Code.png)
6. En la parte superior del cuadro aparecerá un mensaje intermitente que indica que la confirmación del repositorio se ha realizado correctamente.
![Commit Success](./media/visual-studio-online-browse-edit-source-code/Commit-Success.png)







