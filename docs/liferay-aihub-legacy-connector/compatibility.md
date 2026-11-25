---
title: Compatibility
nav_order: 10
description: The eight supported Liferay DXP releases, their platform, and the Liferay Search endpoint on each.
---

# Compatibility
{: .no_toc }

The connector supports the eight quarterly releases from **2024.Q1 to 2025.Q4**, each tested on the latest update of the quarter. It deactivates itself on 2026.Q1 and later, which have Liferay's own cell.

| Release | Platform | AI Hub Liferay Search endpoint |
|---|---|---|
| 2024.Q1 | Java EE | Served by the module (`feature.flag.LPS-179669=true`) |
| 2024.Q2 | Java EE | Served by the module (`feature.flag.LPS-179669=true`) |
| 2024.Q3 | Java EE | Liferay's, behind `feature.flag.LPD-11232=true` |
| 2024.Q4 | Java EE | Liferay's, behind `feature.flag.LPD-11232=true` |
| 2025.Q1 | Java EE | Liferay's, behind `feature.flag.LPD-11232=true` |
| 2025.Q2 | Java EE | Liferay's, behind `feature.flag.LPD-11232=true` |
| 2025.Q3 | Jakarta EE | Liferay's, behind `feature.flag.LPD-11232=true` |
| 2025.Q4 | Jakarta EE | Liferay's, on by default |

The chat, tokens, proxy and the in-editor assistant's editor DOM were validated on a real Web Content editor on every one of the eight releases. A deployed-connector end-to-end run (image install → token → inject → proxy → write-back) was done on 2025.Q4. The current release is **2.11.0**.

{: .important }
**A Liferay DXP Enterprise Subscription and a licensed Liferay AI Hub are both required.** The connector is proprietary, for Liferay internal use, and is not supported by Liferay.
