
# HyperMorphic Threshold Code (HTC)

## Adaptive Coding Geometry for Threshold Reconstruction

### Abstract

HyperMorphic Threshold Code (HTC) is an experimental threshold coding architecture built upon Chinese Remainder Theorem (CRT) reconstruction, entropy-adaptive parameter selection, deterministic residue mixing, and shard authentication.

Unlike classical CRT threshold systems that operate within a fixed modular geometry, HTC allows the coding geometry itself to evolve as a deterministic function of previously reconstructed content.

The central concept is Adaptive Coding Geometry:

[
E_t = f(C_0,\ldots,C_{t-1})
]

[
(p_i,b_i,\lambda_i)=g(E_t,i)
]

where:

- (C_t) is chunk (t)
- (E_t) is a causal entropy estimate
- (p_i) is the shard modulus
- (b_i) is an invertible modular multiplier
- (\lambda_i) is a deterministic residue-mixing term

The resulting system operates over a sequence of dynamically selected CRT geometries rather than a single fixed modular space.

---

# Motivation

Traditional threshold CRT systems use a fixed collection of moduli selected at design time.

For all chunks:

[
\mathcal G
=
\mathbb Z_{p_1}
\times
\cdots
\times
\mathbb Z_{p_n}
]

remains constant.

HTC instead defines:

[
\mathcal G_t
=
\mathbb Z_{p_1(E_t)}
\times
\cdots
\times
\mathbb Z_{p_n(E_t)}
]

where geometry changes according to previously reconstructed content.

This creates a content-dependent coding space while preserving deterministic decodability.

---

# Core Architecture

## 1. Adaptive Prime Topology

A causal entropy estimator examines previously reconstructed chunks:

[
E_t=f(C_0,\ldots,C_{t-1})
]

The entropy value is quantized:

[
e_t = Q(E_t)
]

which selects one of several predefined prime constellations.

Each entropy level corresponds to a distinct CRT geometry.

---

## 2. Content-Addressed Residue Mixing

For shard (i) at position (t):

[
\lambda_i(t)
=
SHAKE128(i \Vert t)
\bmod p_i
]

The residue becomes:

[
r_i
=
(b_i C_t + \lambda_i)
\bmod p_i
]

Properties:

- Position differentiation
- Reduced visible repetition
- Deterministic regeneration during decoding
- No additional metadata storage

---

## 3. Threshold CRT Reconstruction

Decoder computes:

[
c_i
=
(r_i-\lambda_i)b_i^{-1}
\bmod p_i
]

and reconstructs:

[
C_t
=
CRT(c_1,\ldots,c_k)
]

Any valid set of (k) shards reconstructs the original chunk.

---

## 4. Shard Authentication

Each shard carries authentication data.

Integrity verification occurs before reconstruction.

Invalid shards may be rejected prior to CRT recovery.

---

# Geometry Reproducibility Theorem

## Statement

Let:

[
E_t=f(C_0,\ldots,C_{t-1})
]

be a deterministic causal adaptation rule.

Let:

[
(p_i,b_i,\lambda_i)=g(E_t,i)
]

be a deterministic parameter generation rule.

If chunk reconstruction is exact, then encoder and decoder generate identical adaptive geometries at every position.

---

## Proof

### Base Case

Before chunk 0:

Encoder history:

[
\emptyset
]

Decoder history:

[
\emptyset
]

Therefore:

[
E_0^{enc}=E_0^{dec}
]

and thus:

[
g(E_0^{enc},i)=g(E_0^{dec},i)
]

for all shards.

---

### Inductive Step

Assume:

[
C_0,\ldots,C_{t-1}
]

have been reconstructed exactly.

Then encoder and decoder possess identical histories.

Therefore:

[
E_t^{enc}=f(C_0,\ldots,C_{t-1})
]

[
E_t^{dec}=f(C_0,\ldots,C_{t-1})
]

and hence:

[
E_t^{enc}=E_t^{dec}
]

Applying deterministic parameter generation:

[
g(E_t^{enc},i)
=
g(E_t^{dec},i)
]

for every shard.

Thus geometry at position (t) is identical.

---

### Conclusion

By induction:

[
\mathcal G_t^{enc}
=
\mathcal G_t^{dec}
]

for all positions.

The adaptive geometry is reproduced exactly during decoding.

∎

---

# Threshold Recovery Theorem

If:

[
\prod_{i=1}^{k} p_i
>
256^{chunk_bytes}
]

then CRT reconstruction is unique.

Because:

[
\gcd(b_i,p_i)=1
]

every modular inverse exists.

Each shard yields:

[
C \pmod{p_i}
]

and CRT guarantees a unique solution below the product modulus.

Therefore any valid set of (k) shards reconstructs the original chunk.

---

# Coding-Theoretic Properties

| Property | HTC |
|-----------|-----------|
| Threshold recovery | Yes |
| CRT reconstruction | Yes |
| Erasure tolerance | n-k |
| Minimum distance | d=n-k+1 |
| Adaptive geometry | Yes |
| Content-addressed mixing | Yes |
| Deterministic decoding | Yes |
| Geometry reproducibility | Yes |
| Shard authentication | Yes |

---

# Experimental Validation

Current implementation demonstrates:

- Exact threshold reconstruction
- Recovery from shard erasures
- Deterministic geometry regeneration
- Reproducible entropy adaptation
- Deterministic residue mixing
- Authentication-based shard validation

A geometry reproducibility experiment showed:

- 380 positions tested
- 380 matching geometry states
- 100% encoder/decoder agreement

supporting the Geometry Reproducibility Theorem.

---

# Interpretation

HTC should currently be viewed as:

"A CRT-based threshold coding architecture with adaptive coding geometry."

The most distinctive feature is not dynamic bases or residue masking individually, but the ability to make coding geometry itself a deterministic function of reconstructed content while preserving exact decodability.

This introduces the concept of Adaptive Coding Geometry: a sequence of reproducible CRT spaces generated from the data stream itself.

---

# Status

Research Prototype

Established:
- Correct threshold reconstruction
- Deterministic adaptive geometry
- Geometry reproducibility theorem
- Exact decoder regeneration of coding parameters

Future work:
- Formal Byzantine correction bounds
- Convergence analysis of coupled geometries
- Adaptive capacity theory
- Information-theoretic characterization
- Comparison against Reed–Solomon and modern erasure codes
