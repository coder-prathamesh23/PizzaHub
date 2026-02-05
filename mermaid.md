%%{init: {'flowchart': {'curve': 'linear', 'nodeSpacing': 90, 'rankSpacing': 110}} }%%
flowchart LR

subgraph HUB["Connectivity / Hub Subscription"]
    hubvnet["Hub VNet (Connectivity)"]
    dns["Private DNS Zones + DNS Resolver (shared / central)"]
end

subgraph DP["Data Platform Subscription (Spoke)"]
    dpvnet["Landing Zone VNet (min subnets + PE subnet if needed)"]
    dpcore["Core RG: Key Vault + Monitoring (optional Storage)"]
    adls["ADLS Gen2 Storage Account (Blob + DFS)"]
    cap["Fabric Capacity (Azure resource)"]
end

fabric["Microsoft Fabric (SaaS) Workspaces / OneLake"]

subgraph DS["Data Science Subscription (Spoke)"]
    dsvnet["Landing Zone VNet (workload subnet + PE subnet)"]
    pesub["Private Endpoint Subnet"]
    dscore["Core RG: Key Vault + Storage + ACR + Monitoring"]
    amlws["Azure ML Workspace (control plane)"]

    subgraph AML["Azure ML - Managed Network Isolation"]
        mnet["Microsoft Managed VNet"]
        compute["Training + Batch/Online Compute"]
    end
end

hubvnet ---|"VNet peering"| dpvnet
hubvnet ---|"VNet peering"| dsvnet

dns -. "Private DNS VNet links" .-> dpvnet
dns -. "Private DNS VNet links" .-> dsvnet

dsvnet --> pesub
pesub -->|"Private Endpoint(s)"| amlws
dscore --> amlws
mnet --> compute

dpvnet -->|"Private Endpoint(s)"| adls

cap --> fabric
compute -. "HTTPS to OneLake/Fabric (Entra ID + Fabric Conditional Access)" .-> fabric

note["Open item (BIGGEST RISK): Fabric Conditional Access behavior for AML Managed VNet traffic. Option A: CA exception for AML managed network. Option B: Customer VNet injection (fallback)."]
note --- fabric
note --- mnet
