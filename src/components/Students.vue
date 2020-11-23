<template>
   <div>
        <link :href = "style" rel="stylesheet">
         <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.3.1/css/bootstrap.min.css" integrity="sha384-ggOyR0iXCbMQv3Xipma34MD+dH/1fQ784/j6cY/iJTQUOhcWr7x9JvoRxT2MZw1T" crossorigin="anonymous">
        <center><h3>Count: {{studentsCount}}</h3></center>
        <table class="table table-dark">
            <tr v-for="item in students"  v-bind:key="item._id"> 
                <td><router-link v-bind:to="'/student-info/'+item._id"> {{item.name}}</router-link></td><td><input type="checkbox" v-model="item.isDonePr"></td><td>{{item.group}}</td><td>{{ item.mark}}</td>
                <td><a href = "#" v-on:click.prevent = "removeStud(item._id)" v-show = "item.group == getCurrentUser.group">Удалить</a></td>
                 <td v-if = "item.name != search"><a href = "#" @click="changeVal(item.name)">Изменить</a></td>
                <td v-if = "item.name == search">
                    <a href = "#" @click="changeStud(item._id)">Изменино</a>
                </td>
                
            </tr>
            
        </table>
        
        
       <div class="center" >
            <input type="text" v-model = "student.name">
            <select v-model="student.group">
                 <option>Group</option>
                <option value="RPZ 17 1/9">РПЗ 17 1/9</option>
                <option value="RPZ 17 2/9">РПЗ 17 2/9</option>
            </select>
             MARK: <input type="number" v-model = "student.mark">
           <input type = "checkbox" v-model = "student.isDonePr">
            <button v-on:click="add()">Add</button>
        <button v-on:click = "changeStyle()"> Color </button>
        </div> 
   </div>
</template>

<script>
    import Vue from 'vue'
    import axios from 'axios'
    import VueAxios from 'vue-axios'
 
    export default {
       data: function() {
           return {
                search:"",
                students: [],
                student: {name:"", group:"", mark:"", isDonePr:""},
                style:"components/pink.css",
           };
        },

        mounted: async function(){      
               if(this.$store.getters.getStyle){
                this.style = "components/pink.css"
            }
            else{
                this.style = "components/blue.css"
            } 
            let response = await Vue.axios.get("http://46.101.212.195:3000/students");
            this.students = response.data;
            this.$store.commit('setCount', this.students.length)
            
        },
        
                        computed: {
                studentsCount () {
                    return this.$store.getters.getCount
                },
                getCurrentUser () {
                return this.$store.getters.getUser
                            }
                },

        methods: {
            removeStud: function(id){
                Vue.axios.delete("http://46.101.212.195:3000/students/" + id)
                .then((response) => {
                    console.log(response.data)
                    this.reload();
                })
            },
            changeValue: function(name){
                this.student = this.students.find(elem => {
                    if(elem.name == name){
                        return elem
                    }
                });
                this.search = name
            },
           changeStud: function(id){
                Vue.axios.put("http://46.101.212.195:3000/students/" + id,{
                    name: this.student.name,
                    group: this.student.group,
                    mark: this.student.mark,
                    isDonePr: this.student.isDonePr
                })
                .then((response) => {
                    console.log(response.data)
                    this.reload();
                })
            },
              changeStyle:function(){
                
               
             this.$store.commit('setStyle', !this.$store.getters.getStyle)
                localStorage.style = this.$store.getters.getStyle
                if(this.$store.getters.getStyle == false){
                    this.style = "components/blue.css"
                }
                else{
                    this.style = "components/pink.css"
                }
            },
            add: function(){
                Vue.axios.post("http://46.101.212.195:3000/students",{
                    name: this.student.name,
                    group: this.student.group,
                    mark: this.student.mark,
                    isDonePr: this.student.isDonePr
                })
                
                .then((response) => {
                    console.log(response.data)
                    this.reload();
                })
            },
            reload: function(){
                Vue.axios.get("http://46.101.212.195:3000/students").then((response) => {
                console.log(response.data)
                this.students = response.data;

                })
            }
        }
    }
</script>
<style scoped>

</style>


