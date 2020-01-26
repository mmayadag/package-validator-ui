<script>
	import Image from '../Image/Image.svelte';
	export let image;
	export let alt;
	export let title;
</script>

<style>
	.box{
		display: inline-flex;
		flex-direction: column;
		align-items: center;
		justify-content: space-evenly;
		padding: 10px;	
		border: 1px solid #aaa;
		border-radius: 2px;
		box-shadow: 2px 2px 8px rgba(0,0,0,0.1);
		border-radius: 10px;
		width: 128px;
		height: 128px;
		padding: 10px;
		box-sizing: border-box;
	}
	.box > :first-child{
		padding: 20px;
	}
</style>

<div class="box">
	<Image {image} {alt} />
	
	<div>
		{title}
    </div>
    
</div>
