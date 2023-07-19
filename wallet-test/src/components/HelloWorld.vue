<template>
    <div class="all">
        <div class="connect">
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
        <div class="approveAll">
            <div class="approve">
                <h2 class="usdt">
                    质押 USDT
                </h2>
                <h2>质押目标地址(质押授权给谁)：<input v-model="approve_address"></h2> <br/>
                <h2>质押数量：<input v-model="approve_amount"></h2><br/>
                <button @click="approve">approve</button>
            </div>
        </div>
        <div class="transfer">
            <div class="approve">
                <h2 class="usdt">
                    分配质押 USDT
                </h2>
                <h2>授权地址：<input v-model="from_address"></h2> <br/>
                <h2>接受者地址：<input v-model="to_address"></h2><br/>
                <h2>分配数量：<input v-model="share_amount"></h2><br/>
                <button @click="allocation">allocation</button>
            </div>
        </div>
        <div class="read">
            <div class="approve">
                <h2 class="usdt">
                    读取 USDT
                </h2>
                <button @click="read">read</button>
            </div>
        </div>
    </div>

</template>
<script>
    import {EthereumClient, w3mConnectors, w3mProvider} from '@web3modal/ethereum'
    import {configureChains, createConfig} from '@wagmi/core'
    import {zkSyncTestnet, mainnet, zkSync} from '@wagmi/core/chains'
    import {getAccount} from '@wagmi/core'
    import {prepareWriteContract, writeContract,readContract} from '@wagmi/core'
    import { erc20ABI } from '@wagmi/core'
    const CONTRACT_ADDRESS = "0xD14f4B9FD4d788E027b360A4536E8Ada9dEd0d44";
    import CONTRACT_ABI from "../abi.json";

    const CONTRACT_ADDRESS_USDT = "0x0E2f0A595D7A213440e30de76E0dA4426203Cf04";
    import USDT_CONTRACT_ABI from "../aiyueUSDT.json";
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
                contract: "",
                approve_address: "",
                approve_amount: "",
                from_address: "",
                to_address: "",
                share_amount: ""
            }
        },
        methods: {
            async mintUseWagemi() {
                const address = await getAccount().address;
                const config = await prepareWriteContract({
                    address: CONTRACT_ADDRESS,
                    abi: CONTRACT_ABI,
                    functionName: 'mint',
                    args: [address, this.tokenId, this.amount, this.tokenData],
                })
                const hash = await writeContract(config);
                console.log("hash:{}", hash);
            },
            async approve() {
                const config = await prepareWriteContract({
                    address: CONTRACT_ADDRESS_USDT,
                    abi: USDT_CONTRACT_ABI,
                    functionName: 'approve',
                    args: [this.approve_address, this.approve_amount],
                })
                const hash = await writeContract(config);
                console.log("hash:{}", hash);
            },
            async allocation() {
                const config = await prepareWriteContract({
                    address: CONTRACT_ADDRESS_USDT,
                    abi: USDT_CONTRACT_ABI,
                    functionName: 'transferFrom',
                    args: [this.from_address, this.to_address, this.share_amount]
                })
                const hash = await writeContract(config);
                console.log("hash:{}", hash);
            },
           async read(){
               const address = await getAccount().address;
               console.log("address:{}", address);
                const data = await readContract({
                    address: CONTRACT_ADDRESS_USDT,
                    abi: USDT_CONTRACT_ABI,
                    functionName: 'balanceOf',
                    args: [address]
                });
               console.log("data:{}", data);
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
        display: flex;
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

    .usdt {
        text-align: center;
    }

    .approveAll {
        border-style: solid;
        padding: 20px;
        margin-left: 20px;
    }
    .transfer {
        border-style: solid;
        padding: 20px;
        margin-left: 20px;
    }
    .read{
        border-style: solid;
        padding: 20px;
        margin-left: 20px;
    }
</style>
