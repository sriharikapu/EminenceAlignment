![HeaderImage](https://github.com/emcd173/EminenceAlignment/blob/master/Memoir%20Logo.PNG)

# Memoir: A Trustless Digital Time Capsule

<b>Memoir</b> is a web3 dApp that enables content creators to timelock their creations for a predetermined period of time, in a manner similiar to a time-capsule.

By creating a decentralized time-capsule protocol that deterministically locks any digital asset for a period of time, we: 
  1. <b>Remove</b> the need of need of a third-party trusted custodian to hold a decryption key and keep it secret
  2. <b>Enable</b> a proof-of-content creation timestamping mechanism, where the content becomes publically available on after N-period of time
  3. <b>Connect</b> encrypted, content-permanent web objects (ala IPFS) to an ethereum-driven timelock mechanism

Memoir is a novel approach to timelocking secrets of any type.  We built the protocol because of the social need for reliable digital timelocked obfuscation and preservation technique.  We see real economic value in removing the costs associated with third-party custodians and the risk of them defaulting on a promise or contract.


## How it works

On the front-end, the user uploads a file (this can be any form of content, see below for examples) and inputs a set time period for which they'd like their file to be encrypted.  The user may also optionally input a string up to 240 charecters as a public description, etc.

The web3 uploader calls a private function which generates an ECDSA keypair.  They public key is emitted to the client and used to encrypt the file.  The encrypted file is uploaded onto the Interplanetary File System.  The IPFS address for the encrypted file, the description string, and time-lock period are ingested as parameters into the Amsterdam.sol struct.  The private key to decrypt the file remains private for a predetermined period of time.

The parameters taken by the Amsterdam.sol smart-contract is summarized below:

'''
// Data identifying the file, location, and attributes for front-end
  struct Entry {
    uint id;
    uint unlockTime;
    address owner;
    string ipfs;
    string title;
    string descrip;
    uint entryType;
    uint[] enc_msg;
  }

// Seperate struct for key storage
  struct PK {
      uint id;
      uint[] key;
  }
'''

A visual diagram outling the process may be seen below:

![Diagram](https://github.com/emcd173/EminenceAlignment/blob/master/Diagram2.PNG)


## Usage Scenarios

We envsion a wide array of use cases for Memoir, for example:
1. <i><b>Personal Diary</b></i> - An individual may wish to conceal their writing (such as a novel, diary, personal letter) from adversaries - while still proving that they wrote it during a given period in time.  For example, a political refugee deprived of free speech may wish to detail their wartime experience while deflecting risk associated speaking out against an authority.  
2. <i><b>Music</b></i> - An musician, at the peak of their notereiety, might wish to lock up a song for a 100 years - so that it is not released to the public unti after their death.  Imagine if a song from Beethoven, the Beatles, or Biggie was timelocked through history and released today.
3. <i><b>Public Cryptowill</b></i> - An individual may wish to timelock a private key connected to a bitcoin wallet for a given period of time, so that the funds are not released to the public until a given point in the future.
4. <i><b>Message to Future Self</b></i> - Memoir provides a secure and trustless way for indviduals to send messages to their future selves.  A college freshman, for example, may wish to send a digital message to his self upon graduation.  this protocol ensures that the message will not be lost, destroyed, etc.
5. <i><b>Programmatic gaming incentives</b></i> - A game developer, looking to maximize the time-horizon for the enjoyment of a game, may wish to time-locked digital assets (possibly represented as an NFT) and programatically release them into the gaming universe over a given period of tume.  This ensures that the game incentives continue to evolve - keeping the game engaging long after the Creator has moved on.
6. <i>Classified Government Disclosures</i> - Today, the government may choose to release classified material to the public after a given period of time - such as the JFK Files, etc.  However, the public has no proof that these files haven't been altered, tampeered, with, etc. since they were created.  Memoir creates the underpinnings of a better declassification system for classified government material, allowing the public to verify file consistency since a given point in time.


## Potential Vulnerabilites

There are some issues with storing a decryption key on a publically distributed blockchain ledger.  While the key itself exists as a private variable, annd cannot be called by other contracts, it is still recorded in bytecode on the EVM.  An attacker could theoretically attempt to brute force the blockchain dataset for the key binary, but he would have to know the exact length, as well as the IPFS address for the file (which is also stored as a private variable)


### Depreciated Design Iterations

Initially, we envisioned a symetric key schema where the web3 uploader encrypts the uploaded file with a symetric key and uploads the encrypted file onto the Interplanetary File System.  The decryption key, a One-time Pad (OTP) is passed to the Amsterdam.sol smart-contract as a private, time-locked variable along with the IPFS address.  In addition, web3 ingests the description string inputted by the user at the time of upload into Amsterdam.sol.  

However, we realized that this created a few insurmountable vulnerabilities.  First, an <b>"in-transit" vulnerability</b> where the OTP might be intercepted at the client level, this risk could be obfuscated through encryption-in-transit (such as TLS, HTTPS, etc). However, this did not resolve the <b>"exit node" vulnerability</b> - as the exit node making the RPC-call would still have access to the key in plaintext.

A visual diagram outling this first iteration was envisioned as below:

![Memoir Diagram](https://github.com/emcd173/EminenceAlignment/blob/master/MemoirDiagram.PNG)
