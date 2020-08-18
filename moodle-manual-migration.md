## Moodle Manual  Migration
This document explains how to migrate Moodle from OnPrem servers to Azure cloud.
## Prerequisites
- Must have access to the OnPrem servers to take backup of Moodle and database/configurations.
- Should have a Azure subscription and the Azure Blob storage created before migration.
- This migration activity supports and validated with Softwares.
    - Ubuntu 16.04 LTS
    - Nginx web server 1.14.3
    - MySQL PaaS 5.6, 5.7 or 8.0 database server
    - PHP 7.2, 7.3, or 7.4
    - Moodle 3.8 & 3.9

## Migration

Moodle Migration involves following steps,
- Data Export from OnPrem to Azure Cloud
- Import data to Azure cloud.
- DB migration.

 Data Export from OnPrem to Azure Cloud
 
 
- Create Azure Blob storage in the Azure subscription and create below folders, - moodle -      moodledata - configurations - dbbackup
- Make a tar file of the Moodle folder. Ex: /moodle
- Make a tar file of the Moodle data folder. Ex: /moodledata
- Make a tar file of php, nginx & moodle configurations. Ex: config.tar.gz
- Take the back of the MySQL DB.
- Use AZCopy to copy above backup files to respective folders in Azure Blob storage.
- Import data to Azure cloud

### Option 1: Migrating Moodle with Azure ARM Templates 
* Moodle can be installed in two ways. 
* Moodle installation on Azure with 4 predefined templates. 
* Fully Configurable deployment provides various options to select with when the requirement does not match with the predefined ARM templates. [Click here](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FMoodle%2Fmaster%2Fazuredeploy.json)
* First choice is a GitHub repository with 4 predefined options such as Minimal, Short-to-Mid, Large, Maximal. [Click here](https://github.com/Azure/moodle)
    - Minimal: This deployment will use NFS, MySQL, and smaller auto scale web frontend VM sku (1 core) that will give faster deployment time (less than 30 minutes) and requires only 2 VM cores currently that will fit even in a free trial Azure subscription.  
    - Small to Mid: Supporting up to 1000 concurrent users. This deployment will use NFS (no high availability) and MySQL (8 vCores), without other options like elastic search or Redis cache.  
    - Large (high availability): Supporting more than 2000 concurrent users. This deployment will use Azure Files, MySQL (16 vCores) and Redis cache, without other options like elastic search.  
    - Maximum: This maximal deployment will use Azure Files, MySQL with highest SKU, Redis cache, elastic search (3 VMs), and pretty large storage sizes (both data disks and DB).
* To deploy any of the predefined size template click on the launch option.
* It will redirect to Azure Portal where user need to fill mandatory fields such as Subscription, Resource Group, SSH key, Region. 
![custom_deployment](https://github.com/sayali0512/manual-migration/blob/master/images/customdeployment.png)
* Click on purchase to start the deployment of Moodle on Azure. Link for pricing [calculator]( https://azure.microsoft.com/en-us/pricing/calculator/ )
* The deployment will install Infrastructure and Moodle 
    - Moodle version: 3.8, 3.9 and 3.5  
    - Webserver: nginx or apache2 
    - Nginx version: 1.10.0
    - PHP version: 7.4, 7.3 or 7.2 
    - Database server type: MySQL  
    - MySQL version: 5.6, 5.7 and 8.0 
    - Ubuntu version: 16.04-LTS  
* The infrastructure will create the following resources by using the predefined ARM template: 
* Network Template: It will create virtual network, subnet, Public IP, Load Balancer/App gateway and Redis cache etc. 
    - Virtual network: An Azure Virtual Network is a representation of your own network in the cloud. It is a logical isolation of the Azure cloud dedicated to your subscription. When you create a VNet, your services and VMs within your VNet can communicate directly and securely with each other in the cloud. More information on Virtual Network [click here](https://docs.microsoft.com/en-us/azure/virtual-network/virtual-networks-overview). 
    - Subnet: A subnet or subnetwork is a smaller network inside a large network. By default, an IP in a subnet can communicate with any other IP inside the VNET. More information on Subnet [click here](https://docs.microsoft.com/en-us/azure/virtual-network/virtual-network-manage-subnet). 
    - Public IP: Public IP addresses are used to communicate Azure resources to the Internet. The address is dedicated to the Azure resource. More information on Public IP [click here](https://docs.microsoft.com/en-us/azure/virtual-network/public-ip-addresses#:~:text=Public%20IP%20addresses%20enable%20Azure,IP%20assigned%20can%20communicate%20outbound). 
    - Load Balancer: It is an efficient distribution of network or application traffic across multiple servers in a server farm. Ensures high availability and reliability by sending requests only to servers that are online. More information on Load balancer  [click here](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/tutorial-load-balancer#:~:text=An%20Azure%20load%20balancer%20is,traffic%20to%20an%20operational%20VM). 
    - App gateway: Azure Application Gateway is a web traffic load balancer that enables you to manage traffic to your web applications. Application Gateway can make routing decisions based on additional attributes of an HTTP request, for example URI path or host headers. More information on App Gateway [click here](https://docs.microsoft.com/en-us/azure/application-gateway/overview). 
    - Redis cache: Azure Cache for Redis provides an in-memory data store based on the open-source software Redis. Redis improves the performance and scalability of an application that uses on backend data stores heavily. It is able to process large volumes of application request by keeping frequently accessed data in the server memory that can be written to and read from quickly. [click here](https://docs.microsoft.com/en-us/azure/azure-cache-for-redis/cache-overview). 
* Storage Template: 
    - An Azure storage account contains all of your Azure Storage data objects: blobs, files, queues, tables, and disks. The storage account provides a unique namespace for your Azure Storage data that is accessible from anywhere in the world over HTTP or HTTPS
    - storage account will have specific type, replication, Performance, Size.Below are some examples. [click here](https://docs.microsoft.com/en-us/azure/storage/common/storage-account-overview). 
    - The types of storage accounts are:
        - General-purpose V2- It is Basic storage account type for blobs, files, queues, and tables and recommended for most scenarios using Azure Storage
        - General-purpose V1- It is Legacy account type for blobs, files, queues, and tables somecan can use  general-purpose v2 accounts instead when possible.
        - BlockBlobStorage- Storage accounts with premium performance characteristics for block blobs and append blobs. Recommended for scenarios with high transactions rates, or scenarios that use smaller objects or require consistently low storage latency.
        - File Storage- Files-only storage accounts with premium performance characteristics. Recommended for enterprise or high performance scale applications.
        - BlobStorage accounts- Legacy Blob-only storage accounts. Use general-purpose v2 accounts instead when possible.
    - Replication:
        - Locally-redundant storage (LRS)- A simple, low-cost redundancy strategy. Data is copied synchronously three times within the primary region.
        - Zone-redundant storage (ZRS)-  Redundancy for scenarios requiring high availability. Data is copied synchronously across three Azure availability zones in the primary region.
        - Geo redundant storage (GRS)- Cross-regional redundancy to protect against regional outages. Data is copied synchronously three times in the primary region, then copied asynchronously to the secondary region. For read access to data in the secondary region, enable read-access geo-redundant storage (RA-GRS).
    - Performance: 
        - Standard- A standard performance tier for storing blobs, files, tables, queues, and Azure virtual machine disks.
        - Premium- A premium performance tier for storing unmanaged virtual machine disks.
    - Size(sku):  A single storage account can store up to 500 TB of data and like any other Azure service
    - Below are types of storage account types ARM templates support 
        * NFS: A Network File System (NFS) allows remote hosts to mount file systems over a network and interact with those file systems as though they are mounted locally. This enables system administrators to consolidate resources onto centralized servers on the network. More information on NFS [click here](https://docs.microsoft.com/en-us/windows-server/storage/nfs/nfs-overview). 
        * GluserFS: It is an open source distributed file system that can scale out in building-block fashion to store multiple petabytes of data. More information on Gluster FS [click here](https://docs.microsoft.com/en-us/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-glusterfs). 
        * Azure Files: is the only public cloud file storage that delivers secure, Server Message Block (SMB) based, fully managed cloud file shares that can also be cached on-premises for performance and compatibility. More information on Azure Files [click here](https://docs.microsoft.com/en-us/azure/storage/files/storage-files-introduction). 
        * NFS and glusterFS:  
            - Replication is standard Locally-redundant storage (LRS)  
            - Type is Storage (general purpose v1) 
        * Azure Files:  
            - Replication is Premium Locally-redundant storage (LRS)  
            - Type is File Storage  
        * These storage mechanisms will differ according to storage type such as  
            - NFS and glusterFS will create a container  
            - Azure files will create a file share. 
    - To access the containers and file share etc. navigate to storage account in resource       group in the portal. 
    ![storage_account](https://github.com/sayali0512/manual-migration/blob/master/images/storage-account.png)
* Database Template: 
    - Creates an Azure Database for MySQL server. [click here](https://docs.microsoft.com/en-in/azure/mysql/) 
    - Azure Database for MySQL is easy to set up, manage and scale. It automates the management and maintenance of your infrastructure and database server,including routine updates,backups and security. Build with the latest community edition of MySQL, including versions 5.6, 5.7 and 8.0 
    - To access the database server created navigate to the resource group provided while deployment and go to Azure Database for MySQL server  
    - The database server will have a server name, server admin login name, MySQL version, and Performance Configuration 
    - Ways to connect to database server 
        - Use MySQL client or tools such as MySQL Workbench. 
        - For workbench give the connection name, hostname (server name), username (server admin login name) 
    ![mysqlworkbench](https://github.com/sayali0512/manual-migration/blob/master/images/mysql-workbench.png)
        - After giving the details test connection. If the connection is successful it will prompt for password .Provide the password to get connected. 
* Virtual Machine Template: Creates a Virtual Machine
    * Controller VM: 
        - Ubuntu OS defaulted to 16.04 
        - A virtual machine is a computer file, typically called an image, which behaves like an actual compute [Click here](https://azure.microsoft.com/en-in/overview/what-is-a-virtual-machine/) 
    * VM extension: 
        - Extension can be small applications that provide post-deployment configuration and automation tasks on Azure VMs.[Click here](https://docs.microsoft.com/en-us/azure/virtual-machines/extensions/overview) 
        - VM extension will executes a shell script file which installs Moodle on the Virtual Machine and captures the log files. 
        - Log files stderr and stdout are created at the /var/lib/waagent/custom-script/download/0/  
        - User can view the log files as a root user. 

- Scale Set Template: Creates a Virtual Machine Scale Set (VMSS) with the VM instance. [Click here](https://docs.microsoft.com/en-us/azure/virtual-machine-scale-sets/overview) 
    * A virtual machine scale set allows you to deploy and manage a set of auto-scaling virtual machines. You can scale the number of VMs in the scale set manually or define rules to auto scale based on resource usage like CPU, memory demand, or network traffic.
    * Autoscaling of VM Instances depends on the CPU utilization. [Click here](https://docs.microsoft.com/en-us/azure/azure-monitor/platform/autoscale-overview)  
    * While scaling up an instance a VM is deployed and a shell script is executed to install the Moodle prerequisites and setting up cron jobs. 
    * VM instances have Private IP. 
    * For connecting to VM’s on Scale Set with private IP, follow the steps written in the [document](https://italentcorporation1-my.sharepoint.com/:w:/g/personal/skumbhar_italentdigital_com/EWEpBXgy-D5GmWIcVPgbsq8BhLCMHKxlbZ2rILRsJ2gUOA?e=FcGgIS). 
    

### Manual Moodle migration follow the below steps 
* After completion of deployment go to the resource group.  
* Navigate to the controller virtual machine 
* Login into this controller machine using any of the free open-source terminal emulator or serial console tools 
    - Copy the public IP of controller VM and paste as host name 
    - Expand SSH in navigation panel and click on Auth and browse the same SSH key file given while deployment. 
    - Click on Open and it will prompt to give the username. Give it as azureadmin as it is hard coded in template
    ![puttyloginpage](https://github.com/sayali0512/manual-migration/blob/master/images/puttyloginpage.PNG)
    ![puttykey](https://github.com/sayali0512/manual-migration/blob/master/images/puttykeybrowse.PNG)
* After login, download the tar files for configuration and application dataTfrom azure blob storage. 
    - Replace the moodle folder  
        - Download moodle.tar.gz file from the blob storage. The path to download will be /home/azureadmin. Navigate to this path 
        - cd /home/azureadmin 
            ```
                azcopy copy 'https://storageaccount.blob.core.windows.net/container/BlobDirectory/*' 'Path/to/folder' 
            ```
        - After downloading the moodle.tar.gz ,extract moodle.tar.gz file  
            ```
                tar -zxvf yourfile.tar.gz
            ``` 
        
        - Then copy and replace this moodle folder with existing folder 
        - Before accessing the moodle folder switch to root user and copy the moodle folder to existing path 
            ```
                cp /home/azureadmin/moodle /moodle/html
            ```
    - Replace the moodledata folder  
        - Download moodledata.tar.gz file from the blob storage.
        - Navigate to the path 
            
            ```
                cd /home/azureadmin
                azcopy copy 'https://storageaccount.blob.core.windows.net/container/BlobDirectory/*' 'Path/to/folder'
            ``` 
        - After downloading the moodledata.tar.gz ,extract this moodledata.tar.gz file
            ``` 
                tar -zxvf yourfile.tar.gz
            ``` 
        
        - Then copy and replace this moodledata (/moodle/moodledata) folder with existing folder 
        - Go as a root user and copy the moodledata folder existing path 
            ```
                cp /home/azureadmin/moodledata /moodle/
            ``` 
    - Importing the .sql file 
        - Download the database.tar.gz from the blob storage 
        - The path to download will be /home/azureadmin so navigate to this path  
            ```
                cd /home/azureadmin
                azcopy copy 'https://storageaccount.blob.core.windows.net/container/BlobDirectory/*' 'Path/to/folder'
            ``` 
        - After downloading the database.tar.gz file will be present  
        - Extract this database.tar.gz file  
            ```
                tar -zxvf yourfile.tar.gz
            ``` 
        - The database folder will be extracted which contains the .sql file 
        - Go as a root user and navigate to database folder and import the .sql file 
    - For database import first create a database 
        ```
            mysql -h $server_name -u $ server_admin_login_name -p$admin_password -e "CREATE DATABASE ${moodledbname} CHARACTER SET utf8;"
        ```
     
        - Change the permissions 
        ```
            mysql -h $ server_name -u $ server_admin_login_name -p${admin_password } -e "GRANT ALL ON ${moodledbname}.* TO ${moodledbuser} IDENTIFIED BY '${moodledbpass}';"
        ``` 
        - Import the database into it 
        ```
            mysql -h db_server_name -u db_login_name -pdb_pass dbname >/path/to/.sql file
        ```
        - Set 755 and www-data owner:group permissions to Moodle folder 
        ```
            sudo chmod 755 /moodle
            sudo chown -R www-data:www-data /moodle
        ```
        - Set 770 and www-data owner:group permissions to MoodleData folder 
        ```
            sudo chmod 755 /moodle/moodledata
            sudo chown -R www-data:www-data /moodle/moodledata
        ``` 
        - Modify the Moodle configuration 
            - Download the configuration.tar.gz from the blob storage 
        - The path to download will be /home/azureadmin so navigate to this path  
            ```
                cd /home/azureadmin
                azcopy copy 'https://storageaccount.blob.core.windows.net/container/BlobDirectory/*' 'Path/to/folder'
            ``` 
        - After downloading the configuration.tar.gz file ,extract the configuration.tar.gz file
            ```
                tar -zxvf yourfile.tar.gz
            ``` 
    - The configuration folder will be extracted with nginx and php configuration files 
    - For changing nginx configuration 
    - First change the database details in moodle configuration file (/moodle/config.php) 
    - As a root user copy the nginx configuration file to the nginx folder (/etc/nginx/sites-enabled)
    
        ```
            mkdir  -p /home/azureadmin/backup/ 
            sudo mv /etc/nginx/sites-enabled/<dns>.conf /home/azureadmin/backup/ 
            cd /home/azureadmin/storage/configuration/
            sudo cp <dns>.conf  /etc/nginx/sites-enabled/
        ``` 
    - For changing the log paths go to the nginx config file (/etc/nginx/sites-enabled/dns.conf) and replace the site log path as below  
```sh
    # Log to syslog 
    error_log syslog:server=localhost,facility=local1,severity=error,tag=moodle;
    access_log syslog:server=localhost,facility=local1,severity=notice,tag=moodle moodle_combined;
``` 
           
        - User can get the site logs at /var/log/nginx/ path. 
        - Change the log paths and mention the example
        - DNS name and certs and its path 
        - Next copy the php config file from blob storage to the php config folder. 
```sh
    sudo mv /etc/php/<phpVersion>/fpm/pool.d/www.conf /home/azureadmin/backup
    sudo  cp /home/azureadmin/storage/configuration/www.conf /etc/php/<phpVersion>/fpm/pool.d/
    sudo systemctl restart nginx
    sudo systemctl restart php(phpVersion)-fpm  
    ex: sudo systemctl restart php7.4-fpm 
``` 
        
    - Any other extensions that is needed for the php that is not installed as part of the ARM template installion must be done manually 
    - Hit the load balancer DNS name to get the Moodle page. 
