<template>
    <div class="all">
        <div class="greetings">
            <h2>
                Welcome to the web3
            </h2>
            <h1>
                <w3m-core-button></w3m-core-button>
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
    import {configureChains, createConfig} from '@wagmi/core'
    import {zkSyncTestnet, mainnet, zkSync} from '@wagmi/core/chains'
    import {getAccount} from '@wagmi/core'
    import {prepareWriteContract, writeContract} from '@wagmi/core'

    const CONTRACT_ADDRESS = "0xD14f4B9FD4d788E027b360A4536E8Ada9dEd0d44";
    import CONTRACT_ABI from "../abi.json";

    const chains = [zkSyncTestnet, zkSync, mainnet]
    const projectId = '0199eff1e4cb61271f7a6401c397bbca';

    //const {publicClient} = configureChains(chains, [w3mProvider({projectId})])
    // const wagmiConfig = createConfig({
    //     autoConnect: true,
    //     connectors: w3mConnectors({projectId, chains}),
    //     publicClient
    // })
    // const ethereumClient = new EthereumClient(wagmiConfig, chains)
    //const web3modal = new Web3Modal({projectId}, ethereumClient);

    export default {
        data() {
            return {
                tokenId: "",
                amount: "",
                tokenData: "",
                contract: ""
            }
        },
        methods: {
            async mintUseWagemi() {
                const address = await getAccount().address
                const config = await prepareWriteContract({
                    address: CONTRACT_ADDRESS,
                    abi: CONTRACT_ABI,
                    functionName: 'mint',
                    args: [address, this.tokenId, this.amount, this.tokenData],
                })
                const hash = await writeContract(config);
                console.log("hash:{}", hash);
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
