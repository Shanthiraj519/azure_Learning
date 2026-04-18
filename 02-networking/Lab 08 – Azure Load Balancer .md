# Lab 08 – Azure Load Balancer —

Hands-on lab deploying a public Azure Load Balancer to distribute HTTP traffic
across two backend virtual machines in Central US.

---

## Environment

| Component | Details |
|-----------|---------|
| Subscription | Shanthi's_Lab |
| Resource Group | rg-lab-networking |
| Region | Central US |
| VNet | vnet-lab-centralus — 10.0.0.0/16 |
| Subnet | web-subnet — 10.0.1.0/24 |
| NSG | nsg-lab-web — associated with web-subnet |
| VM 1 | vm-lab-lb-01 — Ubuntu 24.04 LTS — Standard_D2s_v3 |
| VM 2 | vm-lab-lb-02 — Ubuntu 24.04 LTS — Standard_D2s_v3 |
| Load Balancer | lb-lab-centralus — Standard SKU — Public |
| Frontend IP | pip-lb-lab-01 — 172.173.69.125 |
| Backend Pool | lb-backend-pool — vm-lab-lb-01, vm-lab-lb-02 |
| Health Probe | lb-health-probe-http — HTTP — port 80 |
| LB Rule | lb-rule-http — TCP — frontend 80 → backend 80 |

---

## Objective

Deploy a Standard public Load Balancer, configure a backend pool with two
Linux VMs running nginx, set up a health probe and load balancing rule, and
verify traffic is distributed across both VMs.

---

## Steps

1. Created VNet `vnet-lab-centralus` (10.0.0.0/16) with `web-subnet` (10.0.1.0/24)
   in resource group `rg-lab-networking`, region Central US.
2. Created NSG `nsg-lab-web` with inbound rules allowing SSH (port 22, priority 100)
   and HTTP (port 80, priority 110). Associated with `web-subnet`.
3. Deployed `vm-lab-lb-01` and `vm-lab-lb-02` — Ubuntu 24.04 LTS, Standard_D2s_v3,
   placed in `vnet-lab-centralus / web-subnet` with no public IP.
4. Temporarily assigned public IPs to each VM to SSH in and install nginx:
```bash
sudo apt update && sudo apt install nginx -y
sudo systemctl enable nginx
```
5. Set custom response pages on each VM to identify which VM is serving traffic:
```bash
# On vm-lab-lb-01
python3 -c "
with open('/var/www/html/index.html', 'w', encoding='utf-8') as f:
    f.write('<!DOCTYPE html><html><head><meta charset=\"UTF-8\"></head><body><h1>Welcome Msg from VM1</h1></body></html>')
"

# On vm-lab-lb-02
python3 -c "
with open('/var/www/html/index.html', 'w', encoding='utf-8') as f:
    f.write('<!DOCTYPE html><html><head><meta charset=\"UTF-8\"></head><body><h1>Welcome Msg from VM2</h1></body></html>')
"
```
6. Removed temporary public IPs from both VMs after nginx setup.
7. Created Load Balancer `lb-lab-centralus` — Standard SKU, Public, Regional.
8. Added frontend IP configuration `lb-frontend` with new public IP `pip-lb-lab-01`.
9. Created backend pool `lb-backend-pool` with both VMs added.
10. Configured health probe `lb-health-probe-http` — HTTP, port 80, path `/`, interval 15s.
11. Created load balancing rule `lb-rule-http` — TCP, frontend port 80 → backend port 80,
    session persistence: None.
12. Verified health probe marked both VMs as Healthy in the backend pool.
13. Navigated to `http://172.173.69.125` — confirmed traffic served from both VMs
    by refreshing multiple times and observing alternating responses.

---

## Key Concepts

**Azure Load Balancer** operates at Layer 4 (TCP/UDP) and distributes inbound
traffic across healthy backend instances based on a configured load balancing rule.

**Standard SKU vs Basic SKU** — Standard LB supports availability zones, higher
SLA (99.99%), HTTPS health probes, and is required for production workloads.
Basic SKU is free but limited and being retired.

**Frontend IP configuration** — the public IP address that clients connect to.
The LB receives traffic here and forwards it to the backend pool.

**Backend pool** — the group of VMs that receive traffic. VMs must be in the
same VNet. The LB uses the health probe to determine which VMs are healthy
before sending traffic to them.

**Health probe** — periodically checks each backend VM on a specified port and
path. If a VM fails the probe, the LB stops sending traffic to it until it
recovers. This enables automatic failover.

**Load balancing rule** — maps a frontend port to a backend port and ties
together the frontend IP, backend pool, and health probe into a single traffic
distribution policy.

**Session persistence (None)** — with no session persistence, each new
connection is distributed across all healthy VMs using a 5-tuple hash
(source IP, source port, destination IP, destination port, protocol).
Setting it to Client IP would pin a client to the same VM.

**No public IP on backend VMs** — backend VMs don't need public IPs when
behind a Standard Load Balancer. All inbound traffic flows through the LB
frontend, which is the correct security posture.

---

## Learnings

- Standard SKU Load Balancer requires backend VMs to also have Standard SKU
  public IPs if assigned — mixing Basic and Standard SKU causes errors.
- VMs with no public IP behind a Standard LB have no default outbound
  internet access — outbound NAT rules or a NAT Gateway are needed for
  outbound connectivity.
- Health probe must succeed on the backend VM before the LB sends traffic to
  it — nginx must be running and port 80 must be open via NSG.
- Session persistence set to None means the LB may route consecutive requests
  from the same browser to different VMs — this is expected behavior and
  confirms load distribution is working.
- Emoji rendering in nginx requires explicit UTF-8 charset declaration in the
  HTML head — terminal encoding alone is not sufficient.
- Regional vCPU quota limits on free/PAYG subscriptions can block VM creation
  even after deallocating existing VMs — switching regions resolves this.

---

## Screenshots

| # | File Name | Description |
|---|-----------|-------------|
| 01 | <img width="1892" height="801" alt="image" src="https://github.com/user-attachments/assets/6942f359-985f-427c-ad69-8006741f3273" />
 | vnet-lab-centralus overview — address space and web-subnet |
| 02 | <img width="1897" height="844" alt="image" src="https://github.com/user-attachments/assets/d08a0362-1d42-4b5f-a598-6e57999a308d" />
 | nsg-lab-web inbound rules — SSH (100) and HTTP (110) |
| 03 | <img width="956" height="375" alt="image" src="https://github.com/user-attachments/assets/77cae36f-1cb7-48b4-b3c4-18e4e19bd4bd" />
 | Both vm-lab-lb-01 and vm-lab-lb-02 showing Status: Running |
| 04 | <img width="1899" height="832" alt="image" src="https://github.com/user-attachments/assets/3a687566-65ca-47b0-af06-0538f7640c3a" />
 | lb-lab-centralus overview — name, frontend IP, region |
| 05 | <img width="1919" height="807" alt="image" src="https://github.com/user-attachments/assets/c17c6aa9-cd1f-4de8-93b5-ebbd00b59047" />
 | Frontend IP configuration showing pip-lb-lab-01 |
| 06 | <img width="1908" height="811" alt="image" src="https://github.com/user-attachments/assets/f3ad0cb9-9ffd-409c-91e0-97f146f83818" />
 | Backend pool showing both VMs added and healthy |
| 07 | <img width="1919" height="792" alt="image" src="https://github.com/user-attachments/assets/92272b7a-34b9-4a9b-9b12-54f9149ac362" />
 | Health probe settings — HTTP, port 80, interval 15s |
| 08 | <img width="1906" height="687" alt="image" src="https://github.com/user-attachments/assets/71167f8c-926e-4f5e-9fa1-8dc21431195b" />
 | Load balancing rule — ports, backend pool, health probe |
| 09 | <img width="805" height="356" alt="image" src="https://github.com/user-attachments/assets/c2fdb9a5-c8ed-4b38-8d4e-77c6f0f72da0" />
 | Browser showing Welcome Msg from VM1 |
| 10 | <img width="697" height="263" alt="image" src="https://github.com/user-attachments/assets/19d5afa4-133a-4b51-aa4d-e5c17ab9dac6" />
| Browser showing Welcome Msg from VM2 |
