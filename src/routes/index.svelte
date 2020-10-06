<script>
	import TransitionWrapper from '../components/TransitionWrapper.svelte';
    import BackgroundBlock from '../components/background.svelte';
    import { onMount } from 'svelte';
    import jq from 'jquery';
    
    let isAnimated = false;
	let curDate = new Date();
    let h = (curDate.getHours() < 10) ? '0' + curDate.getHours() : curDate.getHours();
    let m = (curDate.getMinutes() < 10) ? '0' + curDate.getMinutes() : curDate.getMinutes();
    let s = (curDate.getSeconds() < 10) ? '0' + curDate.getSeconds() : curDate.getSeconds();
    let curDateString = h + ':' + m + ':' + s;
	function clock(){
    	var date = new Date(),
    	hours = (date.getHours() < 10) ? '0' + date.getHours() : date.getHours(),
    	minutes = (date.getMinutes() < 10) ? '0' + date.getMinutes() : date.getMinutes(),
    	seconds = (date.getSeconds() < 10) ? '0' + date.getSeconds() : date.getSeconds();
    	curDateString = hours + ':' + minutes + ':' + seconds;
	}
    setInterval(clock, 1000);
    setInterval(function animateDelay(){
        isAnimated = true;
    }, 1000)

    
</script>

<style>
    :global(#sapper){
        height: 100%;
    }
    :global(body) {
        height: 100%;
        overflow: hidden;
    }
    :global(html) {
        height: 100%;
    }
	h2 {
        text-align: center;
    }

    #time-block {
        text-align: center;
        font-size: 30px;
    }

    #container {
        margin-left: 12%;
        width: 70vw;
        height: 600px;
    }

    #border-block {
        width: 50%;
        margin-left: 25%;
        height: 18%;
    }

    #content-block {
        margin-top: -94px;
        height: 18%;
    }
    
    .elem {
        animation: 1s ease-in-out 0s normal none infinite running trambling-animation;
    }
    @keyframes trambling-animation {
        0%, 50%, 100% {
            transform: rotate(0deg);
        }
     10%, 30% {
            transform: rotate(-5deg);
        }
        20%, 40% {
            transform: rotate(5deg);
        }
    }
    
    .border-animated {
        box-shadow: 0 0 10px 10px rgb(0, 0, 0, 0.21);
        border-radius: 10px;
        animation: 3s ease-in-out 0s alternate none infinite running border-animation;
    }
     @keyframes border-animation {
        0% {
            opacity: 0;
        }
        100% {
            opacity: 1;
        }
    }
</style>

<svelte:head>
	<title>Your weekly calendar</title>
</svelte:head>

<BackgroundBlock>
</BackgroundBlock>

<TransitionWrapper>
    <div id="container">
    <div id="border-block" class:border-animated={isAnimated}>
    </div>
    <div id="content-block">
        <h2>Планируйте своё время</h2>
        <div id="time-block" class="elem">
            {curDateString}
        </div>
    </div>
    </div>
</TransitionWrapper>