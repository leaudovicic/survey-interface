<template>
    <div class="field-select full" data-draggable="true">
        <v-input v-model.lazy="selectOption.value" @change="emitNewValue" class="full-width field">
            <template #prepend>
                <v-icon name="drag_indicator" class="drag-handle"></v-icon>
            </template>

            <template #append>
                <v-icon @click="deleteOption" name="delete" class="has-click" role="button"></v-icon>
            </template>
        </v-input>
    </div>
</template>

<script>
export default {
    name: 'SelectQuestionOption',
    props: {
        option: {
            type: String,
            default: null,
        },
        index: {
            type: Number,
            default: null,
        },
    },
    emits: ['changeOption', 'deleteOption'],
    data() {
        return {}
    },
    computed: {
        selectOption() {
            return {
                index: this.index,
                value: this.option,
            }
        },
    },
    methods: {
        emitNewValue() {
            this.$emit('changeOption', this.selectOption)
        },

        deleteOption() {
            this.$emit('deleteOption', this.selectOption)
        },
    },
}
</script>

<style scoped>
.field-select {
    margin-bottom: 8px;
    --input-height: 48px;
    --input-padding: 8px;
}
</style>
