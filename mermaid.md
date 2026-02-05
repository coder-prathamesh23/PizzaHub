flowchart LR
%% =========================
%% HUB (shared services)
%% =========================
subgraph HUB["Connectivity / Hub Subscription"]
    hubvnet["Hub VNet (connectivity)"]
    dns["Private DNS zones + DNS resolver (central)"]
end

%% =========================
%% DATA PLATFORM SPOKE
%% =========================
subgraph DP["Data Platform Subscription (Spoke)"]
    dpvnet["Landing Zone VNet (min subnets + PE subnet if needed)"]
    dpcore["Core RG (Key Vault + Monitoring + optional Storage)"]
    adls["ADLS Gen2 Storage Account (Blob + DFS)"]
    cap["Fabric Capacity (Azure resource)"]
end

fabric["Microsoft Fabric (SaaS) - Workspaces / OneLake"]

%% =========================
%% DATA SCIENCE SPOKE
%% =========================
subgraph DS["Data Science Subscription (Spoke)"]
    dsvnet["Landing Zone VNet (workload subnet + PE subnet)"]
    pesub["Private Endpoint Subnet"]
    dscore["Core RG (Key Vault + Storage + ACR + Monitoring)"]
    amlws["Azure ML Workspace (control plane)"]

    subgraph AML["Azure ML - Managed Network Isolation"]
        mnet["Microsoft Managed VNet"]
        compute["Training + Batch/Online Compute"]
    end
end

%% =========================
%% HUB-AND-SPOKE LINKS
%% =========================
hubvnet ---|"VNet peering"| dpvnet
hubvnet ---|"VNet peering"| dsvnet

dns -. "DNS links" .-> dpvnet
dns -. "DNS links" .-> dsvnet

%% =========================
%% DS internal wiring
%% =========================
dsvnet --> pesub
pesub -->|"Private endpoints"| amlws
dscore --> amlws
mnet --> compute

%% =========================
%% DATA PLATFORM addition (ADLS + PE)
%% =========================
dpvnet -->|"Private endpoints"| adls

%% =========================
%% Fabric relationship
%% =========================
cap --> fabric
compute -. "HTTPS to Fabric/OneLake (Entra ID + Fabric CA)" .-> fabric

note["Open item: Fabric Conditional Access for AML Managed VNet traffic (CA exception vs Customer VNet injection)."]
note --- fabric
note --- mnet
