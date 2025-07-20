# Azure-VM-Load-Balancing-Project
A comprehensive project demonstrating Azure Load Balancer for distributing web traffic across Virtual Machines, including Azure Bastion and NAT Gateway for secure and scalable infrastructure. This repository includes detailed implementation steps and screenshots
# Project 05: Azure Public Load Balancer with High Availability

## 1. Introduction

The objective of this project is to create a public Azure Load Balancer using the Azure portal. It is configured to manage a backend pool containing two virtual machines (VMs). Additionally, Azure Bastion, a NAT Gateway, a virtual network, and necessary subnets have been established to support the load balancer configuration.

This project aims to address the growing traffic, performance issues, and downtime of an e-commerce platform, ensuring high availability and scalability.

## 2. High-Level Architecture

The architecture of this project is illustrated in the following diagram:

![Azure Load Balancer Architecture](https://github.com/devyani34841/Azure-VM-Load-Balancing-Project/blob/9abc6369ae1744f03142cde212f39d8ebb7dff25/images/architecture.png)

**Components Explanation:**

* **Resource Group:** A logical container for all Azure resources.
* **Virtual Network (VNet):** A private network for all Azure resources.
* **Subnets:** `BackendSubnet` (for VMs) and `AzureBastionSubnet` (for Bastion).
* **Load Balancer:** Distributes incoming traffic among VMs.
* **Load Balancer Public IP:** Entry point for incoming traffic.
* **VM1 & VM2 (Backend Pool):** Servers handling web requests.
* **NAT Gateway:** Enables outbound internet access for VMs.
* **NAT Gateway Public IP:** Public IP for outbound connections.
* **Azure Bastion:** Secure RDP/SSH access to VMs.

## 3. Prerequisites

* An Azure account with an active subscription.

## 4. Implementation Steps 

Here, each step performed in the Azure portal is explained in detail.

### A. Initial Setup

#### 1. Create Resource Group

* **Purpose:** To create a logical container for all our Azure resources, making management and cleanup easier.
* **Steps:**
    1.  Log in to Azure Portal (`portal.azure.com`).
    2.  In the search bar, search for `Resource groups` and select it.
    3.  Click on `+ Create`.
    4.  Fill in the details:
        * **Subscription:** Azure for Student 
        * **Resource group name:** `LoadBalancerProjectRG`
        * **Region:** `(Your Selected Region, e.g., Central India)`
    5.  Click `Review + create`, then `Create`.

* **Screenshot:**
    ![Create Resource Group - Basics](https://github.com/devyani34841/Azure-VM-Load-Balancing-Project/blob/18331ad9d657536ebad86588d927436095d58659/images/Resource.png)
   
#### 2. Create Virtual Network (VNet) and Subnets

* **Purpose:** To establish a private network for all Azure resources and define subnets for different services (VMs and Bastion).
* **Steps:**
    1.  In the Azure Portal search bar, search for `Virtual networks` and select it.
    2.  Click on `+ Create`.
    3.  **Basics Tab:**
        * **Subscription:** Azure for Student 
        * **Resource Group:** `LoadBalancerProjectRG`
        * **Name:** `LoadBalancerVNet`
        * **Region:** `(Your Selected Region, same as RG)`
    4.  **IP Addresses Tab:**
        * `IPv4 address space`: (Leave default, e.g., 10.0.0.0/16)
        * Click `+ Add subnet`:
            * **Subnet name:** `BackendSubnet`
            * **Subnet address range:** (Leave default, e.g., 10.0.0.0/24)
            * Click `Add`.
        * Click `+ Add subnet` again:
            * **Subnet name:** `AzureBastionSubnet` (Exact name required for Bastion)
            * **Subnet address range:** (Ensure it's `/27` or larger, e.g., 10.0.1.0/27)
            * Click `Add`.
    5.  Click `Review + create`, then `Create`.

* **Screenshots:***
*  
    ![Create VNet - Basics](https://github.com/devyani34841/Azure-VM-Load-Balancing-Project/blob/b9b69e01c3add43047d54172c29be86baab193a1/images/Backendsubnet.png)

   
    ![Create VNet - IP Addresses with Subnets](https://github.com/devyani34841/Azure-VM-Load-Balancing-Project/blob/64cecefdb0dfda6545b1364086495a6aec0531ce/images/Bastion.png)


  ![Create VNet - IP Addresses ](https://github.com/devyani34841/Azure-VM-Load-Balancing-Project/blob/7f852dc20757ab7b9160b4d90992f0fd3dee8ec7/images/virtualnetwork.png)
   
### B. Deploying Network Services

#### 1. Create NAT Gateway

* **Purpose:** To provide outbound internet access for VMs within the `BackendSubnet` without public IPs on the VMs.
* **Steps:**
    1.  In the Azure Portal search bar, search for `NAT gateways` and select it.
    2.  Click on `+ Create`.
    3.  **Basics Tab:**
        * **Subscription:** Azure for Student
        * **Resource Group:** `LoadBalancerProjectRG`
        * **NAT gateway name:** `LoadBalancerNATGateway`
        * **Region:** `(Your Selected Region)`
        * **Availability zone:** `No zone`
    4.  **Outbound IP Tab:**
        * Click `Create a new public IP address`.
        * **Name:** `LoadBalancerNATGatewayIP`
        * Click `OK`.
    5.  **Subnets Tab:**
        * **Virtual network:** `LoadBalancerVNet`
        * Check the box next to `BackendSubnet`.
    6.  Click `Review + create`, then `Create`.

* **Screenshots:**
    ![Create NAT Gateway - Basics](images/nat_gateway_basics.png)
   
    ![Create NAT Gateway - Outbound IP](images/nat_gateway_outbound_ip.png)
  
    ![Create NAT Gateway - Subnets](images/nat_gateway_subnets.png)
  

#### 2. Configure Azure Bastion

* **Purpose:** To enable secure and seamless RDP/SSH access to virtual machines directly through the Azure portal.
* **Steps:**
    1.  In the Azure Portal search bar, search for `Bastion` and select it.
    2.  Click on `+ Create`.
    3.  **Basics Tab:**
        * **Subscription:** Your Subscription
        * **Resource Group:** `LoadBalancerProjectRG`
        * **Name:** `LoadBalancerBastion`
        * **Region:** `(Your Selected Region)`
        * **Virtual network:** `LoadBalancerVNet`
        * **Subnet:** `AzureBastionSubnet` (should be pre-selected)
        * **Public IP address:** Click `Create new` and provide name `LoadBalancerBastionIP`, then `OK`.
    4.  Click `Review + create`, then `Create`.

* **Screenshots:**
    ![Create Azure Bastion - Basics](images/bastion_basics.png)
    *Description: Screenshot of the Azure Bastion creation - Basics tab.*
    ![Azure Bastion Deployment In Progress](images/bastion_deploying.png)
    *Description: Screenshot showing Azure Bastion deployment in progress (can take a few minutes).*

### C. Deploying Virtual Machines

#### 1. Create VM1 (`lb-vm1`)

* **Purpose:** To create the first web server VM for the load balancer's backend pool.
* **Steps:**
    1.  In the Azure Portal search bar, search for `Virtual machines` and select it.
    2.  Click `+ Create` and then `Azure virtual machine`.
    3.  **Basics Tab:**
        * **Subscription:** Your Subscription
        * **Resource Group:** `LoadBalancerProjectRG`
        * **Virtual machine name:** `lb-vm1`
        * **Region:** `(Your Selected Region)`
        * **Availability options:** `Availability zone`
        * **Availability zone:** `Zone 1`
        * **Image:** `Windows Server 2019 Datacenter`
        * **Size:** (e.g., `Standard_B2s` or `Standard_B1s`)
        * **Administrator account:** Choose a username (e.g., `azureuser`) and set a strong password. **Remember this password!**
        * **Inbound port rules:** `None`
    4.  **Networking Tab:**
        * **Virtual network:** `LoadBalancerVNet`
        * **Subnet:** `BackendSubnet`
        * **Public IP:** `None` (Crucial for secure setup with Load Balancer and NAT Gateway)
    5.  Click `Review + create`, then `Create`.

* **Screenshots:**
    ![Create VM1 - Basics](images/vm1_basics.png)
    *Description: Screenshot of VM1 creation - Basics tab, highlighting name, region, availability zone, and admin account.*
    ![Create VM1 - Networking](images/vm1_networking.png)
    *Description: Screenshot of VM1 creation - Networking tab, showing VNet, Subnet, and Public IP set to None.*

#### 2. Create VM2 (`lb-vm2`)

* **Purpose:** To create the second web server VM for the load balancer's backend pool.
* **Steps:**
    1.  Repeat the steps for creating VM1.
    2.  **Basics Tab changes:**
        * **Virtual machine name:** `lb-vm2`
        * **Availability zone:** `Zone 2`
        * Ensure the same Administrator account username and password as VM1.
    3.  **Networking Tab changes:**
        * Ensure **Public IP is `None`**.
    4.  Click `Review + create`, then `Create`.

* **Screenshot:**
    ![Create VM2 - Basics](images/vm2_basics.png)
    *Description: Screenshot of VM2 creation - Basics tab, highlighting name and availability zone.*

### D. Install IIS and Configure Load Balancer

#### 1. Connect to VMs and Install IIS

* [cite_start]**Purpose:** To install the Internet Information Services (IIS) web server on both VMs so they can serve web content[cite: 44].
* **Steps (for both `lb-vm1` and `lb-vm2`):**
    1.  Go to the VM's overview page (e.g., search for `lb-vm1` and click on it).
    2.  Click `Connect` -> `Bastion` -> `Use Bastion`.
    3.  Enter the Administrator username (e.g., `azureuser`) and password, then click `Connect`.
    4.  Once connected to the VM's desktop:
        * Open **Server Manager** (it usually opens automatically).
        * Click `Add roles and features`.
        * Follow the wizard, clicking `Next` until `Server Roles`.
        * Check the box next to **`Web Server (IIS)`**. Click `Add Features` in the pop-up.
        * Continue clicking `Next` until `Confirmation`, then click `Install`.
        * After installation, close Server Manager.
        * **Verify IIS:** Open Internet Explorer (or any browser) inside the VM and go to `http://localhost`. You should see the default IIS welcome page.
        * Close the Bastion browser tab to disconnect.

* **Screenshots:**
    ![Connect to VM via Bastion](images/bastion_connect_vm.png)
    *Description: Screenshot showing connecting to a VM using Azure Bastion.*
    ![Install IIS - Server Manager Roles](images/iis_install_roles.png)
    *Description: Screenshot inside the VM, showing Server Manager with "Web Server (IIS)" selected for installation.*
    ![IIS Default Page in VM](images/iis_localhost_vm.png)
    *Description: Screenshot inside the VM, showing the default IIS welcome page accessed via `http://localhost`.*

#### 2. Create Public Azure Load Balancer

* **Purpose:** To distribute incoming HTTP traffic (Port 80) to the two web server VMs in the backend pool.
* **Steps:**
    1.  In the Azure Portal search bar, search for `Load balancers` and select it.
    2.  Click on `+ Create`.
    3.  **Basics Tab:**
        * **Subscription:** Your Subscription
        * **Resource Group:** `LoadBalancerProjectRG`
        * **Name:** `PublicWebLoadBalancer`
        * **Region:** `(Your Selected Region)`
        * **SKU:** `Standard`
        * **Type:** `Public`
        * **Tier:** `Regional`
        * **Availability zone:** `Zone-redundant`
    4.  **Frontend IP configuration Tab:**
        * Click `+ Add a frontend IP configuration`.
        * **Name:** `LoadBalancerFrontendIP`
        * **Public IP address:** Click `Create new`.
            * **Name:** `LoadBalancerPublicIP`
            * **SKU:** `Standard`
            * **Availability zone:** `Zone-redundant`
            * Click `OK`.
        * Click `Add`.
    5.  **Backend pools Tab:**
        * Click `+ Add a backend pool`.
        * **Name:** `WebBackendPool`
        * **Virtual network:** `LoadBalancerVNet`
        * Click `+ Add` (under Virtual machines).
        * Select `lb-vm1` and `lb-vm2`.
        * Click `Add`.
        * Click `Add` on the backend pool creation page.
    6.  **Inbound rules Tab:**
        * Click `+ Add a load balancing rule`.
        * **Name:** `HTTPRule`
        * **Frontend IP address:** `LoadBalancerFrontendIP`
        * **Backend pool:** `WebBackendPool`
        * **Protocol:** `TCP`
        * **Port:** `80`
        * **Backend port:** `80`
        * **Health probe:** Click `Create new`.
            * **Name:** `HTTPProbe`
            * **Protocol:** `HTTP`
            * **Port:** `80`
            * **Path:** `/`
            * Click `OK`.
        * Click `Add`.
    7.  Click `Review + create`, then `Create`.

* **Screenshots:**
    ![Create Load Balancer - Basics](images/lb_basics.png)
    *Description: Screenshot of Load Balancer creation - Basics tab.*
    ![Create Load Balancer - Frontend IP](images/lb_frontend_ip.png)
    *Description: Screenshot of Load Balancer creation - Frontend IP configuration tab with public IP.*
    ![Create Load Balancer - Backend Pool](images/lb_backend_pool.png)
    *Description: Screenshot of Load Balancer creation - Backend pools tab showing lb-vm1 and lb-vm2 added.*
    ![Create Load Balancer - Inbound Rule HTTP](images/lb_inbound_rule.png)
    *Description: Screenshot of Load Balancer creation - Inbound rules tab with the HTTP load balancing rule and health probe configured.*

## 5. Testing and Verification

* **Purpose:** To confirm that the Load Balancer is correctly distributing traffic to the backend VMs.
* **Steps:**
    1.  Navigate to the `PublicWebLoadBalancer` resource in Azure Portal.
    2.  From the "Overview" page, copy the "Frontend public IP address".
    3.  Open a web browser on your **local machine** and paste the copied Public IP address into the address bar. Press Enter.
    4.  You should see the default IIS welcome page.

* **Screenshot:**
    ![Load Balancer Public IP Test](images/lb_public_ip_test.png)
    *Description: Screenshot of a web browser showing the IIS default page when accessing the Load Balancer's public IP.*

* **Optional (Visual Load Balancing Test):**
    1.  Customize the `iisstart.htm` file in `C:\inetpub\wwwroot` on `lb-vm1` to include "Hello from VM1".
    2.  Customize the `iisstart.htm` file in `C:\inetpub\wwwroot` on `lb-vm2` to include "Hello from VM2".
    3.  Refresh the Load Balancer's public IP in your browser multiple times. You should see the message alternate between "Hello from VM1" and "Hello from VM2", demonstrating the load balancing in action.

* **Screenshot (Optional):**
    ![Load Balancer Visual Test](images/lb_visual_test.png)
    *Description: Screenshot showing the browser alternating between "Hello from VM1" and "Hello from VM2" upon refresh.*

## 6. Clean Up

To avoid incurring unnecessary costs, it is a good practice to delete the resource group once the project is completed and verified.

* **Steps:**
    1.  In the Azure Portal search bar, search for `Resource groups` and select it.
    2.  Find and select your resource group, `LoadBalancerProjectRG`.
    3.  Click `Delete resource group` at the top of the page.
    4.  Type the resource group name (`LoadBalancerProjectRG`) to confirm deletion and click `Delete`.

---

### How to Upload to GitHub:

1.  **Create a GitHub Account:** If you don't have an account, sign up at [github.com](https://github.com/).
2.  **Create a New Repository:**
    * Log in to GitHub.
    * On your GitHub homepage, click the `New` button (on the left side or the `+` icon in the top right corner) and select `New repository`.
    * **Repository name:** Give it a meaningful name like `Azure-Load-Balancer-Project` (or any other meaningful name).
    * **Description:** Write a short description of your project.
    * **Public/Private:** Select `Public` (so everyone can see it, and it's good for your resume/portfolio).
    * **Initialize this repository with a README:** **MAKE SURE to check this box.** This will create an empty `README.md` file for you to edit.
    * Click `Create repository`.
3.  **Edit the `README.md` file:**
    * After going to your repository, you will see the `README.md` file.
    * Click on the pencil icon (edit button) next to that file.
    * Copy the entire `Project Documentation Structure` (the content provided above, starting from `# Project 05...`) and paste it into this `README.md` editor box.
4.  **Add Screenshots:**
    * Wherever it says `![Alt text](images/screenshot_name.png)`, you need to add your screenshots.
    * **Method to add screenshots:**
        * The easiest way is: While editing the `README.md` file, **drag and drop** your screenshots directly from your computer into the `README.md` editor box. GitHub will automatically upload those screenshots to your repository (usually in an `images` folder) and insert their Markdown link `![Alt text](images/screenshot_name.png)` into your `README.md` file.
        * If drag-drop doesn't work, you can first create an `images` folder in your GitHub repository (in the `Code` tab, click `Add file` -> `Create new file`, type `images/` as the name, then upload your files there). Then link those images in `README.md` using `![Alt text for image](images/your_screenshot_name.png)`.
5.  **Commit Changes:**
    * Once you have put all the details and screenshots in `README.md`, scroll to the bottom of the editor page.
    * In the "Commit changes" section, write a short message, like `Initial project documentation with Azure steps and screenshots`.
    * Click the `Commit changes` button.

Your project is now live on GitHub, and you can share its link in your resume or portfolio! This approach is professional and descriptive.
