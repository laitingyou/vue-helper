<template>
    <div>
        <input id="view" type="text" v-model="viewValue" :class="{err:isErr}">
        <br>
        <input style="display: none" id="controller" type="text" v-model="controllerValue">
    </div>
</template>
<style scoped>
    .err{
        border: 1px solid red;
    }
</style>
<script>
    export default {
        data(){
            return {
                viewValue:'',
                controllerValue:'',
                isErr:false
            }
        },
        props:{
            value:{
                default:''
            }
        },
        mounted(){
            this.controllerValue=this.value;
            console.log(this.value)
        },
        computed:{

        },
        watch:{
            controllerValue(val){
                if(/\D/.test(val)){
                    this.isErr=true;
                }else {
                    this.isErr=false;
                }
                this.viewValue=val.toString().replace(/\B(?=(\d{3})+(?!\d))/g, ',')
            },
            viewValue(val){
                this.controllerValue=val.toString().replace(/,?/g, '')
                this.$emit('value:update',val.toString().replace(/,?/g, ''))
                this.$emit('inputChange',val.toString().replace(/,?/g, ''))
            }
        },
        methods:{

        }
    }
</script>
