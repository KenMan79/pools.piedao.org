<script>
  import { onMount } from 'svelte';
  import { ethers } from 'ethers';
  import images from '../../config/images.json';
  import WhiteBox from '../../components/elements/WhiteBox.svelte';

  import Meta from '../../components/elements/meta.svelte';
  import { balances, balanceKey, eth } from '../../stores/eth.js';

  import { getTokenImage, subscribeToBalance, toFixed } from '../../components/helpers.js';

  import Modal from '../../components/elements/Modal.svelte';
  import LiquidityModal from '../../components/modals/LiquidityModal.svelte';
  import OvenModal from '../../components/modals/OvenModal.svelte';
  import TooltipButton from '../../components/elements/TooltipButton.svelte';
  import { fetchOvensUserData } from '../../helpers/multicall';
  import Accordion from '../../components/elements/Accordion.svelte'
  import AccordionGroup from '../../components/elements/AccordionGroup.svelte'


  $: ovens = [
    {
      addressOven: '0x1d616dad84dd0b3ce83e5fe518e90617c7ae3915',
      deprecated: false,
      name: 'DEFI++ Oven',
      description: 'Bakes DEFI++ at Zero cost',
      data: {
        ethBalance: 0,
        pieBalance: 0,
      },
      baking: {
        symbol: 'DEFI++',
        address: '0x8d1ce361eb68e9e05573443c407d4a3bed23b033',
        balance: '0',
        icon: getTokenImage('0x8d1ce361eb68e9e05573443c407d4a3bed23b033'),
      },
      highlight: true,
      enabled: true,
    },
    {
      addressOven: '0xE3d74Df89163A8fA1cBa540FF6B339d13D322F61',
      deprecated: false,
      name: 'BCP Oven',
      description: 'Bakes BCP at Zero cost',
      data: {
        ethBalance: 0,
        pieBalance: 0,
      },
      baking: {
        symbol: 'BCP',
        address: '0xe4f726adc8e89c6a6017f01eada77865db22da14',
        balance: '0',
        icon: getTokenImage('0xe4f726adc8e89c6a6017f01eada77865db22da14'),
      },
      highlight: true,
      enabled: true,
    },
    {
      addressOven: '0xAedec86DeDe3DEd9562FB00AdA623c0e9bEEb951',
      deprecated: false,
      name: 'YPIE Oven',
      description: 'Bakes YPIE at Zero cost',
      data: {
        ethBalance: 0,
        pieBalance: 0,
      },
      baking: {
        symbol: 'YPIE',
        address: '0x17525e4f4af59fbc29551bc4ece6ab60ed49ce31',
        balance: '0',
        icon: getTokenImage('0x17525e4f4af59fbc29551bc4ece6ab60ed49ce31'),
      },
      highlight: true,
      enabled: true,
    },
    // {
    //   addressOven: '0x925f860d1596cc6383c16294d8290f82bde172f7',
    //   deprecated: true,
    //   name: 'YPIE Oven',
    //   description: 'Bakes YPIE at Zero cost',
    //   data: {
    //     ethBalance: 0,
    //     pieBalance: 0,
    //   },
    //   baking: {
    //     symbol: 'YPIE',
    //     address: '0x17525e4f4af59fbc29551bc4ece6ab60ed49ce31',
    //     balance: '0',
    //     icon: getTokenImage('0x17525e4f4af59fbc29551bc4ece6ab60ed49ce31'),
    //   },
    //   highlight: true,
    //   enabled: true,
    // },
  ];

  let modal;
  let modalAdd;
  let initialized = false;
  let modalOption = {
    title: "Bake",
    pieAddress: null,
    ovenAddress: null,
  };

  $: ovenData = null;

  $: (async () => {
    if($eth.address && !initialized) {
      ovenData = await fetchOvensUserData(ovens, $eth.address, $eth.provider);
      initialized = true;
    }
  })()

  onMount(() => {
    ovens.forEach((ov) => {
      subscribeToBalance(null, ov.addressOven);
      ov.KeyEthBalance = balanceKey(ethers.constants.AddressZero, ov.addressOven);
    });
  });

  function getPercetageCompletion(balance) {
    let percetage = ( parseFloat(balance) / 10) * 100;
    return toFixed(percetage > 100 ? 100 : percetage, 2); 
  }
  
</script>


<Meta
  metadata={{ title: 'Oven Page - PieDAO', description: "The PieDAO Oven pools ETH to issue pies in batches, giving users access to our index products gas-free. This design makes getting involved affordable and convenient, opening participation to everyone. Don't forget, the Oven won't activate until it's full!", image: images.oven_social, imageAlt: 'How the Oven makes minting pies gas-free.' }} />


<Modal title={modalOption.title} backgroundColor="#f3f3f3" bind:this={modal}>
  <span slot="content">
    <OvenModal
      deprecated={modal.deprecated}
      pieAddress={modal.pieAddress}
      ovenAddress={modal.ovenAddress} />
  </span>
</Modal>

<Modal title="Add Liquidity" backgroundColor="#f3f3f3" bind:this={modalAdd}>
  <span slot="content">
    <LiquidityModal
      token="0x8d1ce361eb68e9e05573443c407d4a3bed23b033"
      method="single"
      poolAction="add" />
  </span>
</Modal>
<section class="pt-4 px-4 text-center md:pt-8 lg:pt-12">
  <div class="w-full max-w-2xl mx-auto">
    <h2 class="text-xl mt-2 mb-2 leading-tight font-heading">Bake Together, save 97% Gas.</h2>
    <p class="mb-4 text-gray-500 font-thin text-md leading-6">
      Get hold of your pie almost gas-free by sharing the cost. <br class="hidden md:block" /> Turn your ETH into delicious
      pie in three easy steps.
    </p>
  </div>
</section>

<div class="w-100pc flex justify-center">
  <div class="flex flex-col md:max-w-1200px p-0 p-4 md:p-6 mx-4 md:mx-0 mb-4 items-center bg-lightgrey md:bg-white rounded">
    <div class="flex flex-col justify-between content-center lg:flex-row leading-5">

        <div class="flex flex-row md:flex-col w-100pc lg:w-1/3 md:min-h-150px items-center my-0 lg:m-10px p-0 md:p-20px">
          <img class="w-50px md:w-80px mr-4 md:mr-0" src={images.depositeth} alt="deposit eth" />
          <div class="flex flex-col text-left md:text-center md:mt-3">
              <div class="text-lg">Deposit ETH</div>
              <div class="font-thin mt-1 md:mt-2">When at least 10 ETH is deposited the Oven can begin.</div>
          </div>
        </div>

        <div class="flex flex-row md:flex-col w-100pc lg:w-1/3 md:min-h-150px items-center mt-4 md:my-0 lg:m-10px p-0 md:p-20px">
          <img class="w-50px md:w-80px mr-4 md:mr-0" src={images.waitoven} alt="wait oven" />
          <div class="flex flex-col text-left md:text-center md:mt-3">
              <div class="text-lg">Wait</div>
              <div class="font-thin mt-1 md:mt-2">Oven will bake when gas price is below 100 gwei, saving everyone money.</div>
          </div>
        </div>

        <div class="flex flex-row md:flex-col w-100pc lg:w-1/3 md:min-h-150px items-center mt-4 md:my-0 lg:m-10px p-0 md:p-20px">
          <img class="w-50px md:w-80px mr-4 md:mr-0 mb-2 md:mb-0" src={images.sharegascost} alt="share gas cost" />
          <div class="flex flex-col text-left md:text-center md:mt-3">
            <div class="text-lg">Withdraw Your Pie</div>
            <div class="font-thin mt-1 md:mt-2">Once the Pie is baked you can withdraw it to your wallet.</div>
          </div>
        </div>

    </div>
  </div>
</div>

<section class="pt-6 pb-2 text-center">
  <div class="w-full max-w-2xl mx-auto">
    <h2 class="text-lg mt-2 mb-0 leading-tight font-heading">Select Your Pie</h2>
  </div>
</section>

<div
  class="content spl flex flex-col justify-center justify-items-center content-center flex-wrap md:flex-row lg:flex-row">
  {#each ovens as oven}
    <div class="max300 my-10px lg:m-10px p-20px rounded-sm bg-white border-thin">
      <a class="flex justify-start mb-4 items-center" href={`#/pie/${oven.baking.address}`}>
        <img
          class="w-50px h-auto md:w-50px h-auto"
          src={oven.baking.icon}
          alt={oven.baking.symbol} />
        <span class="font-bold text-xl text-right ml-2">{oven.baking.symbol}</span>
      </a>
      <div class="flex justify-between my-2">
        <div class="flex items-center ">
          <span class="font-thin text-left mr-2">Minimum reached</span>
          <TooltipButton tooltip="Oven needs 10 ETH and low Gas Price to be activated">
            <img src={images.InfoIcon} class="" alt="info" width="16px" />
          </TooltipButton>
        </div>
        <div class="font-bold text-right rounded-sm bg-black w-40pc">
          <div
            style={`width: ${getPercetageCompletion($balances[oven.KeyEthBalance])}% !important`}
            class="px-2 py-1 rounded-sm text-xs bg-gradient-purple text-left text-white fit-content">
            {getPercetageCompletion($balances[oven.KeyEthBalance])}%
          </div>
        </div>
      </div>
      {#if ovenData}
      <div class="flex justify-between my-2">
        <span class="font-thin text-left">Your ETH in the Oven</span>
        <span class="font-bold text-right">{ovenData[oven.addressOven].ethBalance.label} ETH</span>
      </div>
      <div class="flex justify-between my-2">
        <span class="font-thin text-left">Your Pie ready</span>
        <span class="font-bold text-right">{ovenData[oven.addressOven].pieBalance.label} {oven.baking.symbol}</span>
      </div>
      {/if}
      <button
        on:click={() => {
          modal.pieAddress = oven.baking.address;
          modal.ovenAddress = oven.addressOven;
          modal.deprecated = oven.deprecated;
          modal.open();
        }}
        class="main-cta-ghost m-0 mt-4 rounded-8px p-15px w-full">
        Deposit / Withdraw
      </button>
    </div>
  {/each}
</div>

<section class="py-12 px-4 text-center">
  <div class="w-full max-w-2xl mx-auto">
    <h2 class="text-xl mt-2 mb-6 leading-tight font-heading">Can't wait?</h2>
    <p class="mb-4 text-gray-500 font-thin text-md leading-relaxed">
      You can always
      <strong>Buy </strong>
      your Pie instantly from the exchange page.
    </p>
  </div>
  <a href="#/swap">
    <button
      class="btn m-0 mt-4 rounded-8px p-15px min-w-200px w-96pc lg:w-200px lg:min-w-200px">
      Select your Pie
    </button>
  </a>
</section>

<section class="pt-12 px-4 text-center">
  <div class="w-full max-w-2xl mx-auto">
    <h2 class="text-xl mt-2 mb-6 leading-tight font-heading">How does the Oven work?</h2>
  </div>
</section>

<div class="w-100pc">
<img class="oven-illustration" width="1657px" height="918px" src={images.oven_illustration} alt="Oven Illustration"/>
</div>



<section class="pt-12 pb-6 px-4 text-center">
  <div class="w-full max-w-2xl mx-auto">
    <h2 class="text-xl mt-6 mb-3 leading-tight font-heading">Got more questions?</h2>
  </div>
</section>


<div class="px-4 md:px-8">
<AccordionGroup>

  <Accordion class="flex flex-col">
    <button class="accordionbutton flex flex-col" slot="header" let:toggle on:click={toggle}>How does Oven save me 97% gas?</button>
    <div class="accordioncontent">
      With the community Oven users bake their pies together. 
      Normally baking can require as much as 73 separate transactions for each user, costing a large amount in gas. Oven bakes everyone’s pies together, reducing the total number of transactions needed and sharing the gas costs.  
    </div>
</Accordion>

<Accordion class="flex flex-col">
  <button class="accordionbutton flex flex-col" slot="header" let:toggle on:click={toggle}>When will Oven be triggered?</button>
  <div class="accordioncontent">
    Oven goes through two checks every fifteen minutes. The first checks if more than ten ETH is ready to be baked. The second checks current gas prices to see if baking is cost-efficient.
Once both conditions are met Oven will jump into action, baking everyones’ pies.
  </div>
</Accordion>

<Accordion class="flex flex-col">
  <button class="accordionbutton flex flex-col" slot="header" let:toggle on:click={toggle}>Why does it say 100% but is still not baking?</button>
  <div class="accordioncontent">
    At some points the Ethereum network can experience sustained high demand, causing prolonged high gas prices. This can cause a delay in baking. Oven will bake as soon as it is cost-effective, saving everyone money. If you are in a hurry you are always able to bake your pie independently.
  </div>
</Accordion>

<Accordion class="flex flex-col">
  <button class="accordionbutton flex flex-col" slot="header" let:toggle on:click={toggle}>How long does it usually take to bake a pie?</button>
  <div class="accordioncontent">
    The community Ovens usually bake multiple times a day. Once the minimum ETH and low gas conditions are met Oven will swing into action, and your pie will be ready to withdraw in just a few minutes.
    <br/>You can keep up to date with Oven through the PieDAO <a class="font-bold" about="_blank" href="https://discord.gg/yKkepdMmbz">Discord</a>.
  </div>
</Accordion>

<Accordion class="flex flex-col">
  <button class="accordionbutton flex flex-col" slot="header" let:toggle on:click={toggle}>Can I add more than 10 ETH?</button>
  <div class="accordioncontent">
    Absolutely! Feel free to add as much ETH as you would like to bake. One thing to remember however is that each baking session will use a maximum 30 ETH to reduce slippage.
    <br/>Any left over ETH will remain in the Oven and be baked in the next session.
  </div>
</Accordion>

<Accordion class="flex flex-col">
  <button class="accordionbutton flex flex-col" slot="header" let:toggle on:click={toggle}>Is there a time limit to withdraw my pie?</button>
  <div class="accordioncontent">
    None at all! But don’t forget, the sooner you withdraw your pie the sooner you can put it to work in our farms. We’re incentivizing pie liquidity providers with 150k of delicious DOUGH each week. <a class="font-bold" about="_blank" href="https://pools.piedao.org/#/stake">Check it out</a>.
  </div>
</Accordion>

</AccordionGroup>
</div>
