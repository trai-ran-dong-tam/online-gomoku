# Hosting options near Vietnam within budget

Research for [#6](https://github.com/trai-ran-dong-tam/online-gomoku/issues/6). Question: where can a real-time WebSocket game run near players in Vietnam for under about $25/month, and what does each option teach?

All prices and limits were checked on 2026-10-15 against the linked first-party pages, in USD unless noted, excluding tax. Anything marked **unverified** could not be confirmed from a first-party source in this pass. This file lays out options and trade-offs; the choice belongs to the grilling ticket "Choose where v1 runs".

## Answer

- **Singapore is the default region.** From Hanoi and Ho Chi Minh City it measures about 40 to 55 ms round trip, against about 50 to 65 ms for Hong Kong and 60 to 100 ms for Tokyo. Nearly every provider surveyed has a Singapore region.
- **Under $25/month buys one of three shapes:** a small self-managed cluster (for example three 1 GB VPS at $5 to $7 each, running k3s or similar), one or two nodes on a managed Kubernetes service with a free control plane (DOKS, LKE, VKE, AKS free tier), or a PaaS or edge platform ($0 to $17). Oracle Cloud's Always Free tier (2 Arm OCPUs and 12 GB RAM) is the only zero-cost way to run a multi-node cluster, with reclamation and capacity caveats.
- **Ruled out by cost at steady state:** EKS (about $73/month for the control plane alone), HA control planes (DOKS $40, LKE from $60), Fly.io Managed Postgres ($38), and now Hetzner Singapore, whose smallest CPX plan went to $17.99/month plus IPv4 on 15 June 2026. These suit short-lived drills, not steady state.
- **Free tiers cover the rest in Singapore:** Postgres (Neon, Supabase), Redis-compatible stores (Upstash, region unverified), and observability (Grafana Cloud: 10k metric series, 50 GB of logs and of traces, 500 k6 VU-hours; Sentry: 5k errors).
- **Biggest risk is the network path, not the host.** On 25 August 2026, faults on four international subsea cables cut about 30% of Vietnam's international capacity, with slowdowns for online games at peak. Hosting in Vietnam avoids this, but this pass did not price Vietnamese clouds beyond Bizfly's Kubernetes fee.

## Latency from Vietnam

Two sources, both measured on 2026-10-15. Both use servers in data centers, not home or mobile connections, so real players will see more latency than this.

[WonderNetwork](https://wondernetwork.com/pings/Hanoi) pings between its own servers (averages, in ms):

| From | Singapore | Hong Kong | Taipei | Kuala Lumpur | Bangkok | Tokyo |
|---|---|---|---|---|---|---|
| [Hanoi](https://wondernetwork.com/pings/Hanoi) | 44.8 | 65.2 | 39.0 | 51.2 | 64.7 | 88.9 |
| [Ho Chi Minh City](https://wondernetwork.com/pings/Ho%20Chi%20Minh%20City) | 46.1 | 52.0 | 49.7 | 50.7 | 77.4 | 98.0 |

[Globalping](https://globalping.io) probes in Vietnam, 8 pings each, run for this research at 2026-10-15 11:26 UTC (averages, in ms; probes sit on Vietnamese hosting and ISP networks):

| Target | Hanoi probes | Ho Chi Minh City probes |
|---|---|---|
| Linode Singapore (`speedtest.singapore.linode.com`) | 38.2 (Viet Storage), 49.4 (VNPT), 52.2 (365 Online) | 50.0 (VNG), 52.7 (Verichains) |
| Vultr Singapore (`sgp-ping.vultr.com`) | 40.0 (Viet Storage), 51.0 (365 Online), **211.1 (VNPT)** | 43.2 (Saigon Postel), 53.8 (VNG), 56.2 (Verichains) |
| Linode Tokyo (`speedtest.tokyo2.linode.com`) | 61.2 (365 Online), 88.4 (CMC), 101.5 (VNPT) | 61.2 (Saigon Postel), 63.3 (Viet Digital), 69.8 (VNG) |

Takeaways:

- Singapore sits at about 40 to 55 ms on most paths. Hong Kong is close from Ho Chi Minh City but slower from Hanoi. Tokyo varies most by ISP.
- Routing depends on the ISP and the provider: VNPT reached Vultr Singapore at 211 ms but Linode Singapore at 49 ms. The quality-target and hosting tickets should measure candidate hosts from Vietnamese ISPs (Viettel, VNPT, FPT) before committing.
- The Hong Kong targets tried on Globalping (Vultr, SoftLayer and AWS hostnames) did not resolve or did not answer ping, so Hong Kong figures come only from WonderNetwork.

## Network reliability: subsea cables

- [Light Reading, 31 August 2026](https://www.lightreading.com/cable-technology/four-subsea-cable-faults-hit-vietnam-s-internet): parts of SJC2 and AAE-1 went down on 25 August 2026, and AAG and TGN Intra Asia also had problems. About 30% of Vietnam's international traffic was affected, with slowdowns expected for overseas social media, streaming and online games at peak times. Services hosted inside Vietnam were not expected to be affected.
- The same article: in 2024, full or partial outages hit APG, AAE-1 and IA. Viettel rerouted some traffic over a terrestrial route through Laos to Singapore and added capacity on IA and APG.
- Repair status as of 2026-10-15: **unverified**. No first-party update was found.
- What this means for the plan: a Singapore host is exposed to these faults and a Vietnamese host mostly is not. Players elsewhere in Southeast Asia reach Singapore without crossing Vietnam's cables. A cable fault makes a natural failure-drill scenario (added latency, packet loss) for a later testing ticket.

## Options

### VPS providers with a Singapore region

| Provider | Cheapest plans | Singapore | Notes |
|---|---|---|---|
| DigitalOcean | [$4: 512 MiB, 10 GiB, 500 GiB transfer; $6: 1 GiB, 25 GiB, 1,000 GiB; $12: 2 GiB, 2,000 GiB](https://www.digitalocean.com/pricing/droplets) | [SGP1](https://docs.digitalocean.com/platform/regional-availability/) | The pricing page shows no regional price differences. |
| Akamai (Linode) | [Nanode 1 GB $5 (25 GB, 1 TB); Linode 2 GB $12; 4 GB $24](https://www.akamai.com/cloud/pricing/cpu) | [Singapore and Singapore Expansion (sg-sin-2)](https://www.akamai.com/why-akamai/global-infrastructure/availability) | "Pricing for some plans is different depending on the region" ([pricing](https://www.akamai.com/cloud/pricing/cpu)); Singapore prices **unverified**. New customers cannot create resources in Jakarta ([availability](https://www.akamai.com/why-akamai/global-infrastructure/availability)). |
| AWS Lightsail | [$5: 0.5 GB, 1 TB; $7: 1 GB, 2 TB; $12: 2 GB, 3 TB (with IPv4). IPv6-only bundles are $3.50, $5, $10](https://aws.amazon.com/lightsail/pricing/) | [Listed](https://aws.amazon.com/lightsail/pricing/) | Singapore is not among the regions with halved transfer allowances (Mumbai, Sydney, Jakarta, Malaysia, Hong Kong and São Paulo are) ([pricing](https://aws.amazon.com/lightsail/pricing/)). |
| Vultr | $5: 1 vCPU, 1 GB, 25 GB, 1 TB (from search results of [vultr.com/pricing](https://www.vultr.com/pricing/); the page blocked direct fetch) | Listed | Vultr says some locations price differently ([pricing](https://www.vultr.com/pricing/)); Singapore price **unverified**. |
| Hetzner | CPX12 (1 vCPU, 2 GB) €15.49 / $17.99; CPX22 €26.49 / $30.99; CPX32 €48.99 / $57.99 in Singapore, excluding IPv4, for new orders from 15 June 2026 (previously €7.99, €15.99, €32.49) ([price adjustment](https://docs.hetzner.com/general/infrastructure-and-availability/price-adjustment/)) | [SIN1](https://www.hetzner.com/cloud/regular-performance) | Only 0.5 TB of traffic included on the small plans in Singapore ([plans](https://www.hetzner.com/cloud/regular-performance)). No managed Kubernetes ([cloud](https://www.hetzner.com/cloud)). On 2026-10-15 the fetched plan page text showed "This product is currently unavailable" on its order buttons (possibly a placeholder before scripts load; **unverified**). |

Teaches: Linux administration, IaC (Terraform or OpenTofu providers exist for all of these; **unverified** per provider here), self-managed Kubernetes (k3s or kubeadm across two or three VPS), and HA done by hand (two app nodes behind a managed load balancer or a floating IP).

### Always-free cloud: Oracle Cloud Infrastructure

From [OCI's Always Free resources page](https://docs.oracle.com/en-us/iaas/Content/FreeTier/freetier_topic-Always_Free_Resources.htm):

- Ampere A1 (Arm): 1,500 OCPU-hours and 9,000 GB-hours a month, "equivalent to 2 OCPUs and 12 GB of memory" for Always Free tenancies. Two AMD micro VMs (1/8 OCPU, 1 GB each) on top.
- 200 GB block volume, 10 TB/month outbound data, and one Flexible Load Balancer at 10 Mbps.
- Always Free compute must be in the home region, which is fixed at sign-up.
- Idle instances may be reclaimed: an instance counts as idle when, over 7 days, 95th-percentile CPU, network, and (for A1) memory are all under 20%.
- Whether OCI has a Singapore region open to Always Free sign-ups, how often A1 capacity errors happen there, and whether an OKE basic cluster has no control-plane fee: all **unverified**.

Teaches: Kubernetes on Arm nodes, Terraform (OCI has a provider; **unverified** here), a real cloud load balancer. A game with steady players should not trip the idle rule, but a quiet staging cluster might.

### Managed Kubernetes

| Service | Control plane | Nodes and extras | Fit for $25 |
|---|---|---|---|
| DigitalOcean DOKS | [Free; HA control plane $40/month](https://www.digitalocean.com/pricing/kubernetes) | [Nodes from $12/month; load balancer $12/month; DO recommends at least two nodes](https://www.digitalocean.com/pricing/kubernetes) | One node plus a load balancer is $24. Two nodes plus a load balancer is $36. |
| Akamai LKE | [Standard control plane free; HA from $60/cluster/month, varies by region; LKE Enterprise $300](https://www.akamai.com/cloud/pricing/kubernetes) | [Page example: three Shared 2 GB nodes for $36](https://www.akamai.com/cloud/pricing/kubernetes); NodeBalancers billed separately | Two 2 GB nodes at $12 is $24. Whether 1 GB Nanodes can be LKE nodes: **unverified**. |
| Vultr VKE | [Free](https://www.vultr.com/kubernetes/) | Worker nodes, load balancers, storage billed at normal rates | Depends on node price in Singapore (**unverified**). HA control plane pricing **unverified**. |
| Google GKE | [$0.10/cluster/hour, but a $74.40 monthly credit per billing account covers one zonal or Autopilot cluster](https://cloud.google.com/kubernetes-engine/pricing) | Autopilot bills per second for the CPU, memory and storage pods request, and raises requests below its minimums ([pricing](https://cloud.google.com/kubernetes-engine/pricing)). Singapore is `asia-southeast1`. | Control plane fits. Autopilot compute cost in Singapore for this workload: **unverified**. |
| AWS EKS | [$0.10/cluster/hour standard support ($73 for a 730-hour month); $0.60 in extended support](https://aws.amazon.com/eks/pricing/) | EC2 nodes extra | Over budget at steady state. A 4-hour drill costs about $0.40 in control-plane fees. |
| Azure AKS | [Free tier: free control plane, no SLA, "not meant to run at scale (>10 nodes)"](https://azure.microsoft.com/en-us/pricing/details/kubernetes-service/) | VM nodes extra; Standard tier price not readable on the page | Fits if node VMs fit (**unverified**). The "Southeast Asia" region is listed. |
| Bizfly (Vietnam) | [Standard-0 free; Standard-1 1,260,000 VND/cluster/month excl. VAT](https://bizflycloud.vn/kubernetes-engine/bang-gia) | Nodes, volumes and load balancers billed as Bizfly Cloud Server; node prices **unverified** | Unknown. |

Teaches: Kubernetes operations without running the control plane, cloud load balancers, autoscaling, Terraform for a managed cluster. A free control plane without HA still teaches workload HA (multiple replicas, PodDisruptionBudgets, draining nodes); control-plane HA is a drill-only lesson at these prices.

### PaaS

| Platform | Singapore | Price | WebSockets and caveats |
|---|---|---|---|
| Fly.io | [`sin` and `nrt` are the only Asian regions listed](https://docs.fly.io/reference/regions) | [shared-cpu-1x 1 GB is $8.50/month in `sin` (page example); no free tier for new orgs, only a trial; Asia-Pacific egress $0.04/GB; dedicated IPv4 $2/month; Managed Postgres from $38/month](https://docs.fly.io/about/pricing) | WebSocket support not stated on the pricing page (**unverified** here). |
| Render | [Singapore listed](https://render.com/docs/regions) | [Hobby workspace $0 + compute](https://render.com/pricing); [Starter (0.5 CPU, 512 MB) $7/month](https://render.com/docs/compute-plans) | [WebSockets supported with no maximum duration; deploys and maintenance send SIGTERM with a 30 s window, extendable to 300 s](https://render.com/docs/websocket). [Free web services spin down after 15 minutes idle; free Postgres expires after 30 days](https://render.com/docs/free). |
| Railway | [Southeast Asia Metal (Singapore)](https://docs.railway.com/reference/deployment-regions) | [Hobby $5/month including $5 of usage; $20 per vCPU-month, $10 per GB RAM-month, egress $0.05/GB](https://docs.railway.com/reference/pricing/plans) | WebSocket behavior **unverified** here. |

Teaches: CI/CD and zero-downtime deploys (draining live WebSocket games on SIGTERM is a real lesson), but little Kubernetes or IaC. HA means paying for a second instance.

### Edge: Cloudflare Workers with Durable Objects

From [Durable Objects pricing](https://developers.cloudflare.com/durable-objects/platform/pricing/) and [Workers pricing](https://developers.cloudflare.com/workers/platform/pricing/):

- Durable Objects run on the Workers Free plan (SQLite storage only): 100,000 requests/day and 13,000 GB-s/day. Over the limit, further operations fail.
- Workers Paid is $5/month minimum: 1M Durable Object requests and 400,000 GB-s a month, then $0.15 per million requests and $12.50 per million GB-s. No egress charges.
- WebSocket billing: opening a connection is one request; incoming messages count at 20:1; outgoing messages are free. With the WebSocket Hibernation API, idle objects are not billed for duration. With plain `accept()`, duration is billed the whole time a connection is open.
- [Location](https://developers.cloudflare.com/durable-objects/reference/data-location/): an object is created near its first request, or by a best-effort hint (`apac`, `apac-se`, `apac-ne`). Durable Objects run in a subset of Cloudflare's data centers. Which site serves Vietnamese players: **unverified**.

Teaches: a stateful-edge design where one Durable Object owns one game room, which fits turn-based Caro well. Teaches no Kubernetes or node-level HA, and locks the game server to Cloudflare's runtime.

### Free tiers for data stores

| Service | Free tier | Singapore |
|---|---|---|
| Neon Postgres | [1 GB storage per project, 100 CU-hours per project per month, scales to zero after 5 minutes idle, 5 GB egress](https://neon.com/docs/introduction/plans) | [`aws-ap-southeast-1`](https://neon.com/docs/introduction/regions) |
| Supabase Postgres | [2 active projects, 500 MB database, 5 GB egress, pauses after 7 days without activity](https://supabase.com/pricing) | [`ap-southeast-1`](https://supabase.com/docs/guides/platform/regions) |
| Upstash Redis | [500K commands/month, 250 MB data, 10 GB bandwidth; pay-as-you-go $0.20 per 100K commands; fixed plans from $10/month](https://upstash.com/pricing/redis) | **Unverified** |
| Render Key Value | [One free instance, in-memory only, data lost on restart](https://render.com/docs/free) | [Singapore region exists](https://render.com/docs/regions) |

### Observability free tiers

- [Grafana Cloud Free](https://grafana.com/pricing/): no time limit; 10k active metric series, 50 GB each of logs, traces and profiles, 14-day retention, 3 users, 500 k6 virtual-user-hours, 100k synthetic API checks, Kubernetes Monitoring host and container hours included.
- [Sentry Developer](https://sentry.io/pricing/): 1 user, 5k errors, 5M spans, 50 replays, 30-day retention. Team plan $26/month.

### Student programs

- [GitHub Student Developer Pack](https://education.github.com/pack) (as listed on 2026-10-15): $100 Azure credit, Heroku $13/month for 24 months, Datadog Pro for 10 servers for 2 years, New Relic, a Sentry student plan (50K errors, 1 year, renewable), Doppler, a Namecheap .me domain. No DigitalOcean offer appeared on the page.
- [Azure for Students](https://azure.microsoft.com/en-us/free/students): $100 credit for 12 months, no credit card, full-time university students only, renewable yearly while a student.
- Neither is a steady-state plan: both run out or end with student status.

### Vietnamese clouds

- Hosting inside Vietnam avoids the international-cable risk for players in Vietnam ([Light Reading](https://www.lightreading.com/cable-technology/four-subsea-cable-faults-hit-vietnam-s-internet)) but adds a cable crossing for players elsewhere in Southeast Asia.
- Bizfly offers managed Kubernetes with a free Standard-0 cluster tier ([pricing](https://bizflycloud.vn/kubernetes-engine/bang-gia)). Its VPS prices, and offers from Viettel, FPT and VNG Cloud, were not checked in this pass: **unverified**.
- Data-residency obligations under Vietnamese law are for the legal research ticket; not assessed here.

## Comparison

Monthly cost is steady state, at list prices, for a minimal setup.

| Option | Monthly cost | Kubernetes | IaC | HA | WebSocket fit |
|---|---|---|---|---|---|
| Single VPS in Singapore (DO, Linode, Lightsail, Vultr) | $5 to $12 | No (or single-node k3s) | Yes | No | Good: long-lived connections, full control |
| 3 small VPS, self-managed k3s (for example 3 x Lightsail $7, 3 x Nanode $5) | $15 to $21 + load balancer if wanted | Yes, including the control plane | Yes | Yes, by hand | Good; 1 GB nodes are tight (**unverified** fit) |
| Oracle Always Free, A1 Arm | $0 | Yes (self-managed; OKE fee **unverified**) | Yes | Partial (2 instances, 10 Mbps LB) | Good; idle reclamation and capacity risk |
| DOKS, 1 node + LB | $24 | Yes, managed | Yes | No (one node) | Good |
| DOKS or LKE, 2 nodes | $24 (LKE, no LB) to $36 (DOKS + LB) | Yes, managed | Yes | Workload HA | Good |
| GKE Autopilot | Control plane covered by credit; compute **unverified** | Yes, managed | Yes | Workload HA | Good |
| EKS | $73 control plane + nodes | Yes | Yes | Yes | Drills only |
| Hetzner Singapore | $17.99 + IPv4 per node | Self-managed only | Yes | 2 nodes over budget | Good, but poor value since June 2026 |
| Fly.io, 1 to 2 machines in `sin` | $8.50 to $17 + egress | No | Partial (config file, CLI) | 2 machines | Likely good (**unverified**) |
| Render Starter, 1 to 2 instances | $7 to $14 | No | Partial (Blueprints, **unverified**) | 2 instances | Good, documented; deploys drop connections |
| Railway Hobby | $5 + usage over $5 | No | Partial | Extra replicas cost more | **Unverified** |
| Cloudflare Workers + Durable Objects | $0 (free) or $5 | No | Partial (Wrangler config) | Platform-managed | Good, with Hibernation API; location near Vietnam **unverified** |

Free add-ons usable with any row: Neon or Supabase Postgres in Singapore, Grafana Cloud and Sentry free tiers. Short-lived load-test and failure-drill environments can use EKS, HA control planes, or bigger nodes billed by the hour.
