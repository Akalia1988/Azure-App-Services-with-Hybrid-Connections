# Azure-App-Services-with-Hybrid-Connections

Our organization has decided to modernize our one of web application and move it to Azure app service. App connects to SQL Server on azure SQL hosted VM'S

VNET intergration was the easy option in order to connect our App services to SQLserver but due to technical diffuculties we couldnt implement it so i came up with hybrid connection idea. 

What is Azure App Service Hybrid Connections?
Within App Service, Hybrid Connections can be used to access application resources in other networks. It provides access from your app to an application endpoint. It does not enable an alternate capability to access your application. As used in App Service, each Hybrid Connection correlates to a single TCP host and port combination. The Hybrid Connections feature does not know or care what the application protocol is, or what you are accessing. It is simply providing network access.

How it works?

The Hybrid Connections feature consists of two outbound calls to Azure Service Bus Relay. There is a connection from a library on the host where your app is running in App Service. There is also a connection from the Hybrid Connection Manager (HCM) to Service Bus Relay. The HCM is a relay service that you deploy within the network hosting the resource you are trying to access.
Through the two joined connections, your app has a TCP tunnel to a fixed host:port combination on the other side of the HCM. The connection uses TLS 1.2 for security and shared access signature (SAS) keys for authentication and authorization.

When your App Service on Azure makes a DNS request that matches a configured Hybrid Connection endpoint, the outbound TCP traffic will be redirected through the Hybrid Connection.

![image](https://user-images.githubusercontent.com/58148717/104042078-99fbd480-519f-11eb-848c-2a29765c75f5.png)

App Service Hybrid Connection benefits
There are several benefits to the Hybrid Connections capability, including:

Apps can access on-premises systems and services securely.
The feature does not require an internet-accessible endpoint.
It is quick and easy to set up.
Each Hybrid Connection matches a single host:port combination, helpful for security.
It normally does not require firewall holes. The connections are all outbound over standard web ports.
Because the feature is network level, it is agnostic to the language used by your app and the technology used by the endpoint.
It can be used to provide access in multiple networks from a single app.

I did we provide another Recommendations for example whitelisting the Public IP, go wtth Azure Service Environment (ASE) Create the L7 load balancer (Application gateway it will provide the private IP address) then attached the App service to the backend pool of the Load balancer and traffic would flow through microsoft network instead of public internet. 

what is Azure service Environment? 
The ASE is a solution where you deploy one or more single-tenant Isolated tier app services plans in an environment that is connected to a virtual network. This means that every instance in the app service plan is already in the virtual network and can communicate directly with Azure virtual machines in the same network without needing any VPN or hybrid connections.

ASE is suitable if you need an isolated deployment and offers much greater scalability and simpler networking

![image](https://user-images.githubusercontent.com/58148717/104043052-dda30e00-51a0-11eb-84e0-f4b38bfc9b67.png)

How to set it up?

Before you setup the Azure Hybrid Connection you need the following information:

You need to decide which computer you need to install the Hybrid Connection Manager on. The Hybrid Connections feature requires a relay agent in the network that hosts your Hybrid Connection endpoint. That relay agent is called the Hybrid Connection Manager (HCM). The computer that will host the HCM should be able to resolve the destination service hostname or fully qualified domain name, and should be able to connect to that TCP service on a specific port. It is recommended to install the HCM in the DMZ of your network, but it is not mandatory. Please note that if you install the HCM in the DMZ you might need to open ports on the internal firewall, and not the external company firewall. HCM can be installed on Windows Server 2012 or later. It also works on Windows 10. Linux support is coming soon

You need the SQL Server hostname or fully qualified domain name and the port number for the SQL Server service. The default port for SQL Server is 1433

Configuring the Hybrid Connection

![image](https://user-images.githubusercontent.com/58148717/104045371-11cbfe00-51a4-11eb-92e9-4991cf61d095.png)

![image](https://user-images.githubusercontent.com/58148717/104045449-31632680-51a4-11eb-8b51-f842a39ecf6c.png)

IMPORTANT NOTE: App Service Hybrid Connections are only available in Basic, Standard, Premium, and Isolated pricing SKUs. There are limits tied to the pricing plan. If you have an account through your Visual Studio subscription, you usually have a limit of 5 hybrid connections. In my case I can create up-to 25 hybrid connections for my account as you can see in the screenshot above.

Pricing plan	Number of Hybrid Connections usable in the plan
Basic	5
Standard	25
Premium	200
Isolated	200

Click on the Add hybrid connection as shown in the previous screenshot

In the Create new hybrid connection blade, you must fill in the following fields for SQL server hybrid connection:

Hybrid connection name: Subscription unique reference name for the hybrid connection.
Endpoint Host: Hostname for the endpoint which must resolve on the host where the Hybrid Connection Manager (HCM) is installed.
Endpoint Port: The destination port for the hybrid connection for that service.
Servicebus namespace: hybrid connection is built using service bus namespaces which are used as containers for messaging. In my case I want to create a new Service Bus namespace.
Location: choose the closest region to your service for less latency.
Name: Service Bus namespace name (if you are creating a new Service Bus namespace). This namespace must be unique in Azure and not just your subscription.

Once you click on OK button, Azure will start creating your hybrid connection. Notice that after creation the status of the hybrid connection is “Not connected” in the screenshot below. This is okay as we did not deploy the HCM or establish the connectivity yet.

![image](https://user-images.githubusercontent.com/58148717/104045809-b9493080-51a4-11eb-9590-b1013241147f.png)

Add a new hybrid connection for the SQL Server:

![image](https://user-images.githubusercontent.com/58148717/104045905-da118600-51a4-11eb-9492-70a4f14a8cac.png)

To complete our hybrid connection setup, we must download and install the Hybrid Connection Manager (HCM) from the link in the portal:

![image](https://user-images.githubusercontent.com/58148717/104045959-f57c9100-51a4-11eb-981c-9bcdd670107a.png)

Once you have downloaded the package, run it and perform following steps:

![image](https://user-images.githubusercontent.com/58148717/104046021-104f0580-51a5-11eb-9520-7930c7e6f6e9.png)

The following window will be shown:

![image](https://user-images.githubusercontent.com/58148717/104046101-2c52a700-51a5-11eb-9b28-ce58895be7db.png)

Click on the Add a new Hybrid Connection link, login to your Azure subscription and choose the subscription that you created the hybrid connection under

Highlight both hybrid connections and click on the Save button, and then you should see the next window. Please note that the AZURE STATUS column is “Not Connected“.

To change the status to connected you either need to restart the Azure Hybrid Connection Manager Service or reboot your computer:

![image](https://user-images.githubusercontent.com/58148717/104046266-6de35200-51a5-11eb-80d9-684fe458b4f7.png)

Here is the hybrid connection status in the Azure Portal after the service restart or computer reboot

![image](https://user-images.githubusercontent.com/58148717/104046427-aedb6680-51a5-11eb-8770-038aea21054b.png)

last dont forget to add the connection strings to code base. 































































