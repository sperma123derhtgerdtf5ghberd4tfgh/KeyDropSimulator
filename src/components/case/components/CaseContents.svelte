<script lang="ts">
  import { page } from '$app/stores';
  import { colors, type CaseDrop, type SkinRarity, type SkinWear } from '$lib';
  import { convertPrice } from '$lib';
  import { _ } from 'svelte-i18n';
  export let caseDrops: CaseDrop[];

  // FUNKCJA: Wskazuje na pliki w folderze static/skins/
  // Jeśli nazwy plików się nie zgadzają, zmień to na drop.id + ".png"
  function getSkinPath(weapon: string, skin: string) {
    const w = weapon.toLowerCase().replace(/\s/g, '_');
    const s = skin.toLowerCase().replace(/\s/g, '_');
    return `/skins/${w}_${s}.png`;
  }

  const parsedDisplayDrops = (() => {
    const uniqueKeys = [];
    for (const drop of caseDrops) {
      uniqueKeys.push({ name: drop.skinName, weapon: drop.weaponName });
    }
    const filtered = uniqueKeys.filter(
      ((s) => (o) => ((k) => !s.has(k) && s.add(k))(['name', 'weapon'].map((k) => o[k as keyof typeof o]).join('|')))(new Set())
    );
    const returnArr = [];
    for (const obj of filtered) {
      const allMatchingDrops = caseDrops.filter((item) => item.skinName == obj.name && item.weaponName == obj.weapon);
      const displayDrop: any = {
        displayChance: allMatchingDrops[0].displayChance,
        skinRarity: allMatchingDrops[0].skinRarity,
        weaponName: allMatchingDrops[0].weaponName,
        skinName: allMatchingDrops[0].skinName,
        details: allMatchingDrops.map(d => ({ quality: d.skinQuality, price: d.skinPrice, range: d.oddsRange, odds: d.displayOdds }))
      };
      
      const wearSortOrder: SkinWear[] = ['FN', 'MW', 'FT', 'WW', 'BS'];
      displayDrop.details.sort((a, b) => wearSortOrder.indexOf(a.quality) - wearSortOrder.indexOf(b.quality));
      returnArr.push(displayDrop);
    }
    
    const ordering: { [key in SkinRarity]?: number } = {};
    const sortOrder: SkinRarity[] = ['gold', 'red', 'pink', 'violet', 'blue', 'gray'];
    for (let i = 0; i < sortOrder.length; i++) ordering[sortOrder[i]] = i;

    return returnArr.sort(
      (a, b) =>
        ordering[a.skinRarity]! - ordering[b.skinRarity]! ||
        Math.max(...b.details.map((d: any) => d.price)) - Math.max(...a.details.map((d: any) => d.price))
    );
  })();

  function toggleDetails(e: any) {
    e.currentTarget.offsetParent?.querySelector('.details-page')?.classList.toggle('hidden');
  }

  function parsePriceRange(details: any[]) {
    const prices = details.map((d) => d.price);
    const min = Math.min(...prices);
    const max = Math.max(...prices);
    return min === max ? convertPrice($page.data.currency, min) : `${convertPrice($page.data.currency, min)} - ${convertPrice($page.data.currency, max)}`;
  }
</script>

<div class="container mx-auto">
  <ul class="css-a27jap mb-20 mt-8 grid gap-2">
    {#each parsedDisplayDrops as drop}
      <li class="group" style="contain: content;">
        <div class="ratio css-awr5wf group z-0 grid grid-cols-1 grid-rows-1 items-center justify-center justify-items-center rounded border border-dotted border-navy-400 bg-cover bg-center sm:rounded-lg">
          
          <div class="details-page css-bnrups z-20 col-start-1 row-start-1 row-end-3 hidden h-full w-full rounded bg-navy-800 bg-opacity-70 transition duration-300 sm:rounded-lg">
             <div class="css-15kssi9 mt-5 grid text-right p-4">
                {#each drop.details as details}
                  <div class="text-navy-100">{details.quality}</div>
                  <div class="text-gold">{convertPrice($page.data.currency, details.price)}</div>
                  <div class="text-navy-100">{details.odds}</div>
                {/each}
             </div>
          </div>

          <button class="css-5mrvkg z-30 col-start-1 row-start-1 self-start justify-self-start text-navy-200" on:click={toggleDetails}>
            <div class="css-1t6ze00 flex h-5 w-5 items-center justify-center rounded-full bg-navy-500 text-center font-bold">i</div>
          </button>

          <div class="css-1f31obc z-10 col-start-1 row-start-1 mb-auto ml-auto text-right font-semibold uppercase leading-none text-navy-200">
            Chance <br /> <span class="css-ix4kef">{drop.displayChance}</span>
          </div>

          <img alt="" class="css-96c4l3 col-start-1 row-start-1 mt-6 transform object-contain duration-300 ease-in-out group-hover:scale-110 md:mt-0" src={colors.itemBg[drop.skinRarity]} />
          
          <img 
            src={getSkinPath(drop.weaponName, drop.skinName)} 
            alt="{drop.weaponName} | {drop.skinName}"
            class="css-1w1pcrf pointer-events-none col-start-1 row-start-1 mt-6 w-3/4 transform object-contain duration-300 ease-in-out group-hover:rotate-12 group-hover:scale-75 sm:mt-0"
            onerror="this.style.display='none'"
          />

          <div class="css-1jurzha z-10 col-start-1 row-start-2 w-full self-end justify-self-start font-semibold uppercase leading-tight md:row-start-1">
            <div class="truncate text-3xs text-navy-200">{drop.skinName}</div>
            <div class="css-6plnry truncate font-bold text-white">{drop.weaponName}</div>
            <div class="css-6plnry -mb-1 truncate font-bold text-gold">{parsePriceRange(drop.details)}</div>
          </div>
        </div>
      </li>
    {/each}
  </ul>
</div>