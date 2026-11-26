# NVMe VPS from SixtyNet: $200/Year Entry Tier, Real-NVMe Checks, and Which Plan Fits Your Workload (Full Price Table)

SixtyNet's cheapest listed NVMe VPS is the LAX-STARTER at $200 per year, down from a $240 list price: 2 cores, 2 GB RAM, 40 GB NVMe and 5 TB of traffic on a 2 Gbps port in Los Angeles. For a small database, a CI runner or a handful of containers, that tier is enough. The plans above it are priced so evenly that paying for capacity you don't use buys you nothing extra. What follows is the full plan list with the prices as shown, the two commands that prove whether the disk you get is actually NVMe, and how the annual numbers compare with the monthly prices most of this market advertises.

> **Quick pick:** LAX-STARTER, $200/year (from $240), 2 cores / 2 GB / 40 GB NVMe / 5 TB at 2 Gbps, DDoS protection included. Buy the next tier up only when your RAM or your dataset outgrows 2 GB and 40 GB; there is no bulk discount to chase.
> 👉 [See the LAX-STARTER plan and its current annual price](https://sixtynet.net/aff.php?aff=39)

## SixtyNet NVMe VPS plans and prices

All five plans below are annual, USD, and hosted in Los Angeles. Prices in the last column are the discounted annual rate the site displays next to a struck-through list price.

| Plan | vCPU / RAM | NVMe | Network / traffic | Annual price | Order |
| --- | --- | --- | --- | --- | --- |
| LAX-STARTER | 2 cores / 2 GB | 40 GB | 2 Gbps / 5 TB | $200 (list $240) | [Order LAX-STARTER](https://sixtynet.net/aff.php?aff=39) |
| LAX-MICRO | 4 cores / 4 GB | 80 GB | 4 Gbps / 10 TB | $400 (list $480) | [Order LAX-MICRO](https://sixtynet.net/aff.php?aff=39) |
| LAX-MEDIUM | 8 cores / 8 GB | 160 GB | 10 Gbps / 20 TB | $800 (list $960) | [Order LAX-MEDIUM](https://sixtynet.net/aff.php?aff=39) |
| LAX-LARGE | 8 cores / 16 GB | 320 GB | 10 Gbps / 40 TB | $1,600 (list $1,920) | [Order LAX-LARGE](https://sixtynet.net/aff.php?aff=39) |
| Residential-STARTER | 2 cores / 2 GB | 40 GB | 2 Gbps / 5 TB | $260 (list $300) | [Order Residential-STARTER](https://sixtynet.net/aff.php?aff=39) |

Three details the table can't hold:

- The LAX line ships with DDoS protection, and the site's only port warning applies to the residential line, where ports 80 and 443 stay closed for IP risk-control reasons. If you're hosting a public website, stay on LAX and confirm the open ports with support before you pay.
- SixtyNet publishes per-plan IOPS figures: 12,500 for the two- and four-core plans, 25,000 from eight cores up. A current NVMe drive does several hundred thousand random IOPS on its own, so read those numbers as what your slice is provisioned for, not what the hardware can do.
- LAX-LARGE is listed as 8 cores with 16 GB and 320 GB. The same 16 GB / 320 GB specification appears elsewhere on the site with 16 cores, so verify the core count on the order page.

The page also lists larger residential steps at 4 GB / 80 GB, 8 GB / 160 GB and 16 GB / 320 GB, a China-optimised pair on China Unicom premium AS9929 routing, and a top Los Angeles China Unicom-Premium configuration at $2,500 per year from $3,000. The China-route side advertises free BGP session setup, a 100 Mbps base port with 3× burst and 95th-percentile billing, which matters to you only if mainland latency is the reason you're shopping.

## What the price ladder says about value

SixtyNet's tiers scale linearly, and the arithmetic is clean enough to use as a buying rule. At the discounted annual rate you pay $100 per vCPU, $100 per GB of RAM and $5 per GB of NVMe per year, across every row of the table. The micro tier doubles everything for double the money. The large tier is exactly eight times the starter.

That structure has one practical consequence. There is no volume discount waiting at the top, so an oversized plan is a pure loss, and an undersized one is a forced migration later. Size on the resource you'll hit first, which for most workloads is RAM, not disk.

In monthly terms, LAX-STARTER works out to about $16.67 and LAX-MICRO about $33.33. Against a market where entry NVMe VPS plans start near $2.48 a month, that looks expensive until you do the per-gigabyte maths, and then it still looks expensive: roughly $0.42 per GB of NVMe per month at SixtyNet versus about $0.12 on a $2.48 plan with 20 GB. You're paying for the Los Angeles network position, the residential IP option and the DDoS filtering, not for cheap storage. If all you want is the largest possible disk for the lowest price, this isn't the right shop.

👉 [Compare all SixtyNet VPS tiers on one page](https://sixtynet.net/aff.php?aff=39)

## How to confirm you actually got NVMe

Provider pages say "NVMe" everywhere. The disk underneath is the only thing that settles it, and you can check in about 60 seconds after your first SSH login.

1. **Check the transport.** Run `lsblk -d -o NAME,ROTA,TRAN`. The `TRAN` column should read `nvme` and `ROTA` should be `0`. If `TRAN` shows `sata` or the disk name starts with `sd`, you're on a SATA-attached device regardless of what the order page said. Take a screenshot and open a ticket.
2. **Measure random reads.** Run `fio --rw=randread --bs=4k --runtime=10 --name=t --filename=/tmp/t --size=1G --iodepth=64` and read the `IOPS=` and `BW=` values in the output. This is a 4K random-read test at queue depth 64 over a 1 GB file. Compare the result with the 12,500 IOPS SixtyNet publishes for the two- and four-core plans; a number far below that on an idle server is worth asking about.
3. **Re-test when the server is busy.** Storage on a VPS is shared with neighbours, and the interesting number is the tail latency under your own production load, not a fresh install on an empty host.

Run the same test on a SATA VPS and the gap shows up in the IOPS figure long before it shows up in any sequential benchmark.

## Which plan for which workload

**PostgreSQL or MySQL.** Random I/O is the constraint, and the plan-level IOPS figure applies to every tier. The decision is capacity: add your data size, index size and a month of WAL growth, then leave 30% headroom. A database under about 25 GB fits LAX-STARTER's 40 GB comfortably. Past that, move to LAX-MICRO rather than running the disk near full, because a full NVMe volume slows down before it fills up.

**Container builds and CI.** Layer caches and image extraction are random-read heavy, so NVMe helps here for real. The catch is space: a build host with a dozen cached images fills 40 GB quickly. If you rebuild more than a few projects a day, LAX-MICRO's 80 GB is the sensible floor.

**Search indexes and vector databases.** When an Elasticsearch or Qdrant index spills to disk, tail latency is what users feel, and NVMe keeps that flat. But indexes want RAM first. Running pgvector or Meilisearch in 2 GB is the more common mistake, so pick the tier by RAM and treat the disk as a bonus.

**Anything CPU-bound.** Compiling, encoding, game servers, bots. A faster disk won't move those numbers, and SixtyNet's ladder charges $100 per core per year with no exceptions. Buy the core count you need and nothing else; the storage comes along for the ride.

## Where SixtyNet's price sits in the market

Entry prices for NVMe VPS plans currently advertised across the market, all monthly billing:

| Provider | Entry price | Entry configuration |
| --- | --- | --- |
| Cloudzy | $2.48/mo (50% off a $4.95 list price) | 1 vCPU, 512 MB RAM, 20 GB NVMe, 1 TB transfer |
| Arct Cloud | $3.89/mo | 1 vCPU, 1 GB RAM, 15 GB NVMe |
| DartNode | $8.95/mo | 1 vCPU, 1 GB RAM, 30 GB NVMe, 1 Gbps unmetered |
| RamNode | $14/mo (premium NVMe line) | 2 cores, 2 GB RAM, 80 GB NVMe, 6 TB transfer |
| AsiaWebHost | S$55/mo | 2 vCPU, 4 GB RAM, 60 GB NVMe, 3 TB transfer, Singapore |

The spread from $2.48 to S$55 buys different amounts of RAM and CPU far more than different disks; every plan in that table is NVMe. Cloudzy's headline is a promotional rate with no published renewal price, so treat it as an entry figure rather than a run rate. SixtyNet sits outside this group on billing: annual only on the plans above, with quarterly and annual cycles carrying their own discounts. Over twelve months you'll pay $200 at minimum, which is more cash up front than any monthly plan here and less than RamNode's premium tier for a comparable amount of RAM.

## Four things to confirm before you pay

**What renews, and at what price.** The $200 LAX-STARTER rate sits next to a $240 list price, and the site advertises discounts for quarterly and annual payment. Ask what your specific plan renews at before the first invoice, because a $40 gap on the smallest plan becomes $320 on the largest.

**Ports on the residential line.** Residential IP plans keep 80 and 443 closed. The residential IP is the product, and it's aimed at scrapers, ad verification and account work where a datacenter IP gets flagged. A public website on that line won't serve traffic.

**Refund terms, which vary by product.** The site states that refunds on the AS9929 residential variant are issued as account balance only. Read the terms attached to the plan you're ordering rather than assuming a single policy covers the range, and confirm the window length at checkout.

**Upgrading later needs work inside the OS.** Adding disk to a KVM guest usually means extending the partition and filesystem yourself with `growpart` and `resize2fs` after the control panel finishes. Budget ten minutes and a snapshot before resizing anything.

👉 [Start with the $200 LAX-STARTER plan](https://sixtynet.net/aff.php?aff=39)

## The decision rule

If you need 2 GB of RAM or less and your data fits in 40 GB, buy LAX-STARTER and put the saved $200 toward a backup target. If your database or image cache is heading past 40 GB, or your build jobs are queueing on RAM, LAX-MICRO at $400 a year is the next step and the only one worth taking. The 8-core and 16-core tiers make sense when you're consolidating several services onto one box, not for a single site.

Before the card comes out, log in and run `lsblk -d -o NAME,ROTA,TRAN` on the new server. It takes one command to know whether the NVMe in the marketing copy is the NVMe under your data.

👉 [Order the SixtyNet plan that matches your RAM and storage numbers](https://sixtynet.net/aff.php?aff=39)
