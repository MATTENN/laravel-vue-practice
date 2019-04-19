<template>
    <div class="container">
        <b-modal ref="modal" id="modal-1" @ok="handleOk" title="項目を編集">
            <p class="my-4">{{ name }} さんのデータを編集します。</p>
            <b-form  v-if="show">
                <b-form-group id="input-group-2" label="Your Name:" label-for="input-2">
                    <b-form-input
                            id="input-2"
                            v-model="form.name"
                            required
                            placeholder="Enter name"
                    ></b-form-input>
                </b-form-group>
            </b-form>
        </b-modal>
        <div class="row justify-content-center">
            <div class="col-md-8">
                <div class="card">
                    <div class="card-header">Example Component</div>

                    <div class="card-body">

                        <input v-model="newUser.name">
                        <b-button v-on:click="doAdd()">add</b-button>
                        <ul>
                            <li v-for="(user,index) in users" v-bind:key="user.id">
                                 {{ user.name }} <span v-on:click="doEdit(index)" v-b-modal.modal-1>[Edit]</span> <span v-on:click="doDelete(index)">[Del]</span>
                            </li>
                        </ul>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
    export default {
        data() {
            return {
                name: "test",
                newName: "",
                tempName: "",
                users: null,
                show: true,
                newUser: {
                    name: null
                },
                user: {
                    name: null
                },
                form: {
                    name: null
                },
                keyid : 0
            }
        },
        methods: {
            doAdd() {
                axios.post('/members' ,this.newUser).then(res => {
                    console.log(res);
                    this.getNames();
                });
                alert('Message sent.');

            },
            doEdit(index){
                this.name = this.users[index].name;
                this.form.name = this.users[index].name;
                this.keyid = this.users[index].id;
                console.log(index);
            },
            doDelete(index){
                if(window.confirm( this.users[index].id + "を削除してもよろしいですか？")){
                    axios.delete('/members/' + this.users[index].id).then(res => {
                        console.log(res);
                        this.users.splice(index, 1);
                    });
                }
                console.log(index);

            },
            handleOk(bvModalEvt){
                bvModalEvt.preventDefault()
                if (!this.form.name) {
                    alert('Please enter your name')
                } else {
                    axios.put('/members/' + this.keyid, this.form).then(res => {
                        //alert("「" + this.form.name + "」更新完了");
                        //console.log(res);
                        this.getNames();
                    })
                    .catch(error => {
                        alert("「" + this.form.name + "」更新失敗");
                        console.log(error, this.form.id, this.form);
                    });
                }
                this.$nextTick(() => {
                    // Wrapped in $nextTick to ensure DOM is rendered before closing
                    this.$refs.modal.hide();
                })
            },
            getNames(){
                axios.get('/members')
                    .then(response => (this.users = response.data))
            }
        },
        mounted() {
            this.getNames();
        },
        updated(){

        },

    }
</script>
