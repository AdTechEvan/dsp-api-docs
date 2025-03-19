# Signature

```typescript
ebapi.registerBiddingSignal(name:string, value:string): Promise
```

- **Arguments**
 	- `name` (string) - The signal name.
 	- `value` (string) - The signal value.
- **Returns**
 	- A `Promise` that resolves once the operation is complete.

# Example

```typescript

import ebapi from 'ebapi';

ebapi.init("https://my-dsp.example");

// ...

ebapi.registerBiddingSignal("MY_SIGNAL", "124849560").then(result => {
 console.log("Bidding signal registered?", result);
})
```

# Origin hand-off during `registerBiddingSignals`

| Step | Executed by       | Operation                                          | Running in Origin |
| ---- | ----------------- | -------------------------------------------------- | ----------------- |
| 1    | Advertiser or DSP | Call `registerBiddingSignals(...)`                 | Advertiser        |
| 2    | `api.js`          | Send event to `dsp-iframe`                         | Advertiser        |
| 3    | `dsp-iframe`      | Forward event to `entry-iframe`                    | DSP               |
| 4    | `entry-iframe`    | Perform a `selectURL` using **JoinIG** worklet     | ImpBus            |
| 5    | `JoinIG`          | Count DSP interest groups                          | ImpBus            |
| 6    | `JoinIG`          | Return `accept-frame` if the DSP is below limit    | ImpBus            |
| 7    | `JoinIG`          | Return `reject-frame` if the DSP has reached limit | ImpBus            |
| 8    | `entry-iframe`    | Render returned frame                              | ImpBus            |

# Sequence Diagram

```mermaid

sequenceDiagram
    participant Adv as Advertiser<br/>Website
    participant D_IF as dsp-iframe<br/>(DSP origin)
    participant E_IF as entry-iframe<br/>(ImpBus origin)
    participant JoinIG as JoinIG Worklet<br/>(ImpBus origin)
    
    Adv->>D_IF: Send event
    D_IF->>E_IF: Forward event

    E_IF->>JoinIG: Execute `selectURL`<br/>(Shared Storage API)
    JoinIG->>JoinIG: Count Interest Groups<br/>for DSP in IB origin
    alt count < MAX_DSP
        JoinIG->>E_IF: Return accept_iframe URL
    else count >= MAX_DSP
        JoinIG->>E_IF: Return reject_iframe URL
    end
    E_IF->>E_IF: Load returned iframe
```

## If `accept_url` is loaded

```mermaid

sequenceDiagram
    participant A_IF as accept-iframe<br/>(ImpBus origin)
    participant Browser
    participant IB as ImpBus

 A_IF->>Browser: Call joinAdInterestGroup<br/>(Privacy Sandbox)
 Browser->>IB: Permissions delegation request<br/>(Can DSP joinAdInterestGroup?)
 IB->>Browser: Approve or deny
 Browser->>Browser: If approved,<br/>execute joinAdInterestGroup
 A_IF->>A_IF: Notify api.js via<br/>window.top.postMessage(...)<br />that the join was successful<br/>
```

## If `reject_url` is loaded

```mermaid

sequenceDiagram
    participant A_IF as reject-iframe<br/>(ImpBus origin)
 A_IF->>A_IF: Notify api.js via<br/>window.top.postMessage(...)<br />that the join was rejected<br/>
```
