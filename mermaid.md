%%{init: {'theme':'base','themeVariables':{'fontFamily':'Arial','fontSize':'12px'},'flowchart':{'curve':'linear','nodeSpacing':60,'rankSpacing':70}}}%%
flowchart LR
%% =========================
%% HUB (shared services)
%% =========================
subgraph HUB["Connectivity / Hub Subscription"]
    hubvnet["Hub VNet<br/>(Connectivity)"]
    dns["Private DNS Zones + DNS Resolver<br/>(shared / central)"]
end

%% =========================
%% DATA PLATFORM SPOKE
%% =========================
subgraph DP["Data Platform Subscription (Spoke)"]
    dpvnet["Landing Zone VNet<br/>(min subnets + PE subnet if needed)"]
    dpcore["Core RG:<br/>Key Vault + Monitoring<br/>(+ optional Storage)"]
    adls["ADLS Gen2 Storage Account<br/>(Blob + DFS)"]
    cap["Fabric Capacity<br/>(Azure resource)"]
end

fabric["Microsoft Fabric (SaaS)<br/>Workspaces / OneLake"]

%% =========================
%% DATA SCIENCE SPOKE
%% =========================
subgraph DS["Data Science Subscription (Spoke)"]
    dsvnet["Landing Zone VNet<br/>(workload subnet + PE subnet)"]
    pesub["Private Endpoint Subnet"]
    dscore["Core RG:<br/>Key Vault + Storage + ACR<br/>+ Monitoring"]
    amlws["Azure ML Workspace<br/>(control plane)"]

    subgraph AML["Azure ML - Managed Network Isolation"]
        mnet["Microsoft Managed VNet"]
        compute["Training + Batch/Online<br/>Compute"]
    end
end

%% =========================
%% HUB-AND-SPOKE LINKS
%% =========================
hubvnet ---|"VNet&nbsp;peering"| dpvnet
hubvnet ---|"VNet&nbsp;peering"| dsvnet

dns -. "Private&nbsp;DNS&nbsp;VNet&nbsp;links" .-> dpvnet
dns -. "Private&nbsp;DNS&nbsp;VNet&nbsp;links" .-> dsvnet

%% =========================
%% DS internal wiring
%% =========================
dsvnet --> pesub
pesub -->|"Private&nbsp;Endpoint(s)"| amlws
dscore --> amlws
mnet --> compute

%% =========================
%% Data Platform addition (ADLS + PE)
%% =========================
dpvnet -->|"Private&nbsp;Endpoint(s)"| adls

%% =========================
%% Fabric relationship
%% =========================
cap --> fabric
compute -. "HTTPS to OneLake/Fabric<br/>(Entra ID + Fabric Conditional Access)" .-> fabric

note["Open item (BIGGEST RISK):<br/>How will Fabric Conditional Access treat traffic from AML Managed VNet?<br/>Option A: Conditional Access exception to allow AML Managed VNet.<br/>Option B (fallback): Customer VNet injection."]
note --- fabric
note --- mnet
