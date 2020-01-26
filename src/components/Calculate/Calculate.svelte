<script>
    import Button from './Button.svelte';

	let numbers = [1, 2, 3, 4];

    // optional console.log
    $: console.log({numbers});
	
	function addNumber() {
		numbers.push(numbers.length + 1);
		numbers = numbers;
    }
    
    const plusOne = () => {
        numbers[numbers.length] = numbers.length + 1;
    }
    const reset = () => {
        numbers = [1, 2, 3, 4];
    }
	const removeLastNumber = () => {
		numbers.pop();
		numbers = numbers;
	}
	$: sum = numbers.reduce((t, n) => t + n, 0);
</script>

<div>
    <p>{numbers.join(' + ')} = {sum}</p>
    <ul>
        <li>
            <Button content="+ " on:click={addNumber} className="add" />
        </li>
        <li>
            <Button content="Add a number" on:click={plusOne} className="add" />
        </li>
        
        <li>
            <Button content="reset" on:click={reset} />
        </li>
        
        <li>
            <Button className="remove" on:click={removeLastNumber}>
                Delete Last Number
            </Button>
        </li>
    </ul>
</div>

<style>
ul , li {
    list-style: none;
}
ul > li {
    display: inline-block;
} 

</style>