<script>
import {username,user} from './user';

function signout(){
    user.leave();
    username.set('');
}
</script>

<header>
    <h1>GruWeb3</h1>
    {#if username}

    <div class="user-bio">
        <span>Hello <strong>{$username}</strong></span>
        <img src = {'https://avatars.dicebar.com/api/initials/${$username}.svg'} alt = "avatar"/>
    </div>

    <button class="signout-button" on:click={signout}>

        Sign Out
    
    
    </button>

    {/if}
</header>