<template>
    <div class="card">
        <div class="card-header">
            <like :content="data"></like>
            {{data.user}}
        </div>
        <edit-reply :reply=data  v-if="editing"></edit-reply>
        <div class="card-body" v-else>
            <blockquote class="blockquote mb-0">
                <p v-html="reply"></p>

            </blockquote>
        </div>
        <div class="card-footer text-muted">
            <footer class="blockquote-footer">{{data.created_at}}</footer>
            <div v-if="!editing">
                <i class="fas fa-pen fas1" @click="edit" v-if="own"></i>
                <i class="fas fa-trash fas2" @click="destroy" v-if="own"></i>
            </div>
            <button class="fas fa-check fas3" @click="update" v-if="editing"></button>
            <i class="fas fa-times fas4" v-if="editing" @click="cancel"></i>
        </div>
    </div>
</template>

<script>
    import Reply from './reply'
    import EditReply from "./EditReply";
    import Like from "../likes/Like";
    export default {
        components: {Like, EditReply},
        props:['data','index'],
        data(){
            return {
                editing:false
            }
        },
        component:{Reply},
        computed:{
            own(){
              return User.own(this.data.user_id)
            },
            reply(){
                return md.parse(this.data.reply)
            }

        },
        created(){
            this.listen()
        },
        methods: {
            destroy(){
                EventBus.$emit('deleteReply',this.index)
            },
            edit(){
                this.editing = true

            },
            update(){
                axios.patch(`/api/question/${this.data.question_slug}/reply/${this.data.id}`,{body:this.data.reply})
                    .then(res => this.cancel())

            },
            cancel(){
                EventBus.$emit('cancelEditing')

            },
            listen(){
                EventBus.$on('cancelEditing', () => {
                    this.editing = false
                })
            }
        },
        name: "reply"
    }
</script>

<style scoped>
    .fas1 {
        position: absolute;
        right: 3%;
        top: 35%;
        cursor:pointer;
        color: orange;
    }
    .fas2 {
        position: absolute;
        right: 1.5%;
        top: 35%;
        cursor:pointer;
        color: red;
    }
    .fas3 {
        position: absolute;
        right: 3%;
        top: 35%;
        cursor:pointer;
        color: green;
    }
    .fas4 {
        position: absolute;
        right: 1.5%;
        top: 35%;
        cursor:pointer;
        color: red;
    }
    .card-header{
        position: relative;
    }
    .card-footer {
        position: relative;
    }
</style>