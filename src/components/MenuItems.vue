<template>
    <div class="weather-component">
        <h1>Commande</h1>

        <div v-if="loading" class="loading">
            Server unreachable. Please check your connection.
        </div>

        <div v-if="post" class="content">
            <table>
                <thead>
                    <tr>
                        <th><span style="visibility: hidden;">99</span></th>
                        <th>Nom</th>
                        <th>Prix</th>
                        <th><span style="visibility: hidden;">❌</span></th>
                    </tr>
                </thead>
                <tbody>
                    <tr v-for="menuitem in post" :key="menuitem.id">
                        <td @click="orderimpl[menuitem.id]++"><span v-if="orderimpl[menuitem.id] > 0">{{ orderimpl[menuitem.id] }}</span></td>
                        <td @click="orderimpl[menuitem.id]++">{{ menuitem.name }}</td>
                        <td @click="orderimpl[menuitem.id]++">{{ menuitem.price }} €</td>
                        <td><span v-if="orderimpl[menuitem.id] > 0" @click="orderimpl[menuitem.id]--">❌</span></td>
                    </tr>
                </tbody>
            </table>
            <div>
                <span v-if="Object.values(orderimpl).reduce((a, b) => a + b, 0) <= 0">Tapez sur les lignes<br>pour ajouter<br>des consommations<br>à la commande</span>
                <span v-else><button>Envoyer</button></span>
            </div>
        </div>
    </div>
</template>

<script lang="js">
    import { defineComponent } from 'vue';

    export default defineComponent({
        data() {
            return {
                loading: false,
                post: null,
                orderimpl : {}
            };
        },
        async created() {
            // fetch the data when the view is created and the data is
            // already being observed
            await this.fetchData();
        },
        watch: {
            // call again the method if the route changes
            '$route': 'fetchData'
        },
        methods: {
            async fetchData() {
                this.post = null;
                this.loading = true;

                var response = await fetch('api/menu');
                if (response.ok) {
                    this.post = await response.json();
                    this.loading = false;
                    this.post.forEach(element => {
                        this.orderimpl[element.id] = 0;
                    });
                }
            }
        },
    });
</script>

<style scoped>
th {
    font-weight: bold;
}

th, td {
    padding-left: .5rem;
    padding-right: .5rem;
    border: 1px solid black;
    user-select: none;
}

.weather-component {
    text-align: center;
}

table {
    margin-left: auto;
    margin-right: auto;
    border-collapse: collapse;
}
</style>