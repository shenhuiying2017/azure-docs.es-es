---
title: "Conexión a sistemas de archivos locales: Azure Logic Apps | Microsoft Docs"
description: "Conexión a sistemas de archivos locales desde flujos de trabajo de aplicaciones lógicas a través de la puerta de enlace de datos local y el conector del sistema de archivos"
keywords: sistemas de archivos, locales
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
ms.date: 09/18/2017
ms.author: LADocs; deli
ms.openlocfilehash: 32ab5be41a8dee3b1f2c0b1bde076c0d1a844bdd
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/19/2018
---
# <a name="connect-to-on-premises-file-systems-from-logic-apps-with-the-file-system-connector"></a>Conexión a sistemas de archivos locales desde las aplicaciones lógicas con el conector de sistema de archivos

Para administrar datos y acceder de forma segura a recursos locales, las aplicaciones lógicas pueden utilizar la puerta de enlace de datos local. En este artículo se muestra cómo conectar a un sistema de archivos local a través de un escenario de ejemplo básico: copiar un archivo que se carga en Dropbox a un recurso compartido de archivos y, después, enviar un correo electrónico.

## <a name="prerequisites"></a>requisitos previos

* Descargue la [puerta de enlace de datos local](https://www.microsoft.com/download/details.aspx?id=53127) más reciente.

* Instale y configure la puerta de enlace de datos local más reciente, versión 1.15.6150.1 o superior. Para conocer los pasos, consulte [Obtener acceso a orígenes de datos locales desde aplicaciones lógicas con la puerta de enlace de datos local](http://aka.ms/logicapps-gateway). La puerta de enlace se debe instalar en una máquina local para poder continuar con estos pasos.

* Conocimientos básicos acerca de [cómo crear aplicaciones lógicas](../logic-apps/quickstart-create-first-logic-app-workflow.md)

## <a name="add-trigger-and-actions-for-connecting-to-your-file-system"></a>Incorporación de desencadenadores y acciones para conectarse al sistema de archivos

1. Crear una aplicación lógica en blanco. Agregue este desencadenador como primer paso: **Dropbox - Cuando se crea un archivo** 

2. En el desencadenador, elija **Paso siguiente** > **Agregar una acción**. 

3. En el cuadro de búsqueda, escriba "sistema de archivos" como filtro. Cuando vea todas las acciones del conector Sistema de archivos, elija la acción **Sistema de archivos - Crear archivo**. 

   ![Conector Buscar archivo](media/logic-apps-using-file-connector/search-file-connector.png)

4. Si todavía no tiene ninguna conexión al sistema de archivos, se le pedirá que la cree ahora. 

5. Seleccione **Connect via on-premises data gateway** (Conectarse a través de la puerta de enlace de datos local). Cuando aparezcan las propiedades de la conexión, configure la conexión de acuerdo con lo que se especifica en la tabla.

   ![Configuración de la conexión](media/logic-apps-using-file-connector/create-file.png)

   | Configuración | DESCRIPCIÓN |
   | ------- | ----------- |
   | **Carpeta raíz** | Especifique la carpeta raíz del sistema de archivos. Puede especificar una carpeta local en la máquina en la que está instalada la puerta de enlace de datos local; o bien, la carpeta puede ser un recurso compartido de red al que tiene acceso la máquina. <p>**Sugerencia**: La carpeta raíz es la carpeta primaria principal y se usa para las rutas de acceso relativas para todas las acciones relacionadas con archivos. | 
   | **Tipo de autenticación** | El tipo de autenticación que usa el sistema de archivos | 
   | **Nombre de usuario** | Especifique el nombre de usuario {*dominio*\\*nombre de usuario*} para la puerta de enlace que ha instalado anteriormente. | 
   | **Password** | Especifique la contraseña de la puerta de enlace que ha instalado anteriormente. | 
   | **Gateway** | Seleccione la puerta de enlace que ha instalado anteriormente. | 
   ||| 

6. Después de especificar todos los detalles de la conexión, elija **Crear**. 

   Logic Apps configura y comprueba la conexión, asegurándose de que la conexión funciona correctamente. 
   Si la conexión está configurada correctamente, aparecen las opciones de la acción que ha seleccionado anteriormente. 
   El conector de sistema de archivos ahora está listo para usarse.

7. Configure la acción **Crear archivo** para copiar archivos desde Dropbox a la carpeta raíz del recurso compartido de archivos local.

   ![Acción Crear archivo](media/logic-apps-using-file-connector/create-file-filled.png)

8. Después de esta acción para copiar el archivo, agregue una acción de Outlook que envíe un correo electrónico para que los usuarios relevantes sepan que existe un archivo nuevo. Especifique los destinatarios, el título y el cuerpo del correo electrónico. 

   En la lista **Contenido dinámico**, puede elegir salidas de datos del conector de archivos, con el fin de que pueda agregar más detalles al correo electrónico.

   ![Acción Enviar correo electrónico](media/logic-apps-using-file-connector/send-email.png)

9. Guarde la aplicación lógica. Pruebe la aplicación cargando archivos en Dropbox. El archivo se debe copiar en el recurso compartido local de archivos y debe recibir un correo electrónico acerca de la operación.

Enhorabuena, ahora tiene una aplicación lógica que funciona y que se puede conectar a su sistema de archivos local. 

Intente explorar otras funcionalidades ofrecidas por el conector, por ejemplo:

- Crear archivo
- Enumerar archivos de la carpeta
- Anexar archivo
- Eliminar archivo
- Obtener contenido de archivo
- Obtener contenido de archivo mediante la ruta de acceso
- Obtención de metadatos de archivo
- Obtener metadatos de archivo mediante la ruta de acceso
- Enumerar archivos de la carpeta raíz
- Actualizar archivo

## <a name="view-the-swagger"></a>Visualización de Swagger

Vea los [detalles de Swagger](/connectors/fileconnector/). 

## <a name="get-support"></a>Obtención de soporte técnico

* Si tiene alguna duda, visite el [foro de Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).

* Para ayudar a mejorar Azure Logic Apps y los conectores, vote o envíe ideas en el [sitio de UserVoice de Azure Logic Apps](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>pasos siguientes

* [Conexión a datos locales](../logic-apps/logic-apps-gateway-connection.md) 
* [Supervisar las aplicaciones lógicas](../logic-apps/logic-apps-monitor-your-logic-apps.md)
* [Introducción: Escenarios B2B y comunicación con Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md)
