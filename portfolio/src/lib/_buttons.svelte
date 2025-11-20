<script>
    function redirect(url) {
        window.open(url, '_blank');
    }
</script>

<div id="social_buttons">
    <button on:click={() => redirect('https://github.com/FCzajkowski')}>Github</button> /
    <button on:click={() => redirect('https://discord.gg/TwhBsscT82')}>Discord</button> /
    <button on:click={() => redirect('mailto:FCzajkowski@proton.me')}>Email</button> 
</div>
<div id="pages_buttons">
    <button on:click={() => redirect('/')}>About</button> /
    <button on:click={() => redirect('/about')}>Projects</button> /
    <button on:click={() => redirect('/projects')}>Education</button> 
</div>  