<script>
    import Login from './Login.svelte';
    import {username,user} from './user';
    import {onMount} from 'svelte';

    import GUN from 'gun';
  import SEA from 'gun/sea';
    const db = GUN();

    let newMessage;
    let messages = []

    onMount(() => {

        db.get('chat')
        .map()
        .once(async(data,id) => {
            if(data){
                const key = '#foo';

                var message = {
                    who:await db.user(data).get('alias'),
                    what:(await SEA.decrypt(data.what,key)),
                    when: GUN.state.is(data,'what'),
                };

                if(message.what){
                    messages = [...message.slice(-100),message]
                }
            }
        })


    })

</script>