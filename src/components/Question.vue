<template>
    <div class="question">
        <div class="image-holder"  :style="{'backgroundImage': 'url(' + img + ')'}" :class="{'dimmed': header || subheader}" ></div>
        <div class="content">
            <h2>{{subheader}}</h2>
            <h1>{{header}}</h1>
        </div>


    </div>

</template>

<script>
    export default {
        props: ['img', 'header', 'subheader']
    }
</script>
<style scoped lang="sass">
    .question {
        height: 100%;
        overflow: hidden;
        background: black;

        .image-holder {
            position: absolute;
            top: 0;
            left: 0;
            right: 0;
            bottom: 0;

            background-position: center center;
            background-size: cover;

            
        }


    }

    .image-holder {
        animation-name: kenburns;
        animation-duration: 30s;
        animation-iteration-count: infinite;
    }
    .dimmed {
        opacity: 0.5;
    }

    .content {
        position: absolute;
        top: 0;
        left: 0;
        right: 0;
        bottom: 0;

        display: flex;
        flex-direction: column;
        justify-content: center;
        line-height: 11vh;

        text-shadow: 0 0 20px black;

        h1 {
            font-size: 10vw;

            animation-name: text-animation;
            animation-duration: 8s;
            animation-iteration-count: infinite;
        }
        h2 {
            font-size: 3.5vw;
        }
    }

    @keyframes text-animation {
        50% {
            transform: scale(1.1,1.1);
        }
    }

    .move-enter-active, .move-leave-active {
        .image-holder {
            animation-name: none;
        }
    }

    @keyframes kenburns {
        50% {
            transform: scale(1.15,1.15);
        }
    }

</style>