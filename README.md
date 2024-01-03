# Azure Database Migration

# Project Description
The goal of this project was to migrate a local SQL database to the cloud using an Azure SQL Database. The local database was hosted on an SQL server on a virutal machine (VM).
The AdventureWorks sample database created by Microsoft was used for the purposes of this project.
This project was split into 7 milestones. After each milestone, any relevant files were uploaded to this repository and this README was updated to document my progress.

## Milestone 1: Set up the Environment
In this milestone, this GitHub repo was created and I set up my account on Azure.

## Milestone 2: Set up the Production Environment
In this milestone, I provisioned a Windows Virtual Machine (VM) which was the cornerstone of my production environment. This VM hosted the company's database, serving as a secure, dedicated storage solution. SQL Server and Server Management Studio (SSMS) was installed on this VM to manage the database. 
- I provisioned a Windows 11 pro VM using the Standard B2ms storage size. This size is good for many workloads and so will be sufficient for this project.
- I connected securly to the newly created VM using the RDP protocol and installed SQL Server and SSMS
- After this, I created the company's database by restoring from a .bak backup file.

## Milestone 3: Migrate to Azure SQL Database
In this milestone, I transitioned a local database to an Azure SQL database. I achieved this by provisioning an SQL server and the target Azure SQL Database using SQL login as the authentication method:

<img width="469" alt="m3-1 deploy database" src="https://github.com/LHMak/azure-database-migration873/assets/147920042/65006a6d-44a5-4941-8cd3-cdeba8eaf4f8">

_Image showing successful deployment of database_

I then allowed public network access to the server. This would allow my local PC and the source VM to connect to the database. I validated this by connecting to the database in VS Code on my local PC:

<img width="280" alt="m3-1 vs code connected" src="https://github.com/LHMak/azure-database-migration873/assets/147920042/1018ae27-605c-4608-9763-a7e41aaba423">

_Image showing successful connection to the Azure SQL database in VS Code_

## Milestone 4: Data Backup and Restore

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
