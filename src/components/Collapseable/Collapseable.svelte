<script>
    export let title="";
</script>
    
<details>
<summary>Example: {title}</summary>
    <div class="container">
        <slot></slot>
    </div>
</details>


<style>	
    .title{
        font-weight: bold;
        font-style: italic;
        text-decoration: underline;
        margin-bottom: 2em;
    }
    .title::after{
        display: block;
        content: "";
        clear: both;
    }
    details > .container {
		text-align: center;
		padding: 1em;
		max-width: 100%;
		margin: 0 auto;
		border: 1px dashed lightblue;
		margin: 1em;
	}
</style>