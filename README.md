# Awesome RGB [![Awesome](https://cdn.rawgit.com/sindresorhus/awesome/d7305f38d29fed78fa85652e3a63e154dd8e8829/media/badge.svg)](https://github.com/sindresorhus/awesome)

An opinionated list of everything awesome about RGB.

- [Summary](#summary)
- [How It Works](#how-it-works)
- [Architecture](#architecture)
- [Organizations](#organizations)
- [Platforms](#platforms)
- [Projects](#projects)
- [Resources](#resources)
    - [Social Media](#social-media)
    - [Influencers](#influencers)
    - [Websites](#websites)
- [Awesome Lists](#awesome-lists)
- [Contributing](#contributing)
- [Contributors](#contributors)

---

## Summary

RGB is a scalable and confidential smart contract system designed for Bitcoin and the Lightning Network. Its purpose is to enable the issuance and management of complex, programmable assets—such as tokens, non-fungible tokens (NFTs), and digital securities—without creating a separate token or requiring changes to Bitcoin’s core protocol. RGB represents a fundamental paradigm shift from traditional blockchain models, aiming to deliver advanced functionality while upholding Bitcoin’s principles of decentralization and security.

The protocol's core innovation is **client-side validation**. Unlike blockchains like Ethereum, where all smart contract data and transaction history are stored publicly and validated by every node, RGB keeps all data off-chain. The Bitcoin blockchain is used only as a state commitment layer; it anchors the history of asset ownership through cryptographic proofs embedded in standard Bitcoin transactions. The actual transaction data and validation logic are managed privately by the parties involved. When a user receives an RGB asset, they receive its entire ownership history, allowing them to independently verify its authenticity and validity from its origin point without consulting a public ledger.

This architecture yields three transformative benefits:

1.  **Unparalleled Privacy:** Since asset data never touches the public blockchain, transactions are confidential by default, known only to the sender and receiver. This is a critical feature for commercial and enterprise applications.
2.  **Extreme Scalability:** By eliminating blockchain bloat, RGB’s scalability is not constrained by Bitcoin’s block size. It can handle a virtually unlimited volume of transactions, which can be settled instantly and cheaply over the Lightning Network.
3.  **Future-Proof Security:** RGB inherits the full security and finality of the Bitcoin network for double-spend protection. Because it operates on a higher layer, it can be continuously developed and enhanced without risking the stability of Bitcoin itself.

In essence, RGB aims to be a foundational layer for building a private, decentralized digital economy on Bitcoin's security bedrock. It offers the tools to create sophisticated financial instruments and digital rights management systems, positioning Bitcoin not just as digital gold, but as a global, secure, and confidential platform for all forms of digital assets.

***

## How It Works

RGB is a smart contract system that operates on top of Bitcoin and the Lightning Network. Its functionality is built around a revolutionary concept called **client-side validation**, which fundamentally departs from the "global ledger" model used by blockchains like Ethereum. In RGB, all smart contract data and its transfers are kept off-chain, with the Bitcoin blockchain used only as a cryptographic anchor for security. This design prioritizes privacy, scalability, and future-proof extensibility.

**Issuance (Contract Genesis):** The process begins when an issuer defines a smart contract, known as a "schema," which sets the rules for an asset—its properties, rights, and how it can be transferred. The issuer then creates a specific instance of this schema, called a "contract genesis," which defines the initial state, such as the total supply of a token and who initially owns it. This entire genesis state is not broadcast publicly. Instead, it is cryptographically linked to a specific Bitcoin Unspent Transaction Output (UTXO) controlled by the issuer. The Bitcoin blockchain only sees a standard transaction creating this UTXO; it has no awareness of the complex asset structure it is securing. This UTXO acts as a "single-use seal," a container for the asset's current state.

**Transfers (State Transitions):** When a user wants to transfer an RGB asset, they initiate a "state transition." This involves two key steps. First, they create a Bitcoin transaction that spends the UTXO currently holding the asset (opening the seal). Second, this new Bitcoin transaction creates one or more new UTXOs that will become the new containers for the asset's updated state (e.g., one UTXO for the portion sent to the receiver, and one for the change sent back to the sender).

Crucially, the details of this asset transfer are not recorded on the blockchain. Instead, the sender creates an off-chain data packet called a "consignment." This consignment contains:
1.  The definition of the new asset state.
2.  The full history of the asset's ownership, tracing all the way back to the genesis. This is its "provenance."
3.  The cryptographic proofs linking each state transition to its corresponding Bitcoin transaction.

This consignment is then sent directly to the receiver through a private communication channel (e.g., a dedicated protocol, or even encrypted messaging).

**Validation:** Upon receiving the consignment, the receiver's RGB-enabled wallet performs the client-side validation. It independently verifies the entire history contained within the consignment, checking every rule of the smart contract and every cryptographic signature from the point of creation to the present. The wallet software does this without needing to trust the sender, a central server, or a public block explorer. If the validation is successful, the receiver's wallet accepts the asset and begins monitoring the new UTXO on the Bitcoin blockchain as the new owner. This entire process ensures that all asset data remains private between the transacting parties, and the computational load of validation is distributed among the users rather than being borne by a global network of nodes.

## Architecture

The architecture of RGB is a meticulously designed, multi-layered system that prioritizes modularity, separation of concerns, and adherence to open standards. This structure ensures that each component is specialized and can evolve independently, contributing to a robust and flexible ecosystem.

**Layer 1: The Bitcoin Consensus Layer (LNP/BP-1):** At the absolute base is the Bitcoin blockchain. Its role in the RGB architecture is that of a "State Commitment Layer." It is not used for data storage or smart contract execution. Its sole functions are to provide:
*   **Proof-of-Publication:** A censorship-resistant and globally available medium to publish cryptographic commitments.
*   **Double-Spend Protection:** The UTXO model inherently prevents the same asset state from being spent twice.
*   **Timestamping and Finality:** The immutable, time-ordered nature of the blockchain provides a secure timeline for asset history.
The Lightning Network also exists at this foundational layer, serving as the "State Channel and Communication Layer" for enabling instant, off-chain settlement of RGB state transitions.

**Layer 2: The RGB Core Library:** This is the heart of the system. Unlike a monolithic node, RGB’s core logic is implemented as a Rust library (`rgb-core`). This architectural choice is deliberate, allowing any application—from a mobile wallet to an exchange's backend—to integrate RGB functionality directly. The Core Library is responsible for all the heavy lifting:
*   **Schema & Contract Management:** Defining and interpreting the rules of smart contracts.
*   **Client-Side Validation:** Executing the validation logic for incoming consignments, verifying their entire history.
*   **State Management:** Creating and managing state transitions and asset ownership.
*   **Cryptographic Operations:** Generating commitments, single-use seals, and proofs.
It acts as the trustless validation engine that applications use to interact with the RGB ecosystem securely.

**Layer 3: Schemas and Interfaces (LNP/BP-2 to LNP/BP-4):** This layer defines the "application logic." RGB is not just for tokens; it's a generic smart contract system. Schemas are standardized templates that define the structure, state, and allowed operations for different types of contracts. For example, there is a schema for fungible tokens (`RGB-20`), one for non-fungible tokens (`RGB-21`), and others can be created for more complex assets like voting rights or digital identity. Interfaces define how different wallets and services can interact with these schemas in a standardized way, ensuring interoperability.

**Layer 4: Application & Wallet Layer:** This is the user-facing layer where developers build products. These applications integrate the RGB Core Library and are responsible for:
*   **User Interface (UI/UX):** Providing a way for users to see and manage their assets.
*   **Data Storage:** Storing the user's private keys and the off-chain data (consignments and contract information), often called the "stash."
*   **Peer-to-Peer Communication:** Managing the direct communication channels required to exchange consignments with other users.

This layered architecture, governed by the LNP/BP standards, ensures that RGB is not a single product but a foundational protocol for a new generation of private and scalable Bitcoin-native applications.

## Organizations

The development and stewardship of the RGB protocol are managed by a decentralized and standards-focused collection of organizations and individuals, a structure designed to prevent corporate capture and promote open innovation.

**1. The LNP/BP Standards Association:** At the center of the ecosystem is the LNP/BP (Lightning Network Protocol / Bitcoin Protocol) Standards Association, a non-profit organization based in Switzerland. This body does not write code itself; instead, its primary mission is to develop, maintain, and promote a set of open, formal technical standards for building scalable, private, and censorship-resistant technologies on top of Bitcoin and the Lightning Network. RGB is the flagship protocol under its purview.

The association's role is analogous to that of the IETF (Internet Engineering Task Force) for the internet. By creating clear, public specifications, it ensures that multiple independent teams can build interoperable software, fostering a healthy, competitive ecosystem and preventing any single entity from controlling the protocol's future. The association is led by key figures in the space, most notably Dr. Maxim Orlovsky, who is the lead architect of the standards, and Giacomo Zucco, a prominent Bitcoin educator and evangelist.

**2. Pandora Core AG:** This is the primary engineering and development organization responsible for turning the LNP/BP standards into high-quality, production-ready software. Also based in Switzerland, Pandora Core is a for-profit company that develops and maintains the main implementation of the RGB Core Library, written in Rust. It serves as the main contributor of technical expertise and manpower to the RGB project. The clear separation between the non-profit standards body (LNP/BP) and the for-profit implementation team (Pandora Core) is a deliberate architectural choice to ensure that the protocol's direction is guided by community consensus and technical merit, not by the commercial interests of a single company.

**3. Key Adopters and Financial Supporters:** The real-world viability of RGB is heavily bolstered by the commitment of major industry players. The most significant of these are **Bitfinex** and **Tether**. As the issuer of the world's largest stablecoin (USDT), Tether, along with its sister company Bitfinex, has been a major financial backer of RGB's development. They view RGB as the most promising technology for bringing USDT to the Lightning Network, which would enable scalable, private, and low-cost stablecoin payments on a global scale. Their support provides crucial resources and signals strong market confidence in the protocol.

Other organizations are also building on RGB, demonstrating its versatility. **DIBA** (Digital Bitcoin Art) is a company focused on creating an NFT marketplace using RGB, showcasing its capabilities beyond fungible tokens. Wallets like **Iris Wallet** are among the first to offer a user-friendly mobile experience for RGB assets, bringing the technology directly to consumers. This diverse group of organizations, from a non-profit standards body to development firms and major financial companies, collectively forms the robust ecosystem driving RGB's journey from a theoretical concept to a practical reality.

---

## Platforms

*List of all platforms e.g. wallet, trading software, trading platform, or infrastructure built for RGB*

* Wallet
    * [Bitmask](https://bitmask.app) - BitMask wallet, your Bitcoin swiss army knife.
    * [Bitlight Labs](https://bitlightlabs.com/) - Make Bitcoin Smart
    * [Bitcoin Tribe](https://bitcointribe.app) - Tribe RGB offers self-custody for secure Bitcoin and RGB asset management.
    * [Pandora Prime](https://pandoraprime.ch/) - Empowering Individuals
    * [Ray Wallet](https://raywallet.app/) - The easiest way to start using RGB.
    * [MyCitadel](https://mycitadel.io/) - Ultimate digital sovereignity
    * [Iris Wallet](https://play.google.com/store/apps/details?id=com.iriswallet.testnet&pli=1) - Iris Wallet supports RGB asset
* Trading Platform
    * [DIBA](https://diba.io) - DIBA is the first marketplace on Bitcoin using RGB Smart Contract Protocol and Lightning Network to exchange UDAs.
    * [Kaleidoswap](https://kaleidoswap.com/) - Kaleidoswap is the first decentralized RGB Protocol trading platform in  desktop application.
    * [LNFI](https://lnfi.network/) - An all-in-one financial layer for Taproot + RGB Assets
    * [Spectrum](https://t.me/RGBSpectrumBot) - RGB made easy, available via Telegram
* Cloud Service
    * [Thunderstack](https://thunderstack.org/) - The first Bitcoin-native infrastructure provider offering robust APIs and developer tools to build on cutting-edge Bitcoin Layers.
 
## Projects

*List of all projects built on top of RGB. Either RGB20 or RGB21.*

* Token
    * [PPRGB](https://pepe-rgb.wtf/) - $PPRGB is the first memecoin on RGB Protocol
    * [Bitman](https://bitman.city/) - BitMan is pure art
    * [CatRGB](https://x.com/TheCATRGB) - The RGB cat memecoin. Just meow 🐾.
    * [Single Use Seals](https://x.com/Single_Use_Seal) - Single-Use-Seals are cryptographic primitives & pinnipeds.
* UDA
    * [RGB GOAT](https://x.com/trydiba/status/1824461482249093292) - Cipher GOATs by DIBA.

# Resources

Where to discover learning resources or where you can find more people and talk about it.

## Social Media

* [RGB Community](https://t.me/@rgbtelegram)
* [Bitmask](https://t.me/@rgbtelegram)
* [Bitlight Labs](https://t.me/@rgbtelegram)

## Influencers

* [Dapangdun](https://x.com/DaPangDunCrypto) - Famous KOL in China, focus on RGB
* [Pepper](https://x.com/off_thetarget) - Focus on memecoins

## Websites

* [RGB Tech](https://rgb.tech/)
* [RGB Info](https://rgb.info/)
* [RGB Protocol](https://github.com/rgb-protocol)
* [RGB WG](https://github.com/RGB-WG)
* [RGB Tools](https://github.com/rgb-tools)
* [LNP/BP](https://www.lnp-bp.org/)


# Awesome lists

*List of all awesome lists that I maintain*

* [Awesome Taproot Assets](https://github.com/afeezaziz/awesome-taproot-asset)
* [Awesome AI](https://github.com/afeezaziz/awesome-ai)

# Contributing

Your contributions are always welcome! Please take a look at the [contribution guidelines](https://github.com/afeezaziz/awesome-rgb/blob/main/CONTRIBUTING.md) first. If you have any question about this opinionated list, do not hesitate to contact me [@AfeezAziz](https://twitter.com/AfeezAziz) on Twitter or open an issue on GitHub.

# Contributors

<a align="center" href="https://github.com/afeezaziz/awesome-rgb/graphs/contributors">
  <img src="https://contrib.rocks/image?repo=afeezaziz/awesome-rgb" />
</a>
