<script>
    import sun from '$lib/images/sun2.svg';
    import moon from '$lib/images/moon2.svg';
    import { CosmWasmClient } from "@cosmjs/cosmwasm-stargate"
    import {env} from "$env/dynamic/public";

    let booleanValue
    const checkContractBoolean = async () => {
        /*
        starsd q wasm contract-state smart stars19869yl5mz65zp8hth3fmfhglatcmq5zv30eaq0j3my2q7rxqc44s5h2lhx '{"get_value":{}}'

        execute proxy_call

        starsd tx wasm execute stars1wm05lj5j5h6hca6y3nrm0j4zkjhh559afplmhrtmn8nrfjttjkkst80e7f '{"proxy_call":{}}' --gas-prices 0.025ustars --gas-adjustment 1.7 --gas auto -b block --from owner -o json -y | jq | head -n 42

         */

        try {
            const cwClient = await CosmWasmClient.connect(env.PUBLIC_ENDPOINT || 'https://rpc.elgafar-1.stargaze-apis.com:443')
            booleanValue = await cwClient.queryContractSmart(env.PUBLIC_BOOLEAN_ADDRESS || 'stars19869yl5mz65zp8hth3fmfhglatcmq5zv30eaq0j3my2q7rxqc44s5h2lhx', {
                "get_value": {}
            })
        } catch (e) {
            console.warn('Ran into error querying. Show must go on…', e)
        }
		// console.log('aloha booleanValue', booleanValue)
	}

    let clear = null

    $: {
        clearInterval(clear)
        clear = setInterval(checkContractBoolean, env.PUBLIC_DELAY || 3000)
    }
</script>

<svelte:head>
    <title>CronCat cycle</title>
    <meta name="description" content="CronCat day/night cycle"/>
</svelte:head>

<section>
    {#if booleanValue === true}
        <object type="image/svg+xml" data={moon} class="svg"></object>
    {:else}
        <object type="image/svg+xml" data={sun} class="svg"></object>
    {/if}
</section>

<style>
    section {
        display: flex;
        flex-direction: column;
        justify-content: center;
        align-items: center;
        flex: 0.6;
    }

    h1 {
        width: 100%;
    }

    .svg {
        width: 100%;
    }
</style>
