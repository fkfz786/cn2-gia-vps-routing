# CN2 GIA VPS: Is the Return Path Really GIA? What 140–180 ms Costs, Which Port Speed You Actually Get, and Where Evoxt Fits

CN2 GIA is China Telecom's AS4809 premium backbone, and it only earns its price when the traffic coming **back** from your server into China rides it. Get that right and mainland latency holds at roughly 140–180 ms through Beijing's 20:00–23:00 peak instead of doubling with packet loss. Get it wrong and you have bought ordinary AS4134 routing with a nicer label on the invoice.

Evoxt, the provider this guide is built around, is not a CN2 GIA seller in any documented sense. Its Hong Kong location advertises "optimized routing to China via the high-quality CN2 network," which is a CN2 claim and not a GIA one, and says nothing about the return path. An independent Frankfurt test of Evoxt's $2.99 plan found no CN2, no GIA and no CMIN2 anywhere in the route. What Evoxt actually sells is a 1 Gbps port on every plan, high single-core clock speed, and eleven VM sizes from $2.99 to $95.99 a month.

Below: how to check a GIA claim before you pay, why port caps matter more than routing labels at the cheap end, what each provider does when your traffic runs out, and which Evoxt plan is worth the money for which job.

> **If you want the short answer:** documented bidirectional GIA is what DMIT and HostDare sell, on 30–500 Mbps ports, from $36.9–$40.99 a year. Evoxt is the other kind of buy: 1 Gbps ports, fast single cores, $2.99 entry, China routing you have to test yourself. Most people should start on **VM-1.5 at $6.95/mo** (2 vCPU, 2 GB RAM, 20 GB, 1.5 TB) rather than the 1-core VM-1, which costs only $0.96 less. 👉 [Check Evoxt's current plans and pricing](https://bit.ly/EvoXt)

## Evoxt's full VM lineup, and what the table leaves out

Evoxt runs one plan naming scheme across its 17 locations, from a 512 MB test box up to 16 cores and 32 GB. Every one of the eleven plans carries a 1 Gbps port, which is unusual at this price and is the strongest reason to look at them at all.

| Plan | vCPU / RAM / Storage | Traffic · Port | Price (USD, monthly) | Order |
| --- | --- | --- | --- | --- |
| VM-0.5 | 1 / 512 MB / 5 GB | 500 GB · 1 Gbps | $2.99 | [Get VM-0.5 at $2.99](https://bit.ly/EvoXt) |
| VM-0.75 | 1 / 1 GB / 10 GB | 750 GB · 1 Gbps | $4.99 | [Get VM-0.75 at $4.99](https://bit.ly/EvoXt) |
| VM-1 | 1 / 2 GB / 20 GB | 1 TB · 1 Gbps | $5.99 | [Get VM-1 at $5.99](https://bit.ly/EvoXt) |
| VM-1.5 | 2 / 2 GB / 20 GB | 1.5 TB · 1 Gbps | $6.95 | [Get VM-1.5 at $6.95](https://bit.ly/EvoXt) |
| VM-2 | 2 / 4 GB / 30 GB | 2 TB · 1 Gbps | $11.99 | [Get VM-2 at $11.99](https://bit.ly/EvoXt) |
| VM-3 | 4 / 4 GB / 30 GB | 3 TB · 1 Gbps | $14.99 | [Get VM-3 at $14.99](https://bit.ly/EvoXt) |
| VM-4 | 4 / 8 GB / 60 GB | 4 TB · 1 Gbps | $23.99 | [Get VM-4 at $23.99](https://bit.ly/EvoXt) |
| VM-6 | 8 / 8 GB / 60 GB | 5 TB · 1 Gbps | $29.99 | [Get VM-6 at $29.99](https://bit.ly/EvoXt) |
| VM-8 | 8 / 16 GB / 80 GB | 6 TB · 1 Gbps | $47.99 | [Get VM-8 at $47.99](https://bit.ly/EvoXt) |
| VM-12 | 16 / 16 GB / 80 GB | 8 TB · 1 Gbps | $60.95 | [Get VM-12 at $60.95](https://bit.ly/EvoXt) |
| VM-16 | 16 / 32 GB / 100 GB | 10 TB · 1 Gbps | $95.99 | [Get VM-16 at $95.99](https://bit.ly/EvoXt) |

Prices are the monthly rates published on Evoxt's plan pages. Evoxt sells this same naming scheme across its regions, and the deploy page shows the final spec, port and traffic allowance for whichever location you pick, so confirm there before paying.

What the plan table cannot show you:

- Weekly offsite backup on every VM, included at no charge.
- 1 IPv4 and 1 IPv6 address on every plan.
- 1 Gbps port across the whole lineup. There is no 30 Mbps or 100 Mbps entry tier to get caught by.
- Evoxt states it charges no extra bandwidth fees and no CPU burst fees. A $2.99 plan costs $2.99.
- A 99.99% uptime claim on the homepage.
- Single-core clock claimed at up to 6.0 GHz against 2.2–2.4 GHz on comparison hosts, with Evoxt's own note that the data was collected in 2020.
- No refund window spelled out next to the plans. If that matters, get it in writing from support before you pay for a year.

## What CN2 GIA actually is, and the one check that separates real from relabelled

CN2 GIA is China Telecom's premium backbone, AS4809. CN2 GT is the cheaper tier of the same network; AS4134, the 163 backbone, is the ordinary one everyone shares. The practical difference shows up between 20:00 and 23:00 Beijing time, when the generic US-to-China paths fill up. On GIA, mainland round-trip times sit at 140–180 ms and stay there. On a budget route, the same measurement roughly doubles and packet loss climbs as soon as the evening traffic arrives.

The trick that catches most buyers is directional. A provider can route the outbound leg (your server to China) over AS4809 and dump the return leg onto the cheapest available path, because the return direction is what your users' traffic actually travels. When load rises, that asymmetry turns into the exact symptom you were trying to avoid. Return path is the thing to verify, and it is the thing almost no listing page states plainly.

Here is the check, and it costs nothing:

1. Ask support for a test IP in the **exact region** you intend to buy. Regions differ, sometimes wildly, and a Tokyo test IP tells you nothing about Hong Kong.
2. From a mainland connection, run `mtr` or a traceroute toward that IP. Then run a traceroute from the VPS back to your home IP. The second direction is the one under test.
3. Do it twice: once around 14:00 Beijing time, once between 21:00 and 22:00. A route that looks clean at lunchtime and falls apart at night is the outbound-only pattern.
4. Look for AS4809 in the hops just before China on the return leg. AS4134 there means you are on the ordinary backbone no matter what the sales page calls it.

One more thing the check exposes: IPv6 frequently does not get the premium path even when IPv4 does. DMIT's LAX Pro sends IPv4 over AS4809 and IPv6 over AS4134 plus CMIN2. ByteVirt's IPv6 return runs over 9929 and CMI2. If a meaningful share of your users are on IPv6, test that stack separately.

## Where Evoxt sits on China routing, and where it doesn't

Evoxt's Hong Kong page promises "optimized routing to China via the high-quality CN2 network," which places it in CN2 territory without specifying GT or GIA, and without documenting the return direction. That is not enough to call it a GIA provider, and this page won't.

The independent Frankfurt test from May 2026 makes the regional split concrete. On that node, Telecom traffic left China on AS4134, crossed to San Jose, then took Cogent AS174 through London and Amsterdam to Frankfurt; Unicom rode AS4837 direct; the return leg came back unified through Tata. The tester's summary was blunt: no CN2, no GIA, no CMIN2, standard international BGP. Latency from Frankfurt measured 153 ms to Los Angeles, 160 ms to Singapore and 243 ms to Tokyo.

So Evoxt's China quality lives or dies by which region you deploy into, and Hong Kong is the only one making a China-routing claim at all. Test it before you commit a year to it.

What Evoxt is genuinely good at is the machine, not the route. The same Frankfurt test put single-core Sysbench at 5901 on the $2.99 box, with memory read at 70,976 MB/s and write at 40,431 MB/s. Compare that to what $3 buys elsewhere in this niche. The high-clock positioning is real, and it matters if your workload is PHP rendering, a compile job, or a game server rather than bulk transfer.

If you want to check the clock claim and the China route for yourself on the cheapest possible box, the 512 MB plan is the low-risk way in. 👉 [Start with the $2.99 VM-0.5 test box](https://bit.ly/EvoXt)

## Port speed: where cheap "GIA" plans quietly fall apart

Four providers can all print "CN2 GIA" on the same line of a comparison table and still differ by a factor of thirty in what the machine can actually push.

HostDare's cheapest GIA plan, CSSD0 at $40.99 a year, ships on a **30 Mbps** port. ByteVirt's 512 MB LA plan runs at **100 Mbps**. DMIT's WEE entry plan runs at **500 Mbps**. Evoxt puts **1 Gbps** on a $2.99 plan.

Do the arithmetic on the 30 Mbps tier: about 3.75 MB/s at the ceiling, shared across every connection at once. One 4K stream eats most of it. That cap, not the routing, is what most people feel on a cheap GIA plan, and no amount of AS4809 fixes it. HostDare's nodes carry a 1000 Mbps uplink with per-plan throttling underneath, so the hardware isn't the limit; the price tier is.

This is the honest argument for paying more or choosing differently. If your traffic is a handful of SSH sessions and a proxy, 30 Mbps is fine. If you are moving files, serving video, or running anything concurrent, check the port column before the routing column.

## What happens when you hit the traffic cap

Almost nobody puts this in a review, and it decides whether your server is usable on the 28th of the month.

DMIT throttles instead of suspending: 2 Mbps on WEE and MALIBU, 4 Mbps on TINY, Pocket, PalmSpring and STARTER, 8 Mbps on MINI and MICROv3, 10 Mbps on MEDIUMv2 and above. Your SSH session survives the month at 2 Mbps. ByteVirt's LA line drops to 1 Mbps rather than cutting you off, and it counts traffic in both directions, so uploads draw down the same quota as downloads. HostDare's plan page does not state what happens at the cap at all, which is worth a support ticket before you buy a yearly plan on a 250 GB allowance.

Evoxt states that it charges no extra bandwidth fees, which rules out the surprise-bill outcome. What the connection does once the monthly allowance is gone is not spelled out in the plan copy, so ask support directly. On a $2.99 plan the answer is usually cheap either way, but "usually" is not a policy.

## How the GIA specialists compare

Pure reference, no purchase links, because these are not the product this page is selling.

| Provider & entry plan | Config / traffic | Price | Port | Routing as documented |
| --- | --- | --- | --- | --- |
| DMIT LAX.Pro WEE | 1 vCPU / 1 GB / 20 GB / 500 GB | $36.9/yr | 500 Mbps | Outbound: Telecom AS4809, Unicom AS4837, Mobile CMI. Return: all three via AS4809. IPv6 via CMIN2 |
| HostDare CSSD0 | 1 core / 768 MB / 10 GB NVMe / 250 GB | $40.99/yr | 30 Mbps | "CN2 GIA, CU, CM optimized," Los Angeles |
| ByteVirt LA 512 | 1 core / 512 MB / 15 GB SSD / 500 GB | $66/yr | 100 Mbps | Vendor states its LA GIA line is upstreamed from DMIT; IPv4 return GIA on three carriers, IPv6 via 9929 + CMI2 |

DMIT and ByteVirt figures come from third-party plan compilations rather than the vendors' own price pages, so treat them as a range check on price and specification, not a quote. HostDare's numbers come straight from its own CN2 GIA product page.

Where each one fits: DMIT is the pick when peak-hour consistency across all three carriers is the requirement and you want current-generation hardware underneath it. HostDare is the cheapest way to get documented GIA routing, at the cost of a port that bottlenecks before the route does. ByteVirt is the small-configuration option, worth it if 512 MB to 2 GB is your actual footprint and you accept that the DMIT-upstream claim is the vendor's own. Evoxt is none of these; it is the high-clock, high-port, low-price option with China routing that varies by region.

## Before you pay: four things that only show up in the fine print

**Refund windows are short and conditional.** DMIT offers 3 days for a full refund if you have used under 30 GB, with a partial refund available within 30 days based on remaining traffic or remaining time, whichever is lower. HostDare gives 3 days, requires a valid reason, deducts $0.50–$1, and can decline outright if you have burned 20% or more of the monthly bandwidth. Evoxt's plan copy does not state a window, so confirm before an annual commitment.

**Replacing a blocked IP is a line item.** DMIT's policy notes describe one free replacement every 15 days on Premium plans, then $5 per change, with an IP Care+ option that allows immediate replacement for $5. Budget for it if you run anything that attracts blocking.

**IPv6 is usually not on the premium path.** DMIT sends IPv6 over AS4134 plus CMIN2, ByteVirt over 9929 and CMI2. Evoxt includes IPv6 on every VM but publishes no routing detail for it. If your users are on IPv6, test that separately from IPv4.

**Monthly billing is the honest way to start.** None of these providers publishes a renewal price that differs from the signup price in the material reviewed here, but the traffic cap, the port speed and the China route are all things you can only judge after a week of real use. Pay monthly for the first cycle, measure at 21:00, then commit.

## Which box for which job

Light proxy, SSH, a monitoring node, a personal blog with a few dozen visitors: **Evoxt VM-0.5 at $2.99 or VM-0.75 at $4.99** is more machine than the price suggests, and the 1 Gbps port means you won't be fighting a throttle. Test the Hong Kong route at 21:00 first.

WordPress with real mainland visitors, a Telegram bot, a small API backend: you need documented return-path GIA, which points at **DMIT's WEE or TINY** or **HostDare's CSSD1/CSSD2**. If you would rather stay on Evoxt, go to **VM-1.5 at $6.95** for the extra core and 1.5 TB, and accept that peak-hour behaviour depends on the region you chose.

Compiling, game servers, CPU-bound jobs, or anything where single-core clock beats routing: **Evoxt VM-1.5 through VM-3** is where the high-clock design pays off, and the Frankfurt test's 5901 single-core score at $2.99 backs that up.

Large file distribution or heavy concurrency: port speed first. **Evoxt VM-4 and above** gives you 1 Gbps and 4 TB+, and DMIT's 10 Gbps tiers start at STARTER if the route has to be GIA.

Two things to settle before you pay anywhere: ask for a test IP in your exact region and run the return-path check at peak hour, and get the traffic-cap behaviour in writing. Then pick the smallest plan that clears your RAM and traffic needs. 👉 [Compare Evoxt's VM sizes and current pricing](https://bit.ly/EvoXt)
