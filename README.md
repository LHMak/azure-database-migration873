# Azure Database Migration

# Project Description
The goal of this project was to migrate a local SQL database to the cloud using an Azure SQL Database. The local database was hosted on an SQL server on a Virutal Machine (VM).
The AdventureWorks sample database created by Microsoft was used for the purposes of this project.
This project was split into 7 milestones. After each milestone, any relevant files were uploaded to this repository and this README was updated to document my progress.

## Milestone 1: Set up the Environment
In this milestone, this GitHub repo was created and I set up my account on Azure.

## Milestone 2: Set up the Production Environment
In this milestone, I provisioned a Windows VM which would be treated as my production environent. This production VM hosted the company's database (AdventureWorks sample database), serving as a secure, dedicated storage solution. SQL Server and Server Management Studio (SSMS) was installed on this VM to manage the database. 
- I provisioned a Windows 11 pro VM using the Standard B2ms storage size. This size is good for many workloads and so will be sufficient for this project.
- I connected securely to the newly created VM using the RDP protocol and installed SQL Server and SSMS
- After this, I created the company's database by restoring from a .bak back up file of the AdventureWorks sample database.

## Milestone 3: Migrate to Azure SQL Database
The focus of this milestone was to transition the on-premise database (source), which was stored on the production environent, to the Azure ecosystem. This was achieved by using Azure Data Studio to perform the database migration.

### Set Up Azure SQL Database

I began this part of the project by by provisioning an SQL server and a corresponding Azure SQL Database. This database would be the target of the migration. The server used SQL login as it's authentication method:

<img width="469" alt="m3-1 deploy database" src="https://github.com/LHMak/azure-database-migration873/assets/147920042/65006a6d-44a5-4941-8cd3-cdeba8eaf4f8">

_Image showing successful deployment of database_

I then allowed public network access to the server. This would allow my local PC and the production VM to connect to the database. I validated this by connecting to the database in VS Code on my local PC:

<img width="280" alt="m3-1 vs code connected" src="https://github.com/LHMak/azure-database-migration873/assets/147920042/1018ae27-605c-4608-9763-a7e41aaba423">

_Image showing successful connection to the Azure SQL database in VS Code_

### Prepare for Migration

After this, I installed Azure Data Studio (ADS) on the production VM hosting the local database and connected ADS to the database:

<img width="349" alt="m3-2 connect ADS to local db" src="https://github.com/LHMak/azure-database-migration873/assets/147920042/ebfd82ec-3742-4d19-8a72-bdec17a0b2b0">

_Image from VM showing an active connection in ADS to the local database_

Similarly, I connected ADS to the target Azure SQL database:

<img width="332" alt="m3-3 connect ADS to Azure db" src="https://github.com/LHMak/azure-database-migration873/assets/147920042/ff8c67cb-e8da-49d5-8b72-f5dcf580a00d">

_Image from VM showing an active connection between ADS and the target Azure SQL database_

### Schema Migration

At this point, everything was ready to begin the database migration. The migration was split into two stages, each utilising an extension for ADS- these were "SQL Server Schema Compare" and "Azure SQL Migration"

SQL Server Schema Compare was used to compare and migrate the schemas of the local database to the target Azure database. This was to simmplify and streamline the migration process: 

<img width="470" alt="m3-4 Schema Migration" src="https://github.com/LHMak/azure-database-migration873/assets/147920042/42a746e2-9603-48c6-b373-a1349b1eaf0c">

_Image showing the SQL Server Schema Compare extension for ADS_

Once the schema comparison had completed, I was then able to apply all the changes (As the target Azure database had no tables or data, all changes were applied):

<img width="722" alt="m3-4 Schema compare" src="https://github.com/LHMak/azure-database-migration873/assets/147920042/ba94c712-8b26-4756-bff3-aad60b854c04">

_Image showing the application of schema changes from local database to target Azure database_

After this, the target Azure database contained the same tables as the local database (though at this point they currently contianed no data):

<img width="331" alt="m3-4 Schema migration complete" src="https://github.com/LHMak/azure-database-migration873/assets/147920042/650ae791-f6fb-401f-a2f3-86018943c7b7">

_Image showing successful migration of schema from local database to target Azure database_

### Data Migration

Azure SQL Migration was use to migrate the data to the target Azure database:

<img width="470" alt="m3-5 Data migration extension" src="https://github.com/LHMak/azure-database-migration873/assets/147920042/6ed47b99-655f-459c-9f08-f526e8876389">

_Image showing the Azure SQL Migration extension for ADS_

The migration process was made rather simple using this extension, as it used a wizard. I followed the instructions given, starting by selecting my local database (source):

<img width="470" alt="m3-5 Data migration wizard" src="https://github.com/LHMak/azure-database-migration873/assets/147920042/1413da7f-ca82-4103-82a9-1c9b2e74fd7d">

_Image showing the Azure SQL Migration wizard_

#### Problem
Once I hit step 4 of the wizard I encountered my first issue of the project:

<img width="470" alt="m3-5 Data migration issue" src="https://github.com/LHMak/azure-database-migration873/assets/147920042/7726fa65-fc5e-484f-8be6-496a1556e9fe">

_Image showing step 4 of the Azure SQL Migration wizard where I encountered an issue._

From the screenshot you can see that the wizard asks the user to select items from a series of dropdown menus in order to select the target database. The issue I experienced occured after I selected the relevant Entra tenant, subscription and location. After this stage, I needed to select the resource group containing the target Azure database- however, a resource group in a completely separate Entra tenant was the only selectable option.

This was due to the fact my Azure account contained two directories- one personal directory, used for learning the Azure platform; another for this migration project. The wizard only allowed me to select the resource group and server from my personal directory and not those from the migration project directory.

#### Solution
After taking multiple approaches to fixing this issue such as assigning my account various different permissions, reinstalling ADS and manually inputting the resource group and server I intended to choose, I found a solution. 

I deleted my personal directory so that only the migration project directory existed on my account. This ensured that ADS could only 'see' the resource group relating the my project's directory and so, I was able to successfully connect to the target Azure database: 

<img width="470" alt="m3-5 Data migration issue solved" src="https://github.com/LHMak/azure-database-migration873/assets/147920042/047624a1-7e51-4d45-9d67-004310a4d30b">

_Image showing successful connection to target Azure database in the Azure SQL Migration wizard_

Once this issue had been resolved, I was able to continue to the next step of the wizard. This step instructed me to install Microsoft Integration Runtime Configuration Manager on the VM and supply it with an authentication key from ADS. This handles connectivity between the local (source) database and the target Azure database. 

After continuing through the remaining steps, I was able to begin the migration process and after waiting a few minutes, the migration was complete:

<img width="784" alt="m3-5 Data migration complete" src="https://github.com/LHMak/azure-database-migration873/assets/147920042/e2664f5f-4046-425f-9c6f-b31843846958">

_Image showing the compelted database migration_

## Milestone 4: Data Back up and Restore
In this milestone, I focused on ensuring the data stored in the production environment database was stored securely on Azure. I then created a development environment which could be used for developing and testing new features, etc. Finally, I set up an automated back up routine for the development environment to safeguard any ongoing work in case of errors with testing and development.

### Back up the On-Premise Database
I began this milestone of project by backing up the database hosted in the production environent. I connected to the production VM and used SSMS to take a back up of the the database. When configuring the back up, I chose a Full back up so that I could clone the database once I created a development environment.

<img width="584" alt="m4-2 creating backup" src="https://github.com/LHMak/azure-database-migration873/assets/147920042/6c75876b-f152-4273-9c34-24aeb731bf7c">

_Image showing back up configuration in SSMS_

### Upload Back up to Blob Storage
I then created an Azure storage account to serve as a secure cloud repository for database back ups. Once this was deployed, I then created a Blob Storage container to store the back up file I had created. I gave it the 'Container' access level, as this would allow anonymous connections (i.e. production VM and development VM) to connect to it and upload files.

<img width="744" alt="m4-3 deployed container" src="https://github.com/LHMak/azure-database-migration873/assets/147920042/a363e304-24eb-411e-b197-f430c21d6f33">

_Image showing the deployed Azure Blob Storage container where the production database back up will be stored_

I then stored the back up file of the database.

<img width="915" alt="m4-3 backup file saved" src="https://github.com/LHMak/azure-database-migration873/assets/147920042/122adc76-085b-427c-8e95-f8d1b90b926c">

_Image showing the production database back up file is stored in the Azure Blob Storage container_

### Restore Database on Development Environment
Next, I provisioned another VM to serve as the development enironment. This development VM was configured in the same way as the production VM (e.g. Windows 11 Pro, SQL Server and SSMS installed).

I downloaded the back up file I stored in the Azure Blob Storage container and restored the database on this new development environemnt in SSMS. I did this using the 'Restore Database' feature:

<img width="659" alt="m4-3 database restored" src="https://github.com/LHMak/azure-database-migration873/assets/147920042/90f292be-04cc-4a0f-b6ba-11b7c1cc4252">

_Image showing successful restoration of the production database from the stored back up file_

With this, the development environment had a complete clone of the database hosted in the production environment. This clone could be used for any kind of development purpose, such as testing new features without any worry of affecting the live production database.

### Automate Back ups for Development Database
To ensure consistent protection of the development database, which could in theory be ever changing and evolving, an automated back up routine was set up. This would allow the development database to be restored to a recent point in time in case of an error.

SQL Server Agent was started to enable scheduling and management of periodic backups.

<img width="783" alt="m4-4 start sql server agent" src="https://github.com/LHMak/azure-database-migration873/assets/147920042/d79b8767-8a4c-4bcb-9ff6-d967d3dbe257">

_Image showing the SQL Server Agent being started_

I then created an SQL Server Credential in SSMS to allow SQL Server to connect to the Azure Blob Storage container I created for back ups. This would allow SSMS on the development VM to store back ups to the Azure blob.
To create the credential, I ran a T-SQL query on the server in SSMS. The query took the following format:

<img width="903" alt="m4-4 sql server credential" src="https://github.com/LHMak/azure-database-migration873/assets/147920042/65925ed3-da59-453a-b98c-2ac253ebee3e">

_Image showing the T-SQL query used to create the SQL Server Credential_

I replaced the items in squared brackets ( [] ) with a name for the credential and the storage account name respectively. I then acquired the access key from the ‘Access Keys’ page of the Azure storage account. Then I ran the query, creating the credential.

<img width="558" alt="m4-4 sql server credential created" src="https://github.com/LHMak/azure-database-migration873/assets/147920042/d9a3bc1e-e956-491f-85e9-9f0de47c10fc">

_Image showing the newly created credential_

After this, I began creating the maintenance plan using the Maintenance Plan Wizard:

<img width="568" alt="m4-4 maintenance plan wizard" src="https://github.com/LHMak/azure-database-migration873/assets/147920042/438dc66c-1077-4d68-911f-56506ac2fcbf">

_Image showing the SQL Server Maintenance Plan Wizard_

I set the maintenance plan to occur weekly, every Sunday at 12:00:00 AM:

<img width="625" alt="m4-4 maintenance plan wizard2" src="https://github.com/LHMak/azure-database-migration873/assets/147920042/95f1a673-80e8-4d92-9472-cde9d3dea07a">

_Image showing the configuration of the maintenance plan schedule_

When selecting which tasks this maintenance plan should perform, I chose Full back ups rather than differential or transactional back ups. I decided to choose Full back ups as it would allow point-in-time recovery in case of corruption or data loss. With this being a development environment, I thought these scenarios could be quite likely.

<img width="618" alt="m4-4 maintenance plan wizard3" src="https://github.com/LHMak/azure-database-migration873/assets/147920042/41fedbd0-120a-4d8e-af37-0d5d5e219bb3">

_Image showing the selection of a Full database back up as a task in the maintenance plan_

I set ‘URL’ as the ‘back up to’ option and then configured the ‘Destination’ settings. This involved selecting the credential I created earlier and providing the name of the Azure storage container for the back ups to be stored in. For ease, I set it to create a new container for these back ups. This would mean I had two containers in the storage account: one for the back up I created earlier (production environment) and a new container for weekly back ups from the development environment.

<img width="610" alt="m4-4 maintenance plan wizard4" src="https://github.com/LHMak/azure-database-migration873/assets/147920042/5f8a4b78-787b-4e62-9106-9098b80c76ef">

_Image showing 'URL' being selected is the 'back up to' option in the Maintenance Plan wizard_

<img width="596" alt="m4-4 maintenance plan wizard5" src="https://github.com/LHMak/azure-database-migration873/assets/147920042/c902312f-46af-4701-a4fc-898b2a1339db">

_Image showing the configured 'Destination' settings in the wizard. The credential and name of the new Azure storage container are supplied here._

I then continued through the wizard to create the maintenance plan. To assess whether the new maintenance plan worked, I tried executing it and ran into an error

#### Problem
When executing the maintenance plan, the following error occurred, which at first seems vague. The error message suggested inspecting the SQL Server Agent job history for details.

<img width="713" alt="m4-4 maintenance plan wizard6" src="https://github.com/LHMak/azure-database-migration873/assets/147920042/670ecf5f-2b24-4fd7-9756-5e7bdaf051d5">

_Image showing the error I encountered when executing the weekly backup maintenance plan_

Upon inspecting the SQL Server Agent job history, I found the following log from the time I executed the plan. The error message contained this line: “Exception Message: The remote server returned an error: (404) Not Found.”

<img width="605" alt="m4-4 maintenance plan wizard7" src="https://github.com/LHMak/azure-database-migration873/assets/147920042/189530ca-b292-4a83-8363-fa547e04831f">

_Image showing the error log from the SQL Server Agent job history._

#### Solution
I inferred from this error message that the location I had set to store the back ups could not be found. I recalled that when creating the maintenance plan, I decided to separate production and development environment back ups in separate containers. Therefore, when I configured the ‘Destination’ options, I input a new Azure storage container name (dev-db-backups).
This container had not been created yet and so, I created this container manually (with container-level permissions) then tried executing the plan again. This time it was a success:

<img width="613" alt="m4-4 maintenance plan wizard8" src="https://github.com/LHMak/azure-database-migration873/assets/147920042/38ba83c2-b7b1-4902-900c-ba577f9d992e">

_Image showing a successful execution of the weekly back up maintenance plan._

I confirmed it worked by logging into my Azure account and navigating to the Azure Blob Storage container. I could see a back up file had been created and stored in the container, proving that the issue was now solved.

## Milestone 5: Disaster Recovery Simulation
Milestone 5 of the project revolved around disaster recovery. I simulated a disaster by intentionally corrupting some data in the production database (which has now been migrated to Azure). I then recovered from the incident by restoring the database to a point in time before the data loss occurred.

### Mimic Data Loss in the Production Environment
The first stage in simulating Disaster Recovery was to mimic data loss in the production environment. I connected to the production VM created in Milestone 2 and opened ADS. From there, I connected to the Azure Cloud Database I created in Milestone 3:

<img width="320" alt="m5-1 connected to cloud database" src="https://github.com/LHMak/azure-database-migration873/assets/147920042/0dd16f31-3546-44a6-8515-25b7355f10d0">

_Image showing successful connection of ADS to the Azure Cloud Database created in Milestone 3_

To mimic data corruption, I chose to replace data in one column of the ‘Person.Address’ table with fake values. I examined the data in this table before intentional corruption by running a query to select the top 1000 rows of the 'Person.Address' table. I could see the ‘AddressLine2’ column of the table contained mostly null values.

<img width="483" alt="m5-1 before corruption" src="https://github.com/LHMak/azure-database-migration873/assets/147920042/e80be4b3-2994-4f64-91a7-2a5c1eabdb26">

_Image showing the query I ran to return the top 1000 rows of the Person.Addess table. The image shows the first 5 rows of the result_

I then mimicked data corruption by running a query to set the ‘AddressLine2’ column in the top 100 rows with ‘not_a_real_address’. The query was as follows:

```
UPDATE TOP (100) Person.Address
SET AddressLine2 = 'not_a_real_address'
```

I confirmed the data to be deleted by running a query to select all rows again, expecting the first 100 rows to show ‘not_a_real_address’ in the AddressLine2 column:

<img width="553" alt="m5-1 after corruption" src="https://github.com/LHMak/azure-database-migration873/assets/147920042/6f145f47-9eb0-48b4-9627-02a2ef8713d7">

_Image showing the top 1000 rows of the Person.Address table. The AddressLine2 column now shows 'not_a_real_address' in the top 100 rows_

With this, I had now simulated a data loss incident. Now I needed to recover from the data loss incident.

### Restore Database from Azure SQL Database Backup

In order to perform disaster recovery from the data loss incident, I used the Azure SQL Database Backup feature to restore the production database to a time before the incident occurred. I chose a restore point 24 hours before I corrupted the data:

<img width="611" alt="m5-2 restore database" src="https://github.com/LHMak/azure-database-migration873/assets/147920042/2dd330d4-0153-4f09-a9d5-7857d52e8799">

_Image showing the Restore Database page when choosing to restore an Azure SQL Database_

Once I configured the settings, I clicked 'Review + Create' then Azure began to deploy a new database from the chosen restore point:

<img width="410" alt="m5-2 restore database deploying" src="https://github.com/LHMak/azure-database-migration873/assets/147920042/230a7e6a-5742-4224-b90a-36a891eae707">

_Image showing the restored database being deployed_

#### Problem
The deployment of this new database was taking a long time. I left Azure to deploy the database overnight and the next day, I saw the deployment had failed. In the Azure portal, I navigated to the resource group I set up for databases and checked the 'Deployments' section to investigate. I could see an error under the deployment name "Microsoft.SQLDatabase.NewDatabaseRestoreExisti...":

![m5-2 restore database  failed](https://github.com/LHMak/azure-database-migration873/assets/147920042/d0fcaf1b-a726-4686-a957-cea119388a95)

_Image showing the list of deployments in the database resource group and the error window for one of the entries_

I tried researching the error but was unable to find anything. As I gain more experience with the Azure platform, I am sure I will understand this issue more in depth.

#### Solution
I decided to redeploy the restored database and eventually it succeded:

![m5-2 restore database success](https://github.com/LHMak/azure-database-migration873/assets/147920042/7f83ea9b-4535-407f-9db7-e3135edda2c2)

_Image showing successful deployment of the restored database_

Once the restored production database was deployed, I connected to it in ADS on the production VM. This meant there were two connections to the Azure SQL server in ADS: one for the original, now corrupted database and the other for the restored database from before the data loss:

<img width="466" alt="m5-2 connect to restored database" src="https://github.com/LHMak/azure-database-migration873/assets/147920042/34bd64d3-1511-4710-b96c-0790e3f00de4">

_Image showing me connecting to the restored Azure SQL database from before the data loss incident_

I tested whether the restoration was successful by running a query to return everything in the Person.Address table again. I could see that in the top 100 rows, the AddressLine2 column contained null values instead of ‘not_a_real_address.’

<img width="926" alt="m5-2 restore database query" src="https://github.com/LHMak/azure-database-migration873/assets/147920042/ebb47f52-e9e4-48e8-bb59-71a220a381ae">

_Image showing the results of the query on the Person.Address table in the restored database. The AddressLine2 column has been returned to its original state from before the data loss incident_

In order to fully recover from the data corruption, I deleted the original database, retaining just the restored database. Once I deleted the original database which suffered the data loss, I could see that the database resource group now only contained 3 items: the restored database, the database server and the migration service I created when performing the data migration in milestone 3.

<img width="982" alt="m5-2 original deleted" src="https://github.com/LHMak/azure-database-migration873/assets/147920042/7c54f7ae-7c2d-497a-bfab-b6743bc4824d">

_Image showing the contents of the database resource group in Azure. The original corrupted database has been deleted, leaving just the restored database, Azure SQL server and the database migration service_


## Milestone 6: Geo Replication and Failover
The aim of milestone 6 was to configre geo-replication of the production database to further protect the database. Geo-replication works by replicating a database to a separate geographic region. The two databases are referred to as 'primary' (original database) and secondary (geo-replicated database). The advantage of geo-replication is that if the primary database was to become unavailable, such as in the case of a regional outage, disaster or planned maintenance, the secondary database can be used instead (referred to as a 'failover'). When the disaster or event has been resolved, it is possible to switch back to the primary database (referred to as 'failback').

### Set Up Geo-Replication for Azure SQL Database
To set up geo-replication for the production database (which was restored from data loss in milestone 5), I navigated to the Azure SQL Database in the Azure portal, entered the Replicas menu and clicked 'Create replica'. 

This opened the Geo Replica wizard where I had to create a new server for the secondary database. I based the server in a different geographic location, East US, to my primary database, UK South. Like with the original server, I used SQL authentication. For the Geo Replica settings, I left them as default. I then waited for the secondary database to deploy:

![m6-1 database deployed](https://github.com/LHMak/azure-database-migration873/assets/147920042/efe319a9-e821-4484-aebe-66744cddc592)

_Image showing successful deployment of the secondary database_

Looking at the overview of the database, I could see the ‘Replica type’ and ‘Primary database’ fields, indicating the new server was successfully deployed as a geo-replica of the original database:

![m6-1 database overview](https://github.com/LHMak/azure-database-migration873/assets/147920042/aaafb6cf-7c90-452c-9e28-584e02d76d1d)

_Image showing a section of the overview page of the secondary database. It shows the 'Replica type' and 'Primary database' fields_

With geo-replication of the production database now complete, it was time to test the functionality of failing over and failing back.

### Test Failover and Failback
To test the functionality of the failover environment (secondary database), I initiated a test failover. In a real-world scenario, I would perform this during a planned maintenance period to avoid loss of data or interruption of work for end users of the database. As this is a personal project, the timing of this test was of no concern.

To initiate the failover test, I navigated to the primary server (hosted in UK South). From here, I opened the Failover Groups menu under 'Data Management' and clicked 'Add group,' which opened the 'Failover group' wizard:

![m6-2 failover groups](https://github.com/LHMak/azure-database-migration873/assets/147920042/0ab5a7dd-3740-4e96-a9b8-bd161cc5b5d2)

_Image showing the Failover Groups menu, accessed from the 'Data Management' section of the overview page of the primary server_

I filled in the fields, choosing an identifiable Failover group name and ensuring to select the secondary server in the 'Server' field.

![m6-2 failover group wizard filled](https://github.com/LHMak/azure-database-migration873/assets/147920042/d6bdcd1c-065d-4531-8e4c-2d7d30c4c0f2)

_Image showing the completed Failover group wizard_

To check if failover group was deployed correctly, I navigated to the secondary server in the Azure portal and opened the Failover groups menu. I could see the newly created failover group here, with the primary and secondary server names populating the 'Primary server' and 'Secondary server' columns as expected:

![m6-2 failover group created](https://github.com/LHMak/azure-database-migration873/assets/147920042/dbcffbf9-a7f2-4c8c-9117-0d9f279be335)

_Image showing the successfully created failover group_

I clicked on the failover group to show its overview page

## Milestone 7: Microsoft Entra Directory Integration

# Tools Used
- AdventureWorks sample database
- Microsoft Azure services
- Microsoft Azure Virtual Machine
- SQL Server
- SQL Server Management Studio

# Usage Instructions

# File Structure of Project

# License Information
