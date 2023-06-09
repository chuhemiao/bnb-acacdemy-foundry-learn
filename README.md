# install foundry

Foundry 是一个智能合约开发工具链。

Foundry 管理您的依赖关系、编译项目、运行测试、部署，并允许您通过命令行和 Solidity 脚本与链交互。

## 安装 foundry

`curl -L https://foundry.paradigm.xyz | bash`

`foundryup` 需要把环境变量加入到 bash `source /Users/kk/.zshrc`

## 新建一个项目

`$ forge init hello_foundry_kk`

构建和测试: `forge build & forge test`

如果是旧项目,直接拉代码后: `forge install`

## 使用库

安装一个新的库: `forge install transmissions11/solmate`

这将拉取 solmate 库，在 git 中暂存 .gitmodules 文件并使用消息“Installed solmate”进行提交。需要先初始化 git

重新安装依赖: `forge remappings`

## 目录说明

- 您可以使用 foundry.toml 配置 Foundry 的行为。

- 重新映射在 remappings.txt 中指定。

- 合约的默认目录是 src/。

- 测试的默认目录是 test/，其中任何具有以 test 开头的函数的合约都被视为测试。

- 依赖项作为 git 子模块存储在 lib/ 中。

## Forge

Forge 是 Foundry 附带的命令行工具。 Forge 可用来测试、构建和部署您的智能合约。

gas 生成报告: `forge test --gas-report`

gas 快照,可以对比 gas 的使用情况: `forge snapshot`

查看 gas 快照: `cat .gas-snapshot`

## 部署 token 和验证

```
forge create --rpc-url <your_rpc_url> \
    --constructor-args "ForgeUSD" "FUSD" 18 1000000000000000000000 \
    --private-key <your_private_key> src/MyToken.sol:MyToken \
    --etherscan-api-key <your_etherscan_api_key> \
    --verify
```

// watch 可以用来监测和轮询验证结果

```
$ forge verify-contract --chain-id 42 --num-of-optimizations 1000000 --watch --constructor-args \
    $(cast abi-encode "constructor(string,string,uint256,uint256)" "ForgeUSD" "FUSD" 18 1000000000000000000000) \
    --compiler-version v0.8.10+commit.fc410830 <the_contract_address> src/MyToken.sol:MyToken <your_etherscan_api_key>

```

## Cast 概述

Cast 是 Foundry 用于执行以太坊 RPC 调用的命令行工具。 您可以进行智能合约调用、发送交易或检索任何类型的链数据——所有这些都来自您的命令行！

```
cast call 0x6b175474e89094c44da98b954eedeac495271d0f "totalSupply()(uint256)" --rpc-url https://eth-mainnet.alchemyapi.io/v2/Lc7oIGYeL_QvInzI0Wiu_pOZZDEKBrdf
8603853182003814300330472690
```

## Anvil 概述

Anvil 是 Foundry 附带的本地测试网节点。 您可以使用它从前端测试您的合约或通过 RPC 进行交互。

启动本地测试节点: `anvil`

```
forge script script/token.s.sol:TokenScript --fork-url http://localhost:8545 --private-key ac0974bec39a17e36ba4a6b4d238ff944bacb478cbed5efcae784d7bf4f2ff80 --broadcast
```

```
ONCHAIN EXECUTION COMPLETE & SUCCESSFUL.
Total Paid: 0.004578644 ETH (1144661 gas * avg 4 gwei)

Transactions saved to: /Users/kk/icourse/hello_foundry_kk/broadcast/token.s.sol/31337/run-latest.json
```

保存部署后的 token 合约变量: `export TOKEN_ADDRESS="0x5fbdb2315678afecb367f032d93f642f64180aa3"`

`export LOCAL_ACCOUNT="ac0974bec39a17e36ba4a6b4d238ff944bacb478cbed5efcae784d7bf4f2ff80"`

`cast call $TOKEN_ADDRESS "name()(string)"`

`cast call $TOKEN_ADDRESS "symbol()(string)"`

`cast call $TOKEN_ADDRESS "decimals()(uint8)"`

`cast send --value 0.0001ether --private-key $LOCAL_ACCOUNT $TOKEN_ADDRESS "mintTo(address)" 0xf39fd6e51aad88f6f4ce6ab8827279cfffb92266 -j`

## vscode 配置

```
{
   "solidity.packageDefaultDependenciesContractsDirectory": "src",
   "solidity.packageDefaultDependenciesDirectory"："lib"
}
```

## 部署

- 读取.env 文件内的内容并将其保存为环境变量

`source .env`

- 与部署到本地网络类似，使用命令部署到以太坊中

`forge script script/token.s.sol:TokenScript --rpc-url $ROPSTEN_RPC_URL  --private-key $PRIVATE_KEY --broadcast --verify --etherscan-api-key $ETHERSCAN_KEY -vvvv`
