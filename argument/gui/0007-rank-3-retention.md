# Argument-0007: Retention at Rank III

|                 |                                                                                             |
| --------------- | ------------------------------------------------------------------------------------------- |
| **Report Date** | Date of submission (2026/08/24)                                                             |
| **Submitted by**| Guillaume Thiolliere                                                                        |


## Member details

- Matrix username: @guillaume:parity.io
- Polkadot address: 13psJuWEjBuZGaFqXvFnLMC6ME8RVVfQAhtFhydYjW45oKgZ
- Current rank: III
- Date of initial induction: 2025/01/09
- Date of last report: 2026/02/03
- Link to last report: https://github.com/polkadot-fellows/Evaluations/pull/254
- Area(s) of Expertise/Interest:
  - polkadot business-logic (aka the 'runtime')
  - the internals of the frame pallet framework
  - runtime and host APIs


## Reporting period

- Start date: 2026/02/03
- End date: 2026/08/24


## Argument

During this period I continued to operate as a core implementer of the Proof-of-Personhood
initiative (Individuality) and to maintain and extend major FRAME protocol components in
the Polkadot SDK. Over the reporting period I authored **over 100 pull requests** and
reviewed **more than 200** across `paritytech/polkadot-sdk`, `polkadot-fellows/runtimes`,
`paritytech/individuality` and `paritytech/individuality-community`.

*Note: most Individuality development happened in `paritytech/individuality`, which is
currently private; the code is published in the public
[individuality-community](https://github.com/paritytech/individuality-community) repository,
and I link to the public repository whenever possible.*

### 1. Primary implementation of major protocol components
*Manifesto: "a primary role in the implementation of a major protocol component."*

- **Transaction Extensions — completion of the overhaul.** I finalized the multi-version
  transaction extension support: [polkadot-sdk#7035](https://github.com/paritytech/polkadot-sdk/pull/7035)
  (merged 2026/03) allows an `UncheckedExtrinsic` to carry multiple extension pipelines
  per runtime, enabling runtimes to evolve their extension line without breaking existing
  signers. I then deployed [`AuthorizeCall` to all fellowship runtimes](https://github.com/polkadot-fellows/runtimes/pull/1143),
  and reviewed the SDK-wide [deprecation of `ValidateUnsigned`](https://github.com/paritytech/polkadot-sdk/pull/10150)
  in favor of the `pallet::authorize` API I designed.

- **Coinage** — the private fungible-token system of the People Chain. I have been the
  primary implementer across the whole period: congestion-based pricing and distribution
  of unload tokens, exponential lock time on failed proofs, pay-using-output flows,
  archiving of old recyclers, direct offboarding, and finally making the system
  **permissionless and multi-asset** using pools instead of governance-set rates
  (public code: [pallets/coinage](https://github.com/paritytech/individuality-community/tree/main/pallets)).
  I also wrote its benchmarks and the formula-based weight model for the coin lifecycle.

- **pallet-game / airdrop.** I integrated the airdrop into the game lifecycle, hardened
  all offchain-worker-driven operations against transaction-pool bans and collisions,
  switched randomness to fresh relay-chain randomness, and implemented the daily dollar
  prize distribution.

- **Fellowship runtimes.** I moved the [Polkadot People Chain to 2s blocks with elastic
  scaling](https://github.com/polkadot-fellows/runtimes/pull/1232) in preparation for the
  Individuality workload, and I am preparing [generalized fee payment in any asset with a
  rate](https://github.com/polkadot-fellows/runtimes/pull/1240) (transaction fee, XCM
  transaction fee and XCM delivery fee) on the People Chain. I also reviewed the
  [integration of Individuality into People and Asset Hub Polkadot](https://github.com/polkadot-fellows/runtimes/pull/1233).

### 2. Code design and protocol-level thinking
*Manifesto: "played a supporting role in the code-design of a major protocol component."*

- **Block-level batch proof validation.** Validating Bandersnatch Ring VRF proofs in a
  batch at the end of the block is up to 10× cheaper than one-by-one validation. I designed
  the scheme and implemented its SDK-side enabler: a frame-system/executive flag exposing
  whether the runtime is validating for the transaction pool, allowing conditional
  execution ([polkadot-sdk#12651](https://github.com/paritytech/polkadot-sdk/pull/12651)).
- I elaborated the designs for permissionless multi-asset Coinage, congestion-aware free
  unloading, recycling incentives and extrinsic prioritization under congestion, and a
  standard for safe offchain-worker + transaction-pool interactions — then implemented or
  reviewed their implementations.
- API design proposals for FRAME: descriptive pallet invalidities
  ([`ModuleInvalidity`](https://github.com/paritytech/polkadot-sdk/issues/11337)) and
  host-function-backed hashing ([#12727](https://github.com/paritytech/polkadot-sdk/issues/12727)).

### 3. Security-conscious and game-theoretic contributions
*Manifesto: "thinking in a security-conscious, game-theoretic way."*

I found and fixed several vulnerabilities and economic-safety issues before they could
reach production:

- **User-intent tampering vulnerability** in Coinage unload extrinsics: I discovered that
  unload calls could be tampered with in-flight, wrote a test showcasing the attack, and
  fixed it.
- **Unbounded computation in transaction validation** (a DoS vector on the pool): found and
  fixed twice in Coinage, plus bounding of all call parameters.
- **Spam and collision vectors in the transaction pool**: tag collisions, colliding
  offchain-worker transactions and transactions banned by the pool — diagnosed with
  reproductions and fixed.
- **`LiteAlias` origin was granted unlimited weight allowance**: found and fixed.
- **`merge_rings` was spammable** ([individuality-community#38](https://github.com/paritytech/individuality-community/pull/38))
  and **empty pages were created and never removed** ([#39](https://github.com/paritytech/individuality-community/pull/39))
  in pallet-members: found and fixed.
- **Benchmark under/over-estimates** in Coinage weights: found and reported
  ([#41](https://github.com/paritytech/individuality-community/issues/41),
  [#42](https://github.com/paritytech/individuality-community/issues/42),
  [#43](https://github.com/paritytech/individuality-community/issues/43)).

The economic mechanisms I implemented (congestion-based unload-token pricing, exponential
lock time on failure, extrinsic prioritization) are explicitly game-theoretic: they keep
the anonymous parts of the system fair and abuse-resistant even with a minority of
malicious users.

### 4. Code review and knowledge sharing
*Manifesto: "demonstrable presence of knowledge sharing within the ecosystem."*

I reviewed more than 200 pull requests during the period, including major components by
other Fellows and mentoring reviews for new contributors:

- polkadot-sdk: [FRAME Meta Transactions](https://github.com/paritytech/polkadot-sdk/pull/6428),
  [`MaxParachainBlockWeight`](https://github.com/paritytech/polkadot-sdk/pull/10315),
  [pallet-whitelist deferred dispatch](https://github.com/paritytech/polkadot-sdk/pull/11336),
  [claims below-ED rejection](https://github.com/paritytech/polkadot-sdk/pull/12254),
  [benchmarking 2-point slope fits](https://github.com/paritytech/polkadot-sdk/pull/12075),
  and many community refactors (fungible-traits migrations, try-state checks) where I
  mentor contributors on FRAME best practices.
- Individuality: the crypto module, `DecodeUnchecked` for trusted decodes, post-audit
  changes to the `verifiable` crate, members self-onboarding, VRF-based airdrop tickets,
  and the migrations of unsigned calls to the `authorize` API I designed.
- I also improved contributor-facing documentation and the benchmarking/CI infrastructure
  (e.g. [better omni-bencher diagnostics](https://github.com/paritytech/polkadot-sdk/pull/11510)).

## Voting record
|  Ranks | Activity thresholds | Agreement thresholds | Member's voting activities | Comments |
|---|---|---|---|---|
|I  |90%   |N/A   |   |  |
|II |80%   |N/A   |   |  |
|III|70%   |100%  | I have voted on x out of xx referenda in which I was eligible to vote (i.e. xx% voting activity). Out of xx referenda in which members of higher ranks were in complete agreement, I have voted in line with the consensus x times (i.e. xx% voting agreement). |  |
|IV |60%   |90%   |   |  |
|V  |50%   |80%   |   |  |
|VI |40%   |70%   |   |  |

## Misc

- [ ] Question(s):

- [ ] Concern(s):

- [ ] Comment(s):
