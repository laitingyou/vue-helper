<template>

    <div @click="focusNewTag()"  class="vue-input-tag-wrapper">
    <span v-for="(tag, index) in tags" v-bind:key="index" class="input-tag">
      <span>{{ tag.EmployeeName }}</span>
      <a @click.prevent.stop="remove(index)" class="remove"></a>
    </span>
        <input :placeholder="placeholder" type="text" v-model="newTag" v-on:keydown.delete.stop="removeLastTag()" v-on:keydown.enter.188.tab.prevent.stop="addNew(newTag)" class="new-tag"/>
    </div>

</template>
<script>
    export default {
        name: 'InputTag',

        props: {
            tags: {
                type: Array,
                default: () => []
            },
            placeholder: {
                type: String,
                default: ''
            },
            onChange: {
                type: Function
            }
        },

        data () {
            return {
                newTag: ''
            }
        },

        watch:{
            newTag(val){
                this.$emit('update:value',val)
            }
        },

        methods: {
            focusNewTag () {
                this.$el.querySelector('.new-tag').focus()
            },

            addNew (tag) {
                if (tag && this.tags.indexOf(tag) === -1) {
                    this.tags.push(tag)
                    this.tagChange()
                }
                this.newTag = ''
            },


            remove (index) {
                this.tags.splice(index, 1)
                this.tagChange()
            },

            removeLastTag () {
                if (this.newTag) { return }
                this.tags.pop()
                this.tagChange()
            },

            tagChange () {
                if (this.onChange) {
                    this.onChange(JSON.parse(JSON.stringify(this.tags)))
                }
            }
        }
    }
</script>
<style>

    .vue-input-tag-wrapper {
        background-color: #fff;
        border-radius: 3px;
        border: none;
        overflow: hidden;
        padding-left: 4px;
        padding-top: 4px;
        cursor: text;
        text-align: left;
        -webkit-appearance: textfield;
    }

    .vue-input-tag-wrapper .input-tag {
        border-radius: 14px;
        padding: 4px 15px;
        background-color: #d8d8d8;
        color: #313131;
        display: inline-block;
        font-size: 13px;
        font-weight: 400;
        margin-bottom: 4px;
        margin-right: 4px;
    }

    .vue-input-tag-wrapper .input-tag .remove {
        cursor: pointer;
        font-weight: bold;
        color: #fff;
    }

    .vue-input-tag-wrapper .input-tag .remove:hover {
        text-decoration: none;
    }

    .vue-input-tag-wrapper .input-tag .remove::before {
        content: "  x";
    }

    .vue-input-tag-wrapper .new-tag {
        background: transparent;
        border: 0;
        color: #777;
        font-size: 13px;
        font-weight: 400;
        margin-bottom: 6px;
        margin-top: 1px;
        outline: none;
        padding: 4px;
        padding-left: 0;
        width: 80px;
    }

    .vue-input-tag-wrapper.read-only {
        cursor: default;
    }

</style>
