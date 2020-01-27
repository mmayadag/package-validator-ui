<script>
    import Container from '../components/Container/Container.svelte';
    import Select from '../components/Select/Select.svelte';

    let repoURL = "";
    // https://github.com/mmayadag/package-validator-ui.git
    // https://github.com/mmayadag/s3-hello.git
    // git@github.com:mmayadag/package-validator-ui.git
    let owner = '';
    let repo  = '';
    let error = '';
    let valid = '';
    let email = '';
    let period = '';
    let analyze = false; 
    $: {
        period = ( period != '' && !isNaN(period) ) ? parseInt(period) : '';
    }
    $: isRepo = repoURL.isGitUrl() ;
    $: icon = 'assets/' + (isRepo ? 'tick.svg':'cross.svg');
    $: { 
        if( isRepo ){
            let r = repoURL.gitUserRepo();
            owner = r.owner;
            repo = r.repo;
            isValidRepo();
        }else{
            owner = '';
            repo  = '';
            valid = '';
        }
    };
    $: { analyze = (email && period && owner  && repo) ? true : false }

    
    String.prototype.isGitUrl = function () {
        const regex = /(?:git|ssh|https?|git@[-\w.]+):(\/\/)?(.*?)(\.git)(\/?|\#[-\d\w._]+?)$/;
        return regex.test(this);
    }

    String.prototype.gitUserRepo = function(){
        const regex = /^(https|git)(:\/\/|@)([^\/:]+)[\/:]([^\/:]+)\/(.+).git$/gm;
        const [,,,,owner,repo] = regex.exec(this);
        return{owner,repo} ;
    }

    let promise ;
    async function isValidRepo() {
        if( owner == '' || repo == '') { return; }
        const res = await fetch(
            `http://localhost:3000/repo/isValid/${owner}/${repo}`
        );

        if (res.ok) {
            const obj = await res.json();
            valid = obj.valid;       
        }else{
            throw new Error(res.status)
        }
    }

    async function getValidation() {
        let data = {email, period , owner ,repo};
        console.log({email,period});
        const res = await fetch(
            `http://localhost:3000/repo/schedule`,
            { 
                method: 'POST',
                headers: {
                'Content-Type': 'application/json',
            },
            body: JSON.stringify(data)}
        );        
        if (res.ok) {
            const obj = await res.json();
            // console.log({text1:obj.repoDTO.email});
            return obj.html || obj.repoDTO.email;
        }else{
            throw new Error(res.status)
        }
    }


function findPackages() {
    promise = getValidation();
}

function is(p){
    period=p;
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

{#if valid}
<div><i class="green">This is public repo</i></div>

<h3> Enter your information</h3>
<Container> 
    <p>
        <label for="owner">Email</label>
        <input placeholder="enter your email" bind:value={email} />
    </p>
    <p>
        <label for="repo">Period</label>
        <Select fn={is} />
    </p>
</Container>
{:else if valid === false}
<div><i class="red">This is not public repo</i></div>
{/if}

<Container>
    <button disabled={!isRepo || !analyze} on:click={findPackages}>
    {#if isRepo}
        Analyze
    {:else}{error != '' ?  error : 'Enter repo details'}
    {/if}
    </button>
</Container>

<Container>
    {#if promise && promise != ''}
        {#await promise}
            <p>...waiting</p>
        {:then response}
            <div class="result-box">{@html response}</div>
        {:catch error}
            <p class="red">{error.message}</p>
        {/await}
    {/if}
</Container>