<script>
    export let content="";
    export let className = "default";
</script>

<button class={className} on:click title="content">
    <slot>{content}</slot>
</button>

<style>
button {
    border: 1px solid #dddddd;
    color:white;
}
button.add{
    background: seagreen;

}
button.remove{
    background: red;
}
button.default{
    background: slategrey;
}
</style>