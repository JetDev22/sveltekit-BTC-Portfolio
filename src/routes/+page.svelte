<script>
	import { browser } from '$app/environment';
 
  // Load additional
  import {onMount} from 'svelte';

  // Get current time
  const utc = new Date().toUTCString();

  // Get current BTC Price in € and $
  let eurPrice = $state(0);
  let usdPrice = $state(0);
  let calcPrice;
  let curRef = "EUR";

  async function getPriceData(){
    let url = "https://api.coindesk.com/v1/bpi/currentprice/eur.json";
    const response = await fetch(url);
    const data = await response.json();
    eurPrice = data.bpi.EUR.rate;
    usdPrice = data.bpi.USD.rate;
    localStorage.setItem("eurPrice", eurPrice);
    localStorage.setItem("usdPrice", usdPrice);
  };
  
  //onMount(() => getPriceData());

  // User Portfolio Setup
  let btc = $state(0);
  let avg = $state(0);
  let dca = $state(0);
  let currency = $state("EUR");

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
  }

  // Load Portfolio Data and current BTC Price on page load
  onMount(() =>{
    getPriceData();
    loadPortfolio();
  })

  // Portfolio Data Processor
  let eurCalc;
  let usdCalc;

  if(browser){
    try {
      eurCalc = parseFloat(localStorage.getItem("eurPrice").replace(",", ""));
      usdCalc = parseFloat(localStorage.getItem("usdPrice").replace(",", ""));
      curRef = localStorage.getItem("currency");
    } catch (error) {
      console.log("Apparently null value for localStorage");
    }  
  }

  //console.log(eurCalc, usdCalc, curRef);
  let avgView = $derived(avg * 1);
  let dcaView = $derived(dca * 1);
  let pCost = $derived(btc * avg);
  let pValEUR = $derived(btc * eurCalc);
  let pValUSD = $derived(btc * usdCalc);
  let dcaUSD = $derived(dca / usdCalc);
  let dcaEUR = $derived(dca / eurCalc);
  let eurYield = $derived(pValEUR - pCost);
  let usdYield = $derived(pValUSD - pCost);
  let roiEUR = $derived(((pValEUR - pCost)/pCost)*100);
  let roiUSD = $derived(((pValUSD - pCost)/pCost)*100);

  console.log(curRef);

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
    <h1>{eurPrice} €</h1>
    {utc}
  {/if}
  {#if currency == "USD" || currency == "usd"}
    <h1 id="price">{usdPrice} $</h1>
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
      {#if curRef == "EUR" || curRef == "eur"}
      <td style="text-align: right;">{pCost.toLocaleString("currency", {maximumFractionDigits: 2})} €</td>
      {:else}
      <td style="text-align: right;">{pCost.toLocaleString("currency", {maximumFractionDigits: 2})} $</td>
      {/if}
    </tr>
    <tr>
      <td style="text-align: left;">Average Price</td>
        {#if curRef == "EUR" || curRef == "eur"}
        <td style="text-align: right;">{avgView.toLocaleString("currency", {maximumFractionDigits: 2})} €</td>
        {:else}
        <td style="text-align: right;">{avgView.toLocaleString("currency", {maximumFractionDigits: 2})} $</td>
        {/if}
    </tr>
    <tr>
      <td style="text-align: left;">R.O.I.</td>
      {#if curRef == "EUR" || curRef == "eur"}
      <td style="text-align: right;">{roiEUR.toFixed(2)} %</td>
      {:else}
      <td style="text-align: right;">{roiUSD.toFixed(2)} %</td>
      {/if}
    </tr>
    <tr>
      <td style="text-align: left;">Yield</td>
      {#if curRef == "EUR" || curRef == "eur"}
      <td style="text-align: right;">{eurYield.toLocaleString("currency", {maximumFractionDigits: 2})} €</td>
      {:else}
      <td style="text-align: right;">{usdYield.toLocaleString("currency", {maximumFractionDigits: 2})} $</td>
      {/if}
    <tr>
      <td style="text-align: left;">DCA / Month</td>
      {#if curRef == "EUR" || curRef == "eur"}
      <td style="text-align: right;">{dcaView.toLocaleString("currency", {maximumFractionDigits: 2})} €</td>
      {:else}
      <td style="text-align: right;">{dcaView.toLocaleString("currency", {maximumFractionDigits: 2})} $</td>
      {/if}
    <tr>
      <td style="text-align: left;">DCA in &#8383;</td>
      {#if curRef == "EUR" || curRef == "eur"}
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
