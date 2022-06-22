# zkSocialRecoveryWallet <!-- omit in toc -->

zk-SocialRecoveryWallet is a smart contract wallet using zk-SNARKS to restore a user's access to their smart contract wallet, without revealing any information of the user's and guardians.

The project is currently on [Harmony Testnet](https://explorer.pops.one/) and the frontend is hosted on [Vercel](https://github.com/vercel/vercel).

zkSocialRecoveryWallet Link:

<https://zkSocialRecoveryWallet.vercel.app/>

zkSocialRecoveryWallet Demo Video:

## Table of Contents <!-- omit in toc -->

- [Background](#background)
  - [What is social recovery](#social-recovery)
  - [This project](#this-project)
    - [Guardian Registration flow](#guardian-registration)
    - [Regular flow for a Owner](#owner-flow)
    - [Recovery Flow for Guardians](#recovery-flow)
    - [Guardian Management Flow for an Owner](#guardian-management)
- [Project Structure](#project-structure)
  - [circuits](#circuits)
  - [contracts](#contracts)
  - [guardian-ui](#guardian-ui)
  - [wallet-ui](#wallet-ui)
- [Zero Knowledge Structure](#zero-knowledge-structure)
- [Run Locally](#run-locally)
  - [Clone the Repository](#clone-the-repository)
  - [Run circuits](#run-circuits)
  - [Run contracts](#run-contracts)


## Background

### What is social recovery
Social recovery is a smart contract wallet technique whereby a user is able to grant trusted persons the ability to restore the user access to their account. This is useful in case the user accidentally gets locked out of their account - access can simply be restored and the user is able to again access their account. 

Vitalik released a [post](https://vitalik.ca/general/2021/01/11/recovery.html) about the need for wider adoption of social recovery wallets. Multi-sigs have thus far been the standard for more secure custody of funds, but require multiple parties to coordinate on transactions. Using just a single key paradigm (e.g. Metamask) can lead to loss of funds if the private key is lost, or theft if a hacker acquires the single private key. Thus, we have social recovery wallets. 

Social Recovery wallets are designed to mitigate against two scenarios: 
1. The user loses their private key 
2. A hacker obtains a user's private key

The image below describes the flow of a social recovery wallet. A single owner is able to sign off on transactions, but a set of guardians is able to change the owner (the signing key). 

![image](https://user-images.githubusercontent.com/97858468/153685332-03d92feb-140f-43e4-b8be-6e455206d6cc.png)

### This project
The previous approach places the Ethereum address of the guardian in plain text on-chain. We seek to instead keep the guardian address private, whilst still allowing social recovery functionality.

To do this, when a user is adding a guardian, using zero knowledge addMembers() with Semaphore. Semaphore allows Ethereum users to prove their membership of a group and send signals such as votes or endorsements without revealing their original identity. 

#### Guardian Registration flow
- Guardian register to **register-service**
- Guardian sign with identitycommitment
- After approval, the identitycommitment of the guardian is saves in the **factory contract**

#### Regular flow for a Owner
Social recovery wallets are meant to minimze the burden that an owner faces when making transactions. Thus, the flow consists of just a call `acceptOwnership()` with the new nominee wallet address

#### Recovery Flow for Guardians
In the event that an owner loses their private key, guardians can be notified and a recovery process can be kicked off. 
- Guardian calls `initiateRecovery` with the address of the newOwner.
- `threshold - 1` number of guardians call `supportRecovery` with the newOwner.
- Any guardian calls `executeRecovery` to change the nominee of the wallet.


#### Guardian Management Flow for an Owner
Owners have the ability to swap out guardians in the case that a guardian's keys are compromised or a guardian becomes malicious. 
- Owner calls `removeGuardian` or ``removeGuardians` with hash of a guardian's. 
- Owner calls `addGuardian` or `addGuardian` with the hash of the guardian's. This queues the guardian for adding – the guardian can only be added after a time delay of 3 days. 

## Project Structure

The project has three main folders:

- circuits
- contracts
- zkgames-ui

### circuits

The [circuits folder](/circuits/) contains all the circuits used in zkSocialRecoveryWallet.

### contracts

The [contracts folder](/contracts/) contains all the smart contracts used in zkGames.

To learn more about the zkGames smart contracts, read the [README file](/contracts/README.md) inside the `contracts` folder.

