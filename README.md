# ADFv2Tutorial

In this [tutorial](https://learn.microsoft.com/en-us/azure/data-factory/tutorial-copy-data-dot-net), you create a Data Factory pipeline that copies data from Azure Blob Storage to Azure SQL Database. The configuration pattern in this tutorial applies to copying from a file-based data store to a relational data store. For a list of data stores supported as sources and sinks, see [supported data stores and formats](https://learn.microsoft.com/en-us/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats).

This tutorial uses .NET SDK. You can use other mechanisms to interact with Azure Data Factory; refer to samples under **Quickstarts**.

If you don't have an Azure subscription, create a [free Azure account](https://azure.microsoft.com/free/) before you begin.

## Prerequisites

- Azure Storage account. You use the blob storage as source data store. If you don't have an Azure storage account, see [Create a general-purpose storage account](https://learn.microsoft.com/en-us/azure/storage/common/storage-account-create).
- Azure SQL Database. You use the database as sink data store. If you don't have a database in Azure SQL Database, see the [Create a database in Azure SQL Database](https://learn.microsoft.com/en-us/azure/azure-sql/database/single-database-create-quickstart).
- Visual Studio. The walkthrough in this article uses Visual Studio 2019.
- [Azure SDK for .NET](https://learn.microsoft.com/en-us/dotnet/azure/dotnet-tools).
- Azure Active Directory application. If you don't have an Azure Active Directory application, see the [Create an Azure Active Directory application](https://learn.microsoft.com/en-us/azure/active-directory/develop/howto-create-service-principal-portal#register-an-application-with-azure-ad-and-create-a-service-principal) section of [How to: Use the portal to create an Azure AD application](https://learn.microsoft.com/en-us/azure/active-directory/develop/howto-create-service-principal-portal). Copy the following values for use in later steps: **Application (client) ID**, **authentication key**, and **Directory (tenant) ID**. Assign the application to the **Contributor** role by following the instructions in the same article.

### Create a blob and a SQL table

Now, prepare your Azure Blob and Azure SQL Database for the tutorial by creating a source blob and a sink SQL table.

#### Create a source blob

First, create a source blob by creating a container and uploading an input text file to it:

1. Open Notepad. Copy the following text and save it locally to a file named *inputEmp.txt*.

```txt
John|Doe
Jane|Doe
```

2. Use a tool such as [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) to create the adfv2tutorial container, and to upload the *inputEmp.txt* file to the container.

#### Create a sink SQL table

Next, create a sink SQL table:

1. Use the following SQL script to create the dbo.emp table in your Azure SQL Database.

```sql
CREATE TABLE dbo.emp
(
    ID int IDENTITY(1,1) NOT NULL,
    FirstName varchar(50),
    LastName varchar(50)
)
GO

CREATE CLUSTERED INDEX IX_emp_ID ON dbo.emp (ID);
```

2. Allow Azure services to access SQL Database. Ensure that you allow access to Azure services in your server so that the Data Factory service can write data to SQL Database. To verify and turn on this setting, do the following steps:

    - Go to the [Azure portal](https://portal.azure.com/) to manage your SQL server. Search for and select **SQL servers**.

    - Select your server.

    - Under the SQL server menu's **Security** heading, select **Firewalls and virtual networks**.

    - In the **Firewall and virtual networks** page, under **Allow Azure services and resources to access this server**, select **ON**.
