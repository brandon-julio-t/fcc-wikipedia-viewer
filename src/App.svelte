<script lang="ts">
  import WikiItem from "./models/WikiItem";

  let isLoading = false;
  let wikiItems = [] as WikiItem[];

  const apiUrl = "https://en.wikipedia.org/w/api.php";
  async function search(keyword: string) {
    isLoading = true;

    const args = {
      action: "opensearch",
      format: "json",
      search: keyword,
      namespace: "0",
      limit: "10",
      origin: "*",
    };

    const response = await fetch(apiUrl + "?" + new URLSearchParams(args));
    const [_, titles, __, urls] = (await response.json()) as [
      string,
      string[],
      string[],
      string[]
    ];

    wikiItems = titles.map((title, idx) => new WikiItem(title, urls[idx]));

    isLoading = false;
  }

  let timeout: number;
  function searchHandler(text: string) {
    clearTimeout(timeout);
    timeout = setTimeout(() => search(text), 250);
  }
</script>

<main class="container mx-auto h-screen grid place-items-center">
  <section
    class="max-w-xs w-full border-slate-200 shadow hover:shadow-md transition-all rounded-xl p-4"
  >
    <div class="grid grid-cols-1 gap-4">
      <input
        on:input={(e) => searchHandler(e.currentTarget.value)}
        class="border border-slate-400 rounded px-3 py-2 disabled:animate-pulse"
        placeholder="Search..."
        disabled={isLoading}
      />
      <a
        href="https://en.wikipedia.org/wiki/Special:Random"
        target="_blank"
        rel="noopener noreferrer"
        class="text-center bg-slate-50/75 hover:bg-slate-50 border-slate-200 px-4 py-3 rounded shadow hover:shadow-md transition-all disabled:animate-pulse"
        disabled={isLoading}
      >
        Random
      </a>
    </div>
  </section>
  {#if wikiItems.length && !isLoading}
    <section
      class="max-w-xs w-full border-slate-200 shadow hover:shadow-md transition-all rounded-xl p-4"
    >
      <div class="grid grid-cols-1 gap-4">
        {#each wikiItems as wiki}
          <a
            href={wiki.url}
            target="_blank"
            rel="noopener noreferrer"
            class="text-bold truncate border-slate-200 shadow hover:shadow-md transition-all rounded-xl p-4"
          >
            {wiki.title}
          </a>
        {/each}
      </div>
    </section>
  {/if}
</main>
