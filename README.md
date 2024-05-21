# Introduction

This is my own preparation material I created for the [Azure Certified aAdministraotr Associate AZ-104 exam](https://learn.microsoft.com/en-us/credentials/certifications/azure-administrator). The content is primarily from four sources:

https://skillcertpro.com/az-104-exam-questions/

# Fundamentals

Azure is generally available in more than 60 regions in 140 countries.

Remediation information in policies shall be added to metadata section https://learn.microsoft.com/azure/defender-for-cloud/custom-security-policies?pivots=azure-portal#enhance-your-custom-recommendations-with-detailed-information

Azure Policies are used to enforce rules and compliance (support all resource types, real-time evaluation and enforcement), apply policies at scale (MGs, multiple/aggregated policies, exclusions), perform remediation and exercise governance. There's a long list of built-in policies (e.g. SKUs, locations) but new definitions can also be created.

MGs are used to manage governance across multiple Azure subscriptions.

Policy type could be `Microsoft.Network/loadbalancers`, allowed SKUs could be `Microsoft.Network/loadbalancers/sku.name`, effect could be `Allow` or `Deny`.

# Networking

**Session persistence** based on Client I and protocol makes sure that users connect to same server for all requests to an application (e.g. Load Balancer scenario)

Peering: no overlapping IP addresses. Requires both directions to work.

A Network Security Group (NSG) in Azure is a security feature that helps control inbound and outbound traffic to and from Azure resources within a Virtual Network (VNet). NSGs contain security rules that allow or deny traffic based on factors such as source and destination IP addresses, ports, and protocols. Network interfaces and subnets can be assigned with NSGs.

NSG rules to allow TCP or ICMP traffic for specific ports. Firewall can do the same across multiple VNets.

NSG flow logs allow you to capture and analyze the traffic that is allowed or denied by NSG rules. These logs can be sent to Azure Monitor logs, Azure Storage, or Event Hubs for further analysis.

Application security groups allow you to group together the network interfaces from multiple virtual machines, and then use the group as the source or destination in an NSG rule. This potentially reduces the number of needed NSG rules. The network interfaces must be in the same virtual network.

`New-AzNetworkSecurityRuleConfig` allows you to create a rule and provide the type, protocol, direction, and port number. `New-AzNetworkSecurityGroup` creates a network security group (NSG).

Azure Network Watcher provides tools to monitor, diagnose, view metrics, and enable or disable logs for resources in an Azure virtual network. Network Watcher is a regional service that enables you to monitor and diagnose conditions at a network scenario level. It comes with various features:

- IP flow verify: Quickly diagnose connectivity issues from or to the internet, and from or to your on-premises environment (e.g. identify if network security rule blocks traffic to VM). Simulates traffic between source and destination. Used to test NSGs and UDRs.
- Next hop diagnostics: View the next connection point (or next hop) in your network route, and analyze your network routing configuration (e.g. to confirm traffic reaches some target destination, view route table)
- Visualize network topology: understand network infra visually (diagram)
- Connection Monitor: unified, end-to-end connection monitoring in Network Watcher. Also supports hybrid environments. Primarily used for continuous monitoring and performance measurements between Azure resources and external endpoints.
- Traffic Analysis: focuses on network activity across subscriptions, hot spots, regions

Inspect all network traffic between VMs using Azure Network Watcher Extension & Packet Capture. Packet Capture is also helpful to identify network anomalies/intrusions. 

DNS Record type TXT is the text record. It's used to associate text strings with a domain name. Azure and Microsoft 365 use TXT records to verify domain ownership. Azure DNS doesn't support Domain Name System Security Extensions.

Azure DNS provides RBAC, activity logs, and resource locking.

Azure DNS lets you create private zones. Private DNS zones are not visible on the Internet, and don't require that you use a domain registrar. These provide name resolution for virtual machines (VMs) within a virtual network and between virtual networks without having to create a custom DNS solution. To publish a private DNS zone to your virtual network, you'll specify the list of virtual networks that are allowed to resolve records within the zone.

Azure alias records enable a zone apex domain to reference other Azure resources from the DNS zone. Alias records can point to Traffic Manager profiles, CDN endpoints, Public IP, Front Door profile. Alias records support A, AAAA and CNAME records.

Azure verifies DNS via TXT records at the registrar

**Virtual Network Links** allow association of a virtual network to a private DNS zone. Azure DNS Private Resolver is used to proxy DNS queries between on-premises environments and Azure DNS.

To migrate DNS domains to Azure CLI should be used for least administrative effort (via zone file).

Load balancing rules define how traffic is distributed between VMs.

Azure Application Gateway can also be used to balance load to private IP addresses (of VMs).

Standard load balancers balance load across all VMs in a VNet/backend pools, internal load balancers balance load between VMs / VM scale sets.

Site-to-site VPN requires a gateway subnet (it actually also needs to be named "GatewaySubnet") and a local VPN gateway. Point-to-site VPNs require a client certificate to be present.

A Virtual Network Gateway will handle failover between active and standby instance of Azure VPN gateway. Each Azure VPN gateway needs its own Public IP address.

# Compute

Spot instance can be evicteed when Azure needs the capacity for other pay-as-you-go workloads or when the price of the spot instance exceeds the maximum price that you have set.

A VM will have a downtime if its size is changed or network interfces are added.

DSC extensions can be used to configure VMs during deployment

IOPS summary for disk types:
Ultra SSD: 160.000
Premium SSD: 20.000
Standard SSD: 6.000
Standard HDD: 2.000

Azure Files should be used as persistant storage for Container Instances.

If you want to run a Docker container as an Azure web service, you must configure the Publish option and select Docker container.

You have an Azure subscription that contains a container app named App1. App1 is configured to use cached data. You plan to create a new container. You need to ensure that the new container automatically refreshes the cache used by App1 > sidecar, You can define multiple containers in a single container app to implement the sidecar pattern, for example, an agent that reads logs from the primary app container in a shared volume and forwards them to a logging service. https://learn.microsoft.com/azure/container-apps/containers

`Add-AzVhd`: Uploads an on-premises VHD (VM image) to Azure
`New-AzVM` : Used to create a new virtual machine
`New-AzDisk`: Create new managed disk
`New-AzDataShare`: Create new Azure data share
`Move-AzResource`: Move resources (e.g. VMs) between subscriptions

Resource Manager deployments require access keys to read secrets from KeyVault.

# Storage & Data

Delete locks can be applied to VMs, subscriptions and resource groups

Storage versioning must be enabled to support replication for both the source and destination account. Change feed must be enabled on the source account.

Log Analytics supports KQL-based specific [query syntax](https://learn.microsoft.com/en-gb/azure/azure-monitor/logs/get-started-queries) with basic statements like `search`, `where`, `sort`, `top` and operations based on tables (e.g. `SecurityEvent | take 10`) to cover aspects like

- Search and sort by value, time, property state, and more
- Join data from multiple tables
- Aggregate large sets of data
- Perform intricate operations with minimal code

Aggregation and visualization are implemented via `summarize` and `render` operator. See the [KQL reference](https://learn.microsoft.com/en-us/azure/data-explorer/kusto/query/kql-quick-reference).

Log Analytics can run on different platforms, including other Cloud providers.

Eligible subscriptions for Azure Data Box are Enterprise, CSP and Sponsorship. For a data box order the Owner or Contributor role is mandatory.

# Identity

External collaboration settings let you specify which roles in your organization can invite external users for B2B collaboration. They don't need a specific IdP/domain, settings can be managed in External Collaboration.

Azure AD Identity Protection includes MFA and requires Azure AD Premium P2 Licences. To configure Conditional Access policies (e.g. for certain users) the grant control of the policy needs to be adjusted.

To enforce on-premise AD Connect security and password policies in hybrid scenario Pass-through authentication must be used (Password Hash sync does not work) since there is always a delay in password sync and policies can't be applied in real-time since validation occurs against the Azure AD instead of the OnPrem AD in case of Password Hash Synchronization.

Azure AD Connect needs to have a Microsoft Entra Global Administrator account or Hybrid Identity Administrator account for the AD tenant you want to integrate with.

Essential steps to setup AD COnnect for Hybrid usage:

- install AD Connect on  server in the domain
- setup custom domain in Azure AD
- ensure objects conformity via IdFix
- setup Pass-Through authentication (or Password Hash-based sync)
- add "https://autologon.microsoftazuread-sso.com" to intranet zone settings
- Confirm users were synced from onPrem to AD

IdFix is a tool that identifies issues with the active directory identity objects and provides remediation measures to overcome it. It is useful to debug AD Connect sync issues.

Azure AD Connect Health setup requires global admin rights

MFA does not necessarily require a phone number

PIM can define an Activation Maximum duration, after that period (e.g. 6 hours) a role assignment is automatically expired. Then the role has to be activated again.

PIM does not allow you to manage the roles Account Admin, Service Admin and Co-Admin (classic subscription admin roles).

(Local) Administrators for Azure AD are also managed in the "Devices" settings

# Availability & Backup

18 VMs in two fault domains and 10 update domains. Max unavailable VMs for planned maintenance is 2 (18 virtual machines are shared across 10 update domains. The first 10 virtual machines go to 10 update domains, so eight update domains will have two virtual machines).

https://learn.microsoft.com/azure/app-service/troubleshoot-diagnostic-logs

VM Scale Sets and Availability Sets combined with Managed Disk have a defined SLA of 99.95%

30 days is default retention period of virtual machine backups in the default backup policy (Recovery Service Vault)

Before deleting a Recovery Service Vault, all items in the soft delete state must be removed (soft delete enabled by default) 

To achieve good performance for (public) web apps around the world setup a CDN with Custom Origin Types.

Azure Monitor is based on signal types to monitor the environment:

- Metric alerts provide an alert trigger when a specified threshold is exceeded. For example, a metric alert can notify you when CPU usage is greater than 95 percent.
- Activity log alerts notify you when Azure resources change state. For example, an activity log alert can notify you when a resource is deleted.
- Log alerts are based on things written to log files. For example, a log alert can notify you when a web server has returned a number of 404 or 500 responses.

Every alert is based on a (pre-defined or custom) rule based on resource, condition, actions (e.g. send SMS, use runbbok, trigger logic app) and alert details (name and level).

There are also dynamic threshold metric alerts (define look-back period and allowed deviations).

Creating an alert via CLI looks like `az monitor metrics alert create -n NameOfAlert ...`

Alert processing rules can be used to "override" an alert, they modify the alerts as they're being fired (e.g. suppress notification during maintenance period).