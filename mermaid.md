flowchart LR
%% =========================
%% HUB (shared services)
%% =========================
subgraph HUB["Connectivity / Hub Subscription"]
    hubvnet["Hub VNet<br/>(connectivity)"]
    dns["Private DNS Zones + DNS Resolver<br/>(central)"]
end

%% =========================
%% DATA PLATFORM SPOKE
%% =========================
subgraph DP["Data Platform Subscription (Spoke)"]
    dpvnet["Landing Zone VNet<br/>(minimal)"]
    dppe["Private Endpoint<br/>Subnet"]
    adls["ADLS Gen2 Storage Account<br/>(Blob + DFS)"]
    dpcore["Core RG<br/>(Key Vault, Monitoring,<br/>optional Storage)"]
    cap["Fabric Capacity (Azure resource)<br/>(manual)"]
end

fabric["Microsoft Fabric (SaaS)<br/>Workspaces + OneLake"]

%% =========================
%% DATA SCIENCE SPOKE
%% =========================
subgraph DS["Data Science Subscription (Spoke)"]
    dsvnet["Landing Zone VNet<br/>(workload + PE subnets)"]
    pesub["Private Endpoint<br/>Subnet"]
    dscore["Core RG<br/>(Key Vault, Storage, ACR,<br/>Monitoring)"]
    amlws["Azure ML Workspace<br/>(control plane)"]

    subgraph AML["AML Managed Network Isolation"]
        mnet["Microsoft Managed<br/>VNet"]
        compute["Training + Batch/Online<br/>Compute"]
    end
end

%% =========================
%% HUB-AND-SPOKE LINKS
%% =========================
hubvnet ---|"<span style='color:#1f77b4'>VNet peering</span>"| dpvnet
hubvnet ---|"<span style='color:#ff7f0e'>VNet peering</span>"| dsvnet

dns -. "<span style='color:#2ca02c'>Private DNS VNet links</span>" .-> dpvnet
dns -. "<span style='color:#d62728'>Private DNS VNet links</span>" .-> dsvnet

%% =========================
%% DS internal wiring
%% =========================
dsvnet --> pesub
pesub -->|"<span style='color:#8c564b'>Private endpoints</span>"| amlws
dscore --> amlws
mnet --> compute

%% =========================
%% DP internal wiring (ADLS + PE)
%% =========================
dpvnet --> dppe
dppe -->|"<span style='color:#17becf'>Private endpoints</span>"| adls

%% =========================
%% Fabric relationship
%% =========================
cap --> fabric

%% Shortened to prevent SVG placeholder-label overlap
compute -. "<span style='color:#ff4d4d'>HTTPS to Fabric/OneLake</span>" .-> fabric

note["Open item (BIGGEST RISK):<br/>How will Fabric Conditional Access treat traffic from AML Managed VNet?<br/>Option A: CA exception to allow AML Managed VNet.<br/>Option B (fallback): customer VNet injection."]
note --- fabric
note --- mnet

%% =========================
%% Only highlight Fabric Capacity as manual
%% =========================
classDef manual fill:#FFF2CC,stroke:#B8860B,stroke-width:2px,color:#000;
class cap manual;

%% =========================
%% Unique colors for EVERY connector line
%% =========================
linkStyle 0  stroke:#1f77b4,stroke-width:2px;
linkStyle 1  stroke:#ff7f0e,stroke-width:2px;
linkStyle 2  stroke:#2ca02c,stroke-width:2px;
linkStyle 3  stroke:#d62728,stroke-width:2px;
linkStyle 4  stroke:#9467bd,stroke-width:2px;
linkStyle 5  stroke:#8c564b,stroke-width:2px;
linkStyle 6  stroke:#e377c2,stroke-width:2px;
linkStyle 7  stroke:#7f7f7f,stroke-width:2px;
linkStyle 8  stroke:#bcbd22,stroke-width:2px;
linkStyle 9  stroke:#17becf,stroke-width:2px;
linkStyle 10 stroke:#00a2ff,stroke-width:2px;
linkStyle 11 stroke:#ff4d4d,stroke-width:2px;
linkStyle 12 stroke:#00cc99,stroke-width:2px;
linkStyle 13 stroke:#cc66ff,stroke-width:2px;
