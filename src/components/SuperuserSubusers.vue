<template>
    <div>
        <vs-card class="subusers-count-card">
            <div class="">
                <vs-row vs-w="12">
                    <vs-col vs-lg="3" vs-sm="5" vs-offset="1">
                        <h5>Operators</h5>  
                        <h6 style="padding-left:2.6rem">{{oparators.length}}</h6>
                    </vs-col>
                    <vs-col vs-lg="3" vs-sm="5">
                        <h5>Technecians</h5>
                        <h6 style="padding-left:3rem">{{technicians.length}}</h6>
                    </vs-col>
                    <vs-col vs-lg="5" vs-sm="12" vs-type="flex" vs-justify="flex-end">
                        <vs-button @click="isCreateSubUserModalActive = true; newSubuser.userName = ''; newSubuser.role = ''" :color="primaryColor" icon="group_add" type="line" class="add-subusers-button">Add Sub-users</vs-button>
                    </vs-col>
                </vs-row>
            </div>
        </vs-card>
        <vs-card class="subusers-table-card">
            <div slot="header" class="subusers-table-first-row">
                <vs-row vs-w="12">
                    <vs-col vs-w="4" vs-type="flex" vs-justify="center" vs-align="center">
                        <h5>Name</h5>
                    </vs-col>
                    <vs-col vs-w="4" vs-type="flex" vs-justify="center" vs-align="center">
                        <h5>Code</h5>
                    </vs-col>
                    <vs-col vs-w="4" vs-type="flex" vs-justify="center" vs-align="center">
                        <h5>Role</h5>
                    </vs-col>
                </vs-row>
            </div>
            <div class="table-rows">
                <vs-card class="table-row" v-for="(subuser, index) in subusers" :key="index">
                    <div style="position: relative">
                        <vs-dropdown class="drob-down" :color="primaryColor" vs-trigger-click vs-custom-content>
                            <vs-icon :color="primaryColor" icon="more_horiz"></vs-icon>
                            <vs-dropdown-menu>
                                <div class="text-center" style="width: 9rem; padding: .2rem">
                                    <vs-button @click="openEditSubuserModal(subuser.code, subuser.userName, subuser.role)" color="#C0C0C0" type="line" style="width: 100%; margin: .2rem">Edit Sub-user</vs-button>
                                    <!-- <span style="color: #00A99D">|</span> -->
                                    <vs-button @click="openConfirmDelete(subuser.userName, subuser.code)" color="danger" type="line" style="width: 100%; margin: .2rem">Delete Sub-user</vs-button>
                                </div>
                            </vs-dropdown-menu>
                        </vs-dropdown>

                        <vs-row vs-w="12">
                            <vs-col vs-w="4" vs-type="flex" vs-justify="center" vs-align="center">
                                <p class="table-text">{{subuser.userName}}</p>
                            </vs-col>
                            <vs-col vs-w="4" vs-type="flex" vs-justify="center" vs-align="center">
                                <p class="table-text">{{subuser.code}}</p>
                            </vs-col>
                            <vs-col vs-w="4" vs-type="flex" vs-justify="center" vs-align="center">
                                <p class="table-text">{{subuser.role}}</p>
                            </vs-col>
                        </vs-row>
                    </div>
                </vs-card>
            </div>
        </vs-card>
        
        <!-- add subuser modal -->
        <vs-prompt class=""
            title="Create Sub-user"
            :color="primaryColor"
            button-accept="line"
            button-cancel="line"
            accept-text="Generate"
            cancel-text="Close"
            @cancel="newSubuser.userName='',newSubuser.role=''"
            @accept="acceptSubuserCreate()"
            @close="closeSubuserCreate()"
            :active.sync="isCreateSubUserModalActive">
            <div class="con-exemple-prompt">
                <span class="subuser-form-text">User Name</span>
                <vs-input class="username-input" placeholder="Wrtie here..." v-model="newSubuser.userName" :color="primaryColor"/>
                <span class="subuser-form-text">Role</span>
                <vs-radio class="subuser-form-radio" :color="primaryColor" v-model="newSubuser.role" vs-name="Operator" vs-value="Operator">Operator</vs-radio>
                <vs-radio class="subuser-form-radio" :color="primaryColor" v-model="newSubuser.role" vs-name="Technician" vs-value="Technician">Technician</vs-radio>
            </div>
        </vs-prompt>
        <!-- edit subuser modal -->
        <vs-prompt class=""
            title="Edit Sub-user"
            color="#C0C0C0"
            button-accept="line"
            button-cancel="line"
            accept-text="Edit"
            cancel-text="Close"
            @cancel="newSubuser.userName='',newSubuser.role=''"
            @accept="acceptSubuserEdit()"
            @close="closeSubuserEdit()"
            :active.sync="isEditSubUserModalActive">
            <div class="con-exemple-prompt">
                <span class="subuser-form-text">User Name</span>
                <vs-input class="username-input" placeholder="Wrtie here..." v-model="editSubuser.userName" :color="primaryColor"/>
                <span class="subuser-form-text">Role</span>
                <vs-radio class="subuser-form-radio" :color="primaryColor" v-model="editSubuser.role" vs-name="Operator" vs-value="Operator">Operator</vs-radio>
                <vs-radio class="subuser-form-radio" :color="primaryColor" v-model="editSubuser.role" vs-name="Technician" vs-value="Technician">Technician</vs-radio>
            </div>
        </vs-prompt>
    </div>
</template>

<script>
    import axios from 'axios';

    export default {
        data() {
            return {
                primaryColor: "#00A99D",
                subusers: [],
                oparators: [],
                technicians: [],
                
                newSubuser: {
                    userName: '',
                    role: ''
                },
                editSubuser: {
                    code: '',
                    userName: '',
                    role: ''
                },
                
                isCreateSubUserModalActive: false,
                isEditSubUserModalActive: false,
                
                deleteSubuser: {
                    code: '',
                },

                APIsEndPoints: {
                    getSubusers: "/superuser/subusers",
                    generateSubuser: "/superuser/generateuser",
                    editSubuser: "/superuser/subusers/edit",
                    deleteSubuser: "/superuser/subusers/delete"
                }
            }
        },
        
        methods: {
            callDataAPIs(){
                this.getSubsersAPI(this.APIsEndPoints.getSubusers)
            },
            getSubsersAPI(APIEndPoint){
                this.oparators = []
                this.technicians = []

                this.$vs.loading({
                    color: this.primaryColor
                })
                
                axios.get(this.$websiteLink + APIEndPoint, {
                    headers:{
                        'Authorization': localStorage.getItem(this.$superuserToken)
                    }
                })
                .then(response => {
                    this.subusers = response.data;
                        this.subusers.forEach(subuser => {
                            if (subuser.role == "Operator") {
                                this.oparators.push(subuser)
                            } else {
                                this.technicians.push(subuser)
                            }
                        });
                    }
                )
                .catch(error => {
                    this.catchAPIError(error)
                })
                .finally(() => {
                    this.$vs.loading.close()
                })
            },

            // add, edit, and delete subusers modeals
            acceptSubuserCreate(){
                this.editOrAddSubuser(this.APIsEndPoints.generateSubuser, this.newSubuser, 'Sub-user Generated Successfully')
            },
            closeSubuserCreate(){
                this.$vs.notify({
                    color:'danger',
                    title:'you did not generate any sub-users',
                    text:'you can always change your mind!'
                })
            },

            openEditSubuserModal(code, userName, role){
                this.isEditSubUserModalActive = true;

                this.editSubuser.code = code;
                this.editSubuser.userName = userName;
                this.editSubuser.role = role;
            },
            acceptSubuserEdit(){
                this.editOrAddSubuser(this.APIsEndPoints.editSubuser, this.editSubuser, '"' + this.editSubuser.userName + '" Edited Successfully')
            },
            closeSubuserEdit(){
                this.$vs.notify({
                    color:'danger',
                    title: '"' + this.editSubuser.userName + '" was not edited',
                    text:'you can always change your mind!'
                })
            },
            
            editOrAddSubuser(endPoint, body, title){
                this.$vs.loading({
                    color: this.primaryColor
                })
                axios.post(this.$websiteLink + endPoint, body, {
                    headers:{
                        'Authorization': localStorage.getItem(this.$superuserToken)
                    }
                })
                .then(response => {
                    if (response.status == 201 || response.status == 200) {
                            this.$vs.notify({
                                color: this.primaryColor,
                                title: title,
                                // text:''
                            })

                            // call the Original API again to repapulate the lists
                            this.getSubsersAPI(this.APIsEndPoints.getSubusers);
                        }
                    }
                )
                .catch(error => {
                    this.catchAPIError(error)
                })
                .finally(() => {
                    this.$vs.loading.close()
                })
            },
            
            openConfirmDelete(userName, code){
                this.$vs.dialog({
                    type:'confirm',
                    color: 'danger',
                    title: 'Delete Sub-user',
                    text: 'Are you sure you want to delete ' + '"' + userName + '"' + " you can't undo that action after pressing accept",
                    style: 'z-index: 50000;',
                    accept: this.delete,
                })

                this.deleteSubuser.code = code
            },
            delete(){
                this.$vs.loading({
                    color: this.primaryColor
                })
                axios.post(this.$websiteLink + this.APIsEndPoints.deleteSubuser, this.deleteSubuser, {
                    headers:{
                        'Authorization': localStorage.getItem(this.$superuserToken)
                    }
                })
                .then(response => {
                    if (response.status == 200) {
                            this.$vs.notify({
                                color: this.primaryColor,
                                title:'Sub-user Deleted Successfully',
                                text:'This sub-user Can not be Recovered '
                            })

                            // call the Original API again to repapulate the lists
                            this.getSubsersAPI(this.APIsEndPoints.getSubusers)
                        }
                    }
                )
                .catch(error => {
                    this.catchAPIError(error)
                })
                .finally(() => {
                    this.$vs.loading.close()
                })
            },
            // -----------------------------------------
            catchAPIError(error){
                if (error.response.status == 401) {
                    localStorage.setItem(this.$isSuperuserAuthorized, '');
                    localStorage.setItem(this.$superuserToken, '');
                    this.$vs.notify({title:'ERROR',text:'Your Session Expired, Please Login Again',color:'danger'})
                    setTimeout(() => this.$router.go('/login'), 2500);
                }
                else{
                    this.$vs.notify({title:'ERROR',text:'an Error Occured, Please Try Again',color:'danger'})
                    console.log(error);
                }
            },
        },
        
        beforeMount(){
            this.callDataAPIs();
        },
    }
</script>

<style scoped>
    :root{
        --primarycolor: #00A99D;
    }
    .subusers-count-card{
        background-color: rgb(236, 236, 236);
        /* box-shadow: 10px 10px var(--primarycolor); */
    }
    .add-subusers-button{
        border-radius: 10px;
    }
    .subusers-table-card{
        background-color: rgb(236, 236, 236);
    }
    .subusers-table-first-row{
        display: flex;
        justify-content: space-around;
    }
    .table-row{
        width: auto;
        margin: 1rem;
    }
    .drob-down{
        position: absolute; 
        top: -.6rem; 
        right: 0; 
    }
    .table-text{
        margin: 0 0 .3rem 0 ;
        font-size: 1.15rem;
        font-weight: 400;
    }
    .username-input{
        margin-bottom: .5rem;
        padding-left: 5%;
        width: 100%;
    }
    .subuser-form-text{
        font-size: .9rem;
        font-weight: 500;
        margin-left: .7rem;
    }
    .subuser-form-radio{
        justify-content: flex-start;
        margin: 0 0 .2rem 1.2rem;
    }
</style>