# Workshop 2: Secure Movie analytics pipeline

*Workshop cloned and enhanced originally from https://github.com/djpmsft/ADF_Labs/blob/master/MovieAnalytics_ADLS.md *

*If you're new to Azure Data Factory, see [Introduction to Azure Data Factory](https://docs.microsoft.com/azure/data-factory/introduction).*

In this Lab, you will enhance workshop 1: MovieAnalytics in which Azure Data Factory's visual authoring experience to create a pipeline that copies movie data stored in Azure Data Lake Storage Gen2 to a separate location in that storage account and then executes a Mapping Data Flow to transform and write the data to a Azure SQL Database.

In this workshop, this pipeline will be secured. The following steps are taken:


- **Authentication** - Authorization: Connect to Storage account and Azure SQL using System Assigned Managed Identities rather than keys
- **Networking**: Connect to Storage account and Azure SQL using private endpoints rather than public internet
- **Parametrisation**: Parameterize pipeline to facilitate unit testing and deployment to dev/tst/prd

## Prerequisites

* **Azure subscription**: If you don't have an Azure subscription, create a [free account](https://azure.microsoft.com/free/) before you begin.

* **Azure Data Lake Storage Gen2 storage account**: If you don't have an ADLS Gen2 storage account, see the instructions in [Create an ADLS Gen2 storage account](https://docs.microsoft.com/en-us/azure/storage/blobs/data-lake-storage-quickstart-create-account).

* **Azure SQL Database**: If you don't have an Azure SQL Database, see the instructions in [Create an Azure SQL database](https://learn.microsoft.com/en-us/azure/azure-sql/database/single-database-create-quickstart?view=azuresql&tabs=azure-portal). Database is needed in the last step to write the data to, an empty database can be created.

## Setting up your environment

**Create your data factory:** Use the [Azure Portal](https://portal.azure.com) to create your Data Factory. Detailed instructions can be found at [Create a Data Factory](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-portal). 

1. Once in the Azure Portal, click on the **All Services** button on the left hand-side and select "Data Factories" in the Analytics section.
    ![Azure Portal](./assets/MovieAnalytics/portal1.png)

1. Click **Add** to open the Data Factory creation screen
    ![Azure Portal](./assets/MovieAnalytics/portal2.png)

1. Specify your Data Factory configuration settings in the creation pane. Choose a globally unique data factory name and select your subscription, resource group, and region. Your data factory must be version V2. Once you are done, click **Create**. Your data factory may take a couple minutes to deploy.
    * Mapping Data Flow is not currently available in the following data factory regions: West Central US, Korea Central and France Central. For the purposes of this lab, please do not create your data factory in one of these reasons. 
    * ADF's integration with Azure DevOps and Github will not be covered in this lab. To enable this feature, check **Enable Git** and specify your configuration information. See [Source Control in Azure Data Factory](https://docs.microsoft.com/azure/data-factory/source-control#troubleshooting-git-integration).
    ![Azure Portal](./assets/MovieAnalytics/portal3.png)

1. Make sure that the option "Enable Managed VNET is selected".

    ![Azure Portal](./new_images_rbr/100_managedVNET.png)


## Deploy Movie analytics pipeline

1. There are two possibilities to create the movie analytics pipeline:
   - Follow the steps in [Workshop 1](./Workshop_1_MovieAnalytics.md). Depending on your experience, it takes 1-2 hours to deploy pipeline in 
   - Deploy [ARM template](./demo-adfmovie-adf/ARMTemplateForFactory.json) template in the demo-adfmovie-adf directory

In the remainder of this chapter, template deployment is described. 

1. Go to the Azure Portal, go to the search bar in the top and enter **Deploy a custom template** in the bar. Then select option **Build your own template in the editor**
    ![Azure Portal](./new_images_rbr/101_custom_template.png)

1. Copy the content of [ARM template](./demo-adfmovie-adf/ARMTemplateForFactory.json) and click **save**

    ![Azure Portal](./new_images_rbr/102_custom_template_edit.png)
    

1. Fill in the required parameters in the content of [Deploy ADF](./new_images_rbr/103_deploy_adf_arm.png). For Parameters, use the following:
   - Resource group: Resource group in which ADF with managed VNET is deployed
   - Factory name: Data Factory name deployed earlier
   - ADLS_account Key: Account key of ADLS storage account deployed earlier
   - Azure SQL_connection String: Connection string deployed earlier. SQL shall have the following form: `integrated security=False;encrypt=True;connection timeout=30;data source=<<your sql server name>>.windows.net;initial catalog=<your database name>>;user id=<<your username>>;password=<<your password>>`
   - ADLS properties URL: URL of your storage account

1. Then click **Review + create** and check whether pipeline was succesfully deployed.

    ![Azure Portal](./new_images_rbr/104_ADF_deployment.png)


## Turn on data flow debug mode

In section *Transforming Data with Mapping Data Flow*, you will be building mapping data flows. A best practice before building mapping data flows is to turn on debug mode which allows you to test transformation logic in seconds on an active spark cluster.

To turn on debug, click the **Data flow debug** slider in the factory top bar. Click ok when the confirmation dialog pop-ups. The cluster will take about 5-7 minutes to start-up. Continue on to *Ingesting data into Azure Data Lake Storage Gen2* while it is initializing.

![Data Flow](./assets/MovieAnalytics/dataflow1.png)
  
## Running the Pipeline

Go to the pipeline canvas. In the Execute Data Flow activity's settings tab, you will see the data flow selected and the compute environment used. In this lab, you should use the default 4 core general purpose cluster. 

Before you publish your pipeline, run another debug run to confirm it's working as expected. 

![Data Flow](./assets/MovieAnalytics/pipeline-debug.png)

Look at the **Output** tab, you can monitor the status of both activities as they are running. You can click on the eyeglasses icon next to the Data Flow activity to get a more in depth look at the Data Flow run.

![Data Flow](./assets/MovieAnalytics/pipeline-debug2.png)

In the detailed view, you can see how long each transformation stage takes, view each transformations partitioning information and see how long it took to write to the sink.

![Data Flow Monitoring](./assets/MovieAnalytics/DataFlowMonitoring.PNG "Data Flow monitoring")

The data flow activity should complete in about one minute. If you used the same logic described in this lab, your Data Flow should will written 737 rows to your Azure SQL Database. You can go into [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017) to verify the pipeline worked correctly and see what got written.


## Authentication - Authorization: Connect to Storage account and Azure SQL using System Assigned Managed Identities rather than keys##

<<todo>>

## Networking - Connect to Storage account and Azure SQL using private endpoints rather than public internet

<<todo>>

## Parametrisation - Parameterize pipeline to facilitate unit testing and deployment to dev/tst/prd

<<todo>>