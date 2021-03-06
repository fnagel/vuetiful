<!--
  - Copyright 2020 LABOR.digital
  -
  - Licensed under the Apache License, Version 2.0 (the "License");
  - you may not use this file except in compliance with the License.
  - You may obtain a copy of the License at
  -
  -     http://www.apache.org/licenses/LICENSE-2.0
  -
  - Unless required by applicable law or agreed to in writing, software
  - distributed under the License is distributed on an "AS IS" BASIS,
  - WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
  - See the License for the specific language governing permissions and
  - limitations under the License.
  -
  - Last modified: 2020.07.01 at 10:22
  -->

<template>
    <div class="tabs">
        <div class="tabs__titleContainer">
            <div v-for="(label, index) in preparedItems" @click="onClickOpenTab(index)" class="tabs__label"
                 :class="{'tabs__label--active': open === index}"
                 ref="titles">

                <!-- @slot Used to add additional elements before the label -->
                <slot name="beforeLabel"/>

                <!-- @slot Default label slot for your elements. As default the label from the given items will be used -->
                <slot name="label" :label="label">{{label}}</slot>

                <!-- @slot Used to add additional elements after the label -->
                <slot name="afterLabel"/>
            </div>
        </div>
        <div class="tabs__tabs" :class="classes">
            <transition-group name="tabs" mode="out-in"
                              @before-leave="beforeLeaving"
                              @after-leave="afterLeaving">
                <div v-for="(item, index) in preparedItems"
                     v-show="open === index"
                     :key="'tab-'+ item + index"
                     class="tabs__item"
                     :class="{'tabs__item--active': open === index}"
                     ref="contents">
                    <!-- @slot Used to add additional elements before the content -->
                    <slot name="beforeContent"/>

                    <!-- @slot Default item slot for your elements -->
                    <slot :name="item">{{item}}</slot>

                    <!-- @slot Used to add additional elements after the content -->
                    <slot name="afterContent"/>
                </div>
            </transition-group>
        </div>
    </div>
</template>

<script lang="ts">

    import {forEach} from "@labor-digital/helferlein/lib/Lists/forEach";

    export default {
        name: "BaseTabs",
        props: {

            /**
             * The list of items that should be used. The items are also the name
             * for the looped slots in which you can put your content in.
             */
            items: {
                type: Array,
                required: true
            },

            /**
             * If "items" contains an array of objects, this prop is used to select the object's property
             * which should be used as a label.
             */
            itemLabel: String,

            /**
             * Add custom classes if necessary to the tabs content container.
             */
            classes: String
        },
        computed: {
            preparedItems() {
                const items = [];
                forEach(this.items, (item, index) => {
                    items[index] = item[this.itemLabel] ?? item;
                });
                return items;
            }
        },
        data() {
            return {
                open: 0,
                oldTabHeight: 0,
                newTabHeight: 0
            };
        },
        methods: {
            onClickOpenTab(i) {
                this.open = i;

                /**
                 * Emits an event with "open" and the index of the tab
                 */
                this.$emit("open", i);
            },
            beforeLeaving(el) {
                el.style.position = "absolute";
            },
            afterLeaving(el) {
                el.style.position = "";
            }
        }
    };
</script>

<style scoped lang="sass" src="./BaseTabs.sass"></style>
