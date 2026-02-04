flowchart TB

  subgraph HUB["Connectivity subscription"]
    hub["Hub VNet connectivity"]
    dns["Private DNS zones and DNS resolver"]
  end

  subgraph DS["Data science subscription"]
    direction TB
    dsvnet["Landing zone VNet with workload subnet and private endpoint subnet"]
    pesub["Private endpoint subnet"]
    amlws["Azure ML workspace control plane"]
    core["Core resource group with Key Vault, storage and monitoring, container registry if required"]

    subgraph AML["Azure ML managed network isolation"]
      direction TB
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

  fabric["Microsoft Fabric service with workspaces and OneLake"]

  hub ---|"VNet peering"| dsvnet
  hub ---|"VNet peering"| dpvnet

  dns -. "Private DNS VNet links" .-> dsvnet
  dns -. "Private DNS VNet links" .-> dpvnet

  dsvnet --> pesub
  pesub -->|"Private endpoint"| amlws
  amlws --- core
  amlws --> mnet
  mnet --> compute

  dpvnet --- dpcore
  cap --> fabric

  compute -. "HTTPS to Fabric and OneLake subject to Entra identity and Fabric conditional access" .-> fabric

  note["Open item confirm how Fabric conditional access treats traffic from Azure ML managed VNet. If blocked, request conditional access exception or switch to customer VNet injection."]
  note --- mnet
  note --- fabric
