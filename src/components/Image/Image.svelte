<script>
	export let image = "";
	export let alt = "A man dances.";

	let src = `./assets/${image}`;
	let props = { alt , src };
	let style = `background: url(${src}) center no-repeat;background-size: contain;`
</script>
<style>
	.image{
		max-width: 100px;
		margin: 0 auto;
		width: 74px;
		height: 74px;
	}
	
</style>
<div class="image" {style}></div>
<!--<img {...props} />-->
