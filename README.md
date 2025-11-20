<h1 align="center">Kube Coordinate</h1>
<div align="center">

[![](https://img.shields.io/crates/v/kube-coordinate.svg?color=brightgreen&style=flat-square)](https://crates.io/crates/kube-coordinate)
[![docs.rs](https://docs.rs/kube-coordinate/badge.svg)](https://docs.rs/kube-coordinate)
[![Build Status](https://github.com/thedodd/kube-coordinate/actions/workflows/ci.yaml/badge.svg?branch=main)](https://github.com/thedodd/kube-coordinate/actions)
![](https://img.shields.io/badge/license-MIT%2FApache--2.0-blue?style=flat-square)
![](https://img.shields.io/crates/d/kube-coordinate?label=downloads%20%28crates.io%29&style=flat-square)

  <strong>
    Kubernetes leader election using the coordination.k8s.io API.
  </strong>
</div>

---

Kube Coordinate builds upon the [kube-rs](https://kube.rs/) ecosystem, and implements a streamlined version of the client-go leader election system. This implementation uses only `Lease` objects for coordination.

```rust
// Spawn a leader elector task, and get a handle to the state channel.
let handle = LeaderElector::spawn(/* ... */);
let state_chan = handle.state();

// Before taking action as a leader, just check the channel to ensure
// the lease is currently held by this process.
if state_chan.borrow().is_leader() {
    // Only perform leader actions if in leader state.
}

// Or, for a more sophisticated pattern, watch the state channel for changes,
// and use it to drive your application's state machine.
let state_stream = tokio_stream::wrappers::WatchStream::new(state_chan);
loop {
    tokio::select! {
        Some(state) = state_stream.next() => match state {
            LeaderState::Leader => (), // Leader tasks.
            _ => (), // Non-leader tasks.
        },
    }
}
```
## Install
```
cargo add kube-coordinate
```

## Versioning
This crate follows the semver major version of the `kube` crate.

### k8s-openapi features
In order to ensure that this create remains future proof, and compatible with future versions of K8s, this crate specifies a default feature `k8s-latest` which ensures that `k8s-openapi/latest` feature is enabled. Simply disable default features for this create, and specify an override for the feature as `kube-coordinate/k8s-openapi/latest`.

### License
kube-coordinate is licensed under the terms of the MIT License or the Apache License 2.0, at your choosing.
