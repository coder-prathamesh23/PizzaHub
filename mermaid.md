flowchart TB

  %% =======================
  %% HUB (top)
  %% =======================
  subgraph HUB["Connectivity subscription"]
    direction TB
    hub["Hub connectivity"]
    dns["Private DNS zones and DNS resolver"]
  end

  %% =======================
  %% SPOKES (below hub)
  %% =======================
  subgraph SPOKES["Spoke subscriptions"]
    direction LR

    subgraph DS["Data science subscription"]
      direction TB
      dsvnet["Landing zone VNet with workload subnet and private endpoint subnet"]
      pesub["Private endpoint subnet"]
      amlws["Azure ML workspace control plane"]
      core["Core resource group with Key Vault, storage and monitoring, container registry if required"]

      subgraph AML["Azure ML managed network isolation"]
        direction LR
        mnet["Microsoft managed VNet"]
        compute["Training and batch or online compute"]
      end
    end

    subgraph DP["Data platform subscription"]
      direction TB
      dpvnet["Landing zone VNet minimal"]
      dpcore["Core resource group with Key Vault, monitoring and optional storage"]
      cap["Fabric capacity Azure resource"]
    end
  end

  fabric["Microsoft Fabric service with workspaces and OneLake"]

  %% =======================
  %% HUB to SPOKES
  %% =======================
  hub ---|"Hub to spoke connectivity"| dsvnet
  hub ---|"Hub to spoke connectivity"| dpvnet

  dns -. "Private DNS links" .-> dsvnet
  dns -. "Private DNS links" .-> dpvnet

  %% =======================
  %% DS internal wiring
  %% =======================
  dsvnet --> pesub
  pesub -->|"Private endpoint"| amlws
  amlws --- core

  amlws --> mnet
  mnet --> compute

  %% =======================
  %% Fabric relationship
  %% =======================
  cap --> fabric
  compute -. "HTTPS to Fabric and OneLake subject to Entra identity and Fabric conditional access" .-> fabric

  note["Open item: confirm how Fabric conditional access treats traffic from Azure ML managed VNet. If blocked, request conditional access exception or switch to customer VNet injection."]
  note --- fabric
  note --- mnet
