<script>
  
  // Load additional
  import {onMount} from 'svelte';

  // Get current time
  const utc = new Date().toUTCString();

  // Get current BTC Price in € and $ for header
  let eurPrice = 0;
  let usdPrice = 0;

  async function getPriceData(){
    let url = "https://api.coindesk.com/v1/bpi/currentprice/eur.json";
    const response = await fetch(url);
    const data = await response.json();
    eurPrice = data.bpi.EUR.rate;
    usdPrice = data.bpi.EUR.rate;
    localStorage.setItem("eurPrice", eurPrice);
    localStorage.setItem("usdPrice", usdPrice);
  };
  
  onMount(() => getPriceData());
</script>

<h1>Let's Create Awesome</h1>
