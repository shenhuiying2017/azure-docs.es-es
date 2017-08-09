## <a name="microsoft-open-source-code-of-conduct"></a>Código de conducta de Microsoft Open Source

Este proyecto ha adoptado el [Microsoft Open Source Code of Conduct](https://opensource.microsoft.com/codeofconduct/) (Código de conducta de código abierto de Microsoft).
Para más información, consulte las [preguntas más frecuentes del código de conducta](https://opensource.microsoft.com/codeofconduct/faq/) o póngase en contacto con [opencode@microsoft.com](mailto:opencode@microsoft.com) si tiene cualquier otra pregunta o comentario.

## <a name="contribute-to-azure-technical-documentation"></a>Contribute to Azure technical documentation (Contribución a la documentación técnica de Azure)
Agradecemos las contribuciones de nuestra comunidad (usuarios, clientes, asociados, empleados de MSFT externos a las unidades centrales del producto Azure, etc.), así como de los empleados que trabajan en unidades del producto central Azure. La manera de contribuir depende de quién sea:

* **Actualizaciones menores de la comunidad**: si contribuye con actualizaciones menores por iniciativa propia, busque el artículo en este repositorio, o consulte al artículo en [https://docs.microsoft.com/azure](https://docs.microsoft.com/azure) y haga clic en el vínculo **Editar** del artículo que abre el origen del artículo en GitHub. A continuación, solo tiene que usar la interfaz de usuario de GitHub para realizar las actualizaciones. Por otro lado, también puede bifurcar el repositorio y enviar las actualizaciones desde la bifurcación.

* **Nuevos artículos de la comunidad**: si forma parte de la comunidad de Azure y desea crear un nuevo artículo, debe trabajar con un empleado para que ese nuevo contenido aparezca como combinación de trabajo en el repositorio público y el privado.

* **Empleados**: si usted es escritor técnico, jefe de programa o desarrollador del equipo del producto para un servicio de Azure y su trabajo es crear artículos técnicos o contribuir en ellos, debe usar el repositorio privado (https://github.com/MicrosoftDocs/azure-docs-pr). Si realiza cambios considerables en un artículo existente, agrega o cambia imágenes, o colabora en un nuevo artículo, necesitará bifurcar este repositorio, instalar Git Bash, un editor de Markdown y aprender algunos comandos de Git. Consulte la [guía del colaborador interno](https://review.docs.microsoft.com/en-us/help/contribute/?branch=master) para más información.


## <a name="about-your-contributions-to-azure-content"></a>Acerca de su colaboración en el contenido de Azure
### <a name="minor-corrections"></a>Correcciones menores
Las correcciones menores o aclaraciones que se envían para la documentación y los ejemplos de código de este repositorio se rigen por los [Términos de uso del sitio docs.microsoft.com](https://docs.microsoft.com/legal/termsofuse).

### <a name="larger-submissions"></a>Aportaciones mayores
Si envía una solicitud de incorporación de cambios con cambios importantes o nuevos en la documentación y los ejemplos de código, pondremos un comentario en GitHub para solicitarle que acepte el contrato de licencia de colaboración (CLA, por sus siglas en inglés) si no es un empleado de Microsoft. Necesitamos que rellene el formulario en línea para aceptar su solicitud de incorporación de cambios.

## <a name="tools-and-setup"></a>Herramientas e instalación
Los colaboradores de la comunidad pueden usar la interfaz de usuario de GitHub o bifurcar el repositorio para contribuir. Los empleados deben visitar la [guía del colaborador interno](https://review.docs.microsoft.com/en-us/help/contribute/?branch=master) para más información acerca de cómo contribuir a la documentación técnica.

## <a name="repository-organization"></a>Organización del repositorio
El contenido del repositorio azure-docs está organizado como la documentación de https://docs.microsoft.com/azure. Este repositorio tiene dos carpetas raíz:

### <a name="articles"></a>\articles
La carpeta *\articles* contiene los artículos de la documentación en formato de archivos de Markdown, con la extensión *.md*. Los artículos normalmente se agrupan por servicio de Azure.

La carpeta *\articles* contiene la carpeta *\media* para los archivos multimedia de los artículos del directorio raíz; dentro de ella se encuentran subcarpetas con las imágenes de cada artículo.  Las carpetas de servicio contienen una carpeta multimedia independiente para los artículos que se encuentran en cada carpeta de servicio. Las carpetas de imágenes de los artículos tienen el mismo nombre que el archivo del artículo, sin la extensión de archivo *.md* .

### <a name="includes"></a>\includes
Puede crear secciones de contenido reutilizable para incluirlo en uno o varios artículos. 

## <a name="how-to-use-markdown-to-format-your-topic"></a>Uso de Markdown para dar formato al tema
Todos los artículos de este repositorio usan Markdown adaptado a GitHub.  Aquí tiene una lista de recursos.

* [Reglas básicas de Markdown](https://help.github.com/articles/markdown-basics/)
* [Hoja de referencia de Markdown para impresión](./contributor-guide/media/documents/markdown-cheatsheet.pdf?raw=true)


## <a name="labels"></a>Etiquetas
En el repositorio azure-docs público, se asignan etiquetas automatizadas a las solicitudes de incorporación de cambios para ayudarnos a administrar el flujo de trabajo de este tipo de solicitudes e indicar el estado de las mismas:

* Contrato de licencia de colaboración relacionado
  * cla-not-required: el cambio es relativamente pequeño y no requiere que firme un contrato de licencia de colaboración.
  * cla-required: el alcance del cambio es relativamente importante y requiere que firme un contrato de licencia de colaboración.
  * cla-signed: el colaborador ha firmado el contrato de licencia de colaboración por lo que la solicitud de incorporación de cambios ya puede seguir hacia delante para su revisión.
* Cambio enviado al autor: se ha notificado al autor acerca de la solicitud de incorporación de cambios pendiente.
* ready-to-merge: listo para revisión por parte de nuestro equipo de revisión de solicitudes de incorporación de cambios.


