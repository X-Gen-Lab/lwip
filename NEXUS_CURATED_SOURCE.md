# Nexus curated lwIP source

Upstream: https://github.com/lwip-tcpip/lwip
Version: lwIP 2.2.1
Base commit: 77dcd25a72509eb83f72b033d219b1d40cd8eb95
Base tree: 260547a417890e813eaac769f4fcbdaec75686b2
License: BSD-3-Clause; the upstream COPYING and source notices are retained.

## Local delta

The only upstream C change extends one preprocessor condition in
`src/core/init.c`. It exempts the Nexus custom RX profile from the stock
`TCP_WND` versus `PBUF_POOL` capacity heuristic only when both
`NEXUS_LWIP_RX_CUSTOM_PBUFS=1` and `LWIP_SUPPORT_CUSTOM_PBUF=1` are selected.
Every other memory and TCP sanity check remains active. The default profile
retains the original check, source line count, algorithms and runtime behavior.

The selected Nexus adapter receives payload through finite caller-owned MAC
loans and custom pbufs instead of the stock pbuf pool. It copies a writable
136-byte protocol-header prefix and retains read-only payload tails until the
last reference is freed and the serialized owner returns the exact MAC token.
Its two spare 512-byte pool blocks are for explicit raw-API callers; exhausting
that pool does not allocate from another pool or increase MAC capacity. The
16 KiB RAM arena, TCP window and all other pool settings remain unchanged.

This exemption is a port-specific configuration-validation change, not proof
of sustained TCP throughput, physical board operation or arbitrary RX credit.
The product must budget its MAC descriptors, simultaneous loans and caller
slots for its real traffic and backpressure. All other lwIP source bytes are
unmodified. Future upstream updates must review this single condition again.

The Nexus source lock records the resulting organization commit, tree and
reviewed file closure. A local proposal or this notice alone does not grant
production admission or software qualification.
