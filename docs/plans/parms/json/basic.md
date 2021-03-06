# Example Parameters

## Parameters Example: URI, Static, Dynamic, and Inheritance

Consider the following Plan layout:
#### Hierarchical-node plan, with Inheritance to child Action node in Config & Parms

```yaml
Name: plan0
Description: planDesc
IsActive: true
Actions:
- Name: action0
  Handler:
    Type: Synapse.Core:Synapse.Core.Runtime.EmptyHandler
    Config:
      Name: ConfigSet00
      Type: Json
      Uri: file://C:/Synapse.UnitTests/Plans/Config/json_in.json
      Values:
        {
          "CNode0": "CValue0_inline",
          "CNode1": "CValue1_inline",
          "CNode3": {
            "CNode3_1": "CValue3_1_inline",
            "CNode3_2": "CValue3_2_inline",
          }
        }
      Dynamic:
      - Source: cnode0Dynamic
        Target: CNode0
      - Source: cnode2_1Dynamic
        Target: CNode2:CNode2_1
      - Source: cnode3_1Dynamic
        Target: CNode3:CNode3_1
  Parameters:
    Name: ParamSet00
    Type: Json
    Uri: file://C:/Synapse.UnitTests/Plans/Parms/json_in.json
    Values:
      {
        "PNode0": "PValue0_inline",
        "PNode1": "PValue1_inline",
        "PNode3": {
          "PNode3_1": "PValue3_1_inline",
          "PNode3_2": "PValue3_2_inline",
        }
      }
    Dynamic:
    - Source: pnode0Dynamic
      Target: PNode0
    - Source: pnode2_1Dynamic
      Target: PNode2:PNode2_1
    - Source: pnode3_1Dynamic
      Target: PNode3:PNode3_1
  Actions:
  - Name: action1
    Handler:
      Type: Synapse.Core:Synapse.Core.Runtime.EmptyHandler
      Config:
        Name: ConfigSet01
        InheritFrom: ConfigSet00
        Type: Json
        Values:
          {
            "CNode0": "CValue0_inline_1",
            "CNode3": {
              "CNode3_1": "CValue3_1_inline_1",
            },
          }
    Parameters:
      Name: ParamSet01
      InheritFrom: ParamSet00
      Type: Json
      Values:
        {
          "PNode1": "PValue1_inline_1",
          "PNode3": {
            "PNode3_2": "PValue3_2_inline_1",
          },
        }
```

####Where: Config/json_in.json and Parms/json_in.json contain:
#####Config:
```json
{
  "CNode0": "CValue0_file",
  "CNode1": "CValue1_file",
  "CNode2": {
    "CNode2_1": "CValue2_1_file",
    "CNode2_2": "CValue2_2_file"
  }
}
```

#####Parms:
```json
{
  "PNode0": "PValue0_file",
  "PNode1": "PValue1_file",
  "PNode2": {
    "PNode2_1": "PValue2_1_file",
    "PNode2_2": "PValue2_2_file"
  }
}
```

### action0 Parameter Results
The resulting Config/Parms for action0, before any Dynamic data is applied, is:
#####Config:
```json
{
  "CNode0": "CValue0_inline",
  "CNode1": "CValue1_inline",
  "CNode2": {
    "CNode2_1": "CValue2_1_file",
    "CNode2_2": "CValue2_2_file"
  },
  "CNode3": {
    "CNode3_1": "CValue3_1_inline",
    "CNode3_2": "CValue3_2_inline"
  }
}
```

#####Parms:
```json
{
  "PNode0": "PValue0_inline",
  "PNode1": "PValue1_inline",
  "PNode2": {
    "PNode2_1": "PValue2_1_file",
    "PNode2_2": "PValue2_2_file"
  },
  "PNode3": {
    "PNode3_1": "PValue3_1_inline",
    "PNode3_2": "PValue3_2_inline"
  }
}
```

The resulting Config/Parms for action0, after Dynamic data is applied, is:

```cs
//Key/Value pairs, as collected from an external source:
Dictionary<string, string> dynamicData = new Dictionary<string, string>();
dynamicData.Add( "cnode0Dynamic", "CValue0_dynamic" );
dynamicData.Add( "cnode2_1Dynamic", "CValue2_1_dynamic" );
dynamicData.Add( "cnode3_1Dynamic", "CValue3_1_dynamic" );
dynamicData.Add( "pnode0Dynamic", "PValue0_dynamic" );
dynamicData.Add( "pnode2_1Dynamic", "PValue2_1_dynamic" );
dynamicData.Add( "pnode3_1Dynamic", "PValue3_1_dynamic" );
```

#####Config:
```json
{
  "CNode0": "CValue0_dynamic",
  "CNode1": "CValue1_inline",
  "CNode2": {
    "CNode2_1": "CValue2_1_dynamic",
    "CNode2_2": "CValue2_2_file"
  },
  "CNode3": {
    "CNode3_1": "CValue3_1_dynamic",
    "CNode3_2": "CValue3_2_inline"
  }
}
```

#####Parms:
```json
{
  "PNode0": "PValue0_dynamic",
  "PNode1": "PValue1_inline",
  "PNode2": {
    "PNode2_1": "PValue2_1_dynamic",
    "PNode2_2": "PValue2_2_file"
  },
  "PNode3": {
    "PNode3_1": "PValue3_1_dynamic",
    "PNode3_2": "PValue3_2_inline"
  }
}
```

## action1 Parameter Results
The resulting Config/Parms for action1, which inherits from its config/parms from action0, is:

#####Config:
```json
{
  "PNode0": "PValue0_dynamic",
  "PNode1": "PValue1_inline",
  "PNode2": {
    "PNode2_1": "PValue2_1_dynamic",
    "PNode2_2": "PValue2_2_file"
  },
  "PNode3": {
    "PNode3_1": "PValue3_1_dynamic",
    "PNode3_2": "PValue3_2_inline"
  }
}
```

#####Parms:
```json
{
  "PNode0": "PValue0_inline",
  "PNode1": "PValue1_inline_1",
  "PNode2": {
    "PNode2_1": "PValue2_1_file",
    "PNode2_2": "PValue2_2_file"
  },
  "PNode3": {
    "PNode3_1": "PValue3_1_inline",
    "PNode3_2": "PValue3_2_inline_1"
  }
}
```