# QVI Credential Flow Diagram

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                        QVI Technical Review Process                         │
│                      Qualified vLEI Issuer Credentials                      │
└─────────────────────────────────────────────────────────────────────────────┘

┌──────────────┐
│    GLEIF     │  (Global Legal Entity Identifier Foundation)
│  (Root of    │
│   Trust)     │
└──────┬───────┘
       │
       │ 1. Issues QVI Credential
       │    (Schema: EBfdlu8R27Fbx-ehrqwImnK-8Cm79sqbAQ4MmvEAYqao)
       │
       ▼
┌──────────────────────────────────────────────────────────────┐
│             Veridian Wallet (as QVI Holder)                  │
│  ┌────────────────────────────────────────────────────────┐  │
│  │  QVI Credential Storage                                │  │
│  │  • Schema: Qualified vLEI Issuer Credential           │  │
│  │  • Status: Confirmed                                   │  │
│  │  • LEI: 5493001KJTIIGC8Y1R17                          │  │
│  │  • Authority: GLEIF                                    │  │
│  └────────────────────────────────────────────────────────┘  │
│                                                               │
│  ┌────────────────────────────────────────────────────────┐  │
│  │  QVI Operations (Credential Server)                    │  │
│  │  • Issue LE Credentials                                │  │
│  │  • Revoke Credentials                                  │  │
│  │  • Verify Credential Chains                           │  │
│  │  • Manage vLEI Registry                               │  │
│  └────────────────────────────────────────────────────────┘  │
└────────┬─────────────────────────────────────┬───────────────┘
         │                                     │
         │ 2. Issues LE Credential             │ 3. Revokes if needed
         │    (IPEX Grant)                     │    (Update Registry)
         │                                     │
         ▼                                     ▼
┌─────────────────────┐              ┌─────────────────────┐
│  Legal Entity       │              │  KERI Registry      │
│  Veridian Wallet    │              │  (Cardano Backed)   │
│                     │              │                     │
│  LE Credential:     │              │  • Status: Active   │
│  • Schema: ENP...   │◄─────────────┤  • Status: Revoked  │
│  • LEI: ...         │  Verify      │  • Timestamp        │
│  • QVI Edge: ref    │              │  • Block Height     │
└─────────────────────┘              └─────────────────────┘


═══════════════════════════════════════════════════════════════════════════════
                            PROTOCOL FLOW (IPEX)
═══════════════════════════════════════════════════════════════════════════════

Step 1: QVI Credential Reception (GLEIF → Veridian Wallet)
─────────────────────────────────────────────────────────────
┌─────────┐                                      ┌──────────────┐
│  GLEIF  │──────── IPEX Grant ────────────────►│   Veridian   │
│ Issuer  │                                      │   Wallet     │
└─────────┘                                      └──────────────┘
    │                                                    │
    │ 1a. Create QVI Credential                         │
    │     Schema: EBfdlu8R27Fbx...                      │
    │     Attribute: LEI                                │
    │                                                    │
    │ 1b. Sign with GLEIF AID                           │
    │                                                    │
    │ 1c. Register in vLEI Registry                     │
    │                                                    │
    └────────────────────────────────────────────────────┘
                                                         │
                                                         │ 1d. Admit Credential
                                                         │
                                                         ▼
                                              ┌──────────────────┐
                                              │  Local Storage   │
                                              │  • QVI Metadata  │
                                              │  • Status: Conf. │
                                              └──────────────────┘


Step 2: LE Credential Issuance (QVI Wallet → Legal Entity Wallet)
──────────────────────────────────────────────────────────────────
┌──────────────┐                                  ┌──────────────┐
│   Veridian   │──────── IPEX Grant ────────────►│ Legal Entity │
│  (QVI Role)  │                                  │   Wallet     │
└──────────────┘                                  └──────────────┘
    │                                                     │
    │ 2a. Create LE Credential                           │
    │     Schema: ENPXp1vQzRF6...                        │
    │     Attribute: LEI, legalName                      │
    │     Edge: QVI Credential Reference                 │
    │                                                     │
    │ 2b. Add Disclaimers                                │
    │     • Usage Disclaimer                             │
    │     • Issuance Disclaimer                          │
    │                                                     │
    │ 2c. Sign with QVI AID                              │
    │                                                     │
    │ 2d. Register in vLEI Registry                      │
    │                                                     │
    └─────────────────────────────────────────────────────┘
                                                          │
                                                          │ 2e. Admit Credential
                                                          │
                                                          ▼
                                               ┌──────────────────┐
                                               │  Local Storage   │
                                               │  • LE Metadata   │
                                               │  • QVI Chain     │
                                               └──────────────────┘


Step 3: Credential Verification (Any Verifier → Legal Entity Wallet)
─────────────────────────────────────────────────────────────────────
┌──────────────┐                                  ┌──────────────┐
│   Verifier   │──────── IPEX Apply ─────────────►│ Legal Entity │
│              │                                   │   Wallet     │
└──────────────┘                                   └──────────────┘
    ▲                                                     │
    │                                                     │ 3a. Check Request
    │                                                     │
    │ 3d. Verify Chain:                                  │ 3b. User Approval
    │     • LE Credential                                │
    │     • QVI Credential                               │ 3c. IPEX Offer
    │     • GLEIF Root                                   │     with Attachment
    │                                                     │
    └─────────── IPEX Offer with Anc ───────────────────┘
                                                          │
                                                          │ 3e. Verify Status
                                                          │
                                                          ▼
                                               ┌──────────────────┐
                                               │  vLEI Registry   │
                                               │  • Status Query  │
                                               │  • Not Revoked   │
                                               └──────────────────┘


═══════════════════════════════════════════════════════════════════════════════
                         DATA STRUCTURES (ACDC)
═══════════════════════════════════════════════════════════════════════════════

QVI Credential Structure:
──────────────────────────
{
  "v": "ACDC10JSON00...",    // Version
  "d": "EBfdlu8R27F...",     // SAID (Self-Addressing Identifier)
  "i": "GLEIF_AID",          // Issuer AID
  "s": "EBfdlu8R27F...",     // Schema SAID
  "a": {                     // Attributes
    "i": "QVI_HOLDER_AID",
    "LEI": "5493001KJTIIGC8Y1R17"
  },
  "ri": "vLEI_REGISTRY",     // Registry Identifier
  "dt": "2026-02-06T..."     // Date/Time
}

Legal Entity Credential Structure:
───────────────────────────────────
{
  "v": "ACDC10JSON00...",
  "d": "ENPXp1vQzRF...",
  "i": "QVI_AID",            // Issuer is QVI
  "s": "ENPXp1vQzRF...",     // LE Schema SAID
  "a": {                     // Attributes
    "i": "LE_HOLDER_AID",
    "LEI": "254900...",
    "legalName": "Example Corp"
  },
  "e": {                     // Edges (Chained Credentials)
    "qvi": {
      "n": "EBfdlu8R27F...", // QVI Credential SAID
      "s": "EBfdlu8R27F..."  // QVI Schema SAID
    }
  },
  "r": {                     // Rules (Disclaimers)
    "usageDisclaimer": {...},
    "issuanceDisclaimer": {...}
  },
  "ri": "vLEI_REGISTRY",
  "dt": "2026-02-06T..."
}


═══════════════════════════════════════════════════════════════════════════════
                           SECURITY FEATURES
═══════════════════════════════════════════════════════════════════════════════

1. Cryptographic Security
   ├─ Secure Enclave/TEE for key storage
   ├─ KERI Key Event Logs (immutable)
   ├─ Multi-signature support
   └─ Hardware-backed biometrics

2. Network Security
   ├─ TLS/SSL encryption
   ├─ CESR encoding (compact & efficient)
   └─ OOBI for secure introductions

3. Registry Security
   ├─ Cardano blockchain backing
   ├─ KERI witnesses
   ├─ Cryptographic timestamps
   └─ Revocation status tracking

4. Application Security
   ├─ SQLCipher (data at rest)
   ├─ RASP protection
   ├─ Privacy screen
   └─ Tap jacking protection


═══════════════════════════════════════════════════════════════════════════════
                         VERIFICATION PROCESS
═══════════════════════════════════════════════════════════════════════════════

Credential Chain Verification:
──────────────────────────────

1. Verify LE Credential
   ├─ Check signature against LE schema
   ├─ Verify SAID matches content
   ├─ Check expiration (if applicable)
   └─ Query registry for revocation status

2. Verify QVI Credential (via edge)
   ├─ Resolve QVI credential SAID
   ├─ Check signature against QVI schema
   ├─ Verify issuer is GLEIF
   └─ Query registry for QVI status

3. Verify GLEIF Authority
   ├─ Check GLEIF's root credential
   ├─ Verify governance framework compliance
   └─ Validate authority chain

4. Final Decision
   └─ Accept if all verifications pass
     └─ Reject if any verification fails


═══════════════════════════════════════════════════════════════════════════════
                         IMPLEMENTATION CHECKLIST
═══════════════════════════════════════════════════════════════════════════════

✓ QVI Credential Reception
✓ LE Credential Issuance
✓ Credential Revocation
✓ Status Verification
✓ Chain Validation
✓ IPEX Grant/Admit
✓ IPEX Apply/Offer
✓ Multi-sig Support
✓ Registry Integration
✓ Disclaimer Management
✓ Security Audited
✓ Production Ready

═══════════════════════════════════════════════════════════════════════════════

For detailed documentation, see:
• docs/QVI-Technical-Review.md
• docs/QVI-Compliance-Checklist.md
• https://docs.veridian.id/
```
