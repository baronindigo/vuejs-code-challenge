<template>
    <div class="input-word-count-container">
        <label :class="classes" :for="name">{{label}}</label>
        <input @keyup="changedText($event)" :name="name" ref="textInput" :value="''" type="text" />
        <div class="info-container">
            <div class="text-silver">{{exampleText}}</div>
            <div class="word-count font-weight-bold" :class="{'text-error': maxLengthReached }">{{currentChars}}/{{maxLength}}</div>
        </div>
    </div>
</template>

<script lang="ts">
    import { defineComponent, ref } from 'vue'

    export default defineComponent({
        
        props: {
            max: {
                default: '',
                type: String,
            }, 
            name: { 
                default: '', 
                type: String 
            },
            classes: { 
                default: '', 
                type: String 
            }, 
            label: {
                default: '', 
                type: String 
            }, 
            exampleText: {
                default: 'E.g.', 
                type: String 
            },
        },

        setup(props, context) {
            const textInput = ref();
            const currentChars = ref(0);
            const maxLength:number = parseInt(props.max);
            const maxLengthReached = ref(false)

            const changedText = (e: Event) => {
                // We use currentChars to count the
                // current Input text length
                currentChars.value = (e.target as HTMLInputElement).value.length;

                // On every change, we use a custom event to
                // pass the item name to the parent
                context.emit('surveySubmit', textInput.value.value);

                // When the characters counter hits the max defined length
                // maxLengthReached value is true and the word counter text
                // will turn red
                if (currentChars.value == maxLength) {
                    maxLengthReached.value = true;

                // if the characters counter is bigger than the
                // max length defined, it will removed the recently
                // character added
                } else if (currentChars.value >= maxLength) {
                    textInput.value.value = textInput.value.value.substring(0, maxLength);
                    currentChars.value = maxLength;
                // in any other case, the maxLength flag turns false
                // and the word count text return to black
                } else {
                    maxLengthReached.value = false;
                }
            }

            return {
                textInput,
                maxLength,
                currentChars,
                maxLengthReached,
                changedText,
            }
        }
    })
</script>

<style lang="scss"></style>