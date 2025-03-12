<template>
    <div class="weather-component">
        <h1>Commande</h1>

        <div v-if="loading" class="loading">
            Server unreachable. Please check your connection.
        </div>

        <div v-if="getmenuresponse" class="content">
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
                    <tr v-for="menuitem in getmenuresponse" :key="menuitem.id">
                        <td @click="orderimpl[menuitem.id]++"><span v-if="orderimpl[menuitem.id] > 0">{{ orderimpl[menuitem.id] }}</span></td>
                        <td @click="orderimpl[menuitem.id]++">{{ menuitem.name }}</td>
                        <td @click="orderimpl[menuitem.id]++">{{ menuitem.price }} €</td>
                        <td><span v-if="orderimpl[menuitem.id] > 0" @click="orderimpl[menuitem.id]--">❌</span></td>
                    </tr>
                </tbody>
            </table>
            <div>
                <span v-if="Object.values(orderimpl).reduce((a, b) => a + b, 0) <= 0">Tapez sur les lignes<br>pour ajouter<br>des consommations<br>à la commande</span>
                <span v-else><button @click="async() => await this.postOrder()">Envoyer</button></span>
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
                getmenuresponse: null,
                postorderresponse: null,
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
                this.getmenuresponse = null;
                this.loading = true;

                var response = await fetch('api/menu');
                if (response.ok) {
                    this.getmenuresponse = await response.json();
                    this.loading = false;
                    this.getmenuresponse.forEach(element => {
                        this.orderimpl[element.id] = 0;
                    });
                }
            },
            async postOrder(){
                this.postorderresponse = null;
                this.loading = true;

                const params = new URLSearchParams(window.location.search);
                var response = await fetch('api/orders?cust=' + params.get("customerId"), {method : 'POST', headers:{'Content-Type': 'application/json'}, body:JSON.stringify({'customerId': params.get("customerId"), 'orderItems': Object.entries(this.orderimpl).map((arr)=>({menuId: arr[0], amount: arr[1]}))})});
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