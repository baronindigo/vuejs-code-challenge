<template>
    <div class="">
        <label :class="classes" :for="name">{{label}}</label>
        <div v-if="type === 'currency'" class="currency">
            <i class="lpi-dollar"></i>
            <input :name="name" v-model="input" type="text" @blur=convert class="input-currency pl-1" title="text test" />
        </div>

        <div v-if="type === 'integer'" class="integer">
            <input :name="name" v-model="input" type="text" @blur=convert class="input-integer" />
        </div>

        <div v-if="type === 'decimal'" class="decimal">
            <input :name="name" v-model="input" type="text" @blur=convert class="input-decimal"  />
        </div>

        <div v-if="isError=='true'" class="info-container text-error">
            <div class="errorMsg">Input is not valid</div>
        </div>
    </div>
</template>

<script lang="ts">
import { defineComponent, ref } from 'vue'

    export default defineComponent({
        name: 'InputNumber',
        props: {
            type: {
                default: 'decimal',
                type: String,
            }, 
            maxValue: { 
                default: '9999',
                type: String,
            },
            name: { 
                default: 'InputNumber', 
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
        },

        setup(props, context) {
            const input = ref(),
                isError = ref<string>('');

            isError.value = 'false';

            // Convert Function will set different params
            // for the javascript built-in Intl function
            // depending on the type props received.
            const convert = () => {
                // error message removed
                isError.value = 'false';

                // We strip the posibility of $ and , signs from 
                // the input value
                input.value = input.value.replace(/[$,]/g, "");

                // if the input value received is not a number
                // we display an error.
                if (isNaN(input.value)) {
                    isError.value = 'true';
                    input.value = "";

                    return;
                }

                let opt = {};

                switch(props.type) {
                    case 'decimal':
                        opt = {
                            minimumFractionDigits: 2,
                            maximumFractionDigits: 2,
                        }
                    break;

                    case 'integer':
                        opt = {
                            minimumFractionDigits: 0,
                            maximumFractionDigits: 0,
                        }
                    break;

                    case 'currency':
                        opt = {
                            style: 'currency', 
                            currency: 'USD',
                            minimumFractionDigits: 2,
                            maximumFractionDigits: 2,
                        }
                    break;
                }

                // We initialize the formatter
                const formatter = new Intl.NumberFormat('en-US', opt);

                // if input value is bigger than the max value defined
                // on the props, we set the the max value on the input
                if (parseInt(input.value) > parseInt(props.maxValue)) {
                    input.value = props.maxValue;
                }

                // Finally, we use the formatter on the value
                input.value = formatter.format(input.value);
                
                // Removing extra $ after formatting.
                // We already have the $ sign in the DOM.
                if (props.type === 'currency') {
                    input.value = input.value.replace(/[$]/g, "");
                }

                // we use a custom event to pass the item name
                // to the parent
                context.emit('surveySubmit', input.value);

                return input.value;
            }
            
            return {
                input,
                isError,
                convert,
            }
        }
    })
</script>

<style lang="scss">
    .currency {
        position: relative;

        i {
            position: absolute;
            top: 8px;
            left: 2px;
        }

        .input-currency {
            padding-left: 15px;
        }
    }
</style>