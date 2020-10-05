<template>
    <div>
        <br>
        <h1>Add Contact</h1>
        <form action="#" @submit.prevent = "edit? updateContact(contact.id) : createContact()">
            <div class="form-group">
                <label>Name:</label>
                <input type="text" v-model="contact.name" class="form-control" name="name" placeholder="Name">
            </div>
            <div class="form-group">
                <label>Email:</label>
                <input type="text" v-model="contact.email" class="form-control" name="email" placeholder="Email">
            </div>
            <div class="form-group">
                <label>Phone:</label>
                <input type="text" v-model="contact.phone" class="form-control" name="phone" placeholder="Phone">
            </div>
            <button type="submit" v-show="!edit" class="btn btn-primary">Submit</button>
            <button type="submit" v-show="edit" class="btn btn-primary">Update</button>
        </form>
        <br>
        <h1>List of contacts</h1>
        <ul class="list-group">
            <li class="list-group-item" v-for="contact in list">
                {{ contact.name }} [{{ contact.email }}] [{{ contact.phone }}]
                <button @click="editContact(contact.id)" class="btn btn-success btn-sm">Edit</button>
                <button v-on:click="deleteContact(contact.id)" class="btn btn-danger btn-sm">Delete</button>
            </li>
        </ul>
    </div>
</template>

<script>
    export default {
        data: function () {
            return {
                edit: false,
                list: [],
                contact: {
                    id: '',
                    name: '',
                    email: '',
                    phone: ''
                }
            }
        },
        mounted: function () {
            this.fetchContactList();
        },
        methods: {
            fetchContactList () {
                axios.get('api/contacts')
                .then((response) =>  {
                    this.list = response.data;
                })
                .catch((error) => {
                    console.log(error);
                })
            },
            createContact () {
                let params = Object.assign({}, this.contact);
                axios.post('api/contact/store', params)
                .then(() => {
                    this.contact.name = '';
                    this.contact.email = '';
                    this.contact.phone = '';
                    this.edit = false;
                    this.fetchContactList();
                })
                .catch((error) => {
                    console.log(error)
                });
            },
            editContact (id) {
                axios.get('api/contact/'+id)
                .then((res) => {
                    this.contact.id = res.data.id;
                    this.contact.name = res.data.name;
                    this.contact.email = res.data.email;
                    this.contact.phone = res.data.phone;
                    this.edit = true;
                })
                .catch((error) => {
                    console.log(error)
                })
            },
            updateContact (id) {
                let params = Object.assign({}, this.contact);
                axios.patch('api/contact/'+id, params)
                    .then(() => {
                        this.contact.name = '';
                        this.contact.email = '';
                        this.contact.phone = '';
                        this.edit = false;
                        this.fetchContactList();
                    })
                    .catch((error) => {
                        console.log(error)
                    });
            },
            deleteContact (id) {
                if(confirm("Do you really want to delete?")) {
                    axios.delete('api/contact/'+id)
                        .then((res) => {
                            this.fetchContactList();
                        })
                        .catch((error) => {
                            console.log(error)
                        })
                }
            }
        }
    }
</script>
