## Option 2: Migrating Moodle without template 

 - This option is to create the infrastructure manually in azure and migrate the Moodle 
 - The Azure infrastructure is the basic skeleton of the resources which will host the Moodle application. 
 - For installing the infrastructure for Moodle navigate to the [azure portal](portal.azure.com) 
 - In the home section go the resource group section and add a new a resource group for the Moodle infrastructure [click here](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceGroups) 
  ![createRG]()
 - Select the subscription as per the choice, give the resource group name and select the region of deployment
 - Add the tag for more specification 
 - Tags are name-value pairs that are used to organize resources in Azure Portal [click here](https://docs.microsoft.com/en-us/azure/azure-resource-manager/management/tag-resources) 
 - Click on review and create for creating a resource group 
 - Once resource group is created navigate to it and create resources into it. 

- ##### Below are the prerequisites resource to host the Moodle application 
- **Network Resources**
    * **Standard Load Balancer:**  An Azure load balancer is a Layer-4 (TCP, UDP) load balancer that provides high availability by distributing incoming traffic among healthy VMs. A load balancer health probe monitors a given port on each VM and only distributes traffic to an operational VM. [click here](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/tutorial-load-balancer#:~:text=An%20Azure%20load%20balancer%20is,traffic%20to%20an%20operational%20VM.) 
    *  ![loadbalancer]()
    *  In the Basics tab, Select the same subscription, same resource group created in above step, give the instance details such as name for load balancer, and region same as selected for resource group 
    *  Select the type as public and sku as standard. 
    *  Create a new IP address and give the give the IP address name.
    *  In the tag section, if required give the tag value. 
    *  After giving the mandatory values click on review and create. [Click here](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/tutorial-load-balancer#:~:text=An%20Azure%20load%20balancer%20is,traffic%20to%20an%20operational%20VM.) 
    - **Virtual Network** - An Azure Virtual Network is a representation of your own network in the cloud. It is a logical isolation of the Azure cloud dedicated to your subscription. When you create a VNet, your services and VMs within your VNet can communicate directly and securely with each other in the cloud. More information on Virtual Network [click here](https://docs.microsoft.com/en-us/azure/virtual-network/virtual-networks-overview). 
    - ![Virtual Network SS]()
    - From the Azure portal menu, select Create a resource. From the Azure Marketplace, select Networking > Virtual network.
    - In Create virtual network, enter or select this information: 
        - Subscription: Select the same subscription. 
        - Resource Group: Select same resource group name. 
        - Name: Give the instance name. 
        - Location: Select East US same as resource group 
    - Select Next: IP Addresses, and for IPv4 address space, enter 10.1.0.0/16. 
    - Select Add subnet, then enter Subnet name and 10.1.0.0/24 for Subnet address range.
    - ![subnet SS]()
    - Select Add, then select Review + create. Leave the rest as default and select Create.
    - For more Details [click here](https://docs.microsoft.com/en-us/azure/virtual-network/quick-create-portal)
    
- **Storage Resources**
    - Creates storage account with type, replication, Performance, Size.
    - Below are some examples. click here.
        - Type: General-purpose V2, General-purpose V1, BlockBlobStorage , File Storage
        - Replication: Locally-redundant storage (LRS), Geo redundant storage, etc.
        - Performance: Standard or Premium
        - Size(sku) 
    - ![create storage SS]()
    - On the Azure portal menu, select All services. In the list of resources, type Storage Accounts. As you begin typing, the list filters based on your input. Select Storage Accounts.
    - On the Storage Accounts window that appears, choose Add. 
    - Select the same subscription in which to create the storage account. 
    - Under the Resource group field, select the same resource group 
    - Next, enter a name for your storage account. The name you choose must be unique across Azure. The name also must be between 3 and 24 characters in length, can include numbers and lowercase letters only. 
    - Select a location for your storage account, default location as selected for resource group 
    - Leave these fields set to their default values: 
        - Deployment model: Resource Manager 
        - Performance: standard 
        - Account kind: StorageV2 (general-purpose v2) 
        - Replication: Local redundant storage 
        - Access tier: hot 
    - If you plan to use [Azure Data Lake Storage](https://azure.microsoft.com/services/storage/data-lake-storage/), choose the Advanced tab, and then set Hierarchical namespace to Enabled.
    - Select Review + Create to review your storage account settings and create the account. 
    - Select Create. [Click here](https://docs.microsoft.com/en-us/azure/storage/common/storage-account-create?tabs=azure-portal)
    - For more information about types of storage accounts and other storage account settings, see [Azure storage account overview](https://docs.microsoft.com/en-us/azure/storage/common/storage-account-overview). For more information on resource groups, see [Azure Resource Manager overview](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-overview). 
    - For NFS and glusterFS:  
        - Replication is standard Locally-redundant storage (LRS)  
        - Type is Storage (general purpose v1) 
    - Azure Files: 
        - Replication is Premium Locally-redundant storage (LRS)  
        - Type is File Storage  
    - These storage mechanisms will differ according to storage type such as 
        - NFS and glusterFS will create a container 
        - Azure files will create a file share. 
    - To access the containers and file share etc. navigate to storage account in resource group in the portal. 
    - ![storage account preview]()

  
- **Database Resources** - 
    - Creates an Azure Database for MySQL server. [click here](https://docs.microsoft.com/en-in/azure/mysql/).
    - Azure Database for MySQL is easy to set up, manage and scale. It automates the management and maintenance of your infrastructure and database server, including routine updates,backups and security. Build with the latest community edition of MySQL, including versions 5.6, 5.7 and 8.0.
    - ![mysql server ss]()
    - An Azure Database for MySQL server is created with a defined set of [compute and storage resources](https://docs.microsoft.com/en-us/azure/mysql/concepts-compute-unit-and-storage). [Click here](https://docs.microsoft.com/en-us/azure/mysql/tutorial-design-database-using-portal#:~:text=An%20Azure%20Database%20for%20MySQL%20server%20is%20created%20with%20a,Databases%20%3E%20Azure%20Database%20for%20MySQL.).
    - Select the Create a resource button (+) in the upper left corner of the portal. 
    - Select Databases > Azure Database for MySQL. If you cannot find MySQL Server under the Databases category, click See all to show all available database services. You can also type Azure Database for MySQL in the search box to quickly find the service. 
    - Click Azure Database for MySQL tile. Fill out the Azure Database for MySQL form. 
        - Server name: Choose a unique name that identifies your Azure Database for MySQL server. For example, mydemoserver. The domain name.mysql.database.azure.com is appended to the server name you provide. The server name can contain only lowercase letters, numbers, and the hyphen (-) character. It must contain from 3 to 63 characters. 
        - Subscription: Select the Azure subscription that you want to use for your server. 
        - Resource group: Provide the existing resource group name 
        - Select source: Select Blank to create a new server from scratch. (You select Backup if you are creating a server from a geo-backup of an existing Azure Database for MySQL server). 
        - Server admin login: A sign-in account to use when you're connecting to the server. The admin sign-in name cannot be azure_superuser, admin, administrator, root, guest, or public. 
        - Password: Provide a new password for the server admin account. It must contain from 8 to 128 characters. Your password must contain characters from three of the following categories: English uppercase letters, English lowercase letters, numbers (0-9), and non-alphanumeric characters (!, $, #, %, and so on). 
        - Confirm password: Confirm the admin account password. 
        - Location: Select the same resource group 
        - Version: The latest version (unless you have specific requirements that require another version). 
        - Pricing tire: General Purpose, Gen 5, 2 vCores, 5 GB, 7 days, Geographically Redundant. 
        - The compute, storage, and backup configurations for your new server. Select Pricing tier. Next, select the General-Purpose tab. Gen 5, 2 vCores, 5 GB, and 7 days are the default values for Compute Generation, vCore, Storage, and Backup Retention Period. You can leave those sliders as is. To enable your server backups in geo-redundant storage, select Geographically Redundant from the Backup Redundancy Options. To save this pricing tier selection, select OK. The next screenshot captures these selections.
        - ![pricingTier SS]()
        - Click Review + create. You can click on the Notifications button on the toolbar to monitor the deployment process. Deployment can take up to 20 minutes. 
    - **Configure firewall:**
    -  Azure Databases for MySQL are protected by a firewall. By default, all connections to the server and the databases inside the server are rejected. Before connecting to Azure Database for MySQL for the first time, configure the firewall to add the client machine's public network IP address (or IP address range). 
    -  Click your newly created server, and then click Connection security. 
    -  ![connectionSecurity SS]()
    -  You can Add My IP, or configure firewall rules here. Remember to click Save after you have created the rules. You can now connect to the server using mysql command-line tool or MySQL Workbench GUI tool. 
-  **Get connection information:**

In Azure portal, click All resources from the left-hand menu, type the name, and search for your Azure Database for MySQL server. Select the server name to view the details. 

From the Overview page, note down Server Name and Server admin login name. You may click the copy button next to each field to copy to the clipboard. 4-2 server properties 

In this example, the server name is mydemoserver.mysql.database.azure.com, and the server admin login is myadmin@mydemoserver. 

                 

Virtual Machine 

A virtual machine is a computer file, typically called an image, which behaves like an actual compute Click here 

Before creating Virtual machine create an SSH key pair 

If you already have an SSH key pair, you can skip this step. 

Go to the PuTTY installation folder (the default location is C:\Program Files\PuTTY) and run: puttygen.exe 

In the PuTTY Key Generator window, set Type of key to generate to RSA, and set Number of bits in a generated key to 2048. 

 

Select Generate. 

To generate a key, in the Key box, move the pointer randomly. 

When the key generation has finished, select Save public key, and then select Save private key to save your keys to files. 

 

The public and private key is generated 

 

Create a VM with ubuntu 16.04 / 18.04 operating system with SSH public key 

Select the same subscription and resource group and give name for virtual machine. 

Give the same region group. 

Keep the availability options as default. 

Image is the size of the virtual machine. Browse the image and select it 

Select Authentication type SSH, give the username give the SSH key generated in previous step. 

Select the disk size. 

Select the inbound rule for SSH as 22 and HTTP as 80 

 

 

Click next on Disk section 

Select the OS disk type. There are 3 choices Standard SSD, Premium SSD, Standard HDD 

Keep the other parameters as default 

 

Click next on networking and select the virtual network created in above step and the public IP and keep the above parameters as default. 

Click on next for management and keep the parameters as default. 

 

And keeping the other parameters as default Click on review and create. 

Login into this controller machine using any of the free open-source terminal emulator or serial console tools 

Copy the public IP of controller VM and paste as host name and expand SSH in navigation panel and click on Auth and browse the same SSH key file given while deployment. Click on Open and it will prompt to give the username as azureadmin same as given while deployment that is azureadmin 

 

 

There are 4 files to be copied from blob stoarge to setup moodle application. 

First copy the Moodle folder.  

Download moodle.tar.gz file from the blob storage. 

The path to download will be /home/azureadmin so navigate to this path  

cd /home/azureadmin 

azcopy copy 'https://storageaccount.blob.core.windows.net/container/BlobDirectory/*' 'Path/to/folder' 

After downloading the moodle.tar.gz file will be present  

Extract this moodle.tar.gz file  

Command: tar -zxvf yourfile.tar.gz (here the file name will be moodle.tar.gz) 

Then copy and this moodle folder to the moodle installation path. 

Go as a root user and copy the moodle folder  

cp /home/azureadmin/moodle  

 

Copy the moodledata folder  

Download moodledata.tar.gz file from the blob storage. 

The path to download will be /home/azureadmin so navigate to this path  

cd /home/azureadmin 

azcopy copy 'https://storageaccount.blob.core.windows.net/container/BlobDirectory/*' 'Path/to/folder' 

After downloading the moodledata.tar.gz file will be present  

Extract this moodledata.tar.gz file  

Command: tar -zxvf yourfile.tar.gz (here the file name will be moodledata.tar.gz) 

Then copy this moodledata folder. 

Go as a root user and copy the moodledata folder. 

cp /home/azureadmin/moodledata /moodle/ 

 

Importing the .sql file 

Download the database.tar.gz from the blob storage 

The path to download will be /home/azureadmin so navigate to this path  

cd /home/azureadmin 

azcopy copy 'https://storageaccount.blob.core.windows.net/container/BlobDirectory/*' 'Path/to/folder' 

 

After downloading the database.tar.gz file will be present  

Extract this database.tar.gz file  

Command: tar -zxvf yourfile.tar.gz (here the file name will be database.tar.gz) 

The database folder will be extracted which contains the .sql file 

Go as a root user and navigate to database folder and import the .sql file 

For database import first create a database  

             mysql -h $server_name -u $ server_admin_login_name -p$admin_password -e          "CREATE DATABASE ${moodledbname} CHARACTER SET utf8;" 

Change the permissions 

           mysql -h $ server_name -u $ server_admin_login_name -p${admin_password } -e "GRANT ALL ON ${moodledbname}. * TO ${moodledbuser} IDENTIFIED BY '${moodledbpass}';" 

Import the database into it 

                      mysql -h db_server_name -u db_login_name -pdb_pass dbname >/path/to/.sql file 

 

Set 755 and www-data owner:group permissions to Moodle folder 

   sudo chmod 755 /moodle 

 sudo chown -R www-data:www-data /moodle 

Set 770 and www-data owner:group permissions to MoodleData folder 

sudo chmod 755 /moodle/moodledata 

sudo chown -R www-data:www-data /moodle/moodledata 

Modify the Moodle configuration 

Download the configuration.tar.gz from the blob storage 

The path to download will be /home/azureadmin so navigate to this path  

cd /home/azureadmin 

azcopy copy 'https://storageaccount.blob.core.windows.net/container/BlobDirectory/*' 'Path/to/folder' 

After downloading the configuration.tar.gz file will be present  

Extract this d configuration.tar.gz file  

Command: tar -zxvf yourfile.tar.gz (here the file name will be configuration.tar.gz) 

The configuration folder will be extracted with nginx and php configuration files 

For changing nginx configuration 

First change the database details in moodle configuration file (/moodle/config.php) 

Go to root user and copy the nginx configuration file 

mkdir  -p /home/azureadmin/backup/ 

sudo mv /etc/nginx/sites-enabled/<dns>.conf  /home/azureadmin/backup/ 

cd /home/azureadmin/storage/configuration/ 

sudo cp <dns>.conf  /etc/nginx/sites-enabled/ 

Change the log paths and mention the example 

DNS name and certs and its path 

Next copy the php config file from blob storage to the php config folder. 

sudo mv /etc/php/<phpVersion>/fpm/pool.d/www.conf /home/azureadmin/backup 

sudo  cp /home/azureadmin/storage/configuration/www.conf /etc/php/<phpVersion>/fpm/pool.d/ 

sudo systemctl restart nginx 

sudo systemctl restart php(phpVersion)-fpm  

ex: sudo systemctl restart php7.4-fpm  

 

Scale Set: A virtual machine scale set allows you to deploy and manage a set of auto-scaling virtual machines. You can scale the number of VMs in the scale set manually, or define rules to auto scale based on resource usage like CPU, memory demand, or network traffic. An Azure load balancer then distributes traffic to the VM instances in the scale set. Click here 

Create a scale set in same resource group. 

Prerequisites is the to create a public Standard Load Balancer. 

The name and public IP address created are automatically configured as the load balancer's front end. 

You can deploy a scale set with a Windows Server image or Linux image such as RHEL, CentOS, Ubuntu, or SLES. 

Search Virtual machine scale sets. Select Create on the Virtual machine scale sets page, which will open the Create a virtual machine scale set page. 

In the Basics tab, under Project details, select the subscription and then choose to same resource. 

Type name as the name for your scale set. 

In Region, select the same region. 

Leave the default value of Scale Set VMs for Orchestration mode. 

Enter your desired username, and select which authentication type as SSH and give the same SSH key and username as azureadmin 

 

 

Select the image or browse the image for the scale set 

Select the size for the disk. 

Select the authentication type as SSH and provide the same username as azureadmin and SSH key 

Click Next for the disk tab select the OS disk type as per choice 

 

Click Next for the networking section 

 

Select the same virtual network as selected for virtual machine. 

 

Give the instance count and the scaling policy as manual or custom. 

Select Next and keep the other things as default. 

Click on review and create and the scale set. 

 

Execute the webserver.sh script in the VMSS extension 

Install webserver apache/nginx 

Install php with extensions 

Modify the Moodle, nginx, PHP configuration with on-prem. 

Change the database details in moodle configuration file (/moodle/config.php) 

Download and Copy the nginx config file from blob storage to the nginx config folder. 

Download and Copy the php config file from blob storage to the php config folder. 

Create a local copy of moodle from shared folder 

Set a cron job to copy the shared moodle content to /var/www/html/ folder whenever there is a change in timestamp in shared folder. 

Restart servers 

Restart nginx server 

Restart php-fpm server 

With the above steps Moodle infrastructure is ready 

User now hit the load balancer DNS name to get the migrated moodle web page. 

  

 
 

 
