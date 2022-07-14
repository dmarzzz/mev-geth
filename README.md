# MEV-geth

This is a fork of go-ethereum, [the original README is here](README.original.md).

Flashbots is a research and development organization formed to mitigate the negative externalities and existential risks posed by miner-extractable value (MEV) to smart-contract blockchains. We propose a permissionless, transparent, and fair ecosystem for MEV extraction that reinforce the Ethereum ideals.

## Quick start

```
git clone https://github.com/flashbots/mev-geth
cd mev-geth
make geth
```

See [here](https://geth.ethereum.org/docs/install-and-build/installing-geth#build-go-ethereum-from-source-code) for further info on building MEV-geth from source.

## Documentation

See [here](https://docs.flashbots.net) for Flashbots documentation.

| Version | Spec                                                                                        |
| ------- | ------------------------------------------------------------------------------------------- |
| v0.6    | [MEV-Geth Spec v0.6](https://docs.flashbots.net/flashbots-auction/miners/mev-geth-spec/v06) |
| v0.5    | [MEV-Geth Spec v0.5](https://docs.flashbots.net/flashbots-auction/miners/mev-geth-spec/v05) |
| v0.4    | [MEV-Geth Spec v0.4](https://docs.flashbots.net/flashbots-auction/miners/mev-geth-spec/v04) |
| v0.3    | [MEV-Geth Spec v0.3](https://docs.flashbots.net/flashbots-auction/miners/mev-geth-spec/v03) |
| v0.2    | [MEV-Geth Spec v0.2](https://docs.flashbots.net/flashbots-auction/miners/mev-geth-spec/v02) |
| v0.1    | [MEV-Geth Spec v0.1](https://docs.flashbots.net/flashbots-auction/miners/mev-geth-spec/v01) |

### Feature requests and bug reports

If you are a user of MEV-Geth and have suggestions on how to make integration with your current setup easier, or would like to submit a bug report, we encourage you to open an issue in this repository with the `enhancement` or `bug` labels respectively. If you need help getting started, please ask in the dedicated [#⛏️miners](https://discord.gg/rcgADN9qFX) channel in our Discord.
[geth readme](README.original.md)

# Builder API

Builder API implementing [builder spec](https://github.com/ethereum/builder-specs), making geth into a standalone block builder. 

Run on your favorite network, including Kiln and local devnet.

Requires forkchoice update to be sent for block building, on public testnets run beacon node modified to send forkchoice update on every slot [example modified beacon client (lighthouse)](https://github.com/flashbots/lighthouse)

Test with [mev-boost](https://github.com/flashbots/mev-boost) and [mev-boost test cli](https://github.com/flashbots/mev-boost/tree/main/cmd/test-cli).

Provides summary page at the listening address' root (http://localhost:28545 by default).

## How it works

Builder API has two hooks into geth:
* On forkchoice update, changing the payload attributes feeRecipient to the one registered for next slot's validator
* On new sealed block, consuming the block as the next slot's proposed payload

## Limitations

* Blocks are only built on forkchoice update call from beacon node
* Only works post-Bellatrix, fork version is static
* Does not accept external blocks
* Does not have payload cache, only the latest block is available

## Usage

Configure geth for your network, it will become the block builder.

Builder API options:
```
$ geth --help
BUILDER API OPTIONS:
  --builder.validator_checks               Enable the validator checks
  --builder.secret_key value               Builder API key used for signing headers (default: "0x2fc12ae741f29701f8e30f5de6350766c020cb80768a0ff01e6838ffd2431e11") [$BUILDER_SECRET_KEY]
  --builder.listen_addr value              Listening address for builder endpoint (default: ":28545") [$BUILDER_LISTEN_ADDR]
  --builder.genesis_fork_version value     Gensis fork version (default: "0x02000000") [$BUILDER_GENESIS_FORK_VERSION]
  --builder.bellatrix_fork_version value   Bellatrix fork version (default: "0x02000000") [$BUILDER_BELLATRIX_FORK_VERSION]
  --builder.genesis_validators_root value  Genesis validators root of the network (static). For kiln use 0x99b09fcd43e5905236c370f184056bec6e6638cfc31a323b304fc4aa789cb4ad (default: "0x0000000000000000000000000000000000000000000000000000000000000000") [$BUILDER_GENESIS_VALIDATORS_ROOT]
  --builder.beacon_endpoint value          Beacon endpoint to connect to for beacon chain data (default: "http://127.0.0.1:5052") [$BUILDER_BEACON_ENDPOINT]
```
