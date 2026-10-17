# EPUB sandbox validation checkpoint (2026-10-16)

## Why this sandbox exists

The EPUB sandbox is being developed to inspect untrusted ebook files **before** they are admitted to a self-hosted library. EPUBs are ZIP-based containers with metadata and XML, so a file that looks like an ordinary book may contain malformed archive structures, unsafe paths, decompression bombs, or malicious parser inputs. The goal is to create a controlled inspection boundary that can reject suspicious content without granting it access to library services or other homelab workloads.

The design intentionally separates inspection from import. The scanner receives constrained file descriptors, runs with reduced privileges and systemd isolation, and does not approve, download, transfer, or import files. This limits the consequences of processing untrusted input and provides a testable path toward a future quarantine-and-approval workflow. Passing current regression tests does **not** mean the system is ready to trust arbitrary files.

**Decision rationale:** inspect before import, keep the scanner isolated from the library, and retain disabled production actions until adversarial testing and explicit integration review are complete.

## Scope
Documentation-only checkpoint for an isolated, non-production EPUB inspection sandbox. This records operator-reported test output; it is not an independent audit of the host or source code.

## Reported V3 regression results
- V3 fingerprint verification: PASS.
- Missing EPUB container: rejected with `missing_container`.
- Duplicate ZIP entry: rejected with `duplicate_archive_entries`.
- Unsafe parent traversal: rejected with `unsafe_archive_path`.
- Excessive compression: rejected with `excessive_compression`.
- Invalid EPUB mimetype: rejected with `invalid_mimetype`.
- Total: 5/5 expected rejections; caller offsets preserved for all five.
- Tests used sealed descriptors; no filesystem EPUB test files created.
- V1/V2/V3 inspector files, original scanner, worker, runner, and retained evidence snapshots reported unchanged.
- Production unchanged; downloads, imports, approvals, and transfers disabled.

## Security design decisions and rationale
- Keep testing descriptor-based and isolated: reduces filesystem artifact exposure and avoids mixing test data with production.
- Preserve previous inspector versions and evidence: maintains reproducibility and forensic comparison.
- Keep production actions disabled until further validation: passing five negative cases does not prove comprehensive parser safety.
- Use systemd cgroup lifecycle controls for descendant cleanup rather than relying solely on process groups: a child may create a new session and escape process-group-only termination.

## Next validation work (not yet passed)
1. Local ZIP headers versus central directory disagreements.
2. Truncation and inconsistent/overlapping entry ranges.
3. Unsupported encrypted ZIP entries.
4. Malformed EPUB container XML and unsafe XML entity behavior.
5. Resource-limit boundary conditions.

## Change boundary
This commit updates GitHub documentation only. It does not modify or deploy sandbox code, systemd units, production services, retained evidence, or host configuration.
