# Security and Safety

GPU reverse engineering can affect device stability, data confidentiality, and recovery. Initial experiments must be read-only or reversible. Do not flash firmware, write vendor partitions, replay opaque command buffers, change persistent security policy, or issue unreviewed register writes.

If an artifact may contain credentials, private keys, serial numbers, memory contents, or security-sensitive paths, keep it out of Git. Report security-sensitive findings privately to the project owner before publishing details.
