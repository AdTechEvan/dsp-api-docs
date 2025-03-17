# External Bidder JavaScript API

## Purpose

This API enables Demand-Side Platforms (DSPs) to register bidding signals with our system in such a way to allow them to participate in private auctions.

## Theory of Operation

At its core, this API encodes bidding signals (name, value) within [interest groups](https://developers.google.com/privacy-sandbox/private-advertising/protected-audience#interest-group-detail), thus allowing DSPs to participate in in-browser auctions. It is designed to function within the constraints of [Protected Audiences API (PAAPI)](https://developers.google.com/privacy-sandbox/private-advertising/protected-audience), particularly regarding origin and frame security contexts. It facilitates two key capabilities that would otherwise be restricted:

1. **Interest Group Inspection in a Worklet**  
   The API allows us to examine existing interest groups from within a worklet and make decisions based on this, such as ensuring no single DSP uses all of the available interest groups.

2. **Delegation on the DSP Origin**  
   The API enables permission delegation on the DSP's origin, which is essential for PAAPI-based auctions. See [FLEDGE.md §13: Permission Delegation](https://github.com/WICG/turtledove/blob/main/FLEDGE.md#13-permission-delegation) for additional context.
	- _(devnote) This is blocked until Chrome fixes [this bug](https://chromium.googlesource.com/chromium/src/+/main/content/browser/interest_group/interest_group_permissions_checker.h#42)._

## Get Started

1. [[Installation]]
2. [[Initial Setup]]
3. [[Registering a Bidding Signal]]
4. _Follow-up,_ [[Utilization during runAdAuction]]

## API Functions

### `init(dspOrigin:string, opts:Optional<InitOpts>): Promise`
- Initialize the external bidder API, with the given `dspOrigin`.
- Bidding signals will not be successfully registered until this function is complete.
- See [[Initial Setup]] for an in-depth look of its functionality.

### `registerBiddingSignal(name:string, value:string): Promise`
- Register a bidding signal by storing it within a managed interest group.
- Calls that occur prior to `init(...)` being called will be queued up and run after initialization completes.
- See [[Registering a Bidding Signal]] for more information.

#### `destroy():void`
- Detach all event listeners.
- Remove iframe.

### Helper functions

#### `supportsInterestGroups(): boolean`
- Determines if the web browser supports interest groups.  
- This will additionally get called during `init()`.

#### `isSignalValid(value:string):boolean`
- Will return true if a given input is valid to being a signal name/value. The current limits are that the value is less than `MAX_CHARS` characters long and comprises `A-Za-z0-9`. This is used during `registerBiddingSignals`.
