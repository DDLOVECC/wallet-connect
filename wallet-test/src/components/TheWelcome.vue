<template>
  <div class="all">
    <div class="greetings">
      <h3>
        You’ve successfully created a project with
      </h3>
      <h1>
        <w3m-core-button></w3m-core-button>
      </h1>
      <h1>
        <w3m-network-switch></w3m-network-switch>
      </h1>
    </div>
    <div class="user">
      <h1>获取地址：{{address}}</h1>
      <h1>
        <button @click="getAddress()">getAddress</button>
      </h1>
    </div>
    <div class="mint">
      <h2>tokenId：<input v-model="tokenId"></h2> <br/>
      <h2>amount：<input v-model="amount"></h2><br/>
      <h2>tokenData：<input v-model="tokenData"></h2><br/>
      <button @click="mintUseWagemi">mint</button>
    </div>
  </div>

</template>
<script>
  import {EthereumClient, w3mConnectors, w3mProvider} from '@web3modal/ethereum'
  import {Web3Modal} from '@web3modal/html'
  import {configureChains, createConfig} from '@wagmi/core'
  import {zkSyncTestnet, mainnet, zkSync} from '@wagmi/core/chains'
  import {getAccount} from '@wagmi/core'
  import {providers, utils, Contract} from 'ethers'
  import {prepareWriteContract, writeContract} from '@wagmi/core'

  const CONTRACT_ADDRESS = "0xD14f4B9FD4d788E027b360A4536E8Ada9dEd0d44";
  // eslint-disable-next-line
  import CONTRACT_ABI from "../abi.json";

  const chains = [zkSyncTestnet, zkSync, mainnet]
  const projectId = '0199eff1e4cb61271f7a6401c397bbca'

  const {publicClient} = configureChains(chains, [w3mProvider({projectId})])
  const wagmiConfig = createConfig({
    autoConnect: true,
    connectors: w3mConnectors({projectId, chains}),
    publicClient
  })
  const ethereumClient = new EthereumClient(wagmiConfig, chains)
  const web3modal = new Web3Modal({projectId}, ethereumClient);

  export default {
    data() {
      return {
        address: "",
        tokenId: "",
        amount: "",
        tokenData: "",
        contract: ""
      }
    },
    methods: {
      async mintUseWagemi() {
        const config = await prepareWriteContract({
          address: CONTRACT_ADDRESS,
          abi: CONTRACT_ABI,
          functionName: 'mint',
          args: [this.address, this.tokenId, this.amount, this.tokenData],
        })
        const hash = await writeContract(config);
        console.log("hash:{}", hash);
      },
      async getAddress() {
        this.address = await getAccount().address;
      },
      async mint() {
        this.initContract();
        let result = await this.contract.mint(this.address, this.tokenId, this.amount, this.tokenData);
        console.log("result:" + JSON.stringify(result));
      },
      initContract() {
        const provider = new providers.JsonRpcProvider("https://testnet.era.zksync.dev")
        const signer = provider.getSigner(this.address);
        //creating contract instance and invoking contract function
        this.contract = new Contract(
                CONTRACT_ADDRESS,
                CONTRACT_ABI,
                signer
        );
        console.log("contract:{}", this.contract)
      },
      async initContractTwo() {
        const web3modal = new Web3Modal({projectId}, ethereumClient);
        let instance = await web3modal.connect();
        //creating etherjs instance from wallet connect
        let provider = new providers.Web3Provider(instance);
        console.log("injected:{}", provider)
        const signer = provider.getSigner();
        console.log("signer:{}", signer)
        this.contract = new Contract(
                CONTRACT_ADDRESS,
                CONTRACT_ABI,
                signer
        );
        console.log("contract:{}", this.contract)
      }
    }
  }
</script>
<style scoped>
  h1 {
    font-weight: 500;
    font-size: 2.6rem;
    position: relative;
    top: -10px;
  }

  h3 {
    font-size: 1.2rem;
  }

  .greetings {
    text-align: center;
    border-style: solid;
  }

  .all {
    display: block;
    flex-direction: row;
  }

  @media (min-width: 1024px) {
    .greetings h1,
    .greetings h3 {
      text-align: center;
    }
  }

  .user {
    margin-top: 10px;
    border-style: solid;
    padding: 20px;
  }

  .mint {
    margin-top: 10px;
    border-style: solid;
    padding: 20px;
  }
</style>
