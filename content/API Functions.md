
## `init(dspOrigin:string, opts:Optional<InitOpts>): Promise`

- Initialize the external bidder API, with the given `dspOrigin`.
- Bidding signals will not be successfully registered until this function is complete.
- See [[2. Initialize]] for an in-depth look of its functionality.

## `registerBiddingSignal(name:string, value:string): Promise`

- Register a bidding signal by storing it within a managed interest group.
- Calls that occur prior to `init(...)` being called will be queued up and run after initialization completes.
- See [[3. Registering a Bidding Signal]] for more information.

## `destroy():void`

- Detach all event listeners.
- Remove iframe.

## Helper functions

### `supportsInterestGroups(): boolean`

- Determines if the web browser supports interest groups.  
- This will additionally get called during `init()`.

### `isSignalValid(value:string):boolean`

- Will return true if a given input is valid to being a signal name/value. The current limits are that the value is less than `MAX_CHARS` characters long and comprises `A-Za-z0-9`. This is used during `registerBiddingSignals`.
