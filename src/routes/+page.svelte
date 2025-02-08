<script>
	import { browser } from '$app/environment';
 
  // Load additional
  import {onMount} from 'svelte';

  // Get current time
  const utc = new Date().toUTCString(); 

  async function getPriceData(){
    let url = "https://blockchain.info/ticker";
    const response = await fetch(url);
    const data = await response.json();
    eurPrice = data.EUR.last;
    usdPrice = data.USD.last;
    pCost = btc * avg;
    pValEUR = btc * eurPrice;
    pValUSD = btc * usdPrice;
    dcaUSD = dca / usdPrice;
    dcaEUR = dca / eurPrice;
    eurYield = pValEUR - pCost;
    usdYield = pValUSD - pCost;
    roiEUR = ((pValEUR - pCost)/pCost)*100;
    roiUSD = ((pValUSD - pCost)/pCost)*100;
    avg = avg * 1;
    dca = dca * 1;
};

  // User Portfolio Setup
  let eurPrice = $state(0);
  let usdPrice = $state(0);
  let btc = $state(0);
  let avg = $state(0);
  let dca = $state(0);
  let currency = $state("EUR");
  let pCost = $state(0);
  let pValEUR = $state(0);
  let pValUSD = $state(0);
  let dcaUSD = $state(0);
  let dcaEUR = $state(0);
  let eurYield = $state(0);
  let usdYield = $state(0);
  let roiEUR = $state(0);
  let roiUSD = $state(0);


  function saveUserPortfolio(){
    localStorage.setItem("btc", btc.toString());
    localStorage.setItem("avg", avg.toString());
    localStorage.setItem("dca", dca.toString());
    localStorage.setItem("currency", currency.toString());
    window.location.reload();
  }

  function clearUserPortfolio(){
    localStorage.removeItem("btc");
    localStorage.removeItem("avg");
    localStorage.removeItem("dca");
    localStorage.removeItem("currency");
    window.location.reload();
  }

  function loadPortfolio(){
    if("btc" in localStorage && "avg" in localStorage && "dca" in localStorage && "currency" in localStorage){
      btc = localStorage.getItem("btc");
      avg = localStorage.getItem("avg");
      dca = localStorage.getItem("dca");
      currency = localStorage.getItem("currency");
    }
    else{
      btc = 0;
      avg = 0;
      dca = 0;
      currency = "EUR";
    }
  }

  // Load Portfolio Data and current BTC Price on page load
  onMount(() =>{
    loadPortfolio();
    getPriceData();
  })

</script>

<style>
  div{
    background: rgba( 255, 255, 255, 0.3 );
    box-shadow: 0 5px 5px 0 rgba( 255, 255, 255, 0.37 );
    backdrop-filter: blur( 5px );
    -webkit-backdrop-filter: blur( 5px );
    border-radius: 10px;
    justify-content: center;

    padding: 10px;
    margin-top: 10px;
    margin-bottom: 15px;
    margin-left: auto;
    margin-right: auto;
    max-width: 700px;
  }

  img{
    height: 100px;
  }

  #Logo, #Setup, #Disclaimer, #Price, #Portfolio{
    text-align: center;
  }

  input, button{
    margin-bottom: 5px;
    margin-top: 5px;
    text-align: center;
    border-radius: 5px;
    border-width: 0px;
    height: 25px;
    font-size: 15px;
  }

  summary{
    margin-bottom: 5px;
    font-size: 20px;
  }

  button{
    margin-left: 5px;
    margin-right: 5px;
  }

  table{
    font-size: 20px;
    margin-left: auto;
    margin-right: auto;
    width: 350px
  }
 
  h2{
    margin-top: 5px;
  }
</style>

<div id="Logo">
  <img src="/logo.png" alt="btc logo">
</div>

<div id="Price">
  <b>Current &#8383; Price</b>
  {#if currency == "EUR" || currency == "eur"}
    <h1>{eurPrice.toLocaleString("currency", {maximumFractionDigits: 2})} €</h1>
    {utc}
  {/if}
  {#if currency == "USD" || currency == "usd"}
    <h1 id="price">{usdPrice.toLocaleString("currency", {maximumFractionDigits: 2})} $</h1>
    {utc}
  {/if}
</div>

<div id="Setup">
  <details><summary>Portfolio Setup</summary>
    BTC owned<br><input type="number" bind:value={btc} required><br>
    Average buy Price<br><input type="number" bind:value={avg} required><br>
    Montly DCA<br><input type="number" bind:value={dca}><br>
    EUR / USD<br><input type="text" bind:value={currency} placeholder={currency} required><br>
    <button onclick={saveUserPortfolio}>Update</button><button onclick={clearUserPortfolio}>Clear Data</button>
  </details>
</div>

<div id="Portfolio">
 <h2>Portfolio Analysis</h2>
 <table>
    <tbody>
    <tr>
      <td style="text-align: left;">BTC owend</td>
      <td style="text-align: right;">{btc} &#8383;</td>
    </tr>
    <tr>
      <td style="text-align: left;">Portfolio Value</td>
      {#if currency == "EUR" || currency == "eur"}
      <td style="text-align: right;">{pCost.toLocaleString("currency", {maximumFractionDigits: 2})} €</td>
      {:else}
      <td style="text-align: right;">{pCost.toLocaleString("currency", {maximumFractionDigits: 2})} $</td>
      {/if}
    </tr>
    <tr>
      <td style="text-align: left;">Average Price</td>
        {#if currency == "EUR" || currency == "eur"}
        <td style="text-align: right;">{avg.toLocaleString("currency", {maximumFractionDigits: 2})} €</td>
        {:else}
        <td style="text-align: right;">{avg.toLocaleString("currency", {maximumFractionDigits: 2})} $</td>
        {/if}
    </tr>
    <tr>
      <td style="text-align: left;">R.O.I.</td>
      {#if currency == "EUR" || currency == "eur"}
      <td style="text-align: right;">{roiEUR.toFixed(2)} %</td>
      {:else}
      <td style="text-align: right;">{roiUSD.toFixed(2)} %</td>
      {/if}
    </tr>
    <tr>
      <td style="text-align: left;">Yield</td>
      {#if currency == "EUR" || currency == "eur"}
      <td style="text-align: right;">{eurYield.toLocaleString("currency", {maximumFractionDigits: 2})} €</td>
      {:else}
      <td style="text-align: right;">{usdYield.toLocaleString("currency", {maximumFractionDigits: 2})} $</td>
      {/if}
    <tr>
      <td style="text-align: left;">DCA / Month</td>
      {#if currency == "EUR" || currency == "eur"}
      <td style="text-align: right;">{dca.toLocaleString("currency", {maximumFractionDigits: 2})} €</td>
      {:else}
      <td style="text-align: right;">{dca.toLocaleString("currency", {maximumFractionDigits: 2})} $</td>
      {/if}
    <tr>
      <td style="text-align: left;">DCA in &#8383;</td>
      {#if currency == "EUR" || currency == "eur"}
      <td style="text-align: right;">{dcaEUR.toFixed(5)} &#8383;</td>
      {:else}
      <td style="text-align: right;">{dcaUSD.toFixed(5)} &#8383;</td>
      {/if}
    </tr>
    </tbody>
  </table>
</div>

<div id="Disclaimer">
  <h2>Disclaimer</h2>
  All data is saved only in your browser. No data is send back to the server. This web app uses CSR (Client side rendering only).
    <br>Stay vigilant and HODL &#8383;
</div>
