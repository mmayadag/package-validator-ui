<script>
    import Container from '../components/Container/Container.svelte';
    import Select from '../components/Select/Select.svelte';
    const { host, protocol } = document.location
    let api = `${protocol}//${host}`;
    api = `https://validator.mayadag.com`;
    let repoURL = "";
    // https://github.com/mmayadag/package-validator-ui.git
    // https://github.com/mmayadag/s3-hello.git
    // git@github.com:mmayadag/package-validator-ui.git
    let owner = '';
    let repo  = '';
    let error = '';
    let valid = '';
    let email = '';
    let period = 24;
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
            `${api}/repo/isValid/${owner}/${repo}`
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
            `${api}/repo/schedule`,
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
function repoUrlChange(newUrl){
    repoURL = newUrl;
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
        height: 48px;
        border-radius: 2px;
        background-color: #419C1C;
        margin: 24px 16px;
        width: 360px;
        color: #fff;

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
    <p>
        <label for="repoUrl">RepoUrl</label>
        <input placeholder="Repo Url" id="repoUrl" bind:value={repoURL} />
        {#if isRepo}<img alt="status" class="status-icon" src={icon}/>{/if}
    </p>
</Container>

<!--
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
-->
{#if valid}
<div><i class="green">This is public repo</i></div>

<h3> Enter your information</h3>
<Container> 
    <p>
        <label for="Email">Email</label>
        <input placeholder="enter your email" bind:value={email} />
    </p>
    <!--<p>
        <label for="repo">Period</label>
        <Select fn={is} />
    </p>-->
</Container>
{:else if valid === false}
<div><i class="red">This is not public repo</i></div>
{/if}


<Container>
    {#if analyze}
        <button disabled={!isRepo || !analyze} on:click={findPackages}>
        {#if isRepo}
            Analyze
        {/if}
        </button>
    {/if}
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



<Container>
    <span on:click|preventDefault={()=> repoUrlChange("https://github.com/mmayadag/bicycle-in-izmir.git")}>TEST > mmayadag/bicycle-in-izmir</span>
</Container>