<template>
    <div class="score" :class="{'leader': team.leader && team.score > 0, 'active': team.active, 'correct': team.answered === true, 'incorrect': team.answered === false}" @click="team.score++">
        <div class="team-name">{{team.name}}</div>
        <div class="score-value">{{team.score}} </div>
    </div>
</template>
<script>
    export default {
        props: ['team']
    }
</script>