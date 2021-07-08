## Headers
| UMIP-PLACEHOLDER    |                                                                                                                                          |
|------------|------------------------------------------------------------------------------------------------------------------------------------------|
| UMIP Title | Register Polygon Oracle Root Tunnel             |
| Authors    | Nick Pai (nick@umaproject.org) |
| Status     | Draft                                                                                                                                    |
| Created    | July 8, 2021                                                                                                                           |

## Summary (2-5 sentences)
This UMIP will have the effect of registering an oracle root tunnel contract that will enable the optimistic oracle deployed on Polygon to make cross-chain price requests to the DVM on Ethereum.

## Motivation
[This document](https://github.com/UMAprotocol/protocol/blob/master/packages/core/contracts/polygon/README.md) describes a two way tunnel architecture that is built on top of the [canonical state transfer mechanism](https://docs.matic.network/docs/develop/l1-l2-communication/state-transfer) between Polygon and Ethereum. Data is sent between the networks through the tunnel using the same public infrastructure that you use to deposit ERC20 tokens from Ethereum to Polygon and vice versa. The only trusted third parties are the Polygon validators.

In order for price requests to be relayed from Polygon to Ethereum, there must be a "root tunnel" contract deployed to Ethereum which can make price requests to the DVM. This UMIP registers such a contract.

Note that this "root tunnel" contains reference to a "child tunnel" deployed on Polygon, whose address can only be set once. This means that the "root tunnel" will only relay price requests originated from its `fxChildTunnel()` return value.

## Technical Specification
To accomplish this upgrade, the following actions need to be taken:
- The `OracleRootTunnel` will need to be registered with the `Registry` so that it can make requests to the DVM.

## Rationale
Currently, the optimistic oracle deployed on Polygon can make price requests to the "child tunnel" oracle on Polygon, but the child tunnel cannot relay price requests to the DVM via the root tunnel, which is unregistered. This was intended to enable developers to test out their optimistic oracle integrations on Polygon.

This UMIP will productionize the optimistic oracle infrastructure on Polygon and secure it by the canonical DVM on Ethereum.

## Implementation

The Polygon tunnel contract implementations can be found [here](https://github.com/UMAprotocol/protocol/tree/master/packages/core/contracts/polygon) and they have been audited. Note that the directory contains Governor tunnel contracts as well, which are built on the same state transfer mechanism as the oracle tunnel. There are no governance actions neccessary at this time involving the governor tunnel contracts, but they are deployed to both Mainnet and Polygon and will enable UMA voters on Ethereum to execute governance actions on Polygon.

The following PR's implemented the contract:
- [Introduce Polygon Oracle tunnel architecture](https://github.com/UMAprotocol/protocol/commit/a3bf46270787cbaae4ed2218f064b1217c153a50#diff-0b107e6f8b172512e41e497fa2381f53ca1a8f193a4d51dec6b460abbb161256)
- [Added unit tests to tunnel contracts](https://github.com/UMAprotocol/protocol/commit/2f8eaf0d4c0e9261424aa415cf675a4011849eb8#diff-0b107e6f8b172512e41e497fa2381f53ca1a8f193a4d51dec6b460abbb161256)
- [Small changes made to contracts after integration testing](https://github.com/UMAprotocol/protocol/commit/d146d4feddcf99e157a909032a3071e95ca51eb9#diff-0b107e6f8b172512e41e497fa2381f53ca1a8f193a4d51dec6b460abbb161256)
- [Add Governor tunnel achitecture and unit tests](https://github.com/UMAprotocol/protocol/commit/20a386693cc380827b3eedd7ff7382b15d7670f3#diff-0b107e6f8b172512e41e497fa2381f53ca1a8f193a4d51dec6b460abbb161256)
- [Response to audit on all Polygon tunnel contracts](https://github.com/UMAprotocol/protocol/commit/dd211c4e3825fe007d1161025a34e9901b26031a#diff-0b107e6f8b172512e41e497fa2381f53ca1a8f193a4d51dec6b460abbb161256)

Contract addresses can be found here:
- [OracleRootTunnel](https://etherscan.io/address/0x55a8ec3fe18f8074d090eea8abd56501a4ba098e#code)
- [OracleChildTunnel](https://polygonscan.com/address/0x4093dC528c8A8371647e4E960c8Bb7Bc5e90e7C9#code)
- [GovernorRootTunnel](https://etherscan.io/address/0xeA7Cf05c0bF907997Abd499395556df52F2a20bb#code)
- [GovernorChildTunnel](https://polygonscan.com/address/0x5E828cB22CEfa920E0f3fa18583269f6239818AD#code)

## Security considerations
Please see the individual PRs for details on how each affects the security of the UMA ecosystem. This repo has been audited by OpenZeppelin and the audit feedback can be found in this [PR](https://github.com/UMAprotocol/protocol/pull/3188) specifically in the section on PRs #3054, #3082, and #3092.

This tunnel system relies on the [Polygon consensus mechanism](https://docs.matic.network/docs/home/architecture/security-models/#proof-of-stake-security) secured by validators in a Proof of Stake system. The validator set enforces the integrity of data passed between networks (i.e. downstream users need to trust that the validators are not modifying the arbitrary messages that are being sent between networks).

Moreover, downstream users also rely on off-chain actors to relay messages in a timely fashion. Historically messages are sent once per hour.

More details about the tunnel can be found [here](https://github.com/UMAprotocol/protocol/tree/master/packages/core/contracts/polygon#readme).