<script>
export let fn = () => {};
export let List = [6,12,24];
export let selected = '';
selected = List[List.length-1];
$: fn(selected);
</script>
<select bind:value={selected}>
    {#each List as item}
        <option value={item}>{item}</option>
    {/each}
</select>
