<script>
    import Container from '../components/Container/Container.svelte';
    let repoURL = "";
    // https://github.com/mmayadag/package-validator-ui.git
    // git@github.com:mmayadag/package-validator-ui.git
    let owner = '';
    let repo = '';
    let error = '';
    $: isRepo = repoURL.isGitUrl() ;
    $: icon = 'assets/' + (isRepo ? 'tick.svg':'cross.svg');
    $: { 
        if( isRepo ){
            let r = repoURL.gitUserRepo();
            owner = r.owner;
            repo = r.repo;
        }
    };

    
    String.prototype.isGitUrl = function () {
        const regex = /(?:git|ssh|https?|git@[-\w.]+):(\/\/)?(.*?)(\.git)(\/?|\#[-\d\w._]+?)$/;
        return regex.test(this);
    }

    String.prototype.gitUserRepo = function(){
        const regex = /^(https|git)(:\/\/|@)([^\/:]+)[\/:]([^\/:]+)\/(.+).git$/gm;
        const [,,,,owner,repo] = regex.exec("https://github.com/mmayadag/package-validator-ui.git");
        return{owner,repo} ;
    }

    let promise ;

    async function getRandomNumber() {
        const res = await fetch(
            `https://www.random.org/integers/?num=10&min=1&max=6&col=1&base=10&format=plain&rnd=new`
        );
        const text = await res.text();

        if (res.ok) {
            return text;
        } else {
            throw new Error(text);
        }
    }


function findPackages() {
    promise = getRandomNumber();
}

</script>

<style>
    
    input {
        width: 100%;
        margin-bottom: 0;
        padding: 8px 20px;

    }
    p,input{
        flex:1;
    }
    .status-icon{
        width: 1rem;
        margin: 0 1rem 0 0.2rem;
    }
    button{
        background-color: #2ecc71;
        padding: 10px 60px;
        margin-bottom: 0;
        min-width:241px;
    }
    button:disabled{
        background-color: white;
    }
    label{
        font-weight: bold;
        font-size: 14px;
    }

</style>
<Container>
    <input placeholder="Repo Url" bind:value={repoURL} />
    {#if isRepo}<img alt="status" class="status-icon" src={icon}/>{/if}
</Container>

<Container>
    <p>
        <label for="owner">Owner</label>
        <input placeholder="owner" bind:value={owner} />
    </p>
    <p>
        <label for="repo">Repo</label>
        <input placeholder="repo" bind:value={repo} />
    </p>
</Container>

<Container>
    <button disabled={!isRepo} on:click={findPackages}>
    {#if isRepo}Analyze
    {:else}{error != '' ?  error : 'Enter repo details'}
    {/if}
    </button>
</Container>

<Container>
{#if promise && promise != ''}
    {#await promise}
        <p>...waiting</p>
    {:then number}
        <p>The number is {number}</p>
    {:catch error}
        {console.log(error)}
        <p style="color: red">{error.message}</p>
    {/await}
{/if}
</Container>