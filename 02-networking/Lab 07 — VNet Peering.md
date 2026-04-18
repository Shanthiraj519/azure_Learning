<img width="918" height="799" alt="image" src="https://github.com/user-attachments/assets/fceacb3c-90f3-4ec2-adae-621ca3ab762d" /># Lab 07 — VNet Peering —

Hands-on lab configuring bidirectional VNet peering between two Azure virtual
networks across different regions using the Azure portal.

---

## Environment

| Component | Details |
|-----------|---------|
| Subscription | Shanthi's_Lab |
| VNet 1 | vnet-peer-hub — 10.10.0.0/16 — Central India |
| VNet 2 | DC-AZU-vnet — 10.0.0.0/16 — East US 2 |
| Peering name | Test_peering (both sides) |
| Peering state | Connected — Fully Synchronized |
| Gateway transit | Disabled |
| Forwarded traffic | Enabled (both sides) |

---

## Steps

1. Navigated to `vnet-peer-hub` in the Azure portal.
2. Opened **Peerings** blade and clicked **+ Add**.
3. Named the peering `Test_peering` on the local side.
4. Selected `DC-AZU-vnet` as the remote virtual network.
5. Enabled **Allow access** and **Allow forwarded traffic** on both sides.
6. Left gateway transit disabled — no VPN gateway deployed.
7. Azure automatically created the return peering on `DC-AZU-vnet`.
8. Verified both sides show **Peering state: Connected** and **Fully Synchronized**.

---

## Key concepts

**VNet Peering** connects two Azure VNets so traffic routes over the Microsoft
backbone — not the public internet — with low latency and no bandwidth bottleneck.

**Global VNet Peering** extends peering across regions. This lab peers
Central India and East US 2, making it a global peering scenario.

**Bidirectional peering** — Azure provisions a link on each VNet when configured
from one side. Both must show Connected for traffic to flow.

**Forwarded traffic** — allows traffic originating outside the VNet to pass
through the peering. Enabled here for future hub-spoke topology readiness.

**Gateway transit** — allows a peered VNet to use a VPN or ExpressRoute gateway
in the remote VNet. Not configured — no gateway deployed in this lab.

---

## Learnings

- Creating a peering from one side automatically provisions the return link on
  the remote VNet — no need to configure both sides manually.
- Both **Peering state: Connected** and **Sync status: Fully Synchronized** must
  be green before traffic flows.
- Global VNet Peering works identically to regional peering in configuration
  but carries data transfer charges across regions.
- Non-overlapping address spaces are mandatory — 10.10.0.0/16 and 10.0.0.0/16
  satisfy this requirement.

---

## Screenshots

| Screenshot | Description |
|------------|-------------|
| 01 | vnet-peer-hub — Peerings blade showing Test_peering — Connected| <img width="1568" height="512" alt="image" src="https://github.com/user-attachments/assets/cdc32595-b105-4c85-969a-69681506b912" />
 |<img width="1568" height="621" alt="image" src="https://github.com/user-attachments/assets/a334b0c1-38ee-42a0-91fd-14a516e75806" />
| 02 | Test_peering settings — Forwarded traffic enabled, gateway transit disabled |<img width="1236" height="718" alt="image" src="https://github.com/user-attachments/assets/0879a07a-0474-4bf1-9160-e6df9ab67dbc" />

| 03 | DC-AZU-vnet — Address space showing peered network 10.10.0.0/16 |<img width="1236" height="718" alt="image" src="https://github.com/user-attachments/assets/9a46f36a-42b4-494e-8fb1-d048c5f8cb44" />

| 04 | DC-AZU-vnet — Test_peering — Connected, both access options enabled |<img width="1568" height="779" alt="image" src="https://github.com/user-attachments/assets/edd52cf2-f7af-432e-872f-f9dc5f0dc079" />|
<img width="959" height="376" alt="image" src="https://github.com/user-attachments/assets/acf40f58-3ce6-41e9-9968-fc204aa13212" />

