<script>
  import { _ } from 'svelte-i18n';
  import images from '../config/images.json';
  export let pie;
</script>

<div class="quantstamp-container pointer">
  <a href={`https://www.coingecko.com/en/coins/${pie.coingeckoId}`}  alt="Coingecko page" target="_blank">
    <span>Check this token on</span>
    <img width="100%" height="auto" src={images.logos.coingecko} alt="Coingecko logo" />
  </a>
</div>