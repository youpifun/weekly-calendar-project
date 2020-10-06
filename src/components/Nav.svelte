<script>
    export let segment;
    import jq from 'jquery';
    import { onMount } from 'svelte';
    $: textAreaValue = 'Авторизация'
    onMount(async() => {
        window.jq = jq;
        if (document.cookie != '') {
            let data = {
                type: 'get_username'
            }
            jq.ajax({type: 'POST', headers: {"Cookie": document.cookie}, xhrFields: {withCredentials: true
}, crossDomain: true, url: 'http://localhost:5000', data: JSON.stringify(data)}).done(  function(res) {
                textAreaValue = res; //SUCCESS
            })
        }
    })
</script>
<style>
	
    nav {
        display: flex;
        width: 100%;
        background-color: rgb(149, 213, 243);
    }

    nav :last-child{
        margin-left: auto;
    }

    a {
        display: block;
        text-align: center;
        width: 150px;
        height: 10vh;
        color: rgb(63, 63, 63);
        padding: 1vh 2vh;
        text-decoration: none;
        line-height: 9vh;
        user-select: none;
        font-size: 20px;
    }

    a:hover {
        background-color: rgb(76, 212, 190);
        color: rgb(255, 255, 255);
}
</style>

<nav>
		<a aria-current="{segment === undefined ? 'page' : undefined}" href=".">Главная</a>
		<a aria-current="{segment === 'shedule' ? 'page' : undefined}" href="shedule">Расписание</a>
		<a aria-current="{segment === 'about' ? 'page' : undefined}" href="about">Информация</a>
		<!-- for the blog link, we're using rel=prefetch so that Sapper prefetches
		     the blog data when we hover over the link or tap it on a touchscreen -->
		<a rel=prefetch aria-current="{segment === 'login' ? 'page' : undefined}" href="login">{textAreaValue}</a>
</nav>
