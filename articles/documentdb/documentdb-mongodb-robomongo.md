---
title: Uso de Robomongo para MongoDB con Azure DocumentDB | Microsoft Docs
description: "Obtenga información sobre cómo usar Robomongo con una cuenta de DocumentDB compatible con los protocolos de MongoDB, que ahora se encuentra disponible en versión preliminar."
keywords: Robomongo
services: documentdb
author: AndrewHoh
manager: jhubbard
editor: 
documentationcenter: 
ms.assetid: 352c5fb9-8772-4c5f-87ac-74885e63ecac
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/29/2016
ms.author: anhoh
translationtype: Human Translation
ms.sourcegitcommit: a6aadaae2a9400dc62ab277d89d9a9657833b1b7
ms.openlocfilehash: 141154f9e8236e595f77bd4880c4f63d480da445


---
# <a name="use-robomongo-with-a-documentdb-account-with-protocol-support-for-mongodb"></a>Uso de Robomongo con una cuenta de DocumentDB compatible con los protocolos de MongoDB
Para conectarse a una cuenta de Azure DocumentDB con soporte de protocolo para MongoDB mediante Robomongo, debe hacer lo siguiente:

* Descargar e instalar [Robomongo](https://robomongo.org/)
* Disponer de la cuenta de DocumentDB con soporte de protocolo para la información de la [cadena de conexión](documentdb-connect-mongodb-account.md) de MongoDB

## <a name="connect-using-robomongo"></a>Conectarse mediante Robomongo
Para agregar su cuenta de DocumentDB compatible con los protocolos de MongoDB en la utilidad MongoDB Connections de Robomongo, realice los pasos siguientes.

1. Recupere la cuenta de DocumentDB con compatibilidad de protocolos con la información de cadena de conexión de MongoDB siguiendo [estas](documentdb-connect-mongodb-account.md)instrucciones.

    ![Captura de pantalla de la hoja Cadena de conexión](./media/documentdb-mongodb-robomongo/connectionstringblade.png)
2. Ejecute *Robomongo.exe*.

3. Haga clic en el botón de conexión, que está bajo **File** (Archivo), para administrar las conexiones. Después, haga clic en la opción **Create** (Create) de la ventana **MongoDB Connections**; se abrirá **Connection Settings** (Configuración de conexiones).

4. En la ventana **Connection Settings** (Configuración de conexiones), elija un nombre. Después, busque el **host** y **puerto** en la información de conexión del paso 1 y escríbalos en **Address** (Dirección) y **Port** (Puerto) respectivamente.

    ![Captura de pantalla de la funcionalidad de administración de conexiones de Robomongo](./media/documentdb-mongodb-robomongo/manageconnections.png)
5. En la pestaña **Authentication** (Autenticación), haga clic en **Perform authentication** (Realizar autenticación). Después, escriba la base de datos (el valor predeterminado es *Admin*), el **nombre de usuario** y la **contraseña**.
Tanto el **nombre de usuario** como la **contraseña** están en la información de conexión del paso 1.

    ![Captura de pantalla de la pestaña de autenticación de Robomongo](./media/documentdb-mongodb-robomongo/authentication.png)
6. En la pestaña **SSL**, marque **Use SSL protocol** (Usar protocolo SSL) y, luego, cambie el **método de autenticación** a **Self-signed Certificate** (Certificado autofirmado).

    ![Captura de pantalla de la pestaña SSL de Robomongo](./media/documentdb-mongodb-robomongo/SSL.png)
7. Por último, haga clic en **Test** (Probar) para comprobar que puede conectarse. Después, seleccione **Save** (Guardar).

## <a name="next-steps"></a>Pasos siguientes
* Explore DocumentDB con soporte de protocolo para buscar [ejemplos](documentdb-mongodb-samples.md)de MongoDB.



<!--HONumber=Jan17_HO4-->


