# Rollups Deployment

This repository provides a procedure and documentation for building and deployment of Cartesi Rollups DApps to a cloud based execution infrastructure managed by the Cartesi team.

The process consists of the steps below, which are detailed in the following sections.

1. Build and publishing of the Cartesi Machine
2. Deployment of Rollups smart contract to a public network
3. Deployment of the DApp validator node to Cartesi infrastructure

![diagram](/diagram.jpg)

## Build and publishing of the Cartesi Machine

The first step of a Cartesi Rollups DApp development is to build an application using any supported programming language. This is covered in detail in the [Cartesi Documentation](https://docs.cartesi.io/build-dapps/overview).

The artifact produced by the build process of a DApp is a docker image which starts from the [cartesi/server-manager](https://hub.docker.com/repository/docker/cartesi/server-manager) image and adds the application Cartesi Machine.

The process of building such image is demonstrated in the [cartesi/rollups-examples](https://github.com/cartesi/rollups-examples#building) repository. The image of one of the examples, i.e. the [echo-python](https://github.com/cartesi/rollups-examples/tree/main/echo-python) example, can be created by running the command `docker buildx bake server`.

> Note: Docker buildx bake is provided by modern versions of [Docker Desktop](https://www.docker.com/products/docker-desktop/)

Once the docker image is produced locally it must be published to a public registry. This is done by running the command `docker push <image-name>`. Any public registry can be used, including [Docker Hub](https://hub.docker.com) or [GitHub Container Registry](https://github.com/features/packages).

## Deployment of Rollups smart contract to a public network

Each DApp must have a smart contract deployed to the blockchain it will use. This smart contract can be deployed by calling the `newApplication` method of the [CartesiDAppFactory](https://github.com/cartesi/rollups/blob/main/onchain/rollups/contracts/CartesiDAppFactory.sol).

The `CartesiDAppFactory` of [rollups 0.5.0](https://github.com/cartesi/rollups/releases/tag/v0.5.0) is already deployed to the following blockchains:

- Goerli: [0xDd08D2aDa2208815238EBE97D41Ff219ea129D39](https://goerli.etherscan.io/address/0xDd08D2aDa2208815238EBE97D41Ff219ea129D39)
- Polygon Mumbai: [0x7cB0A55687eC513B1836aC9eCbBEf79afe749A8C](https://mumbai.polygonscan.com/address/0x7cB0A55687eC513B1836aC9eCbBEf79afe749A8C)

The deployment procedure of a DApp smart contract is demonstrated in the cartesi/rollups-examples [README](https://github.com/cartesi/rollups-examples#deploying-dapps).

Once the deployment is completed the user must take a note of the address of the deployed DApp contract.

## Deployment of the DApp validator node to Cartesi infrastructure

After completing the steps above the developer can request the deployment of a validator node for his DApp.
This node will be deployed to the Cartesi infrastructure and mantained by the Cartesi team.

The developer must follow the steps below:

1. Fork this repository;
2. Add a directory for your DApp, and add a `<network>.yml` file in it, following the template below. Valid values for network are `goerli` and `polygon-mumbai`.
3. Open a pull request so the Cartesi team can review the deployment request.

This is an example of the `goerli.yml` file of the `echo-python` DApp with its Cartesi Machine docker image published at [Docker Hub](https://hub.docker.com/layers/dapp/cartesi/dapp/echo-python-0.8.0-server/images/sha256-4a34d7eabbecff1a8d8e2531e57acd620142b11401d089b980c0b935af016acc?context=repo) and deployed to the [Goerli](https://goerli.etherscan.io/address/0xAF500C1C68D412b6Fd18EDC190f45B98604E0697) network.

```yaml
dapp:
  image: docker.io/cartesi/dapp:echo-python-0.8.0-server
  contractAddress: "0xAF500C1C68D412b6Fd18EDC190f45B98604E0697"
```

After the review and approval of the pull request by the Cartesi Team the DApp validator node will be deployed and be available at a URL with the following format:

> https://<`address`>.<`network`>.rollups.[`env`].cartesi.io

- `address`: address of the deployed smart contract
- `network`: one of the supported networks: [`goerli`, `polygon-mumbai`]
- `env`: one of the currently supported environments: [`dev`, `staging`, `prod` (empty)]

As an example the `echo-python` DApp above, when deployed to the staging environment of Goerli would be accessible at https://0xaf500c1c68d412b6fd18edc190f45b98604e0697.goerli.rollups.staging.cartesi.io/graphql.

## Published DApps

Cartesi is initially running Cartesi nodes for decentralized applications. In the future, other node runners will also run their nodes and validate Cartesi Rollups DApps. The implementation and hosting of a frontend is not in the scope of what is done by the Cartesi team. Find below the first hacks implemented and hosted by the community:

- Calculator: https://web3calculator-stg.surge.sh/
- Iris flower ML classification: https://irisclassifier-stg.surge.sh/
- Titanic survival ML model: https://titanicsurvivor-stg.surge.sh/
