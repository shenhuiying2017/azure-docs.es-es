Esta condición evaluará el campo de dirección de correo electrónico de cada nuevo cliente potencial de Salesforce. Si la dirección de correo electrónico contiene *amazon.com*, el resultado de la condición será *True*.

1. Seleccione **+ New step**(+ Nuevo paso).  
   ![Imagen 1 de condición de Salesforce](./media/connectors-create-api-salesforce/condition-1.png)   
2. Seleccione **Agregar una condición**.    
   ![Imagen 2 de condición de Salesforce](./media/connectors-create-api-salesforce/condition-2.png)  
3. Seleccione **Choose a value**(Elegir un valor).    
   ![Imagen 3 de condición de Salesforce](./media/connectors-create-api-salesforce/condition-3.png)  
4. Seleccione el token *Correo electrónico* del cliente potencial del desencadenador.    
   ![Imagen 4 de condición de Salesforce](./media/connectors-create-api-salesforce/condition-4.png)  
5. Seleccione *Contiene*.      
   ![Imagen 5 de condición de Salesforce](./media/connectors-create-api-salesforce/condition-5.png)  
6. Seleccione **Choose a value** (Elegir un valor) en la parte inferior del control.     
   ![Imagen 6 de condición de Salesforce](./media/connectors-create-api-salesforce/condition-6.png)  
7. Escriba *amazon.com* como el valor para el que desea evaluar la dirección de correo electrónico del nuevo cliente potencial. Si la dirección de correo electrónico contiene *amazon.com*, la condición se evaluará como *True* y se puede continuar con el resto de los pasos de la aplicación lógica.    
   ![Imagen 7 de condición de Salesforce](./media/connectors-create-api-salesforce/condition-7.png)  
8. Guarde las aplicaciones lógicas.  

