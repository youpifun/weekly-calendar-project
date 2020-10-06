<style>
    .stage {
        position: absolute;
        left: 0;
        top: 0;
        width: 100%;
        height: 100%;
        overflow: hidden;
        background: steelblue;
        box-shadow: 0 -400px 300px -300px orangered inset;
        animation: sky-change 15s infinite linear;
    }

    @keyframes sky-change {
    0% {
        background: rgb(149, 213, 243);
    }
    50% {
        background: rgb(120, 191, 219);
        box-shadow: 0 -400px 300px -300px steelblue inset;
    }
    85% {
        background: rgba(143, 196, 245);
        box-shadow: 0 -400px 300px -300px orangered inset;
    }
    100% {
        background: rgb(149, 213, 243);
    }
    }
</style>

<div class="stage">
</div>