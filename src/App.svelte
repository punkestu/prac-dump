<script>
  import Dump from "./lib/dump.json";
  import Bookmark from "./assets/bookmark.svg";
  import BookmarkFill from "./assets/bookmark--filled.svg";

  let search = "";
  let saveView = false;
  let saved = [];
  $: dump = Dump.filter((obj) => {
    saved = localStorage.getItem("saved")
      ? JSON.parse(localStorage.getItem("saved"))
      : [];
    return saveView ? saved.includes(obj.id) : true;
  }).filter((obj) => {
    if (search === "") return true;
    return (
      obj.name.toLowerCase().includes(search.toLowerCase()) ||
      obj.tags.some((tag) => tag.toLowerCase().includes(search.toLowerCase()))
    );
  });
</script>

<main class="text-slate-50 p-2 flex flex-col items-center gap-2">
  <h1 class="font-semibold text-xl">Praktikum-Dump</h1>
  <div class="w-full flex gap-4 items-center">
    <input
      type="text"
      placeholder="Query..."
      class="text-slate-950 px-3 py-1 rounded-md flex-1"
      bind:value={search}
    />
    <input
      class="hidden"
      type="checkbox"
      id="save-view"
      bind:checked={saveView}
    />
    <label for="save-view"
      ><img
        src={saveView ? BookmarkFill : Bookmark}
        alt="bookmark"
        class="w-10"
      /></label
    >
  </div>
  {#if dump.length === 0}
    <p class="text-slate-100 font-medium pt-8">No results found</p>
  {/if}
  <div class="flex flex-wrap gap-2 w-full">
    {#each dump as obj}
      <div class="relative">
        <button
          class="absolute right-1 top-1"
          on:click={() => {
            if (saved.includes(obj.id)) {
              saved = saved.filter((id) => id !== obj.id);
            } else {
              saved = [...saved, obj.id];
            }
            localStorage.setItem("saved", JSON.stringify(saved));
          }}
        >
          <img
            src={saved.includes(obj.id) ? BookmarkFill : Bookmark}
            alt="bookmark"
            class="w-8"
          /></button
        >
        <a
          href={obj.url}
          class="h-full flex flex-col justify-center gap-2 text-slate-100 bg-slate-700 p-4 rounded-lg"
        >
          <p class="pe-10 text-lg font-medium">{obj.name}</p>
          {#if obj.tags.length > 0}
            <div>
              {#each obj.tags as tag}
                <span>
                  #{tag}
                </span>
              {/each}
            </div>
          {/if}
        </a>
      </div>
    {/each}
  </div>
</main>
