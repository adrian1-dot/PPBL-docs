##Recommended Hardware
We recommend a minimum hardware configuration of 16 GB RAM, 2 processors and 100 GB free hard disk space.

##Setting up NIX and Haskell 
The following steps should get your environment set up without hassles. The instructions assume that you have a Linux based OS. If you are on Windows, 
you can set up WSL2 on your machine and then follow the steps below.

1. Set up nix 
    Create a directory /etc/nix if it does not exist
    Install nix: [https://nixos.org/download.html](https://nixos.org/download.html) 

2. Install Haskell toolchain from [Haskell.org](https://www.haskell.org/downloads/) 

3. Install GHC, cabal-install and haskell-language-server using [ghcup](https://www.haskell.org/ghcup/) installer.

##Setting up the Plutus platform 
The Plutus Platform is an application development platform for developing distributed applications using the Cardano blockchain. This is similar 
to SDKs that you use in other programming languages. It includes the core libraries that are needed for writing Plutus smart contracts, the Haskell 
to Plutus compiler and the Plutus playground - a web based interactive playground. It also includes the toolchain for Marlowe, a domain-specific language 
for building smart contracts, which is not covered in this guidebook.

Clone the [plutus-apps](https://github.com/input-output-hk/plutus-apps) repository . Follow the steps for building the project. Remember to add 
the IOHK binary cache to your Nix configuration as  mentioned, as that speeds up the build. 

##Plutus Starter project
This project will give you a feel of how a smart contract executable works. Running the example in this project first before you read the code or watch the lectures, will 
help visualise how things work and can work as you progress. This is especially the case if you are a developer. To build and execute the starter project, it is sufficient 
that you have the environment setup done correctly. You do not need to write any code at this point. You can find IOHK’s plutus starter 
repository [here](https://github.com/input-output-hk/plutus-starter).
