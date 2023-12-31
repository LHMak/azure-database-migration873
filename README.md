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

## Milestone 6: GEO Replication and Failover

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
