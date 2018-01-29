<style scoped>
    .input{
        height: 30px;
        -webkit-appearance: none;
        -moz-appearance: none;
        appearance: none;
        background-color: #fff;
        background-image: none;
        border-radius: 4px;
        border: 1px solid #bfcbd9;
        box-sizing: border-box;
        color: #1f2d3d;
        display: inline-block;
        font-size: 14px;
        line-height: 1;
        outline: none;
        padding: 3px 10px;
        transition: border-color .2s cubic-bezier(.645,.045,.355,1);
        width: 100%;
        padding-right: 35px;
    }
    .select-ul{
        padding: 0;
        list-style: none;
        margin: 0;
        max-height: 274px;
    }
    .select-li{
        font-size: 14px;
        padding: 8px 10px;
        position: relative;
        white-space: nowrap;
        overflow: hidden;
        text-overflow: ellipsis;
        color: #48576a;
        height: 36px;
        line-height: 1.5;
        box-sizing: border-box;
        cursor: pointer;
    }
    .select-li:hover{
        background-color: #e4e8f1;
    }
    .select-box{
        position: fixed;
        z-index: 1001;
        border: 1px solid #d1dbe5;
        border-radius: 2px;
        background-color: #fff;
        box-shadow: 0 2px 4px rgba(0,0,0,.12), 0 0 6px rgba(0,0,0,.04);
        box-sizing: border-box;
        margin: 5px 0;
        /*width: 100%;*/
        overflow-x: hidden;
        overflow-y: scroll;
        margin-bottom: -15px;
        margin-right: -15px;
        max-height: 274px;
        max-width: 300px;
        min-width:180px;
    }
    .selected,.selected:hover{
        color: #fff;
        background-color: #20a0ff;
    }
    .isNone{
        display: none !important;
    }
</style>
<script>
    export default {
        name:'BSelect',
        props:{
            option:{
                type:Array,
                default(){
                    return []
                }
            },
            defaultValue:{
//                type:String,
                default:''
            },
            placeholder:{
                type:String,
                default:''
            },
        },
        data(){
            return {
                active:'',
                currentValue:'',
                isFocus:false,
                error:{}
            }
        },
        mounted(){
            this.currentValue=this.defaultValue
        },
        watch:{
            defaultValue(value){
                if(Object.prototype.toString.call(value)=='[object Object]'){
                    this.error=value
                    this.currentValue=this.error.value;
                }else {
                    this.error={}
                    this.currentValue=value;
                }

            }
        },
        methods:{
            itemClick(item,i){
                this.currentValue=item.label
                this.$emit('update:defaultValue',this.currentValue)
            },
            getResult(options){
                let result=[];
                if( !this.isFocus){
                    return []
                }
                if(!this.currentValue){
                    return options;
                }
                for (let i = 0;i < options.length;i++){
                  let option = options[i];
                  if(option.label.toString().indexOf(this.currentValue)>=0){
                      result.push(option);
                  }
                };
                return result;
            }
        },
        render(createElement){
            let {option,
                active,
                currentValue,
                getResult,
                isFocus
            }=this;
            let EVENT={};
           return createElement('div',{style:{'position': 'relative'}},[
                    createElement('i',{
                        style:{cursor: 'pointer'},
                        class:['el-input__icon',{
                            'el-icon-caret-bottom':!this.isFocus,
                            'el-icon-circle-close':this.isFocus,
                        }],
                        on:{
                            click:()=>{
                                if(this.isFocus){
                                    this.currentValue='';
                                    this.$emit('update:defaultValue',this.currentValue)
                                    this.$emit('change')
                                    this.$emit('mouse-up')
                                }

                            }
                        }
                    }),
                    createElement('input',{
                        attrs:{
                            type:'text',
                            icon:"caret-top",
                        },
                        domProps:{
                            value:this.currentValue
                        },
                        on:{
                            input:(e)=>{
                                this.currentValue=e.target.value;
                            },
                            focus:(e)=>{
                                this.$emit('visible-change')
                                setTimeout(_=>{
                                    e.target.nextElementSibling.style.top=(e.target.getBoundingClientRect().bottom)+'px';
                                    e.target.nextElementSibling.style.left=(e.target.getBoundingClientRect().left)+'px';
                                    let viewH=e.target.nextElementSibling.getBoundingClientRect().height;
                                    if((document.body.clientHeight-e.target.getBoundingClientRect().bottom)<viewH){

                                        e.target.nextElementSibling.style.top=(e.target.getBoundingClientRect().top-6-viewH)+'px';
                                    }
                                },0)

                                this.isFocus=true;
                            },
                            blur:(e)=>{
                                setTimeout(_=>{
                                    this.isFocus=false
                                    if(Object.prototype.toString.call(this.defaultValue)=='[object Object]'){
                                        this.error=this.defaultValue
                                        this.currentValue=this.error.value;
                                    }else {
                                        this.error={}
                                        this.currentValue=this.defaultValue;
                                    }
                                },200)

                            },
                        },
                        style: {
                            borderColor:this.error.color,
                            backgroundColor: this.error['color']?'#fcffd5':''
                        },
                        class:['input',{}]
                    }),
                    createElement('div',{'class':['select-box',{'isNone':!this.isFocus}]},[
                        createElement('ul',{'class':['select-ul']},
                            [
                                (()=>{
                                    let result=getResult(option);
                                    if(result.length>0){
                                        return result.map((item, i) => {
                                            return createElement('li', {
                                                'class': ['select-li', {'selected': i === active}],
                                                on: {
                                                    click: () => {
                                                        this.itemClick(item, i);
                                                        this.active = i;
                                                        this.$emit('change')
                                                    },
                                                    mouseup:()=>{
                                                        setTimeout(_=>{
                                                            this.$emit('mouse-up')
                                                        },0)

                                                    }
                                                },
                                            }, item.label)
                                        })

                                    }else {
                                        return createElement('li',{
                                            'class':['select-li'],
                                        },'无匹配数据')
                                    }
                                })()
                            ]
                        )
                    ]),
            ])
//            return createElement('div',{style:'position:relative;'},[
//                createElement('span',{style:'margin-left:100px;width:18px;overflow:hidden;'},[
//                    createElement('select',{style:'width:100%;margin-left:-100px'},[
//                        Array.apply(null,{length:0}).map(_=>{
//                            return createElement('option',{domProps:{value:'123'}},234)
//                        })
//                    ]),
//                createElement('input',{style:'width:155px;position:absolute;left:0px;'})
//                ]
//
//                )
//            ])
        }
    }
</script>