## What is Atlas Governance?

Atlas utilizes an on-chain governance mechanism to manage and upgrade the protocol, such as upgrading smart contracts,
adding new stablecoins, or modifying the allocation of reserve assets. All changes must be approved by MAPO token
holders. A supermajority threshold model is used to determine the required number of votes for a proposal to pass.

## Proposal Process

Changes are managed through the Atlas Governance smart contract. This contract acts as the "owner" to modify other
protocol smart contracts. Such smart contracts are referred to as governable. The Governance contract itself is
governable and owned by itself.

The change process consists of the following stages:

1. Proposal
2. Approval
3. Referendum
4. Execution

## Overview of Phases

Each proposal starts in the proposal queue, where it may receive approval votes to move forward in the queue relative to
other queued proposals. Proposal authors should strive to find community members to vote in favor of their proposal (
proposers can also vote in favor of their own proposal). Up to three proposals from the top of the queue are
automatically dequeued and promoted to the approval stage per day. Any proposal that remains in the queue for 4 weeks
will expire.

- `Approval` lasts 1 days (24 hours), during which the proposal must be approved by the Approver(s). Approved proposals
  are promoted to the Referendum stage.
- `Referendum` lasts five days, during which owners of Locked Celo vote yes or no on the proposal. Proposals that
  satisfy the necessary quorum are promoted to the execution phase.
- `Execution` lasts up to three days, during which anybody may trigger the execution of the proposal.

## Proposal

Any user can submit a proposal to the `Governance` smart contract along with a small amount of MAPO deposit. This
deposit
is required to prevent spam proposals, and if the proposal enters the approval stage, the deposit will be refunded to
the proposer. Proposals consist of a transaction list and a description URL, where voters can find more information
about the proposal. The transaction data included in the proposal includes the target address, data, and value. If the
proposal is approved, the included transactions will be executed by the `Governance` contract.

Submitted proposals are added to the proposal queue. While a proposal is in this queue, voters can use their locked MAPO
to vote in favor of the proposal. Once per day, the top three proposals in the queue, based on the weight of locked MAPO
supporting them, are dequeued and move to the approval stage. Note that if there are fewer than three proposals in the
queue, all proposals may be dequeued even without any approval votes. If a proposal remains in the queue for more than 4
weeks, it expires and the deposit is forfeited.

## Approval

Every day the top three proposals at the head of the queue are pop off and move to the Approval phase. At this time, the
original proposers are eligible to reclaim their locked MAPO deposit. In this phase, the proposal needs to be approved
by the Approver. The Approval phase lasts 1 day and if the proposal is not approved in this window, it is considered
expired and does not move on to the “Referendum” phases.

## Referendum

Once the Approval phase is over, approved proposals graduate to the referendum phase. Any user may vote yes, no, or
abstain on these proposals. Their vote's weight is determined by the weight of their locked MAPO. After the Referendum
phase is over, which lasts five days, each proposal is marked as passed or failed as a function of the votes and the
corresponding passing function parameters.

In order for a proposal to pass, it must meet a minimum threshold for participation, and agreement:

- Participation is the minimum portion of locked MAPO which must cast a vote for a proposal to pass. It exists to
  prevent proposals passing with very low participation. The participation requirement is calculated as a governable
  portion of the participation baseline, which is an exponential moving average of final participation in past
  governance proposals.
- Agreement is the portion of votes cast that must be "yes" votes for a proposal to pass. Each contract and function can
  define a required level of agreement, and the required agreement for a proposal is the maximum requirement among its
  constituent transactions.

## Execution

Proposals that graduate from the Referendum phase to the Execution phase may be executed by anyone, triggering a call
operation code with the arguments defined in the proposal, originating from the Governance smart contract. Proposals
expire from this phase after three days.