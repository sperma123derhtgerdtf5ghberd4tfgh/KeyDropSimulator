<script>
  // Logika skrzynek eventowych
  const eventCases = [
    { name: 'GIRLS FAVORITE', price: '4.40 PLN', img: 'cases/girls_fav.png', url: '/cases/girls-fav' },
    { name: 'FRAGILE', price: '11.00 PLN', img: 'cases/fragile.png', url: '/cases/fragile' },
    { name: 'NO SHARPS', price: '22.00 PLN', img: 'cases/no_sharps.png', url: '/cases/no-sharps' },
    { name: 'PRIME DELIVERY', price: '88.00 PLN', img: 'cases/prime.png', url: '/cases/prime' },
    { name: 'DEEP CLEAN', price: '220.00 PLN', img: 'cases/clean.png', url: '/cases/deep-clean' }
  ];
</script>

<section class="mb-10 px-4">
  <div class="relative w-full rounded-2xl overflow-hidden mb-6 shadow-2xl">
    <img src="/cases/evencik2.png" alt="Event Background" class="w-full h-auto block" />
    <div class="absolute inset-0 flex items-center justify-center">
       <img src="/cases/evencik.png" alt="Huge Mess" class="w-[33%] h-auto object-contain" />
    </div>
  </div>

  <div class="grid grid-cols-2 md:grid-cols-5 gap-4">
    {#each eventCases as eCase}
      <a href={eCase.url} class="bg-[#1a1a2e] p-4 rounded-xl border border-zinc-700 hover:border-yellow-500 transition-all cursor-pointer group flex flex-col items-center">
        <div class="h-24 w-full bg-zinc-950 rounded mb-3 flex items-center justify-center overflow-hidden">
          <img src={eCase.img} alt={eCase.name} class="h-full object-contain p-2 group-hover:scale-110 transition-transform duration-300" />
        </div>
        <h3 class="text-white text-xs font-bold uppercase text-center">{eCase.name}</h3>
        <p class="text-yellow-500 text-[10px] mt-1 text-center">{eCase.price}</p>
      </a>
    {/each}
  </div>
</section>