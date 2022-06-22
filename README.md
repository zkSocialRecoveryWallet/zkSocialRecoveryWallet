# zkSocialRecoveryWallet <!-- omit in toc -->
![zkSocialRecoveryWalletImage](https://user-images.githubusercontent.com/52170174/164951489-8f3d9b0a-4334-4dfb-b0d6-b6a87ff81424.png)

zk-SocialRecoveryWallet is a smart contract wallet using zk-SNARKS to restore a user's access to their smart contract wallet, without revealing any information of the user's and guardians.

The project is currently on [Harmony Testnet](https://explorer.pops.one/) and the frontend is hosted on [Vercel](https://github.com/vercel/vercel).

zkSocialRecoveryWallet Link:

<https://zkSocialRecoveryWallet.vercel.app/>

zkSocialRecoveryWallet Demo Video:

## Table of Contents <!-- omit in toc -->

- [Background](#background)
  - [What is social recovery](#social-recovery)
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
  - [Run zkgames-ui](#run-zkgames-ui)
- [Sources](#sources)

## Background

### What is social recovery
Social recovery is a smart contract wallet technique whereby a user is able to grant trusted persons the ability to restore the user access to their account. This is useful in case the user accidentally gets locked out of their account - access can simply be restored and the user is able to again access their account. See also this blog post pf Vitalik Buterin: [Why we need wide adoption of social recovery wallets](https://vitalik.ca/general/2021/01/11/recovery.html)



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

## Sources
- [Why we need wide adoption of social recovery wallets](https://vitalik.ca/general/2021/01/11/recovery.html)
- [EIP-2535: Diamonds, Multi-Facet Proxy](https://eips.ethereum.org/EIPS/eip-2535)
- [Diamond storage pattern](https://medium.com/1milliondevs/new-storage-layout-for-proxy-contracts-and-diamonds-98d01d0eadb)
