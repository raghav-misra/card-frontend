<template>
    <main>
        <MoneyIndicator />

        <div v-if="activePlayer" class="info-bar">
            <b class="h4">It's {{ activePlayer.name }}'s turn!</b>
        </div>

        <TableTop />
        <PlayerHand />
    </main>
</template>

<script lang="ts">
import Vue from "vue";
import Swal from "sweetalert2";
import connection from "~/connection";
import { IAttackBroadcast } from "~/@types";

export default Vue.extend({
    computed: {
        connection() {
            return connection;
        },
        activePlayer() {
            return connection.state.players?.[connection.state.activePlayerID];
        },
    },
    mounted() {
        connection.room?.onMessage("attacked", (d: IAttackBroadcast) => {
            const attackerName = connection.state.players?.[d.attacker]?.name;
            const targetName = connection.state.players?.[d.reciever]?.name;

            const healText =
                typeof d.attack.heal === "number" && d.attack.heal > 0
                    ? `${attackerName} healed for ${d.attack.heal}.`
                    : "";

            const attackText =
                typeof d.attack.damage === "number" && d.attack.damage > 0
                    ? `${targetName} took ${d.attack.damage} damage!`
                    : "";

            Swal.fire({
                toast: true,
                position: "top-end",
                title: `${attackerName} played!`,
                text: `${healText} ${attackText}`,
            });
        });
    },
});
</script>

<style scoped>
.info-bar {
    padding: 1rem;
}

main {
    align-items: center;
    overflow: hidden;
}
</style>
