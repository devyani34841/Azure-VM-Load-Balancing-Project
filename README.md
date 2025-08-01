# Azure-VM-Load-Balancing-Project
A comprehensive project demonstrating Azure Load Balancer for distributing web traffic across Virtual Machines, including Azure Bastion and NAT Gateway for secure and scalable infrastructure. This repository includes detailed implementation steps and screenshots
# Project : Azure Public Load Balancer with High Availability

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
* 
    ![Create NAT Gateway - Basics](https://github.com/devyani34841/Azure-VM-Load-Balancing-Project/blob/a4c13264e4cd30f864571c402d72aa16674c81bd/images/NatGateway.png)

   
    ![Create NAT Gateway - Outbound IP](https://github.com/devyani34841/Azure-VM-Load-Balancing-Project/blob/d6335798b79f67bf0165c9541a7ceb5e3d2e3c0a/images/NatGateway2.png)

  
    ![Create NAT Gateway - Subnets](https://github.com/devyani34841/Azure-VM-Load-Balancing-Project/blob/1223e44cc2cd5b2133a65c094fe30650d7dc9121/images/NatGateway3.png)
  

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
* 
    ![Create Azure Bastion - Basics](https://github.com/devyani34841/Azure-VM-Load-Balancing-Project/blob/ef8e34ed7cba21148033279789db1beaa32f356d/images/createbastion.png)

  
    ![Azure Bastion Deployment In Progress](https://github.com/devyani34841/Azure-VM-Load-Balancing-Project/blob/fc5a7c8ffe8df4c55a01b51f863f8a7c23616d11/images/c%20bastion.png)
  

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
    ![Create VM2 - Basics](https://github.com/devyani34841/Azure-VM-Load-Balancing-Project/blob/40a448086188e64f87da2db1b9a402500dce4fb8/images/virtualmachine.png)

    ![Create VM2 - Basic](https://github.com/devyani34841/Azure-VM-Load-Balancing-Project/blob/2ce338c54178ee0bc4063930a8b07e901a1c7ee4/images/virtualmachinecomplete.png)
  
### D. Install IIS and Configure Load Balancer

#### 1. Connect to VMs and Install IIS

* **Purpose:** To install the Internet Information Services (IIS) web server on both VMs so they can serve web content
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
* 
    ![Connect to VM via Bastion](https://github.com/devyani34841/Azure-VM-Load-Balancing-Project/blob/94722000b890e0b7fa0b57150e96c442a110a8fa/images/fbastion.png)
   
    ![Install IIS - Server Manager Roles](https://github.com/devyani34841/Azure-VM-Load-Balancing-Project/blob/fed3c62f67f05aed6390f491a61331709e040dc7/images/out.png)
   
    ![IIS Default Page in VM](https://github.com/devyani34841/Azure-VM-Load-Balancing-Project/blob/d31ecb59d165e2e697a668927c769609c4839639/images/output.png)
  

#### 2. Create Public Azure Load Balancer

* **Purpose:** To distribute incoming HTTP traffic (Port 80) to the two web server VMs in the backend pool.
* **Steps:**
    1.  In the Azure Portal search bar, search for `Load balancers` and select it.
    2.  Click on `+ Create`.
    3.  **Basics Tab:**
        * **Subscription:** Azure for Student 
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

## 5. Testing and Verification

* **Purpose:** To confirm that the Load Balancer is correctly distributing traffic to the backend VMs.
* **Steps:**
    1.  Navigate to the `PublicWebLoadBalancer` resource in Azure Portal.
    2.  From the "Overview" page, copy the "Frontend public IP address".
    3.  Open a web browser on your **local machine** and paste the copied Public IP address into the address bar. Press Enter.
    4.  You should see the default IIS welcome page.

* **Screenshot:**
* 
    ![Load Balancer Public IP Test](https://github.com/devyani34841/Azure-VM-Load-Balancing-Project/blob/7bb862a612d3096c269450fe2e7b93b5c584091a/images/Screenshot%202025-07-21%20000006.png)
  




   
