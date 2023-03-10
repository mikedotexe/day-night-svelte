<script>
    import {browser} from "$app/environment";
    import {onMount} from "svelte";
    import sun from '$lib/images/sun.svg';
    import moon from '$lib/images/moon.svg';
    import { CosmWasmClient } from "@cosmjs/cosmwasm-stargate"
    import {env} from "$env/dynamic/public";
    import {dayNightToggle} from "./body-toggle.ts";
    import {bounceIn} from 'svelte/easing';

    let booleanValue
    let hardcoded = false

    const updateBooleanFromContract = async () => {
        try {
            const cwClient = await CosmWasmClient.connect(env.PUBLIC_ENDPOINT || 'https://rpc.elgafar-1.stargaze-apis.com:443')

            const currentValue = await cwClient.queryContractSmart(env.PUBLIC_BOOLEAN_ADDRESS || 'stars19869yl5mz65zp8hth3fmfhglatcmq5zv30eaq0j3my2q7rxqc44s5h2lhx', {
                "get_value": {}
            })
            if (currentValue !== booleanValue && browser) dayNightToggle()
            booleanValue = currentValue
        } catch (e) {
            console.warn('Ran into error querying. Show must go on…', e)
        }
    }

    onMount(async () => {
        await updateBooleanFromContract()
    })

    const checkContractBoolean = async () => {
        /*
        check value:
        starsd q wasm contract-state smart stars19869yl5mz65zp8hth3fmfhglatcmq5zv30eaq0j3my2q7rxqc44s5h2lhx '{"get_value":{}}'

        execute proxy_call:
        starsd tx wasm execute stars1wm05lj5j5h6hca6y3nrm0j4zkjhh559afplmhrtmn8nrfjttjkkst80e7f '{"proxy_call":{}}' --gas-prices 0.025ustars --gas-adjustment 1.7 --gas auto -b block --from owner -o json -y | jq | head -n 42
        */
        await updateBooleanFromContract()

        hardcoded = !hardcoded
	}

    let clear = null

    $: {
        clearInterval(clear)
        clear = setInterval(checkContractBoolean, env.PUBLIC_DELAY || 3000)
    }

    function sunTransition(node, { duration }) {
        return {
            duration,
            css: t => {
                const inTiming = bounceIn(t)
                const o = +getComputedStyle(node).opacity;
                return `
					opacity: ${t * o};
					transform: rotate(${inTiming}turn) scale(${t});
                `
            }
        };
    }

    function moonTransition(node, { duration }) {
        return {
            duration,
            css: t => {
                const o = +getComputedStyle(node).opacity;
                return `
					opacity: ${t * o};
					transform: scale(${t}) skew(${(t * 66) - 66}deg, ${(t * 19) - 19}deg);
                `
            }
        };
    }
</script>

<svelte:head>
    <title>CronCat cycle</title>
    <meta name="description" content="CronCat day/night cycle"/>
</svelte:head>

<section>
    <!--{#if hardcoded === true}-->
    {#if booleanValue === true}
        <object type="image/svg+xml" data={moon} class="svg" in:moonTransition={{duration: 666}} title="moon"></object>
    {:else}
        <object type="image/svg+xml" data={sun} class="svg" in:sunTransition={{duration: 666}} title="sun"></object>
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

    .svg {
        width: 100%;
    }
</style>
