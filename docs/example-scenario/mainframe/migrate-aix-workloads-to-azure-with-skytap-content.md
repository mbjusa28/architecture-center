Skytap on Azure provides a path for migrating and running traditional applications that depend on IBM Power Systems in Azure without requiring an immediate rewrite or replatforming effort. This example shows how to migrate AIX logical partitions (LPARs) to Skytap on Azure while integrating the migrated workloads with Azure networking, storage, and application services.

Skytap on Azure supports IBM Power-based workloads, including AIX, IBM i, and Linux on Power, alongside x86 workloads. Depending on the Skytap region and workload requirements, IBM Power9 or Power10 infrastructure can be used to host these workloads. This approach lets organizations preserve existing IBM Power applications while incrementally adopting Azure-native services.

A web application hosted in Azure provides a modern interface for services running on the AIX LPARs in Skytap on Azure.

## Architecture

:::image type="content" border="false" source="media/migrate-aix-workloads-to-azure-with-skytap.svg" alt-text="Architecture showing Azure services and Skytap on Azure used to migrate and run AIX workloads on IBM Power Systems." lightbox="media/migrate-aix-workloads-to-azure-with-skytap.svg" :::

*Download a [Visio file](https://arch-center.azureedge.net/migrate-aix-workloads-to-azure-with-skytap.vsdx) of this architecture.*
 
### Workflow

The numbers in the diagram correspond to the following data flow.

1.  An on-premises user connects to resources in Azure over [Azure ExpressRoute](/azure/expressroute/expressroute-introduction). ExpressRoute provides private connectivity between the organization's network and Microsoft cloud services. A web application hosted in Azure can provide a modern interface to business services that continue to run on AIX LPARs in Skytap on Azure.

1.  Azure Data Box Gateway is deployed in the on-premises environment near the existing AIX infrastructure, which can include an AIX Network Installation Management (NIM) server. The organization creates AIX system and data backups by using native AIX utilities such as mksysb and savevg.

1.  Backup data is transferred to Azure Storage. A private endpoint can be used so that traffic to Azure Blob Storage remains on private network paths instead of traversing the public internet.

1.  In the Skytap on Azure environment, an AIX NIM server can be used to install or restore the base AIX operating system onto the destination LPARs running on IBM Power infrastructure

1.  After the operating system is restored, the AIX LPAR is restarted and application data and volume groups are restored. The process is repeated for each workload included in the migration.

   The exact migration method depends on the source AIX environment, operating-system level, network configuration, storage layout, application dependencies, recovery requirements, and target IBM Power configuration.

### Components

- [Azure Data Box Gateway](/azure/databox-gateway/data-box-gateway-overview)  is a virtual storage appliance that can transfer data between an on-premises environment and Azure Storage. In this architecture, it provides one option for staging AIX backup data before restoring workloads in Skytap on Azure.

- [Azure Virtual Machines](/azure/well-architected/service-guides/virtual-machines) provides scalable compute resources in Azure. In this architecture, an Azure VM can host a web application or other application-tier services that communicate with workloads running in Skytap on Azure.

- [Azure Virtual Network](/azure/well-architected/service-guides/virtual-network) provides network isolation and connectivity for Azure resources. It can be used with ExpressRoute and other networking services to connect Azure-native application components with on-premises systems and workloads hosted in Skytap on Azure.

- [Blob Storage](/azure/well-architected/service-guides/azure-blob-storage) provides massively scalable object storage. In this architecture, Blob Storage can be used to stage AIX backup data during migration.

- [ExpressRoute](/azure/well-architected/service-guides/azure-expressroute) extends an organization's on-premises network to Microsoft cloud services over a private connection. ExpressRoute can provide private connectivity among on-premises infrastructure, Azure resources, and the Skytap on Azure environment.

  For more information about how ExpressRoute works with Skytap, see [Skytap Get started with Azure networking guide](https://www.skytap.com/blog/skytap-on-azure-networking-considerations/).

- [Private Link](/azure/private-link/private-link-overview) provides private connectivity to supported Azure services through private endpoints. In this architecture, a private endpoint can be used to access Azure Storage without sending storage traffic over the public internet.

- [Kyndryl Cloud Uplift (formerly Skytap on Azure)](https://marketplace.microsoft.com/search/products?search=Kyndryl%20Cloud%20Uplift) provides an environment for running traditional IBM Power and x86 workloads in Azure datacenters. Applications running on AIX, IBM i, or Linux on Power can be migrated while retaining their existing operating-system and 10 infrastructure.

  For more information about how Skytap on [Azure connectivity](https://www.skytap.com/blog/skytap-on-azure-networking-considerations/) works, see [Skytap Well-Architected Framework](https://skytap.github.io/well-architected-framework/).

### Alternatives

-   For access to the AIX instances running in Skytap on Azure, you can connect over a virtual private network (VPN) or the internet. For example, you can use SSH (Secure Shell) to access your AIX systems on Azure.

-   To maximize security and minimize the number of open ports, you can use VMs as bastion hosts for administrative access to the LPARs. The bastion host runs within the VNet on Azure.

-   To simplify user access, you can build modern front ends and apps on Azure for the AIX instances running in Skytap on Azure while continuing to run critical components or systems of record (SOR) on AIX.

## Scenario details

Many organizations continue to depend on applications built for IBM Power Systems because those applications contain business logic, operational processes, and data accumulated over many years. Rewriting these applications before moving to the cloud can introduce significant cost, risk, and project duration.

Skytap on Azure provides an alternative migration path. Organizations can move supported AIX workloads to IBM Power infrastructure hosted in Azure datacenters while preserving the existing operating-system and application architecture. They can then modernize incrementally by integrating those workloads with Azure services.

For example, an application can continue to run its existing business logic on AIX while new web, API, analytics, integration, automation, or AI capabilities are developed using Azure-native services.

This approach separates infrastructure migration from application modernization. Organizations can first reduce their dependency on on-premises Power Systems infrastructure and then modernize individual application components according to business priorities.

### Potential use cases

-   Start an easy, self-service lift-and-shift of AIX workloads to Skytap on Azure.

-   Improve business continuity with cost-effective Azure solutions for backup and disaster recovery.

-   Add scale by rapidly deploying AIX LPARs on demand.

-   Accelerate DevOps and increase your test coverage using on-demand resources.

-   Create virtual labs using Skytap on Azure templates and environments so that you can easily demo AIX applications to customers and users.

## Considerations

These considerations implement the pillars of the Azure Well-Architected Framework, which is a set of guiding tenets that can be used to improve the quality of a workload. For more information, see [Microsoft Azure Well-Architected Framework](/azure/well-architected/).

### Reliability

Reliability ensures that a workload can meet the availability and recovery commitments made to its users. For more information, see the [Design review checklist for Reliability](/azure/well-architected/reliability/checklist).

Design availability and disaster recovery around the business requirements of the complete application, including the AIX LPARs, Azure-native application components, network connectivity, storage, and external dependencies.

For production AIX workloads, consider:

-   Application recovery objectives, including recovery time objective (RTO) and recovery point objective (RPO).
-   Redundant application components where the application architecture supports them.
-   Backup and recovery procedures for AIX system and application data.
-   Connectivity dependencies between Azure, Skytap on Azure, and on-premises systems.
-   Capacity availability in a recovery region.
-   Regular recovery testing rather than relying only on successful backup completion.

Skytap on Azure is supported by a service-level agreement (SLA) of 99.95% availability.

### Security

Security provides assurances against deliberate attacks and the abuse of your valuable data and systems. For more information, see [Design review checklist for Security](/azure/well-architected/security/checklist).

Skytap on Azure meets industry cloud security requirements, including System and Organization Controls for Service Organizations 2 (SOC 2) and SOC 3 attestations and compliance with ISO 27001 and PCI DSS 3.2.

To learn more about how Skytap secures your workloads, you can get more information in the [Skytap Well-Architected Framework Security Pillar](https://skytap.github.io/well-architected-framework/security/).

### Cost Optimization

Cost Optimization is about looking at ways to reduce unnecessary expenses and improve operational efficiencies. For more information, see [Design review checklist for Cost Optimization](/azure/well-architected/cost-optimization/checklist).

Running your AIX-based workloads in Skytap on Azure helps optimize costs compared to on-premises deployments. The consumption-based usage plans let you deploy AIX LPARs only as needed and scale them on demand to meet the needs of your workloads.

See more pricing information on the [Plans + Pricing](https://marketplace.microsoft.com/search/products?search=Kyndryl%20Cloud%20Uplift) tab in the Microsoft Marketplace.

### Performance Efficiency

Performance Efficiency is the ability of a workload to adapt efficiently to changes in demand. For more information, see the [Design review checklist for Performance Efficiency](/azure/well-architected/performance-efficiency/checklist).

Size AIX LPARs according to measured processor, memory, storage, and I/O requirements rather than reproducing the physical configuration of the source Power Systems server.

Skytap on Azure provides IBM Power capacity that can be allocated to LPARs according to workload requirements. Power9 and Power10 platforms can have different processor, memory, and capacity characteristics, so benchmark performance-sensitive applications on the intended target configuration.

For hybrid applications, also evaluate network latency and throughput between the AIX workload and Azure services. Application tiers that exchange large amounts of synchronous traffic should be placed and connected to minimize unnecessary network latency.

One of the advantages of an Azure–based solution is the ability to scale out. Scaling makes nearly limitless compute capacity available to an application. Azure supports multiple methods to scale out compute power, such as [virtual machine scale sets](/azure/virtual-machine-scale-sets/overview) and
[load balancing](/azure/load-balancer/load-balancer-overview) across a cluster. Other platform as a service (PaaS) options scale compute resources dynamically. In addition, applications on Azure can also use
[Kubernetes clusters](/azure/aks/core-aks-concepts) as compute services for specified resources.

To scale up on Azure, choose a [larger VM size](https://azure.microsoft.com/services/virtual-machines/) for your workload.

## Deploy this scenario

To get started running AIX applications on Azure, check out the [Kyndryl Cloud Uplift (formerly Skytap)](https://marketplace.microsoft.com/search/products?search=Kyndryl%20Cloud%20Uplift) template in the Microsoft Marketplace.
Learn more about the different Migration and Deployment options with the [Skytap Well-Architected Framework](https://skytap.github.io/well-architected-framework/).

## Next steps

- See the [Cloud Migration for Apps Running IBM Power](https://techcommunity.microsoft.com/t5/video-hub/skytap-on-azure-cloud-migration-for-apps-running-ibm-power/m-p/1693588) demo.

- Learn how to [accelerate your cloud strategy with Skytap on Azure](https://azure.microsoft.com/blog/accelerate-your-cloud-strategy-with-skytap-on-azure/).

- [Skytap Well-Architected Framework](https://skytap.github.io/well-architected-framework)

- [Skytap documentation](https://help.skytap.com/)

## Related resources

- [Mainframe file replication and sync on Azure](../../solution-ideas/articles/mainframe-azure-file-replication.yml)
- [Modernize mainframe & midrange data](/azure/architecture/example-scenario/mainframe/modernize-mainframe-data-to-azure)
