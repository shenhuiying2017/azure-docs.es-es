En este tutorial aprenderá a utilizar el desencadenador **Salesforce - When an object is created** (Salesforce: cuando se crea un objeto) para iniciar un flujo de trabajo de aplicación lógica cuando se crea un nuevo cliente potencial en Salesforce.

> [!NOTE]
> Si aún no ha creado una *conexión* a Salesforce se le pedirá que inicie sesión en su cuenta de Salesforce.
> 
> 

1. Escriba *salesforce* en el cuadro de búsqueda del diseñador de Logic Apps y seleccione el desencadenador **Salesforce - When an object is created** (Salesforce: cuando se crea un archivo).  
   ![Imagen 1 de desencadenador de Salesforce](./media/connectors-create-api-salesforce/trigger-1.png)  
2. Se muestra el control **When an object is created** (Cuando se crea un objeto).  
   ![Imagen 2 de desencadenador de Salesforce](./media/connectors-create-api-salesforce/trigger-2.png)  
3. Seleccione el **tipo de objeto** y luego *Lead* (Cliente potencial) en la lista de objetos. En este paso está indicando que va a crear un desencadenador que enviará una notificación a su aplicación lógica cada vez que se cree un nuevo cliente potencial en Salesforce.  
   ![Imagen 3 de desencadenador de Salesforce](./media/connectors-create-api-salesforce/trigger-3.png)  
4. Eso es todo. Ha creado el desencadenador. Sin embargo, debe crear al menos una acción para que esta aplicación lógica sea válida.  
   ![Imagen 4 de desencadenador de Salesforce](./media/connectors-create-api-salesforce/trigger-4.png)  

En este punto, la aplicación lógica está configurada con un desencadenador que activará otros desencadenadores y acciones del flujo de trabajo cuando se cree un nuevo elemento en Salesforce.

<!---HONumber=AcomDC_0727_2016-->
