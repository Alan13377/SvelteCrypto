<script>
  import { onMount } from "svelte";

  let titles = ["#", "Coin", "Price", "Price Change", "24h Volume"];
  let coins = [];
  let filterCoins = [];
  let ref = null;

  const loadCoins = async () => {
    const response = await fetch(
      "https://api.coingecko.com/api/v3/coins/markets?vs_currency=usd&order=market_cap_desc&per_page=100&page=1&sparkline=false"
    );
    const data = await response.json();
    coins = data;
    filterCoins = data;
  };

  const searchCoins = (value) => {
    filterCoins = coins.filter(
      (coin) =>
        coin.name.toLowerCase().includes(value.toLowerCase()) ||
        coin.symbol.toLowerCase().includes(value.toLowerCase())
    );
  };
  loadCoins();
  onMount(() => {
    ref.focus();
  });
</script>

<main>
  <div class="container">
    <div class="row">
      <h1>Coin Market</h1>
      <input
        type="text"
        class="form-control bg-dark text-white rounded-0 border-0 my-4"
        placeholder="Search"
        on:keyup={({ target: { value } }) => searchCoins(value)}
        bind:this={ref}
      />
      <table class="table table-dark">
        <thead>
          <tr>
            {#each titles as title}
              <th>{title}</th>
            {/each}
          </tr>
        </thead>
        <tbody>
          {#each filterCoins as coin, index}
            <tr>
              <td class="text-muted">{index + 1}</td>
              <td>
                <img
                  src={coin.image}
                  alt={coin.name}
                  style="width:2rem;"
                  class="img-fluid me-3 "
                />
                <span>
                  {coin.name}
                </span>
                <span class="text-muted text-uppercase ms-3">
                  {coin.symbol}
                </span>
              </td>
              <td>
                $ {coin.current_price.toLocaleString()}
              </td>
              <td
                class={coin.price_change_percentage_24h > 0
                  ? "text-success"
                  : "text-danger"}
              >
                {coin.price_change_percentage_24h} %
              </td>
              <td>
                $ {coin.total_volume.toLocaleString()}
              </td>
            </tr>
          {/each}
        </tbody>
      </table>
    </div>
  </div>
</main>

<style>
  :global(body) {
    background: #141414;
    color: #ffffff;
  }
</style>
