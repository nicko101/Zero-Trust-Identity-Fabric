# implementation logic: azure transit security hub

this directory documents the as-built configuration that executes the dual-tunnel transit design. it details the specific azure routing tables, load balancer configurations, and nva traffic steering logic required to maintain stateful inspection.

---

## 1. direct s2s tunnel & internet breakout (tunnel 1)
the implementation utilizes a direct s2s ipsec tunnel terminating on the palo alto nva for all internet-bound traffic originating from the on-premises site 1.

* **logic:** by terminating the tunnel directly on the nva (via the external load balancer), we bypass the standard azure gateway for internet-bound traffic. this ensures the palo alto l7 engine has full visibility into outbound flows before they exit the azure edge.
* **split routing proof:** validation of the split tunnel architecture separating internet and backbone traffic.
![split routing proof](../validation-proof/images/proof_split_routing.png)
* **deep dive:** [palo alto azure transit](../../docs/tech-notes/palo-azure-transit.md)

## 2. ingress steering & vpn gateway udr (tunnel 2)
to satisfy the requirement for 100% inspection of hybrid traffic, native azure system routing is overridden at the gateway level.

* **logic:** a user-defined route (udr) is attached to the gatewaysubnet. this route table forces traffic arriving from the on-premises vpn gateway to hit the nva trust interface before reaching the spokes.
* **nva gateway routing proof:**
![nva gateway udr](../validation-proof/images/proof_split_routing_nva_gw_.png)
* **vpn to spoke flow logs:**
![vpn spoke logs](../validation-proof/images/2303_logs_vpn_gw_spoke.png)

## 3. spoke egress & traffic steering
workload spokes are configured to recognize the transit hub as their primary security boundary.

* **logic:** subnet-level udrs point the default route and inter-vnet routes to the internal load balancer vip. 
* **spoke client validation:**
![spoke egress proof](../validation-proof/images/proof_client_spokes.png)

## 4. high availability & backbone bgp
the hybrid backbone utilizes dynamic routing to share spoke address spaces while maintaining the isolated overlay for inspection.

* **bgp adjacency:** validation of the bgp peering established over the vpn gateway tunnel.
![bgp established](../validation-proof/images/palo-azure_vpngw_bgp.png)
* **poc architecture view:**
![poc split routing](../validation-proof/images/proof_poc_split_routing.png)

## 5. security policy enforcement
* **logic:** the palo alto utilizes user-id and security profiles to enforce the security mandate on all transit traffic.
* **l7 inspection logs:**
![palo alto logs](../validation-proof/images/logs_onprem_pa-cppmvm1812.png)
* **deep dive:** [palo alto security logic](../../docs/tech-notes/palo-alto-security.md)