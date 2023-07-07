<script>
    import Login from './Login.svelte';
    import {username,user} from './user';
    import {onMount} from 'svelte';
    import debounce from 'lodash.debounce';
    import GUN from 'gun';
    import SEA from 'gun/sea';
    const db = GUN();

    let newMessage;
    let messages = [];

    let scrollBottom;
    let lastScrollTop;
    let canAutoScroll = true;
    let unreadMessages = false;

    function autoScroll() {
        setTimeout(() => scrollBottom?.scrollIntoView({behavior: 'auto'}),50)
        unreadMessages = false;
    }

    function watchScroll(){
        canAutoScroll = (e.target.ScrollTop || Infinity) > lastScrollTop;
        lastScrollTop = e.target.ScrollTop;
    }

    $: debounceWatchScroll = debounce(watchScroll,1000);

    onMount(() => {

        var match = {
            '.' : {
                '>':new Date(+new Date() - 1 * 1000 * 60 * 60 * 3).toISOString(),
            },

            '-':1,
        };

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
                    messages = [...message.slice(-100),message].sort((a,b) => a.when - b.when);
                    if(canAutoScroll){
                        autoScroll();
                    }else{
                        unreadMessages = true;
                    }
                
                }
            }
        });


    });

    async function sendMessage(){
        const secret = await SEA.encrypt(newMessage,"#foo");
        const message = user.get('all').setTimeout({what:secret});
        db.get('chat').get(index).put(message);
        newMessage = '';
        canAutoScroll = true;
        autoScroll();
    }

</script>