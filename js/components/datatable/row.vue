<template>
<tr class="pointer"
    :class="{ 'active': selected }"
    @click="item_click(item)">
    <td v-for="field in fields" track-by="key"
        :class="{
            'text-center': field.align === 'center',
            'text-left': field.align === 'left',
            'text-right': field.align === 'right',
            'ellipsis': field.ellipsis
        }">
        <component :is="field.type || 'text'" :item="item" :field="field">
        </component>
    </td>
</tr>
</template>

<script>
import Vue from 'vue';
import Cell from './cell.vue';

export default {
    name: 'datatable-row',
    replace: true,
    props: {
        item: Object,
        fields: Array,
        selected: {
            type: Boolean,
            default: false
        }
    },
    created: function() {
        // Loads cells from fields definitions
        for(let field of this.fields) {
            this.load_cell(field.type || 'text');
        }
    },
    methods: {
        item_click: function(item) {
            this.$dispatch('datatable:item:click', item);
        },
        /**
         * Asynchronously load view (Webpack Lazy loading compatible)
         * @param  {string}   name     the filename (basename) of the view to load.
         * @param  {Function} callback An optionnal callback executed
         *                             in the application scope when
         *                             the view is loaded
         */
        load_cell: function(name) {
            if (!this.$options.components.hasOwnProperty(name)) {
                let options = require('./cells/' + name + '.vue');
                if (!options.hasOwnProperty('mixins')) {
                    options.mixins = [];
                }
                if (!(Cell in options.mixins)) {
                    options.mixins.push(Cell);
                }
                this.$options.components[name] = Vue.extend(options);
            }
        }
    }
};
</script>
