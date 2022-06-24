# zkSocialRecoveryWallet <!-- omit in toc -->

![zkWalletImage](images/zkWallet.png)

zk-SocialRecoveryWallet is a smart contract wallet using zk-SNARKS to restore a user's access to their smart contract wallet, without revealing any information of the user's and guardians.

The project is currently on [Harmony Testnet](https://explorer.pops.one/) and the frontend is hosted on [Vercel](https://github.com/vercel/vercel).

zkSocialRecoveryWallet Link:

<https://zkSocialRecoveryWallet.simplicy.io/>

Guardian registration Link:

<https://guardian-ui.simplicy.io>

zkSocialRecoveryWallet Demo Video:

## Table of Contents <!-- omit in toc -->

- [Background](#background)
  - [What is social recovery](#social-recovery)
  - [Our approach](#our-approach)
    - [Overview](#flow-overview) 
    - [Guardian Registration flow](#guardian-registration)
    - [Regular flow for a Owner](#owner-flow)
    - [Recovery Flow for Guardians](#recovery-flow)
    - [Guardian Management Flow for an Owner](#guardian-management)
- [Project Structure](#project-structure)
  - [circuits](#circuits)
  - [contracts](#contracts)
  - [guardian-ui](#guardian-ui)
  - [wallet-ui](#wallet-ui)
- [Zero Knowledge Structure](#zk-structure)
- [Run Locally](#run-locally)
  - [Clone the Repository](#clone-the-repository)
  - [Get the Submodules](#get-submodules)
  - [Run circuits](#run-circuits)
  - [Run contracts](#run-contracts)
  - [Run Guardian Service](#run-guardian-service)
  - [Run Guardian UI](#run-guardian-ui)
  - [Run Wallt UI](#run-wallet-ui)
- [Deployed contracts](#deployed-contracts)

## Background

### What is social recovery
Social recovery is a smart contract wallet technique whereby a user is able to grant trusted persons the ability to restore the user access to their account. This is useful in case the user accidentally gets locked out of their account - access can simply be restored and the user is able to again access their account. 

Vitalik released a [post](https://vitalik.ca/general/2021/01/11/recovery.html) about the need for wider adoption of social recovery wallets. Multi-sigs have thus far been the standard for more secure custody of funds, but require multiple parties to coordinate on transactions. Using just a single key paradigm (e.g. Metamask) can lead to loss of funds if the private key is lost, or theft if a hacker acquires the single private key. Thus, we have social recovery wallets. 

Social Recovery wallets are designed to mitigate against two scenarios: 
1. The user loses their private key 
2. A hacker obtains a user's private key

The image below describes the flow of a social recovery wallet. A single owner is able to sign off on transactions, but a set of guardians is able to change the owner (the signing key). 

![image](images/diag2.png)

<a name="our-approach"/>

### Our approach
The previous approach places the Ethereum address of the guardian in plain text on-chain. We seek to instead keep the guardian address private, whilst still allowing social recovery functionality.

To do this, when a user is adding a guardian, using zero knowledge addGuardians() with Semaphore. Semaphore allows Ethereum users to prove their membership of a group and send signals such as votes or endorsements without revealing their original identity. 

<a name="flow-overview"/>

#### Overview
![FLow overview](./images/flow-overview.png)

<a name="guardian-registration"/>

#### Guardian Registration flow
- Guardian register to **register-service**
- Guardian sign with identitycommitment
- After approval, the identitycommitment of the guardian is saves in the **factory contract**

<a name="owner-flow"/>

#### Regular flow for a Owner
Social recovery wallets are meant to minimze the burden that an owner faces when making transactions. Thus, the flow consists of just a call `acceptOwnership()` with the new nominee wallet address

<a name="recovery-flow"/>

#### Recovery Flow for Guardians
In the event that an owner loses their private key, guardians can be notified and a recovery process can be kicked off. 
- Guardian calls `initiateRecovery` with the address of the newOwner.
- `threshold - 1` number of guardians call `supportRecovery` with the newOwner.
- Any guardian calls `executeRecovery` to change the nominee of the wallet.

<a name="guardian-management"/>

#### Guardian Management Flow for an Owner
Owners have the ability to swap out guardians in the case that a guardian's keys are compromised or a guardian becomes malicious. 
- Owner calls `removeGuardian` or ``removeGuardians` with hash of a guardian's. 
- Owner calls `addGuardian` or `addGuardian` with the hash of the guardian's. This queues the guardian for adding – the guardian can only be added after a time delay of 3 days. 

<a name="project-structure"/>

## Project Structure

The project has three main folders within packages directory:

- zkWallet-contracts/circuits
- zkWallet-contracts/contracts


### circuits

The [circuits folder](/circuits/) contains all the circuits used in zkSocialRecoveryWallet.
To learn more about the zkSocialRecoveryWallet circuits, read the [README file](/packages/zkWallet-contracts/circuits/README.md) inside the `circuits` folder.

### contracts

The [contracts folder](/contracts/) contains all the smart contracts used in zkSocialRecoveryWallet.

To learn more about the zkSocialRecoveryWallet smart contracts, read the [README file](/packages/zkWallet-contracts/contracts/README.md) inside the `contracts` folder.

<a name="zk-structure"/>

## Zero Knowledge Structure

The following graphic shows the structure of the most important zero knowledge elements of the zkSocialRecoveryWallet project.

```text
├── packages
│   ├── zkWallet-contracts
│   │   ├── build
│   │   │   ├── snark-artifacts
│   │   │   │   ├── semaphore.wasm
│   │   │   │   ├── semaphore.zkey
│   │   ├── circuits
│   │   │   ├── semaphore.circom
│   │   │   ├── ecdh.circom
│   │   │   ├── tree
│   │   │   │   ├── hasherPoseidon.circom
│   │   │   │   ├── poseidon
│   │   │   │   │   ├── poseidonHashT3.circom
│   │   │   │   │   ├── poseidonHashT4.circom
│   │   │   │   │   ├── poseidonHashT5.circom
│   │   │   │   │   ├── poseidonHashT6.circom
│   │   ├── contracts
│   │   │   ├── contracts
│   │   │   │   ├── facets
│   │   │   │   │   ├── GuardianFacet.sol
│   │   │   │   │   ├── RecoveryFacet.sol
│   │   │   │   │   ├── SemaphoreFacet.sol
│   │   │   │   │   ├── SemaphoreGroupsFacet.sol
│   │   │   │   ├── recovery
│   │   │   │   │   ├── IRecovery.sol
│   │   │   │   │   ├── IRecoveryInternal.sol
│   │   │   │   │   ├── Recovery.sol
│   │   │   │   │   ├── RecoveryInternal.sol
│   │   │   │   │   ├── RecoveryStorage.sol
│   │   │   │   ├── semaphore
│   │   │   │   │   ├── base
│   │   │   │   │   │   ├── SemaphoreCoreBase
│   │   │   │   │   │   │   ├── ISemaphoreCoreBase.sol
│   │   │   │   │   │   │   ├── ISemaphoreCoreBaseInternal.sol
│   │   │   │   │   │   │   ├── SemaphoreCoreBase.sol
│   │   │   │   │   │   │   ├── SemaphoreCoreBaseInternal.sol
│   │   │   │   │   │   │   ├── SemaphoreCoreBaseMock.sol
│   │   │   │   │   │   │   ├── SemaphoreCoreBaseStorage.sol
│   │   │   │   │   │   ├── SemaphoreGroupsBase
│   │   │   │   │   │   │   ├── ISemaphoreGroupsBase.sol
│   │   │   │   │   │   │   ├── ISemaphoreGroupsInternal.sol
│   │   │   │   │   │   │   ├── SemaphoreGroupsBase.sol
│   │   │   │   │   │   │   ├── SemaphoreGroupsBaseInternal.sol
│   │   │   │   │   │   │   ├── SemaphoreGroupsBaseMock.sol
│   │   │   │   │   │   │   ├── SemaphoreGroupsBaseStorage.sol
│   │   │   │   │   ├── extensions
│   │   │   │   │   │   ├── SemaphoreVoting
│   │   │   │   │   │   │   ├── ISemaphoreVoting.sol
│   │   │   │   │   │   │   ├── ISemaphoreVotingInternal.sol
│   │   │   │   │   │   │   ├── SemaphoreVoting.sol
│   │   │   │   │   │   │   ├── SemaphoreVotingInternal.sol
│   │   │   │   │   │   │   ├── SemaphoreVotingStorage.sol
│   │   │   │   │   ├── ISemaphore.sol
│   │   │   │   │   ├── ISemaphoreGroups.sol
│   │   │   │   │   ├── ISemaphoreInternal.sol
│   │   │   │   │   ├── Semaphore.sol
│   │   │   │   │   ├── SemaphoreInternal.sol
│   │   │   │   │   ├── SemaphoreStorage.sol
│   │   │   │   ├── utils
│   │   │   │   │   ├── cryptography
│   │   │   │   │   │   ├── IncrementalBinaryTree
│   │   │   │   │   │   │   ├── IIncrementalBinaryTree.sol
│   │   │   │   │   │   │   ├── IIncrementalBinaryTreeInternal.sol
│   │   │   │   │   │   │   ├── IncrementalBinaryTreeInternal.sol
│   │   │   │   │   │   │   ├── IncrementalBinaryTreeStorage.sol
│   │   │   │   │   │   ├── Hashes.sol
│   │   │   │   │   ├── Constant.sol
│   │   │   │   ├── verifier
│   │   │   │   │   ├── Verifier20.sol
```

To see the full directory structure of the smart contracts, see [README file](/packages/zkWallet-contracts/contracts/README.md) inside the `contracts` folder.

<a name="run-locally"/>

## Run Locally

<a name="clone-the-repository"/>

### Clone the epository

```bash
git clone https://github.com/zkSocialRecoveryWallet/zkSocialRecoveryWallet.git
```
<a name="get-submodules"/>

### Get the Submodules

```bash
git submodule update --init --recursive
```

<a name="run-circuits"/>

### Run circuits

To run cicuits, go inside the `contract` folder:

```bash
cd packages/zkWallet-contracts/
```

Run:

```bash
yarn compile:circuits
```

See also the follow the intructions in the [README file](/packages/zkWallet-contracts/contracts/circuits/README.md) in the  **`circuits`** folder.

<a name="run-contracts"/>

### Run contracts

To run contracts, go inside the **`contracts`** folder:

```bash
cd packages/zkWallet-contracts/
```

Then, follow the intructions in the [README file](/packages/zkWallet-contracts/README.md) in the `contracts` folder.

<a name="run-guardian-service"/>

### Run Guardian service

To run the Guardian Service, go inside the **`guardian-service`** folder:

```bash
cd packages/guardian-service
```

Then, follow the intructions in the [README file](/packages/guardian-service/contracts/README.md) in the `contracts` folder.

<a name="run-guardian-ui"/>

### Run Guardian ui

To run the Guardian Ui, go inside the **`guardian-ui`** folder:

```bash
cd packages/guardian-ui
```

Then, follow the intructions in the [README file](/packages/guardian-ui/README.md) in the `contracts` folder.

<a name="deployed-contracts"/>

## Deployed contracts

 Name                   | Harmony One Testnet                             | Version |
| :---                  | :---                                            |  :---   |
| Alice Wallet          | 0x4537224A3332FAa4d179d6d2fcD776D4bF96fC39      | 0.0.1   |
| Bob Wallet            | 0x93Fb183E6aFcf5193CDdc3bB5dA1b511a9909dc6      | 0.0.1   |
| Verifier16            | 0xEC4b5d3cB604D4771473F9E58394A45619C38BB2      | 1.0.0   |
| Verifier17            | 0x9BBCD95F0D06C6ff992E2baC305cC7C004101214      | 1.0.0   |
| Verifier18            | 0x4cEcE384bCB377EB7a3d39449a96478f5fa5fa72      | 1.0.0   |
| Verifier19            | 0x9671cBE0C930DC94085118dB0197e861abF74e02      | 1.0.0   |
| Verifier20            | 0x01F5C5E69AC1bcBC41Cef0a90A43A8e5A79cFb69      | 1.0.0   |
| Verifier21            | 0x8d2171b7dDC7F6B0f04657835a6b19F04B821353      | 1.0.0   |
| Verifier22            | 0x64D87dC944Fb6956Ff09b4a1da2BfBFf4F78f976      | 1.0.0   |
| Verifier23            | 0xa8007c877333508A07eA9f16CF2a2415057Ec0BC      | 1.0.0   |
| Verifier24            | 0x434a85Da614E439929e7480464B18c1cea043B19      | 1.0.0   |
| Verifier25            | 0x21176AA38497bdeab3CdB4368CFF53c428B001f7      | 1.0.0   |
| Verifier26            | 0x48164EcB1CB426C72DCE0421F4426daE264724a7      | 1.0.0   |
| Verifier27            | 0x50c3B09c4b478bd88D0ED14B056AcaFb679Bd345      | 1.0.0   |
| Verifier28            | 0xbDc6e16F514e8d8098ABc90Ab40482d5CbD7c9Aa      | 1.0.0   |
| Verifier29            | 0x6c3Ebf4d06e9595248fA7cc506ad87CffE445dee      | 1.0.0   |
| Verifier30            | 0x6fCe423A2fDDe6788a27a99b59e0Cb40579CE988      | 1.0.0   |
| Verifier31            | 0xCD81D914F1032F7d4c4AB088546c7d70A438cFC3      | 1.0.0   |
| Verifier32            | 0x9eE3cC20Eb1e6a695192A9EDB0512694d31d81bf      | 1.0.0   |
| PoseidonT3            | 0x18145aa8e5eb71959EaAC9Be2B383A070E80300A      | 1.0.0   |
| ERC20Facet            |                                                 | 0.0.1   |
| ERC721Facet           |                                                 | 0.0.1   |
| GuardianFacet         |                                                 | 0.0.1   |
| RecoveryFacet         |                                                 | 0.0.1   |
| SemaphoreFacet        | 0x532E815c80198b78512858F4cf125be4858c5e9A      | 0.0.1   |
| SemaphoreGroupsFacet  | 0x5a6A9c1412179ef061CDF328E6b66BB8c5F337B6      | 0.0.1   |
| SemaphoreVotingFacet  | 0x5a6A9c1412179ef061CDF328E6b66BB8c5F337B6      | 0.0.1   |
| SimplicyWalletDiamond | 0x93Fb183E6aFcf5193CDdc3bB5dA1b511a9909dc6      | 0.0.1   |
| WalletFactoryDiamond  | 0x93Fb183E6aFcf5193CDdc3bB5dA1b511a9909dc6      | 0.0.1   |
| WalletFactoryFacet    |                                                 | 0.0.1   |