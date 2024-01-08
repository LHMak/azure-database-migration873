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
- After this, I created the company's database by restoring from a .bak backup file of the AdventureWorks sample database.

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

## Milestone 4: Data Backup and Restore
In this milestone, I focused on ensuring the data stored in the production environment database was stored securely on Azure. I then created a development environment which could be used for developing and testing new features, etc. Finally, I set up an automated backup routine for the development environment to safeguard any ongoing work in case of errors with testing and development.

### Backup the On-Premise Database

### Upload Backup to Blob Storage

### Restore Database on Development Environment

### Automate Backups for Development Database


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
