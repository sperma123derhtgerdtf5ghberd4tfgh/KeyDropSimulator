<script lang="ts">
  import { goldenNames, convertPrice, type CaseSection } from '$lib';
  import { page } from '$app/stores';

  export let data: CaseSection;

  function getCategory(name: string) {
    const n = name.toUpperCase();

    // 1. KNIFES (Noże)
    if (['KARAMBIT', 'BUTTERFLY', 'KNIFE', 'SKELETON', 'BAYONET'].some(k => n.includes(k))) return 'KNIFES';

    // 2. HERO (Bohaterowie)
    if (['HULK', 'SPIDER', 'THOR', 'IRON', 'SUPERMAN'].some(k => n.includes(k))) return 'HERO';
    
    // 3. VILLIANS (Złoczyńcy)
    if (['THANOS', 'JOKER', 'VENOM', 'LOKI'].some(k => n.includes(k))) return 'VILLIANS';
    
    // 4. PREMIUM (Drogie, ekskluzywne)
    if (['GOLD', 'DIAMOND', 'PLATINUM', 'CASE HARDENED'].some(k => n.includes(k))) return 'PREMIUM';
    
    // 5. LOW CHANCE (Szansa, ryzyko)
    if (['1%', 'LOW', 'CHANCE', 'RISK', 'HARD'].some(k => n.includes(k))) return 'LOW CHANCE';
    
    // 6. WEAPONS (Bronie - domyślny worek dla reszty)
    return 'WEAPONS';
  }

  $: groupedCases = data.cases.reduce((acc, c) => {
    const cat = getCategory(c.websiteName);
    if (!acc[cat]) acc[cat] = [];
    acc[cat].push(c);
    return acc;
  }, {} as Record<string, typeof data.cases>);

  let hiddenSections: Record<string, boolean> = {};
  const toggleSection = (cat: string) => { hiddenSections[cat] = !hiddenSections[cat]; };
</script>

{#each ['WEAPONS', 'KNIFES','CREATORS', 'PREMIUM', 'HERO', 'VILLIANS', 'LOW CHANCE'] as categoryName}
  {#if groupedCases[categoryName] && groupedCases[categoryName].length > 0}
    <section class="relative py-4">
      <div class="flex items-center justify-between px-4 mb-6 border-b border-gray-700 pb-3">
        <h2 class="text-white text-xl font-bold uppercase tracking-wider">{categoryName}</h2>
        <button on:click={() => toggleSection(categoryName)} class="text-gray-500 text-xs hover:text-white transition-colors uppercase font-bold">
          {hiddenSections[categoryName] ? 'POKAŻ v' : 'UKRYJ ^'}
        </button>
      </div>

      {#if !hiddenSections[categoryName]}
        <div class="grid grid-cols-2 sm:grid-cols-3 md:grid-cols-4 lg:grid-cols-5 xl:grid-cols-6 gap-5 px-3">
          {#each groupedCases[categoryName] as caseData}
            <div class="group relative rounded-xl border border-zinc-800 shadow-2xl transition-all duration-300 hover:border-gold hover:-translate-y-2 overflow-hidden bg-[#1a1a2e]" style="height: 320px;">
              <a href="/skins/category/{caseData.urlName}" class="block h-full w-full relative transition-transform duration-500 group-hover:scale-105">
                
                <img 
                  src="/cases/{caseData.imgName}" 
                  alt={caseData.websiteName}
                  loading="lazy"
                  decoding="async"
                  class="absolute inset-0 z-10 h-full w-full object-contain p-full"
                />

                <div class="absolute inset-0 z-20 flex flex-col justify-between p-2 pointer-events-none">
                  <div class="self-end rounded-md bg-black/50 px-4 py-1.5 text-[12px] font-bold text-gold-500 pointer-events-auto border border-gold/40">
                    {goldenNames.includes(caseData.websiteName) ? caseData.price : convertPrice($page.data.currency, caseData.price)}
                  </div>
                  <div class="w-full rounded-lg bg-navy-800 py-2 text-center text-xs font-black uppercase text-white pointer-events-auto border-t border-navy-600">
                    {caseData.websiteName}
                  </div>
                </div>
              </a>
            </div>
          {/each}
        </div>
      {/if}
    </section>
  {/if}
{/each}