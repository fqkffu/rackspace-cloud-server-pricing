# Rackspace Server: How to Launch One, Resize Rules Worth Reading First, and What Spot Bidding Really Costs

Rackspace uses the word "server" for three products that share nothing but a logo. A **Cloud Server** is a virtual machine you build in the Cloud Control Panel and pay for by uptime hour. **Spot** is auctioned Kubernetes capacity with a bid floor of $0.01/hr and on-demand rates from $0.019/hr. **Managed bare metal** is quote-only, dedicated hardware in one of 40+ data centers.

The single most useful fact before you click anything: on Rackspace Cloud Servers, **most flavors resize up only**. The exceptions are narrow, and one of them can destroy data. Get that wrong and you are rebuilding a server instead of upgrading it.

If what you actually want is one physical machine at a published monthly rate, that is a different purchase. Spin Servers lists a Dallas dedicated server at **$79/mo** with 32 GB RAM, a 1 TB NVMe drive and 30 TB of transfer on a 10 Gbps port, provisioned automatically.

> **Quick pick:** Building a VM you administer yourself? Read the Cloud Server steps and resize rules below before you order. Running Kubernetes and able to survive node loss? Spot's on-demand rates start at $0.019/hr and the bid floor is $0.01/hr. Want single-tenant hardware with a fixed monthly bill and full root? 👉 [Check Spin Servers' dedicated server plans and live stock](https://bit.ly/Spinservers)

## Dedicated plans with published monthly rates

Spin Servers sells bare metal in Dallas, Texas and San Jose, California. Every listing below is month-to-month with no setup fee, includes a dedicated IPv4 address and full root access, and auto-provisions rather than waiting on a human.

| Plan | CPU / RAM | Storage | Bandwidth | Monthly |
| --- | --- | --- | --- | --- |
| Intel Xeon E3-1280 v5 (Dallas) | 4C/8T @ 3.7 GHz turbo, 32 GB DDR4 ECC | 1 TB M.2 NVMe | 30 TB on 10 Gbps | $79 |
| Starter Package | 32 GB RAM | 1 TB NVMe | 10 TB on 10 Gbps | $99 |
| Growth Package | 256 GB RAM | 2 × 1.6 TB SSD | 20 TB on 10 Gbps | $149 |
| High Performance Package | 768 GB RAM | 8 × 2 TB SSD | 40 TB on 10 Gbps | $679 |

Monthly rates as published on the vendor's dedicated server pages in September 2026; Dallas and San Jose stock and pricing differ, so confirm the final figure in the configurator. Order links: [E3-1280 v5](https://bit.ly/Spinservers) · [Starter](https://bit.ly/Spinservers) · [Growth](https://bit.ly/Spinservers) · [High Performance](https://bit.ly/Spinservers).

The San Jose catalogue runs wider than the four Dallas packages: 12 cores and 64 GB DDR3, 16 cores with 64 GB DDR4 and a 1.6 TB SSD or NVMe, 24 cores with 48 GB, 16 cores with 256 GB RAM and a hardware RAID controller with cache, up to 32 cores with 512 GB RAM and 2 × 3.84 TB SSDs on battery-backed RAID. All of them list 2 × 10 Gbps ports, 30 TB monthly transfer and instant provisioning. 👉 [See the current San Jose configurations](https://bit.ly/Spinservers)

## What a Rackspace Cloud Server costs, and what Spot costs

These are not the same product and the numbers do not transfer between them. Rackspace's UK Cloud Server pricing page quotes service-level minimums rather than a full flavor rate card: **£35/mo minimum** on Managed Infrastructure, **£350/mo minimum** on Managed Operations: SysOps, and a **£1,750/mo flat fee** on top for DevOps Automation, which then qualifies the account for volume discounts on server usage. Those are floors on the managed layer, before any compute.

Spot publishes actual hourly numbers because it is auctioned. A sample of General Purpose rates:

| Server class | vCPU / RAM | On-Demand $/hr | Spot $/hr | Region |
| --- | --- | --- | --- | --- |
| gp.vs1.medium | 2 / 4 GB | $0.023 | $0.01 | Chicago |
| gp.vs1.2xlarge | 16 / 64 GB | $0.336 | $0.01 | Chicago |
| gp.vs1.2xlarge | 16 / 64 GB | $0.304 | $0.01 | N. Virginia (Cost Optimized) |
| gp.vs2.2xlarge | 16 / 64 GB | $0.350 | $0.15 | N. Virginia (Performance Optimized) |
| gp.vs2.2xlarge | 16 / 64 GB | $0.350 | $0.15 | San Jose |
| gp.vs1.2xlarge | 16 / 64 GB | $0.289 | $0.01 | London |
| gp.vs1.2xlarge | 16 / 64 GB | $0.339 | $0.01 | Hong Kong |
| gp.vs1.2xlarge | 16 / 64 GB | $0.319 | $0.01 | Sydney |
| gpu.vs2.xlargeplusplus | 24 / 64 GB | $7.25 | $0.15 | San Jose |

Compute Heavy sits between $0.03 and $0.292/hr on demand; Memory Heavy runs from $0.051/hr for 2 vCPU / 16 GB in London to $0.494/hr for 16 vCPU / 128 GB in Hong Kong. Two things worth noticing before you pick a class. Memory Heavy in London has a Spot rate of **$0.036/hr** for 8 vCPU / 64 GB, well above the $0.01 floor, so the cheap regions are not uniformly cheap. And `ch.vs1.medium` (2 vCPU / 4 GB, $0.037) costs more than `gp.vs1.medium` (2 vCPU / 4 GB, $0.023) in the same Chicago region. Compute Heavy is not an upgrade, it is a different ratio; choose by what your workload saturates.

Add-ons are flat and predictable: SATA persistent volumes at $0.02 per GB-month, SSD at $0.06, load balancers at $10/mo, high-availability control planes at $40/mo, control plane itself free. Billing is per second.

## Launching a Cloud Server: what the form actually asks

The path is **Select a Product > Rackspace Cloud**, then **Servers > Cloud Servers**, then **Create Server**. From the dashboard, the **Server** tile in the Quick Build block jumps straight to the same page.

You supply a server name, a region (the data center the machine lands in), an image and a flavor. The flavor class determines the CPU-to-RAM ratio and therefore what you can resize to later.

One requirement catches people out: **Ubuntu 22.04 and Ubuntu 24.04 will not provision without an SSH key**. Every other image lets you choose. You add the key through **Add Public Key**, name it, paste the public half, and confirm it appears in that server's SSH key list before you hit create. If you have no key pair yet, generate one first; a server you cannot log into is not cheaper than one you can.

Networking needs **PublicNet** and **ServiceNet** selected. The **Recommended Installs** section offers automatic configuration of common stacks at build time, which saves the first twenty minutes of setup. Provisioning finishes with the status **Running** and connection instructions in the right-hand sidebar.

## The resize rules that decide whether you can move at all

A resize changes vCPU and RAM, and may change disk size. It happens in the Cloud Control Panel, and four conditions govern whether it is allowed:

1. Standard flavor Linux servers running the deprecated **paravirtual (PV)** virtualization mode can resize **down**, and may lose data doing it.
2. **OnMetal** servers cannot be resized in either direction.
3. **All other flavors resize up only.** There is no downgrade path if you overbuy.
4. **I/O flavors cannot be resized in the panel at all.** Going up requires contacting Rackspace Support.

Rule 3 is the one with money attached. Sizing too generously on a non-PV flavor is not reversible without a rebuild, so start smaller than your peak estimate and grow into it.

Cost changes the moment the resize finishes. Each server size has its own hourly uptime rate, and the new rate starts when the process completes, which means **one billing cycle can contain two different rates for the same server**. A mid-month resize produces a split bill, not a prorated single line.

The steps: gear icon next to the server, **Resize**, pick the new size in the dialog, **Resize Server**, then verify. Verification is the part to slow down on. It is the **last point at which you can revert**, and the site staying up proves nothing, because individual processes may have stopped during the operation. SSH into either the public or private IP and run three commands:

- `nproc` for the processor count
- `df -h` for disk usage
- `free -m` for available RAM

Two traps sit inside this step. Servers booted from a **Cloud Block Storage volume gain no disk space** after a resize, regardless of the flavor you pick. **Windows servers** need extra steps before the added space is usable at all.

If everything checks out, click **Confirm**. Completion means the status reads `Active`, Current Action is `None`, and the server has restarted. Manually started web services may need a restart of their own. If you do nothing, **the system auto-confirms the resize after 24 hours**, so an unattended verification window closes on its own whether or not you tested anything.

## Where Spot's low rates come from

Spot sells spare capacity at auction. You set a maximum bid; while the market price stays at or below it, your node runs and you pay the market rate, not your bid. The $0.01/hr figure is the floor you can bid, not a guaranteed price for a running node, and the product is delivered as managed Kubernetes clusters rather than raw VMs.

Nodes are reclaimed when a higher bid displaces yours. Rackspace's own guidance is to design for interruption with checkpoints, replicas and graceful shutdown. Batch jobs, CI runners, stateless API workers and anything with a queue in front of it fit. A single database that cannot be restarted on demand does not.

Egress follows a formula rather than a fixed cap: each cluster gets **(average GB of RAM) × 100 GB per month** free, then $0.01/GB. A 64 GB node therefore includes 6,400 GB of outbound traffic. Ingress and cross-region traffic are free, which makes multi-region replication cheaper than the headline number suggests.

## Managed bare metal: dedicated hardware, no public price

Rackspace's dedicated server line is managed hosting on bare metal, 100% single-tenant, with a choice of CPU, memory, OS, storage, virtualization, RAID, backup, monitoring and database software. Hardware comes from Dell, HPE, NetApp, Pure Storage, Cisco, Palo Alto, F5, Microsoft and VMware.

The reach is the selling point: 40+ data centers with interconnection between them and direct links into AWS, GCP and Azure, extended through Equinix and Megaport. Data Freedom keeps data in Rackspace facilities while making it available to VMware Cloud, AWS, Azure and GCP, which is aimed squarely at egress fees and lock-in.

No prices are published for this line. Budget a quote cycle and expect the managed layer to dominate the bill. If you want dedicated hardware without a management contract attached, the arithmetic is simpler elsewhere: a fully dedicated Dallas machine with 32 GB RAM and 1 TB NVMe starts at $79/mo. 👉 [Compare Spin Servers' bare metal against a managed quote](https://bit.ly/Spinservers)

## Which one fits

**A single app server you administer** → Rackspace Cloud Servers, sized conservatively because you cannot resize down. Budget the managed service-level minimum if you are on the UK pricing structure.

**Kubernetes workloads that tolerate node loss** → Spot, bidding near the floor in Chicago, N. Virginia, London, Hong Kong or Sydney, with checkpointing in place.

**Dedicated hardware, fixed monthly cost, full root** → Spin Servers, where $79/mo buys a whole Xeon E3-1280 v5 with 32 GB RAM, 1 TB NVMe and 30 TB of transfer, or $149/mo buys 256 GB RAM and 2 × 1.6 TB SSD.

**Compliance, colocation or hybrid connectivity into AWS/Azure/GCP** → Rackspace managed bare metal, on a quote.

Two checks before you order anything: confirm the region actually has stock for the exact configuration (Spot rates and dedicated inventory both vary by location), and decide whether you want a management layer at all, because that is the line item that turns a $79 server into a four-figure monthly contract. 👉 [Start with Spin Servers' $79/mo Dallas dedicated server](https://bit.ly/Spinservers)
