1. En una nueva ventana, inicie sesión en [Azure Portal](https://portal.azure.com/).
2. En el panel de la izquierda, haga clic en **Nuevo**, luego en **Bases de datos** y, después, en **la base de datos de Azure Cosmos**.
   
   ![El panel de las bases de datos de Azure Portal](./media/cosmos-db-create-dbaccount/create-nosql-db-databases-json-tutorial-1.png)

3. En la hoja **Nueva cuenta**, especifique la configuración que quiera para la cuenta de la base de datos de Azure Cosmos. 

    Con Azure Cosmos DB, puede elegir uno de cuatro modelos de programación: Gremlin (gráfico), MongoDB, SQL (DocumentDB) y Table (clave-valor). 
    
    En este artículo de guía de inicio rápido programaremos con la API DocumentDB, así que elija **SQL (DocumentDB)** al rellenar el formulario. Pero si tiene datos de gráficos para una aplicación de redes sociales, datos de clave/valor (tabla) o datos migrados desde una aplicación de MongoDB, debe tener en cuenta que la base de datos de Azure Cosmos puede proporcionar una plataforma de servicio de base de datos distribuida globalmente y de alta disponibilidad para todas las aplicaciones críticas.

    Rellene los campos en la hoja **Nueva cuenta** usando la información de la captura de pantalla siguiente como guía. Al configurar su cuenta, elija valores únicos que no coincidan con los de la captura de pantalla. 
 
    ![La nueva hoja de la base de datos de Azure Cosmos](./media/cosmos-db-create-dbaccount/create-nosql-db-databases-json-tutorial-2.png)

    Configuración|Valor sugerido|Descripción
    ---|---|---
    ID|*Valor único*|Un nombre único que identifica la cuenta de la base de datos de Azure Cosmos. La cadena *documents.azure.com* se anexará al identificador que proporcione para crear el URI, por lo que debe usar un identificador único pero reconocible. El identificador puede contener solo letras minúsculas, números y el carácter guion (-), y tiene que tener una extensión de entre 3 y 50 caracteres.
    API|SQL (DocumentDB)|Más adelante en este artículo programaremos con la [API DocumentDB](../articles/documentdb/documentdb-introduction.md).|
    La suscripción|*Su suscripción*|Suscripción de Azure que quiere usar para la cuenta de Azure Cosmos DB. 
    Grupo de recursos|*Mismo valor que el identificador*|Nombre del nuevo grupo de recursos para la cuenta. Para simplificar, puede usar el mismo nombre del identificador. 
    Ubicación|*Región más cercana a los usuarios*|Ubicación geográfica en la que se va a hospedar la cuenta de Azure Cosmos DB. Elija la ubicación más cercana a los usuarios para proporcionarles el acceso más rápido a los datos.
4. Haga clic en **Crear** para crear la cuenta.
5. En la barra de herramientas superior, haga clic en **Notificaciones** para supervisar el proceso de implementación.

    ![El panel de las notificaciones de Azure Portal](./media/cosmos-db-create-dbaccount-graph/azure-documentdb-nosql-notification.png)

6.  Una vez completada la implementación, abra la nueva cuenta desde el icono **All Resources** (Todos los recursos). 

    ![Cuenta de DocumentDB en el icono All Resources (Todos los recursos)](./media/cosmos-db-create-dbaccount/all-resources.png)
 
