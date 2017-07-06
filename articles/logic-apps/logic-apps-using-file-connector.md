---
title: "Conexión a una carpeta del sistema de archivos local desde Azure Logic Apps | Microsoft Docs"
description: "Conexión a sistemas de archivos locales desde el flujo de trabajo de la aplicación lógica a través de la puerta de enlace de datos local y el conector del sistema de archivos"
keywords: Sistemas de archivos
services: logic-apps
author: derek1ee
manager: anneta
documentationcenter: 
ms.assetid: 
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/27/2017
ms.author: LADocs; deli
ms.translationtype: Human Translation
ms.sourcegitcommit: a30a90682948b657fb31dd14101172282988cbf0
ms.openlocfilehash: f33e7c58103c57e17e4e273caba1ab9b83f0cd2b
ms.contentlocale: es-es
ms.lasthandoff: 07/06/2017

---

# <a name="connect-to-on-premises-file-systems-from-logic-apps-with-the-file-system-connector"></a>Conexión a sistemas de archivos locales desde las aplicaciones lógicas con el conector de sistema de archivos

La conectividad de nube híbrida es fundamental para las aplicaciones lógicas y para administrar datos y acceder de forma segura a recursos locales, las aplicaciones lógicas pueden utilizar la puerta de enlace de datos local. En este artículo, mostramos cómo conectarse a un sistema de archivos local con un escenario básico: copiar un archivo que se carga en Dropbox para un recurso compartido de archivos y, después, enviar un correo electrónico.

## <a name="prerequisites"></a>Requisitos previos

- Instale y configure la [puerta de enlace de datos local](https://www.microsoft.com/download/details.aspx?id=53127) más reciente.
- Instale la puerta de enlace de datos local más reciente, versión 1.15.6150.1 o superior. En [Conexión a la puerta de enlace de datos local](http://aka.ms/logicapps-gateway) se enumeran los pasos. La puerta de enlace debe instalarse en una máquina local para poder continuar con el resto de los pasos.

## <a name="add-trigger-and-actions-for-connecting-to-your-file-system"></a>Incorporación de desencadenadores y acciones para conectarse al sistema de archivos

1. Cree una aplicación de la lógica y agregue el desencadenador **Dropbox - Cuando se crea un archivo**. 
2. En el desencadenador, elija **Paso siguiente** > **Agregar una acción**. 
3. En el cuadro de búsqueda, escriba `file system` para poder ver todas las acciones admitidas para el conector del sistema de archivos.

   ![Conector Buscar archivo](media/logic-apps-using-file-connector/search-file-connector.png)

2. Elija la acción **Crear archivo** y cree una conexión con el sistema de archivos.

   Si no tiene una conexión existente, se le pedirá que cree una.

   1. Seleccione **Connect via on-premises data gateway** (Conectarse a través de la puerta de enlace de datos local). Aparecen más propiedades.
   2. Seleccione la carpeta raíz para el sistema de archivos.
      
       > [!NOTE]
       > La carpeta raíz es la carpeta primaria principal, que se usa para las rutas de acceso relativas para todas las acciones relacionadas con archivos. Puede especificar una carpeta local en la máquina en la que está instalada la puerta de enlace de datos local; o bien, la carpeta puede ser un recurso compartido de red al que tiene acceso la máquina.

   3. Escriba el nombre de usuario y la contraseña para la puerta de enlace.
   4. Seleccione la puerta de enlace que ha instalado anteriormente.

       ![Configuración de la conexión](media/logic-apps-using-file-connector/create-file.png)

3. Después de proporcionar todos los detalles, elija **Crear**. 

   Logic Apps configura y comprueba la conexión, asegurándose de que la conexión funciona correctamente. 
   Si la conexión se configura correctamente, verá opciones para la acción que ha seleccionado anteriormente. 
   El conector de sistema de archivos ahora está listo para usarse.

4. Especifique que desea copiar archivos desde Dropbox a la carpeta raíz para el recurso compartido de archivos local.

   ![Acción Crear archivo](media/logic-apps-using-file-connector/create-file-filled.png)

5. Después de la aplicación lógica copie el archivo, agregue una acción de Outlook que envíe un correo electrónico para que los usuarios correspondientes lo sepan. Especifique los destinatarios, el título y el cuerpo del correo electrónico. 

   En el selector de contenido dinámico, puede elegir salidas de datos desde el conector de archivos para que pueda agregar más detalles al correo electrónico.

   ![Acción Enviar correo electrónico](media/logic-apps-using-file-connector/send-email.png)

6. Guarde la aplicación lógica. Pruebe la aplicación cargando archivos en Dropbox. El archivo se debe copiar en el recurso compartido local de archivos y debe recibir un correo electrónico acerca de la operación.

   > [!TIP] 
   > Aprenda cómo [supervisar las aplicaciones lógicas](../logic-apps/logic-apps-monitor-your-logic-apps.md).

Enhorabuena, ahora tiene una aplicación lógica que funciona y que se puede conectar a su sistema de archivos local. Intente explorar otras funcionalidades ofrecidas por el conector, por ejemplo:

- Crear archivo
- Enumerar archivos de la carpeta
- Anexar archivo
- Eliminar archivo
- Obtener contenido de archivo
- Obtener contenido de archivo mediante la ruta de acceso
- Obtener metadatos de archivo
- Obtener metadatos de archivo mediante la ruta de acceso
- Enumerar archivos de la carpeta raíz
- Actualizar archivo

## <a name="view-the-swagger"></a>Visualización de Swagger
Vea los [detalles de Swagger](/connectors/fileconnector/). 

## <a name="get-help"></a>Obtener ayuda

Para formular preguntas, o responderlas, y saber lo que hacen otros usuarios de Azure Logic Apps, visite el [foro de Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).

Para ayudar a mejorar Azure Logic Apps y los conectores, vote o envíe ideas en el [sitio de comentarios de usuario de Azure Logic Apps](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Pasos siguientes

- [Conexión a datos locales](../logic-apps/logic-apps-gateway-connection.md) desde aplicaciones lógicas
- Aprenda sobre la [integración empresarial](../logic-apps/logic-apps-enterprise-integration-overview.md).

