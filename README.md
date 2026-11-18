# Staging dual-output publication validation

This branch is an isolated pre-cutover output target in `advisories-test`.
It contains staging assessments and staging trust. It is not a production feed.
The existing post-cutover staging output stays on this repository's `main` branch.

The initial tree has an empty package index and no managed `dhi/osv`, `dhi/vex`
or `pkg` content. The publisher adds those surfaces only for advisories admitted
by new qualifying source events after the configured cutoff. No historical
catalog is copied into the branch.

## Signature verification

The staging Cosign key is pinned to:

`gcpkms://projects/atomist-internal-staging/locations/us-east1/keyRings/dhi-advisory-signing/cryptoKeys/dhi-advisory-signing/cryptoKeyVersions/1`

`trust/staging-dhi-cosign.pub` is its public key. Its SHA-256 SubjectPublicKeyInfo
DER fingerprint is `d8c1a204e9548eeeb03528faf5358526e0cd94ac07c9531cb370ef6aabd2bd48`.
Record the approved bootstrap commit as the trust anchor before publication.

For a package bucket or index artifact and its Sigstore bundle, use Cosign v3.0.2:

```sh
cosign verify-blob --key trust/staging-dhi-cosign.pub \
  --bundle index.json.sig index.json
```

OSV/VEX artifacts also retain the existing staging OpenPGP/checksum contract.
`trust/staging-advisory-gpg.pub.asc` is the staging publisher's public GPG key.
Import it into a disposable GPG home, verify each `.asc` against its exact
artifact/checksum bytes, and compare checksum values to the published artifact.
Do not treat the production DHI key as this branch's trust anchor.
