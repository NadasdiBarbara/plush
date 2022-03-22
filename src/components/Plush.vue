<template>
<div>
    <h1>Plush</h1>
        <table>
            <thead>
                <tr>
                    <th>Name</th>
                    <th>Operations</th>
                </tr>
            </thead>
            <tbody>
                <tr v-for="plush in plushes" v-bind:key="plush.id">
                    <td>{{ plush.name }}</td>
                    <td>
                        <button @click="loadEditData(plush.id)">Edit</button>
                        <button @click="deletePlush(plush.id)">Delete</button>
                    </td>
                </tr>
                <tr>
                    <td>
                        <input type="text" v-model="plush.name">
                    </td>
                    <td>
                        <button v-if="newData" @click="newPlush">Add</button>
                        <button v-if="!newData" @click="saveEditedData">Save</button>
                        <button @click="clearForm">Cancel</button>
                    </td>
                </tr>
            </tbody>
        </table>
    </div>
</template>

<script>
export default {
   name: 'Plush',
    data()  {
        return {
            newData: true,
            plush: {
                id: null,
                name: ''
            },
            plushes: []
        }
    },
    methods: {
        async loadData() {
            let Response = await fetch('http://127.0.0.1:8000/api/plush');
            let data = await Response.json();
            this.plushes = data;
        },
        async newPlush() {
            await fetch('http://127.0.0.1:8000/api/plush', {
                method: 'POST',
                headers: {
                    'Content-Type': 'application/json',
                    'Accept': 'application/json'
                },
                body: JSON.stringify(this.plush)
            });
            await this.loadData();
            this.clearForm();
        },
        async deletePlush(id) {
            let Response = await fetch(`http://127.0.0.1:8000/api/plush/${id}`, {
                method: 'DELETE'
            });
            console.log(Response);
            await this.loadData();
        },
        async loadEditData(id) {
            let Response = await fetch(`http://127.0.0.1:8000/api/plush/${id}`);
            let data = await Response.json();
            this.plush = {...data};
            this.newData = false;
        },
        async saveEditedData() {
            await fetch(`http://127.0.0.1:8000/api/plush/${this.plush.id}`, {
                method: 'PATCH',
                headers: {
                    'Content-Type': 'application/json',
                    'Accept': 'application/json'
                },
                body: JSON.stringify(this.plush) 
            });
            this.loadData();
            this.clearForm();
            this.newData = true;
        },
        clearForm() {
            this.plush = {
                id: null,
                name: ''
            };
        }
    },
    mounted() {
        this.loadData();
    }
}
</script> 