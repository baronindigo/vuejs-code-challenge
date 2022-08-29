<template>
    <div class="dropdown-filter-container">
        <label :class="classes" :for="name">{{label}}</label>
        <div @click="isVisible = !isVisible" class="selected-item">
            
            <div v-if="selectedItem"><span :id="selectedItem.id">{{selectedItem.name}}</span></div>
            <div v-else>{{placeholder}}</div>
            <i class="lpi-caret-down" :class="isVisible ? 'active' : ''"></i>
        </div>
        <div :class="isVisible ? 'active' : ''" class="dropdown-filter shadow">
            <div v-show="withFilter=='true'" class="search-container">
                <i class="lpi-search"></i>
                <input @keyup="changed($event)" class="input-filter" ref="searchQueryInput" type="text" :value="''" />
            </div>
            <div class="list-container">
                <div class="options">
                    <ul>
                        <li v-for="(item, index) in filteredItem" :key="`item-${index}`" @click="selectItem(item)">{{item.name}}</li>
                        <li v-if="filteredItem.length === 0">Not found</li>
                    </ul>
                </div>
            </div>
        </div>
    </div>
</template>

<script lang="ts">
    import { defineComponent, ref } from 'vue'
    import Item from '../types/Item';

    export default defineComponent({
        name: 'DropdownFilter',
        props: {
            name: { 
                default: 'DropdownFilter', 
                type: String,
            }, 
            classes: { 
                default: '', 
                type: String,
            }, 
            label: {
                default: '', 
                type: String,
            },
            placeholder: {
                default: 'Select item',
                type: String,
            },
            withFilter: {
                default: 'false',
                type: String,
            },
            payload: {
                default: [],
                type: Array,
            },
        },

        setup(props, context) {
            const searchQueryInput = ref()
            const isVisible = ref<boolean>(false);
            const selectedItem = ref();
            const filteredItem = ref();
            let itemArray = {};

            if (props.payload.length > 0) {
                itemArray = props.payload;
                filteredItem.value = itemArray;
            }

            //  Change Event from Input
            const changed = (e: Event) => {
                const query = (e.target as HTMLInputElement).value.toLowerCase();

                // If the Search Input is empty we assign every element in the array
                if ((e.target as HTMLInputElement).value === "") {
                    filteredItem.value = itemArray;
                }

                // We use Object values to treat the itemArray object as an array
                // so we can use the filter method on it.
                filteredItem.value = Object.values(itemArray).filter((item:any) => {
                    // We look for an item that includes the query string on it
                    return (item.name).toLowerCase().includes(query);
                })

            }

            // On click event for every Item rendered.
            const selectItem = (item:Item) => {
                selectedItem.value = item;
                isVisible.value = false;
                searchQueryInput.value.value = '';

                // Once we select and item on the list
                // we use a custom event to pass the item name
                // to the parent
                context.emit('surveySubmit', item.name);
            }

            return {
                searchQueryInput,
                selectedItem,
                isVisible,
                itemArray,
                filteredItem,
                changed,
                selectItem,
            }
        }
    })
</script>

<style lang="scss">
.dropdown-filter-container {
    position: relative;

    .selected-item {
        height: 40px;
        background-color: #dfe4ec;
        padding: 5px 10px;
        display: flex;
        justify-content: space-between;
        align-items: center;
        font-size: 16px;
        font-weight: 500;

        i {
            transform: rotate(0deg);
            transition: all .2s ease;

            &.active {
               transform: rotate(180deg);
                
            }
        }
    }

    .dropdown-filter {
        background-color: #fff;
        border-bottom-right-radius: 10px;
        border-bottom-left-radius: 10px;
        position: absolute;
        top: 64px;
        left: 0;
        right: 0;
        visibility: hidden;
        transition: all 0.4s ease-in-out;
        max-height: 0;
        overflow: hidden;
        width: 100%;
        z-index: 1;

        &.active {
            max-height: 450px;
            visibility: visible;
            
        }

        .search-container {
            padding: 0 15px;
            position: relative;

            i {
                position: absolute;
                top: 18px;
                left: 22px;
            }
        }

        input.input-filter {
            width: 100%;
            border: 2px solid lightgray;
            font-size: 16px;
            margin: 10px auto;
            padding-left: 25px;

            &:focus {
                border: 3px solid #1A62FF;
            }
        }

        .options {
            width: 100%;

            ul {
                list-style: none;
                text-align: left;
                max-height: 120px;
                overflow-y: scroll;
                overflow-x: hidden;

                li {
                    border-left: 5px solid transparent;
                    cursor: pointer;
                    font-size: 16px;
                    font-weight: 500;
                    padding: 10px;
                    width: 100%;

                    &:hover {
                        background: #8CB1FF;
                        border-left: 5px solid #0043D2;
                    }
                }
            }
        }
    }
}
</style>