<template>
    <div class="score" :class="{ 'active': team.active, 'correct': team.answered === true, 'incorrect': team.answered === false}" :style="{'transform': 'scale('+team.scale+','+team.scale+')'}" @click="team.score++">
        <div class="team-name">{{team.name}}</div>
        <div class="score-value">{{team.score}}</div>
    </div>
</template>
<script>
    export default {
        props: ['team']
    }
</script>