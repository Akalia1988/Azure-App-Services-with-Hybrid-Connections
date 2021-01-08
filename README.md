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
















