---
title: Připojení pomocí Javy – Databáze Azure pro MySQL
description: Tento rychlý start obsahuje ukázku kódu Jazyka Java, kterou můžete použít k připojení a dotazování dat z databáze Azure pro MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.custom: mvc, devcenter, seo-java-july2019, seo-java-august2019
ms.topic: quickstart
ms.devlang: java
ms.date: 3/18/2020
ms.openlocfilehash: b5f1cbf2f822f350b1eeba032199676651364d84
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/09/2020
ms.locfileid: "80983819"
---
# <a name="quickstart-use-java-to-connect-to-and-query-data-in-azure-database-for-mysql"></a>Úvodní příručka: Připojení k datům azure pro MySQL a dotazování na javu

V tomto rychlém startu se připojíte k databázi Azure pro MySQL pomocí aplikace Java a ovladače JDBC MariaDB Connector/J. Potom pomocí příkazů SQL můžete dotazovat, vkládat, aktualizovat a odstraňovat data v databázi z platforem Mac, Ubuntu Linux a Windows. 

Toto téma předpokládá, že jste obeznámeni s vývojem pomocí jazyka Java, ale jste na práci s Azure Database for MySQL.

## <a name="prerequisites"></a>Požadavky

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- Databáze Azure pro server MySQL. [Vytvořte databázi Azure pro server MySQL pomocí portálu Azure](quickstart-create-mysql-server-database-using-azure-portal.md) nebo [vytvořte azure databázi pro server MySQL pomocí Azure CLI](quickstart-create-mysql-server-database-using-azure-cli.md).
- Zabezpečení připojení Azure Database for MySQL je nakonfigurované s otevřenou bránou firewall a nastavením připojení SSL nakonfigurovaným pro vaši aplikaci.

## <a name="obtain-the-mariadb-connector"></a>Získání konektoru MariaDB

Pomocí jednoho z následujících přístupů získáte konektor [MariaDB Connector/J:](https://mariadb.com/kb/en/library/mariadb-connector-j/)
   - Použijte balíček Maven [mariadb-java-client](https://search.maven.org/search?q=a:mariadb-java-client) zahrnout [mariadb-java-client závislost](https://mvnrepository.com/artifact/org.mariadb.jdbc/mariadb-java-client) v souboru POM pro váš projekt.
   - Stáhněte si ovladač JDBC [MariaDB Connector/J](https://downloads.mariadb.org/connector-java/) a zahrňte soubor Jar JDBC (například mariadb-java-client-2.4.3.jar) do cesty třídy aplikace. Informace o specifikách cesty ke kurzu třídy, jako je [Apache Tomcat](https://tomcat.apache.org/tomcat-7.0-doc/class-loader-howto.html) nebo [Java SE,](https://docs.oracle.com/javase/7/docs/technotes/tools/windows/classpath.html) naleznete v dokumentaci vašeho prostředí.

## <a name="get-connection-information"></a>Získání informací o připojení

Získejte informace o připojení potřebné pro připojení ke službě Azure Database for MySQL. Potřebujete plně kvalifikovaný název serveru a přihlašovací údaje.

1. Přihlaste se k [portálu Azure](https://portal.azure.com/).
2. V levé nabídce na webu Azure Portal vyberte **Všechny prostředky**a vyhledejte server, který jste vytvořili (například **mydemoserver).**
3. Vyberte název serveru.
4. Na panelu **Přehled** serveru si poznamenejte **Název serveru** a **Přihlašovací jméno správce serveru**. Pokud zapomenete své heslo, můžete ho na tomto panelu také resetovat.
 ![Název serveru Azure Database for MySQL](./media/connect-java/azure-database-mysql-server-name.png)

## <a name="connect-create-table-and-insert-data"></a>Připojení, vytvoření tabulky a vložení dat

Pomocí následujícího kódu se připojte a načtěte data s využitím funkce s příkazem **INSERT** jazyka SQL. Metoda [getConnection()](https://mariadb.com/kb/en/library/about-mariadb-connector-j/#using-drivermanager) slouží k připojení k MySQL. Metody [createStatement()](https://mariadb.com/kb/en/library/about-mariadb-connector-j/#creating-a-table-on-a-mariadb-or-mysql-server) a execute() slouží k odstranění a vytvoření tabulky. Objekt prepareStatement slouží k sestavení příkazů INSERT a metody setString() a setInt() k vázání hodnot parametrů. Metoda executeUpdate() vkládá hodnoty spuštěním příkazu pro každou sadu parametrů. 

Nahraďte parametry host (hostitel), database (databáze), user (uživatel) a password (heslo) hodnotami, které jste zadali při vytváření vlastního serveru a databáze.

```java
import java.sql.*;
import java.util.Properties;

public class CreateTableInsertRows {

    public static void main (String[] args)  throws Exception
    {
        // Initialize connection variables. 
        String host = "mydemoserver.mysql.database.azure.com";
        String database = "quickstartdb";
        String user = "myadmin@mydemoserver";
        String password = "<server_admin_password>";

        Connection connection = null;

        // Initialize connection object
        try
        {
            String url = String.format("jdbc:mariadb://%s/%s", host, database);

            // Set connection properties.
            Properties properties = new Properties();
            properties.setProperty("user", user);
            properties.setProperty("password", password);
            properties.setProperty("useSSL", "true");
            properties.setProperty("verifyServerCertificate", "true");
            properties.setProperty("requireSSL", "false");

            // get connection
            connection = DriverManager.getConnection(url, properties);
        }
        catch (SQLException e)
        {
            throw new SQLException("Failed to create connection to database.", e);
        }
        if (connection != null) 
        { 
            System.out.println("Successfully created connection to database.");
        
            // Perform some SQL queries over the connection.
            try
            {
                // Drop previous table of same name if one exists.
                Statement statement = connection.createStatement();
                statement.execute("DROP TABLE IF EXISTS inventory;");
                System.out.println("Finished dropping table (if existed).");
    
                // Create table.
                statement.execute("CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);");
                System.out.println("Created table.");
                
                // Insert some data into table.
                int nRowsInserted = 0;
                PreparedStatement preparedStatement = connection.prepareStatement("INSERT INTO inventory (name, quantity) VALUES (?, ?);");
                preparedStatement.setString(1, "banana");
                preparedStatement.setInt(2, 150);
                nRowsInserted += preparedStatement.executeUpdate();

                preparedStatement.setString(1, "orange");
                preparedStatement.setInt(2, 154);
                nRowsInserted += preparedStatement.executeUpdate();

                preparedStatement.setString(1, "apple");
                preparedStatement.setInt(2, 100);
                nRowsInserted += preparedStatement.executeUpdate();
                System.out.println(String.format("Inserted %d row(s) of data.", nRowsInserted));
    
                // NOTE No need to commit all changes to database, as auto-commit is enabled by default.
    
            }
            catch (SQLException e)
            {
                throw new SQLException("Encountered an error when executing given sql statement.", e);
            }       
        }
        else {
            System.out.println("Failed to create connection to database.");
        }
        System.out.println("Execution finished.");
    }
}

```

## <a name="read-data"></a>Čtení dat

Pomocí následujícího kódu načtěte data s využitím příkazu **SELECT** jazyka SQL. Metoda [getConnection()](https://mariadb.com/kb/en/library/about-mariadb-connector-j/#using-drivermanager) slouží k připojení k MySQL. Metody [createStatement()](https://mariadb.com/kb/en/library/about-mariadb-connector-j/#creating-a-table-on-a-mariadb-or-mysql-server) a executeQuery() se používají k připojení a spuštění příkazu select. Výsledky se zpracovávají pomocí objektu ResultSet. 

Nahraďte parametry host (hostitel), database (databáze), user (uživatel) a password (heslo) hodnotami, které jste zadali při vytváření vlastního serveru a databáze.

```java
import java.sql.*;
import java.util.Properties;

public class ReadTable {

    public static void main (String[] args)  throws Exception
    {
        // Initialize connection variables.
        String host = "mydemoserver.mysql.database.azure.com";
        String database = "quickstartdb";
        String user = "myadmin@mydemoserver";
        String password = "<server_admin_password>";

        Connection connection = null;

        // Initialize connection object
        try
        {
            String url = String.format("jdbc:mariadb://%s/%s", host, database);

            // Set connection properties.
            Properties properties = new Properties();
            properties.setProperty("user", user);
            properties.setProperty("password", password);
            properties.setProperty("useSSL", "true");
            properties.setProperty("verifyServerCertificate", "true");
            properties.setProperty("requireSSL", "false");
            
            // get connection
            connection = DriverManager.getConnection(url, properties);
        }
        catch (SQLException e)
        {
            throw new SQLException("Failed to create connection to database", e);
        }
        if (connection != null) 
        { 
            System.out.println("Successfully created connection to database.");
        
            // Perform some SQL queries over the connection.
            try
            {
    
                Statement statement = connection.createStatement();
                ResultSet results = statement.executeQuery("SELECT * from inventory;");
                while (results.next())
                {
                    String outputString = 
                        String.format(
                            "Data row = (%s, %s, %s)",
                            results.getString(1),
                            results.getString(2),
                            results.getString(3));
                    System.out.println(outputString);
                }
            }
            catch (SQLException e)
            {
                throw new SQLException("Encountered an error when executing given sql statement", e);
            }       
        }
        else {
            System.out.println("Failed to create connection to database."); 
        }
        System.out.println("Execution finished.");
    }
}
```

## <a name="update-data"></a>Aktualizace dat

Pomocí následujícího kódu změňte data s využitím příkazu **UPDATE** jazyka SQL. Metoda [getConnection()](https://mariadb.com/kb/en/library/about-mariadb-connector-j/#using-drivermanager) slouží k připojení k MySQL. Metody [prepareStatement()](https://docs.oracle.com/javase/tutorial/jdbc/basics/prepared.html) a executeUpdate() slouží k příprav a spuštění příkazu UPDATE. 

Nahraďte parametry host (hostitel), database (databáze), user (uživatel) a password (heslo) hodnotami, které jste zadali při vytváření vlastního serveru a databáze.

```java
import java.sql.*;
import java.util.Properties;

public class UpdateTable {
    public static void main (String[] args)  throws Exception
    {
        // Initialize connection variables. 
        String host = "mydemoserver.mysql.database.azure.com";
        String database = "quickstartdb";
        String user = "myadmin@mydemoserver";
        String password = "<server_admin_password>";

        Connection connection = null;

        // Initialize connection object
        try
        {
            String url = String.format("jdbc:mariadb://%s/%s", host, database);
            
            // set up the connection properties
            Properties properties = new Properties();
            properties.setProperty("user", user);
            properties.setProperty("password", password);
            properties.setProperty("useSSL", "true");
            properties.setProperty("verifyServerCertificate", "true");
            properties.setProperty("requireSSL", "false");

            // get connection
            connection = DriverManager.getConnection(url, properties);
        }
        catch (SQLException e)
        {
            throw new SQLException("Failed to create connection to database.", e);
        }
        if (connection != null) 
        { 
            System.out.println("Successfully created connection to database.");
        
            // Perform some SQL queries over the connection.
            try
            {
                // Modify some data in table.
                int nRowsUpdated = 0;
                PreparedStatement preparedStatement = connection.prepareStatement("UPDATE inventory SET quantity = ? WHERE name = ?;");
                preparedStatement.setInt(1, 200);
                preparedStatement.setString(2, "banana");
                nRowsUpdated += preparedStatement.executeUpdate();
                System.out.println(String.format("Updated %d row(s) of data.", nRowsUpdated));
    
                // NOTE No need to commit all changes to database, as auto-commit is enabled by default.
            }
            catch (SQLException e)
            {
                throw new SQLException("Encountered an error when executing given sql statement.", e);
            }       
        }
        else {
            System.out.println("Failed to create connection to database.");
        }
        System.out.println("Execution finished.");
    }
}
```

## <a name="delete-data"></a>Odstranění dat

Pomocí následujícího kódu odstraňte data s využitím příkazu **DELETE** jazyka SQL. Metoda [getConnection()](https://mariadb.com/kb/en/library/about-mariadb-connector-j/#using-drivermanager) slouží k připojení k MySQL.  Metody [prepareStatement()](https://docs.oracle.com/javase/tutorial/jdbc/basics/prepared.html) a executeUpdate() se používají k přípravě a spuštění příkazu delete. 

Nahraďte parametry host (hostitel), database (databáze), user (uživatel) a password (heslo) hodnotami, které jste zadali při vytváření vlastního serveru a databáze.

```java
import java.sql.*;
import java.util.Properties;

public class DeleteTable {
    public static void main (String[] args)  throws Exception
    {
        // Initialize connection variables.
        String host = "mydemoserver.mysql.database.azure.com";
        String database = "quickstartdb";
        String user = "myadmin@mydemoserver";
        String password = "<server_admin_password>";

        Connection connection = null;

        // Initialize connection object
        try
        {
            String url = String.format("jdbc:mariadb://%s/%s", host, database);
            
            // set up the connection properties
            Properties properties = new Properties();
            properties.setProperty("user", user);
            properties.setProperty("password", password);
            properties.setProperty("useSSL", "true");
            properties.setProperty("verifyServerCertificate", "true");
            properties.setProperty("requireSSL", "false");
            
            // get connection
            connection = DriverManager.getConnection(url, properties);
        }
        catch (SQLException e)
        {
            throw new SQLException("Failed to create connection to database", e);
        }
        if (connection != null) 
        { 
            System.out.println("Successfully created connection to database.");
        
            // Perform some SQL queries over the connection.
            try
            {
                // Delete some data from table.
                int nRowsDeleted = 0;
                PreparedStatement preparedStatement = connection.prepareStatement("DELETE FROM inventory WHERE name = ?;");
                preparedStatement.setString(1, "orange");
                nRowsDeleted += preparedStatement.executeUpdate();
                System.out.println(String.format("Deleted %d row(s) of data.", nRowsDeleted));
    
                // NOTE No need to commit all changes to database, as auto-commit is enabled by default.
            }
            catch (SQLException e)
            {
                throw new SQLException("Encountered an error when executing given sql statement.", e);
            }       
        }
        else {
            System.out.println("Failed to create connection to database.");
        }
        System.out.println("Execution finished.");
    }
}
```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Migrace databáze MySQL do služby Azure Database for MySQL pomocí výpisu a obnovení.](concepts-migrate-dump-restore.md)
