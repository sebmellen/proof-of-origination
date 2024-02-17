# Proof of Origination for Research Projects

## Motivation

After a close friend was made third author on a research project which they initiated, funded, collected data for, and wrote the majority of, I realized the need for an open-source and widely-accepted standard for "Proof of Origination" in the research community. This project aims to define an open schema for proving that you are the author or originator of a given work, without needing to publicize the existence of the proof.

Each proof is made up of a publicly-auditable timestamp, a proof of identity, and a description of the works being claimed. Ideally, the proof model will allow for easy integration with research tooling such as Electronic Lab Notebooks (ELNs), allowing for iterative, automated proofs about a given body of work.

I am open to any feedback, comments, or assistance with deploying this standard!

## Goals

The "Proof of Origination for Research Projects" initiative is driven by a set of core objectives designed to address the challenges faced by authors and researchers in establishing and maintaining control over their intellectual contributions. The overarching goal is to prevent research theft and ensure that all contributors can assert their rights and roles in relation to their discoveries and work. To achieve this, the standard is built around several key principles:

- **Widely Integrated and Pragmatic Standard**: Aimed at becoming a universally accepted protocol, this standard is designed for seamless integration across various research tools, platforms, and repositories. Its pragmatic approach ensures adaptability to different disciplines and research methodologies.
- **Flexibility**: Recognizing the diverse nature of research outputs and contributions, the schema is flexible, accommodating various types of work, from traditional papers to datasets and software.
- **Ease of Traversal and UI Rendering**: With the intention of being user-friendly, the standard supports straightforward navigation and rendering in user interfaces, allowing researchers, reviewers, and the public to easily understand and verify claims of origination.
- **Simplicity in Creation**: The process of creating a proof of origination is streamlined to encourage widespread adoption among researchers, requiring minimal technical expertise.
- **Format Agnostic**: To ensure inclusivity and broad applicability, the schema is designed to be agnostic to the underlying format of the research output, supporting everything from textual documents to multimedia files and codebases.
- **Versioned for Easy Recall**: Acknowledging the iterative nature of research, the standard incorporates versioning, enabling authors to update their proofs of origination as their work evolves, while maintaining a clear record of all changes.

The ultimate aim of this initiative is to empower authors and researchers, ensuring they have full control over their discoveries and receive appropriate recognition for their contributions. By establishing a reliable, transparent system for proving origination, we seek to protect the integrity of the research process and foster a culture of respect and fairness within the academic and scientific communities.


## Structure

The "Proof of Origination" schema is composed of three core components which are detailed below.

### Works Claimed

This section details the work being claimed, encapsulating the title, description, author(s), and associated files or links. A unique nonce is included to prevent external verification or reconstruction by unauthorized parties, ensuring the claim's integrity.

- **Nonce**: A unique identifier that ensures the privacy of the hashed data by guarding against enumeration.
- **Title**: The title of the work.
- **Description**: A brief overview of the work.
- **Authors**: A list of individuals who contributed to the work.
- **Files**: Documents or datasets associated with the work, each accompanied by a hash to verify its integrity.
- **Links**: URLs to access the work or related resources.

### Proof of Identity

To verify the claimant's identity, this section encompasses methods of proof, including digital signatures and links to externally published proofs, such as social media declarations.

- **ProofOfIdentityData**: An array of identity proofs, each specifying the verification method, identifier, message, and an optional link to external proof.
- **ProofOfIdentityHash**: A cryptographic hash of the entire `ProofOfIdentityData`, serving as an immutable summary of the claimant's identity proofs.

### Publicly-Auditable Timestamp

A blockchain-based timestamp certifies the moment the claim was made, linking it to the work and identity proofs through cryptographic hashes.

- **TimestampType**: The method used for timestamping, with blockchain as the default.
- **BlockchainIdentifier**: Details of the blockchain used, including the network name and transaction reference URLs.
- **TransactionId** & **BlockNumber**: Identifiers for the blockchain transaction.
- **WorksClaimedHashReference** & **ProofOfIdentityHashReference**: Hash references to the claimed work and identity proofs, ensuring their verifiability.
- **FinalProof**: A combined hash of the `WorksClaimedHashReference` and `ProofOfIdentityHashReference`, offering a single point of verification for the entire claim.

## Schema

```json
{
  "$schema": "http://json-schema.org/draft-07/schema#",
  "title": "Proof of Origination for Research Projects",
  "type": "object",
  "properties": {
    "worksClaimed": {
      "type": "object",
      "properties": {
        "worksClaimedData": {
          "type": "object",
          "properties": {
            "nonce": { "type": "string" },
            "title": { "type": "string" },
            "description": { "type": "string" },
            "authors": {
              "type": "array",
              "items": { "type": "string" }
            },
            "files": {
              "type": "array",
              "items": {
                "type": "object",
                "properties": {
                  "fileName": { "type": "string" },
                  "fileHash": { "type": "string" },
                  "hashAlgorithm": { "type": "string" }
                },
                "required": ["fileName", "fileHash", "hashAlgorithm"]
              }
            },
            "links": {
              "type": "array",
              "items": { "type": "string" }
            }
          },
          "required": ["nonce", "title", "description", "authors", "files"]
        },
        "worksClaimedHash": {
          "type": "object",
          "properties": {
            "hashValue": { "type": "string" },
            "hashAlgorithm": { "type": "string" }
          },
          "required": ["hashValue", "hashAlgorithm"]
        }
      },
      "required": ["worksClaimedData", "worksClaimedHash"]
    },
    "proofOfIdentity": {
      "type": "object",
      "properties": {
        "proofOfIdentityData": {
          "type": "array",
          "items": {
            "type": "object",
            "properties": {
              "method": { "type": "string" },
              "identifier": { "type": "string" },
              "signature": { "type": "string" },
              "message": { "type": "string" },
              "externalProofLink": { "type": "string" }
            },
            "required": ["method", "identifier", "message"]
          }
        },
        "proofOfIdentityHash": {
          "type": "object",
          "properties": {
            "hashValue": { "type": "string" },
            "hashAlgorithm": { "type": "string" }
          },
          "required": ["hashValue", "hashAlgorithm"]
        }
      },
      "required": ["proofOfIdentityData", "proofOfIdentityHash"]
    },
    "publiclyAuditableTimestamp": {
      "type": "object",
      "properties": {
        "timestampType": { "type": "string", "default": "blockchain" },
        "blockchainIdentifier": {
          "type": "object",
          "properties": {
            "name": { "type": "string" },
            "network": { "type": "string" },
            "referenceURLs": { "type": "array", "items": { "type": "string" } }
          },
          "required": ["name", "network", "referenceURLs"]
        },
        "transactionId": { "type": "string" },
        "blockNumber": { "type": "integer" },
        "timestamp": { "type": "string", "format": "date-time" },
        "currencySymbol": { "type": "string" },
        "worksClaimedHashReference": { "type": "string" },
        "proofOfIdentityHashReference": { "type": "string" },
        "finalProof": { "type": "string" },
        "retrievalExplanation": {
          "type": "string",
          "default": "The final proof is generated by hashing the combined worksClaimedHashReference and proofOfIdentityHashReference, ensuring a unique and secure link between the work claimed and the identity proofs at the time of timestamping."
        }
      },
      "required": ["timestampType", "blockchainIdentifier", "transactionId", "blockNumber", "timestamp", "worksClaimedHashReference", "proofOfIdentityHashReference", "finalProof"]
    }
  },
  "required": ["worksClaimed", "proofOfIdentity", "publiclyAuditableTimestamp"]
}
```

## Example

```json
{
  "worksClaimed": {
    "worksClaimedData": {
      "nonce": "uniqueNonceValue12345",
      "title": "A Study on the Impact of Urban Green Spaces on Air Quality",
      "description": "This research explores the relationship between urban green spaces and their effect on air quality, focusing on particulate matter reduction.",
      "authors": ["Dr. Alex Rivera", "Prof. Jordan Lee", "Dr. Samantha Diaz"],
      "files": [
        {
          "fileName": "ResearchPaper.pdf",
          "fileHash": "b1a2b3c4d5e6f7g8h9i0j1k2l3m4n5o6p7q8r9s0t1u2v3w4x5y6z7a8b9c0d1e2f3g4h5i6",
          "hashAlgorithm": "SHA-512"
        },
        {
          "fileName": "DataSet.csv",
          "fileHash": "c1d2e3f4g5h6i7j8k9l0m1n2o3p4q5r6s7t8u9v0w1x2y3z4a5b6c7d8e9f0g1h2i3j4k5l6",
          "hashAlgorithm": "SHA-512"
        }
      ],
      "links": ["http://www.example.org/greenspaces-airquality"]
    },
    "worksClaimedHash": {
      "hashValue": "d1e2f3g4h5i6j7k8l9m0n1o2p3q4r5s6t7u8v9w0x1y2z3a4b5c6d7e8f9g0h1i2j3k4l5m6",
      "hashAlgorithm": "SHA-512"
    }
  },
  "proofOfIdentity": {
    "proofOfIdentityData": [
      {
        "method": "Social Media Verification",
        "identifier": "@DrAlexRivera",
        "signature": "N/A",
        "message": "I hereby declare that I am an author of the work titled 'A Study on the Impact of Urban Green Spaces on Air Quality'.",
        "externalProofLink": "https://twitter.com/DrAlexRivera/status/1234567890123456789"
      }
    ],
    "proofOfIdentityHash": {
      "hashValue": "e1f2g3h4i5j6k7l8m9n0o1p2q3r4s5t6u7v8w9x0y1z2a3b4c5d6e7f8g9h0i1j2k3l4m5n6",
      "hashAlgorithm": "SHA-512"
    }
  },
  "publiclyAuditableTimestamp": {
    "timestampType": "blockchain",
    "blockchainIdentifier": {
      "name": "Ethereum",
      "network": "mainnet",
      "referenceURLs": [
        "https://etherscan.io/tx/0x4e306b5d73960ac6e9ae3b7f9192120db56a4e8e7a10f5e9b3b2b4e3d8f4ed6f"
      ]
    },
    "transactionId": "0x4e306b5d73960ac6e9ae3b7f9192120db56a4e8e7a10f5e9b3b2b4e3d8f4ed6f",
    "blockNumber": 1234567,
    "timestamp": "2024-02-17T12:00:00Z",
    "currencySymbol": "ETH",
    "worksClaimedHashReference": "d1e2f3g4h5i6j7k8l9m0n1o2p3q4r5s6t7u8v9w0x1y2z3a4b5c6d7e8f9g0h1i2j3k4l5m6",
    "proofOfIdentityHashReference": "e1f2g3h4i5j6k7l8m9n0o1p2q3r4s5t6u7v8w9x0y1z2a3b4c5d6e7f8g9h0i1j2k3l4m5n6",
    "finalProof": "f1g2h3i4j5k6l7m8n9o0p1q2r3s4t5u6v7w8x9y0z1a2b3c4d5e6f7g8h9i0j1k2l3m4n5o6",
    "retrievalExplanation": "The final proof is generated by hashing the combined worksClaimedHashReference and proofOfIdentityHashReference, ensuring a unique and secure link between the work claimed and the identity proofs at the time of timestamping."
  }
}
```

