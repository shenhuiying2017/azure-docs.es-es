---
redirect_url: https://docs.microsoft.com/azure/documentdb/documentdb-performance-levels
ROBOTS: NOINDEX, NOFOLLOW
translationtype: Human Translation
ms.sourcegitcommit: 94ae24e605f99e23766702a3954db567bb64bb51
ms.openlocfilehash: 1c055187405b9c858676b2b80e4bc5c4157ef580


---
# <a name="migrate-your-documentdb-s1-s2-or-s3-account"></a>Migración de la cuenta de DocumentDB S1, S2 o S3
Para aprovechar la mayor capacidad de proceso de la cuenta de Azure DocumentDB, muévala al plan de tarifa Estándar. Para ello, siga estos pasos. Con poco o sin ningún costo adicional, puede aumentar el rendimiento de su cuenta S1 existente de 250 [RU/s](documentdb-request-units.md) a 400 RU/s o más. Todas las cuentas de nivel Estándar se benefician de la posibilidad de escalar el rendimiento para satisfacer las necesidades de sus aplicaciones. Ya no necesita elegir entre las opciones de rendimiento predefinidas; puede escalarlo siempre que tenga que conseguir el rendimiento que necesita su aplicación. 

## <a name="change-to-user-defined-performance-in-the-azure-portal"></a>Cambio al rendimiento definido por el usuario en el Portal de Azure
1. En el explorador, vaya a [**Azure Portal**](https://portal.azure.com). 
2. En el menú de la izquierda, haga clic en **NoSQL (DocumentDB)** o en **Más servicios**, desplácese a **Bases de datos** y luego seleccione **NoSQL (DocumentDB)**.   
3. En la hoja **NoSQL (DocumentDB)**, seleccione la cuenta que quiere modificar.
4. En la hoja nueva, en el menú situado bajo **Colecciones**, haga clic en **Escala**. 

      ![Captura de pantalla de las hojas Configuración y Elija un plan de tarifa de DocumentDB](./media/documentdb-supercharge-your-account/documentdb-change-performance.png)
5. Haga lo siguiente, como se muestra en le captura de pantalla anterior: 

 - En la nueva hoja, use el menú desplegable para seleccionar la colección con el plan de tarifa S1, S2 o S3. 
 - Haga clic en **Plan de tarifa S1**, **S2** o **S3**.
 - En la hoja **Elegir su nivel de precios**, haga clic en **Estándar** y luego haga clic en **Seleccionar** para guardar el cambio.
   
6. En la hoja **Escala**, el cuadro **Procesamiento (RU/s)** se muestra con un valor predeterminado de 400 y el valor de **Plan de tarifa** cambia a **Estándar**.  Puede cambiar el rendimiento a cualquier valor que requiera la aplicación. Se recomienda usar el [planificador de capacidad de DocumentDB](https://www.documentdb.com/capacityplanner) para determinar cuántas [unidades de solicitud ](documentdb-request-units.md)/seg. (RU/s) requiere la aplicación. La opción **Factura mensual estimada** en la parte inferior de la página se actualiza automáticamente para ofrecer una estimación del costo mensual. Haga clic en **Guardar** para guardar los cambios. 
      
    ![Captura de pantalla de la hoja Configuración que muestra dónde cambiar el valor de rendimiento](./media/documentdb-supercharge-your-account/documentdb-change-performance-set-thoughput.png)
7. La colección se escala para satisfacer los requisitos nuevos y se muestra un mensaje de confirmación.  
   
    ![Captura de pantalla de la hoja Base de datos con la colección modificada](./media/documentdb-supercharge-your-account/documentdb-change-performance-confirmation.png)

Para obtener más información sobre los cambios de precio relativos al rendimiento predefinido y definido por el usuario, consulte la entrada del blob [DocumentDB: Everything you need to know about using the new pricing options](https://azure.microsoft.com/blog/documentdb-use-the-new-pricing-options-on-your-existing-collections/)(DocumentDB: todo lo que necesita saber sobre el uso de las nuevas opciones de precio).

## <a name="next-steps"></a>Pasos siguientes

Aprenda más sobre cómo crear particiones de datos y conseguir el escalado global con DocumentDB en [Partición y escalado en Azure DocumentDB](documentdb-partition-data.md).



<!--HONumber=Feb17_HO2-->


